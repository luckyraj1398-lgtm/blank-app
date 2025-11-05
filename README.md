# 🎈 Blank app template

A simple Streamlit app template for you to modify!

[![Open in Streamlit](https://static.streamlit.io/badges/streamlit_badge_black_white.svg)](https://blank-app-template.streamlit.app/)

### How to run it on your own machine

1. Install the requirements

   ```
   $ pip install -r requirements.txt
   ```
<!DOCTYPE html>
<html lang="en" class="h-full">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>AI Video Studio</title>
    <!-- Load Tailwind CSS -->
    <script src="https://cdn.tailwindcss.com"></script>
    <!-- Use Inter font -->
    <link rel="preconnect" href="https://fonts.googleapis.com">
    <link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
    <link href="https://fonts.googleapis.com/css2?family=Inter:wght@400;500;600;700&display=swap" rel="stylesheet">
    <!-- Add simple icons for buttons -->
    <script src="https://unpkg.com/lucide-react@0.378.0/dist/umd/lucide-react.js"></script>
    <style>
        /* Custom styles */
        body {
            font-family: 'Inter', sans-serif;
            background-color: #111827; /* gray-900 */
            color: #f3f4f6; /* gray-100 */
        }
        /* Custom scrollbar for a modern look */
        ::-webkit-scrollbar {
            width: 8px;
            height: 8px;
        }
        ::-webkit-scrollbar-track {
            background: #1f2937; /* gray-800 */
        }
        ::-webkit-scrollbar-thumb {
            background: #4b5563; /* gray-600 */
            border-radius: 4px;
        }
        ::-webkit-scrollbar-thumb:hover {
            background: #6b7280; /* gray-500 */
        }
        /* Style for the file input button */
        input[type="file"]::file-selector-button {
            @apply bg-indigo-600 text-white font-semibold py-2 px-4 rounded-lg cursor-pointer transition-colors duration-200 hover:bg-indigo-500 mr-4;
        }
        /* Custom modal for messages */
        #message-modal {
            transition: opacity 0.3s ease;
        }
        /* Simple spinner */
        .spinner {
            border: 4px solid rgba(255, 255, 255, 0.3);
            border-radius: 50%;
            border-top: 4px solid #4f46e5; /* indigo-600 */
            width: 40px;
            height: 40px;
            animation: spin 1s linear infinite;
        }
        @keyframes spin {
            0% { transform: rotate(0deg); }
            100% { transform: rotate(360deg); }
        }
    </style>
</head>
<body class="h-full flex flex-col antialiased">

    <!-- Header -->
    <header class="bg-gray-800 shadow-md w-full z-10">
        <div class="max-w-7xl mx-auto px-4 sm:px-6 lg:px-8">
            <div class="flex justify-between items-center h-16">
                <div class="flex items-center">
                    <svg class="h-8 w-8 text-indigo-400" xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"><path d="m16 13 5.223 3.482a.5.5 0 0 0 .777-.416V7.934a.5.5 0 0 0-.777-.416L16 11l-5.223-3.482a.5.5 0 0 0-.777.416v8.132a.5.5 0 0 0 .777.416L16 13z"/><rect x="2" y="6" width="13" height="12" rx="2"/><path d="M12 12h.01"/></svg>
                    <span class="ml-3 text-2xl font-bold text-white">AI Video Studio</span>
                </div>
            </div>
        </div>
    </header>

    <!-- Main Content -->
    <div class="flex-1 flex overflow-hidden">
        <!-- AI Tools Panel (Left) -->
        <nav class="w-64 bg-gray-800/50 p-4 overflow-y-auto space-y-4">
            <h3 class="text-xs font-semibold text-gray-400 uppercase tracking-wider">AI Tools</h3>
            <!-- File Input -->
            <div>
                <label for="file-input" class="block text-sm font-medium text-gray-300 mb-2">Load Video</label>
                <input id="file-input" type="file" accept="video/*" class="block w-full text-sm text-gray-400 file:border-0 file:rounded-lg file:text-sm">
            </div>

            <button id="analyze-frame-btn" class="w-full flex items-center justify-start px-3 py-2 text-sm font-medium rounded-lg bg-gray-700 text-white hover:bg-gray-600 transition-colors duration-200 disabled:opacity-50 disabled:cursor-not-allowed">
                <svg class="lucide-scan-eye h-5 w-5 mr-3" xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"><path d="M3 7V5a2 2 0 0 1 2-2h2"/><path d="M17 3h2a2 2 0 0 1 2 2v2"/><path d="M21 17v2a2 2 0 0 1-2 2h-2"/><path d="M7 21H5a2 2 0 0 1-2-2v-2"/><circle cx="12" cy="12" r="3"/><path d="M21 12c-1.85-4.27-5.3-7-9-7s-7.15 2.73-9 7c1.85 4.27 5.3 7 9 7s7.15-2.73 9-7Z"/></svg>
                Analyze Current Frame
            </button>
            
            <button id="sim-subtitle-btn" class="w-full flex items-center justify-start px-3 py-2 text-sm font-medium rounded-lg bg-gray-700 text-gray-300 hover:bg-gray-600 transition-colors duration-200 disabled:opacity-50 disabled:cursor-not-allowed">
                 <svg class="lucide-subtitles h-5 w-5 mr-3" xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"><path d="M21 12.5a8.5 8.5 0 0 1-17 0V12a8.5 8.5 0 0 1 17 0Z"/><path d="M7 10.5h4"/><path d="M13.5 10.5h4"/><path d="M7 14h4"/><path d="M13.5 14h4"/></svg>
                Auto-Subtitles (Sim)
            </button>
            
            <button id="sim-bg-remove-btn" class="w-full flex items-center justify-start px-3 py-2 text-sm font-medium rounded-lg bg-gray-700 text-gray-300 hover:bg-gray-600 transition-colors duration-200 disabled:opacity-50 disabled:cursor-not-allowed">
                <svg class="lucide-person-standing h-5 w-5 mr-3" xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"><path d="M12 2v10"/><path d="M9 12H5l-1 4 4 2 2-6"/><path d="m15 12 4 0-1 4-4 2-2-6"/><circle cx="12" cy="5" r="1"/><path d="M12 17v5"/></svg>
                BG Removal (Sim)
            </button>
        </nav>

        <!-- Main Player & Analysis (Center) -->
        <main class="flex-1 flex flex-col p-6 overflow-hidden">
            <!-- Video Player -->
            <div class="flex-1 bg-black rounded-lg shadow-lg overflow-hidden flex items-center justify-center relative">
                <video id="video-player" class="w-full h-auto max-h-full" controls>
                    <!-- User will load a video -->
                </video>
                <div id="video-placeholder" class="absolute text-gray-500 text-center p-8">
                    <svg class="lucide-video-off h-24 w-24 mx-auto" xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"><path d="M10.66 6.09 14 8.34l.01.01"/><path d="m2 2 20 20"/><path d="M14 14.34V17a2 2 0 0 1-2 2H4a2 2 0 0 1-2-2V7a2 2 0 0 1 2-2h1.17"/><path d="M16.18 11.4 22 15V7l-3.6 2.4"/></svg>
                    <p class="mt-4 text-lg font-medium">Please load a video using the panel on the left.</p>
                </div>
            </div>
            
            <!-- AI Analysis Output -->
            <div class="h-1/3 mt-6 bg-gray-800 rounded-lg shadow-lg p-4 flex flex-col">
                <h3 class="text-lg font-semibold text-white mb-3">AI Analysis</h3>
                <div id="ai-output" class="flex-1 overflow-y-auto text-gray-300 text-sm leading-relaxed pr-2">
                    <p id="ai-placeholder" class="text-gray-500">Click "Analyze Current Frame" to get AI insights about the video...</p>
                    <div id="ai-loader" class="hidden flex-col items-center justify-center h-full">
                        <div class="spinner"></div>
                        <p class="mt-3 text-gray-400">Analyzing frame with Gemini...</p>
                    </div>
                </div>
            </div>
        </main>
    </div>

    <!-- Hidden canvas for frame capture -->
    <canvas id="frame-canvas" class="hidden"></canvas>

    <!-- Custom Message Modal -->
    <div id="message-modal" class="fixed inset-0 bg-black bg-opacity-75 flex items-center justify-center p-4 z-50 opacity-0 pointer-events-none">
        <div class="bg-gray-800 rounded-lg shadow-xl max-w-sm w-full p-6 text-center">
            <h3 id="modal-title" class="text-lg font-medium text-white mb-4">Feature Simulation</h3>
            <p id="modal-message" class="text-sm text-gray-300 mb-6">This is a demo. In a full app, this feature would process the entire video clip.</p>
            <button id="modal-close-btn" class="bg-indigo-600 text-white font-semibold py-2 px-6 rounded-lg hover:bg-indigo-500 transition-colors duration-200">
                Got it
            </button>
        </div>
    </div>

    <script type="module">
        // --- Firebase/API Imports (Not used here, but showing structure) ---
        // Normally, you'd have API keys and SDKs.
        // For this demo, we'll call the Gemini API directly.

        // --- DOM Elements ---
        const fileInput = document.getElementById('file-input');
        const videoPlayer = document.getElementById('video-player');
        const videoPlaceholder = document.getElementById('video-placeholder');
        const frameCanvas = document.getElementById('frame-canvas');
        const ctx = frameCanvas.getContext('2d');
        
        const analyzeBtn = document.getElementById('analyze-frame-btn');
        const simSubtitleBtn = document.getElementById('sim-subtitle-btn');
        const simBgRemoveBtn = document.getElementById('sim-bg-remove-btn');
        
        const aiOutput = document.getElementById('ai-output');
        const aiPlaceholder = document.getElementById('ai-placeholder');
        const aiLoader = document.getElementById('ai-loader');

        const messageModal = document.getElementById('message-modal');
        const modalTitle = document.getElementById('modal-title');
        const modalMessage = document.getElementById('modal-message');
        const modalCloseBtn = document.getElementById('modal-close-btn');

        let videoLoaded = false;

        // --- Initial State ---
        function updateButtonState() {
            analyzeBtn.disabled = !videoLoaded;
            simSubtitleBtn.disabled = !videoLoaded;
            simBgRemoveBtn.disabled = !videoLoaded;
        }
        updateButtonState();

        // --- Event Listeners ---
        fileInput.addEventListener('change', (event) => {
            const file = event.target.files[0];
            if (file) {
                const videoURL = URL.createObjectURL(file);
                videoPlayer.src = videoURL;
                videoPlaceholder.classList.add('hidden');
                videoPlayer.classList.remove('hidden');
                videoLoaded = true;
                updateButtonState();
            }
        });

        analyzeBtn.addEventListener('click', handleAnalyzeFrame);
        simSubtitleBtn.addEventListener('click', () => showModal('Auto-Subtitles (Simulated)', 'This is a demo. In a full app, this feature would process the video\'s audio and generate a subtitle track (.vtt file).'));
        simBgRemoveBtn.addEventListener('click', () => showModal('Background Removal (Simulated)', 'This is a demo. In a full app, this would use an AI model (like a segmentation model) to process each frame and create a new video with a transparent background (e.g., in WebM format).'));
        modalCloseBtn.addEventListener('click', hideModal);

        // --- Modal Functions ---
        function showModal(title, message) {
            modalTitle.textContent = title;
            modalMessage.textContent = message;
            messageModal.classList.remove('opacity-0', 'pointer-events-none');
        }

        function hideModal() {
            messageModal.classList.add('opacity-0', 'pointer-events-none');
        }

        // --- Core AI Function ---
        async function handleAnalyzeFrame() {
            if (!videoLoaded || videoPlayer.paused) {
                if (!videoLoaded) {
                    showModal('Error', 'Please load a video first.');
                    return;
                }
                if (videoPlayer.paused && videoPlayer.currentTime === 0) {
                     showModal('Error', 'Please play the video a little, then pause it to select a frame.');
                     return;
                }
                // If paused but not at the start, proceed
            }
            
            videoPlayer.pause();
            
            // Set canvas dimensions to video dimensions
            frameCanvas.width = videoPlayer.videoWidth;
            frameCanvas.height = videoPlayer.videoHeight;
            
            // Draw the current video frame onto the canvas
            ctx.drawImage(videoPlayer, 0, 0, frameCanvas.width, frameCanvas.height);
            
            // Get the frame as base64 data (JPEG format)
            // Using JPEG for smaller payload size
            const base64ImageData = frameCanvas.toDataURL('image/jpeg', 0.8).split(',')[1];
            
            if (!base64ImageData) {
                showModal('Error', 'Could not capture video frame.');
                return;
            }

            // Show loader and hide placeholder
            aiPlaceholder.classList.add('hidden');
            aiLoader.classList.remove('hidden');
            aiOutput.innerHTML = ''; // Clear previous results
            aiOutput.appendChild(aiLoader);

            try {
                const aiResponse = await callGeminiApi(base64ImageData);
                
                // Format response as simple HTML
                const formattedResponse = aiResponse
                    .replace(/\*\*(.*?)\*\*/g, '<strong class="font-semibold text-white">$1</strong>') // Bold
                    .replace(/\*(.*?)\*/g, '<em>$1</em>') // Italic
                    .replace(/^- (.*)/gm, '<li class="ml-4 list-disc">$1</li>'); // List items
                
                aiOutput.innerHTML = `<div class="prose prose-invert prose-sm">${formattedResponse}</div>`;

            } catch (error) {
                console.error('Error analyzing frame:', error);
                aiOutput.innerHTML = `<p class="text-red-400">Error: ${error.message}. Check console for details.</p>`;
            } finally {
                // Hide loader in all cases
                aiLoader.classList.add('hidden');
            }
        }

        // --- Gemini API Call Function ---
        async function callGeminiApi(base64ImageData, retryCount = 0) {
            const apiKey = ""; // API key is handled by the environment
            const apiUrl = `https://generativelanguage.googleapis.com/v1beta/models/gemini-2.5-flash-preview-09-2025:generateContent?key=${apiKey}`;
            
            const prompt = "Analyze this video frame. Describe the main subject(s), the setting, and any notable objects or actions. Be concise and descriptive.";

            const payload = {
                contents: [
                    {
                        role: "user",
                        parts: [
                            { text: prompt },
                            {
                                inlineData: {
                                    mimeType: "image/jpeg",
                                    data: base64ImageData
                                }
                            }
                        ]
                    }
                ],
                generationConfig: {
                    "temperature": 0.3,
                    "maxOutputTokens": 1024,
                }
            };

            try {
                const response = await fetch(apiUrl, {
                    method: 'POST',
                    headers: { 'Content-Type': 'application/json' },
                    body: JSON.stringify(payload)
                });

                if (!response.ok) {
                    if (response.status === 429 || response.status >= 500) {
                        // Throttling or server error, implement exponential backoff
                        if (retryCount < 3) {
                            const delay = Math.pow(2, retryCount) * 1000 + Math.random() * 1000;
                            await new Promise(resolve => setTimeout(resolve, delay));
                            return callGeminiApi(base64ImageData, retryCount + 1);
                        } else {
                            throw new Error(`API error after retries: ${response.status} ${response.statusText}`);
                        }
                    }
                    const errorData = await response.json();
                    throw new Error(`API Error: ${errorData?.error?.message || response.statusText}`);
                }

                const result = await response.json();
                
                if (result.candidates && result.candidates[0].content?.parts?.[0]?.text) {
                    return result.candidates[0].content.parts[0].text;
                } else {
                    console.warn('Unexpected API response structure:', result);
                    throw new Error("Could not parse AI response. Check console for details.");
                }
            } catch (error) {
                console.error("Fetch failed:", error);
                throw new Error(`Network or fetch error: ${error.message}`);
            }
        }

    </script>
</body>
</html>


2. Run the app

   ```
   $ streamlit run streamlit_app.py
   ```
