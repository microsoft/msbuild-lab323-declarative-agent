# What are Declarative Agents? 

**Declarative agents** leverage the same scalable infrastructure and platform of Microsoft 365 Copilot, tailored specifically to meet focus on a special area of your needs. They function as subject matter experts in a specific area or business need, allowing you to use the same interface as a standard Microsoft 365 Copilot chat while ensuring they focus exclusively on the specific task at hand.

## Anatomy of a Declarative Agent

As you build more Copilot extensions, you’ll notice that the final output is a set of a few files bundled into a zip archive—what we call an app package—that you’ll install and use. So it's important you have a basic understanding of what the app package consists of. The app package of a declarative agent is similar to a Teams app if you have built one before with additonal elements. See the table to see all the core elements. You will also see that the app deployment process is very similar to deploying a teams app.

| File Type                          | Description                                                                                                                                                     | Required |
|-----------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------|----------|
| Microsoft 365 App Manifest        | A JSON file (`manifest.json`) that defines the standard Teams app manifest.                                                                                     | Yes      |
| Declarative Agent Manifest        | A JSON file containing the agent's name, instructions, capabilities, conversation starters, and actions (if applicable).                                        | Yes      |
| Plugin Manifest (Optional)        | A JSON file used to configure your action as an API plugin. Includes authentication, required fields, adaptive card responses, etc. Only needed if actions exist. | No       |
| OpenAPI Spec (Optional)           | A JSON or YAML file defining your API. Required only if your agent includes actions.                                                                            | No       |

## Capabilities of a Declarative Agent
You can enhance the agent's focus on context and data by not only adding instructions but also specifying the knowledge base it should access. They are called capabilities and there are five types of capabilities supported.

- **Copilot Connectors** - let you centralize content on Microsoft 365. By importing external content to Microsoft 365, you not only make it easier to find relevant information, but you also let others in your organization discover new content.
- **OneDrive and SharePoint** - let you provide URLs of files and sites to agent, for it to gain access to those contents.
- **Web search** - let you enables or disables web content as part of the agent's knowledge base. You can also pass around 4 websites as source. 
- **Code interpreter** - enables you to build an agent with capabilities to better solve math problems and, when needed, leverage Python code for complex data analysis or chart generation.
- **GraphicArt** - enables you to build an agent support image or video generation by using DALL-e capability.
- **Email knowledge** - enables you to build an agent to acces a personal or shared mailbox, and optionally, a specific mailbox folder as knowledge.
- **People knowledge** - enables you to build an agent to answer questions about individuals in an organization.
- **Dataverse knowledge** - enables you to add a Dataverse instance as a knowledge source and to add synonyms and a glossary to help the system better interpret customized data in your tables.

☑️  Well done, you've built a solid foundation in the theory of declarative agents. Select **Next >** to go to the next chapter.