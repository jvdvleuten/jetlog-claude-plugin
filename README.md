# Jetlog for Claude

Jetlog is a pilot logbook app (https://jetlog.app). This plugin lets Claude work with your Jetlog logbook: ask for your totals and recent experience, find a flight, look up crew, aircraft and airports, add a flight or simulator session, or correct an entry. It adds one skill that teaches Claude how a pilot logbook works (roles, times, simulator sessions, signatures) and how to use the Jetlog tools carefully.

## What it connects to

The plugin connects to one server, `https://jetlog.app/mcp`, which is Jetlog's own MCP server. Nothing else is contacted. The plugin contains no scripts, hooks or binaries, only the connection (`.mcp.json`) and the skill (`skills/jetlog-logbook/`).

## Signing in

The first time Claude uses Jetlog, a Jetlog page opens in your browser. Sign in with the Jetlog app or with a one-time code sent to your email address, then pick what Claude may do. The plugin holds no password, key or token, and Claude never sees your password.

## What data goes where

When you ask a question, Claude calls Jetlog tools and the logbook data it requests (entries, people, aircraft, places, totals, attachment names and short-lived download links) is sent to Claude, which is part of your conversation. Jetlog sends only what a tool call asks for. Jetlog's privacy policy is at https://jetlog.app/privacy.

## How changes are confirmed

Claude never changes your logbook on its own. It first proposes a change, which writes nothing, and shows you the before and after of every field in the chat. It applies the change only after you say yes to that exact proposal. A proposal expires after 24 hours. After a change is written, the Jetlog app sends you a notification, and under Settings > Connected Apps & CLI > Logbook Changes you can see what changed and undo it. If you untick "Propose changes" when you connect, Claude can only read.

## Switching access off

Open Settings > Connected Apps & CLI in the Jetlog app and choose Revoke on the Claude connection. It stops working immediately. Changes it already made stay listed under Logbook Changes, where you can undo them.

## Support

Mail support@jetlog.app. More about assistants and the Jetlog command line: https://jetlog.app/docs/ai-assistants-and-the-cli.

## License

MIT, see LICENSE.
