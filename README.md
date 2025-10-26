# quantum-melodies-1
a music generator which links AI and quantum computing
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Quantum Melodies - AI Music Generator</title>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/tone/14.8.49/Tone.js"></script>
    <style>
        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
        }
        body {
            font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, Oxygen, Ubuntu, Cantarell, sans-serif;
            background: #0a0e27;
            min-height: 100vh;
            color: white;
            overflow-x: hidden;
        }

        /* Animated Background */
        .bg-animation {
            position: fixed;
            width: 100%;
            height: 100%;
            top: 0;
            left: 0;
            z-index: 0;
            background: linear-gradient(135deg, #667eea 0%, #764ba2 25%, #f093fb 50%, #4facfe 75%, #00f2fe 100%);
            background-size: 400% 400%;
            animation: gradientShift 15s ease infinite;
        }

        @keyframes gradientShift {
            0% { background-position: 0% 50%; }
            50% { background-position: 100% 50%; }
            100% { background-position: 0% 50%; }
        }

        .particles {
            position: fixed;
            width: 100%;
            height: 100%;
            top: 0;
            left: 0;
            z-index: 1;
            pointer-events: none;
        }

        .particle {
            position: absolute;
            width: 4px;
            height: 4px;
            background: rgba(255, 255, 255, 0.6);
            border-radius: 50%;
            animation: float 20s infinite;
        }

        @keyframes float {
            0%, 100% { transform: translateY(0) translateX(0); opacity: 0; }
            10% { opacity: 1; }
            90% { opacity: 1; }
            100% { transform: translateY(-100vh) translateX(100px); opacity: 0; }
        }

        /* Musical Notes Floating */
        .musical-notes {
            position: fixed;
            width: 100%;
            height: 100%;
            top: 0;
            left: 0;
            z-index: 1;
            pointer-events: none;
            overflow: hidden;
        }

        .music-note {
            position: absolute;
            font-size: 2rem;
            opacity: 0;
            animation: floatNote 8s ease-in-out infinite;
        }

        @keyframes floatNote {
            0% {
                transform: translateY(100vh) translateX(0) rotate(0deg);
                opacity: 0;
            }
            10% {
                opacity: 0.8;
            }
            50% {
                opacity: 0.6;
            }
            90% {
                opacity: 0.3;
            }
            100% {
                transform: translateY(-100px) translateX(200px) rotate(360deg);
                opacity: 0;
            }
        }

        /* Glitter Effect */
        .glitter {
            position: absolute;
            width: 3px;
            height: 3px;
            background: white;
            border-radius: 50%;
            box-shadow: 0 0 8px 2px rgba(255, 255, 255, 0.8);
            animation: glitterSparkle 2s ease-in-out infinite;
        }

        @keyframes glitterSparkle {
            0%, 100% {
                opacity: 0;
                transform: scale(0);
            }
            50% {
                opacity: 1;
                transform: scale(1.5);
            }
        }

        /* Singing Indicator */
        .singing-indicator {
            display: none;
            align-items: center;
            justify-content: center;
            gap: 0.5rem;
            margin: 1rem 0;
            padding: 1rem;
            background: rgba(167, 139, 250, 0.2);
            border-radius: 1rem;
            border: 2px solid rgba(167, 139, 250, 0.4);
        }

        .singing-indicator.active {
            display: flex;
        }

        .voice-wave {
            width: 4px;
            height: 20px;
            background: linear-gradient(180deg, #a78bfa 0%, #ec4899 100%);
            border-radius: 2px;
            animation: voiceWave 0.6s ease-in-out infinite;
        }

        .voice-wave:nth-child(2) { animation-delay: 0.1s; }
        .voice-wave:nth-child(3) { animation-delay: 0.2s; }
        .voice-wave:nth-child(4) { animation-delay: 0.3s; }
        .voice-wave:nth-child(5) { animation-delay: 0.4s; }

        @keyframes voiceWave {
            0%, 100% { height: 20px; }
            50% { height: 45px; }
        }

        .container {
            position: relative;
            z-index: 2;
            max-width: 1400px;
            margin: 0 auto;
            padding: 2rem;
        }

        .header {
            text-align: center;
            margin-bottom: 3rem;
            animation: fadeInDown 1s ease;
        }

        @keyframes fadeInDown {
            from { opacity: 0; transform: translateY(-30px); }
            to { opacity: 1; transform: translateY(0); }
        }

        .header-content {
            display: flex;
            align-items: center;
            justify-content: center;
            gap: 1.5rem;
            margin-bottom: 1rem;
        }

        .logo-wrapper {
            position: relative;
            width: 80px;
            height: 80px;
        }

        .logo-ring {
            position: absolute;
            width: 100%;
            height: 100%;
            border: 3px solid rgba(255, 255, 255, 0.3);
            border-radius: 50%;
            animation: pulse-ring 2s ease-in-out infinite;
        }

        .logo-ring:nth-child(2) {
            animation-delay: 0.5s;
        }

        @keyframes pulse-ring {
            0% { transform: scale(1); opacity: 1; }
            100% { transform: scale(1.4); opacity: 0; }
        }

        .header h1 {
            font-size: 3.5rem;
            background: linear-gradient(135deg, #fff 0%, #a78bfa 50%, #ec4899 100%);
            -webkit-background-clip: text;
            -webkit-text-fill-color: transparent;
            background-clip: text;
            font-weight: 800;
            letter-spacing: -1px;
        }

        .header p {
            font-size: 1.25rem;
            color: rgba(255, 255, 255, 0.9);
            text-shadow: 0 2px 10px rgba(0, 0, 0, 0.3);
        }

        .icon {
            width: 60px;
            height: 60px;
            color: #fff;
            filter: drop-shadow(0 0 20px rgba(167, 139, 250, 0.6));
            animation: float-icon 3s ease-in-out infinite;
        }

        @keyframes float-icon {
            0%, 100% { transform: translateY(0); }
            50% { transform: translateY(-10px); }
        }

        .card {
            background: rgba(255, 255, 255, 0.1);
            backdrop-filter: blur(20px);
            border-radius: 1.5rem;
            padding: 2.5rem;
            margin-bottom: 2rem;
            box-shadow: 0 20px 60px rgba(0, 0, 0, 0.3);
            border: 1px solid rgba(255, 255, 255, 0.2);
            animation: fadeInUp 1s ease;
            transition: transform 0.3s ease;
        }

        .card:hover {
            transform: translateY(-5px);
        }

        @keyframes fadeInUp {
            from { opacity: 0; transform: translateY(30px); }
            to { opacity: 1; transform: translateY(0); }
        }

        .form-group {
            margin-bottom: 1.5rem;
        }

        label {
            display: block;
            font-size: 0.875rem;
            font-weight: 600;
            margin-bottom: 0.5rem;
            color: rgba(255, 255, 255, 0.95);
            text-transform: uppercase;
            letter-spacing: 0.5px;
        }

        textarea, select, input[type="number"], input[type="text"] {
            width: 100%;
            padding: 0.875rem 1.25rem;
            background: rgba(255, 255, 255, 0.15);
            border: 2px solid rgba(255, 255, 255, 0.2);
            border-radius: 0.75rem;
            color: white;
            font-size: 1rem;
            font-family: inherit;
            transition: all 0.3s ease;
        }

        textarea {
            resize: vertical;
            min-height: 120px;
        }

        textarea::placeholder, input::placeholder {
            color: rgba(255, 255, 255, 0.6);
        }

        textarea:focus, select:focus, input:focus {
            outline: none;
            border-color: #a78bfa;
            background: rgba(255, 255, 255, 0.2);
            box-shadow: 0 0 0 4px rgba(167, 139, 250, 0.2);
        }

        .grid {
            display: grid;
            grid-template-columns: repeat(auto-fit, minmax(200px, 1fr));
            gap: 1.25rem;
        }

        .checkbox-group {
            display: flex;
            align-items: center;
            gap: 0.75rem;
            padding: 0.875rem 1.25rem;
            background: rgba(255, 255, 255, 0.15);
            border: 2px solid rgba(255, 255, 255, 0.2);
            border-radius: 0.75rem;
            cursor: pointer;
            transition: all 0.3s ease;
        }

        .checkbox-group:hover {
            background: rgba(255, 255, 255, 0.2);
            border-color: #a78bfa;
        }

        .checkbox-group input[type="checkbox"] {
            width: 24px;
            height: 24px;
            cursor: pointer;
            accent-color: #a78bfa;
        }

        .checkbox-group label {
            margin: 0;
            cursor: pointer;
            font-size: 0.95rem;
        }

        .btn {
            width: 100%;
            padding: 1.25rem;
            font-size: 1.125rem;
            font-weight: 700;
            border: none;
            border-radius: 0.75rem;
            cursor: pointer;
            display: flex;
            align-items: center;
            justify-content: center;
            gap: 0.75rem;
            transition: all 0.3s ease;
            text-transform: uppercase;
            letter-spacing: 1px;
        }

        .btn-primary {
            background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
            color: white;
            box-shadow: 0 10px 30px rgba(102, 126, 234, 0.4);
        }

        .btn-primary:hover:not(:disabled) {
            transform: translateY(-3px);
            box-shadow: 0 15px 40px rgba(102, 126, 234, 0.6);
        }

        .btn-primary:disabled {
            opacity: 0.6;
            cursor: not-allowed;
        }

        .spinner {
            width: 24px;
            height: 24px;
            border: 3px solid rgba(255, 255, 255, 0.3);
            border-top-color: white;
            border-radius: 50%;
            animation: spin 0.8s linear infinite;
        }

        @keyframes spin {
            to { transform: rotate(360deg); }
        }

        .player-card {
            background: linear-gradient(135deg, rgba(102, 126, 234, 0.3) 0%, rgba(118, 75, 162, 0.3) 100%);
            border: 2px solid rgba(167, 139, 250, 0.4);
        }

        .player-header {
            display: flex;
            justify-content: space-between;
            align-items: center;
            margin-bottom: 1.5rem;
        }

        .player-title h3 {
            font-size: 1.75rem;
            margin-bottom: 0.5rem;
            font-weight: 700;
        }

        .player-meta {
            color: rgba(255, 255, 255, 0.8);
            font-size: 0.95rem;
        }

        .player-controls {
            display: flex;
            gap: 1rem;
        }

        .btn-icon {
            width: 64px;
            height: 64px;
            border-radius: 50%;
            background: rgba(255, 255, 255, 0.2);
            border: 2px solid rgba(255, 255, 255, 0.3);
            cursor: pointer;
            display: flex;
            align-items: center;
            justify-content: center;
            transition: all 0.3s ease;
        }

        .btn-icon:hover {
            background: rgba(255, 255, 255, 0.3);
            transform: scale(1.1);
            box-shadow: 0 8px 25px rgba(167, 139, 250, 0.4);
        }

        .btn-icon svg {
            width: 28px;
            height: 28px;
            color: white;
        }

        .waveform {
            display: flex;
            align-items: center;
            justify-content: center;
            gap: 4px;
            height: 60px;
            margin: 1.5rem 0;
        }

        .bar {
            width: 6px;
            background: linear-gradient(180deg, #a78bfa 0%, #ec4899 100%);
            border-radius: 3px;
            animation: wave 1s ease-in-out infinite;
        }

        .bar:nth-child(2) { animation-delay: 0.1s; }
        .bar:nth-child(3) { animation-delay: 0.2s; }
        .bar:nth-child(4) { animation-delay: 0.3s; }
        .bar:nth-child(5) { animation-delay: 0.4s; }
        .bar:nth-child(6) { animation-delay: 0.5s; }
        .bar:nth-child(7) { animation-delay: 0.6s; }

        @keyframes wave {
            0%, 100% { height: 20px; }
            50% { height: 60px; }
        }

        .lyrics-display {
            background: rgba(0, 0, 0, 0.3);
            border-radius: 1rem;
            padding: 2rem;
            margin-top: 1.5rem;
            max-height: 300px;
            overflow-y: auto;
            text-align: center;
            font-size: 1.1rem;
            line-height: 1.8;
            color: rgba(255, 255, 255, 0.95);
        }

        .lyrics-display::-webkit-scrollbar {
            width: 8px;
        }

        .lyrics-display::-webkit-scrollbar-track {
            background: rgba(255, 255, 255, 0.1);
            border-radius: 4px;
        }

        .lyrics-display::-webkit-scrollbar-thumb {
            background: rgba(167, 139, 250, 0.6);
            border-radius: 4px;
        }

        .track-list {
            margin-top: 1.5rem;
        }

        .track-item {
            background: rgba(255, 255, 255, 0.1);
            border: 1px solid rgba(255, 255, 255, 0.2);
            border-radius: 1rem;
            padding: 1.25rem;
            margin-bottom: 1rem;
            cursor: pointer;
            transition: all 0.3s ease;
            display: flex;
            justify-content: space-between;
            align-items: center;
        }

        .track-item:hover {
            background: rgba(255, 255, 255, 0.2);
            transform: translateX(8px);
            border-color: #a78bfa;
        }

        .track-info h4 {
            font-weight: 700;
            margin-bottom: 0.5rem;
            font-size: 1.1rem;
        }

        .track-info p {
            font-size: 0.9rem;
            color: rgba(255, 255, 255, 0.7);
        }

        .btn-play {
            width: 48px;
            height: 48px;
            border-radius: 50%;
            background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
            border: none;
            cursor: pointer;
            display: flex;
            align-items: center;
            justify-content: center;
            transition: all 0.3s ease;
        }

        .btn-play:hover {
            transform: scale(1.15);
            box-shadow: 0 8px 20px rgba(102, 126, 234, 0.5);
        }

        .btn-play svg {
            width: 20px;
            height: 20px;
            color: white;
        }

        select option {
            background: #1a1f3a;
            color: white;
        }

        .section-title {
            display: flex;
            align-items: center;
            gap: 0.75rem;
            font-size: 1.75rem;
            font-weight: 700;
            margin-bottom: 1.5rem;
        }

        .modal {
            display: none;
            position: fixed;
            z-index: 1000;
            left: 0;
            top: 0;
            width: 100%;
            height: 100%;
            background: rgba(0, 0, 0, 0.8);
            backdrop-filter: blur(10px);
        }

        .modal-content {
            position: relative;
            background: rgba(26, 31, 58, 0.95);
            margin: 10% auto;
            padding: 2.5rem;
            border-radius: 1.5rem;
            width: 90%;
            max-width: 600px;
            border: 2px solid rgba(167, 139, 250, 0.3);
            animation: modalSlideIn 0.4s ease;
        }

        @keyframes modalSlideIn {
            from { opacity: 0; transform: translateY(-50px); }
            to { opacity: 1; transform: translateY(0); }
        }

        .close {
            position: absolute;
            right: 1.5rem;
            top: 1.5rem;
            font-size: 2rem;
            font-weight: bold;
            color: rgba(255, 255, 255, 0.7);
            cursor: pointer;
            transition: color 0.3s ease;
        }

        .close:hover {
            color: #fff;
        }

        .modal h2 {
            margin-bottom: 1.5rem;
            font-size: 1.75rem;
        }

        .option-btn {
            width: 100%;
            padding: 1.25rem;
            margin-bottom: 1rem;
            background: rgba(255, 255, 255, 0.1);
            border: 2px solid rgba(255, 255, 255, 0.2);
            border-radius: 0.75rem;
            color: white;
            cursor: pointer;
            transition: all 0.3s ease;
            font-size: 1.05rem;
            font-weight: 600;
        }

        .option-btn:hover {
            background: rgba(167, 139, 250, 0.3);
            border-color: #a78bfa;
            transform: translateX(5px);
        }

        /* Navigation Bar */
        .navbar {
            position: fixed;
            top: 0;
            left: 0;
            right: 0;
            z-index: 100;
            background: rgba(10, 14, 39, 0.95);
            backdrop-filter: blur(20px);
            border-bottom: 1px solid rgba(255, 255, 255, 0.1);
            padding: 1rem 2rem;
        }

        .navbar-content {
            max-width: 1400px;
            margin: 0 auto;
            display: flex;
            justify-content: space-between;
            align-items: center;
        }

        .navbar-brand {
            font-size: 1.5rem;
            font-weight: 800;
            background: linear-gradient(135deg, #fff 0%, #a78bfa 50%, #ec4899 100%);
            -webkit-background-clip: text;
            -webkit-text-fill-color: transparent;
            cursor: pointer;
        }

        .navbar-menu {
            display: flex;
            gap: 2rem;
            align-items: center;
        }

        .nav-link {
            color: rgba(255, 255, 255, 0.8);
            text-decoration: none;
            font-weight: 600;
            transition: all 0.3s ease;
            cursor: pointer;
        }

        .nav-link:hover {
            color: #a78bfa;
        }

        .login-btn {
            padding: 0.75rem 1.5rem;
            background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
            border: none;
            border-radius: 0.5rem;
            color: white;
            font-weight: 600;
            cursor: pointer;
            transition: all 0.3s ease;
        }

        .login-btn:hover {
            transform: translateY(-2px);
            box-shadow: 0 8px 20px rgba(102, 126, 234, 0.4);
        }

        /* About Section */
        .about-section {
            display: none;
            margin-top: 80px;
        }
        
        .about-section.active {
            display: block;
        }
        
        .about-content {
            display: grid;
            grid-template-columns: 1fr 1fr;
            gap: 2rem;
            margin-bottom: 2rem;
        }
        
        .team-grid {
            display: grid;
            grid-template-columns: repeat(auto-fit, minmax(250px, 1fr));
            gap: 1.5rem;
            margin-top: 2rem;
        }
        
        .team-member {
            background: rgba(255, 255, 255, 0.1);
            backdrop-filter: blur(10px);
            border: 1px solid rgba(255, 255, 255, 0.2);
            border-radius: 1rem;
            padding: 2rem;
            text-align: center;
            transition: all 0.3s ease;
        }
        
        .team-member:hover {
            transform: translateY(-5px);
            border-color: #a78bfa;
        }
        
        .team-avatar {
            width: 100px;
            height: 100px;
            border-radius: 50%;
            background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
            margin: 0 auto 1rem;
            display: flex;
            align-items: center;
            justify-content: center;
            font-size: 2.5rem;
        }
        

        /* Chatbot */
        .chatbot-container {
            position: fixed;
            bottom: 2rem;
            right: 2rem;
            z-index: 1000;
        }

        .chatbot-toggle {
            width: 64px;
            height: 64px;
            border-radius: 50%;
            background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
            border: none;
            cursor: pointer;
            box-shadow: 0 8px 25px rgba(102, 126, 234, 0.5);
            display: flex;
            align-items: center;
            justify-content: center;
            transition: all 0.3s ease;
        }

        .chatbot-toggle:hover {
            transform: scale(1.1);
        }

        .chatbot-toggle svg {
            width: 32px;
            height: 32px;
            color: white;
        }

        .chatbot-window {
            display: none;
            position: absolute;
            bottom: 80px;
            right: 0;
            width: 350px;
            height: 500px;
            background: rgba(26, 31, 58, 0.98);
            backdrop-filter: blur(20px);
            border-radius: 1.5rem;
            border: 2px solid rgba(167, 139, 250, 0.3);
            box-shadow: 0 20px 60px rgba(0, 0, 0, 0.5);
            flex-direction: column;
        }

        .chatbot-window.active {
            display: flex;
        }

        .chatbot-header {
            padding: 1.5rem;
            border-bottom: 1px solid rgba(255, 255, 255, 0.1);
            display: flex;
            justify-content: space-between;
            align-items: center;
        }

        .chatbot-header h3 {
            font-size: 1.25rem;
        }

        .chatbot-close {
            background: none;
            border: none;
            color: white;
            font-size: 1.5rem;
            cursor: pointer;
            opacity: 0.7;
            transition: opacity 0.3s;
        }

        .chatbot-close:hover {
            opacity: 1;
        }

        .chatbot-messages {
            flex: 1;
            overflow-y: auto;
            padding: 1.5rem;
            display: flex;
            flex-direction: column;
            gap: 1rem;
        }

        .chatbot-messages::-webkit-scrollbar {
            width: 6px;
        }

        .chatbot-messages::-webkit-scrollbar-track {
            background: rgba(255, 255, 255, 0.05);
        }

        .chatbot-messages::-webkit-scrollbar-thumb {
            background: rgba(167, 139, 250, 0.5);
            border-radius: 3px;
        }

        .chat-message {
            padding: 1rem;
            border-radius: 1rem;
            max-width: 80%;
            animation: messageSlideIn 0.3s ease;
        }

        @keyframes messageSlideIn {
            from { opacity: 0; transform: translateY(10px); }
            to { opacity: 1; transform: translateY(0); }
        }

        .chat-message.bot {
            background: rgba(102, 126, 234, 0.2);
            border: 1px solid rgba(102, 126, 234, 0.3);
            align-self: flex-start;
        }

        .chat-message.user {
            background: rgba(236, 72, 153, 0.2);
            border: 1px solid rgba(236, 72, 153, 0.3);
            align-self: flex-end;
        }

        .chatbot-input-container {
            padding: 1rem;
            border-top: 1px solid rgba(255, 255, 255, 0.1);
            display: flex;
            gap: 0.5rem;
        }

        .chatbot-input {
            flex: 1;
            padding: 0.75rem;
            background: rgba(255, 255, 255, 0.1);
            border: 1px solid rgba(255, 255, 255, 0.2);
            border-radius: 0.5rem;
            color: white;
            font-size: 0.95rem;
        }

        .chatbot-input:focus {
            outline: none;
            border-color: #a78bfa;
        }

        .chatbot-send {
            padding: 0.75rem 1.25rem;
            background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
            border: none;
            border-radius: 0.5rem;
            color: white;
            cursor: pointer;
            transition: all 0.3s ease;
        }

        .chatbot-send:hover {
            transform: scale(1.05);
        }

        /* Main Section */
        .main-section {
            margin-top: 80px;
        }

        .main-section.hidden {
            display: none;
        }

        /* Voice Options */
        .voice-option-card {
            background: rgba(255, 255, 255, 0.1);
            border: 2px solid rgba(255, 255, 255, 0.2);
            border-radius: 1rem;
            padding: 1.5rem;
            cursor: pointer;
            transition: all 0.3s ease;
        }

        .voice-option-card:hover {
            background: rgba(255, 255, 255, 0.15);
            border-color: #a78bfa;
            transform: translateX(5px);
        }

        .voice-option-card.selected {
            background: rgba(102, 126, 234, 0.3);
            border-color: #667eea;
        }

        .voice-option-header {
            display: flex;
            justify-content: space-between;
            align-items: center;
            margin-bottom: 0.5rem;
        }

        .voice-option-name {
            font-weight: 700;
            font-size: 1.1rem;
        }

        .voice-badge {
            padding: 0.25rem 0.75rem;
            background: rgba(167, 139, 250, 0.3);
            border-radius: 1rem;
            font-size: 0.85rem;
        }

        /* Multi-Select Voice */
        .voice-multi-select {
            display: flex;
            flex-wrap: wrap;
            gap: 0.75rem;
            margin-top: 1rem;
        }

        .voice-tag {
            padding: 0.5rem 1rem;
            background: rgba(167, 139, 250, 0.3);
            border-radius: 1rem;
            font-size: 0.9rem;
            cursor: pointer;
            transition: all 0.3s ease;
        }

        .voice-tag:hover {
            background: rgba(167, 139, 250, 0.5);
        }

        .voice-tag.selected {
            background: rgba(102, 126, 234, 0.6);
        }

        /* Voice Selection Grid */
        .voice-grid {
            display: grid;
            grid-template-columns: repeat(auto-fit, minmax(180px, 1fr));
            gap: 1rem;
            margin-top: 1rem;
        }

        .voice-card {
            background: rgba(255, 255, 255, 0.1);
            border: 2px solid rgba(255, 255, 255, 0.2);
            border-radius: 1rem;
            padding: 1.25rem;
            cursor: pointer;
            transition: all 0.3s ease;
            text-align: center;
        }

        .voice-card:hover {
            background: rgba(255, 255, 255, 0.15);
            border-color: #a78bfa;
            transform: translateY(-3px);
        }

        .voice-card.selected {
            background: rgba(102, 126, 234, 0.3);
            border-color: #667eea;
        }

        .voice-icon {
            font-size: 2rem;
            margin-bottom: 0.5rem;
        }

        .voice-name {
            font-weight: 600;
            font-size: 1rem;
            margin-bottom: 0.25rem;
        }

        .voice-desc {
            font-size: 0.85rem;
            color: rgba(255, 255, 255, 0.7);
        }

        @media (max-width: 768px) {
            .header h1 {
                font-size: 2.25rem;
            }
            .grid {
                grid-template-columns: 1fr;
            }
            .player-header {
                flex-direction: column;
                align-items: flex-start;
                gap: 1.5rem;
            }
            .about-content {
                grid-template-columns: 1fr;
            }
            .navbar-menu {
                gap: 1rem;
            }
            .chatbot-window {
                width: 90vw;
                right: 5vw;
            }
        }
    </style>
</head>
<body>
    <div class="bg-animation"></div>
    <div class="particles" id="particles"></div>
    <div class="musical-notes" id="musicalNotes"></div>

    <!-- Navigation Bar -->
    <nav class="navbar">
        <div class="navbar-content">
            <div class="navbar-brand">🎵 Quantum Melodies</div>
            <div class="navbar-menu">
                <a class="nav-link" onclick="showSection('main')">Home</a>
                <a class="nav-link" onclick="showSection('about')">About</a>
                <button class="login-btn" onclick="openLoginModal()">
                    <svg style="display: inline; width: 20px; height: 20px; vertical-align: middle; margin-right: 0.5rem;" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2">
                        <path d="M20 21v-2a4 4 0 0 0-4-4H8a4 4 0 0 0-4 4v2"></path>
                        <circle cx="12" cy="7" r="4"></circle>
                    </svg>
                    <span id="loginBtnText">Login</span>
                </button>
            </div>
        </div>
    </nav>

    <div class="container">
        <!-- Main Section -->
        <div class="main-section" id="mainSection">
        <!-- Header -->
        <div class="header">
            <div class="header-content">
                <div class="logo-wrapper">
                    <div class="logo-ring"></div>
                    <div class="logo-ring"></div>
                    <svg class="icon" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2">
                        <path d="M9 18V5l12-2v13M9 13l12-2"/>
                        <circle cx="6" cy="18" r="3"/>
                        <circle cx="18" cy="16" r="3"/>
                    </svg>
                </div>
                <h1>Quantum Melodies</h1>
            </div>
            <p>Next-Gen AI Music Generator with Rhythm, Lyrics & Quantum Randomness</p>
        </div>
        <!-- Generation Form -->
        <div class="card">
            <div class="form-group">
                <label for="prompt">Describe Your Music</label>
                <textarea id="prompt" placeholder="e.g., Uplifting electronic melody with dreamy vibes and powerful drops..."></textarea>
            </div>
            <div class="grid">
                <div class="form-group">
                    <label for="genre">Genre</label>
                    <select id="genre">
                        <option value="electronic">Electronic</option>
                        <option value="ambient">Ambient</option>
                        <option value="classical">Classical</option>
                        <option value="jazz">Jazz</option>
                        <option value="hiphop">Hip Hop</option>
                        <option value="rock">Rock</option>
                        <option value="experimental">Experimental</option>
                    </select>
                </div>
                <div class="form-group">
                    <label for="mood">Mood</label>
                    <select id="mood">
                        <option value="uplifting">Uplifting</option>
                        <option value="calm">Calm</option>
                        <option value="energetic">Energetic</option>
                        <option value="dark">Dark</option>
                        <option value="romantic">Romantic</option>
                        <option value="epic">Epic</option>
                    </select>
                </div>
                <div class="form-group">
                    <label for="duration">Duration (seconds)</label>
                    <input type="number" id="duration" value="30" min="10" max="120">
                </div>
                <div class="form-group">
                    <div class="checkbox-group">
                        <input type="checkbox" id="quantumMode">
                        <label for="quantumMode">Quantum Mode</label>
                    </div>
                </div>
            </div>

            <div class="form-group" style="margin-top: 1rem;">
                <label>Voice Style</label>
                <div class="voice-grid">
                    <div class="voice-card selected" onclick="toggleVoice('ethereal')" id="voice-ethereal">
                        <div class="voice-icon">✨</div>
                        <div class="voice-name">Ethereal</div>
                        <div class="voice-desc">Dreamy, airy, and floating</div>
                    </div>
                    <div class="voice-card" onclick="toggleVoice('powerful')" id="voice-powerful">
                        <div class="voice-icon">🔥</div>
                        <div class="voice-name">Powerful</div>
                        <div class="voice-desc">Bold, resonant, and commanding</div>
                    </div>
                    <div class="voice-card" onclick="toggleVoice('smooth')" id="voice-smooth">
                        <div class="voice-icon">🎤</div>
                        <div class="voice-name">Smooth</div>
                        <div class="voice-desc">Rich, warm, and velvety</div>
                    </div>
                    <div class="voice-card" onclick="toggleVoice('robotic')" id="voice-robotic">
                        <div class="voice-icon">🤖</div>
                        <div class="voice-name">Robotic</div>
                        <div class="voice-desc">Electronic, synthetic, and digital</div>
                    </div>
                    <div class="voice-card" onclick="toggleVoice('choir')" id="voice-choir">
                        <div class="voice-icon">👥</div>
                        <div class="voice-name">Choir</div>
                        <div class="voice-desc">Multiple harmonizing voices</div>
                    </div>
                    <div class="voice-card" onclick="openCustomVoiceModal()" id="voice-custom">
                        <div class="voice-icon">⚙️</div>
                        <div class="voice-name">Custom</div>
                        <div class="voice-desc">Design your own voice</div>
                    </div>
                </div>
            </div>

            <div class="form-group" style="margin-top: 1rem;">
                <label>Add Lyrics?</label>
                <div class="grid" style="grid-template-columns: repeat(auto-fit, minmax(150px, 1fr));">
                    <button class="option-btn" onclick="openLyricsModal()">Configure Lyrics</button>
                </div>
            </div>

            <button id="generateBtn" class="btn btn-primary">
                <svg width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2">
                    <polygon points="5 3 19 12 5 21 5 3"></polygon>
                </svg>
                Generate Music
            </button>
        </div>
        <!-- Lyrics Modal -->
        <div id="lyricsModal" class="modal">
            <div class="modal-content">
                <span class="close" onclick="closeLyricsModal()">&times;</span>
                <h2>Lyrics Configuration</h2>

                <button class="option-btn" onclick="selectLyricsOption('ai')">
                    🤖 AI-Generated Lyrics
                </button>
                <button class="option-btn" onclick="selectLyricsOption('manual')">
                    ✍️ Write My Own Lyrics
                </button>
                <button class="option-btn" onclick="selectLyricsOption('record')">
                    🎤 Record My Voice
                </button>
                <button class="option-btn" onclick="selectLyricsOption('none')">
                    🎵 Instrumental Only
                </button>

                <div id="aiLyricsOptions" style="display: none; margin-top: 1.5rem;">
                    <div class="form-group">
                        <label for="lyricsLanguage">Language</label>
                        <select id="lyricsLanguage">
                            <option value="en">English</option>
                            <option value="es">Spanish</option>
                            <option value="fr">French</option>
                            <option value="de">German</option>
                            <option value="it">Italian</option>
                            <option value="pt">Portuguese</option>
                            <option value="hi">Hindi</option>
                            <option value="ja">Japanese</option>
                            <option value="ko">Korean</option>
                            <option value="zh">Chinese</option>
                        </select>
                    </div>
                    <div class="form-group">
                        <label for="lyricsTheme">Theme (Optional)</label>
                        <input type="text" id="lyricsTheme" placeholder="e.g., love, freedom, adventure...">
                    </div>
                    <button class="btn btn-primary" onclick="confirmAILyrics()">Generate AI Lyrics</button>
                </div>

                <div id="manualLyricsOptions" style="display: none; margin-top: 1.5rem;">
                    <div class="form-group">
                        <label for="manualLyrics">Your Lyrics</label>
                        <textarea id="manualLyrics" placeholder="Write your lyrics here..." style="min-height: 200px;"></textarea>
                    </div>
                    <button class="btn btn-primary" onclick="confirmManualLyrics()">Save Lyrics</button>
                </div>

                <!-- Record Voice Modal -->
                <div id="recordVoiceModal" class="modal">
                    <div class="modal-content">
                        <span class="close" onclick="closeRecordVoiceModal()">&times;</span>
                        <h2>Record Your Voice</h2>
                        <p>Click the button below to start recording. Speak or sing into your microphone.</p>

                        <div style="display: flex; gap: 1rem; margin: 1.5rem 0;">
                            <button id="startRecordBtn" class="btn btn-primary" onclick="startRecording()">
                                <svg width="20" height="20" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2">
                                    <circle cx="12" cy="12" r="10"></circle>
                                </svg>
                                Start Recording
                            </button>
                            <button id="stopRecordBtn" class="btn btn-primary" onclick="stopRecording()" disabled>
                                <svg width="20" height="20" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2">
                                    <rect x="6" y="6" width="12" height="12"></rect>
                                </svg>
                                Stop Recording
                            </button>
                        </div>

                        <div style="margin: 1rem 0;">
                            <audio id="recordedAudio" controls style="width: 100%;"></audio>
                        </div>

                        <button class="btn btn-primary" onclick="saveRecordedVoice()">Use This Recording</button>
                    </div>
                </div>
            </div>
        </div>
        <!-- Custom Voice Modal -->
        <div id="customVoiceModal" class="modal">
            <div class="modal-content">
                <span class="close" onclick="closeCustomVoiceModal()">&times;</span>
                <h2>Design Your Custom Voice</h2>

                <div class="form-group">
                    <label for="customOscillator">Oscillator Type</label>
                    <select id="customOscillator">
                        <option value="sine">Sine</option>
                        <option value="square">Square</option>
                        <option value="sawtooth">Sawtooth</option>
                        <option value="triangle">Triangle</option>
                    </select>
                </div>

                <div class="form-group">
                    <label>Envelope</label>
                    <div style="display: grid; grid-template-columns: 1fr 1fr; gap: 1rem;">
                        <div>
                            <label for="customAttack">Attack (s)</label>
                            <input type="range" id="customAttack" min="0.01" max="2" step="0.01" value="0.2">
                            <span id="customAttackValue">0.2</span>
                        </div>
                        <div>
                            <label for="customDecay">Decay (s)</label>
                            <input type="range" id="customDecay" min="0.01" max="2" step="0.01" value="0.3">
                            <span id="customDecayValue">0.3</span>
                        </div>
                        <div>
                            <label for="customSustain">Sustain (0-1)</label>
                            <input type="range" id="customSustain" min="0" max="1" step="0.01" value="0.6">
                            <span id="customSustainValue">0.6</span>
                        </div>
                        <div>
                            <label for="customRelease">Release (s)</label>
                            <input type="range" id="customRelease" min="0.01" max="3" step="0.01" value="1.2">
                            <span id="customReleaseValue">1.2</span>
                        </div>
                    </div>
                </div>

                <div class="form-group">
                    <label for="customReverb">Reverb (0-1)</label>
                    <input type="range" id="customReverb" min="0" max="1" step="0.01" value="0.5">
                    <span id="customReverbValue">0.5</span>
                </div>

                <button class="btn btn-primary" onclick="saveCustomVoice()">Save Custom Voice</button>
            </div>
        </div>
        <!-- Current Track Player -->
        <div id="playerCard" class="card player-card" style="display: none;">
            <div class="player-header">
                <div class="player-title">
                    <h3 id="trackTitle">Track Title</h3>
                    <p class="player-meta" id="trackMeta">Genre • Mood • Duration</p>
                </div>
                <div class="player-controls">
                    <button id="playBtn" class="btn-icon">
                        <svg id="playIcon" width="28" height="28" viewBox="0 0 24 24" fill="currentColor">
                            <polygon points="5 3 19 12 5 21 5 3"></polygon>
                        </svg>
                        <svg id="pauseIcon" width="28" height="28" viewBox="0 0 24 24" fill="currentColor" style="display: none;">
                            <rect x="6" y="4" width="4" height="16"></rect>
                            <rect x="14" y="4" width="4" height="16"></rect>
                        </svg>
                    </button>
                    <button id="stopBtn" class="btn-icon">
                        <svg width="28" height="28" viewBox="0 0 24 24" fill="currentColor">
                            <rect x="6" y="6" width="12" height="12"></rect>
                        </svg>
                    </button>
                </div>
            </div>
            <div class="waveform" id="waveform">
                <div class="bar"></div>
                <div class="bar"></div>
                <div class="bar"></div>
                <div class="bar"></div>
                <div class="bar"></div>
                <div class="bar"></div>
                <div class="bar"></div>
            </div>

            <div class="singing-indicator" id="singingIndicator">
                <svg width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2">
                    <path d="M12 1a3 3 0 0 0-3 3v8a3 3 0 0 0 6 0V4a3 3 0 0 0-3-3z"></path>
                    <path d="M19 10v2a7 7 0 0 1-14 0v-2"></path>
                    <line x1="12" y1="19" x2="12" y2="23"></line>
                    <line x1="8" y1="23" x2="16" y2="23"></line>
                </svg>
                <div class="voice-wave"></div>
                <div class="voice-wave"></div>
                <div class="voice-wave"></div>
                <div class="voice-wave"></div>
                <div class="voice-wave"></div>
                <span style="margin-left: 0.5rem; font-weight: 600;">AI Voice Singing...</span>
            </div>

            <div id="lyricsDisplay" class="lyrics-display" style="display: none;"></div>
        </div>
        <!-- Track History -->
        <div id="historyCard" class="card" style="display: none;">
            <h3 class="section-title">
                <svg width="28" height="28" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2">
                    <circle cx="12" cy="12" r="10"></circle>
                    <polyline points="12 6 12 12 16 14"></polyline>
                </svg>
                Recent Generations
            </h3>
            <div id="trackList" class="track-list"></div>
        </div>
        </div>

        <!-- About Section -->
        <div class="about-section" id="aboutSection">
            <div class="header">
                <h1>About Quantum Melodies</h1>
                <p>Pioneering the Future of AI Music Generation</p>
            </div>

            <div class="card">
                <div class="about-content">
                    <div>
                        <h2 style="font-size: 2rem; margin-bottom: 1rem; background: linear-gradient(135deg, #a78bfa 0%, #ec4899 100%); -webkit-background-clip: text; -webkit-text-fill-color: transparent;">Our Mission</h2>
                        <p style="font-size: 1.1rem; line-height: 1.8; color: rgba(255, 255, 255, 0.9);">
                            We're revolutionizing music creation by combining cutting-edge AI technology with quantum randomness.
                            Our platform empowers musicians, creators, and dreamers to generate unique compositions with AI-powered
                            vocals, intelligent rhythm sections, and multilingual lyrics support.
                        </p>
                    </div>
                    <div>
                        <h2 style="font-size: 2rem; margin-bottom: 1rem; background: linear-gradient(135deg, #a78bfa 0%, #ec4899 100%); -webkit-background-clip: text; -webkit-text-fill-color: transparent;">Our Vision</h2>
                        <p style="font-size: 1.1rem; line-height: 1.8; color: rgba(255, 255, 255, 0.9);">
                            To democratize music production and make professional-quality composition accessible to everyone.
                            We believe that everyone has a song inside them, and our AI helps bring those melodies to life
                            with unprecedented ease and creativity.
                        </p>
                    </div>
                </div>

                <h2 style="font-size: 2rem; margin: 2rem 0 1rem; background: linear-gradient(135deg, #a78bfa 0%, #ec4899 100%); -webkit-background-clip: text; -webkit-text-fill-color: transparent;">Meet Our Team</h2>
                <div class="team-grid">
                    <div class="team-member">
                        <div class="team-avatar">👨‍💻</div>
                        <h3 style="margin-bottom: 0.5rem;">Aleena F Antony</h3>
                        <p style="color: #a78bfa; margin-bottom: 0.5rem;">Lead AI Engineer</p>
                        <p style="font-size: 0.9rem; color: rgba(255, 255, 255, 0.7);">Quantum computing & ML specialist</p>
                    </div>
                    <div class="team-member">
                        <div class="team-avatar">👩‍🎨</div>
                        <h3 style="margin-bottom: 0.5rem;">Shria</h3>
                        <p style="color: #a78bfa; margin-bottom: 0.5rem;">Music Director</p>
                        <p style="font-size: 0.9rem; color: rgba(255, 255, 255, 0.7);">Composer & sound designer</p>
                    </div>
                </div>
                

                <div style="margin-top: 3rem; padding: 2rem; background: rgba(102, 126, 234, 0.2); border-radius: 1rem; border: 1px solid rgba(102, 126, 234, 0.3);">
                    <h3 style="font-size: 1.5rem; margin-bottom: 1rem;">🚀 Key Features</h3>
                    <ul style="list-style: none; padding: 0;">
                        <li style="padding: 0.5rem 0; font-size: 1.05rem;">✅ AI-Powered Music Generation</li>
                        <li style="padding: 0.5rem 0; font-size: 1.05rem;">✅ 5+ Unique Voice Styles</li>
                        <li style="padding: 0.5rem 0; font-size: 1.05rem;">✅ Multilingual Lyrics (10+ Languages)</li>
                        <li style="padding: 0.5rem 0; font-size: 1.05rem;">✅ Quantum Randomness Integration</li>
                        <li style="padding: 0.5rem 0; font-size: 1.05rem;">✅ Full Rhythm Section (Drums & Bass)</li>
                        <li style="padding: 0.5rem 0; font-size: 1.05rem;">✅ Real-time Lyrics Animation</li>
                        <li style="padding: 0.5rem 0; font-size: 1.05rem;">✅ Multiple Genre Support</li>
                        <li style="padding: 0.5rem 0; font-size: 1.05rem;">✅ User Voice Recording</li>
                    </ul>
                </div>
            </div>
        </div>
    </div>

    <!-- Chatbot -->
    <div class="chatbot-container">
        <button class="chatbot-toggle" onclick="toggleChatbot()">
            <svg viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2">
                <path d="M21 15a2 2 0 0 1-2 2H7l-4 4V5a2 2 0 0 1 2-2h14a2 2 0 0 1 2 2z"></path>
            </svg>
        </button>
        <div class="chatbot-window" id="chatbotWindow">
            <div class="chatbot-header">
                <h3>🤖 AI Assistant</h3>
                <button class="chatbot-close" onclick="toggleChatbot()">×</button>
            </div>
            <div class="chatbot-messages" id="chatMessages">
                <div class="chat-message bot">
                    Hi! I'm your AI music assistant. I can help you with:
                    <br>• Choosing the right genre
                    <br>• Writing lyrics
                    <br>• Selecting voice styles
                    <br>• Tips for better compositions
                    <br><br>How can I help you today?
                </div>
            </div>
            <div class="chatbot-input-container">
                <input type="text" class="chatbot-input" id="chatInput" placeholder="Ask me anything..." onkeypress="handleChatKey(event)">
                <button class="chatbot-send" onclick="sendChatMessage()">
                    <svg width="20" height="20" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2">
                        <line x1="22" y1="2" x2="11" y2="13"></line>
                        <polygon points="22 2 15 22 11 13 2 9 22 2"></polygon>
                    </svg>
                </button>
            </div>
        </div>
    </div>

    <!-- Login Modal -->
    <div id="loginModal" class="modal">
        <div class="modal-content" style="max-width: 450px;">
            <span class="close" onclick="closeLoginModal()">&times;</span>
            <h2 style="text-align: center; margin-bottom: 2rem;">
                <svg style="display: inline; width: 40px; height: 40px; vertical-align: middle;" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2">
                    <path d="M20 21v-2a4 4 0 0 0-4-4H8a4 4 0 0 0-4 4v2"></path>
                    <circle cx="12" cy="7" r="4"></circle>
                </svg>
                <br>Login to Quantum Melodies
            </h2>
            <p style="text-align: center; color: rgba(255, 255, 255, 0.7); margin-bottom: 2rem;">
                Sign in to save your compositions and access premium features
            </p>
            <button class="btn btn-primary" onclick="loginWithEmail()" style="margin-bottom: 1rem;">
                <svg style="display: inline; width: 24px; height: 24px; vertical-align: middle;" viewBox="0 0 24 24" fill="currentColor">
                    <path d="M22.56 12.25c0-.78-.07-1.53-.2-2.25H12v4.26h5.92c-.26 1.37-1.04 2.53-2.21 3.31v2.77h3.57c2.08-1.92 3.28-4.74 3.28-8.09z" fill="#4285F4"/>
                    <path d="M12 23c2.97 0 5.46-.98 7.28-2.66l-3.57-2.77c-.98.66-2.23 1.06-3.71 1.06-2.86 0-5.29-1.93-6.16-4.53H2.18v2.84C3.99 20.53 7.7 23 12 23z" fill="#34A853"/>
                    <path d="M5.84 14.09c-.22-.66-.35-1.36-.35-2.09s.13-1.43.35-2.09V7.07H2.18C1.43 8.55 1 10.22 1 12s.43 3.45 1.18 4.93l2.85-2.22.81-.62z" fill="#FBBC05"/>
                    <path d="M12 5.38c1.62 0 3.06.56 4.21 1.64l3.15-3.15C17.45 2.09 14.97 1 12 1 7.7 1 3.99 3.47 2.18 7.07l3.66 2.84c.87-2.6 3.3-4.53 6.16-4.53z" fill="#EA4335"/>
                </svg>
                Continue with Email
            </button>
            <p style="text-align: center; font-size: 0.85rem; color: rgba(255, 255, 255, 0.5);">
                By continuing, you agree to our Terms of Service and Privacy Policy
            </p>
        </div>
    </div>

    <script>
        // Create floating particles
        const particlesContainer = document.getElementById('particles');
        for (let i = 0; i < 30; i++) {
            const particle = document.createElement('div');
            particle.className = 'particle';
            particle.style.left = Math.random() * 100 + '%';
            particle.style.animationDelay = Math.random() * 20 + 's';
            particle.style.animationDuration = (Math.random() * 10 + 10) + 's';
            particlesContainer.appendChild(particle);
        }
        // Create floating musical notes
        const musicalNotesContainer = document.getElementById('musicalNotes');
        const musicSymbols = ['♪', '♫', '♬', '♩', '🎵', '🎶', '🎼'];

        function createMusicalNote() {
            const note = document.createElement('div');
            note.className = 'music-note';
            note.textContent = musicSymbols[Math.floor(Math.random() * musicSymbols.length)];
            note.style.left = Math.random() * 100 + '%';
            note.style.fontSize = (Math.random() * 2 + 1) + 'rem';
            note.style.color = `hsl(${Math.random() * 360}, 80%, 70%)`;
            note.style.animationDelay = Math.random() * 8 + 's';
            note.style.animationDuration = (Math.random() * 5 + 6) + 's';
            musicalNotesContainer.appendChild(note);

            setTimeout(() => note.remove(), 12000);
        }

        // Create notes periodically
        setInterval(createMusicalNote, 1500);

        // Create initial notes
        for (let i = 0; i < 8; i++) {
            setTimeout(createMusicalNote, i * 500);
        }

        // Create glitter effect
        function createGlitter(x, y) {
            const glitter = document.createElement('div');
            glitter.className = 'glitter';
            glitter.style.position = 'fixed';
            glitter.style.left = x + 'px';
            glitter.style.top = y + 'px';
            glitter.style.zIndex = '10';
            document.body.appendChild(glitter);

            setTimeout(() => glitter.remove(), 2000);
        }

        // Add glitter on click
        document.addEventListener('click', (e) => {
            for (let i = 0; i < 8; i++) {
                setTimeout(() => {
                    const offsetX = (Math.random() - 0.5) * 50;
                    const offsetY = (Math.random() - 0.5) * 50;
                    createGlitter(e.clientX + offsetX, e.clientY + offsetY);
                }, i * 50);
            }
        });

        // Global Variables
        let synth = null;
        let bass = null;
        let drums = null;
        let voiceSynth = null;
        let sequence = null;
        let bassSequence = null;
        let drumSequence = null;
        let voiceSequence = null;
        let isPlaying = false;
        let currentTrack = null;
        let generatedTracks = [];
        let lyricsMode = 'none';
        let currentLyrics = '';
        let isSinging = false;
        let selectedVoices = ['ethereal'];
        let mediaRecorder = null;
        let audioChunks = [];
        let recordedBlob = null;

        // Voice Presets
        const voicePresets = {
            ethereal: {
                oscillator: {
                    type: 'sine',
                    partials: [1, 0.5, 0.3, 0.2, 0.1]
                },
                envelope: {
                    attack: 0.2,
                    decay: 0.3,
                    sustain: 0.6,
                    release: 1.2
                },
                reverb: 0.5
            },
            powerful: {
                oscillator: {
                    type: 'sawtooth',
                    partials: [1, 0.8, 0.6, 0.4, 0.3]
                },
                envelope: {
                    attack: 0.05,
                    decay: 0.2,
                    sustain: 0.8,
                    release: 0.5
                },
                reverb: 0.2
            },
            smooth: {
                oscillator: {
                    type: 'triangle',
                    partials: [1, 0.7, 0.5, 0.3]
                },
                envelope: {
                    attack: 0.1,
                    decay: 0.25,
                    sustain: 0.7,
                    release: 0.9
                },
                reverb: 0.35
            },
            robotic: {
                oscillator: {
                    type: 'square',
                    partials: [1, 0, 0.3, 0, 0.2]
                },
                envelope: {
                    attack: 0.01,
                    decay: 0.1,
                    sustain: 0.9,
                    release: 0.1
                },
                reverb: 0.1
            },
            choir: {
                oscillator: {
                    type: 'sine',
                    partials: [1, 0.6, 0.4, 0.3, 0.2, 0.15]
                },
                envelope: {
                    attack: 0.3,
                    decay: 0.4,
                    sustain: 0.7,
                    release: 1.5
                },
                reverb: 0.6
            }
        };

        // Section Navigation
        function showSection(section) {
            document.getElementById('mainSection').classList.toggle('hidden', section !== 'main');
            document.getElementById('aboutSection').classList.toggle('active', section === 'about');
        }

        // Voice Selection
        function toggleVoice(voice) {
            const index = selectedVoices.indexOf(voice);
            if (index === -1) {
                selectedVoices.push(voice);
            } else {
                selectedVoices.splice(index, 1);
            }

            // Update UI
            document.querySelectorAll('.voice-card').forEach(card => {
                card.classList.remove('selected');
            });

            selectedVoices.forEach(v => {
                document.getElementById(`voice-${v}`).classList.add('selected');
            });
        }

        // Open Custom Voice Modal
        function openCustomVoiceModal() {
            document.getElementById('customVoiceModal').style.display = 'block';
        }

        // Close Custom Voice Modal
        function closeCustomVoiceModal() {
            document.getElementById('customVoiceModal').style.display = 'none';
        }

        // Save Custom Voice
        function saveCustomVoice() {
            const customVoice = {
                oscillator: {
                    type: document.getElementById('customOscillator').value,
                    partials: [1, 0.5, 0.3, 0.2, 0.1]
                },
                envelope: {
                    attack: parseFloat(document.getElementById('customAttack').value),
                    decay: parseFloat(document.getElementById('customDecay').value),
                    sustain: parseFloat(document.getElementById('customSustain').value),
                    release: parseFloat(document.getElementById('customRelease').value)
                },
                reverb: parseFloat(document.getElementById('customReverb').value)
            };

            // Store the custom voice
            voicePresets.custom = customVoice;

            // Add to selected voices
            if (!selectedVoices.includes('custom')) {
                selectedVoices.push('custom');
            }

            // Update UI
            document.querySelectorAll('.voice-card').forEach(card => {
                card.classList.remove('selected');
            });

            selectedVoices.forEach(v => {
                if (v === 'custom') {
                    document.getElementById('voice-custom').classList.add('selected');
                } else {
                    document.getElementById(`voice-${v}`).classList.add('selected');
                }
            });

            closeCustomVoiceModal();
            alert('Custom voice saved! Use it to generate music.');
        }

        // Update Slider Values
        document.getElementById('customAttack').addEventListener('input', function() {
            document.getElementById('customAttackValue').textContent = this.value;
        });
        document.getElementById('customDecay').addEventListener('input', function() {
            document.getElementById('customDecayValue').textContent = this.value;
        });
        document.getElementById('customSustain').addEventListener('input', function() {
            document.getElementById('customSustainValue').textContent = this.value;
        });
        document.getElementById('customRelease').addEventListener('input', function() {
            document.getElementById('customReleaseValue').textContent = this.value;
        });
        document.getElementById('customReverb').addEventListener('input', function() {
            document.getElementById('customReverbValue').textContent = this.value;
        });

        // Login Functions
        function openLoginModal() {
            if (isLoggedIn) {
                // Logout
                isLoggedIn = false;
                userName = '';
                document.getElementById('loginBtnText').textContent = 'Login';
                alert('Logged out successfully!');
            } else {
                document.getElementById('loginModal').style.display = 'block';
            }
        }

        function closeLoginModal() {
            document.getElementById('loginModal').style.display = 'none';
        }

        function loginWithEmail() {
            // Simulate login (in production, use OAuth)
            userName = 'User' + Math.floor(Math.random() * 1000);
            isLoggedIn = true;
            document.getElementById('loginBtnText').textContent = '👋 ' + userName;
            closeLoginModal();

            // Add welcome message to chatbot
            const chatMessages = document.getElementById('chatMessages');
            const welcomeMsg = document.createElement('div');
            welcomeMsg.className = 'chat-message bot';
            welcomeMsg.textContent = `Welcome ${userName}! You're now logged in. All your compositions will be saved to your account.`;
            chatMessages.appendChild(welcomeMsg);
            chatMessages.scrollTop = chatMessages.scrollHeight;
        }

        // Chatbot Functions
        function toggleChatbot() {
            document.getElementById('chatbotWindow').classList.toggle('active');
        }

        function handleChatKey(event) {
            if (event.key === 'Enter') {
                sendChatMessage();
            }
        }

        function sendChatMessage() {
            const input = document.getElementById('chatInput');
            const message = input.value.trim();

            if (!message) return;

            const chatMessages = document.getElementById('chatMessages');

            // Add user message
            const userMsg = document.createElement('div');
            userMsg.className = 'chat-message user';
            userMsg.textContent = message;
            chatMessages.appendChild(userMsg);

            input.value = '';

            // Generate bot response
            setTimeout(() => {
                const botMsg = document.createElement('div');
                botMsg.className = 'chat-message bot';
                botMsg.textContent = generateBotResponse(message);
                chatMessages.appendChild(botMsg);
                chatMessages.scrollTop = chatMessages.scrollHeight;
            }, 500);

            chatMessages.scrollTop = chatMessages.scrollHeight;
        }

        function generateBotResponse(message) {
            const lowerMsg = message.toLowerCase();

            if (lowerMsg.includes('genre') || lowerMsg.includes('style')) {
                return "Great question! We support Electronic, Ambient, Classical, Jazz, Hip Hop, Rock, and Experimental genres. Electronic and Hip Hop are perfect for energetic tracks, while Ambient and Classical work well for calm, emotional pieces. What mood are you going for?";
            } else if (lowerMsg.includes('lyrics') || lowerMsg.includes('words')) {
                return "For lyrics, you have 3 options: 1) AI-generated lyrics in 10+ languages, 2) Write your own custom lyrics, or 3) Create an instrumental track. AI lyrics are great for quick inspiration, while custom lyrics give you full creative control!";
            } else if (lowerMsg.includes('voice') || lowerMsg.includes('singing')) {
                return "We offer 5+ unique voice styles: Ethereal (dreamy), Powerful (strong), Smooth (silky), Robotic (futuristic), Choir (epic), and Custom (design your own). Each voice has its own character. Try Ethereal for emotional ballads or Powerful for anthems!";
            } else if (lowerMsg.includes('quantum')) {
                return "Quantum mode uses real quantum random numbers from ANU's quantum RNG! This creates truly unpredictable, unique melodies that are mathematically impossible to reproduce. It's perfect for experimental and one-of-a-kind compositions!";
            } else if (lowerMsg.includes('help') || lowerMsg.includes('how')) {
                return "Here's how to create amazing music: 1) Choose your genre and mood, 2) Select a voice style, 3) Add lyrics (optional), 4) Enable Quantum mode for uniqueness, 5) Click Generate! Start with shorter durations (30s) to experiment faster.";
            } else if (lowerMsg.includes('save') || lowerMsg.includes('download')) {
                return "Your recent tracks are automatically saved in the 'Recent Generations' section below! You can play them anytime. To keep them permanently, make sure you're logged in. Premium features coming soon!";
            } else if (lowerMsg.includes('hi') || lowerMsg.includes('hello')) {
                return "Hello! 👋 I'm here to help you create amazing music. What would you like to know about our AI music generator?";
            } else if (lowerMsg.includes('thank')) {
                return "You're welcome! Feel free to ask me anything else about music creation. Happy composing! 🎵";
            } else {
                return "That's an interesting question! I can help you with genres, lyrics, voice styles, quantum mode, and composition tips. What would you like to know more about?";
            }
        }

        // Audio Engine
        function initAudio() {
            if (!synth) {
                synth = new Tone.PolySynth(Tone.Synth, {
                    oscillator: { type: 'triangle' },
                    envelope: {
                        attack: 0.05,
                        decay: 0.2,
                        sustain: 0.5,
                        release: 1.5
                    }
                }).toDestination();
                synth.volume.value = -10;
                bass = new Tone.MonoSynth({
                    oscillator: { type: 'sawtooth' },
                    envelope: {
                        attack: 0.1,
                        decay: 0.3,
                        sustain: 0.4,
                        release: 1.2
                    },
                    filterEnvelope: {
                        attack: 0.05,
                        decay: 0.3,
                        sustain: 0.4,
                        release: 1.2,
                        baseFrequency: 200,
                        octaves: 2.5
                    }
                }).toDestination();
                bass.volume.value = -8;
                drums = new Tone.MembraneSynth({
                    pitchDecay: 0.05,
                    octaves: 6,
                    oscillator: { type: 'sine' },
                    envelope: {
                        attack: 0.001,
                        decay: 0.4,
                        sustain: 0.01,
                        release: 1.4,
                        attackCurve: 'exponential'
                    }
                }).toDestination();
                drums.volume.value = -6;

                // Voice Synthesizer for singing
                voiceSynth = new Tone.PolySynth(Tone.Synth, {
                    oscillator: {
                        type: 'sine',
                        partials: [1, 0.5, 0.3, 0.2, 0.1]
                    },
                    envelope: {
                        attack: 0.1,
                        decay: 0.3,
                        sustain: 0.6,
                        release: 0.8
                    },
                    portamento: 0.05
                }).toDestination();
                voiceSynth.volume.value = -5;

                // Add reverb for voice
                const reverb = new Tone.Reverb({
                    decay: 2.5,
                    wet: 0.3
                });
                reverb.toDestination();
                voiceSynth.connect(reverb);
            }
        }

        // Fetch quantum random numbers with fallback
        async function getQuantumRandomNumber() {
            try {
                // Try primary quantum API
                const controller = new AbortController();
                const timeoutId = setTimeout(() => controller.abort(), 3000);

                const response = await fetch('https://qrng.anu.edu.au/API/jsonI.php?length=1&type=uint8', {
                    signal: controller.signal,
                    mode: 'cors'
                });
                clearTimeout(timeoutId);

                if (response.ok) {
                    const data = await response.json();
                    return data.data[0] / 255;
                }
            } catch (error) {
                // Silent fallback - quantum API might be blocked by CORS
            }

            // Use crypto-based high-quality randomness as fallback
            const array = new Uint32Array(1);
            crypto.getRandomValues(array);
            return array[0] / 4294967295;
        }

        // Musical scales
        const scales = {
            major: [0, 2, 4, 5, 7, 9, 11],
            minor: [0, 2, 3, 5, 7, 8, 10],
            pentatonic: [0, 2, 4, 7, 9],
            blues: [0, 3, 5, 6, 7, 10],
            chromatic: [0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11]
        };

        const genrePresets = {
            electronic: { scale: 'minor', tempo: 128, drumPattern: 'fourOnFloor' },
            ambient: { scale: 'pentatonic', tempo: 80, drumPattern: 'sparse' },
            classical: { scale: 'major', tempo: 100, drumPattern: 'minimal' },
            jazz: { scale: 'blues', tempo: 120, drumPattern: 'swing' },
            hiphop: { scale: 'minor', tempo: 90, drumPattern: 'hiphop' },
            rock: { scale: 'pentatonic', tempo: 140, drumPattern: 'rock' },
            experimental: { scale: 'chromatic', tempo: 140, drumPattern: 'experimental' }
        };

        // Drum Patterns
        function generateDrumPattern(type, duration) {
            const patterns = {
                fourOnFloor: ['0:0:0', '0:1:0', '0:2:0', '0:3:0'],
                sparse: ['0:0:0', '0:2:0'],
                minimal: ['0:0:0'],
                swing: ['0:0:0', '0:0:2', '0:1:1', '0:2:0', '0:2:2', '0:3:1'],
                hiphop: ['0:0:0', '0:1:0', '0:2:0', '0:3:2'],
                rock: ['0:0:0', '0:0:2', '0:1:0', '0:1:2', '0:2:0', '0:2:2', '0:3:0', '0:3:2'],
                experimental: []
            };
            let pattern = patterns[type] || patterns.fourOnFloor;
            let fullPattern = [];
            const bars = Math.ceil(duration / 2);
            for (let bar = 0; bar < bars; bar++) {
                pattern.forEach(time => {
                    const [b, beat, sub] = time.split(':').map(Number);
                    fullPattern.push({
                        time: `${bar}:${beat}:${sub}`,
                        note: 'C1',
                        velocity: 0.8
                    });
                });
            }
            return fullPattern;
        }

        // Generate Bass Line
        function generateBassLine(melody, scale, baseNote) {
            const bassNotes = [];
            const bassPattern = [0, 4, 2, 6]; // Root, fourth, second, sixth

            for (let i = 0; i < melody.length; i += 4) {
                const patternIndex = (i / 4) % bassPattern.length;
                const scaleIndex = bassPattern[patternIndex] % scale.length;
                const note = baseNote - 24 + scale[scaleIndex];

                bassNotes.push({
                    note: Tone.Frequency(note, 'midi').toNote(),
                    time: i * 0.5,
                    duration: '2n',
                    velocity: 0.7
                });
            }

            return bassNotes;
        }

        // Generate Voice Melody for Lyrics
        function generateVoiceMelody(lyrics, melody, duration) {
            if (!lyrics) return null;

            const lines = lyrics.split('\n').filter(l => l.trim());
            const voiceMelody = [];
            const timePerLine = duration / lines.length;

            lines.forEach((line, lineIndex) => {
                const words = line.split(' ');
                const timePerWord = timePerLine / words.length;

                words.forEach((word, wordIndex) => {
                    if (word.trim()) {
                        const time = (lineIndex * timePerLine) + (wordIndex * timePerWord);
                        const melodyIndex = Math.floor((time / duration) * melody.length);
                        const baseNote = melody[melodyIndex] ? melody[melodyIndex].note : 'C4';

                        voiceMelody.push({
                            note: baseNote,
                            time: time,
                            duration: Math.min(timePerWord * 0.8, 1) + 'n',
                            velocity: 0.7
                        });
                    }
                });
            });

            return voiceMelody;
        }

        // Animate lyrics display during singing
        function animateLyrics(lyrics, duration) {
            if (!lyrics) return;

            const lyricsDisplay = document.getElementById('lyricsDisplay');
            const lines = lyrics.split('\n').filter(l => l.trim());
            const timePerLine = duration / lines.length;
            let currentLineIndex = 0;

            const interval = setInterval(() => {
                if (currentLineIndex < lines.length && isPlaying) {
                    lyricsDisplay.innerHTML = lines.map((line, index) => {
                        if (index === currentLineIndex) {
                            return `<div style="font-size: 1.4rem; font-weight: bold; color: #fff; text-shadow: 0 0 20px rgba(167, 139, 250, 0.8); transform: scale(1.1); transition: all 0.3s;">${line}</div>`;
                        } else if (index < currentLineIndex) {
                            return `<div style="opacity: 0.4;">${line}</div>`;
                        } else {
                            return `<div style="opacity: 0.6;">${line}</div>`;
                        }
                    }).join('');
                    currentLineIndex++;
                } else {
                    clearInterval(interval);
                }
            }, timePerLine * 1000);

            // Store interval to clear on stop
            window.lyricsInterval = interval;
        }

        async function generateMelody(genre, mood, duration) {
            const useQuantum = document.getElementById('quantumMode').checked;
            const preset = genrePresets[genre];
            const scale = scales[preset.scale];
            const baseNote = 60;
            const numNotes = Math.floor(duration / 0.5);
            const melody = [];
            let rhythmPattern = [0.5, 0.5, 1, 0.5, 0.5, 1]; // Varied rhythm
            let currentTime = 0;

            for (let i = 0; i < numNotes; i++) {
                let scaleIndex;
                if (useQuantum) {
                    scaleIndex = Math.floor(await getQuantumRandomNumber() * scale.length);
                } else {
                    scaleIndex = Math.floor(Math.random() * scale.length);
                }
                const octaveShift = Math.floor(Math.random() * 3) - 1;
                const note = baseNote + scale[scaleIndex] + (octaveShift * 12);
                let velocity;
                switch(mood) {
                    case 'uplifting': velocity = 0.8 + Math.random() * 0.2; break;
                    case 'calm': velocity = 0.3 + Math.random() * 0.3; break;
                    case 'energetic': velocity = 0.9 + Math.random() * 0.1; break;
                    case 'dark': velocity = 0.4 + Math.random() * 0.3; break;
                    case 'romantic': velocity = 0.5 + Math.random() * 0.3; break;
                    case 'epic': velocity = 0.85 + Math.random() * 0.15; break;
                    default: velocity = 0.5 + Math.random() * 0.3;
                }
                const rhythmIndex = i % rhythmPattern.length;
                const noteDuration = rhythmPattern[rhythmIndex];
                melody.push({
                    note: Tone.Frequency(note, 'midi').toNote(),
                    time: currentTime,
                    duration: noteDuration + 'n',
                    velocity
                });
                currentTime += noteDuration * 0.5;
            }
            return melody;
        }

        // Generate AI Lyrics
        function generateAILyrics(language, theme, mood, genre) {
            const lyricsTemplates = {
                en: {
                    uplifting: [
                        ["Rise above the clouds today", "Feel the sunshine on your face", "Together we can find our way", "Living in this moment's grace"],
                        ["A new dawn breaks, the sky is bright", "Every step we take feels just right", "Dancing through the golden light", "Future's shining, oh so bright"],
                        ["Hands up high, we reach the sky", "No more limits, we can fly", "Chasing dreams that never die", "Underneath the endless sky"]
                    ],
                    calm: [
                        ["Whispers in the gentle breeze", "Floating through the silent trees", "Peace is all we need to find", "Quiet moments ease the mind"],
                        ["Softly falls the evening rain", "Washing all the pain away", "In this calm, we stay the same", "Drifting on a quiet bay"],
                        ["Stars above, the night is deep", "In this silence, secrets keep", "Close your eyes and drift to sleep", "Where the peaceful waters creep"]
                    ],
                    energetic: [
                        ["Dancing through the electric night", "Feel the rhythm burning bright", "Moving faster than the speed of light", "Everything just feels so right"],
                        ["Loud and wild, we break the chains", "Fire in our veins again", "No more shadows, no more pain", "We're the storm, we're the flame"],
                        ["Turn it up, let's lose control", "Music's got us, heart and soul", "Never stopping, never slow", "This is how the legends roll"]
                    ],
                    dark: [
                        ["Shadows dancing on the wall", "Echoes through an empty hall", "Lost within the midnight call", "Into darkness we will fall"],
                        ["Ghosts are whispering my name", "In this game, we all play", "Nothing ever stays the same", "Fading into shades of gray"],
                        ["Silent screams in hollow halls", "Where the lonely nightfall crawls", "No one hears, no one calls", "Just the echoes on the walls"]
                    ],
                    romantic: [
                        ["Your eyes shine like stars above", "Every heartbeat speaks of love", "Together we rise like a dove", "You're the one I'm dreaming of"],
                        ["In your arms, the world stands still", "Every touch, a sweet thrill", "With you, my heart will always fill", "Like the moon and stars always will"],
                        ["Hand in hand, we walk the shore", "Love is all we're living for", "With you, my heart will soar", "Forever yours, forevermore"]
                    ],
                    epic: [
                        ["Warriors of the rising sun", "Battle's lost but not yet won", "Glory waits for everyone", "Our journey's only just begun"],
                        ["Through the fire, through the rain", "Standing tall through all the pain", "Victory is ours to claim", "Rise again and break the chain"],
                        ["Legends never fade away", "They still fight, they still stay", "Through the darkest night or day", "We will find our own way"]
                    ]
                },
                es: {
                    uplifting: [
                        ["Volar sobre las nubes hoy", "Sentir el sol brillar", "Juntos podemos ganar", "Viviendo sin parar"],
                        ["Un nuevo día, el cielo está claro", "Cada paso que damos es sincero", "Bailando bajo el sol dorado", "El futuro brilla, tan claro"]
                    ],
                    calm: [
                        ["Susurros en el viento suave", "Paz que el alma encuentra", "Silencio que nos calma", "Momentos que alimentan"],
                        ["La lluvia cae sin prisa", "Lavando lo que aprieta", "En esta calma, todo es pura", "Drifting on a quiet bay"]
                    ]
                },
                fr: {
                    uplifting: [
                        ["S'élever au-dessus des nuages", "Sentir le soleil briller", "Ensemble nous trouverons", "Vivre dans ce moment présent"],
                        ["Un nouveau jour se lève enfin", "Chaque pas est un chemin", "Dansons sous le ciel serein", "L'avenir brille, c'est certain"]
                    ],
                    calm: [
                        ["Murmures dans la brise douce", "Flottant à travers les arbres", "La paix est tout ce qu'il nous faut", "Des moments tranquilles"],
                        ["La pluie tombe en douceur", "Emportant toute douleur", "Dans ce calme, tout est meilleur", "Lâche prise, tout est fleur"]
                    ]
                },
                hi: {
                    uplifting: [
                        ["आसमान छू लेंगे आज", "सूरज की किरणों में", "साथ मिलकर चलेंगे", "इस पल में जीते हैं"],
                        ["नई सुबह, नया दिन", "हर कदम है नया राह", "नाचते हैं धूप में", "भविष्य है उजाला"]
                    ],
                    calm: [
                        ["हवा में धीमी सरगोशी", "पेड़ों के बीच शांति", "सुकून ही चाहिए", "मन को शांत करे"],
                        ["बारिश की बूंदें गिरती", "ले जाती सब तनाव", "इस शांति में सब है प्यार", "चुपके से बहता नदी का पानी"]
                    ]
                }
            };

            // Select a random template for variety
            const languageTemplates = lyricsTemplates[language] || lyricsTemplates.en;
            const moodTemplates = languageTemplates[mood] || languageTemplates.uplifting;
            const randomTemplate = moodTemplates[Math.floor(Math.random() * moodTemplates.length)];

            return randomTemplate.join('\n');
        }

        // Play Enhanced Track with Rhythm and Voice
        async function playTrack(track) {
            await Tone.start();
            initAudio();
            if (isPlaying) {
                stopPlayback();
                return;
            }

            const preset = genrePresets[track.genre];
            Tone.Transport.bpm.value = preset.tempo;

            // Melody
            sequence = new Tone.Part((time, note) => {
                synth.triggerAttackRelease(
                    note.note,
                    note.duration,
                    time,
                    note.velocity
                );
            }, track.melody);
            sequence.start(0);
            sequence.loop = true;
            sequence.loopEnd = track.duration;

            // Bass Line
            if (track.bassLine) {
                bassSequence = new Tone.Part((time, note) => {
                    bass.triggerAttackRelease(
                        note.note,
                        note.duration,
                        time,
                        note.velocity
                    );
                }, track.bassLine);
                bassSequence.start(0);
                bassSequence.loop = true;
                bassSequence.loopEnd = track.duration;
            }

            // Drums
            if (track.drums) {
                drumSequence = new Tone.Part((time, note) => {
                    drums.triggerAttackRelease(
                        note.note,
                        '8n',
                        time,
                        note.velocity
                    );
                }, track.drums);
                drumSequence.start(0);
                drumSequence.loop = true;
                drumSequence.loopEnd = track.duration;
            }

            // Voice Singing
            if (track.lyrics && track.lyrics.type === 'recording') {
                const audioURL = URL.createObjectURL(track.lyrics.blob);
                const audioPlayer = new Audio(audioURL);
                audioPlayer.loop = true;
                audioPlayer.play();

                // Show lyrics display (if any)
                document.getElementById('lyricsDisplay').style.display = 'block';
                document.getElementById('lyricsDisplay').textContent = 'Playing your recorded voice...';
            } else if (track.voiceMelody && track.lyrics) {
                // Create a new voice synth for each selected voice
                const voiceSynths = selectedVoices.map(voice => {
                    const synth = new Tone.PolySynth(Tone.Synth, {
                        oscillator: voicePresets[voice].oscillator,
                        envelope: voicePresets[voice].envelope,
                        portamento: 0.05
                    }).toDestination();

                    const reverb = new Tone.Reverb({
                        decay: 2.5,
                        wet: voicePresets[voice].reverb
                    }).toDestination();
                    synth.connect(reverb);
                    synth.volume.value = -5;

                    return synth;
                });

                voiceSequence = new Tone.Part((time, note) => {
                    voiceSynths.forEach(synth => {
                        synth.triggerAttackRelease(
                            note.note,
                            note.duration,
                            time,
                            note.velocity
                        );
                    });

                    // Show singing indicator
                    Tone.Draw.schedule(() => {
                        isSinging = true;
                        document.getElementById('singingIndicator').classList.add('active');
                    }, time);

                    Tone.Draw.schedule(() => {
                        isSinging = false;
                        document.getElementById('singingIndicator').classList.remove('active');
                    }, time + 0.5);
                }, track.voiceMelody);

                voiceSequence.start(0);
                voiceSequence.loop = true;
                voiceSequence.loopEnd = track.duration;

                // Show and animate lyrics
                document.getElementById('lyricsDisplay').style.display = 'block';
                animateLyrics(track.lyrics, track.duration);
            } else if (track.lyrics) {
                // Just show static lyrics if no voice
                document.getElementById('lyricsDisplay').style.display = 'block';
                document.getElementById('lyricsDisplay').textContent = track.lyrics;
            }

            Tone.Transport.start();
            isPlaying = true;
            updatePlayButton(true);
        }

        // Stop Playback
        function stopPlayback() {
            Tone.Transport.stop();
            if (sequence) {
                sequence.stop();
                sequence.dispose();
            }
            if (bassSequence) {
                bassSequence.stop();
                bassSequence.dispose();
            }
            if (drumSequence) {
                drumSequence.stop();
                drumSequence.dispose();
            }
            if (voiceSequence) {
                voiceSequence.stop();
                voiceSequence.dispose();
            }
            if (window.lyricsInterval) {
                clearInterval(window.lyricsInterval);
            }
            isPlaying = false;
            isSinging = false;
            document.getElementById('singingIndicator').classList.remove('active');
            updatePlayButton(false);
        }

        // Update Play Button
        function updatePlayButton(playing) {
            const playIcon = document.getElementById('playIcon');
            const pauseIcon = document.getElementById('pauseIcon');
            const waveform = document.getElementById('waveform');

            if (playing) {
                playIcon.style.display = 'none';
                pauseIcon.style.display = 'block';
                waveform.style.display = 'flex';
            } else {
                playIcon.style.display = 'block';
                pauseIcon.style.display = 'none';
            }
        }

        // Update Player UI
        function updatePlayer(track) {
            document.getElementById('playerCard').style.display = 'block';
            document.getElementById('trackTitle').textContent = track.title;
            document.getElementById('trackMeta').textContent =
                `${track.genre} • ${track.mood} • ${track.duration}s`;
        }

        // Add Track to History
        function addToHistory(track) {
            generatedTracks.unshift(track);
            if (generatedTracks.length > 5) generatedTracks.pop();
            const historyCard = document.getElementById('historyCard');
            const trackList = document.getElementById('trackList');
            historyCard.style.display = 'block';
            trackList.innerHTML = '';
            generatedTracks.forEach(t => {
                const trackItem = document.createElement('div');
                trackItem.className = 'track-item';
                trackItem.innerHTML = `
                    <div class="track-info">
                        <h4>${t.title}</h4>
                        <p>${t.timestamp} • ${t.genre} • ${t.mood} ${t.lyrics ? '• 🎤 Lyrics' : ''}</p>
                    </div>
                    <button class="btn-play" onclick="loadTrack(${t.id})">
                        <svg width="20" height="20" viewBox="0 0 24 24" fill="currentColor">
                            <polygon points="5 3 19 12 5 21 5 3"></polygon>
                        </svg>
                    </button>
                `;
                trackList.appendChild(trackItem);
            });
        }

        // Load Track
        window.loadTrack = function(id) {
            const track = generatedTracks.find(t => t.id === id);
            if (track) {
                stopPlayback();
                currentTrack = track;
                updatePlayer(track);
            }
        };

        // Lyrics Modal Functions
        function openLyricsModal() {
            document.getElementById('lyricsModal').style.display = 'block';
        }

        function closeLyricsModal() {
            document.getElementById('lyricsModal').style.display = 'none';
        }

        function selectLyricsOption(option) {
            document.getElementById('aiLyricsOptions').style.display = 'none';
            document.getElementById('manualLyricsOptions').style.display = 'none';
            document.getElementById('recordVoiceModal').style.display = 'none';

            if (option === 'ai') {
                document.getElementById('aiLyricsOptions').style.display = 'block';
                lyricsMode = 'ai';
            } else if (option === 'manual') {
                document.getElementById('manualLyricsOptions').style.display = 'block';
                lyricsMode = 'manual';
            } else if (option === 'record') {
                document.getElementById('recordVoiceModal').style.display = 'block';
                lyricsMode = 'record';
            } else {
                lyricsMode = 'none';
                currentLyrics = '';
                closeLyricsModal();
            }
        }

        function confirmAILyrics() {
            const language = document.getElementById('lyricsLanguage').value;
            const theme = document.getElementById('lyricsTheme').value;
            const mood = document.getElementById('mood').value;
            const genre = document.getElementById('genre').value;

            currentLyrics = generateAILyrics(language, theme, mood, genre);
            closeLyricsModal();
            alert('AI lyrics generated! They will appear when you generate the music.');
        }

        function confirmManualLyrics() {
            currentLyrics = document.getElementById('manualLyrics').value;
            closeLyricsModal();
            alert('Your lyrics have been saved!');
        }

        // Record Voice Functions
        function openRecordVoiceModal() {
            document.getElementById('recordVoiceModal').style.display = 'block';
        }

        function closeRecordVoiceModal() {
            document.getElementById('recordVoiceModal').style.display = 'none';
        }

        async function startRecording() {
            try {
                const stream = await navigator.mediaDevices.getUserMedia({ audio: true });
                mediaRecorder = new MediaRecorder(stream);
                audioChunks = [];

                mediaRecorder.ondataavailable = (event) => {
                    audioChunks.push(event.data);
                };

                mediaRecorder.onstop = () => {
                    recordedBlob = new Blob(audioChunks, { type: 'audio/wav' });
                    const audioURL = URL.createObjectURL(recordedBlob);
                    document.getElementById('recordedAudio').src = audioURL;
                };

                mediaRecorder.start();
                document.getElementById('startRecordBtn').disabled = true;
                document.getElementById('stopRecordBtn').disabled = false;
            } catch (error) {
                alert('Error accessing microphone: ' + error.message);
            }
        }

        function stopRecording() {
            if (mediaRecorder && mediaRecorder.state !== 'inactive') {
                mediaRecorder.stop();
                document.getElementById('startRecordBtn').disabled = false;
                document.getElementById('stopRecordBtn').disabled = true;
            }
        }

        function saveRecordedVoice() {
            if (!recordedBlob) {
                alert('Please record your voice first!');
                return;
            }

            // Store the recorded audio for playback
            currentLyrics = {
                type: 'recording',
                blob: recordedBlob
            };

            closeRecordVoiceModal();
            alert('Your voice recording has been saved!');
        }

        // Generate Button Handler
        document.getElementById('generateBtn').addEventListener('click', async function() {
            const btn = this;
            const prompt = document.getElementById('prompt').value;
            const genre = document.getElementById('genre').value;
            const mood = document.getElementById('mood').value;
            const duration = parseInt(document.getElementById('duration').value);
            const useQuantum = document.getElementById('quantumMode').checked;

            btn.disabled = true;
            btn.innerHTML = `
                <div class="spinner"></div>
                ${useQuantum ? 'Fetching Quantum Randomness...' : 'Generating Music...'}
            `;

            await Tone.start();

            setTimeout(async () => {
                const melody = await generateMelody(genre, mood, duration);
                const preset = genrePresets[genre];
                const scale = scales[preset.scale];
                const bassLine = generateBassLine(melody, scale, 60);
                const drums = generateDrumPattern(preset.drumPattern, duration);
                const voiceMelody = lyricsMode !== 'none' ? generateVoiceMelody(currentLyrics, melody, duration) : null;

                const track = {
                    id: Date.now(),
                    title: prompt || `${genre} ${mood} composition`,
                    melody,
                    bassLine,
                    drums,
                    voiceMelody,
                    genre,
                    mood,
                    duration,
                    lyrics: lyricsMode !== 'none' ? currentLyrics : null,
                    timestamp: new Date().toLocaleTimeString()
                };

                currentTrack = track;
                updatePlayer(track);
                addToHistory(track);

                btn.disabled = false;
                btn.innerHTML = `
                    <svg width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2">
                        <polygon points="5 3 19 12 5 21 5 3"></polygon>
                    </svg>
                    Generate Music
                `;
            }, 2000);
        });

        // Play Button Handler
        document.getElementById('playBtn').addEventListener('click', function() {
            if (currentTrack) {
                playTrack(currentTrack);
            }
        });

        // Stop Button Handler
        document.getElementById('stopBtn').addEventListener('click', stopPlayback);

        // Close modal when clicking outside
        window.onclick = function(event) {
            const modal = document.getElementById('lyricsModal');
            if (event.target == modal) {
                closeLyricsModal();
            }

            const customModal = document.getElementById('customVoiceModal');
            if (event.target == customModal) {
                closeCustomVoiceModal();
            }

            const recordModal = document.getElementById('recordVoiceModal');
            if (event.target == recordModal) {
                closeRecordVoiceModal();
            }
        };
    </script>
</body>
</html>




