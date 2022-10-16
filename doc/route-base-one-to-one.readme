## Use Case 02: One To One Based On Message Routing

In the [Use Case 01: One To One](one-to-one.readme) tutorial, we routed a
simple message to a single endpoint in the back-end service. In this tutorial, we are building on the same sequence by creating the mediation artifacts that can route a message to the relevant endpoint depending on the content of the message payload.

![](../resouces/onetoone-routing-based.png)

When the client sends the appointment reservation request to the Micro Integrator, the message payload of the request contains the name of the hospital where the appointment needs to be confirmed. The HTTP request method that is used for this is POST. Based on the hospital name sent in the request message, the Micro Integrator should route the appointment reservation to the relevant hospital's back-end service.

To implement this use case, you will add a new REST resource to the existing REST API and define the new content-based mediation logic.

## Let's get started!
We setup our backend service and undarstand the basic workflow. The we implement the workflow with the the Micro Integrator.

1. The [backend service](../apis/Hospital-Service-2.0.0-EI7.jar). To run the backend service, download and run by the command 
    ```bash
    java -jar Hospital-Service-2.0.0-EI7.jar
    curl -v http://localhost:9090/healthcare/surgery
    ```
    Response: Hospital and Doctor information.
    ```json
    //output
    [
    {
        "name":"thomas collins",
        "hospital":"grand oak community hospital",
        "category":"surgery",
        "availability":"9.00 a.m - 11.00 a.m",
        "fee":7000.0
    },
    {
        "name":"anne clement",
        "hospital":"clemency medical center",
        "category":"surgery",
        "availability":"8.00 a.m - 10.00 a.m",
        "fee":12000.0
    },
    {
        "name":"seth mears",
        "hospital":"pine valley community hospital",
        "category":"surgery",
        "availability":"3.00 p.m - 5.00 p.m",
        "fee":8000.0
    }
    ]
    ...........
    ```
2. To make appoinment, send just send a request with this [make-an-appointment-payload.json](../resouces/make-an-appointment-payload.json)
    ```json
    {
        "patient": {
        "name": "John Doe",
        "dob": "1940-03-19",
        "ssn": "234-23-525",
        "address": "California",
        "phone": "8770586755",
        "email": "johndoe@gmail.com"
        },
        "doctor": "thomas collins",
        "hospital": "grand oak community hospital",
        "appointment_date": "2025-04-02"
    }
    ```
    Execute the command in your terminal
    ```bash
    curl -v -X POST --data @make-an-appointment-payload.json http://localhost:9090/{hospitalName}/categories/{category}/reserve --header "Content-Type:application/json"
    ```
    Here,The hospital name will,
    - `grand oak community hospital`
    - `clemency medical center`
    - `pine valley community hospital`

    Category: It's the Specialty of doctor like `surgery`

    Response: if the appointment is success,then you can see the output:
    ```json
    {
   "patient":{
      "name":"John Doe",
      "dob":"1940-03-19",
      "ssn":"234-23-525",
      "address":"California",
      "phone":"8770586755",
      "email":"johndoe@gmail.com"
   },
   "doctor":"thomas collins",
   "hospital":"grand oak community hospital",
   "appointment_date":"2025-04-02"
}

### Step 2: Develop the integration artifacts based on the workflow

Follow the instructions given in this section to create and configure the required artifacts.

#### Create Endpoints

In this tutorial, we have three hospital services hosted as the backend:

-   Grand Oak Community Hospital: `http://localhost:9090/grandoaks/`
-   Clemency Medical Center: `http://localhost:9090/clemency/`
-   Pine Valley Community Hospital: `http://localhost:9090/pinevalley/`

The request method is POST and the format of the request URL expected by the back-end services is
`http://localhost:9090/grandoaks/categories/{category}/reserve`.

Let's create three different HTTP endpoints for the above services.

