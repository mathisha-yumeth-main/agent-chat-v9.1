# Agent Studio

A local, Gemini-style web app for chatting with AI models, running an agent that edits and runs things
on your computer, generating images, and writing Python in a Playground.

## Start

- **Windows:** double-click `start.bat`.
- **Linux / macOS:** run `./start.sh` (or `bash start.sh`).

The app opens at http://127.0.0.1:8787. Keep the console window open while you use it. To apply an update,
close the old console window first, then start again.

## Chat models

- **Ollama (local):** install Ollama and pull a model, for example `ollama pull llama3.2:3b`.
  Settings → Ollama URL should be `http://127.0.0.1:11434`. Answers stream in live, and Stop
  really stops generation.
- **Claude, ChatGPT, Gemini:** add the API key in Settings, then click **Refresh models**.

Model lists are discovered automatically. Pick a model from the button in the chat box.

## Image models (Images tab)

Only models that can generate images are listed. Providers: OpenAI, Gemini (Imagen), or any
OpenAI-compatible endpoint you enter in Settings. Pictures are saved in the `workspace/images` folder.

## Agent tools

read, write and edit files, run commands, glob/grep search, web fetch, task list, run Python,
SSH (when you turn it on), MCP servers, and generate images. Anything that changes things outside the workspace, runs
code, touches a remote host, or downloads from the web asks for your approval first.

## SSH (paste your terminal command + password)

Open **MCP & SSH** in the sidebar. In the **SSH connection** box:

1. Paste the same command you type in a terminal, for example `ssh user@192.168.1.10 -p 22`
   (options such as `-i key` work too; paste only the connection, not a command after it).
2. Type the password and click **Turn SSH on**. When ssh asks for the password, Agent Studio types it for you.
   The password is kept in the server's memory only (never written to a file); you enter it again after restarting the server.
3. While SSH is **on**, the agent gets the `ssh_run` tool and works on the server. Local commands (`run_command`)
   are switched off. Click **Turn SSH off** to go back.

Risky commands on the server still ask for your approval. This needs the OpenSSH client (`ssh`) on this computer.
The older "Saved SSH hosts" list (key-based login) is still there under it for advanced use.

## Skills

Open **Skills** in the sidebar to create skills. A skill is a folder `skills/<name>/SKILL.md` with a
name, a description, and instructions. 350 skills are included (200 of them new: server administration over SSH,
languages and frameworks, DevOps, AI/ML, web, business, writing, study and utilities).
The agent sees every skill name and the best matches for your request with their descriptions, and loads a skill when it fits the task.

## Playground

Write Python or HTML, run it, and save it into the workspace. Python code in any chat reply also has a
Run button.

## Troubleshooting

- **Page does nothing:** hard-refresh with Ctrl+F5, and make sure only one server window is open.
  Check http://127.0.0.1:8787/api/info (it shows the server version).
- **`cannot connect to ... 11434`:** start Ollama.
- **Image models missing:** add the API key for that provider in Settings, then click Refresh.
- **Windows Defender blocks files:** restore them from Protection history, or add the folder as an exclusion.
- **Slow answers:** use a smaller model, and keep the mode on Default.

## Thinking with any Ollama model
Plan/Max modes ask Ollama to think. If the model has no thinking support, Agent Studio detects it once,
falls back to a normal chat and remembers it, so every model works. Models that print `<think>…</think>`
inside the answer are handled too, and gpt-oss gets low/medium/high levels. While a model thinks you see
an animated brain with an orbit ring and sparks (faster in Plan and Max), a rotating status line, a progress sweep and a timer.
When the model streams its reasoning, it appears live in a panel; afterwards it folds into a "Thought for Ns · N words" pill.

## Live preview panel
HTML/SVG code blocks open in a panel beside the chat (automatically after a reply, or with the Preview button).
It has Preview/Code tabs, reload, "Open ↗" for a full tab, and a draggable edge to resize. Clicking an
.html file link from the Files list opens it there too. Previews run sandboxed and cannot touch the app.

## Autopilot (on by default)
The "Autopilot" pill next to the mode picker makes the agent keep working until the whole task is finished:
no 4/10/25 step limit (hard cap 200), it is nudged to continue if it stops early with "I will..." or without
finishing, it retries on network/model errors, and it is told to change approach if it repeats a failing step.
It ends with a message starting "DONE:". Risky commands and files outside the workspace still ask for your
approval. Settings has a new "Ollama context window" value (default 16384) so long tasks are not forgotten.

## What changed in v9
- **Model and image menus always drop down** from the pill (chat, image and image-model menus). They no longer flip upward, and the suggestion chips hide while a menu is open.
- **Commands run without asking when the safety checker is offline.** If the Ollama detector is not running, a built-in allowlist approves plain read, build and workspace-edit commands (for example `ls`, `cat`, `mkdir`, `python3 script.py`, `git status`). Anything with `rm`, `sudo`, `curl`, absolute or home paths, `..`, command substitution, or `npm install`/`git push` still asks for approval.
- **Jump to latest:** a ↓ button appears when you scroll up in a long chat.
- **Shortcuts:** Ctrl/Cmd+K starts a new chat; `/` focuses the message box.

## What changed in v9.1 / v9.2
- **Sign-in screen** (animated). The first account created is the admin. Later accounts are added by the admin in the Admin panel.
- **Chats are saved to the account on the server** (`data/chats/<account>.json`), not only in this browser. Each save is written atomically. Chats from older versions in this browser move into the account on first sign-in.
- **Admin panel:** see every account and every chat, create or delete accounts, reset passwords.
- **Network access:** the server listens on all interfaces by default (`HOST=0.0.0.0`). The terminal prints the address for this computer and the network addresses. Set `HOST=127.0.0.1` to allow only this computer. Use the port with `PORT`.
- **Terminal log:** every web action (sign-ins, requests, chat sends, tool calls, saves, admin actions) prints one line.
- **Thinking indicator** redesigned (sparkle + shimmering "Thinking" + live reasoning). **Max effort** has a moving Google-style gradient.
- **Sidebar:** search chats; double-click a chat to rename it.
- Security: anyone with an account can run agent commands on this computer (within the approval rules). Give accounts only to people you trust.
