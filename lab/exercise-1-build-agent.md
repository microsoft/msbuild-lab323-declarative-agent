So far what you did was set up the environment to build agents. It’s time to build your first declarative agent using Microsoft 365 Agents Toolkit. 
You will create an agent called RepairServiceAgent, which interacts with repairs data via an API service to help users manage car repair records.
So, let’s get to it step by step.
## Step 1: Scaffold your base agent project using Microsoft 365 Agents Toolkit
-	Locate the Microsoft 365 Agents Toolkit icon from the VS Code menu on the left and select it. An activity bar will be open. 
-	Select the “Create a New Agent/App” button in the activity bar which will open the palette with a list of app templates available on Microsoft 365 Agents Toolkit.
-	Choose “Declarative Agent” from the list of templates.
-	Next, select “Start with TypeSpec for Microsoft 365 Copilot” to define your agent using TypeSpec.
-	Next, select the folder where you want the toolkit to scaffold the agent project.
-	Next, give an application name like - “RepairServiceAgent” and select Enter to complete the process. You will get a new VSCode window with the agent project preloaded.
Step 2: Sign into the Microsoft 365 Agents Toolkit 
-	In project window, select the Microsoft 365 Agents Toolkit icon again from the left side menu. This will open the toolkit’s activity bar with sections like Accounts, Environment, Development etc. 
-	Under Accounts section select “Sign in to Microsoft 365”. This will open a dialog from the editor to sign in or create a Microsoft 365 developer sandbox or cancel. Select “Sign in”. 
-	In the virtual machine, the credentials to log into the Microsoft 365 tenant will be given in the same “Resources” panel where this instruction is, under title “Azure portal”. Use the username and password provided. Make sure you use the copy text instruction.
-	Once signed it, close the browser and go back to the project window.

## Step 3: Define your agent using TypeSpec
Next, you will define the agent using the TypeSpec file called “main.tsp” in your project, the only file you will be modifying as you build your agent.

### Setting up Agent metadata

In the main.tsp file you will find the basic structure of the file. Review the content provided by the template which includes:
-	Agent name and description
-	Basic instructions
-	Placeholder code (commented out)
Now let’s us start defining our agent. Replace the contents in the default file and paste below snippet to define the agent’s metadata

```
import "@typespec/http";
import "@typespec/openapi3";
import "@microsoft/typespec-copilot-skills";

using TypeSpec.Http;
using TypespecCopilotSkills;
using TypespecCopilotSkills.Agents;

@agent(
  "RepairServiceAgent",
  "An agent for managing repair information"
)

@instructions("""
## Purpose

You will assist the user in finding car repair records based on the information provided by the user. 

""")
@conversationStarter(#{
  title: "List repairs",
  text: "List all repairs"
})
@conversationStarter(#{
  title: "Create repair",
  text: "Create a new repair titled [TO_REPLACE] and assign it to me"
})
```

As you can see, you have defined name, description, system prompt/instructions and conversation starters. In a simple instruction based agent, this is all you need to provide.

### Set up the Repair Service Logic for the agent

In our case, we need to also add our Repair Service logic than just instructions. To do that let us now paste below code snippet to define operations and data model of your service for your agent.



