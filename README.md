# **AgentZ — First-Time Setup Guide**

## **1. Create a Workspace**

Go to:

**Workspaces → Create workspace**

Give the workspace a meaningful name and open it.

Think of the workspace as the boundary for your AgentZ project. The configuration required by your agents—such as sandboxes, MCP connections, secrets and skills—is managed from here.

```text
Login
  ↓
Create Workspace
  ↓
Open Workspace
```

---

## **2. Create a Sandbox**

Go to:

**Workspace settings → Sandboxes → New sandbox**

Enter the sandbox name.

Sandbox names must use:

```text
lowercase letters
numbers
hyphens (-)

Maximum: 32 characters
```

Example:

```text
security-agent-lab
```

### **Why is a Sandbox required?**

The Sandbox defines the environment and capabilities that an Agent can use.

During its configuration you can control things such as:

```text
Packages
MCP tools
Inference models
Allowed network hosts
```

An Agent is later created using one of these configured Sandboxes.

---

## **3. Configure Sandbox Packages**

Inside the Sandbox, open the **Packages** section.

Select the packages required by your agent.

AgentZ may mark some packages as **Required**. Keep required packages selected.

Additional packages should be selected only when the Agent needs them.

For example:

```text
curl       → HTTP/API operations
jq         → JSON processing
yq         → YAML processing
grep/sed   → text processing
```

### **Important**

Do **not** install packages just because another Agent uses them.

Configure the Sandbox according to what the new Agent actually needs.

---

## **4. Configure MCP Connections**

If your Agent needs tools provided by another system, configure an MCP connection.

Go to:

**Workspace settings → MCP connections → Add MCP connection**

Provide:

```text
Name
Endpoint
Authentication
Additional configuration/headers, if required
```

The exact authentication method depends on the MCP server. For example, a server may require:

```text
Bearer token

or

OAuth

or

other connection-specific configuration
```

Save the connection.

### **Validate it**

Check:

```text
Status: Ready
```

and verify that AgentZ discovers the expected tools.

```text
MCP Server
    ↓
Authentication
    ↓
Connection Ready
    ↓
Tools Discovered
```

If the connection shows **Error**, resolve the connection/authentication issue before using it with an Agent.

---

## **5. Choose Which MCP Tools the Sandbox Can Expose**

Return to:

**Workspace settings → Sandboxes → your sandbox**

The configured MCP connections can appear in the Sandbox configuration.

For each connection, select the tools that this Sandbox should be allowed to expose to Agents.

For example:

```text
MCP Connection
      │
      ├── Tool A  ✓
      ├── Tool B  ✓
      ├── Tool C
      └── Tool D
```

Select only the tools required for the Agent.

### **Why?**

There are two separate steps:

```text
Workspace MCP Connection
        ↓
Makes the MCP available to the workspace

Sandbox MCP Tool Selection
        ↓
Controls which MCP tools this Sandbox
may expose to Agents
```

So creating an MCP connection alone does not mean every tool needs to be available to every Sandbox.

---

## **6. Configure Secrets When Required**

If the Agent or Sandbox needs credentials for an external host, go to:

**Workspace settings → Secrets → New secret**

Provide:

```text
Secret key/name
Host or host pattern
Secret value
```

Example structure:

```text
Key:
SERVICE_API_TOKEN

Host:
api.example.com

Value:
<secret-value>
```

Save it.

The secret value becomes **write-only after it is saved**.

### **Why use Secrets?**

Secrets keep credentials outside Agent instructions and user prompts.

Use:

```text
Agent/Sandbox
      ↓
Secret resolved at runtime
      ↓
External service
```

Instead of:

```text
Agent prompt
      ↓
Hard-coded password/API token   ✗
```

Use Secrets only when the integration actually requires them.

---

## **7. Configure Allowed Hosts**

In the Sandbox, configure **Allowed hosts** when the Agent needs direct network access to an external host.

Enter an exact domain, IP address, or supported host/CIDR pattern.

Example:

```text
api.example.com
```

### **Why?**

This controls the network destinations that the Sandbox is allowed to access directly.

A useful distinction is:

```text
MCP connection
→ gives the Agent tools

Secret
→ provides a credential when required

Allowed host
→ permits direct network access

Package
→ provides runtime functionality
```

They solve different problems and are not interchangeable.

---

## **8. Configure Inference**

In the Sandbox, open **Inference**.

Select the models that should be available to this Sandbox.

Then configure the appropriate model roles exposed by the environment, such as:

```text
Default model
→ Used for new sessions and Workflow runs

Capable default model
→ Optional model for tasks such as images/scanned pages

Lower-cost model
→ Optional model for lightweight/background tasks
```

The exact models available depend on the inference providers configured for the workspace/organization.

### **For a basic setup**

Make sure the Sandbox has an appropriate **default model** available.

