# Publishing and verifying Programscape MCP updates

The hosted server, public documentation, directory display and local router have separate release surfaces.
An update is complete only when each changed surface has been checked publicly.

## Hosted server and documentation

1. Deploy the server change at `https://programscape.com/api/mcp`.
2. Check `initialize` and `tools/list` against that public URL. Compare the returned tool names with the README and
   `https://programscape.com/claude`, including which tools need sign-in and which change saved data.
3. Run the signed-in save/read check with an account meant for verification: save a plan, start a fresh client
   session, sign in to the same account, and read back the saved values using its explicit `planId`. Verify that
   other plans remain unchanged, and remove the verification plan when finished.
4. Merge the matching public README and manifest change. Do not describe a proposed capability as deployed.

The hosted URL serves the deployed implementation to all clients. A client may cache tool definitions; refresh
its tool list after a schema change. `initialize.serverInfo.version`, the registry metadata version and the router
package version identify different things.

## Official MCP Registry

The server name is `com.programscape/programscape`, with a Streamable HTTP remote at the hosted URL above.
Publish new metadata under that same name; keep the remote URL and repository URL stable.

1. Increment `server.json`'s version. Published versions are immutable.
2. Validate the manifest with the official [MCP Registry publisher](https://github.com/modelcontextprotocol/registry):

   ```sh
   mcp-publisher validate
   ```

3. Authenticate for `programscape.com` using the domain ownership key already held by the maintainer. Keep private
   keys and publisher tokens outside git. The publisher supports DNS and HTTP domain authentication; follow its
   [current authentication instructions](https://github.com/modelcontextprotocol/registry/blob/main/docs/modelcontextprotocol-io/authentication.mdx).
4. From this repository, publish the validated manifest:

   ```sh
   mcp-publisher publish
   ```

5. Read the [public latest entry](https://registry.modelcontextprotocol.io/v0.1/servers/com.programscape%2Fprogramscape/versions/latest)
   without publisher credentials. Confirm the manifest version, `active` status, `isLatest: true`, repository and
   remote URL. A changed file in this repository alone does not publish the registry update.

## Claude connector directory

Deploying a tool change updates the actual connector. The names and description displayed in the directory are
listing metadata, edited separately in the [developer portal](https://claude.ai/directory/manage). Anthropic reviews
listing edits; pending edits do not establish that the public listing changed.

1. Refresh the saved Programscape connector's tools list in Claude before editing the listing.
2. Open the existing Programscape submission. Keep its URL, slug, documentation URL and privacy URL stable.
3. Update the displayed tool list to match the public `tools/list` result and the README. Include `report_balance`.
   Explain that portfolio deals require membership through the partner's invite link and administration requires
   that list's admin access.
4. Keep authentication **Required when the server asks**, with dynamic client registration. Catalog tools answer
   without sign-in; plan and partner tools request it when needed.
5. Submit the edit, then check both portal status and the public listing after approval. Record pending review or
   missing portal access as unfinished work.

Anthropic's [listing maintenance guide](https://claude.com/docs/connectors/building/managing-your-listing) and
[server update guide](https://claude.com/docs/connectors/building/after-publishing) describe the current process.

## Local router

The router is released from [its own repository](https://github.com/Deirfgeiz/programscape-router), as the npm package
`@programscape/router`. Follow that repository's release checks. After publishing, verify npm's public `latest` tag
and fetch the published package into a fresh directory; a GitHub commit or tag alone is not an npm release.
