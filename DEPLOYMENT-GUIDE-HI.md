# AI EDITOR PRO — Vercel पर वेबसाइट प्रकाशित करने की हिंदी गाइड

## पहले जरूरी बात समझें

यह ZIP एक **browser-based video editor starter** है। इसमें वीडियो import, manual timeline editing, heuristic frame-difference scene detection, audio-energy/silence analysis और browser-based export का कोड है। यह अभी किसी hosted generative-AI/LLM scene-understanding service को call नहीं करता और इसका अलग Node.js video-rendering backend भी नहीं है।

इसलिए इस गाइड से आप मौजूदा editor को सार्वजनिक वेबसाइट के रूप में deploy कर सकते हैं; इससे अपने-आप cloud AI features, user accounts या cloud project storage नहीं जुड़ेंगे।

## वेबसाइट deploy करने का तरीका

### 1. ZIP को extract करें

`AI-EDITOR-PRO-StackBlitz-Complete.zip` को extract करें। अंदर `ai-editor-pro` नाम का folder होगा। उसी folder के अंदर `package.json`, `src`, `index.html`, `vite.config.ts` और `vercel.json` मौजूद होने चाहिए।

### 2. GitHub repository बनाएं

1. GitHub में sign in करके नया repository बनाएं।
2. Repository में `ai-editor-pro` folder **के अंदर की सभी फाइलें** upload करें। ध्यान रखें कि `package.json` repository के root में हो, एक अतिरिक्त nested folder के अंदर नहीं।
3. `.env` या `.env.local` जैसी निजी environment files कभी upload न करें। इस starter के `.gitignore` में ये पहले से ignore हैं।

### 3. Vercel से GitHub repository जोड़ें

1. https://vercel.com/ खोलें और GitHub से sign in करें।
2. Dashboard में **Add New → Project** चुनें।
3. अपनी repository के सामने **Import** दबाएं।
4. Project settings में ये values जाँचें:
   - Framework Preset: Vite (या Auto-detected)
   - Root Directory: `./` (जब `package.json` repository root में है)
   - Build Command: `npm run build`
   - Output Directory: `dist`
5. इस starter को पहले बिना Supabase environment variables के deploy करें। फिर **Deploy** दबाएं।
6. Deployment पूरा होने पर Vercel एक website URL देगा। उसी URL को खोलकर editor जाँचें।

Vercel की `vercel.json` फ़ाइल में build command, output directory और SPA fallback पहले से जोड़े गए हैं। अगर तुमने repository के root में सीधे project contents upload किए हैं, तो Root Directory `./` ही रखें।

## अभी deploy होने पर क्या काम करेगा?

- Browser-supported video files import करना
- Timeline में clips जोड़ना, चुनना, split करना और reorder करना
- Preset आधारित heuristic auto-edit
- Browser में scene-frame difference और audio-energy analysis
- Browser support के अनुसार MP4 या WebM export

वीडियो processing और export उपयोगकर्ता के browser/device पर चलेंगे। बड़ी फ़ाइलें, 1080p/60 FPS और मोबाइल डिवाइस की memory के कारण performance अलग-अलग हो सकती है। पहले छोटी MP4 फ़ाइल से परीक्षण करें।

## अभी क्या production-ready नहीं है?

- यह semantic AI scene understanding या cloud generative AI model नहीं है।
- Preview अभी पूरी timeline का composited playback नहीं दिखाता; यह selected source clip preview है।
- Final render browser के MediaRecorder/canvas pipeline से होता है; server-side FFmpeg render नहीं है।
- Supabase Save/Load के लिए मौजूदा app में authentication UI और per-user ownership isolation लागू नहीं है।
- `supabase-setup.sql` में demo `anon` policies हैं जो सभी anonymous visitors को demo table/bucket तक पहुँच दे सकती हैं। **इसे public website या real/private videos वाले project पर न चलाएं।**
- Public production use से पहले Supabase Auth, per-user RLS/Storage policies, project ownership और account flows implement तथा test करने होंगे।

## Supabase के बारे में

पहले public frontend deployment को Supabase के बिना test करें। `.env.example` में केवल variable names हैं। `VITE_SUPABASE_URL` और `VITE_SUPABASE_ANON_KEY` जोड़ने से पहले सुरक्षित authentication और RLS लागू करना जरूरी है। Supabase `service_role` key या कोई server secret कभी `VITE_` variable में न रखें—Vite ऐसे variables को browser bundle में expose करता है।

## Deploy के बाद परीक्षण

1. Website खोलें और एक छोटी H.264/AAC MP4 import करें।
2. उसे timeline में जोड़ें, split/reorder करें और transition चुनें।
3. Footage Analysis & Auto Edit चलाएँ और timeline को manually जाँचें।
4. 5–10 सेकंड का 720p/30 FPS sample export करें।
5. डाउनलोड हुई file को अलग video player में चलाकर video और audio जाँचें।
6. दूसरे browser और mobile device पर भी परीक्षण करें।

## Build verification status

इस ZIP की dependency installation इस जाँच वाले environment में npm registry timeout के कारण पूरी नहीं हो सकी और local npm cache में packages उपलब्ध नहीं थे। इसलिए `npm run build` को सफल घोषित नहीं किया गया है। Vercel deployment से पहले repository पर build result अवश्य जाँचें; build fail हो तो Vercel के Build Logs साझा करें।
