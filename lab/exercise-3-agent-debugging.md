# Exercise 3: Diagnosing and Debugging Agent

You can enable developer mode in a chat to allow you as a developer to understand how well the agent understands the tasks, ensure it calls your services appropriately, identify areas that need fine-tuning, detect performance issues, and generally help you track and analyse its interactions.

## Step 1: Agent debugging in the chat

- Copy and paste the following line into the chat with your agent to enable debugging mode.

    ```
    -developer on
    ```
- Next to test, send a prompt to interact with the agent like the one below.

   +++*Find out what Karin is working on.*+++

- You will get a response with information from the repair service but also get the **Agent debug info** card along with the response.
- Expand the **Agent debug info** card to view all the details.
- You will be able to see: 
    -	Agent information 1️⃣
    -	Capabilities of the agent 2️⃣
    -	Actions and what function were selected 3️⃣
    -	Executed action info with detailed information about the request, latency, response data etc. 4️⃣

![agent debug info](https://github.com/microsoft/msbuild-lab323-declarative-agent/blob/main/lab/images/exercise-3/agent-info.png)  

## Step 2: Agent debugging in Agents Toolkit

If you want to see debugger information on your code editor where you are working, that is also possible now using the Microsoft 365 Agents Toolkit. 

- Go back to your project in VS Code.
- Select the debug icon from the VS Code menu on the left side of the editor. 
- Select the run and debug button > Preview in Copilot. This will open the browser where you can login to your tenant with your tenant credentials you did in Exercise 1. This will open your agent right away for test. 
- Next, select the conversation starter prompt – *List all repairs* and send it to the agent chat and keep watching the Debug console in the editor.
- You will be able to see the debugger information in the console as shown below. 

☑️ You've successfully completed the all exercises! Select **Next >** to go see the finish line 😎