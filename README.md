# Workout

Four-day rotation: Push, Pull, Hinge/Squat, Core. Runs entirely in the browser, no backend, no accounts.

## Why it has to be hosted

iOS will not execute JavaScript in an HTML file opened from the Files app or a document picker. The preview sandbox gives the page a single file and no real origin, so the page renders as inert markup. Served over HTTPS it runs normally, which is what this repo is for.

## Putting it on GitHub Pages

1. Create a new repository on github.com. Public is fine. Call it `workout`.
2. Upload all six files in this folder to the root of the repo. The web UI works: **Add file → Upload files**, drag them in, commit.
3. **Settings → Pages**. Under *Build and deployment*, set Source to **Deploy from a branch**, branch **main**, folder **/ (root)**. Save.
4. Wait about a minute. The URL will be `https://<your-username>.github.io/workout/`.

## Putting it on the phone

1. Open that URL in **Safari** on the iPhone.
2. Share button → **Add to Home Screen** → Add.
3. Launch from the new icon. It opens full screen with no Safari chrome, and nothing runs in the background once you close it.

Delete the Scriptable script when you're satisfied this works.

## Files

| File | Purpose |
| --- | --- |
| `index.html` | The whole app: program data, UI, timers, rotation tracking |
| `manifest.webmanifest` | Name, icons, standalone display mode |
| `sw.js` | Service worker. Network first, cache fallback, so the app works with no signal |
| `icon-180.png` | Home screen icon for iOS |
| `icon-192.png`, `icon-512.png` | Manifest icons |

## Editing the program

Everything lives in the `DAYS` array near the top of the `<script>` block in `index.html`. Each exercise is:

```js
{ name: 'DB Bench Press', sets: 3, reps: '8 reps', workTime: 45,
  note: 'Full ROM, 3s eccentric.', tag: 'upper' }
```

`workTime` is the set countdown in seconds. `tag` is optional and only used on the core day (`upper`, `lower`, `oblique`).

Rest between sets is one constant, `REST_DURATION`, currently 30 seconds.

After editing, commit the file. Reload the app once while online to pick up the new version, since the service worker fetches from the network first.

## Timers and pausing

Both countdowns run off a wall-clock end time, so the display never drifts.

- Tap the timer ring to pause, tap again to resume. The number and ring turn grey and the label reads `paused`.
- The session clock at the top right holds while paused, so a pause does not inflate your session time.
- Backgrounding the app pauses the countdown automatically and resumes it when you come back. A pause you set by hand survives backgrounding and only lifts when you tap the ring again.

Green is work, burnt orange is rest, grey is paused.

## Rotation tracking

State is a single `localStorage` key, `workoutRotation_v2`, holding the last day completed and a timestamp. The home screen dims that day and outlines the next one.

- Finishing a session records it. Ending early records it too, if at least one set was completed.
- Long-press any day card for 600ms to set it manually as your most recent session.
- The `reset` link clears history and sends you back to Day 1.

Installing to the Home Screen gives the app its own persistent storage, so the rotation survives reboots and Safari cache clearing.
