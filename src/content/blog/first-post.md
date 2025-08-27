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




Mollis nunc sed id semper risus in. Convallis a cras semper auctor neque. Diam sit amet nisl suscipit. Lacus viverra vitae congue eu consequat ac felis donec. Egestas integer eget aliquet nibh praesent tristique magna sit amet. Eget magna fermentum iaculis eu non diam. In vitae turpis massa sed elementum. Tristique et egestas quis ipsum suspendisse ultrices. Eget lorem dolor sed viverra ipsum. Vel turpis nunc eget lorem dolor sed viverra. Posuere ac ut consequat semper viverra nam. Laoreet suspendisse interdum consectetur libero id faucibus. Diam phasellus vestibulum lorem sed risus ultricies tristique. Rhoncus dolor purus non enim praesent elementum facilisis. Ultrices tincidunt arcu non sodales neque. Tempus egestas sed sed risus pretium quam vulputate. Viverra suspendisse potenti nullam ac tortor vitae purus faucibus ornare. Fringilla urna porttitor rhoncus dolor purus non. Amet dictum sit amet justo donec enim.

Mattis ullamcorper velit sed ullamcorper morbi tincidunt. Tortor posuere ac ut consequat semper viverra. Tellus mauris a diam maecenas sed enim ut sem viverra. Venenatis urna cursus eget nunc scelerisque viverra mauris in. Arcu ac tortor dignissim convallis aenean et tortor at. Curabitur gravida arcu ac tortor dignissim convallis aenean et tortor. Egestas tellus rutrum tellus pellentesque eu. Fusce ut placerat orci nulla pellentesque dignissim enim sit amet. Ut enim blandit volutpat maecenas volutpat blandit aliquam etiam. Id donec ultrices tincidunt arcu. Id cursus metus aliquam eleifend mi.

Tempus quam pellentesque nec nam aliquam sem. Risus at ultrices mi tempus imperdiet. Id porta nibh venenatis cras sed felis eget velit. Ipsum a arcu cursus vitae. Facilisis magna etiam tempor orci eu lobortis elementum. Tincidunt dui ut ornare lectus sit. Quisque non tellus orci ac. Blandit libero volutpat sed cras. Nec tincidunt praesent semper feugiat nibh sed pulvinar proin gravida. Egestas integer eget aliquet nibh praesent tristique magna.
