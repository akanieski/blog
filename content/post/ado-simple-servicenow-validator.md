+++
author = "Andrew Kanieski"
title = "Simple Azure Pipelines Service Now Change Control Validation"
date = "2023-10-24"
description = "A simple, light-weight integration with Service Now for validating Change Control Numbers in Service Now"
tags = [
    "azure devops",
    "devops",
    "build",
    "service now",
    "smc",
    "servicenow"
]
+++

Many enterprise customers use Service Now for Change Control management. But some customers are not looking 
for all the robust features Service Now offers in their DevOps integration module. If you're looking for simple
way of validating a change control number during a pipeline's execution, please find below a simple `Task` 
template that can be used to validte change controls in service now.

**Note: We could improve the below by switching from Basic auth to OAuth. Perhaps in a future post I can provide an example**

```
parameters:
- name: changeControlNumber
  type: string
  displayName: Change Control Number
  default: 'CHG000000'
- name: serviceNowUri
  type: string
  displayName: Service Now Base Uri
  default: https://service-now-tenant.service-now.com
- name: serviceNowUser
  type: string
- name: serviceNowPassword
  type: string
- name: requiresAllActive
  type: boolean
  default: true

steps:
- powershell: |
    Get-ChildItem env:
    $url = "$($env:SYSTEM_TEAMFOUNDATIONSERVERURI)$env:SYSTEM_TEAMPROJECTID/_apis/git/repositories/$($env:BUILD_REPOSITORY_NAME)/pullRequests/$($env:SYSTEM_PULLREQUEST_PULLREQUESTID)/labels?api-version=5.1-preview.1"
    $response = Invoke-RestMethod -Uri $url -Method Get -Headers @{
        Authorization = "Bearer $env:SYSTEM_ACCESSTOKEN"
    }
    $val = $response.value.name -split ",\s*" | Where-Object { $_.StartsWith("CHG") } | ConvertTo-Json -Compress
    Write-Host "##vso[task.setvariable variable=PullRequestTags;isOutput=true]$val"
  displayName: Scan for PR Tags
  name: PR

- powershell: |
    try {
      $url = '${{ parameters.serviceNowUri }}'
      $combined = '${{ parameters.serviceNowUser }}' + ':' + '${{ parameters.serviceNowPassword }}'
      $bytes = [System.Text.Encoding]::UTF8.GetBytes($combined)
      $b64creds = [System.Convert]::ToBase64String($bytes)

      $headers = @{
          "Authorization" = "Basic $b64creds"
          "Accept" = "application/json"
      }

      $changeControlNumbers = '$(PR.PullRequestTags)' | ConvertFrom-Json
      if ($changeControlNumbers.length -eq 0) {
          $changeControlNumbers = '["${{ parameters.changeControlNumber }}"]' | ConvertFrom-Json
      }

      $requiresAllActive = '${{ parameters.requiresAllActive }}'
      $notActive = New-Object System.Collections.ArrayList($null)
      $areActive = New-Object System.Collections.ArrayList($null)

      foreach ($changeNum in $changeControlNumbers) {
          $response = Invoke-RestMethod "$url/api/sn_chg_rest/change?sysparm_query=number=$changeNum" -Headers $headers
          $active_cc = @($response.result | Where-Object { $_.active.value -eq $true })
          if ($active_cc.length -gt 0) 
          {
            $areActive.Add($changeNum)
          } else {
            $notActive.Add($changeNum)
          }
      }

      if ($requiresAllActive -eq $true) {
          if ($areActive.length -ne $changeControlNumbers.length) {
              Write-Host "##vso[task.logissue type=error;] The following change controls are not active: $notActive"
              exit 1
          }
          else {
              Write-Host "All change controls provided are active ($areActive)."
              exit 0
          }
      }
      else {
          if ($areActive.length -gt 0) {
              Write-Host "At least one Change Control is active ($areActive)"
              exit 0
          }
          else {
            Write-Host "##vso[task.logissue type=error;] The following change controls are not active: $notActive"
            exit 1
          }
      }
    } catch {
      Write-Host "##vso[task.logissue type=error;] $_"
      exit 1
    }
  displayName: Validate Change Control
```
