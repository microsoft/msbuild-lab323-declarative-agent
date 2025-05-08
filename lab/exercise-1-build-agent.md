# Exercise 1:  Build your first agent using Microsoft 365 Agents Toolkit

So far what you did was set up the environment to build agents. It’s time to build your first declarative agent using Microsoft 365 Agents Toolkit. 
You will create an agent called **RepairServiceAgent**, which interacts with repairs data via an API service to help users manage car repair records.
So, let’s get to it step by step.
## Step 1: Scaffold your base agent project using Microsoft 365 Agents Toolkit
-	Locate the Microsoft 365 Agents Toolkit icon from the VS Code menu on the left and select it. An activity bar will be open. 
-	Select the “Create a New Agent/App” button in the activity bar which will open the palette with a list of app templates available on Microsoft 365 Agents Toolkit.
-	Choose “Declarative Agent” from the list of templates.
-	Next, select “Start with TypeSpec for Microsoft 365 Copilot” to define your agent using TypeSpec.
-	Next, select the folder where you want the toolkit to scaffold the agent project.
-	Next, give an application name like - “RepairServiceAgent” and select Enter to complete the process. You will get a new VSCode window with the agent project preloaded.

## Step 2: Sign into the Microsoft 365 Agents Toolkit 

You'll need to sign into the Microsoft 365 Agents Toolkit inorder to upload and test your agent from within the toolkit.

-	In project window, select the Microsoft 365 Agents Toolkit icon again from the left side menu. This will open the toolkit’s activity bar with sections like Accounts, Environment, Development etc. 
-	Under Accounts section select “Sign in to Microsoft 365”. This will open a dialog from the editor to sign in or create a Microsoft 365 developer sandbox or cancel. Select “Sign in”. 
-	In the virtual machine, the credentials to log into the Microsoft 365 tenant will be given in the same “Resources” panel where this instruction is, under title “Azure portal”. Use the username and password provided. Make sure you use the copy text instruction.
-	Once signed it, close the browser and go back to the project window.

## Step 3: Define your agent 

In the project folder, you will find the two TypeSpec files **main.tsp** and **actions.tsp**.
The agent is defined with its metadata, instructions and capabilities in the main.tsp file.
Use the actions.tsp file to define your agent’s actions. If your agent includes any actions, this is the file where they should be implemented.

Let’s open main.tsp and inspect what is there in the default template, which we will modify for our agent’s scenario. 


### Update the Agent Metadata and instructions

In the main.tsp file you will find the basic structure of the file. Review the content provided by the template which includes:
-	Agent name and description
-	Basic instructions
-	Placeholder code for actions and capabilities (commented out)

Let’s begin defining our agent for this scenario. Replace the @agent and @instructions definitions with below code snippet

```
@agent(
  "RepairServiceAgent",
   "An agent for managing repair information"
)

@instructions("""
  ## Purpose
You will assist the user in finding car repair records based on the information provided by the user. 
""")

```
Next, we will add some conversation starters for our agent. Just below the instructions you will see a commented conversation starter definition. Uncomment it.
And replace title and text as below.

```
// Uncomment this part to add a conversation starter to the agent.
// This will be shown to the user when the agent is first created.
@conversationStarter(#{
  title: "List repairs",
  text: "List all repairs"
})

```

### Update the action for the agent

Next, define the action for your agent by opening the actions.tsp file. You’ll return to the main.tsp file later to complete the agent metadata with the action reference, but first, the action itself must be defined.

The placeholder code in actions.tsp is designed to search for open issues in a GitHub repository. It serves as a starting point to help newcomers understand how to define an action for their agent like action’s metadata, API host url and operations or functions and their definitions. 

After the general import and using statements, replace the code snippet with below  to define action metadata and server url. The namespace is also changed from  GitHubAPI to RepairsAPI

