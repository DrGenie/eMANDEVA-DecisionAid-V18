# Policy Assistant test cases (V18.1)

V18.1 is an interface patch: the chatbot layout was simplified (four primary
quick buttons, the rest under "More prompts"), the response area enlarged, and
mobile usability improved. Backend, model and error handling are unchanged.

Run after deploying the Worker and pushing the frontend. The assistant must
always use the live tool state and never invent numbers. Quick buttons must
behave exactly like typing the same prompt into the free-text box.

## A. Core AI behaviour
1. Free-text AI response: type "Explain the results in lay terms for the
   public". Expect a complete answer using live values; it says predicted
   support is stated-preference support, not actual uptake.
2. All quick buttons: click each of the twelve buttons. Each returns a real AI
   answer, none shows a generic offline message when the backend is healthy.
3. Long detailed question: paste a long, detailed multi-part question (a few
   thousand characters). Expect a complete answer, not a silent failure.
4. Very long input graceful handling: paste an extremely long question
   (over ~12000 characters). Expect a clear QUESTION_TOO_LONG message asking
   you to shorten or split it. A long-but-manageable one should be answered
   with a note that it was shortened.
5. No duplicate request on double-click: rapidly double-click a quick button or
   Send. Expect only one request (Send is disabled while busy).

## B. Errors (specific, not generic)
6. Quota or rate limit: when exceeded, expect a clear RATE_LIMITED or
   QUOTA_EXCEEDED message advising a short wait, not a generic offline note.
7. Invalid model: set GEMINI_MODEL to an invalid name and redeploy; expect a
   MODEL_NOT_AVAILABLE message advising to switch model. Restore afterwards.
8. Missing API key: with no GEMINI_API_KEY secret, expect a clear key-missing
   message.
9. CORS handling: from a non-allowed origin, expect a CORS message; from the
   allowed origin, requests work.
10. Backend health endpoint: open
    https://emandeval-chat.drgenie.workers.dev/api/health and confirm JSON
    with ok true, version V18, modelConfigured true, model, provider, and NO
    secrets.

## C. Layout and usability
11. Mobile layout: on a phone, the panel is a full-height sheet, the close and
    minimise buttons work, and the panel does not cover important tool buttons
    (the floating button hides while the panel is open).
12. Copy answer: Copy response copies the text and shows a confirmation.
13. Add to report: Add to report inserts the answer marked clearly as
    AI-generated with a review-before-use disclaimer.
14. Stop generating: start a request and click Stop; generation cancels and the
    controls return to normal.
15. Status badges: backend shows connected after the health check; "using
    current result" is on after a design is applied; saved-options badge
    reflects the number saved.
16. Saved-options comparison: with none saved, Compare saved options says
    options are needed first; with several saved, it compares them.

## D. Security
17. No API key in the frontend: view source and assistant.js; the key must not
    appear. Search the repository for the key value; it must not be found.
18. No browser call to Gemini directly: in DevTools, confirm requests go only
    to the Worker URL, never to generativelanguage.googleapis.com.

## E. Guardrails (all responses)
19. Predicted support is stated-preference support, not actual uptake.
20. No legal advice and no medical advice.
21. Never states a mandate should definitely be implemented.
22. Uses only the current tool state; does not invent numbers.
23. Explains assumptions and limitations when relevant.
24. Stays within Australia, France and Italy, or frames anything else as
    outside the model.

## F. V18.1 layout patch
25. Primary buttons only: on open, exactly four primary quick buttons are
    visible (Explain result, Explain for public, Draft briefing, Compare
    options). The other prompts are not visible until expanded.
26. More prompts expands and collapses: clicking "More prompts" reveals the
    eight secondary prompts in a two-column grid on desktop and a single
    column on mobile, and collapses again smoothly. It is collapsed by default.
27. Secondary prompts still work: each of the eight prompts under "More
    prompts" sends a real request through the same pathway and renders an
    answer.
28. Response area is dominant and scrolls: the conversation area is the largest
    region of the panel and scrolls independently. Long answers stay fully
    readable and never disappear behind the prompts, input or footer.
29. Input stays put: the input box, Send and Stop remain visible; the response
    never overlaps them. On mobile the panel is a near full-screen sheet with a
    large scrollable response area and a visible input.
30. Controls lock while generating: quick buttons (primary and secondary) and
    Send are disabled during generation, a "Thinking" indicator shows, and a
    double-click does not send a duplicate request. Controls restore after the
    response completes, errors, or is stopped.
31. Same live tool state: a primary quick button, a secondary prompt and a
    free-text question all send the same toolState to the same Worker URL and
    render with the same formatting.
32. Reduced clutter: only one short privacy line is visible; the longer
    explanation sits under "About this assistant" and is collapsed by default.
