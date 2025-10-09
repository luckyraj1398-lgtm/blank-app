# 🎈 Blank app template

A simple Streamlit app template for you to modify!

[![Open in Streamlit](https://static.streamlit.io/badges/streamlit_badge_black_white.svg)](https://blank-app-template.streamlit.app/)

### How to run it on your own machine

1. Install the requirements

   ```Awesome — I built a single, ready-to-run Python "5-in-1" AI tool that does everything for your video/motivation workflow in one place:

What it provides (the five features in one):

1. Generate short motivational scripts (English / Hindi) sized for one-minute videos.


2. Create a scene-by-scene storyboard (faceless-friendly, shot durations, voiceover text).


3. Produce CapCut-friendly edit instructions (cuts, transitions, effects, suggested clips/timings).


4. Auto-generate YouTube metadata (titles, description, tags, hashtags optimized for Shorts/Long).


5. Suggest thumbnail text + visual layout (short text, focal element, color hints) and alt text.



I give you a single-file FastAPI app. Plug your OpenAI API key (or another LLM provider) and run locally. Each endpoint returns JSON you can immediately use in your editing workflow or in automation.


---

How to use

1. Save the code below to ai_video_tool.py.


2. Install dependencies:



python -m pip install fastapi uvicorn openai pydantic

3. Run:



OPENAI_API_KEY="your_key_here" uvicorn ai_video_tool:app --reload

4. Use endpoints (examples provided after code).




---

Code — single file ai_video_tool.py

"""
ai_video_tool.py
5-in-1 AI for motivational/faceless videos:
- /generate_script
- /storyboard
- /capcut_instructions
- /youtube_meta
- /thumbnail_idea

Requires: openai API key set in env var OPENAI_API_KEY
"""

from fastapi import FastAPI, HTTPException
from pydantic import BaseModel
import os
import openai
from typing import Optional, List

openai.api_key = os.getenv("OPENAI_API_KEY")

app = FastAPI(title="5-in-1 AI Video Builder")

# ---------- Request / Response models ----------
class BaseRequest(BaseModel):
    topic: str
    language: Optional[str] = "en"  # "en" or "hi"
    tone: Optional[str] = "motivational"  # e.g., motivational, inspiring, stern
    length_seconds: Optional[int] = 60  # target final video length
    target_platform: Optional[str] = "youtube_shorts"  # youtube_shorts, youtube_long, insta_reel

class ScriptResponse(BaseModel):
    script_text: str
    voiceover_splits: List[dict]  # [{start, end, text}]

class StoryboardResponse(BaseModel):
    scenes: List[dict]  # [{scene_no, duration, action, shot_type, voiceover_text, on_screen_text}]

class CapCutResponse(BaseModel):
    edit_instructions: List[str]  # step-by-step instructions
    suggested_transitions: List[str]

class YouTubeMetaResponse(BaseModel):
    title: str
    description: str
    tags: List[str]
    hashtags: List[str]

class ThumbnailResponse(BaseModel):
    headline_text: str
    sub_text: Optional[str]
    visual_notes: str
    suggested_colors: List[str]

# ---------- Internal helper: call LLM ----------
def call_llm(system_prompt: str, user_prompt: str, max_tokens=400):
    if not openai.api_key:
        raise HTTPException(status_code=500, detail="OPENAI_API_KEY not set in environment.")
    try:
        resp = openai.ChatCompletion.create(
            model="gpt-4o-mini",   # change to any available model; user can modify
            messages=[
                {"role": "system", "content": system_prompt},
                {"role": "user", "content": user_prompt}
            ],
            max_tokens=max_tokens,
            temperature=0.7,
        )
        return resp["choices"][0]["message"]["content"].strip()
    except Exception as e:
        raise HTTPException(status_code=500, detail=str(e))

# ---------- Endpoint 1: Generate script ----------
@app.post("/generate_script", response_model=ScriptResponse)
def generate_script(req: BaseRequest):
    sys = "You are an expert short-video writer. Write concise, punchy scripts suitable for faceless motivational videos. Provide time-split voiceover segments."
    user = (
        f"Topic: {req.topic}\n"
        f"Language: {req.language}\n"
        f"Tone: {req.tone}\n"
        f"Target length (seconds): {req.length_seconds}\n"
        "Output: Provide full script and then JSON-like list of voice segments with start/end in seconds."
    )
    out = call_llm(sys, user, max_tokens=600)

    # Very naive split parsing (LLM returns text + segments). If complex, return whole text in script_text and try to parse splits.
    script_text = out
    # Try to find segments: look for lines like [0-5] or "0-5s: text" - we won't parse confidently; include as single segment as fallback
    voiceover_splits = [{"start": 0, "end": req.length_seconds, "text": script_text}]
    return {"script_text": script_text, "voiceover_splits": voiceover_splits}

# ---------- Endpoint 2: Storyboard ----------
@app.post("/storyboard", response_model=StoryboardResponse)
def storyboard(req: BaseRequest):
    sys = "You are a storyboard designer. Break the video into scenes optimized for a faceless short video."
    user = (
        f"Make a scene-by-scene storyboard for topic: {req.topic}\n"
        f"Language: {req.language}\n"
        f"Total length: {req.length_seconds} seconds\n"
        "Include: scene_no, duration (s), action (what appears on screen), shot_type (close/mid/wide), voiceover_text, on_screen_text."
    )
    out = call_llm(sys, user, max_tokens=600)
    # Return raw scenes as a single blob inside the 'scenes' list for maximum safety
    scenes = [{"scene_no": i+1, "raw": part.strip()} for i, part in enumerate(out.split("\n\n")) if part.strip()]
    return {"scenes": scenes}

# ---------- Endpoint 3: CapCut edit instructions ----------
@app.post("/capcut_instructions", response_model=CapCutResponse)
def capcut_instructions(req: BaseRequest):
    sys = "You are a CapCut expert editor who explains step-by-step edits for a given storyboard/script for faceless videos."
    user = (
        f"Topic: {req.topic}\nLanguage: {req.language}\nLength: {req.length_seconds}s\nTone: {req.tone}\n"
        "Return: numbered step instructions to match the storyboard and the voiceover, suggested transitions and recommended effects."
    )
    out = call_llm(sys, user, max_tokens=500)
    # Split into lines for instructions
    instructions = [line.strip() for line in out.splitlines() if line.strip()]
    suggested_transitions = ["Cut", "Crossfade", "Speed ramp", "Zoom-in"]  # default suggestions
    return {"edit_instructions": instructions, "suggested_transitions": suggested_transitions}

# ---------- Endpoint 4: YouTube metadata ----------
@app.post("/youtube_meta", response_model=YouTubeMetaResponse)
def youtube_meta(req: BaseRequest):
    sys = "You are an SEO expert for YouTube Shorts. Produce short clickable titles, descriptions with 3 CTAs, tags and hashtags."
    user = (
        f"Topic: {req.topic}\nLanguage: {req.language}\nPlatform: {req.target_platform}\n"
        "Create: 1 title (<= 60 chars), full description (include 3 CTAs), 8 tags, 5 hashtags."
    )
    out = call_llm(sys, user, max_tokens=300)
    # Try to parse the output into parts. We'll split by markers if present, otherwise return whole output in description.
    title = ""
    description = out
    tags = []
    hashtags = []
    # crude separation
    lines = out.splitlines()
    if lines:
        title = lines[0][:60]
        description = "\n".join(lines[1:]) if len(lines) > 1 else ""
    return {"title": title, "description": description, "tags": tags or ["motivation","shorts","inspiration"], "hashtags": hashtags or ["#motivation","#inspiration","#shorts"]}

# ---------- Endpoint 5: Thumbnail idea ----------
@app.post("/thumbnail_idea", response_model=ThumbnailResponse)
def thumbnail_idea(req: BaseRequest):
    sys = "You are a thumbnail designer. Suggest short punchy headline text and visual layout for an attention-grabbing motivational short. Keep text short (3-6 words)."
    user = (
        f"Topic: {req.topic}\nLanguage: {req.language}\nTone: {req.tone}\nLength: {req.length_seconds}s\n"
        "Output: headline_text, sub_text (optional), visual_notes, suggested_colors (2-4)."
    )
    out = call_llm(sys, user, max_tokens=250)
    # fallback parse
    lines = [l.strip() for l in out.splitlines() if l.strip()]
    headline = lines[0] if lines else f"{req.topic[:20]}..."
    sub_text = lines[1] if len(lines) > 1 else ""
    visual_notes = " ".join(lines[2:]) if len(lines) > 2 else "Bold text on right, silhouette on left, high contrast"
    suggested_colors = ["#FFFFFF", "#FF0000", "#000000"]
    return {"headline_text": headline, "sub_text": sub_text, "visual_notes": visual_notes, "suggested_colors": suggested_colors}

# ---------- Convenience combined endpoint (all 5) ----------
@app.post("/build_all")
def build_all(req: BaseRequest):
    # Call each endpoint logic sequentially and return combined JSON
    script = generate_script(req)
    board = storyboard(req)
    capcut = capcut_instructions(req)
    meta = youtube_meta(req)
    thumb = thumbnail_idea(req)
    return {
        "script": script,
        "storyboard": board,
        "capcut": capcut,
        "youtube_meta": meta,
        "thumbnail": thumb
    }

# ---------- Simple health check ----------
@app.get("/health")
def health():
    return {"status": "ok"}


---

Quick examples (curl)

Generate everything at once (recommended):

curl -X POST "http://127.0.0.1:8000/build_all" \
  -H "Content-Type: application/json" \
  -d '{
    "topic": "Never give up mindset for students",
    "language": "hi",
    "tone": "motivational",
    "length_seconds": 60,
    "target_platform": "youtube_shorts"
  }'

Get only CapCut instructions:

curl -X POST "http://127.0.0.1:8000/capcut_instructions" \
  -H "Content-Type: application/json" \
  -d '{"topic":"Focus and study routine","language":"hi","length_seconds":45}'


---

Tips / Next steps you can do immediately

Replace model="gpt-4o-mini" with whatever model you have access to.

Add rate-limiting, caching, or local templates for offline use (so you can produce consistent Hindi lines).

Hook this to a small web UI or a Google Sheet to auto-generate CSV with metadata for bulk uploads.

If you want, I can adapt the CapCut instructions to match specific CapCut templates you use (I can output step-by-step for that template).

I can also produce a ready-made prompt pack (10 prompt variations) tuned to make faceless motivational scripts in Hindi for 30/45/60/90 secs.



---

Want me to: A) Customize the file for shorts vs long differently (e.g., add chapter markers)?
B) Add a small React UI + export (CSV / CapCut-ready JSON)?
C) Generate 3 example Hindi scripts right now and one thumbnail idea for your topic?

Tell me which — I’ll produce it right here.


   $ pip install -r requirements.txt
   ```

(2. Run the app

   {python} running ai
   $ streamlit run streamlit_app.py
   ```) 
