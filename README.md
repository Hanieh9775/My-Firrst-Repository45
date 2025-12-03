import os
from flask import Flask, request, render_template_string
import openai

app = Flask(__name__)

openai.api_key = "YOUR_API_KEY_HERE"

HTML = """
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>AI Text Summarizer</title>
    <style>
        body { font-family: Arial, sans-serif; padding: 40px; background: #f5f5f5; }
        .container { background: white; padding: 30px; width: 700px; margin: auto; border-radius: 12px; box-shadow: 0 0 15px rgba(0,0,0,0.1); }
        textarea { width: 100%; height: 180px; padding: 12px; border: 1px solid #ccc; border-radius: 8px; resize: none; }
        button { padding: 14px 22px; background: #007bff; color: white; border: none; border-radius: 8px; cursor: pointer; }
        button:hover { background: #005ad1; }
        .result { margin-top: 20px; background: #eef5ff; padding: 20px; border-radius: 8px; }
    </style>
</head>
<body>
    <div class="container">
        <h1>AI Text Summarizer</h1>
        <form method="POST">
            <textarea name="text" placeholder="Enter your text here..." required></textarea>
            <br><br>
            <button type="submit">Generate Summary</button>
        </form>

        {% if summary %}
            <div class="result">
                <h3>Summary:</h3>
                <p>{{ summary }}</p>
            </div>
        {% endif %}
    </div>
</body>
</html>
"""

def summarize(text):
    response = openai.ChatCompletion.create(
        model="gpt-4o-mini",
        messages=[
            {"role": "system", "content": "Summarize the text clearly and concisely."},
            {"role": "user", "content": text}
        ]
    )
    return response["choices"][0]["message"]["content"]

@app.route("/", methods=["GET", "POST"])
def home():
    summary = None
    
    if request.method == "POST":
        user_text = request.form["text"]
        summary = summarize(user_text)

    return render_template_string(HTML, summary=summary)

if __name__ == "__main__":
    app.run(debug=True)
