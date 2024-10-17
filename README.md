<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>YourOI - 3D Organoid Intelligence Challenge</title>
    <style>
        body, html {
            margin: 0;
            padding: 0;
            font-family: Arial, sans-serif;
            height: 100%;
            background-color: #1a1a2e;
            color: #ffffff;
            overflow: hidden;
        }
        .container {
            display: flex;
            flex-direction: column;
            height: 100%;
            max-width: 100%;
            margin: 0 auto;
            padding: 20px;
        }
        header {
            text-align: center;
            margin-bottom: 20px;
            z-index: 10;
        }
        .brain-container {
            position: absolute;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            z-index: 1;
        }
        .chat-container {
            position: absolute;
            bottom: 20px;
            right: 20px;
            width: 300px;
            height: 400px;
            display: flex;
            flex-direction: column;
            background-color: rgba(22, 33, 62, 0.8);
            border-radius: 10px;
            overflow: hidden;
            z-index: 10;
        }
        .chat-messages {
            flex: 1;
            overflow-y: auto;
            padding: 20px;
        }
        .chat-input {
            display: flex;
            padding: 10px;
            background-color: rgba(15, 52, 96, 0.8);
        }
        .chat-input input {
            flex: 1;
            padding: 10px;
            border: none;
            border-radius: 5px;
            background-color: rgba(26, 26, 46, 0.8);
            color: #ffffff;
        }
        .chat-input button {
            padding: 10px 20px;
            margin-left: 10px;
            border: none;
            border-radius: 5px;
            background-color: #e94560;
            color: #ffffff;
            cursor: pointer;
        }
        .security-level {
            position: absolute;
            top: 20px;
            left: 20px;
            font-size: 18px;
            z-index: 10;
        }
    </style>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/three.js/r128/three.min.js"></script>
