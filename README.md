# agentz-setup

For someone opening **AgentZ for the first time**, use this exact setup order:

**1. Login to AgentZ → Workspaces → Create Workspace**  
Create a workspace for the use case. Everything else will be configured under this workspace.

**2. Go to Sandboxes → Create Sandbox**  
Create/select the execution sandbox. This is where the agent gets its runtime to execute commands and make API calls.

**3. Configure Sandbox packages**  
Add only what the use case needs. For our API flow:

```
curl
jq
mcporter
```

`curl` = API calls, `jq` = JSON processing, `mcporter` = MCP tooling.

**4. Configure Network/Allowed Host**  
Add the backend that the sandbox needs to access.

For our AccuKnox Demo setup:

```
cspm.demo.accuknox.com
```

Use the **API/backend host**, not the UI URL.

**5. Go to Secrets → Add Secret**  
Create the credential required by the API.

```
Type: Static Secret
Key: ACCUKNOX_TOKEN
Value: <your token>
Host: cspm.demo.accuknox.com
```

For India we used a separate variable:

```
ACCUKNOX_TOKEN_IN
```

**Why:** the token stays securely injected into the runtime instead of being written inside the agent prompt.

**6. Test Sandbox + Secret first**  
From the sandbox, make one simple authenticated AccuKnox request.

Expected:

```
HTTP 200
```

Do **not** create/debug the full agent until this works.

**7. Test the actual API/data source**  
For our use case, test:

```
GET https://cspm.demo.accuknox.com/api/v1/findings
```

Confirm actual findings are returned. This proves:

```
Sandbox → Network → Secret → AccuKnox API → Data ✓
```

**8. Go to MCP Connections → Add MCP**  
Now configure the external tools the agent needs.

For our email flow, create the **Composio MCP** connection.

```
MCP: Composio
Status: Ready
```

Don't continue until the MCP shows **Ready**.

**9. Connect Gmail in Composio**  
Authorize the Gmail account through Composio/OAuth.

No Gmail password needs to be stored in AgentZ Secrets.

After authorization:

```
AgentZ → Composio MCP → Gmail ✓
```

**10. Test Gmail separately**  
Use the Composio Gmail tool to send one test email.

Confirm the action returns a successful response/message ID.

Now both sides work independently:

```
AccuKnox API ✓
Gmail ✓
```

**11. Go to Agents → Create Agent**  
Now create the agent and select the workspace/sandbox you configured.

For our setup:

```
Workspace
   ↓
Sandbox
   ├── AccuKnox Secret
   └── API access

Agent
   ├── Sandbox
   └── Composio MCP
          └── Gmail
```

**12. Add Agent Instructions**  
Tell the agent **what it should do**, not credentials.

Example:

```
Fetch active AccuKnox findings.
Analyze and summarize the findings.
Prepare a security report.
Send the report using the configured Gmail tool.
```

**13. Attach/enable required tools**  
Make sure the agent can access:

```
Sandbox
Composio MCP
Required MCP tools
```

The secret remains attached/injected through the sandbox configuration.

**14. Open the Agent → Chat/Run**  
This is where the user actually talks to the configured agent. The Workspace screen in Vanisha's screenshot is the starting/configuration area; she needs to create the resources and then open the configured agent to interact with it. Untitled document (1)

Give a simple test first:

```
Fetch 5 active findings and summarize them.
```

If successful, test email:

```
Fetch 5 active findings, summarize them,
and email the summary using Gmail.
```

**15. Only after this works → create Skill/Workflow**  
Once manual Agent Chat works end-to-end, convert the repeated task into a reusable Skill/Workflow.

### Complete setup flow

```
Login
  ↓
Create Workspace
  ↓
Create Sandbox
  ↓
Add runtime packages
  ↓
Allow API/backend host
  ↓
Add Secret
  ↓
Test authentication
  ↓
Test AccuKnox API/data
  ↓
Add MCP
  ↓
Connect Composio
  ↓
Authorize Gmail
  ↓
Test Gmail
  ↓
Create Agent
  ↓
Select Sandbox
  ↓
Enable MCP/tools
  ↓
Add Agent Instructions
  ↓
Open Agent Chat
  ↓
Test simple task
  ↓
Test complete task
  ↓
Create reusable Skill/Workflow
```

**Rule for a first-time setup:** don't configure everything and test at the end. Validate it layer-by-layer: **Sandbox → Secret → API → MCP → Gmail → Agent → full workflow.** This makes it immediately clear where a failure is occurring.
