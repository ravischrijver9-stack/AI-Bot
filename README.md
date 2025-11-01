<html lang="nl">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />
  <title>IVAR — Jouw AI Chatbot</title>
  <script src="https://cdn.tailwindcss.com"></script>
  <style>
    body { font-family: sans-serif; background-color: #f4f7f9; }
    .chat-bubble { padding: 0.75rem; margin: 0.5rem 0; border-radius: 0.5rem; max-width: 80%; }
    .user { background-color: #2563eb; color: white; align-self: flex-end; }
    .bot { background-color: #e5e7eb; color: #111827; align-self: flex-start; }
  </style>
</head>
<body class="p-6">
  <div class="max-w-xl mx-auto bg-white p-6 rounded shadow flex flex-col space-y-4">
    <h1 class="text-2xl font-bold text-center text-indigo-700">IVAR — Jouw AI Chatbot</h1>
    <div id="chat" class="flex flex-col space-y-2 overflow-y-auto h-96 border p-4 rounded bg-gray-50"></div>
    <div class="flex space-x-2">
      <input id="input" type="text" placeholder="Typ je vraag..." class="flex-1 border p-2 rounded" />
      <button id="send" class="bg-indigo-600 text-white px-4 py-2 rounded">Verstuur</button>
    </div>
  </div>

  <script>
    const API_KEY = "AIzaSyDYCpPo7jRa8vJDbH3P5R1eopFo5koGPAo"; // ← Jouw sleutel
    const API_URL = "https://generativelanguage.googleapis.com/v1beta/models/gemini-2.5-flash-preview-09-2025:generateContent?key=";

    const chat = document.getElementById("chat");
    const input = document.getElementById("input");
    const send = document.getElementById("send");

    let ivarMemory = [];

    function addMessage(text, sender) {
      const bubble = document.createElement("div");
      bubble.className = `chat-bubble ${sender}`;
      bubble.textContent = text;
      chat.appendChild(bubble);
      chat.scrollTop = chat.scrollHeight;
    }

    async function fetchIVARReply(message) {
      ivarMemory.push(message);
      const context = ivarMemory.slice(0, -1).join(" | ");
      const prompt = `
Je bent IVAR, een slimme AI-chatbot die met Ravi praat. Je onthoudt wat hij eerder heeft gezegd: ${context}.
Reageer vriendelijk, slim en creatief. Gebruik hedendaags Nederlands. Geef altijd een inhoudelijk en persoonlijk antwoord.
Vraag door als iets onduidelijk is.
Gebruiker zegt: "${message}"
      `.trim();

      const response = await fetch(API_URL + API_KEY, {
        method: "POST",
        headers: { "Content-Type": "application/json" },
        body: JSON.stringify({
          contents: [{ role: "user", parts: [{ text: prompt }] }]
        })
      });
      const result = await response.json();
      return result.candidates?.[0]?.content?.parts?.[0]?.text || "IVAR heeft even geen antwoord.";
    }

    send.addEventListener("click", async () => {
      const text = input.value.trim();
      if (!text) return;
      addMessage(text, "user");
      input.value = "";
      const reply = await fetchIVARReply(text);
      addMessage(reply, "bot");
    });

    input.addEventListener("keydown", e => {
      if (e.key === "Enter") send.click();
    });
  </script>
</body>
</html>
