---
name: flownato-ui-research
description: Look up how real mobile apps handle a screen or flow before designing, reviewing or building one. Use when the user is working on a mobile app's onboarding, sign-in or OTP entry, permission prompts, search, product listing, cart and checkout, address or delivery-slot selection, booking, payment, subscription, or empty and error states, or on a component such as a bottom sheet, dialog, tab bar or carousel, and real examples would help, even if they don't mention Flownato. Also use it when the user asks how a named app (for example Swiggy, Zepto, BookMyShow, redBus or Nykaa) does something, or wants the same flow compared across two or three apps. Uses the Flownato MCP server included in this plugin.
---

# Flownato UI research

Flownato records real mobile apps screen by screen and publishes reviewed journeys, screens and a UI pattern
vocabulary: screen patterns, UI elements, flow patterns and flow actions. The Flownato MCP tools search that
library and return exact journey and step identifiers, so every claim about another app can point to the screen
it came from.

Use it to replace "apps usually do X" with what specific apps actually show. Remembered app interfaces are often
out of date or invented; recorded screens are not.

## Setup

This plugin adds the remote server `https://agent.flownato.com/mcp`. On first use the client opens Flownato
sign-in in the browser and asks for read-only access; a free account works. If the Flownato tools are not
available in the session, say so and give the connect command instead of answering from memory:

```bash
claude mcp add --transport http flownato https://agent.flownato.com/mcp
```

## Pick the tool for the question

| The user wants | Start with |
|---|---|
| Examples of a product moment ("the delivery slot step in checkout") | `search_ui_library` |
| A specific screen state, component or piece of copy ("OTP entry with a resend timer") | `search_ui_screens` |
| How common a pattern is within a category or an app | `analyze_ui_pattern` |
| Ideas when the exact pattern isn't known yet | `explore_ui_patterns` |
| The same moment in two or three named apps | `compare_ui_flows` |
| Everything one app covers | `inspect_app_library` |
| Whether something exists at all in a scope | `check_ui_presence` |
| Which apps and categories are covered | `inspect_library_coverage` |
| A follow-up on results you already have | `inspect_ui_resources` with their `resourceRef` objects |
| To see a screen | `get_screen_image` with an `assetId` from another result |

When you don't know whether an app or category is covered, call `inspect_library_coverage` before guessing names.

## Working method

1. **Frame the moment.** Name the step ("choosing a delivery slot after adding items"), the kind of app and any
   apps the user mentioned. A narrow query returns better evidence than "good checkout UX".
2. **Look before you describe.** Open the relevant screens with `get_screen_image` before describing layout,
   copy or visual details. Text results describe structure; images show what a person actually sees.
3. **Cite.** For each claim, name the app and the step, and keep the journey and step identifiers or Flownato
   links the tools return, so the user can check them.
4. **Report counts as observations.** A sentence like "most grocery checkouts in the results use a bottom sheet
   for slot choice" describes the recorded apps. It is not proof of best practice; say so when it matters.
5. **Back absence with an exhaustive check.** Say "none of these apps do X" only after `check_ui_presence`
   returns an exhaustive result for that scope. Otherwise say you didn't find it.
6. **Synthesize instead of copying.** Finish with what the examples share, where they differ and why that might
   be (audience, regulation, business model), and a recommendation for the user's product. Don't reproduce one
   app's screen wholesale.

## Locked results

Free accounts see the complete journeys that flownato.com marks free and the first 3 screens of every other
journey; later steps come back with `locked: true` and no image. Say which steps were locked and work from what is
visible. Never fill locked steps in from memory. Mention upgrading only if the user asks how to see more.

## Limits

- Coverage is the set of apps Flownato has recorded. If an app or moment isn't there, say so and offer the closest
  covered example, clearly labelled as a substitute.
- The tools are read-only and answer UI and product-design research questions only.
- Screen image URLs are signed and expire. Refer to screens by app, journey and step, not by image URL.

## Example

The user says: "We're adding address entry to our grocery app's checkout. How do other apps handle it?"

1. Call `search_ui_library` with "add or select a delivery address during checkout".
2. Open the address steps of the strongest two or three journeys with `get_screen_image`.
3. Answer with each app's steps and citations, note whether each starts from a map pin or a form, then recommend an
   approach for the user's checkout and what to test with real users.
