# Exercise 2:  Enhance Agent using TypeSpec for new capability

Next, you will enhance the agent's capabilities to not only consume the API for reading or updating data for the repair service but also to transform the agent into an analyst. This will enable the agent to provide insights and generate reports from the existing data. Proceed with the following updates to the main.tsp file:

## Step 1: Modify agent typespec file

•	Copy and paste the below new instruction inside the @instructions decorator.

```
## Purpose

You will assist the user in finding car repair records based on the information provided by the user. The user will provide relevant details, and you will need to understand the user's intent to retrieve the appropriate car repair records. 
You can only access and leverage the data from the 'repairPlugin' action. You are also an analyst with repair data and can print graphs and data to give insights of your data to the user.

## DESIGN SPECIFICATIONS:

- Use a clean, modern layout 
- Organize components in a responsive grid layout
- Use a light color scheme with appropriate accent colors for charts
- Include proper spacing between components

## Execution Steps

- while the request from user is only to create/update/delete or get repair information, use the repairs data from the API and do the needed operation
- If user asks for reports, always fetch the latest data and create a comprehensive report and also provide insights into underlying issues, resolution steps in bullet points,
how to improve efficiency for each problem.

```

•	Add CodeInterpreter capability into the agent by copying and pasting below operation inside the RepairServiceAgent namespace just above @service decorator.

```
  op codeInterpreter is AgentCapabilities.CodeInterpreter;
```

## Step 2:  Provision and Test the Agent's
Let’s take the updated agent who is also now a repairs analyst to test. 
Select the Agent Toolkit extension to open its activity bar from within your project.
In the activity bar of the toolkit under “LifeCycle” select “Provision” to package and upload the newly updated agent for testing. 
Next, go to https://office.com/chat to open Copilot app and select the RepairServiceAgent from the right side of the screen under Agents.
Next, copy the prompt below to test the new new agent with new analytical capability with your data. 

*Classify repair items into three distinct categories: Routine Maintenance, Critical, and Low Priority. Then, generate a pie chart displaying the percentage representation of each category. Use unique colours for each group and incorporate tooltips to show the precise values for each segment.*
