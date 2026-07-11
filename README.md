# Ticking Type Bomb 💣⌨️
A localized, event-driven alarm clock built to defeat sleep inertia by physically decoupling the siren from the off-switch. 

## The Problem
Standard mobile alarms fail because the off-switch is in bed with you. When sleep inertia hits, the brain treats the "Snooze" or "Dismiss" button as the path of least resistance. Even puzzle-based alarm apps didn't work for me—I needed multiple layers of friction, but the best features on the market were locked behind subscriptions.

**The Goal:** Build a zero-cost system where the phone acts strictly as a "dumb speaker" and the only way to kill the alarm is by passing a strict cognitive typing test on a separate device across the room.

## The Architecture
The system relies on a basic Client-Server model running over a local Wi-Fi network (LAN), turning two distinct devices into a single closed-loop system.

*   **The Siren (Android / Server):** Built using the Automate app. A scheduled fiber triggers the audio loop at the target wake-up time. The flow then pauses at an HTTP Webhook listener, waiting for a specific `fetch` request on the local IP to kill the sound and close the connection.
*   **The Key (Laptop / Client):** A vanilla HTML/JS frontend that forces the user to accurately type a randomized, complex paragraph. Once the string exactly matches the target, the browser fires an HTTP `POST` request to the phone's local IP.

## Anti-Cheat Mechanics & UX
To prevent a sleep-deprived brain from bypassing the test, the UI enforces strict friction rules:

*   **No Copy/Paste:** The DOM actively blocks `contextmenu`, `cut`, `copy`, `paste`, and `drop` events.
*   **Text Highlighting Disabled:** `user-select: none` prevents attempting to drag-select the target text.
*   **The "Stun" Penalty (Rate Limiting):** A single typo doesn't wipe the text—it flashes the screen red and freezes the input field for 3 seconds (`inputField.disabled = true`). This mimics a token-bucket rate limit, penalizing mistakes with lost time and forcing intense focus.
*   **Lore-Heavy Payloads:** The target texts aren't standard typing test sentences. They are randomized lore drops and tenets from games like *Elden Ring* and *Sekiro*. Typing out Ranni's monologue perfectly requires actual cognitive presence, preventing muscle-memory autopilot.

## Security Post-Mortem & Known Vulnerabilities
While the frontend software is locked down, the system is fundamentally vulnerable to physical hardware exploits. 

If the user has unmitigated physical access to the Android device, they can bypass the HTTP webhook by manually killing the Automate notification channel from the lock screen. Furthermore, a highly motivated (and sleepy) user could simply hold the physical power button to restart the phone. 

**Conclusion:** Software locks are ultimately bound by the permissions of the host OS. A true, impenetrable alarm would require kernel-level Device Administrator privileges to block OS-level overrides. 

## Tech Stack
*   **Frontend:** HTML5, CSS3, Vanilla JavaScript
*   **Backend / Networking:** Local LAN IP routing, HTTP Fetch API, Android Automate (Flowchart Scripting)