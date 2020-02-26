# Serverless Openwhisk Intro
 
 
**Are you looking for a gitbook with updated materials? 
It is here: [ibm.biz/sv-serverless-2020](http://ibm.biz/sv-serverless-2020)!**
 
Learn about serverless through 10 minutes labs done solely in a browser, and later in the terminal. If you like it give me a star. Find the short link to this site here: [ibm.biz/sv-serverless-2019](http://ibm.biz/sv-serverless-2019)

Feel free to learn more from [the presentation attached - click here](Sadowski-Cloud-Functions-SSA-small.pdf).

You need a LITE Cloud account to do the labs - here comes the signup link to use cloud functions with the free of charge lite account (IBM wants just your email) - [ibm cloud](https://ibm.biz/BdzcZt)

You might want to take **a full 101 Serverless / Cloud Function Bootcamp** - the link is here: [github.com/IBM-Cloud/openwhisk-workshops](https://github.com/IBM-Cloud/openwhisk-workshops)

## the browser based examples - no installation!

The following links provide you with some simple examples on using Serverless from the Cloud Function starter kit. The only thing required to start it is a signup for the Cloud to leverage serverless with the lite cloud account. 

- Here comes the signup link to use cloud functions with the free of charge lite account - [ibm cloud](https://ibm.biz/BdYtWL)
- check this first - [the link to the starter kit](https://console.bluemix.net/openwhisk)

## the terminal based examples

Here comes some simple examples to run in your terminal. Please check the following pages to test the serverless:

- **Swift** - see below
- **Java** - [link to Cloud Function documentation](https://console.bluemix.net/docs/openwhisk/openwhisk_actions.html#creating-java-actions)
- Python - see below
- other languages tbc.

### Swift based terminal example
**Add ibmcloud CLI**: https://cloud.ibm.com/functions/learn/cli - check the basic steps to log in, and setup environment. When you are ready to get all the existing actions with the following command follow steps below to create an action in Swift:

```
$ ibmcloud fn list
```

**Step 1. Clone the template repository**
```
$ git clone https://github.com/ibm-functions/template-hello-world.git
```

**Step 2. check the directory and the `manifest` file**
```
$ cd template-hello-world/runtimes/swift/
```

**Step 3. change the swift code**
In the `actions/helloworld.swift` file chenge the source code to the following

```swift
func main(args: [String:Any]) -> [String:Any] {
  return [ "message" : "Hello World!" ]
}
```

**Step 4. Create the action**

```
$ PACKAGE_NAME=hello-world-serverless-swift-cli ibmcloud fn deploy -m manifest.yaml
```

**Step 5. Call the action**

```
$ ibmcloud fn action invoke --result hello-world-serverless-swift-cli/helloworld
```
You should see the following response: `{"message": "Hello World!"}`. The first call might take couple seconds to provision the container with the Swift based action. The following calls will render a very fast responses - if made under about 6 minutes from the first call - after that time the resources are going to be released by the OpenWhisk resource manager.

**Step 6. You are also able to update the action**

```
$ ibmcloud fn action update hello-world-serverless-swift-cli/helloworld actions/helloworld.swift 
```

You should receive the response: `ok: updated action hello-world-serverless-swift-cli/helloworld`
**Comment**
Also it is possible to **deploy warmed up Swift action** with a `Docker` based Swift action - more info would follow.

### JAVA based terminal example
Add ibmcloud CLI: https://cloud.ibm.com/functions/learn/cli

In this section you will create a serverless action, and invoke it in the cloud.
In order to preceed furhter you need [JVM8 - check it out here](http://openjdk.java.net/install/). To make sure just write:
```bash
$ java -version
```

and the possible output:

```bash
java version "1.8.0_181"
Java(TM) SE Runtime Environment (build 1.8.0_181-b13)
Java HotSpot(TM) 64-Bit Server VM (build 25.181-b13, mixed mode)
```

Then create a file ```Hello.java```:
```bash
$ cat > Hello.java
```

And copy - paste the following file:
```java
 import com.google.gson.JsonObject;
 public class Hello {
     public static JsonObject main(JsonObject args) {
         String name = "stranger";
         if (args.has("name"))
             name = args.getAsJsonPrimitive("name").getAsString();
         JsonObject response = new JsonObject();
         response.addProperty("greeting", "Hello " + name + "!");
         return response;
     }
 }
```

Before you can compile it, you need to add gson to the ```CLASSPATH``` from [maven repo - hit download](https://search.maven.org/artifact/com.google.code.gson/gson/2.8.5/jar)


```bash
#assuming that you downloaded gson.jar to the current directory:
$ pwd
/your-path-to-the-file/

$ export CLASSPATH=/your-path-to-the-file/gson-2.8.5.jar
```

And the final steps include compiling the file:
```bash
$ javac Hello.java
$ jar cvf hello.jar Hello.class
```

If that finished without the errors you can now create the action in the Cloud:
```bash

$ ibmcloud login 
# I think you do not need this: $ ibmcloud target --cf
$ ibmcloud fn action create helloJava hello.jar --main Hello
```

You can see the action being registered in your dashboard - [link](https://console.bluemix.net/openwhisk/actions)

Finally you can invoke the action:

```bash
$ ibmcloud fn action invoke --result helloJava --param name Marek

{
    "greeting": "Hello Marek!"
}

```

More information on this example you will find here: https://console.bluemix.net/docs/openwhisk/openwhisk_actions.html#creating-java-actions

## adding REST API backend feature with API Gateway for OpenWhisk
check this link on adding ```--web true``` option: https://cloud.ibm.com/docs/openwhisk?topic=cloud-functions-openwhisk_apigateway#openwhisk_apigateway

check this commands:
```
$ ibmcloud fn action create helloJavaWeb hello.jar --main Hello --web true
$ ibmcloud fn api create /hello /world get helloJavaWeb --response-type json

ok: created API /hello/world GET for action /_/hello
https://service.us.apiconnect.ibmcloud.com/gws/apigateway/api/<GENERATED_API_ID>/hello/world

$ curl https://service.us.apiconnect.ibmcloud.com/gws/apigateway/api/<GENERATED_API_ID>/hello/world?name=OpenWhisk

{
"payload": "Hello world OpenWhisk"
}

```

## Python example with Watson VR from UI
The Python runtime includes the Watson Developer Cloud SDKs (Software Development Kits) including the visual recognition SDK you can use. Please import this visual recognition SDK to make calls to the service in a python-native way.

1. Replace the hello world Python code with the following code, found on the next page. You can copy paste this code:
```python
from watson_developer_cloud import VisualRecognitionV3

def main(params):
    # init visual recognition library
    apiKey = params['apiKey']
    version = "2018-03-19"
    visual_recognition = VisualRecognitionV3(version=version, iam_apikey=apiKey)

    # get image url from params
    image_url = params['imageUrl']

    # parse visual recognition return data for our tags
    tags = ""
    classifiedImages = visual_recognition.classify(url=image_url).get_result()
    image = classifiedImages['images'][0]
    classes = image['classifiers'][0]['classes']
    for theClass in classes:
        currentTag = theClass['class']
        print(currentTag)
        tags = tags + currentTag + ", "
    result = {'classes': tags}
    return result
```

2. This action expects the apiKey to be passed in as a parameter.
```
 init visual recognition library
apiKey = params['apiKey']
version = "2018-03-19"
visual_recognition = VisualRecognitionV3(version=version, iam_apikey=apiKey)
```

3. Default parameters can be set for an action, rather than passing the parameters into the action every time. This is a useful option for data that stays the same on every invocation. Let’s set the apiKey as one of our default parameters. Click Parameters in the left side menu, and then click ```Add Parameter +```.
4. For parameter name, ```apiKey```, with a capital ```K```. For parameter value, insert your apiKey value enclosed in quotation marks.

5. Click Save.

6. add another parameter: 
- `"imageUrl"` and the value: `"https://raw.githubusercontent.com/beemarie/ow-vr/master/images/puppy.jpg"`

I will add description on how to add the JSON as an input shortly:
```JSON
{
  "imageUrl":"https://raw.githubusercontent.com/beemarie/ow-vr/master/images/puppy.jpg"
}
```
7. Test it! Did it work? If yes, try other URLs with awesome pictures. **How many calls you can make for free?**

## mobile serverless backend as a service

Find the following guide for building the mobile application with the serverless backend for mobile applications with Watson and Mobile Notifications:
- [https://console.bluemix.net/docs/tutorials/serverless-mobile-backend.html?programming_language=swift#mobile-application-with-a-serverless-backend](https://console.bluemix.net/docs/tutorials/serverless-mobile-backend.html?programming_language=swift#mobile-application-with-a-serverless-backend)

## the full examples for further hacking:

Check out this examples to get into the advanced subjects with 
- video analytics with AI in serverless batch jobs - [check Dark Vision app](https://github.com/IBM-Cloud/openwhisk-darkvisionapp)
- https://github.com/csantanapr/ato-serverless-swift
- serverless for mobile backend - https://www.ibm.com/blogs/bluemix/2018/01/performant-swift-serverless-actions/
- [an example for Android](https://github.com/IBM-Cloud/serverless-followupapp-android)

Please follow me on Twitter @blumareks

