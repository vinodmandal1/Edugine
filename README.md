<!DOCTYPE html>
<html lang="hi">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Ajit's assistant</title>
    <style>
        body {
            font-family: 'Open Sans', Arial, sans-serif;
            background-color: #f0f4f8; /* हल्का नीला बैकग्राउंड */
            color: #333;
            margin: 0;
            padding: 0;
            box-sizing: border-box;
            height: 100vh;
            display: flex;
            flex-direction: column;
        }

        .chat-container {
            display: flex;
            flex-direction: column;
            height: 100%; /* Take up full height */
            overflow: hidden;
        }

        .chat-header {
            background-color: #0077b6;
            color: white;
            padding: 15px;
            font-size: 20px;
            text-align: center;
            font-weight: bold;
            border-bottom: 2px solid #fff;
            flex-shrink: 0;
        }

        .chat-box {
            padding: 20px;
            background-color: #ffffff;
            border-radius: 12px;
            overflow-y: auto;
            flex-grow: 1;
            box-shadow: 0px 4px 8px rgba(0, 0, 0, 0.1);
            margin: 10px;
            max-height: 100%; /* Allow it to fill the screen */
            display: flex;
            flex-direction: column;
        }

        .message {
            padding: 12px 18px;
            border-radius: 12px;
            margin-bottom: 10px;
            max-width: 75%;
            word-wrap: break-word;
            animation: fadeIn 0.3s ease-in-out;
        }

        .user-message {
            background-color: #0084ff;
            color: white;
            align-self: flex-end;
            border: 2px solid #ddd;
        }

        .bot-message {
            background-color: #f1f1f1;
            color: #333;
            align-self: flex-start;
            border: 3px solid #ee82ee;
        }

        .chat-input {
            display: flex;
            padding: 15px;
            background-color: #0077b6;
            position: fixed;
            bottom: 0;
            width: 100%;
            box-sizing: border-box;
            z-index: 100;
        }

        .chat-input input {
            flex: 1;
            padding: 10px;
            border: none;
            border-radius: 8px;
            font-size: 16px;
            outline: none;
            box-shadow: 0px 4px 8px rgba(0, 0, 0, 0.1);
        }

        .chat-input button {
            padding: 10px 20px;
            background-color: #00f2fe;
            color: black;
            border: none;
            border-radius: 8px;
            cursor: pointer;
            font-weight: bold;
            margin-left: 10px;
            transition: background-color 0.3s ease;
        }

        .chat-input button:hover {
            background-color: #00c4cc;
        }

        @media (prefers-color-scheme: dark) {
            body {
                background-color: #121212;
                color: white;
            }

            .chat-header {
                background-color: #1f1f1f;
            }

            .chat-box {
                background-color: #333;
            }

            .message {
                background-color: #444;
            }

            .chat-input {
                background-color: #333;
            }
        }

    </style>
</head>
<body>

<div class="chat-container">
    <div class="chat-header">Ajit's AI for Study📚 🤓</div>
    <div class="chat-box" id="chat-box"></div>
</div>

<div class="chat-input">
    <input type="text" id="user-input" placeholder="Ask anything...">
    <button onclick="sendMessage()">Send</button>
</div>

<script>
    const API_KEY = "AIzaSyAmLESBQD_ZzOVmA1lOEcGlnEVM9jROXLI";  // 🔹 अपना Google Gemini API Key यहाँ डालें

    async function sendMessage() {
        let userInput = document.getElementById("user-input").value.trim();
        if (userInput === "") return;

        let chatBox = document.getElementById("chat-box");

        // ✅ यूजर का मैसेज जोड़ना
        let userMessage = document.createElement("div");
        userMessage.classList.add("message", "user-message");
        userMessage.textContent = userInput;
        chatBox.appendChild(userMessage);

        // ✅ लोडिंग मैसेज दिखाना
        let loadingMessage = document.createElement("div");
        loadingMessage.classList.add("message", "loading");
        loadingMessage.textContent = "सोच रहा हूँ...";
        chatBox.appendChild(loadingMessage);

        chatBox.scrollTop = chatBox.scrollHeight;

        // ✅ Gemini API को कॉल करना
        try {
            let botResponse = await getBotResponse(userInput);
            chatBox.removeChild(loadingMessage);  // ✅ लोडिंग हटाना
            
            let botMessage = document.createElement("div");
            botMessage.classList.add("message", "bot-message");
            chatBox.appendChild(botMessage);

            // ✅ Slow Typing Effect (Text धीरे-धीरे दिखे)
            typeText(botMessage, botResponse);
            
            chatBox.scrollTop = chatBox.scrollHeight;
        } catch (error) {
            console.error("API Error:", error);
            chatBox.removeChild(loadingMessage);  
            
            let errorMessage = document.createElement("div");
            errorMessage.classList.add("message", "bot-message");
            errorMessage.textContent = "मुझे माफ करें, मैं इस समय जवाब देने में असमर्थ हूँ!";
            chatBox.appendChild(errorMessage);
        }

        document.getElementById("user-input").value = "";
    }

    async function getBotResponse(input) {
        let response = await fetch(`https://generativelanguage.googleapis.com/v1beta/models/gemini-pro:generateContent?key=${API_KEY}`, {
            method: "POST",
            headers: {
                "Content-Type": "application/json"
            },
            body: JSON.stringify({
                "contents": [{ "role": "user", "parts": [{ "text": input }] }]
            })
        });

        let data = await response.json();
        
        if (data && data.candidates && data.candidates.length > 0) {
            return data.candidates[0].content.parts[0].text;  
        } else {
            return "मुझे माफ करें, मैं इस प्रश्न का उत्तर नहीं समझ सका!";
        }
    }

    // ✅ Slow Typing Effect
    function typeText(element, text) {
        let index = 0;
        function type() {
            if (index < text.length) {
                element.textContent += text.charAt(index);
                index++;
                setTimeout(type, 10); // स्पीड को एडजस्ट कर सकते हैं
            }
        }
        type();
    }
</script>

</body>
</html>