1.  Right-click **SampleServices** in the Project Explorer and navigate to **New -> Endpoint**. 
2.  Ensure **Create a New Endpoint** is selected and click **Next**.
3.  Enter the information given below to create the new endpoint.

    <table>
        <tr>
            <th>Property</th>
            <th>Value</th>
            <th>Description</th>
        </tr>
        <tr>
            <td>Endpoint Name </td>
            <td>
                <code>GrandOakEP</code>
            </td>
            <td>
                The name of the endpoint representing the Grand Oaks Hospital service.
            </td>
        </tr>
        <tr>
            <td>Endpoint Type </td>
            <td>
                <code>HTTP Endpoint</code>
            </td>
            <td>
                Indicates that the back-end service is HTTP.
            </td>
        </tr>
        <tr>
            <td>URI Template</td>
            <td>
                <code>http://localhost:9090/grandoaks/categories/{uri.var.category}/reserve</code>
            </td>
            <td>
                The template for the request URL expected by the back-end service.
            </td>
        </tr>
        <tr>
            <td>Method</td>
            <td>
                <code>POST</code>
            </td>
            <td>
                Endpoint HTTP REST Method.
            </td>
        </tr>
        <tr>
         <td>Static Endpoint</td>
         <td><br/>
         </td>
         <td>Select this option because we are going to use this endpoint only in this Config project and will not reuse it in other projects.</br/></br/> <b>Note</b>: If you need to create a reusable endpoint, save it as a Dynamic Endpoint in either the Configuration or Governance Registry.</td>
      </tr>
      <tr>
         <td>Save Endpoint in</td>
         <td><code>               SampleServices              </code></td>
         <td>This is the Config project we created in the last section</td>
      </tr>
    </table>

    <img src="https://ei.docs.wso2.com/en/7.0.0/micro-integrator/assets/img/tutorials/119132155/119132166.png" width="500">

4.  Click **Finish**.
5.  Similarly, create the HTTP endpoints for the other two hospital services using the URI Templates given below:
    -   ClemencyEP: `http://localhost:9090/clemency/categories/{uri.var.category}/reserve`
    -   PineValleyEP: `http://localhost:9090/pinevalley/categories/{uri.var.category}/reserve`

You have now created the three endpoints for the hospital back-end services that will be used to make appointment reservations.

!!! Tip
    You can also create a single endpoint where the differentiation of the hospital name can be handled using a variable in the URI template. See the tutorial on [Exposing Several Services as a Single Service](exposing-several-services-as-a-single-service.md).

    Using three different endpoints is advantageous when the back-end services are very different from one another and/or when there is a requirement to configure error handling differently for each of them.

#### Add a REST resource

To implement the routing scenario, let's add a new API resource to the REST API we created in the [previous tutorial](sending-a-simple-message-to-a-service.md).

