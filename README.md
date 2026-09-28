# Torn Most Wanted - Clickable Profile Links

A lightweight Tampermonkey userscript for [Torn.com](https://www.torn.com/) that automatically turns player names and IDs found in Most Wanted messages into clickable links to their Torn profiles.

For example:

```text
RGiskard [1953860]
```

becomes a clickable link to:

```text
https://www.torn.com/profiles.php?XID=1953860
```

The script works with Torn's dynamically loaded mailbox/message interface and automatically processes newly loaded messages.

## Features

- Automatically detects Torn player references in message bodies.
- Converts `PlayerName [1234567]` into a clickable profile link.
- Opens profiles in a new browser tab.
- Works with messages loaded dynamically through Torn's AJAX/navigation system.
- Uses a `MutationObserver` to detect newly inserted message content.
- Avoids processing the same DOM nodes repeatedly.
- Does not modify links that already exist.
- Requires no external libraries.
- Requires no Torn API key or API access.

## Example

Before:

```text
DrGonzo [59922] : $4,532,396 offered for their arrest
```

After, player references are clickable and open their Torn profiles in new tabs.

## Installation

### Requirements

You need a userscript manager capable of running Tampermonkey/Greasemonkey-style userscripts.

[![Install with Tampermonkey](https://img.shields.io/badge/Install%20with-Tampermonkey-00485B?logo=tampermonkey&logoColor=white&style=for-the-badge)](https://github.com/YOUR_USERNAME/torn-most-wanted-profile-links/raw/refs/heads/main/torn-most-wanted-profile-links.user.js)

Recommended:

- [Tampermonkey](https://www.tampermonkey.net/)

### Install manually

1. Install Tampermonkey.
2. Open the Tampermonkey dashboard.
3. Create a new userscript.
4. Replace the default contents with `torn-most-wanted-profile-links.user.js`.
5. Save the script.
6. Visit Torn.com.
7. Open your Most Wanted messages.

Player references matching the expected format should now be clickable.

### Install from GitHub

Once this repository is published, the userscript can also be installed directly from the raw `.user.js` file. Tampermonkey should detect the userscript metadata and offer to install it.

## Supported player format

The script recognises player references in the following format:

```text
PlayerName [1234567]
```

Player names may contain:

- Letters
- Numbers
- Underscores (`_`)
- Hyphens (`-`)

Examples:

```text
RGiskard [1953860]
Turt [2472641]
Some_Player [123456]
Player-Name [9876543]
```

## How it works

The script searches Torn message body elements for text matching:

```regex
/([A-Za-z0-9_-]+)\s*\[(\d+)\]/g
```

When a match is found, it is replaced with an HTML `<a>` element pointing to the corresponding Torn profile:

```text
https://www.torn.com/profiles.php?XID=PLAYER_ID
```

Generated links:

- Open in a new tab.
- Use `noopener noreferrer`.
- Are styled to stand out from surrounding message text.
- Include a tooltip containing the player's name and ID.

## Dynamic page support

Torn uses dynamic/AJAX navigation in parts of its interface. Running the script only once when the page loads would therefore miss messages inserted later.

The script uses a `MutationObserver` to monitor the page for newly inserted message content.

This allows it to process messages when:

- The mailbox initially loads.
- A different message is opened.
- Torn replaces message content dynamically.
- Additional message content is inserted without a complete page reload.

## Where the script looks

The primary target is Torn's message content container:

```css
.unreset.editor-content
```

If those elements aren't found, the script falls back to:

```css
#mailbox-main .cont-gray
```

The fallback is intended to provide some resilience if Torn changes its mailbox markup.

## Privacy

This userscript does not:

- Send data to an external server.
- Collect player information.
- Use the Torn API.
- Require a Torn API key.
- Make external network requests.
- Store personal data.

All processing takes place locally in your browser.

## Permissions

The script uses:

```text
@grant none
```

It therefore does not request privileged Tampermonkey APIs.

## Compatibility

The script is intended for:

- Torn.com
- Modern desktop browsers
- Tampermonkey-compatible userscript managers

The userscript currently matches:

```text
https://www.torn.com/*
```

## Limitations

The current pattern intentionally supports a conservative set of player-name characters.

It recognises:

```text
Player_Name [123456]
Player-Name [123456]
Player123 [123456]
```

but names containing other characters may not be detected.

The script also relies on Torn's current mailbox HTML structure. If Torn substantially changes its mailbox markup, the selectors may need to be updated.

## Troubleshooting

### Player names aren't becoming links

Check that:

1. Tampermonkey is installed and enabled.
2. The userscript is enabled.
3. You are visiting `https://www.torn.com/`.
4. The player reference follows the expected format:

   ```text
   PlayerName [1234567]
   ```

5. Refresh the Torn page after installing or updating the script.

### The script worked previously but stopped working

Torn may have changed its mailbox HTML structure.

The script currently targets:

```css
.unreset.editor-content
```

with a fallback to:

```css
#mailbox-main .cont-gray
```

If Torn changes these selectors, an update to the script may be required.

### Links open in a new tab

This is intentional. Generated links use:

```html
target="_blank"
```

so opening a player's profile does not replace the current Torn message.

## Development

The entire script is contained in a single JavaScript userscript file.

No build system, package manager, or external dependencies are required.

### Player detection

```javascript
const PLAYER_PATTERN = /([A-Za-z0-9_-]+)\s*\[(\d+)\]/g;
```

### Profile URL generation

```javascript
link.href = `https://www.torn.com/profiles.php?XID=${id}`;
```

### Dynamic content detection

```javascript
const observer = new MutationObserver(...)
```

This keeps the script functional when Torn dynamically replaces mailbox content.

## Contributing

Bug reports and improvements are welcome.

When reporting a problem, please include:

- Browser and version
- Userscript manager and version
- Torn page where the problem occurs
- Example player-name format that isn't being detected
- Any relevant browser console errors

Avoid posting private Torn messages or other sensitive account information in public issues.

## Disclaimer

This is an unofficial community userscript.

It is not affiliated with, endorsed by, or sponsored by Torn or Torn Ltd.

Torn and related trademarks belong to their respective owners.

## License

This project is released under the MIT License.

See [`LICENSE`](LICENSE) for the full license text.
