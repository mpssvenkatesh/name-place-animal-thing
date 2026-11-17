# Name · Place · Animal · Thing

An online multiplayer version of the classic word game. It's one static page (`index.html`) that uses Firebase Realtime Database for live sync and runs on Vercel.

## Try it right now (no setup)
Open `index.html` in a browser, create a room, then open the same page in a **new tab** (not a duplicated tab), and join with the code. Without a Firebase config the game runs in **local test mode**, which only syncs between tabs in the same browser.

> **Already configured:** this copy is connected to the Firebase project `name-place-animal-thing-9cba3`, and its database rules are published. The steps below are only needed if you ever move it to a different Firebase project.

## 1. Set up Firebase (≈5 min)
1. Go to <https://console.firebase.google.com>, then **Add project** (or reuse the project from your bingo game).
2. **Build → Realtime Database → Create database**. Pick a location and start in **locked mode**.
3. Open the **Rules** tab, paste the contents of `database.rules.json`, and click **Publish**.
4. **Project settings (⚙) → Your apps → Web (`</>`)**. Register an app and copy the `firebaseConfig` object.
5. In `index.html`, find `const firebaseConfig = { … }` near the top of the script and paste your values. Make sure **`databaseURL`** is included (it looks like `https://<project>-default-rtdb.firebaseio.com`, or `…firebasedatabase.app` outside the US).

> If you reuse the bingo project, both games can share the database. This one only touches the `rooms/` path. If bingo also uses `rooms/`, change `const ROOT = 'rooms/'` in `index.html` to something like `'npat-rooms/'`, and rename `"rooms"` in the rules to match.

## 2. Deploy on Vercel
1. Create a GitHub repo (e.g. `name-place-animal-thing`) and push `index.html`, `README.md`, and `database.rules.json`.
2. In Vercel, go to **Add New → Project**, import the repo, keep the framework preset as **Other**, and click **Deploy**. No build step is needed.
3. Share the URL. Invite links look like `https://your-app.vercel.app/#ABCD` and open with the room code already filled in.

## How a game works
- **Lobby:** the host (👑) picks categories (presets + custom, 2–10), number of rounds, timer, and whether the round ends on **STOP** or when the timer runs out. They can also skip hard letters (Q U V X Y Z).
- **Round:** after a 3-2-1 countdown everyone gets the same letter (no repeats within a game). A ✓/✗ next to each box shows whether the answer starts with the right letter. The first player to fill every box can hit **STOP**, which freezes everyone's sheet.
- **Scoring (host picks one):**
  - **🔄 Swap sheets (default):** like passing papers. Each player is handed another player's sheet (it rotates every round, so you mark someone different) and gives each answer **10, 5 or 0**. The game pre-fills a suggestion: 10 if nobody else wrote it, 5 if someone else wrote the same thing, 0 for blank or wrong letter. The marker can change any of them, and each player can watch their own sheet being marked live. Scores lock in automatically once everyone taps **Done marking**, or the host can lock early, in which case unmarked answers get the suggested points.
  - **🗳 Group vote:** everyone sees all answers and taps ✗ on ones that don't count. An answer is out if more than half of the *other* players vote against it. Points are 10 for unique, 5 for shared, and 0 otherwise.
  - When matching answers, the game ignores capitals, spaces and accents.
- **Results → Final:** leaderboard after each round, a podium at the end, and **Play again** keeps the same room.

Reconnecting is handled: if someone refreshes, they rejoin with their score. If the host leaves, the longest-standing player takes over as host automatically.

## Notes
- The included rules leave rooms open to anyone who knows a code. That's fine for games with friends, but it isn't locked down. Old rooms aren't deleted automatically, so you can clear `rooms/` in the Firebase console now and then.
- Everything (HTML, CSS, JS) is in `index.html`, so it's easy to edit. Category presets are in `DEFAULT_CATS` / `EXTRA_CATS`, and point values are in `computeRound()`, and who marks whom is in `markerMap()`.
