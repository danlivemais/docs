## Handling Custom Responses

The Cloud Scripting engine provides functionality to handle custom responses. The responses handling is related to the action result codes that can be located within the <a href="/troubleshooting/" target="_blank">Jelastic Console Log Panel</a> upon a corresponding action execution. Therefore, you can predefine a response text that is displayed in case of an error occurrence.           

There are the following types of pop-up windows that emerge while custom responses are being handled:                    

- `info` - *information* pop-up window                

<center>![SuccessText](/img/SuccessText.jpg)</center>          

- `warning` - *warning* pop-up window with a custom message                
 
<center>![new-warning](/img/new-warning.png)</center>        

- `error` - *error* pop-up window          

<center>![new-error](/img/new-error.png)</center>          

When the action is executed with the expected result code, the *success* window is displayed.                       

<center>![success](/img/successResponse.jpg)</center>                               

The *info*, *error* and *warning* pop-up windows emerge as a result of failed installation. The manifest installation is finished immediately when any action returns the negative result code or code predefined in the *responses* block. Therefore, the installation process is marked by the red cross like in the picture below.                        

<center>![success](/img/redCross.jpg)</center>

The basic custom response message can be returned in one string via the *return* or *script* action as follows.                     
 
```json
{
  "type": "update",
  "name": "response handlers",
  "onInstall": {
    "return": "Warning!"
  }
}
```

```json
{
  "type": "update",
  "name": "response handlers",
  "onInstall": {
    "script": "return 'Warning!'"
  }
}
```

In this case, the default response type is *error* and the response *warning* message is returned in a string.

It is possible to return a response with a predefined result type and with a custom message text via the *return* or *script* action.                  

The *return* action.                  

```json
{
    "type": "update",
    "name": "response handlers",
    "onInstall": {
        "return": {
          "result": "warning",
          "message": "Warning!",
          "email": "string"
        }
    }
}
```
!!! note
    The *email* parameter is available only for *success* response type. The email is delivered when an action is executed with the *success* response code.                         

The *script* action. 

```json
{
    "type": "update",
    "name": "response handlers",
    "onInstall": {
        "script": "return {result: 'warning', message: 'Warning!','email': 'string'}"
    }
}
```

The *message* and *email* parameters support all the available<a href="/reference/placeholders/" target="_blank">placeholders</a>. Thus, placeholders can be uploaded from any external source via the direct link or via the <a href="/creating-manifest/basic-configs/#relative-links" target="_blank">baseUrl</a>.                          

When a response code with the *success* installation type is returned, two response objects impose one another. And the *success* text from the *response* object has a higher priority than a <a href="/creating-manifest/user-input-parameters/#success-text-customization" target="_blank">*success*</a> text from the main manifest block.            

**Examples**                           

Here, the result code is with the *success* installation type, the message is '*Hello!!*', and the email message is '*success!!*'.                                        
```json
{
    "type": "update",
    "name": "response handlers",
    "onInstall": {
        "script": "return {'result': 'success','message': 'Hello!!'}"
    },
    "success": "success!!"
}
```
Here, the result code is with the *success* installation type, the message is '*Hello!!*', and the email message is '*Hello!!*'.                                             
```json
{
    "type": "update",
    "name": "response handlers",
    "onInstall": {
        "script": "return {'result': 'success','message': 'Hello!!', 'email': 'Hello!!'}"
    },
    "success": "success!!"
}
```

The result message text can be localized according to the languages, available within the Jelastic Platform.                   

``` json
{
  "type": "warning",
  "message": {
    "en": "Localized text",
    "es": "Texto localizado"
  }
}
```

If it is necessary to display the same response codes, you can add a *response* object where you define custom responses.                           

**Examples**

**File creation error**

The example below describes a creation of the same file twice and handling a response that is received as a result of such action execution. Consequently, the result code of this response will be defined as *4036*. Thus, all the actions with *4036* result are displayed via *error* pop-up window with a custom response text.                      

``` json
{
  "type": "update",
  "name": "Handling File Creation",
  "onInstall": [
    {
      "createFile [cp]": "/tmp/customDirectory"
    },
    {
      "createFile [cp]": "/tmp/customDirectory"
    }
  ],
  "responses": {
    "4036": {
      "type": "error",
      "message": "file path already exists"
    }
  }
}
```

where: 

- `createFile` - predefined within the Cloud Scripting <a href="/reference/actions/#createfile" target="_blank">action</a>              
- `responses` - object (array) to describe custom responses     
- `type` - type of a pop-up window, emerging upon the response occurrence. The available values are: *error*, *warning*, *info*, *success*.       

The additional functionality is provided to display action responses using <a href="/reference/actions" target="_blank">*return*</a> action.                         

``` json
{
  "type": "update",
  "name": "Custom Response Handlers",
  "onInstall": {
    "script": "return {result : 1000};"
  },
  "responses": {
    "1000": {
      "type": "warning",
      "message": "Custom Warning message!"
    }
  }
}
```

where:

- `script` - Cloud Scripting <a href= "/reference/actions/#script" target="__blank">action</a> for executing *Javascript* or *Java* code (*Javascript* is set by default)                     
- `1000` - custom predefined result code for responses handling. It is returned from the *script* action in the *onInstall* block.        

If the result code is delivered via string, then the default result code is *11039*. Therefore, responses can be handled by the following outcoming string text.                                             

``` json
{
	"type": "update",
	"name": "Custom Response Handlers",
	"onInstall": {
		"script": "return 'error'"
	},
	"responses": {
		"error": {
			"type": "info",
			"message": "Custom Warning message!"
		}
	}
}
```

In all the other cases, when a custom response is not predefined within the *responses* block, the default pop-up window type is *error* with an output message.          

The response objects that are returned from custom scripts and predefined in the *response* block are imposed one to another. Thus, the response object from custom scripts has a higher priority than responses in the *response* object.                    

**Example**

```json
{
  "type": "update",
  "name": "Custom Response Handlers",
  "onInstall": {
    "script": "return {'result': '2308', 'message': 'Success from script with result 2308'}"
  },
  "responses": {
    "2308": {
      "type": "success",
      "message": "Custom Success message!"
    }
  }
}
```

The final *success* form is similar to the following one. 

<center>![redefinedSuccessResponseHandler](/img/redefinedSuccessResponseHandler.jpg)</center>                         
## What's next?

- Explore how to customize <a href="/creating-manifest/visual-settings/" target="_blank">Visual Settings</a>                                       
- Examine a bunch of <a href="/samples/" target="_blank">Samples</a> with operation and package examples        
- See <a href="/troubleshooting/" target="_blank">Troubleshooting</a> for helpful tips and specific suggestions                 
- Read <a href="/releasenotes/" target="_blank">Realese Notes</a> to find out about the recent CS improvements                                  
- Find out the correspondence between <a href="/jelastic-cs-correspondence/" target="_blank">CS & Jelastic Versions</a>      