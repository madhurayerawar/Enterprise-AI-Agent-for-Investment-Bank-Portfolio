# Enterprise-AI-Agent-for-Investment-Bank-Portfolio

### Summary
This project sets up an Amazon Bedrock agent with an action group to dynamically create investment company portfolios and provide Q&A for FOMC reports, using Streamlit for the UI. It also includes an email-sending method (not fully configured).
For IaC users, an AWS CloudFormation template deploys key resources (S3, action group, Lambda), though the knowledge base setup remains manual. Refer to the workshop guide for CloudFormation deployment.
Alternatively, this README provides a step-by-step manual setup using the AWS Console.

![Architecture of Workshop](https://github.com/user-attachments/assets/f2a5a082-eb23-4ce9-bace-39bb751e8caa)

### Setup 
#### 1.	Creating S3 buckets: 
  a.	Goto S3 Bucket on console and Click Create Bucket.
  b.	Give Bucket name and click Create Bucket.
  c.	Upload FOMC documents to the S3 bucket. These files are the Federal Open Market Committee documents describing monetary policy decisions made at the Federal Reserve board meetings. The documents include 
   discussions of economic conditions, policy directives to the Federal Reserve Bank of New York for open market operations, and votes on the federal funds rate.
![S3 RAG](https://github.com/user-attachments/assets/0c28d435-f3c3-4f28-90a8-a39c7c634773)

#### 2.	Knowledge Base Setup in Bedrock Agent: 
a.	Before we set up the knowledge base we need to grant access to the models: Anthropic: Claude 3 Haiku and Titan Text Embeddings V2. 
b.	Go to Amazon Bedrock on console and on the left-hand side panel select Knowledge bases and Create knowledge base 
c.	Click Next, keeping everything default and ensuring Data Source as Amazon S3
d.	Browse to add S3 URI and Click Next
e.	Select Embedding models as Titan Text Embeddings V2 and keeping rest of the attributes default, click on Next.
f.	Review all the fields and click Create Knowledge Base (This may take few minutes depending on the size of your domain data. Please wait till it completes.)
![knowledge base embedding models](https://github.com/user-attachments/assets/db6c3a50-9118-4fc9-82cf-d7cf0b619441)
![knowledge base](https://github.com/user-attachments/assets/36670a72-9112-4f51-bca3-031e0cb4c58c)

#### 3.	Creating Lambda Function:
a.	Goto Lambda on Console and click on Create a Lambda Function
b.	Select Author from Scratch and provide Name for the function
c.	Select Runtime as Python 3.12
d.	Click Create Function
e.	Implement the Lambda function to represent various companies along with functions and Click Deploy
f.	Go to Configuration Tab and then click Permissions 
g.	Scroll to Resource-based policy statements section and click Add permissions
h.	To give allow any Agent in the account to invoke Lambda function. Select-
    i.	 Select AWS Service
    ii.	 Service: Other
    iii. Statement ID: allow-bedrock-agent
    iv.	 Principal: bedrock.amazonaws.com
    v.	 Source ARN: arn:aws:bedrock:{aws-region}:{account-id}:agent/*
    vi.	 Action: lambda:InvokeFunction
    vii. Click Save
![agent lambda](https://github.com/user-attachments/assets/9e16ce35-1faa-45e9-bbf1-f364217c5043)

#### 4.	Setup Bedrock Agent and Action Group
a.	Goto Amazon Bedrock on console
b.	On left hand side panel select Agents and click on Create Agent.
c.	Provide Agent name and Click Create
d.	Select model as Claude 3 Haiku
e.	Add Agent Instructions – these instructions outlines a comprehensive framework which streamlines investment analysis by generating industry-specific company portfolios, conducting in-depth research, and 
    summarizing financial documents. It adapts to user feedback and maintains contextual awareness for accurate, efficient responses.
f.	Next we will add Action Group-
    i.	Scroll down to Action groups and click Add
    ii.	Provide Action Group name
    iii.	Action group type: Define with API schemas
    iv.	Action group invocations: Existing Lambda function and provide the function name
    v.	Action group schema: Provide JSON to represent schema so that the bedrock agent knows the format structure and parameters needed for the action group to interact with the Lambda function for endpoints - 
           /companyResearch, /createPortfolio etc.
    vi.	In Advanced Prompts Section Click Edit. We must provide the Bedrock agent with a prompt showcasing of a formatted investment company portfolio. The agent was initially set up with four foundational 
        prompt templates for pre-processing, orchestration, knowledge base response generation, and post-processing. These templates guide interactions with the foundation model, manage workflow, and format user 
        responses. Customizing them with advanced prompts or few-shot examples enhances accuracy and performance.
    vii.	This will override orchestration
    ![agent action group](https://github.com/user-attachments/assets/83b52ab5-a2a2-4a36-be8c-9bf75a824ccc)
    ![Agent-Portfolio creator](https://github.com/user-attachments/assets/9ba009f7-6298-4dbe-ad91-5ee02ec0120c)


#### 5.	Setup Knowledge Base with Bedrock Agent:
a.	While on Bedrock Agent console scroll down to knowledge base and click Add
b.	When integrating the KB with the agent,  need to provide basic instructions on how to handle
c.	Click Add. Scroll top
d.	Click Prepare to udpdate the changes and click Save and Exit
![action group agent schema](https://github.com/user-attachments/assets/2c6fdec1-f2e8-426f-97c9-ca00fbca65ec)

#### 6.	Create an Alias
a.	 To create a new version we create new alias. 
b.	Give alias name and click Create
c.	Note Agent Id and Alias Id to update it in application 

#### 7.	Go to Amazon Bedrock -> Knowledge bases -> select your KB -> click Sync
![knowledge base](https://github.com/user-attachments/assets/baf8585c-5a26-4de6-8d71-dbd723ab8e1a)

#### 8.	Setup and run Streamlit app on EC2
a.	Connect the EC2
b.	Edit app/streamlit_app/invoke_agent.py
c.	Update Agent ID, Alias ID and respective AWS Region
d.	Save the file
e.	Run the application using below instruction:
    i.	streamlit run app/streamlit_app/app.py
    ii.	Copy the Externla URL and paste in on the Browser to access the application and use Agent interactively.
![EC2](https://github.com/user-attachments/assets/f745a7a7-566d-48d4-bb64-19b12bbf86d7)
![workshop streamlit demostration](https://github.com/user-attachments/assets/428468d9-a1f9-4848-bad8-bfec2e85e79a)


All the related screenshots are attached below. Have you tried creating your won AI agent yet?
