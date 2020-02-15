+++
author = "Andrew Kanieski"
title = "Computer Vision Made Easy with Azure Cognitive Services"
date = "2019-02-26"
description = "Computer Vision Made Easy with Azure Cognitive Services"
tags = [
    "cv",
    "cognitive service",
	"computer vision"
]
+++

Computer vision is truly amazing technology! It can be used to distill rich information from images that can help you breathe life into your applications. Microsoft's Cognitive Services - Computer Vision API gives developers the power to develop their software with the ability to truly see and comprehend the world around it.



When I come upon a new technology I try to make application in my life. I ask myself how could I see myself using this? There are many ways in which we can use Computer Vision to make our day to day lives easier!



**Context: Enter the family dog**



One problem in my home we always struggle with is keeping our family dog out of our bedrooms. As much as we train him, he always sneaks in to nap on our beds. How can we write software to help us train our family friend?



**Problem:** I need to be alerted when our dog enters a room This is where I applied my new found knowledge of the Computer Vision API. What if every time my dog entered our bedroom I was alerted via SMS message?



### Prerequisites

IP Camera (or other means of collecting images) - I am using an Amcrest IP2M-841B
.NET Core Installed - Install Instructions Here
VSCode (or other editor) - Install Instructions Here
Twilio Account (Trial or Subscriber) - More Info on Twilio + Azure Here
Assuming you have .NET Core installed, let's start by creating a new .NET Core Console Application:

From console:
``` sh
dotnet new console
dotnet restore
```

This will create our initial application with a Program.cs and a dog-watcher.csproj

In Program.cs I added the needed variables that will be passed into our application via Environment. Other means of storing and working with configuration are also fine. I just happen to prefer environment variables.

``` cs

static string IP_CAMERA_SNAPSHOT_URL = System.Environment.GetEnvironmentVariable("IP_CAMERA_SNAPSHOT_URL");

static string IP_CAMERA_USER = System.Environment.GetEnvironmentVariable("IP_CAMERA_USER");

static string IP_CAMERA_PASSWORD = System.Environment.GetEnvironmentVariable("IP_CAMERA_PASSWORD");
```

In this case I am using an IP based camera that provides you with a REST API for getting a snapshot from the camera via HTTP.



Now that we have our needed configuration collected, let's begin by creating logic to fetch our snapshots. Our IP camera uses HTTP GET with basic auth.



First, lets add System.Net.Http;


``` sh
dotnet add package System.Net.Http
```

Now you can go ahead and use the System.Net.Http namespace.



``` Program.cs

static byte[] GetSnapshotFromIPCamera(string url) {
        Client.DefaultRequestHeaders.Authorization = new System.Net.Http.Headers.AuthenticationHeaderValue("Basic", System.Convert.ToBase64String(System.Text.ASCIIEncoding.ASCII.GetBytes(IP_CAMERA_USER + ":" + IP_CAMERA_PASSWORD)));
        return Client.GetByteArrayAsync(url).Result;
    }
```

Now that we have logic to collect images, let's create some classes that match with the response from the Computer Vision API. This is only mapping Tags since that is the data element we are working with. In this case tags will provide us with a list of descriptors that computer vision has identified and with what level of confidence it feels those descriptors are identified.


``` cs
using Newtonsoft.Json;

namespace Vision {
    public class Response {
        [JsonProperty("tags")]
        public Tag[] Tags {get;set;}
    }
    public class Tag {
        [JsonProperty("name")]
        public string Name {get;set;}
        [JsonProperty("confidence")]
        public float Confidence {get;set;}
    }
}
```

Now let's post those images to Cognitive Services for analysis. Notice the use of the variable AZURE_VISION_URL and AZURE_VISION_KEY these are coming directly from the Azure Portal after we've gone and added our Coginitive Services subscription.


``` cs
static string AZURE_VISION_URL = System.Environment.GetEnvironmentVariable("AZURE_VISION_URL");

static string AZURE_VISION_KEY = System.Environment.GetEnvironmentVariable("AZURE_VISION_KEY");
static Vision.Response GetVisionAnalysisResponse(byte[] image) {
    Client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", AZURE_VISION_KEY);
    using (var content = new System.Net.Http.ByteArrayContent(image))
    {
        content.Headers.ContentType = new System.Net.Http.Headers.MediaTypeHeaderValue("application/octet-stream");
        string res = Client.PostAsync(AZURE_VISION_URL + @"/analyze?visualFeatures=Tags, Description", content).Result.Content.ReadAsStringAsync().Result;
        return JsonConvert.DeserializeObject(res);
    }
}
```

Now let's wire up the loop that watches our room.


``` cs
static int INTERVAL = String.IsNullOrEmpty(System.Environment.GetEnvironmentVariable("INTERVAL")) ? 3000 : Convert.ToInt32(System.Environment.GetEnvironmentVariable("INTERVAL"));

static void Main(string[] args)
{
    while(true) {
        try {
            var res = GetVisionAnalysisResponse(GetSnapshotFromIPCamera(IP_CAMERA_SNAPSHOT_URL));

            // Only trigger alert logic when CV is 90% sure a dog has been spotted!
            if (res.Tags
                    .Where(t => t.Name.ToUpper().Equals("DOG") && t.Confidence > 0.90)
                    .Count() > 0) {
                // TODO: ADD LOGIC FOR WHEN DOG IS FOUND IN THE ROOM
            }

        } catch (Exception ex) {
            Console.WriteLine(ex);
        }
        Console.WriteLine("Waiting for next interval");
        System.Threading.Thread.Sleep(INTERVAL);
    }
}
```

With Twilio, sending SMS messages from C# is very easy. Simply setup a trial account and install the needed nuget packages.



Note: that trial Twilio accounts can only send SMS messages to verified devices.


``` sh
dotnet add package Twilio
```

``` Program.cs

static string TWILIO_API_KEY = System.Environment.GetEnvironmentVariable("TWILIO_API_KEY");

static string TWILIO_API_TOKEN = System.Environment.GetEnvironmentVariable("TWILIO_API_TOKEN");

static string TWILIO_API_PHONE = System.Environment.GetEnvironmentVariable("TWILIO_API_PHONE");

static void Main(string[] args)
{
    while(true) {
        try {
            var res = GetVisionAnalysisResponse(GetSnapshotFromIPCamera(IP_CAMERA_SNAPSHOT_URL));

            // Only trigger alert logic when CV is 90% sure a dog has been spotted!
            if (res.Tags
                    .Where(t => t.Name.ToUpper().Equals("DOG") && t.Confidence > 0.90)
                    .Count() > 0) {
                Twilio.TwilioClient.Init(TWILIO_API_KEY, TWILIO_API_TOKEN);
                Twilio.Rest.Api.V2010.Account.MessageResource.Create(
                    new Twilio.Types.PhoneNumber(TARGET_PHONE), 
                    from: new Twilio.Types.PhoneNumber(TWILIO_API_PHONE), 
                    body: "It looks like Jack has made his way into your room!"
                );
            }

        } catch (Exception ex) {
            Console.WriteLine(ex);
        }
        Console.WriteLine("Waiting for next interval");
        System.Threading.Thread.Sleep(INTERVAL);
    }
}
```

Now we can simply adjust our camera for ideal viewing angle and run our application.

### Conclusion
Working with Microsoft's Cognitive Services is an easy way to build intelligent applications. Additionally, Azure provides a seemless hosting environment for applications that interact with Cognitive Services.