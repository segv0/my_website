---
title: "LIT-CTF 2025 WriteUp"
description: "Some solutions of remarkable challenges from LIT-CTF"
pubDate: "Aug 27 2025"
heroImage: "/LIT_logo.png"
---

The LIT-CTF 2025 competition featured a diverse set of challenges spanning multiple categories, including web exploitation, cryptography, reverse engineering, and forensics. Each task was carefully designed to test not only technical proficiency but also creativity and problem-solving under pressure.

# 1) Web challenge:     web/group chat 2 |  49 solves / 198 points

In this challenge we are given a "main.py" file that we can run it on our local machine. And we examine the source code:

from flask import Flask, render_template, render_template_string, request, session, redirect, url_for
from flask_socketio import SocketIO, send
import os

app = Flask(__name__)
app.config['SECRET_KEY'] = os.urandom(24)
socketio = SocketIO(app)

chat_logs = []  # Store chat messages in memory

@app.route('/')
def index():
    if 'username' not in session:
        return redirect(url_for('set_username'))
    html = '''
...
<div id="chat-box">''' + '<br>'.join(chat_logs) + '''
</div>
...
'''
    return render_template_string(html)

@app.route('/set_username', methods=['GET', 'POST'])
def set_username():
    if request.method == 'POST':
        if len(request.form['username']) > 14:
            return redirect(url_for('set_username'))
        if request.form['username'].count('{') and request.form['username'].count('}'):
            return redirect(url_for('set_username'))
        session['username'] = request.form['username']
        return redirect(url_for('index'))
    html = '''
... (HTML omitted) ...
'''
    return render_template_string(html)

@app.route('/send_message', methods=['POST'])
def send_message():
    if 'username' not in session:
        return redirect(url_for('set_username'))
    msg = request.form['message']
    username = session.get('username', 'Guest')
    if not msg.isalnum():
        return redirect(url_for('index'))
    chat_message = username + ': ' + msg
    chat_logs.append(chat_message)
    return redirect(url_for('index'))

if __name__ == '__main__':
    socketio.run(app, debug=False)




## The application is vulnerable because it uses render_template_string with untrusted user input. Chat messages are concatenated into an HTML string and then passed to Jinja2, which interprets any template syntax like {{ ... }}.
