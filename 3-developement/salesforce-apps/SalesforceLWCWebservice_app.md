Creating a Salesforce Lightning Web Component (LWC) that interacts with a web service involves several steps. You will need to create the LWC itself, an Apex class to handle server-side logic, and potentially some configuration to handle authentication and setup.

Here’s a basic example to get you started. This example demonstrates how to call an external REST web service from an LWC through an Apex controller.

### 1. Create the Apex Controller

First, you need an Apex controller to handle the web service call. This controller will have a method that makes an HTTP request to the external web service.

**Apex Controller (WebServiceController.cls):**
```apex
public with sharing class WebServiceController {
    @AuraEnabled(cacheable=true)
    public static String callWebService() {
        Http http = new Http();
        HttpRequest request = new HttpRequest();
        request.setEndpoint('https://api.example.com/data');
        request.setMethod('GET');

        HttpResponse response = http.send(request);
        if (response.getStatusCode() == 200) {
            return response.getBody();
        } else {
            throw new AuraHandledException('Error calling web service: ' + response.getStatus());
        }
    }
}
```

### 2. Create the Lightning Web Component

Next, create the LWC that will call this Apex method and display the data.

**LWC JavaScript (webServiceComponent.js):**
```javascript
import { LightningElement, track, wire } from 'lwc';
import callWebService from '@salesforce/apex/WebServiceController.callWebService';

export default class WebServiceComponent extends LightningElement {
    @track data;
    @track error;

    @wire(callWebService)
    wiredWebService({ error, data }) {
        if (data) {
            this.data = data;
            this.error = undefined;
        } else if (error) {
            this.error = error;
            this.data = undefined;
        }
    }
}
```

**LWC HTML (webServiceComponent.html):**
```html
<template>
    <lightning-card title="Web Service Data" icon-name="custom:custom63">
        <template if:true={data}>
            <div class="slds-m-around_medium">
                <pre>{data}</pre>
            </div>
        </template>
        <template if:true={error}>
            <div class="slds-m-around_medium slds-text-color_error">
                <p>Error: {error.body.message}</p>
            </div>
        </template>
    </lightning-card>
</template>
```

**LWC CSS (webServiceComponent.css):**
```css
.slds-m-around_medium {
    margin: 1rem;
}
```

### 3. Deploy the LWC and Apex Controller

Ensure both the LWC and Apex controller are deployed to your Salesforce org. Use your preferred method (Salesforce CLI, Workbench, or the Developer Console) to push the components to your org.

### 4. Add the LWC to a Lightning Page

Finally, add your new LWC to a Lightning page or a record page within the Lightning App Builder:

1. Go to the Lightning App Builder in Salesforce.
2. Choose the page where you want to add your component or create a new page.
3. Drag your `webServiceComponent` onto the page.
4. Save and activate the page.

### Notes

- Ensure you have the appropriate permissions and the remote site setting configured for the external web service endpoint.
- Handle authentication for the external web service if needed (e.g., OAuth tokens).
- For production use, consider more robust error handling and potentially logging.

This example gives you a basic starting point for building an LWC that calls an external web service using an Apex controller. Adjust the code as needed for your specific use case and external service requirements.