You don’t need to enable every available model.

---

## **9. Update the Sandbox**

Before creating the Agent, review the Sandbox configuration:

```text
Sandbox
   │
   ├── Packages
   ├── MCP connections/tools
   ├── Inference
   └── Allowed hosts
```

Then click:

**Update sandbox**

At this point, the execution environment for the Agent is prepared.

---

# **10. Create the Agent**

Now go to:

**General → Agents → New agent**

Provide:

```text
Agent name
Sandbox
```

Select the Sandbox you configured in the previous steps.

The relationship is:

```text
Agent
  │
  └── Sandbox
       │
       ├── Packages
       ├── MCP tools
       ├── Models
       └── Network permissions
```

If the UI provides:

**Allow this Agent to save facts and journal entries across sessions**

enable it only when the Agent needs that persistence across sessions.

Create the Agent.

---

# **11. Start a Session With the Agent**

Open the Agent and start a new session.

Start with a **simple request** that validates one capability.

If you configured an MCP:

```text
Ask the Agent to perform a simple operation
using one of the MCP capabilities.
```

If you configured direct API access:

```text
Ask it to perform a small/read-only
operation against that service.
```

Verify that the expected tool actually executes and returns a result.

---

# **12. Connect External Applications When Required**

Some MCP integrations can provide access to external applications/services.

If the selected integration requires authorization, complete its supported authentication flow.

For an OAuth-based service, the general flow is:

```text
Agent / MCP
     ↓
Request application connection
     ↓
OAuth authorization
     ↓
User grants permission
     ↓
Connection established
     ↓
Agent can use permitted application tools
```

Do not store an application’s username/password in Agent instructions simply to bypass its supported OAuth/integration mechanism.

---

# **13. Test Each Capability Separately**

Before asking the Agent to perform a large task, validate each dependency independently.

Use this order:

```text
Agent session works
        ↓
Model works
        ↓
Required MCP tool works
        ↓
Required secret/authentication works
        ↓
Required network access works
        ↓
External application works
        ↓
Combined task works
```

This is important for troubleshooting.

If the complete task fails, you’ll know which layer was already validated.

---

# **14. Add Skills When Needed**

Go to:

**Workspace settings → Skills**

Use Skills when you need reusable instructions/capabilities rather than repeatedly providing the same operational guidance in every session.

Skills are **not a prerequisite for creating your first Agent**.

Start with a working Agent first; add Skills when the behavior needs to become reusable.

---

# **15. Build a Workflow When the Process Is Repeatable**

Once the Agent and its required capabilities work correctly, create a **Workflow** for a repeatable process.

Conceptually:

```text
Input
  ↓
Agent / processing
  ↓
Tool or MCP operation
  ↓
Additional processing
  ↓
Action/output
```

A Workflow is useful when you want a defined process to run consistently rather than manually instructing the Agent every time.

---

# **16. Add a Trigger Only If Automation Is Required**

If a Workflow needs to start automatically, configure a **Trigger** appropriate to the workflow.

So the progression should generally be:

```text
Agent works manually
       ↓
Workflow works
       ↓
Trigger automates it
```

Don’t make Triggers part of the minimum first-time Agent setup unless automation is actually required.

---

# **Complete Setup Flow**

```text
                 AGENTZ
                    │
                    ▼
            CREATE WORKSPACE
                    │
                    ▼
           WORKSPACE SETTINGS
                    │
          ┌─────────┴─────────┐
          │                   │
          ▼                   ▼
     CREATE SANDBOX      MCP CONNECTIONS
          │                   │
          │              Add connection
          │                   │
          │              Configure auth
          │                   │
          │              Verify READY
          │                   │
          │              Verify tools
          │                   │
          └─────────┬─────────┘
                    │
                    ▼
             CONFIGURE SANDBOX
                    │
          ┌─────────┼──────────┐
          ▼         ▼          ▼
       Packages   MCP Tools  Inference
                              Models
          │         │          │
          └─────────┼──────────┘
                    │
              Allowed Hosts
                    │
                    ▼
              UPDATE SANDBOX
                    │
                    ▼
          ADD SECRETS IF NEEDED
                    │
                    ▼
          GENERAL → AGENTS
                    │
                    ▼
              CREATE AGENT
                    │
              Select Sandbox
                    │
                    ▼
              OPEN AGENT
                    │
                    ▼
             START SESSION
                    │
                    ▼
        TEST ONE CAPABILITY
                    │
                    ▼
       TEST ALL DEPENDENCIES
                    │
                    ▼
        TEST END-TO-END TASK
                    │
                    ▼
              AGENT READY
                    │
          ┌─────────┴─────────┐
          ▼                   ▼
     Add Skills          Add Workflow
     if needed            if needed
                              │
                              ▼
                         Add Trigger
                         if automation
                         is required
```

