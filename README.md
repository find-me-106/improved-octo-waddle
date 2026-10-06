# Ben's Lab: AI Business Advisor

A small-business web app with a dashboard and an AI advisor. People sign up, land in the advisor, get greeted by name, and answer a few short questions to receive practical business advice. They can also ask their own business or IT questions.

Built with plain **HTML, CSS and JavaScript**, **Firebase Authentication** for login, and the **Groq API** for the AI. No build step and no backend.

## Features

- **Dashboard pages:** Overview, Insights, Strategy and Goals, all with light and dark themes.
- **Sign up and log in:** email and password through Firebase. The name entered at signup is used by the AI.
- **Two AI modes:**
  - **Diagnostic:** the AI asks one question at a time, then gives findings, ranked opportunities, confidence and next actions.
  - **Ask a question:** free-form business and IT questions with practical advice.
- **Personal greeting:** "Hello, *name*!" every time the advisor opens. The profile is created automatically from the signup name.
- **Profile and settings:** name, business, AI tone, answer length, voice language and theme.
- **Streaming replies:** answers appear word by word.
- **Pause button:** stops a reply mid-way and keeps what was already written.
- **Voice to text:** speak your answer with the microphone button.
- **Light and dark theme** in the advisor.
- **Chat history** per user, with a delete button on each chat.
- **Automatic model fallback:** if the configured model isn't available to your key, the app picks a working one.
- **Image icons:** all icons are SVG files in `icons/`.

## Project structure

```
.
├── index.html          Dashboard (home)
├── insights.html       Insights page
├── strategy.html       Strategy page
├── goals.html          Goals page
├── login.html          Log in
├── signup.html         Create account
├── ai.html             AI advisor (requires login)
├── app.js              Advisor logic: chat, streaming, voice, profile, history
├── auth.js             Firebase auth helper functions
├── firebase-config.js  Firebase project settings
├── config.js           Groq API settings (base URL, key, model)
├── style.css           Styles for ai.html
├── icons.css           Icon sizing for the dashboard pages
└── icons/              SVG icon files
```

## Requirements

- A modern browser. Chrome or Edge is needed for voice input.
- A free [Firebase](https://console.firebase.google.com) project.
- A [Groq](https://console.groq.com/keys) API key.
- Python or Node, only to serve the files locally.

## Setup

### 1. Firebase

1. Create a project in the Firebase console and add a **Web app**.
2. Open **Authentication → Sign-in method** and enable **Email/Password**.
3. Open **Authentication → Settings → Authorized domains** and make sure `localhost` is listed.
4. Copy your web app settings into `firebase-config.js`.

### 2. Groq

1. Create an API key at [console.groq.com/keys](https://console.groq.com/keys).
2. Open `config.js` and fill in:

```js
const API_BASE = "https://api.groq.com/openai/v1";
const API_KEY = "YOUR_GROQ_API_KEY";
const MODEL = "openai/gpt-oss-120b";
```

`MODEL` must be a model your key can use. To list them, open `https://api.groq.com/openai/v1/models` with your key, or check the Groq docs. If the model isn't available, the app switches to another one automatically and shows which.

### 3. Run it

The pages use ES modules, so they must be served over HTTP. Opening the files by double-click will not work.

```bash
python -m http.server 8000
# or
npx serve
```

Open `http://localhost:8000`.

## How it works

```
index.html (dashboard)
   └─ "AI" links ─▶ signup.html ─▶ ai.html
                    login.html  ─▶ ai.html
```

1. A visitor signs up with a name, email and password. Firebase stores the name as the account's display name.
2. `ai.html` checks who is logged in. If nobody is, it redirects to `login.html`.
3. On the first visit, a profile is created from the signup name. Profiles and chats are saved in the browser, separately for each user.
4. The advisor greets the user by name and starts the chosen mode. The name, business, tone and answer length are sent to the AI in its instructions.

## Using the advisor

| Action | How |
| --- | --- |
| Switch mode | **Diagnostic** or **Ask a question** in the sidebar |
| Start a new chat | **New chat** |
| Stop a reply | **Pause** (type "continue" to resume the answer) |
| Speak instead of typing | Microphone button, then allow microphone access |
| Change theme | Sun / moon button in the top bar |
| Edit profile | Your name in the top bar |
| Delete a chat | Bin icon next to a chat in the history |
| Log out | Profile dialog → **Log out** |

## Security notes

- **Do not publish this version with a real Groq key.** `config.js` runs in the browser, so anyone who opens the site can read the key. It is fine on your own computer.
- To put the site online, move the AI calls to a small backend that holds the key, and have the browser call your backend instead of Groq.
- The Firebase web settings in `firebase-config.js` are meant to be public. Protect your project with Firebase Authentication rules and authorized domains.
- If a key has been shared or committed anywhere, delete it in the Groq console and create a new one.
- Model replies are shown as plain text, never as HTML.

## Troubleshooting

| Problem | Fix |
| --- | --- |
| Blank page or module / CORS errors | Serve the folder over `http://localhost`, not `file://`. |
| `model_not_found` error | Set `MODEL` to a model from your own model list, or let the automatic fallback pick one. |
| `401` or "invalid API key" | Check `API_KEY` in `config.js`: no quotes inside, no spaces, and not revoked. |
| Replies are slow | Reasoning models think before answering. Choose a smaller, non-reasoning model. |
| Redirected to the login page | You are not signed in. Log in or sign up first. |
| `auth/operation-not-allowed` | Enable Email/Password in Firebase Authentication. |
| `auth/unauthorized-domain` | Add your domain (for example `localhost`) to Firebase Authorized domains. |
| Microphone does nothing | Use Chrome or Edge, allow microphone access, and use `localhost` or HTTPS. Firefox does not support speech recognition. |
| Greeting shows an email name instead of the signup name | The account has no display name. Edit the name in the profile dialog. |

## Known limits

- Chat history and profiles are stored in the browser only. Clearing site data or switching device removes them.
- Pause stops generation but cannot resume it exactly. Ask the AI to continue instead.
- Voice input depends on the browser's built-in speech recognition.
- AI advice is general guidance based on what the user says. It is not professional legal, financial or tax advice.

## Ideas for next steps

- A small backend to hide the API key and sync chats between devices.
- Export a finished diagnostic as a PDF or email.
- Show the diagnostic results as a summary card on the dashboard.

---

Built for Ben's Lab.
