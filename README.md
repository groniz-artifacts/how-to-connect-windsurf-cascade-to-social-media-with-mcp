# How to Connect Windsurf Cascade to Social Media With MCP

Windsurf Cascade can connect to Groniz through a remote MCP server. Check which agent is active before editing your configuration: this guide applies to legacy Cascade. Current documentation distinguishes Cascade from the newer default, Devin Local, which uses different configuration. If your active agent is Devin Local, you will need a different setup; this guide covers legacy Cascade only. [Cascade MCP documentation](https://docs.devin.ai/desktop/cascade/mcp)

Start with an account list and the requirements for one destination. Use those results to prepare a post for review. Groniz handles the social connection, platform formatting, and delivery; you remain responsible for the words, media rights, destination, and schedule.

The steps follow the documented configuration and workflow. We have not tested a complete delivery inside Cascade. The broader [AI agent publishing setup guide](https://groniz.com/blog/ai-agent-social-media-publishing-setup-a-client-by-client-guide) explains where the client fits.

## Check the agent before changing configuration

The editor name alone does not tell you which agent will call the tool. Write down the active agent, its configuration location, and the environment in which it runs. You will need these details if the connection fails; an enabled toggle alone does not establish which configuration the agent loaded.

| Check | Evidence to record | Continue when |
| --- | --- | --- |
| Active agent | Agent name shown in your current session | It is legacy Cascade |
| Configuration | File you actually edited | It belongs to that Cascade setup |
| Credential source | Environment variable name only | The running client can resolve it |
| MCP availability | Groniz appears in the client's tool configuration | The relevant tools are enabled and permitted |
| Destination | Account label and integration ID returned by discovery | They identify the intended social account |

Use a real discovery result for the last row. A familiar account name copied from an old task is not enough, especially when personal and company accounts have similar names.

## Add the Groniz server

Cascade uses `~/.codeium/windsurf/mcp_config.json`. Merge this entry into the existing `mcpServers` object, preserving other servers:

```json
{
  "mcpServers": {
    "groniz": {
      "serverUrl": "https://mcp.groniz.com/mcp",
      "headers": {
        "Authorization": "Bearer ${env:GRONIZ_API_KEY}"
      }
    }
  }
}
```

Cascade supports environment interpolation; an unset variable resolves to an empty string. Its remote MCP support includes HTTP and OAuth, while tool toggles and administrator policy can affect availability. Consult the client documentation for the controls in your installation. [Cascade MCP documentation](https://docs.devin.ai/desktop/cascade/mcp)

The example uses Groniz's MCP Bearer authentication. Supply the key through your approved local credential setup, and keep its value out of the repository, prompts, screenshots, and debugging notes. Record that the variable is available to the client without printing it.

If configuration parses but authentication fails, check the running client's environment. A variable available in a terminal does not by itself establish what another process received. Resolve authentication before preparing the post.

## Ask for discovery before a delivery

Ask Cascade to discover the account requirements:

List my connected Groniz integrations. Identify the account I select by integration ID, then retrieve its current settings schema. Report required fields, content limits, and media requirements. Stop after discovery.

Check the discovery response before continuing. You should be able to match the returned account to the intended destination and see what the provider requires before any post is prepared for submission.

The settings schema is the authority for required fields and provider-specific constraints. Capabilities differ between platforms, so a successful setup for one account is not a reusable payload for every account. The [social media MCP server guide](https://groniz.com/blog/social-media-mcp-servers-how-ai-agents-actually-publish-posts) covers this distinction in more detail.

When a tool is absent, check the client configuration, enabled tools, and applicable administrator policy. When the tool is present but rejects a call, preserve the error and investigate that call's authentication or inputs. The first case concerns tool availability; the second concerns a failed request.

## Review the exact post

Prepare one destination first. Ask Cascade to show the complete text, integration ID, provider settings, media references, and exact ISO timestamp with the intended timezone. Replace phrases such as "tomorrow morning" with a timestamp the reviewer can verify.

For media, Groniz requires an upload before creating the post. Use the returned `.path` as the media reference; a workstation file path is not the uploaded asset. Check that the preview and the uploaded reference describe the same approved file.

Approval should identify the specific payload and permitted action. "The announcement looks good" leaves the account and timing unresolved. A useful instruction is: "Schedule this exact text and listed media to this integration at this timestamp." The [human approval guide](https://groniz.com/blog/how-to-design-human-approval-for-ai-generated-social-posts) gives a fuller review pattern.

## Record the result

After the authorized submission, record the returned post ID alongside the reviewed payload. A queued post has been accepted for later delivery; it has not yet been shown to be published.

If the request times out or its result is unclear, inspect the remote queue or available post record before trying again. An uncertain response can occur after a write was accepted. Repeating the call without reconciliation can create a second scheduled post.

To begin, [connect your destination in Groniz](https://groniz.com/console/connectors), then return to Cascade for account discovery and one reviewed delivery.