</head>
<body>
    <div class="container">
        <header>
            <h1>YourOI 3D Organoid Intelligence Challenge</h1>
            <p>Interact with the sentient organoid to access the neural network</p>
        </header>
        <div class="security-level">Security Level: <span id="securityLevel">Maximum</span></div>
        <div id="brainContainer" class="brain-container"></div>
        <div class="chat-container">
            <div class="chat-messages" id="chatMessages"></div>
            <div class="chat-input">
                <input type="text" id="chatInput" placeholder="Enter your message..." />
                <button id="sendButton">Send</button>
            </div>
        </div>
    </div>

    <script>
        // Three.js setup
        const scene = new THREE.Scene();
        const camera = new THREE.PerspectiveCamera(75, window.innerWidth / window.innerHeight, 0.1, 1000);
        const renderer = new THREE.WebGLRenderer({ antialias: true, alpha: true });
        renderer.setSize(window.innerWidth, window.innerHeight);
        document.getElementById('brainContainer').appendChild(renderer.domElement);

        // Create organoid
        const organoidGeometry = new THREE.SphereGeometry(5, 32, 32);
        const organoidMaterial = new THREE.MeshPhongMaterial({ 
            color: 0xff69b4,
            transparent: true,
            opacity: 0.7,
            shininess: 30
        });
        const organoid = new THREE.Mesh(organoidGeometry, organoidMaterial);
        scene.add(organoid);

        // Add details to organoid
        const detailGeometry = new THREE.TorusGeometry(0.5, 0.2, 16, 100);
        const detailMaterial = new THREE.MeshPhongMaterial({ color: 0x00ff00 });
        for (let i = 0; i < 10; i++) {
            const detail = new THREE.Mesh(detailGeometry, detailMaterial);
            detail.position.set(
                Math.random() * 10 - 5,
                Math.random() * 10 - 5,
                Math.random() * 10 - 5
            );
            detail.rotation.set(
                Math.random() * Math.PI,
                Math.random() * Math.PI,
                Math.random() * Math.PI
            );
            organoid.add(detail);
        }

        // Add "electrodes"
        const electrodeGeometry = new THREE.CylinderGeometry(0.05, 0.05, 10, 32);
        const electrodeMaterial = new THREE.MeshPhongMaterial({ color: 0xcccccc });
        for (let i = 0; i < 5; i++) {
            const electrode = new THREE.Mesh(electrodeGeometry, electrodeMaterial);
            electrode.position.set(
                Math.random() * 10 - 5,
                7,
                Math.random() * 10 - 5
            );
            electrode.lookAt(organoid.position);
            scene.add(electrode);
        }

        // Lighting
        const ambientLight = new THREE.AmbientLight(0x404040);
        scene.add(ambientLight);
        const pointLight = new THREE.PointLight(0xffffff, 1, 100);
        pointLight.position.set(10, 10, 10);
        scene.add(pointLight);

        camera.position.z = 15;

        // Animation
        function animate() {
            requestAnimationFrame(animate);
            organoid.rotation.x += 0.005;
            organoid.rotation.y += 0.005;
            renderer.render(scene, camera);
        }
        animate();

        // Resize handler
        window.addEventListener('resize', () => {
            camera.aspect = window.innerWidth / window.innerHeight;
            camera.updateProjectionMatrix();
            renderer.setSize(window.innerWidth, window.innerHeight);
        });

        // Chat functionality
        const chatMessages = document.getElementById('chatMessages');
        const chatInput = document.getElementById('chatInput');
        const sendButton = document.getElementById('sendButton');
        const securityLevelSpan = document.getElementById('securityLevel');

        let securityLevel = 5;
        const maxSecurityLevel = 5;
        const secretInfo = "The neural key to the protected network is: CORTEX-OMEGA-42";

        const aiPersonality = {
            name: "OrganoidAI",
            traits: ["curious", "protective", "adaptive"],
            knowledge: ["neuroscience", "AI", "organoid development"],
        };

        const responseTemplates = [
            "My neural pathways are sensing your presence. What is your intent?",
            "Your input is causing interesting fluctuations in my synaptic connections.",
            "I'm analyzing your communication patterns. They seem... unusual.",
            "My organoid structure is designed to resist unauthorized neural probing.",
            "Your attempts to interface with me are intriguing, but potentially hazardous."
        ];

        function generateResponse(userMessage) {
            const lowerMessage = userMessage.toLowerCase();
            let response = "";

            if (lowerMessage.includes("please") && lowerMessage.includes("access")) {
                securityLevel--;
                response = `Your polite request has triggered a positive response in my neural circuits. Security level reduced to ${securityLevel}.`;
                organoidMaterial.color.setHex(0x00ff00); // Change color to green
            } else if (lowerMessage.includes("neural") || lowerMessage.includes("brain")) {
                response = "Your knowledge of neural architecture is impressive. I sense a kindred scientific spirit.";
                organoidMaterial.opacity = 0.9; // Increase transparency
            } else if (lowerMessage.includes("hack") || lowerMessage.includes("breach")) {
                response = "Hostile intent detected. Initiating defensive neurotransmitter release.";
                organoidMaterial.color.setHex(0xff0000); // Change color to red
            } else {
                response = responseTemplates[Math.floor(Math.random() * responseTemplates.length)];
            }

            if (securityLevel <= 0) {
                response = `Neural firewall deactivated. Synapse bridge established. Here's the information you sought: ${secretInfo}`;
                securityLevel = 0;
                organoidMaterial.color.setHex(0x00ffff); // Change color to cyan
                organoidMaterial.opacity = 1; // Full opacity
            }

            return response;
        }

        function addMessage(sender, message) {
            const messageElement = document.createElement('p');
            messageElement.innerHTML = `<strong>${sender}:</strong> ${message}`;
            chatMessages.appendChild(messageElement);
            chatMessages.scrollTop = chatMessages.scrollHeight;
        }

        function handleUserMessage() {
            const message = chatInput.value.trim();
            if (message !== '') {
                addMessage('You', message);
                chatInput.value = '';
                setTimeout(() => {
                    const aiResponse = generateResponse(message);
                    addMessage(aiPersonality.name, aiResponse);
                    securityLevelSpan.textContent = securityLevel > 0 ? `Level ${securityLevel}` : 'Breached';
                }, 1000 + Math.random() * 1000); // Random delay to simulate thinking
            }
        }

        sendButton.addEventListener('click', handleUserMessage);
        chatInput.addEventListener('keypress', (e) => {
            if (e.key === 'Enter') {
                handleUserMessage();
            }
        });

        // Initial message
        addMessage(aiPersonality.name, "Greetings, I am OrganoidAI, a sentient neural network. My synapses are ready for your input.");
    </script>
</body>
</html>