```
namespace RepairServiceAgent {

  @service
  @skill(#{
    nameForHuman: "Repair Service Agent",
    descriptionForHuman: "Manage your repairs and maintenance tasks.",
    descriptionForModel: "Plugin to add, update, remove, and view repair objects.",
  })
  @server("https://repairshub.azurewebsites.net", "Repairs Hub API")
  namespace RepairsHub {
    /**
     * List all repairs.
     * @param assignedTo Optional filter to list repairs assigned to a specific user.
     */
    @state(orchestratorState.responding, 
      #{
        instructions: """    
            When listing more than one repairs, display them in a neat table. For single item display in rich cards.  
        """
      })
    @route("/repairs")
    @get
    @capabilities(#{
      responseSemantics: #{
        dataPath: "$",
        properties: #{
          title: "$.title",
          subTitle: "$.description",
          url: "$.image",
          thumbnailUrl: "$.image"
        },
        staticTemplate: #{
          $schema: "http://adaptivecards.io/schemas/adaptive-card.json",
          type: "AdaptiveCard",
          version: "1.4",
        body: #[
          #{
            type: "Container",
            $data: "\${\$root}",
            items: #[
           #{ 
          type: "ColumnSet",       
          columns: #[ 
            #{ 
              type: "Column",
              width: "auto",
              items: #[ 
                #{ 
                  type: "Image",
                  url: "\${image}",
                  $when: "\${image != null}", 
                  size: "Medium",
                  style: "default"
                } 
              ] 
            },
            #{ 
              type: "Column",
              width: "stretch",
              items: #[ 
                #{ 
                  type: "TextBlock",
                  text: "\${if(title, title, 'N/A')}",
                  weight: "Bolder",
                  size: "Large",
                  wrap: true
                },
                #{ 
                  type: "TextBlock",
                  text: "Assigned to: \${if(assignedTo, assignedTo, 'N/A')}",
                  spacing: "Small",
                  isSubtle: true,
                  wrap: true
                },
                #{ 
                  type: "TextBlock",
                  text: "Due Date: \${if(date, date, 'N/A')}",
                  spacing: "Small",
                  isSubtle: true,
                  wrap: true
                } 
            ] 
          } 
    ] 
  },  #{ 
    type: "TextBlock",
    text: "\${if(description, description, 'N/A')}",
    wrap: true,
    spacing: "Medium"
  } ]

}],
actions: #[
            #{
              type: "Action.OpenUrl",
              title: "View Repair",
              url: "https://www.bing.com/search?q=\${image}",
            }
          ]
        },
      }
    })
    op listRepairs(@query assignedTo?: string): string;

    /**
     * Create a new repair. 
     * When creating a repair, the `id` field is optional and will be generated by the server.
     * The `date` field should be in ISO 8601 format (e.g., "2023-10-01T12:00:00Z").
     * The `image` field should be a valid URL pointing to the image associated with the repair.
     * @param repair The repair to create.
     */
    @route("/repairs")
    @capabilities(#{
      confirmation: #{
        type: "AdaptiveCard",
        title: "Create a new repair",
        body: """   
        Creating a new repair with the following details:       
          * **Title**: {{ function.parameters.title }}
          * **Description**: {{ function.parameters.description }}
          * **Assigned To**: {{ function.parameters.assignedTo }}
        """
      }
    })
    @post
    op createRepair(@body repair: Repair): Repair;

    /**
     * Update an existing repair.
     * The `id` field is required to identify the repair to update.
     * The `date` field should be in ISO 8601 format (e.g., "2023-10-01T12:00:00Z").
     * The `image` field should be a valid URL pointing to the image associated with the repair.
     * @param repair The repair to update.
     */
    @route("/repairs")
    @state(orchestratorState.reasoning, 
      #{
        instructions: """
          **ALWAYS** use the listRepairs function to get the ID first.
        """
      })
    @capabilities(#{
      confirmation: #{
        type: "AdaptiveCard",
        title: "Create a new repair",
        body: """   
        Updating a repair with the following details:       
          * **ID**: {{ function.parameters.id }}      
          * **Title**: {{ function.parameters.title }}
          * **Description**: {{ function.parameters.description }}
          * **Assigned To**: {{ function.parameters.assignedTo }}
          * 
        """
      }
    })
    @patch
    op updateRepair(@body repair: Repair): Repair;

    /**
     * Delete a repair.
     * The `id` field is required to identify the repair to delete.
     * @param repair The repair to delete.
     */
    @route("/repairs")
    @state(orchestratorState.reasoning, 
      #{
        instructions: """
          **ALWAYS** use the listRepairs function to get the ID first.
        """
      })
    @capabilities(#{
      confirmation: #{
        type: "AdaptiveCard",
        title: "Delete a repair",
        body: """   
        Deleting a repair with the following details:       
          * **ID**: {{ function.parameters.id }}
        """
      }
    })
    @delete
    op deleteRepair(@body repair: Repair): Repair;
    
    /**
     * A model representing a repair.
     */
    model Repair {
      /**
       * The unique identifier for the repair.
       */
      id?: string;

      /**
       * The short summary or title of the repair.
       */
      title: string;

      /**
       * The detailed description of the repair.
       */
      description?: string;

      /**
       * The user who is assigned to the repair.
       */
      assignedTo?: string;

      /**
       * The optional date and time when the repair is scheduled or completed.
       */
      @format("date-time")
      date?: string;

      /**
       * The URL of the image associated with the repair.
       */
      @format("uri")
      image?: string;
    }

    enum orchestratorState {
      reasoning,
      responding
    }
  }
}
```

