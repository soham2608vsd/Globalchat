<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Global Chat</title>
    <style>
        body {
            font-family: Arial, sans-serif;
            margin: 0;
            background: #f5f5f5;
        }
        header {
            text-align: center;
            padding: 20px;
            background: #4a90e2;
            color: white;
            font-size: 28px;
            font-weight: bold;
        }
        #container {
            width: 90%;
            max-width: 500px;
            margin: 30px auto;
            background: white;
            padding: 20px;
            border-radius: 10px;
            box-shadow: 0 3px 10px rgba(0,0,0,0.1);
        }
        #messages {
            height: 300px;
            overflow-y: scroll;
            border: 1px solid #ccc;
            border-radius: 5px;
            padding: 10px;
            background: #fafafa;
        }
        input, button {
            margin: 8px 0;
            padding: 10px;
            width: 100%;
            font-size: 16px;
        }
        button {
            background: #4a90e2;
            color: white;
            border: none;
            cursor: pointer;
            border-radius: 5px;
        }
        button:hover {
            background: #357ABD;
        }
        #error {
            color: red;
            font-weight: bold;
        }
    </style>
</head>
<body>

<header>🌍 Global Chat</header>

<div id="container">

    <!-- Setup -->
    <div id="setup">
        <label>Name:</label>
        <input type="text" id="name" placeholder="Enter your name">

        <label>Birthday:</label>
        <input type="date" id="birthday">

        <button id="joinBtn">Join Chat</button>
        <p id="error"></p>
    </div>

    <!-- Chat -->
    <div id="chat" style="display:none;">
        <div id="messages"></div>
        <input type="text" id="messageInput" placeholder="Type your message...">
        <button id="sendBtn">Send</button>
    </div>

</div>

<script>
    const setup = document.getElementById('setup');
    const chat = document.getElementById('chat');
    const nameInput = document.getElementById('name');
    const birthdayInput = document.getElementById('birthday');
    const joinBtn = document.getElementById('joinBtn');
    const error = document.getElementById('error');
    const messages = document.getElementById('messages');
    const messageInput = document.getElementById('messageInput');
    const sendBtn = document.getElementById('sendBtn');

    let ws;
    let userName = '';

    joinBtn.addEventListener('click', () => {
        const name = nameInput.value.trim();
        const birthday = birthdayInput.value;

        if (!name || !birthday) {
            error.textContent = 'Please enter both name and birthday.';
            return;
        }

        // Just like your rule
        if (name.toLowerCase() === 'boy') {
            error.textContent = 'Error: This name cannot join the chat.';
            return;
        }

        userName = name;
        error.textContent = '';
        setup.style.display = 'none';
        chat.style.display = 'block';

        // Working WebSocket (Public Echo Test Server)
        ws = new WebSocket('wss://ws.postman-echo.com/raw');

        ws.onopen = () => {
            addMessage(`✔ Welcome, ${userName}! Your birthday is ${birthday}.`);
        };

        ws.onmessage = (event) => {
            addMessage(event.data);
        };

        ws.onclose = () => {
            addMessage('⚠ Disconnected from chat.');
        };
    });

    sendBtn.addEventListener('click', () => {
        const message = messageInput.value.trim();
        if (message && ws.readyState === WebSocket.OPEN) {
            ws.send(`${userName}: ${message}`);
            messageInput.value = '';
        }
    });

    function addMessage(msg) {
        const p = document.createElement('p');
        p.textContent = msg;
        messages.appendChild(p);
        messages.scrollTop = messages.scrollHeight;
    }
</script>

</body>
</html>
