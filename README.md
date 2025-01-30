# JarvisAI - Voice Assistant

JarvisAI is a Python-based voice assistant capable of executing various commands such as fetching time and date, sending emails, searching Wikipedia, opening websites, fetching weather updates, and more.

---

## Features

- **Text-to-Speech** using `pyttsx3`
- **Voice Recognition** using `SpeechRecognition`
- **Greeting & Wishing** based on time of day
- **Time & Date Functionality**
- **Wikipedia Search**
- **Web Search** (Google, YouTube, etc.)
- **Email Sending** using `smtplib`
- **Weather Updates**
- **Jokes Function**
- **Taking Screenshots**

---

## Installation

1. Clone the repository:

   ```bash
   git clone https://github.com/your-repo/JarvisAI.git
   cd JarvisAI
   ```

2. Install dependencies:

   ```bash
   pip install -r requirements.txt
   ```

3. Set up the `secrets.py` file for email functionality.

---

## Project Structure

```
JarvisAI/
│── jarvis.py         # Main program
│── secrets.py        # Stores email credentials
│── requirements.txt  # List of dependencies
└── README.md         # Instructions
```

---

## Dependencies

Ensure you have the following Python libraries installed:

```
pyttsx3
SpeechRecognition
wikipedia
requests
datetime
smtplib
```

Install them using:

```bash
pip install -r requirements.txt
```

---

## Usage

Run the assistant using:

```bash
python jarvis.py
```

Jarvis will listen for your commands and respond accordingly.

### Example Commands:

- "What time is it?"
- "Tell me today's date."
- "Search Wikipedia for Python programming."
- "Open YouTube."
- "Send an email."
- "What's the weather like?"
- "Tell me a joke."
- "Take a screenshot."

---

## Configuration

### Email Setup (`secrets.py`)

Create a `secrets.py` file and store your email credentials securely:

```python
EMAIL = "your-email@gmail.com"
PASSWORD = "your-email-password"
```

⚠️ **Important:** Do not share this file. Use environment variables or encryption for security.



