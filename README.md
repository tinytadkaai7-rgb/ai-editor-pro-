# AI EDITOR PRO — Browser Video Editor

A React 18 + TypeScript + Vite editing workspace designed to run in StackBlitz or locally.

## Included in this build

- Import browser-decodable video files and inspect metadata.
- Manual timeline: add clips, select, trim source in/out, split, reorder, remove, undo and redo.
- Five transitions: dissolve and four directional wipes. Transition overlaps are applied by the timeline model.
- Local footage analysis: sample real decoded video frames and compare frame signatures to detect likely hard scene changes; this is genuine frame analysis, but it is not semantic understanding by a trained AI model.
- Audio-energy analysis: decode audio with Web Audio, find quiet sections, and build keep ranges. Silence detection is best-effort and can be unavailable for files whose audio codec the browser cannot decode.
- Presets: Cinematic, Professional YouTube, Fast-paced Shorts and Documentary. The planner uses actual detected scene/audio ranges plus deterministic preset rules; it is not a cloud generative AI model or semantic story editor.
- Browser export through canvas.captureStream + MediaRecorder, including visual transition compositing and decoded audio mixed with transition fades.
- Resolution options (480p / 720p / 1080p), 24 / 30 / 60 FPS, progress, cancellation, and MP4 when the browser supports it with WebM fallback.
- Export thumbnail, estimated size and render-time guidance (estimates, not guarantees).
- Optional Supabase persistence: source videos upload to Storage; project title, timeline and storage paths are saved in Postgres; project load downloads source files again.

## Run in StackBlitz

1. Create a new **Vite + React + TypeScript** project, or import this ZIP as a project.
2. Make sure all files/folders from this archive are copied into the project root (including `src`, `index.html`, `vite.config.ts`, `tsconfig.json`, and `package.json`).
3. Open the StackBlitz terminal and run:
   ```bash
   npm install
   npm run build
   npm run dev
   ```
4. Open the preview. Import two short MP4 H.264/AAC test videos, add them to the timeline, select a transition for the second clip, and export a short 720p / 30 FPS sample first.

## Configure Supabase (optional)

1. Create a Supabase project.
2. Open **Project Settings → API** and copy the Project URL and `anon` key.
3. Add these variables to the StackBlitz environment/secrets panel (or `.env` for local development):
   ```env
   VITE_SUPABASE_URL=https://YOUR_PROJECT.supabase.co
   VITE_SUPABASE_ANON_KEY=YOUR_SUPABASE_ANON_KEY
   ```
4. Run the SQL in `supabase-setup.sql` in Supabase SQL Editor.
5. Restart the Vite dev server. Use **Save** to upload source clips and project metadata; use **Load** to download and restore them.

### Supabase security warning

The supplied SQL deliberately grants the `anon` role access to the single demo project and its media bucket so the no-login starter works. This is suitable only for a private test project with no sensitive files. Anyone who obtains the public project URL and anon key could access data under these policies. Before a public deployment, implement Supabase Auth, owner-scoped project IDs, and RLS/Storage policies that restrict every row and object to `auth.uid()`; do not treat the anon key as a secret.

## Important technical limits (read before production)

- This is **local deterministic analysis**, not an LLM or cloud AI model. Scene detection samples frames at intervals and uses image-difference scoring; quick cuts between samples can be missed, and camera flashes/motion may be false positives. Thresholds are heuristic.
- Silence detection uses RMS audio energy and a fixed -43 dB threshold. Music, noise, quiet speech, audio compression, and codec differences can affect results. Always review the generated timeline.
- The preview monitor displays the selected source clip, not a fully composited playback of the entire timeline. The exported render does composite timeline transitions. This is an explicit current limitation, not a hidden feature.
- Browser export is real-time. Keep the tab foregrounded and the device powered. Large 1080p/60 FPS edits can drop frames or run out of memory on mobile devices.
- MP4 recording depends on browser MediaRecorder support. WebM is used when MP4 is unavailable. The filename extension follows the recorded MIME type.
- Audio decode may fail for some codecs. In that case the video still exports and the UI reports which source audio could not be decoded.
- Browser and device support varies. Test the target browser, source codecs, transitions, audio sync, and downloads with real footage before production use. No browser application can truthfully guarantee zero errors on every device.
- Supabase stores the source files and edit metadata, not a server-rendered final output. Final video export downloads locally in the browser.