```
@service
@server(RepairsAPI.SERVER_URL)
@actions(RepairsAPI.ACTIONS_METADATA)
namespace RepairsAPI{
  /**
   * Metadata for the API actions.
   */
  const ACTIONS_METADATA = #{
    nameForHuman: "Repair Service Agent",
    descriptionForHuman: "Manage your repairs and maintenance tasks.",
    descriptionForModel: "Plugin to add, update, remove, and view repair objects.",
    legalInfoUrl: "https://docs.github.com/en/site-policy/github-terms/github-terms-of-service",
    privacyPolicyUrl: "https://docs.github.com/en/site-policy/privacy-policies/github-general-privacy-statement"
  };
  
  /**
   * The base URL for the  API.
   */
  const SERVER_URL = "https://repairshub.azurewebsites.net";

```

Next, we will replace the only operation in the template code from searchIssues to a repair operation to get list of repairs.
To do that replace the entire block of code after the SERVER_URL definition with below snippet. 

```
  /**
   * List repairs from the API 
   * @param assignedTo The user assigned to a repair item.
   */

  @route("/repairs")
  @get  op listRepairs(@query assignedTo?: string): string;
 }


````

Now let’s go back to main.tsp file and add this action into the agent. After the conversation starters replace the entire block of code with below snippet.

```
namespace RepairServiceAgent{  
  // Uncomment this part to add actions to the agent.
  @service
  @server(global.RepairsAPI.SERVER_URL)
  @actions(global.RepairsAPI.ACTIONS_METADATA)
  namespace RepairServiceActions {
    op listRepairs is global.RepairsAPI.listRepairs;   
  }
}
```


## Step 4: (Optional) Understand the code
This is an optional step but if curious to know what we have defined in the TypeSpec file just read through this step, or if you wish to test the agent right away go to Step 5.

A declarative agent application package typically consists of the following files
-	A M365 app manifest in JSON (the standard teams app manifest file called manifest.json)
-	A Declarative agent manifest in JSON which will consist of the agent’s name, instructions, capabilities, conversation starters and actions if needed.
-	An optional plugin manifest in JSON to configure your action as an API plugin if they have authentication, required fields, adaptive card responses etc. This file is only needed if you have an action in your agent.
-	An optional OpenAPI spec file in JSON or YAML for your API definitions. Again, only needed if you have an action in your agent

Clearly that is a lot of file that developers need to maintain. To simplify this, we use a single TypeSpec file that generates all necessary manifests from one definition. TypeSpec helps define data and services, reducing errors in API development and consumption. 

In the TypeSpec files main.tsp and actions.tsp, you'll find decorators (starting with @), namespaces, models, enums, and other definitions for your agent.

Check this table to understand some of the decorators used in these files 



| Annotation             | Description                                                                                                                                                     |
|------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------|
| @agent             | Defines the namespace (name) and description of the agent                                                                                                       |
| @instructions       | Defines the instructions that prescribe the behaviour of the agent. 8000 characters or less                                                                     |
| @conversationStarter | Defines conversation starters for the agent                                                                                                                     |
| @op            | Defines any operation. Either it can be an operation to define agent’s capabilities like **op GraphicArt**, **op CodeInterpreter** etc., or define API operations like **op listRepairs**. For a post operation, define it like: **op createRepair(@body repair: Repair): Repair; **                                                                                                                |
| @actions             | Defines the name and descriptions for human and model to be used in various manifest files                                                                      |
| @server           | Defines the server endpoint of the API and its name                                                                                                              |
| @capabilities      | When used inside a function, it defines simple adaptive cards with small definitions like a confirmation card for the operation                                  |

## Step 5: Test your agent

Next step is to test the Repair Service Agent. 

- Select the Agent Toolkit extension, to open the activity bar from within your project.
- In the activity bar of the toolkit under “LifeCycle” select “Provision”. This will build the app package consisting of the generated manifest files and icons and side load the app into the catalog only for you to test. 

> 💡 **Note**  
> Here the toolkit also helps validate all the definitions provided in the TypeSpec file to ensure accuracy. It also identifies errors to streamline the developer experience.

- Next, go to https://m365.cloud.microsoft/chat to open Copilot app and select the **RepairServiceAgent** from the right side of the screen under **Agents**.
- Select a conversation starter like “List repairs” and see check out the response.


> 💡 **Tip**  
> When prompted to connect the agent to process a query, you’ll usually see this screen just once.  
> To streamline your experience in this lab, select **“Always allow”** when it appears.





