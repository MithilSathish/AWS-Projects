**Project 2 : Create a CRUD HTTP API with Lambda and DynamoDB**

Objective: Create a serverless API to perform CRUD operations on a DynamoDB table.

Steps:
1.	Create DynamoDB Table: Use the DynamoDB console to set up your table.
2.	Set Up Lambda Function: Create a Lambda function to handle CRUD operations.
3.	Configure API Gateway: Create an HTTP API that routes requests to your Lambda function.
4.	Test the API: Invoke the API to ensure it interacts with DynamoDB as expected.

Operation Flow:
•	Invoke API: API Gateway routes requests to Lambda.
•	Lambda Execution: Lambda performs CRUD operations on DynamoDB.
•	Response: Lambda sends the result back through API Gateway.

![image](https://github.com/user-attachments/assets/88de7103-9ff1-42be-877a-7494d2032905)

 
Step 1: Create a DynamoDB Table
1.	Open DynamoDB console.
2.	Click Create table.
3.	Enter Table name as http-crud-tutorial-items.
4.	Set Partition key to id.
5.	Click Create table.

 ![image](https://github.com/user-attachments/assets/4b1ceb36-30af-4e34-a994-67b0ce86d549)

Step 2: Create a Lambda Function
1.	Go to Lambda console.
2.	Click Create function.
3.	Name it http-crud-tutorial-function.
4.	Choose a runtime (Node.js or Python).
5.	Under Permissions, select Change default execution role.
6.	Create a new role named http-crud-tutorial-role.
7.	Use the Simple microservice permissions policy.
8.	Click Create function.
 
 ![image](https://github.com/user-attachments/assets/6bfb63b5-423e-4f71-8784-10a539c68282)


9.	Edit the function code and click Deploy.
 
![image](https://github.com/user-attachments/assets/3cc00e76-c47e-4995-a002-e936a4fc86b9)



Step 3: Create an HTTP API
1.	Go to API Gateway console.
2.	Click Create API and select HTTP API then Build.
3.	Enter API name as http-crud-tutorial-api.
4.	Click Next.
5.	Skip route creation by clicking Next.
6.	Review the stage and click Next.
7.	Click Create.
 
![image](https://github.com/user-attachments/assets/4d88b65d-5e8c-4c61-ac98-72200c797b80)


Step 4: Create Routes
1.	Go to API Gateway console.
2.	Select your API.
3.	Click Routes.
4.	Click Create.
5.	For Method, choose GET.
6.	For Path, enter /items/{id} and click Create.
7.	Repeat steps 4-6 for the following routes:
o	GET /items
o	DELETE /items/{id}
o	PUT /items

 ![image](https://github.com/user-attachments/assets/8b4c54d3-8ccb-4e17-b4b6-a96eb1613422)



Step 5: Create an Integration
1.	Go to API Gateway console.
2.	Select your API.
3.	Click Integrations.
4.	Click Manage integrations and then Create.
5.	Skip Attach this integration to a route (you’ll do this later).
6.	For Integration type, choose Lambda function.
7.	Enter Lambda function as http-crud-tutorial-function.
8.	Click Create.

 ![image](https://github.com/user-attachments/assets/10c0212a-dc6e-418e-96db-cf53873c3b12)


Step 6: Attach Your Integration to Routes
1.	Go to API Gateway console.
2.	Select your API.
3.	Click Integrations.
4.	Choose a route.
5.	Under Choose an existing integration, select http-crud-tutorial-function.
6.	Click Attach integration.
7.	Repeat steps 4-6 for all routes.
All routes should now show an AWS Lambda integration.
 
![image](https://github.com/user-attachments/assets/c3e6f585-4902-481b-89a1-8283e440b5c7)


Step 7: Test Your API
1.	Get the API URL:
o	Go to API Gateway console.
o	Select your API.
o	Note the Invoke URL from the Details page and copy it.

 ![image](https://github.com/user-attachments/assets/a9db8a1f-e83e-481c-8b95-b784d0d5db9e)


2.	Create or Update an Item: Use the URL to send a request to create or update an item.
3.	Get All Items: Use the URL to list all items.
4.	Get an Item by ID: Use the URL to retrieve an item by its ID.
5.	Delete an Item: Use the URL to delete an item.
6.	Verify Deletion: Use the URL to ensure the item has been deleted.

 ![image](https://github.com/user-attachments/assets/88120188-971c-4888-9ec7-ea1aa290d624)