1.  Select **API Resource** in the API palette of the REST API and drag it to the canvas just below the previous API resource that was created.  

    ![](https://ei.docs.wso2.com/en/7.0.0/micro-integrator/assets/img/tutorials/119132155/119132165.png)

2.  Click the new API Resource to access the **Properties** tab and enter the following details:
    <table>
    <tr>
        <th>Property</th>
        <th>Description</th>
    </tr>
    <tr>
        <td>Url Style</td>
        <td>
            Click in the <b>Value</b> field, click the down arrow, and select <b>URI_TEMPLATE</b> from the list.
        </td>
    </tr>
    <tr>
        <td>URI-Template</td>
        <td>
            Enter <code>/categories/{category}/reserve</code>.
        </td>
    </tr>
    <tr>
        <td>Methods</td>
        <td>
            From the list of methods, select <b>POST</b>.
        </td>
    </tr>
    </table>

    <img src="https://ei.docs.wso2.com/en/7.0.0/micro-integrator/assets/img/tutorials/119132155/119132164.png" width="800">

#### Define the mediation flow 

You can now start configuring the API resource.

1.  Drag a **Property** mediator from the **Mediators** palette to the In Sequence of the API resource and name it **Get Hospital**. This is used to extract the hospital name that is sent in the request payload. 
2.  With the **Property** mediator selected, access the **Properties** tab and give the following details:
    <table>
        <tr>
            <th>Property</th>
            <th>Description</th>
        </tr>
      <tr class="odd">
         <td>Property Name</td>
         <td>Enter <code>New Property...</code>.</td>
      </tr>
      <tr class="even">
         <td>New Property Name</td>
         <td>Enter <code>Hospital</code>.</td>
      </tr>
      <tr class="odd">
         <td>Property Action</td>
         <td>Enter <code>set</code>.</td>
      </tr>
      <tr class="even">
         <td>Property Scope</td>
         <td>Enter <code>default</code>.</td>
      </tr>
      <tr class="odd">
         <td>Value Type</td>
         <td>Enter <code>EXPRESSION</code>.</td>
      </tr>
      <tr class="even">
         <td>Value Expression</td>
         <td>
            <div class="content-wrapper">
              <p>Follow the steps given below to specify the expression:</p>
            <ol>
                <li>Click the text box of the <strong>Value Expression</strong> field. This opens the <b>Expression Selector</b> dialog box.</li>
               <li>Select <strong>Expression</strong> from the list.
                </li>
               <li>Enter <code>json-eval($.hospital)</code> to overwrite the default expression.</li>
               <li>Click <strong>OK.</strong> <strong><br />
                  </strong>
               </li>
            </ol>
               <b>Note</b>:
               This is the JSONPath expression that will extract the hospital from the request payload.
            </div>
         </td>
      </tr>
    </table>

3.  Add a **Switch** mediator from the **Mediator** palette just after the Property Mediator.
4.  Right-click the Switch mediator you just added and select **Add/Remove Case** to add the number of cases you want to specify.  
    
    <img src="https://ei.docs.wso2.com/en/7.0.0/micro-integrator/assets/img/tutorials/119132155/119132163.png" width="500">

    We have three different hospital endpoints, which corresponds to three switch cases. Enter 3 for **Number of branches** and click **OK**.  

    ![](https://ei.docs.wso2.com/en/7.0.0/micro-integrator/assets/img/tutorials/119132155/switch-cases-dialog.png)

5.  With the Switch mediator selected, go to the **Properties** tab and give the following details:
    <table>
        <tr>
            <th>Property</th>
            <th>Description</th>
        </tr>
   <tr class="odd">
      <td><strong>Source XPath</strong></td>
      <td>
         <div class="content-wrapper">
            <p>The <strong>Source XPath</strong> field is where we specify the XPath expression, which obtains the value of Hospital that we stored in the Property mediator.</p>
            <p>Follow the steps given below to specify the expression:</p>
            <ol>
                <li>Click the text box of the <strong>Source XPath</strong> property. This opens the <b>Expression Selector</b> dialog box.</li>
               <li>Select <strong>Expression</strong> from the list.
                </li>
               <li>Enter <code>                  get-property('Hospital')                 </code> to overwrite the default expression.</li>
               <li>Click <strong>OK.</strong> <strong><br />
                  </strong>
               </li>
            </ol>
         </div>
      </td>
   </tr>
   <tr class="even">
      <td><strong>Case Branches</strong></td>
      <td>
         <div class="content-wrapper">
            <p>Follow the steps given below to add the case branches:</p>
            <ol>
                <li>Double click each <b>case regex</b> (corresponding to each branch) that is listed. This will open the <b>SwitchCaseBranchOutputConnector</b> dialog box.</li>
               <li>
                  Change the RegEx values for the switch cases as follows:
                  <ul>
                     <li>Case 1: grand oak community hospital</li>
                     <li>Case 2:  clemency medical center</li>
                     <li>Case 3:  pine valley community hospital</li>
                  </ul>
               </li>
            </ol>
         </div>
      </td>
   </tr>
    </table>

6.  Let's add a **Log** mediator to print a message indicating to which hospital the request message is being routed. Drag a Log mediator to the first Case box of the Switch mediator and name it **Grand Oak Log**.  
7.  With the Log mediator selected, access the **Properties** tab and give the following details:
    <table>
    <tr>
        <th>Property</th>
        <th>Value</th>
        <th>Description</th>
    </tr>
    <tr>
        <td>Log Category</td>
        <td>
           <code>INFO</code> 
        </td>
        <td>
            Indicates that the log contains an informational message.
        </td>
    </tr>
    <tr>
        <td>Log Level</td>
        <td>
            <code>CUSTOM</code>
        </td>
        <td>
            Only specified properties will be logged by this mediator.
        </td>
    </tr>
    <tr>
        <td>Log Separator</td>
        <td>(blank)</td>
        <td>
           Since there is only one property that is being logged, we do not require a separator, so this field can be left blank. 
        </td>
    </tr>
    <tr>
        <td>Properties</td>
        <td colspan="2">
            Follow the steps given below to extract the stock symbol from the request and print a welcome message in the log:
            <ol>
                <li>
                    Click the <b>plus</b> icon (<img src="../../../assets/img/tutorials/common/plus-icon.png" width="30">)
    to start defining a property. This opens the <b>LogProperty</b> dialog box.
                </li>
                <li>
                    Add the following values in the <b>LogProperty</b> dialog box:
                    <ul>
                        <li>
                            <b>Name</b> : `message`
                        </li>
                        <li> 
                            <b>Type</b> : `EXPRESSION`. (We select `EXPRESSION`
        because the required properties for the log message must be
        extracted from the request, which we can do using an XPath
        expression.)    </li>
                        <li>
                            <b>Property Expression</b> : Click the text box to open the <b>Expression Selector</b> dialog box and enter `fn:concat('Routing to ', get-property('Hospital'))` as the value.
                        </li>
                    </ul>
                <b>Note</b>: This XPath expression value gets the value stored in the Property mediator and concatenates the two strings to display the log message: `Routing to <hospital name>`.
                </li>
                <li>
                    Click <b>OK</b>.
                </li>
            </ol>
        </td>
    </tr>
    </table>

8.  Add a **Send** mediator adjoining the Log mediator and add the **GrandOakEP endpoint** from **Defined Endpoints** palette to the empty box adjoining the Send mediator.  

    ![](https://ei.docs.wso2.com/en/7.0.0/micro-integrator/assets/img/tutorials/119132155/119132159.png)

9.  Add **Log mediators** in the other two **Case boxes** in the Switch mediator and then enter the same properties. Make sure to name the two Log mediators as follows:

    -   `Clemency Log`
    -   `Pine Valley Log`

10. Add **Send** mediators adjoining these log mediators and add the **ClemencyEP** and **PineValleyEP** endpoints respectively from the **Defined Endpoints** palette.

    !!! Info
        You have now configured the Switch mediator to log the `Routing to <Hospital Name>` message when a request is sent to this API resource. The request message will then be routed to the relevant hospital backend service based on the hospital that is sent in the request payload.

11. Add a **Log mediator** to the **Default** case (the bottom box) of the Switch mediator and configure it the same way as the previous Log mediators.

    !!! Note
        Make sure to name this **Fault Log** and change the **Property Expression** value to:`fn:concat('Invalid hospital - ', get-property('Hospital'))`

    The default case of the Switch mediator handles the invalid hospital requests that are sent to the request payload. This logs the message (`Invalid hospital - <Hospital Name>`) for requests that have the invalid hospital name.

12. Drag a **Respond mediator** next to the Log mediator you just added. This ensures that there is no further processing of the current message and returns the request message back to the client.  

13. Drag a **Send** mediator to the **Out sequence** of the API resource to send the response back to the client.

The In Sequence of the API resource configuration should now look like this:  

![](https://ei.docs.wso2.com/en/7.0.0/micro-integrator/assets/img/tutorials/119132155/119132158.png?effects=drop-shadow)

You have successfully created all the artifacts that are required for routing messages to a back-end service depending on the content in the request payload. 

### Step 3: Package the artifacts

Package the artifacts in your composite application project (SampleServicesCompositeApplication project) to be able to deploy the artifacts in the server.

1.  Open the `          pom.xml         ` file in the composite application project POM editor.
2.  Ensure that the following artifacts are selected in the POM file.

    -   `HealthcareAPI`
    -   `ClemencyEP`
    -   `GrandOakEP`
    -   `PineValleyEP`

3.  Save the project.

### Step 4: Build and run the artifacts

To test the artifacts, deploy the [packaged artifacts](#step-3-package-the-artifacts) in the embedded Micro Integrator:

1.  Right-click the composite application project and click **Export Project Artifacts and Run**.
2.  In the dialog that opens, select the artifacts that you want to deploy.  
4.  Click **Finish**. The artifacts will be deployed in the embedded Micro Integrator and the server will start. See the startup log in the **Console** tab. 

#### Get details of deployed artifacts (Optional)

Let's use the **CLI Tool** to find the URL of the REST API (that is deployed in the Micro integrator) to which you will send a request.

!!! Tip
    Be sure to set up the CLI tool for your work environment as explained in the [first step](#step-1-set-up-the-workspace) of this tutorial.

1.  Open a terminal and execute the following command to start the tool:
    ```bash
    mi
    ```
    
2.  Log in to the CLI tool. Let's use the server administrator user name and password:
    ```bash
    mi remote login admin admin
    ```
    
    You will receive the following message: *Login successful for remote: default!*

3.  Execute the following command to find the APIs deployed in the server:
    ```bash
    mi api show
    ```

    You will receive the following information:

    *NAME : HealthcareAPI*            
    *URL  : http://localhost:8290/healthcare* 

Similarly, you can get details of other artifacts deployed in the server. Read more about [using the CLI tool](https://ei.docs.wso2.com/en/7.0.0/micro-integrator/administer-and-observe/using-the-command-line-interface/).

#### Send the client request

1. Install and set up [cURL](https://curl.haxx.se/) as your REST client.
2. Create a JSON file named [] with the following request payload.
    ```json
    {
        "patient": {
        "name": "John Doe",
        "dob": "1940-03-19",
        "ssn": "234-23-525",
        "address": "California",
        "phone": "8770586755",
        "email": "johndoe@gmail.com"
        },
        "doctor": "thomas collins",
        "hospital": "grand oak community hospital",
        "appointment_date": "2025-04-02"
    }
    ```
3. Open a terminal and navigate to the directory where you have saved the `request.json` file.
4. Execute the following command.
    ```json
    curl -v -X POST --data @request.json http://localhost:8290/healthcare/categories/surgery/reserve --header "Content-Type:application/json"
    ```

#### Analyze the response

You will see the following response received to your <b>HTTP Client</b>:

```json
{ "patient": 
  { "name": "John Doe", 
    "dob": "1940-03-19", 
    "ssn": "234-23-525", 
    "address": "California", 
    "phone": "8770586755", 
    "email": "johndoe@gmail.com" }, 
  "doctor": "thomas collins", 
  "hospital": "grand oak community hospital", 
  "appointment_date": "2025-04-02" 
}
```

Now check the **Console** tab of WSO2 Integration Studio and you will see the following message: `INFO - LogMediator message = Routing to grand oak community hospital`
    
This is the message printed by the Log mediator when the message from the client is routed to the relevant endpoint in the Switch mediator.

You have successfully completed this tutorial and have seen how the requests received by the Micro Integrator can be routed to the relevant endpoint using the Switch mediator.


### Source code of the configuration:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<api context="/healthcare-route-based" name="HealthcareAPIRouteBased" xmlns="http://ws.apache.org/ns/synapse">
    <resource methods="POST" uri-template="/categories/{category}/reserve-without-settle-payment">
        <inSequence>
            <property description="get the hostpital value from payload and set to Hospital variable" expression="json-eval($.hospital)" name="Hospital" scope="default" type="STRING"/>
            <switch source="get-property('Hospital')">
                <case regex="grand oak community hospital">
                    <log level="custom">
                        <property expression="fn:concat('Routing to ', get-property('Hospital'))" name="message"/>
                    </log>
                    <call>
                        <endpoint key="GrandOakEP"/>
                    </call>
                    <respond/>
                </case>
                <case regex="clemency medical center">
                    <log level="custom">
                        <property name="message" value="fn:concat('Routing to ', get-property('Hospital'))"/>
                    </log>
                    <call>
                        <endpoint key="ClemencyEP"/>
                    </call>
                    <respond/>
                </case>
                <case regex="pine valley community hospital">
                    <log level="custom">
                        <property name="message" value="fn:concat('Routing to ', get-property('Hospital'))"/>
                    </log>
                    <call>
                        <endpoint key="PineValleyEP"/>
                    </call>
                    <respond/>
                </case>
                <default>
                    <log level="custom">
                        <property name="message" value="fn:concat('Invalid hospital - ', get-property('Hospital'))"/>
                    </log>
                </default>
            </switch>
        </inSequence>
        <outSequence/>
        <faultSequence/>
    </resource>
</api>

```
Defination Of the implemented mediators: 

Link: [https://docs.wso2.com/display/EI650/ESB+Mediators](https://docs.wso2.com/display/EI650/ESB+Mediators)

- ***Resource mediator(resource):***  Publishing the Rest Api for the subcriber.A subcriber can access the api using `https/http://<APIM-IP>/querydoctor/{category}`

- ***InSequence (inSequence):*** The Sequence Mediator refers to an already defined sequence element, which is used to invoke a named sequence of mediators. This is useful when you need to use a particular set on mediators in a given order repeatedly.

- ***Log mediator(log)***: The Log mediator is used to log mediated messages.

- ***Call mediator(call)***: The Call mediator is used to send messages out of the ESB profile to an endpoint. You can invoke services either in blocking or non-blocking manner. 

- ***OutSequence mediator***: Applies to messages that are in the Out path of the ESB profile.

- ***Switch Mediator***: The Switch mediator works like `Switch Case Statement` of any programming language. It transfer the request to desired case statement based to switching key and the case transfer the request to desired service.

