# Selfie Studio

**AppADay 180** | Category: Creative (C) | Uses Claude

Selfie Studio turns a handful of everyday selfies into polished headshots without generating fake faces. Claude scores each photo for lighting, framing, expression, and background and writes a LinkedIn headline and caption. In the Studio, Claude art directs a retouch of your own photo (light, color, skin, eyes, tilt, key light, and backdrop color), you refine it in plain words, and an on device portrait cutout places you on studio backdrops before exporting four ready sizes.

Live app: https://augustineiacopelli.github.io/appaday-180-selfie-studio/

Portfolio: https://augustineiacopelli.github.io/appaday/

## How to use it

1. **Add.** Choose 3 to 10 photos from your library or take a selfie with the front camera. Three photos unlock the critique. One photo is enough to use Skip Critique and go straight to the Studio. Tap the small x on any thumbnail to remove it.
2. **Critique.** Optionally describe your role or field so the headline fits, then run the critique. Claude scores every photo from 1 to 10 on lighting, framing, expression, and background (40 possible), marks the Best photo, suggests a backdrop, and drafts a LinkedIn headline (220 characters at most) and a caption (150 at most). Both are editable, with live character counters and Copy buttons. Use This Photo on any card sends that photo to the Studio.
3. **Studio.** Tap Retouch with Claude. Claude studies the chosen photo and returns a retouch recipe: exposure, contrast, shadows, highlights, warmth, tint, saturation, skin smoothing, under eye lift, eye brightening, a soft key light from one side, head straightening, and a backdrop color that suits your skin tone and clothing. The app applies the recipe on your device using a face map, and Claude lists each change in plain words. Ask for changes with the quick chips (More natural, Warmer, More corporate, Brighter, Less smoothing, Change the backdrop color) or by typing your own request, and Claude revises the recipe. Hold to compare shows the original. Fine tune exposes every setting as a slider, with Undo and Reset. The on device cutout lifts you off the background. Pick a backdrop (Studio gradient in three tones, Office blur, Editorial, Warm outdoor, or Solid in white, light gray, navy, or plum), drag the photo to reposition it, set Zoom, and adjust Edge softness. Cutout Preview shows the cutout over a checkerboard. The Cutout and No Cutout control switches modes. In No Cutout mode the backdrop row becomes a treatment row: Natural, Warm, Editorial, and Soft Vignette.
**Reshoot coach.** Coach me through a better shot (on the Add screen, and Reshoot with the coach after a critique) opens the front camera with live on device guides. A level line follows your eyes, and a target oval and eye line show where to sit. Six checks turn green as you fix them: level head, facing the camera, phone at eye level, distance, centered, and even light on both cheeks. One plain sentence names the next thing to fix, and the notes from your critique sit underneath. When everything is green, Auto capture counts down and takes the shot, or you can tap the shutter. New shots join your set so the critique can compare them with the originals.

4. **Export.** Four sizes are rendered fresh from the full resolution photo, each with its own head framing. Download saves a PNG. On a phone it opens the share sheet so you can save to Photos; on a computer it downloads. Download All shares all four files at once, or downloads them one after another.

| Size | Pixels | File |
| --- | --- | --- |
| LinkedIn | 800 by 800 (circle crop previewed on screen only) | selfie-studio-linkedin.png |
| Instagram | 1080 by 1350 | selfie-studio-instagram.png |
| ID Square | 600 by 600 (badges and school IDs, not a passport photo) | selfie-studio-id-square.png |
| Parish Directory | 1200 by 1500 | selfie-studio-parish-directory.png |

## Privacy

Photos are decoded in the browser tab and never saved to storage. Closing or reloading the tab discards them. The critique sends each photo to Claude as an 896 pixel JPEG and nothing else. The cutout runs entirely on the device. The only things saved in the browser are the critique text, the chosen photo number, the edited headline and caption, the optional role line, and the default edge softness. Clear Session in Settings removes all of it.

## Claude

The critique uses the sonnet tier through the shared models.js, so the model follows the portfolio config and no model ID lives in this app. The key comes from the shared AppADay key ring (keys.js, app 180). Settings mounts the key manager; the app has no key field of its own. If the key sheet is dismissed, the critique simply does not run. Requests use max_tokens 1800, read only text blocks, and fall back through the thinking settings (between_tools, then disabled, then none) when the model rejects one. Replies must be strict JSON; the parser strips code fences, takes the outermost braces, validates every field, and clamps every score to 1 to 10. Each kind of failure (connection, rejected key, rate limit, oversized request, busy service, unexpected format) gets a plain sentence and a Retry button.

## The on device model exception

AppADay apps normally load nothing beyond fonts and Chart.js. This app also loads two on device models, both imported lazily the first time the Studio opens, so the Add and Critique steps never download them. The first load is about 30 MB and the browser caches it afterward.

**Face map:** Google MediaPipe Tasks Vision, pinned to **@mediapipe/tasks-vision 1.2.0** from jsdelivr with the wasm from that same version, running FaceLandmarker (face_landmarker float16 from storage.googleapis.com) in IMAGE mode on the CPU. Its 478 landmarks place the eyes for framing, measure head tilt, and define the skin, eye, and under eye regions the retouch touches.

**Portrait matte:** **@huggingface/transformers 4.3.2** from jsdelivr running MODNet (Xenova/modnet, 8 bit) on WebAssembly. MODNet is a portrait matting model, so it returns a soft alpha matte with hair detail rather than a yes or no mask. The matte is then refined against the photo itself with a guided filter at up to 1024 pixels, so the edge follows hair and shoulders, and edge colors are pulled toward the subject so the old background does not fringe the outline. Edge softness defaults to 1.

All retouching runs in pixel loops, a guided filter for edge preserving skin smoothing, and canvas compositing; canvas filters are never used. Office blur builds a clean plate by removing the person and filling the gap from coarse to fine before blurring, so no ghost of your outline shows behind you.

## Fallback behavior

The model loads race a 25 second timeout. If the face map cannot load, the tone and color parts of the retouch still work and the face specific settings have no effect. If the matte cannot load because jsdelivr or Hugging Face is blocked, slow, or failing, a toast explains it and the Studio switches to No Cutout mode, where the four treatments and all four export sizes still work. If the load finishes after the timeout, a toast says the cutout is ready, and tapping Cutout uses it. Tapping Cutout after a failed load tries again. If no person is found in a photo, that photo stays in No Cutout mode and the next photo returns to Cutout automatically.

## Files

Single index.html with inline CSS and JS, plus this README. Head tags: home screen icon block, Save to Phone manifest and save.js, models.js, and keys.js with data-app 180.

## 2.0 candidate

Photo to video: turn the finished headshot into a short subtle motion clip (a slow push in with a light sweep across the backdrop) for profile banners and story posts, still built only from the real photo.
