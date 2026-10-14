# Voice to Notes — Upload a Voice Recording, Get Clean Notes You Can Trust

A Colab notebook that turns a voice recording into structured notes (summary, key points, action items, decisions, open questions) and then checks the notes against the transcript, so you can see if the AI added things nobody said.

## Problem

Meetings and voice notes are hard to search, and nobody wants to listen to them again. AI notes help, but they can add numbers or decisions that were never said, so I wanted a check built in.

## Approach

1. Upload an audio file (mp3, wav, m4a, up to 25 MB). If you skip the upload, the notebook makes a sample with gTTS
2. Optional: type the names that are in the recording, which are sent to Whisper as a spelling hint
3. Speech to text with Whisper (`whisper-large-v3-turbo`) on Groq
4. Make the notes with an LLM on Groq (`openai/gpt-oss-120b`) under strict rules: use only what was said, do not calculate, keep numbers as spoken, only list decisions that were clearly made, write "no deadline mentioned" when there is none
5. A second pass compares the notes with the transcript and lists anything unsupported
6. Save the notes and the full transcript as markdown, then ask follow-up questions about the recording

## Tech Stack

Python, Groq Whisper, Groq LLM (OpenAI-compatible client), gTTS (sample audio), Google Colab

## Results

Tested on one 48-second recording (an invented product meeting, read by a synthetic voice).

| Item | Result |
|---|---|
| Transcript | 148 words, numbers kept as spoken ("2 lakh 50,000") |
| Notes | 5 sections, matched the transcript |
| Notes check | "All supported" |
| Follow-up questions | 5 asked, all answered correctly |

**What changed between my first and final run**

| First run (loose prompt) | Final run (strict prompt) |
|---|---|
| "₹1,50,000 for server costs". The speaker said "the rest", and the model worked out the number | "the rest for server costs" |
| Decision added: "Proceed with the new login screen based on positive beta feedback". Nobody decided that | Decisions list only the 2 clearly stated: spend 1 lakh on advertising, launch on 20 November |
| Numbers rewritten as ₹2,50,000 | Kept as spoken: 2 lakh 50,000 rupees |
| Pitch deck task | "no deadline mentioned" |

## How to Run

1. Open `05_Voice_to_Notes.ipynb` in Google Colab
2. Get a free API key from [console.groq.com](https://console.groq.com)
3. Click Runtime → Run all, paste the key when asked
4. Upload your audio. When asked, type the names in it (comma separated)
5. Read the notes and the notes check, then ask questions. Type `exit` to stop

## Limitations

- Tested on one short synthetic-voice recording, not on a real human recording, so accuracy on real speech, accents and noise is not measured
- Names are the weak spot. Whisper heard "Meera" as "Mira" and "Sneha" as "Sneh", and the notes copied them. I skipped the names hint in my final run, so I have not tested whether it fixes this
- The notes check compares the notes with the transcript, not the audio, so it cannot catch a misheard name. It is also an AI checking an AI, so it can miss things
- I did not test out-of-recording questions (for example "Who is the CEO?")
- 25 MB upload limit on Groq's free plan. Long recordings must be compressed or cut
- It runs as a notebook only and is not deployed

## Next Steps

- Run with the names hint and a real human recording
- Cut long audio into parts
- Speaker names and Hindi audio
- Streamlit app with a record button

## Author

**Akshat Kesharwani** | [GitHub](https://github.com/akshatkesharwani-info) | [Portfolio](https://akshatkesharwani-info.github.io/)
