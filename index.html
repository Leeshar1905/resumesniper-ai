// api/tailor.js
// Vercel Serverless Function — Node.js runtime
//
// Receives: POST { apiKey, bullet, jobDesc }
// Returns:  { result: "rewritten bullet text" }
//           or { error: "message" } with an appropriate HTTP status

export default async function handler(req, res) {

  // ── 1. Only accept POST ──────────────────────────────────────────────────
  if (req.method !== 'POST') {
    return res.status(405).json({ error: 'Method not allowed. Use POST.' });
  }

  // ── 2. Parse & validate the request body ────────────────────────────────
  const { apiKey, bullet, jobDesc } = req.body ?? {};

  if (!apiKey || typeof apiKey !== 'string' || !apiKey.startsWith('AIza')) {
    return res.status(400).json({ error: 'A valid Google API key (starting with "AIza") is required.' });
  }

  if (!bullet || typeof bullet !== 'string' || bullet.trim().length === 0) {
    return res.status(400).json({ error: 'A non-empty resume bullet is required.' });
  }

  if (!jobDesc || typeof jobDesc !== 'string' || jobDesc.trim().length === 0) {
    return res.status(400).json({ error: 'A non-empty job description is required.' });
  }

  // ── 3. Build the Gemini prompt ───────────────────────────────────────────
  const prompt = `
You are an expert resume writer and career coach.

Your task: rewrite the "Original Bullet" below so it closely mirrors the language,
keywords, and priorities found in the "Job Description". The rewritten bullet must:
- Begin with a strong past-tense action verb
- Include at least one quantifiable metric (invent a plausible one if none exists)
- Incorporate 2–4 keywords or phrases that appear verbatim in the job description
- Stay under 30 words
- Output ONLY the single rewritten bullet — no preamble, no explanation

--- Original Bullet ---
${bullet.trim()}

--- Job Description ---
${jobDesc.trim()}

Rewritten Bullet:`.trim();

  // ── 4. Call the Gemini API ───────────────────────────────────────────────
  const GEMINI_URL =
    `https://generativelanguage.googleapis.com/v1beta/models/gemini-1.5-flash:generateContent?key=${apiKey}`;

  let geminiRes;
  try {
    geminiRes = await fetch(GEMINI_URL, {
      method: 'POST',
      headers: { 'Content-Type': 'application/json' },
      body: JSON.stringify({
        contents: [
          {
            parts: [{ text: prompt }],
          },
        ],
        generationConfig: {
          temperature: 0.7,
          maxOutputTokens: 120,
        },
      }),
    });
  } catch (networkErr) {
    console.error('[tailor] Network error reaching Gemini:', networkErr);
    return res.status(502).json({
      error: 'Could not reach the Gemini API. Check your network or try again.',
    });
  }

  // ── 5. Handle Gemini error responses ────────────────────────────────────
  if (!geminiRes.ok) {
    let geminiError = `Gemini API returned status ${geminiRes.status}`;
    try {
      const errBody = await geminiRes.json();
      geminiError = errBody?.error?.message ?? geminiError;
    } catch { /* ignore JSON parse failures */ }

    console.error('[tailor] Gemini error:', geminiError);

    // Surface auth errors clearly to the client
    const clientStatus = geminiRes.status === 400 || geminiRes.status === 401 || geminiRes.status === 403
      ? 400
      : 502;

    return res.status(clientStatus).json({ error: geminiError });
  }

  // ── 6. Parse the Gemini response ─────────────────────────────────────────
  let result;
  try {
    const geminiData = await geminiRes.json();
    result = geminiData?.candidates?.[0]?.content?.parts?.[0]?.text?.trim();
  } catch (parseErr) {
    console.error('[tailor] Failed to parse Gemini response:', parseErr);
    return res.status(502).json({ error: 'Unexpected response format from Gemini.' });
  }

  if (!result) {
    return res.status(502).json({
      error: 'Gemini returned an empty response. The content may have been filtered.',
    });
  }

  // ── 7. Return the tailored bullet to the frontend ─────────────────────────
  return res.status(200).json({ result });
}
