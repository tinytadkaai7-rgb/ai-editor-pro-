# Real-device acceptance checklist

The code package has static syntax/type checks, but browser media rendering needs real-file testing. Test on the target phone and desktop browser before production.

## Required smoke test

1. Import two short MP4 files with H.264 video and AAC audio.
2. Add both to the timeline and move one clip before/after the other.
3. Trim both clips and split one clip.
4. Apply each transition type to the second clip and verify the overlap duration.
5. Run Footage Analysis & Auto Edit. Compare the detected cuts with the footage manually; do not assume every hard cut is found.
6. Check a clip with speech and long quiet gaps. Confirm the audio-energy analyzer identifies the expected regions; quiet speech may be classified as silence.
7. Export a 5–10 second 720p / 30 FPS sample. Confirm the downloaded file plays and transitions/audio are synchronized.
8. Repeat at 1080p / 60 FPS only on a capable desktop. Mobile devices may drop frames or run out of memory.
9. Test cancel during preparation and during recording.
10. Configure Supabase, run `supabase-setup.sql`, Save, refresh, Load, and confirm source clips are downloaded and timeline IDs match.

## Known limits

- No semantic/generative model is called. Scene cuts use sampled frame-difference scoring; silence uses RMS energy.
- Preview is selected-source preview; final export composites the full timeline.
- Export is real-time browser recording, not offline/server rendering.
- Supabase anon demo policies are open. Add Auth and owner-scoped RLS before a public launch.