Inside the RepairServiceAgent namespace, add the full service definition including:
@service and @skill metadata
Operations: listRepairs, createRepair, updateRepair, deleteRepair
The Repair model and orchestratorState enum

Your agent should now be fully defined with metadata, instructions, and operational logic.

## Step 4: (Optional) Understand the code
This is an optional step but if curious to know what we have defined in the TypeSpec file just read through this step, or if you wish to test the agent right away go to Step 5.

A declarative agent application package typically consists of the following files
-	A M365 app manifest in JSON (the standard teams app manifest file called manifest.json)
-	A Declarative agent manifest in JSON which will consist of the agent’s name, instructions, capabilities, conversation starters and actions if needed.
-	An optional plugin manifest in JSON to configure your action as an API plugin if they have authentication, required fields, adaptive card responses etc. This file is only needed if you have an action in your agent.
-	An optional OpenAPI spec file in JSON or YAML for your API definitions. Again, only needed if you have an action in your agent

Clearly that is a lot of file that developers need to maintain. To simplify this, we use a single TypeSpec file that generates all necessary manifests from one definition. TypeSpec helps define data and services, reducing errors in API development and consumption. 

Let's understand the TypeSpec file. Open main.tsp from the project's root. In this file, you'll find decorators (starting with @), namespaces, models, enums, and other definitions for your agent.

Check this table to understand the decorators used in main.tsp file. 


| Annotation             | Description                                                                                                                                                     |
|------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `@agent`              | Defines the namespace (name) and description of the agent                                                                                                       |
| `@instructions`       | Defines the instructions that prescribe the behaviour of the agent. 8000 characters or less                                                                     |
| `@conversationStarter`| Defines conversation starters for the agent                                                                                                                     |
| `@op`                 | Defines any operation. Either it can be an operation to define agent’s capabilities like `op GraphicArt`, `op CodeInterpreter` etc., or define API operations like `op listRepairs`. For a post operation, define it like: `op createRepair(@body repair: Repair): Repair; |
`                                                                          |
| `@skill`              | Defines the name and descriptions for human and model to be used in various manifest files                                                                      |
| `@server`             | Defines the server endpoint of the API and its name                                                                                                              |
| `@state`              | Defines reasoning and responding states of the orchestrator of any function                                                                                     |
| `@capabilities`       | When used inside a function, it defines simple adaptive cards with small definitions like a confirmation card for the operation                                  |

Step 5: Test your agent
Next step is to test the Repair Service Agent. 
Select the Agent Toolkit extension, to open the activity bar from within your project.
In the activity bar of the toolkit under “LifeCycle” select “Provision”. 
This will build the app package consisting of the generated manifest files and icons and side load the app into the catalog only for you to test. 
Here the toolkit also helps validate all the definitions provided in the TypeSpec file to ensure accuracy. It also identifies errors to streamline the developer experience.
Next, go to https://m365.cloud.microsoft/chat to open Copilot app and select the RepairServiceAgent from the right side of the screen under Agents.
Select a conversation starter like “List repairs” and see check out the response.


> 💡 **Tip**  
> When prompted to connect the agent to process a query, you’ll usually see this screen just once.  
> To streamline your experience in this lab, select **“Always allow”** when it appears.





