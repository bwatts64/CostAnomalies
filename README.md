# Azure Cost Anomalies  

Below we will walk through a solution that calls the Azure Cost Management Rest API from Logic Apps and inserts the data into Log Analytics. We will then use the Kusto Query language to find anomalies in the data.  

## Required Resources  

-- Logic Apps  
-- Log Analytics Workspace  
-- Azure Monitor Workbook  
-- Service Principal that can read Cost Data  

##  Creating the Logic Apps workflow  

1) Create a new Logic Apps and select "Blank Logic App" Template  

<img src="./pics/BlankTemplate.PNG" alt="Designer"  Width="200">  

2) In the template designer select code view  

<img src="./pics/CodeView.PNG" alt="Code View"  Width="600">

3) Remove the content in the Code View and replate it with the content of the "LogicApp.json" found in this repository  

<img src="./pics/CodeViewContent.PNG" alt="Code View Content"  Width="600">  

4) Click on "Save" and then back on the "Designer" view  

5) Modify the "Initialize Secret" step adding in the secret for your Service Principal

<img src="./pics/InitializeSecret.PNG" alt="Initialize Secret"  Width="400">  

5) Modify the "HTTP" step modifying the following:  
- URL: Modify the scope
- Tennant: Add the tennant id where your service principal is located
- Client ID: Add the client id for your service principal  

<img src="./pics/HTTP1.PNG" alt="Scope"  Width="600">  

<img src="./pics/HTTP2.PNG" alt="Service Principal"  Width="600">

6) Expand the "Foreach" loop and below the "Set JSON" step click on "Add Action"  

<img src="./pics/ForEach.PNG" alt="Loop"  Width="600">  

7) Search for "Log Analytics Data Collector" and choose the "Send Data (preview)" action  

<img src="./pics/SendData.PNG" alt="Send Data Action"  Width="600">  

8) Fill out the following to make the connection to Log Analytics  
- Connection Name: Display Name for the connection  
- Workspace ID: Gather from Log Analytics  
- WOrkspace Key: Gather from Log Analytics  

9) Click in the "JSON Request Body" field and when the dynamic content box comes up change to the "Expression" tab  

<img src="./pics/RequestBody.PNG" alt="Request Body"  Width="700"> 

10) In the Expression field enter the below and then click on "Ok"  

    concat('{"CostData":',items('For_each'), '}')

<img src="./pics/Expression.PNG" alt="Expression"  Width="300">   

11) For the Custom Log Name enter "AuzreCostDetails". The action should look like: 

<img src="./pics/SendDataDetails.PNG" alt="Send LA Data"  Width="500">  

12) Save the Logic App and if you want to test it you can click on "Run"    
  
## Creating the Azure Monitor Workbook  
1) Open Azure Monitor and go to the Workbook section  

<img src="./pics/Workbooks.PNG" alt="Workbooks"  Width="300">  

2) Click "New" to start an empty Azure Workbook  

3) Click on the "Advanced Editor" option across the toolbar  

<img src="./pics/Advanced Editor.PNG" alt="Advanced Editor"  Width="600">  

4) Replace all of the content with the content found in "CostWorkbook.json" in the repo  

<img src="./pics/WorkbookDetails.PNG" alt="Workbook Details"  Width="600">  

5) Click on Apply which will take you back to the editor  

6) Go ahead and use the drop down to select the Log Analytics Workspace where you inserted the data using Logic Apps  

7) At the top click on "Done Editing" and then "Save" your workbook
