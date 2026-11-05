# Quiz Runner Over a Restricted Network

**Final-year group project · BS Artificial Intelligence · Hazara University, Mansehra · 2025**

Quiz Runner turns teaching material from a PDF into a timed multiple-choice quiz. Teachers generate and preview questions, approve student registration requests, and publish quizzes. Students attempt quizzes from their assigned teacher and can view their scores. The project was built to reduce the manual work of drafting, grading, and recording classroom quizzes.

## Team and individual contribution

**Team:** Khizer Dawood, Muhammad Uzair, and Ramish Khan  
**Supervisor:** Mr. Miraj Gul, Department of CS & IT, Hazara University

**Muhammad Uzair** coordinated the three-person team and contributed to the OpenAI LLM integration, PDF text extraction and processing with PyMuPDF, and Firebase configuration and student and teacher data management. He tested the application with the team and contributed to Chapters 3–5 of the thesis, including system design and testing documentation.

## How it works

1. A teacher uploads a text-based PDF. PyMuPDF extracts the text page by page.
2. The application groups text into chunks and prompts the OpenAI API to return a question, four answer options, and the correct answer in JSON for each selected chunk.
3. The teacher previews the questions and publishes the quiz with a title and an availability window.
4. A registered student submits answers during the availability window. The application compares the selected options with the stored answer key and saves the score in Firestore.
5. The teacher views submissions for the quiz. The student can review previous scores.

## Implementation

| Component | Technology or source |
| --- | --- |
| Application, sign-in, and role routing | Python, Streamlit · [app.py](app.py) |
| Teacher and student workflows | [teacher_dashboard.py](teacher_dashboard.py), [student_dashboard.py](student_dashboard.py) |
| PDF text extraction | PyMuPDF · [utils/pdf_utils.py](utils/pdf_utils.py) |
| Question generation | OpenAI Chat Completions · [utils/mcqs_generator.py](utils/mcqs_generator.py) |
| Authentication and persistence | Firebase Authentication and Cloud Firestore · [utils/auth.py](utils/auth.py), [firebaseConfig.py](firebaseConfig.py), [utils/quiz_db.py](utils/quiz_db.py) |

Firestore stores user profiles, teacher registration requests, published quizzes, and student results in separate collections.

## Scope and limitations

- The title reflects the thesis's restricted-network deployment goal. The public application limits quiz visibility through student–teacher registration and time windows. A same-network or IP allowlist would require a separate deployment control. The current Firebase and OpenAI integrations require outbound connectivity.
- The thesis describes GPT-4 in its design. The public implementation configures `gpt-3.5-turbo` through the legacy OpenAI Python client.
- The extractor uses the PDF text layer. Image-only scans require OCR before upload.
- The question count depends on how many usable text chunks are available. Generated questions and answer keys should be checked against the source before publishing.

## Local setup

The repository declares Python 3.11. Use an isolated environment and a separate Firebase project for development.

```bash
git clone https://github.com/Khizer-khan701/Quiz-Runner-Over-a-Restricted-Network.git
cd Quiz-Runner-Over-a-Restricted-Network
python -m venv .venv
```

Activate the environment with `source .venv/bin/activate` on macOS/Linux or `.venv\Scripts\Activate.ps1` in Windows PowerShell. Then install the dependencies:

```bash
python -m pip install -r requirements.txt
```

1. Enable email/password sign-in and create a Firestore database in your development Firebase project.
2. Configure a fresh Firebase service-account credential under the `[firebase]` section of your local `.streamlit/secrets.toml`. Keep that file private. Set the matching project's Firebase Web API key in the local copy of `utils/auth.py`, where the current code reads it.
3. Set `OPENAI_API_KEY` in your environment or local Streamlit secrets. Live question generation requires access to the model configured in `utils/mcqs_generator.py`.
4. Start the application:

```bash
streamlit run app.py
```

## Testing

Chapter 4 of the thesis documents functional test scenarios. The repository includes tests for authentication, PDF extraction, question generation, Firestore quiz creation, and dashboard behavior in [tests/](tests/).
