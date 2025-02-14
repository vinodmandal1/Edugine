
<html lang="hi">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Ajit's Assistant</title>
    <style>
        body {
            font-family: 'Open Sans', Arial, sans-serif;
            background-color: #f0f4f8;
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
            height: 100%;
            overflow: hidden;
            padding-bottom: 80px;
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
            max-height: 100%;
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
            border: 3px solid red;
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

        /* Updated Image Upload Button Design */
        .image-upload-button {
            position: absolute;
            bottom: 90px;
            right: 20px;
            cursor: pointer;
            font-size: 50px;
            color: #ff9900;
            background-color: transparent;
            border: none;
            outline: none;
            animation: bounce 1s infinite;  /* Animation added for the button */
        }

        .image-upload-button:hover {
            color: #cc7a00;
        }

        .image-upload {
            display: none;
        }

        /* Scrollbar customization */
        ::-webkit-scrollbar {
            width: 8px;
        }

        ::-webkit-scrollbar-thumb {
            background-color: #0077b6;
            border-radius: 4px;
        }

        ::-webkit-scrollbar-track {
            background-color: #f1f1f1;
        }

        @keyframes bounce {
            0%, 100% {
                transform: translateY(0);
            }
            50% {
                transform: translateY(-10px);
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

<!-- Updated Image Upload Button with Animation -->
<button class="image-upload-button" onclick="document.getElementById('image-upload').click()">
    <span>+</span> <!-- Plus sign -->
</button>

<!-- Image Upload Input -->
<input type="file" id="image-upload" class="image-upload" accept="image/*" onchange="handleImageUpload(event)">

<script>
    const API_KEY = " AIzaSyBVPzPyNNXUrXmetRNScg6TgkLNG_9Z4mA";  // 🔹 अपना Google Gemini API Key यहाँ डालें

    // स्टोर चैट इतिहास
    let chatHistory = [];

    async function sendMessage() {
        let userInput = document.getElementById("user-input").value.trim();
        if (userInput === "") return;

        let chatBox = document.getElementById("chat-box");

        // यूजर का मैसेज जोड़ना
        let userMessage = document.createElement("div");
        userMessage.classList.add("message", "user-message");
        userMessage.textContent = userInput;
        chatBox.appendChild(userMessage);

        // लोडिंग मैसेज दिखाना
        let loadingMessage = document.createElement("div");
        loadingMessage.classList.add("message", "loading");
        loadingMessage.textContent = "सोच रहा हूँ...";
        chatBox.appendChild(loadingMessage);

        // चैट बॉक्स को स्क्रॉल डाउन करना
        chatBox.scrollTop = chatBox.scrollHeight;

        // चैट हिस्ट्री में यूज़र का मैसेज जोड़ना
        chatHistory.push({ role: "user", text: userInput });

        // Gemini API को कॉल करना
        try {
            let botResponse = await getBotResponse(chatHistory);
            chatBox.removeChild(loadingMessage);

            let botMessage = document.createElement("div");
            botMessage.classList.add("message", "bot-message");
            chatBox.appendChild(botMessage);

            // धीरे-धीरे टेक्स्ट दिखाना
            typeText(botMessage, botResponse);

            // चैट बॉक्स को स्क्रॉल डाउन करना
            chatBox.scrollTop = chatBox.scrollHeight;
        } catch (error) {
            console.error("API Error:", error);
            chatBox.removeChild(loadingMessage);

            let errorMessage = document.createElement("div");
            errorMessage.classList.add("message", "bot-message");
            errorMessage.textContent = "मुझे माफ करें, मैं इस समय जवाब देने में असमर्थ हूँ!";
            chatBox.appendChild(errorMessage);

            // चैट बॉक्स को स्क्रॉल डाउन करना
            chatBox.scrollTop = chatBox.scrollHeight;
        }

        // इनपुट खाली करना
        document.getElementById("user-input").value = "";
    }

    async function getBotResponse(history) {
        let response = await fetch(`https://generativelanguage.googleapis.com/v1beta/models/gemini-pro:generateContent?key=${API_KEY}`, {
            method: "POST",
            headers: {
                "Content-Type": "application/json"
            },
            body: JSON.stringify({
                "contents": history.map(message => ({
                    "role": message.role,
                    "parts": [{ "text": message.text }]
                }))
            })
        });

        let data = await response.json();

        if (data && data.candidates && data.candidates.length > 0) {
            return data.candidates[0].content.parts[0].text;
        } else {
            return "मुझे माफ करें, मैं इस प्रश्न का उत्तर नहीं समझ सका!";
        }
    }

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

    // Image Upload Handler
    function handleImageUpload(event) {
        const file = event.target.files[0];
        if (!file) return;

        let chatBox = document.getElementById("chat-box");

        // Uploading image notification
        let imageMessage = document.createElement("div");
        imageMessage.classList.add("message", "user-message");
        imageMessage.textContent = "इमेज अपलोड हो गई है। अब कृपया इमेज में क्या चाहिए, वो बताएं।";
        chatBox.appendChild(imageMessage);

        // Process and send image (simulated)
        let imageUrl = URL.createObjectURL(file);
        let imageElement = document.createElement("img");
        imageElement.src = imageUrl;
        imageElement.style.maxWidth = "100%";
        imageElement.style.maxHeight = "300px";
        chatBox.appendChild(imageElement);

        // Asking for comment on image
        let commentMessage = document.createElement("div");
        commentMessage.classList.add("message", "bot-message");
        commentMessage.textContent = "आप इस इमेज से क्या चाहते हैं? कृपया बताएं।";
        chatBox.appendChild(commentMessage);

        chatBox.scrollTop = chatBox.scrollHeight;
    }
</script>

</body>
</html>
