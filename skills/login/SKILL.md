---
name: login
description: "Sign in to Phinite (OAuth, the same login as the Phinite app) or switch workspace"
argument-hint: "[workspace name]"
triggers:
  - user
---
Help the user sign in to Phinite through the `phinite` MCP server. Requested workspace: $ARGUMENTS

1. Call `whoami`. If it works, report the user, org, workspace and hours left in one or two lines. If the user asked for
   another workspace, go to step 3.
2. If it fails because the server isn't authenticated, tell the user in three short lines:
   - In the Devin app: **Customize → MCPs**, find **phinite** and click **Connect**. In the Devin CLI: run
     `devin mcp login phinite`.
   - A browser opens on the Phinite login (Google, GitHub or email, as in the app). Pick the org and the workspace to
     build in, then return here.
   - The assistant never sees a password or token. The sign-in ends when its Phinite session expires, or right away
     when they sign out of all sessions in Phinite.
3. To switch workspace, `list_workspaces` shows the choices. A connection works in the workspace picked at sign-in,
   so the user signs in again (Customize → MCPs → **phinite** → disconnect and Connect, or `devin mcp logout phinite` then `devin mcp login phinite`) and picks the other workspace.
4. Call `whoami` again and confirm.
