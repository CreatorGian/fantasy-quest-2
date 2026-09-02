
<!DOCTYPE html>
<html lang="de">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Fantasy Quest: The Gamified Flashcard Adventure</title>
    <style>
        /* Modern Medieval/Fantasy Theme */
        :root {
            --bg-dark: #0f1016;
            --panel-bg: #1a1c26;
            --accent-gold: #ffd700;
            --accent-green: #2ecc71;
            --accent-red: #e74c3c;
            --accent-blue: #3498db;
            --text-light: #f5f6fa;
            --text-muted: #a0a5b5;
            --border-radius: 12px;
            --font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
        }

        * {
            box-sizing: border-box;
            margin: 0;
            padding: 0;
            font-family: var(--font-family);
        }

        body {
            background-color: var(--bg-dark);
            color: var(--text-light);
            display: flex;
            justify-content: center;
            align-items: center;
            min-height: 100vh;
            overflow-x: hidden;
            padding: 10px;
        }

        /* Screen boundaries & Scaling */
        .game-container {
            width: 100%;
            max-width: 900px;
            height: 600px;
            background-color: var(--panel-bg);
            border: 4px solid var(--accent-gold);
            border-radius: var(--border-radius);
            position: relative;
            overflow: hidden;
            box-shadow: 0 15px 30px rgba(0,0,0,0.5), inset 0 0 15px rgba(255,215,0,0.1);
        }

        /* Active screens */
        .screen {
            position: absolute;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            display: none;
            flex-direction: column;
            padding: 25px;
        }

        .screen.active {
            display: flex;
        }

        /* Utility Buttons */
        .btn {
            background: linear-gradient(135deg, #2c3e50, #34495e);
            color: var(--text-light);
            border: 2px solid var(--accent-gold);
            border-radius: 8px;
            padding: 12px 24px;
            font-size: 1rem;
            font-weight: bold;
            cursor: pointer;
            transition: all 0.2s ease;
            box-shadow: 0 4px 6px rgba(0,0,0,0.2);
            text-align: center;
            display: inline-block;
        }

        .btn:hover {
            transform: translateY(-2px);
            box-shadow: 0 6px 12px rgba(255,215,0,0.2);
            background: linear-gradient(135deg, #34495e, #4f6f8f);
        }

        .btn:active {
            transform: translateY(1px);
        }

        .btn-gold {
            background: linear-gradient(135deg, #d4af37, #aa7c11);
            color: #111;
            border-color: #fff;
        }

        .btn-gold:hover {
            background: linear-gradient(135deg, #ffd700, #d4af37);
            box-shadow: 0 6px 12px rgba(255,215,0,0.4);
        }

        .btn-red {
            background: linear-gradient(135deg, #c0392b, #962d22);
            border-color: var(--accent-red);
        }

        .btn-red:hover {
            background: linear-gradient(135deg, #e74c3c, #c0392b);
            box-shadow: 0 6px 12px rgba(231,76,60,0.4);
        }

        /* Views Styles */
        /* 1. Main Menu */
        .menu-view {
            justify-content: space-between;
            align-items: center;
            background: radial-gradient(circle at center, #1e2130 0%, #0f1016 100%);
            text-align: center;
        }

        .menu-header h1 {
            font-size: 3rem;
            color: var(--accent-gold);
            text-shadow: 0 4px 10px rgba(255,215,0,0.3), 0 0 20px rgba(255,215,0,0.2);
            margin-bottom: 5px;
            font-weight: 800;
            letter-spacing: 1px;
        }

        .menu-header p {
            color: var(--text-muted);
            font-size: 1.1rem;
        }

        .menu-buttons {
            display: flex;
            flex-direction: column;
            gap: 15px;
            width: 320px;
            margin: auto 0;
        }

        .menu-footer {
            font-size: 0.85rem;
            color: var(--text-muted);
        }

        /* 2. Character Select Screen */
        .class-select-view h2 {
            font-size: 2rem;
            color: var(--accent-gold);
            text-align: center;
            margin-bottom: 20px;
        }

        .class-grid {
            display: grid;
            grid-template-columns: repeat(3, 1fr);
            gap: 15px;
            flex-grow: 1;
            overflow-y: auto;
            padding-right: 5px;
            margin-bottom: 15px;
        }

        .class-card {
            background-color: rgba(255,255,255,0.03);
            border: 2px solid #34495e;
            border-radius: var(--border-radius);
            padding: 15px;
            text-align: center;
            cursor: pointer;
            transition: all 0.2s ease;
            display: flex;
            flex-direction: column;
            align-items: center;
            justify-content: space-between;
        }

        .class-card:hover {
            background-color: rgba(255,215,0,0.05);
            border-color: var(--accent-gold);
            transform: scale(1.03);
        }

        .class-card.selected {
            border-color: var(--accent-gold);
            background-color: rgba(255,215,0,0.1);
            box-shadow: 0 0 15px rgba(255,215,0,0.2);
        }

        .class-icon {
            font-size: 3rem;
            margin-bottom: 10px;
        }

        .class-name {
            font-size: 1.2rem;
            font-weight: bold;
            color: var(--accent-gold);
            margin-bottom: 5px;
        }

        .class-desc {
            font-size: 0.85rem;
            color: var(--text-muted);
            line-height: 1.3;
        }

        .class-passive {
            margin-top: 10px;
            font-size: 0.8rem;
            color: var(--accent-green);
            background-color: rgba(46,204,113,0.15);
            padding: 4px 8px;
            border-radius: 4px;
            font-weight: bold;
        }

        .class-select-controls {
            display: flex;
            justify-content: space-between;
            align-items: center;
        }

        /* 3. Gameplay View */
        .gameplay-view {
            padding: 0;
            display: flex;
            flex-direction: column;
        }

        /* Upper Stats Header */
        .game-header {
            height: 60px;
            background-color: rgba(0,0,0,0.4);
            border-bottom: 2px solid var(--accent-gold);
            display: flex;
            align-items: center;
            justify-content: space-between;
            padding: 0 20px;
            font-size: 0.9rem;
            z-index: 10;
        }

        .stats-item {
            display: flex;
            align-items: center;
            gap: 6px;
        }

        .bar-container {
            width: 120px;
            height: 14px;
            background-color: #2c3e50;
            border-radius: 7px;
            overflow: hidden;
            border: 1px solid #000;
        }

        .bar-fill {
            height: 100%;
            transition: width 0.3s ease;
        }

        .hp-bar-fill {
            background-color: var(--accent-red);
            width: 100%;
        }

        .shield-bar-fill {
            background-color: #3498db;
            width: 0%;
        }

        .xp-bar-fill {
            background-color: var(--accent-green);
            width: 0%;
        }

        /* Parallax Scrolling Stage */
        .game-stage {
            flex-grow: 1;
            position: relative;
            background: linear-gradient(180deg, #1b263b 0%, #0d1b2a 100%);
            overflow: hidden;
        }

        /* Background elements */
        .layer {
            position: absolute;
            bottom: 60px; /* Ground level */
            width: 200%;
            height: 150px;
            background-repeat: repeat-x;
            background-position: left bottom;
        }

        .sky-layer {
            top: 0;
            height: 100px;
            background-image: radial-gradient(circle at 10% 20%, rgba(255,255,255,0.05) 10%, transparent 10.5%);
            background-size: 150px 150px;
            animation: scrollBg 30s linear infinite;
        }

        .mountain-layer {
            bottom: 60px;
            height: 130px;
            opacity: 0.2;
            background-image: linear-gradient(135deg, transparent 40%, #111 40.5%), linear-gradient(225deg, transparent 40%, #111 40.5%);
            background-size: 120px 120px;
            background-repeat: repeat-x;
            animation: scrollBg 20s linear infinite;
        }

        .forest-layer {
            bottom: 60px;
            height: 90px;
            opacity: 0.4;
            background-image: linear-gradient(145deg, transparent 45%, #2c3e50 45.5%), linear-gradient(215deg, transparent 45%, #2c3e50 45.5%);
            background-size: 60px 60px;
            background-repeat: repeat-x;
            animation: scrollBg 8s linear infinite;
        }

        /* Forest pathway ground */
        .ground {
            position: absolute;
            bottom: 0;
            left: 0;
            width: 100%;
            height: 60px;
            background-color: #3e2723;
            border-top: 6px solid #4e342e;
            box-shadow: inset 0 10px 15px rgba(0,0,0,0.5);
            z-index: 5;
        }

        /* Runner character & Entities */
        .sprite {
            position: absolute;
            bottom: 40px; /* offset above ground top */
            font-size: 3.5rem;
            width: 80px;
            height: 80px;
            display: flex;
            justify-content: center;
            align-items: center;
            user-select: none;
            z-index: 6;
        }

        .player-sprite {
            left: 15%;
            animation: bobbing 0.6s ease-in-out infinite alternate;
            transition: all 0.3s ease;
        }

        .player-sprite.hit {
            animation: shake 0.15s ease-in-out infinite alternate;
            filter: drop-shadow(0 0 10px var(--accent-red)) saturate(200%);
        }

        .player-sprite.attack {
            transform: translateX(50px) scale(1.15);
        }

        .monster-sprite {
            right: -100px; /* start off-screen */
            transition: right 0.1s linear, transform 0.2s ease, opacity 0.3s ease;
        }

        .monster-sprite.bob {
            animation: bobbing 0.8s ease-in-out infinite alternate;
        }

        .monster-sprite.hit-flash {
            filter: drop-shadow(0 0 15px white) invert(100%);
            opacity: 0.5;
        }

        /* Interactive attack projectile */
        .spell-effect {
            position: absolute;
            font-size: 2rem;
            z-index: 7;
            display: none;
            transition: left 0.3s ease, top 0.3s ease;
        }

        /* Text floats (Damage numbers, Combo text) */
        .damage-number {
            position: absolute;
            font-size: 1.5rem;
            font-weight: bold;
            color: var(--accent-red);
            text-shadow: 2px 2px 0px #000;
            z-index: 8;
            opacity: 0;
            animation: floatUp 0.8s ease-out forwards;
        }

        .combo-alert {
            position: absolute;
            left: 50%;
            top: 40px;
            transform: translateX(-50%);
            font-size: 1.8rem;
            font-weight: bold;
            color: var(--accent-gold);
            text-shadow: 2px 2px 4px rgba(0,0,0,0.8);
            z-index: 8;
            animation: bounceIn 0.5s cubic-bezier(0.175, 0.885, 0.32, 1.275) forwards;
            display: none;
        }

        /* Distance track indicator */
        .progress-indicator {
            position: absolute;
            top: 20px;
            left: 50%;
            transform: translateX(-50%);
            background-color: rgba(0,0,0,0.6);
            padding: 5px 15px;
            border-radius: 20px;
            border: 1px solid var(--accent-gold);
            z-index: 6;
            font-size: 0.85rem;
            color: var(--accent-gold);
            font-weight: bold;
            display: flex;
            align-items: center;
            gap: 10px;
        }

        .track-dot {
            width: 10px;
            height: 10px;
            border-radius: 50%;
            background-color: var(--accent-gold);
            display: inline-block;
        }

        /* Question Combat Screen Overlay */
        .combat-overlay {
            position: absolute;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            background-color: rgba(15,16,22,0.85);
            z-index: 100;
            display: none;
            justify-content: center;
            align-items: center;
            padding: 30px;
            backdrop-filter: blur(5px);
        }

        .combat-overlay.active {
            display: flex;
        }

        .question-card {
            width: 100%;
            max-width: 550px;
            background-color: var(--panel-bg);
            border: 3px solid var(--accent-gold);
            border-radius: var(--border-radius);
            box-shadow: 0 10px 25px rgba(0,0,0,0.6);
            display: flex;
            flex-direction: column;
            padding: 25px;
            animation: modalScale 0.3s cubic-bezier(0.175, 0.885, 0.32, 1.275) forwards;
        }

        .question-header {
            text-align: center;
            margin-bottom: 20px;
        }

        .question-label {
            font-size: 0.85rem;
            color: var(--accent-gold);
            text-transform: uppercase;
            font-weight: bold;
            letter-spacing: 2px;
            margin-bottom: 8px;
        }

        .question-word {
            font-size: 2.2rem;
            font-weight: 800;
            color: var(--text-light);
            text-shadow: 0 0 10px rgba(255,255,255,0.1);
        }

        .question-hint {
            font-size: 0.9rem;
            color: var(--accent-blue);
            margin-top: 5px;
            font-style: italic;
            font-weight: bold;
        }

        /* 3.1. Options Mode */
        .options-container {
            display: grid;
            grid-template-columns: 1fr 1fr;
            gap: 12px;
            margin-bottom: 20px;
        }

        .option-btn {
            background-color: #2c3e50;
            border: 2px solid #34495e;
            border-radius: 8px;
            color: var(--text-light);
            padding: 14px;
            font-size: 1rem;
            font-weight: bold;
            cursor: pointer;
            transition: all 0.15s ease;
            text-align: center;
        }

        .option-btn:hover {
            background-color: #34495e;
            border-color: var(--accent-gold);
        }

        .option-btn.correct {
            background-color: rgba(46,204,113,0.3) !important;
            border-color: var(--accent-green) !important;
            color: #fff;
        }

        .option-btn.wrong {
            background-color: rgba(231,76,60,0.3) !important;
            border-color: var(--accent-red) !important;
            color: #fff;
        }

        /* 3.2. Text Typing Mode */
        .typing-container {
            display: flex;
            flex-direction: column;
            gap: 15px;
            margin-bottom: 20px;
        }

        .typing-input {
            width: 100%;
            background-color: #0f1016;
            border: 2px solid #34495e;
            border-radius: 8px;
            color: var(--text-light);
            padding: 14px;
            font-size: 1.1rem;
            text-align: center;
            font-weight: bold;
            outline: none;
            transition: border-color 0.2s ease;
        }

        .typing-input:focus {
            border-color: var(--accent-gold);
        }

        /* Feedback Screen Overlay */
        .feedback-overlay {
            text-align: center;
            padding: 10px 0;
            font-size: 1.1rem;
            font-weight: bold;
            display: none;
        }

        .feedback-overlay.correct-text {
            color: var(--accent-green);
        }

        .feedback-overlay.wrong-text {
            color: var(--accent-red);
        }

        /* 4. Results Screen */
        .results-view h2 {
            font-size: 2.2rem;
            text-align: center;
            margin-bottom: 10px;
        }

        .results-view.victory h2 {
            color: var(--accent-green);
            text-shadow: 0 0 10px rgba(46,204,113,0.3);
        }

        .results-view.defeat h2 {
            color: var(--accent-red);
            text-shadow: 0 0 10px rgba(231,76,60,0.3);
        }

        .results-container {
            display: grid;
            grid-template-columns: 1fr 1fr;
            gap: 20px;
            flex-grow: 1;
            margin-bottom: 20px;
            overflow: hidden;
        }

        .results-stats {
            background-color: rgba(255,255,255,0.02);
            border: 2px solid #34495e;
            border-radius: var(--border-radius);
            padding: 20px;
            display: flex;
            flex-direction: column;
            justify-content: center;
            gap: 12px;
            }

        .result-row {
            display: flex;
            justify-content: space-between;
            font-size: 1.05rem;
            border-bottom: 1px solid rgba(255,255,255,0.05);
            padding-bottom: 8px;
        }

        .result-row span:last-child {
            font-weight: bold;
            color: var(--accent-gold);
        }

        .results-practice {
            background-color: rgba(255,255,255,0.02);
            border: 2px solid #34495e;
            border-radius: var(--border-radius);
            padding: 20px;
            display: flex;
            flex-direction: column;
            overflow: hidden;
        }

        .results-practice h3 {
            font-size: 1.1rem;
            color: var(--accent-gold);
            margin-bottom: 10px;
            border-bottom: 2px solid #34495e;
            padding-bottom: 5px;
        }

        .practice-list {
            list-style: none;
            overflow-y: auto;
            flex-grow: 1;
            display: flex;
            flex-direction: column;
            gap: 8px;
        }

        .practice-item {
            background-color: rgba(231,76,60,0.1);
            border-left: 4px solid var(--accent-red);
            padding: 8px 12px;
            border-radius: 4px;
            font-size: 0.9rem;
            display: flex;
            justify-content: space-between;
        }

        .practice-item span:first-child {
            font-weight: bold;
        }

        .results-controls {
            display: flex;
            justify-content: center;
            gap: 20px;
        }

        /* 5. Editor Screen */
        .editor-view h2 {
            font-size: 2rem;
            color: var(--accent-gold);
            text-align: center;
            margin-bottom: 15px;
        }

        .editor-container {
            display: grid;
            grid-template-columns: 2fr 3fr;
            gap: 20px;
            flex-grow: 1;
            overflow: hidden;
            margin-bottom: 15px;
        }

        .editor-form {
            background-color: rgba(255,255,255,0.02);
            border: 2px solid #34495e;
            border-radius: var(--border-radius);
            padding: 20px;
            display: flex;
            flex-direction: column;
            gap: 12px;
        }

        .form-group {
            display: flex;
            flex-direction: column;
            gap: 5px;
        }

        .form-group label {
            font-size: 0.85rem;
            color: var(--accent-gold);
            font-weight: bold;
        }

        .form-control {
            background-color: #0f1016;
            border: 1px solid #34495e;
            border-radius: 6px;
            color: var(--text-light);
            padding: 10px;
            font-size: 0.95rem;
            outline: none;
        }

        .form-control:focus {
            border-color: var(--accent-gold);
        }

        .editor-list-container {
            background-color: rgba(255,255,255,0.02);
            border: 2px solid #34495e;
            border-radius: var(--border-radius);
            padding: 20px;
            display: flex;
            flex-direction: column;
            overflow: hidden;
        }

        .editor-list-header {
            display: flex;
            justify-content: space-between;
            align-items: center;
            margin-bottom: 10px;
            border-bottom: 2px solid #34495e;
            padding-bottom: 5px;
        }

        .editor-list-header h3 {
            font-size: 1.1rem;
            color: var(--accent-gold);
        }

        .editor-list-header span {
            font-size: 0.8rem;
            color: var(--text-muted);
        }

        .card-table-scroll {
            overflow-y: auto;
            flex-grow: 1;
        }

        .card-table {
            width: 100%;
            border-collapse: collapse;
            font-size: 0.85rem;
            text-align: left;
        }

        .card-table th {
            color: var(--accent-gold);
            border-bottom: 2px solid #34495e;
            padding: 8px;
            font-weight: bold;
        }

        .card-table td {
            padding: 8px;
            border-bottom: 1px solid rgba(255,255,255,0.05);
        }

        .card-table tr:hover {
            background-color: rgba(255,255,255,0.02);
        }

        .action-icon {
            font-size: 1.1rem;
            cursor: pointer;
            user-select: none;
            margin-left: 8px;
        }

        .action-delete {
            color: var(--accent-red);
        }

        .action-delete:hover {
            text-shadow: 0 0 5px rgba(231,76,60,0.5);
        }

        .editor-controls {
            display: flex;
            justify-content: space-between;
        }

        /* 6. Instructions Overlay */
        .instructions-modal {
            position: absolute;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            background-color: rgba(15,16,22,0.95);
            z-index: 200;
            display: none;
            flex-direction: column;
            padding: 30px;
            overflow-y: auto;
        }

        .instructions-modal.active {
            display: flex;
        }

        .instructions-content {
            max-width: 600px;
            margin: 0 auto auto auto;
            line-height: 1.5;
        }

        .instructions-content h2 {
            color: var(--accent-gold);
            font-size: 1.8rem;
            margin-bottom: 15px;
            text-align: center;
        }

        .instructions-content h3 {
            color: var(--accent-gold);
            margin: 15px 0 5px 0;
            font-size: 1.1rem;
        }

        .instructions-content ul {
            list-style-position: inside;
            margin-left: 10px;
            margin-bottom: 10px;
            display: flex;
            flex-direction: column;
            gap: 5px;
        }

        /* Animations Keyframes */
        @keyframes scrollBg {
            from { background-position: 0 0; }
            to { background-position: -600px 0; }
        }

        @keyframes bobbing {
            from { transform: translateY(0); }
            to { transform: translateY(-10px); }
        }

        @keyframes floatUp {
            0% { transform: translateY(0); opacity: 1; }
            100% { transform: translateY(-40px); opacity: 0; }
        }

        @keyframes bounceIn {
            0% { transform: translate(-50%, -20px) scale(0.7); opacity: 0; }
            70% { transform: translate(-50%, 0) scale(1.1); }
            100% { transform: translate(-50%, 0) scale(1); opacity: 1; }
        }

        @keyframes modalScale {
            from { transform: scale(0.85); opacity: 0; }
            to { transform: scale(1); opacity: 1; }
        }

        @keyframes shake {
            0% { transform: translate(2px, 1px) rotate(0deg); }
            10% { transform: translate(-1px, -2px) rotate(-1deg); }
            20% { transform: translate(-3px, 0px) rotate(1deg); }
            30% { transform: translate(0px, 2px) rotate(0deg); }
            40% { transform: translate(1px, -1px) rotate(1deg); }
            50% { transform: translate(-1px, 2px) rotate(-1deg); }
            60% { transform: translate(-3px, 1px) rotate(0deg); }
            70% { transform: translate(2px, 1px) rotate(-1deg); }
            80% { transform: translate(-1px, -1px) rotate(1deg); }
            90% { transform: translate(2px, 2px) rotate(0deg); }
            100% { transform: translate(1px, -2px) rotate(-1deg); }
        }

        /* Responsive styling for Mobile */
        @media (max-width: 600px) {
            .game-container {
                height: 100vh;
                border: none;
                border-radius: 0;
            }

            .class-grid {
                grid-template-columns: 1fr;
            }

            .options-container {
                grid-template-columns: 1fr;
            }

            .editor-container {
                grid-template-columns: 1fr;
            }

            .results-container {
                grid-template-columns: 1fr;
            }
        }
    </style>
</head>
<body>

    <div class="game-container">

        <!-- 1. MAIN MENU SCREEN -->
        <div id="screen-menu" class="screen menu-view active">
            <div class="menu-header">
                <h1>FANTASY QUEST</h1>
                <p>The Gamified Flashcard Adventure</p>
            </div>
            <div class="menu-buttons">
                <button class="btn btn-gold" id="btn-start-adventure">Abenteuer Starten</button>
                <button class="btn" id="btn-card-editor">Karteikarten Editor</button>
                <button class="btn" id="btn-instructions">Anleitung</button>
            </div>
            <div class="menu-footer">
                <p>&copy; 2026 Gemini Notebook Studio &bull; Grounded Learning System</p>
            </div>
        </div>

        <!-- 2. CHARACTER SELECTION SCREEN -->
        <div id="screen-class-select" class="screen class-select-view">
            <h2>Wähle deine Abenteurer-Klasse</h2>
            <div class="class-grid" id="class-grid-container">
                <!-- Populated dynamically via JS -->
            </div>
            <div class="class-select-controls">
                <button class="btn btn-red" id="btn-class-back">Zurück</button>
                <button class="btn btn-gold" id="btn-class-confirm" disabled>Abenteuer Starten!</button>
            </div>
        </div>

        <!-- 3. GAMEPLAY SCREEN -->
        <div id="screen-gameplay" class="screen gameplay-view">
            <!-- Game Header Bar -->
            <div class="game-header">
                <div class="stats-item">
                    <span>❤️ Leben:</span>
                    <div class="bar-container">
                        <div id="hp-bar-fill" class="bar-fill hp-bar-fill"></div>
                    </div>
                    <span id="hp-text">4/4</span>
                </div>
                <div class="stats-item" id="shield-item" style="display: none;">
                    <span>🛡️ Schild:</span>
                    <div class="bar-container">
                        <div id="shield-bar-fill" class="bar-fill shield-bar-fill"></div>
                    </div>
                    <span id="shield-text">2/2</span>
                </div>
                <div class="stats-item">
                    <span>⭐ Level <span id="game-level">1</span>:</span>
                    <div class="bar-container">
                        <div id="xp-bar-fill" class="bar-fill xp-bar-fill"></div>
                    </div>
                </div>
                <div class="stats-item">
                    <span>🪙 Gold: <span id="game-coins" style="color:var(--accent-gold); font-weight:bold;">0</span></span>
                </div>
            </div>

            <!-- Parallax Scrolling Stage Area -->
            <div class="game-stage" id="game-stage">
                <div class="layer sky-layer"></div>
                <div class="layer mountain-layer"></div>
                <div class="layer forest-layer"></div>
                <div class="ground"></div>

                <!-- Distance Track Display -->
                <div class="progress-indicator">
                    <span class="track-dot"></span>
                    <span>Weg bis zum Boss: <span id="distance-text">0m / 500m</span></span>
                </div>

                <!-- Floating/Alert Effects -->
                <div class="combo-alert" id="combo-alert">3x Combo! 🔥</div>

                <!-- Characters sprites -->
                <div class="sprite player-sprite" id="player-sprite">🛡️</div>
                <div class="sprite monster-sprite bob" id="monster-sprite">👹</div>
                
                <!-- Projectile Spell/Attack effect -->
                <div class="spell-effect" id="spell-effect">🔥</div>
            </div>

            <!-- Combat Quiz Overlay Modal -->
            <div class="combat-overlay" id="combat-overlay">
                <div class="question-card" id="question-card">
                    <div class="question-header">
                        <div class="question-label" id="question-type-label">Vokabel Abfrage</div>
                        <div class="question-word" id="question-word">le genou</div>
                        <div class="question-hint" id="question-hint"></div>
                    </div>

                    <!-- Mode A: Multiple Choice Container -->
                    <div class="options-container" id="options-container" style="display: none;">
                        <button class="option-btn">Option 1</button>
                        <button class="option-btn">Option 2</button>
                        <button class="option-btn">Option 3</button>
                        <button class="option-btn">Option 4</button>
                    </div>

                    <!-- Mode B: Typing Container -->
                    <div class="typing-container" id="typing-container" style="display: none;">
                        <input type="text" class="typing-input" id="typing-input" placeholder="Übersetzung hier eintippen..." autocomplete="off">
                        <button class="btn btn-gold" id="btn-submit-typing">Antwort senden</button>
                    </div>

                    <!-- Combat Screen Feedback -->
                    <div class="feedback-overlay" id="combat-feedback"></div>
                </div>
            </div>
        </div>

        <!-- 4. RESULTS / SUMMARY SCREEN -->
        <div id="screen-results" class="screen results-view">
            <h2 id="results-headline">Abenteuer Beendet!</h2>
            <div class="results-container">
                <div class="results-stats">
                    <div class="result-row">
                        <span>Ergebnis:</span>
                        <span id="res-status">Sieg</span>
                    </div>
                    <div class="result-row">
                        <span>Zurückgelegter Weg:</span>
                        <span id="res-distance">500m</span>
                    </div>
                    <div class="result-row">
                        <span>Genauigkeit:</span>
                        <span id="res-accuracy">85%</span>
                    </div>
                    <div class="result-row">
                        <span>Beantwortete Fragen:</span>
                        <span id="res-questions">12</span>
                    </div>
                    <div class="result-row">
                        <span>Gesammeltes Gold:</span>
                        <span id="res-gold">120 🪙</span>
                    </div>
                    <div class="result-row">
                        <span>Erfahrungspunkte (XP):</span>
                        <span id="res-xp">+450 XP</span>
                    </div>
                </div>
                <div class="results-practice">
                    <h3>🔍 Diese Vokabeln solltest du üben:</h3>
                    <ul class="practice-list" id="practice-list-container">
                        <!-- Missed vocabulary entries populated dynamically -->
                    </ul>
                </div>
            </div>
            <div class="results-controls">
                <button class="btn btn-gold" id="btn-results-retry">Nochmal Spielen</button>
                <button class="btn" id="btn-results-menu">Hauptmenü</button>
            </div>
        </div>

        <!-- 5. CARD EDITOR SCREEN -->
        <div id="screen-editor" class="screen editor-view">
            <h2>Lernset & Karteikarten Editor</h2>
            <div class="editor-container">
                <div class="editor-form">
                    <h3 style="font-size:1.1rem; color:var(--accent-gold); border-bottom:1px solid #34495e; padding-bottom:5px;">Neue Vokabel hinzufügen</h3>
                    
                    <div class="form-group">
                        <label for="input-front">Fremdwort (Vorderseite):</label>
                        <input type="text" id="input-front" class="form-control" placeholder="z.B. la tête" required>
                    </div>

                    <div class="form-group">
                        <label for="input-back">Übersetzung (Rückseite):</label>
                        <input type="text" id="input-back" class="form-control" placeholder="z.B. der Kopf" required>
                    </div>

                    <div class="form-group">
                        <label for="input-mode">Bevorzugter Abfragetyp:</label>
                        <select id="input-mode" class="form-control">
                            <option value="any">Zufällig (Choice oder Schreiben)</option>
                            <option value="choice">Nur Multiple-Choice</option>
                            <option value="text">Nur schriftliche Eingabe</option>
                        </select>
                    </div>

                    <button class="btn btn-gold" style="margin-top:10px;" id="btn-add-card">Karte Speichern</button>
                </div>
                <div class="editor-list-container">
                    <div class="editor-list-header">
                        <h3>Aktuelles Lernset</h3>
                        <span>Karten gesamt: <span id="card-count">0</span></span>
                    </div>
                    <div class="card-table-scroll">
                        <table class="card-table">
                            <thead>
                                <tr>
                                    <th>Fremdwort</th>
                                    <th>Übersetzung</th>
                                    <th>Typ</th>
                                    <th>Aktion</th>
                                </tr>
                            </thead>
                            <tbody id="editor-card-list-body">
                                <!-- Cards dynamically listed -->
                            </tbody>
                        </table>
                    </div>
                </div>
            </div>
            <div class="editor-controls">
                <button class="btn btn-red" id="btn-reset-cards">Zurücksetzen (Standard)</button>
                <button class="btn btn-gold" id="btn-editor-back">Hauptmenü</button>
            </div>
        </div>

        <!-- 6. INSTRUCTIONS OVERLAY -->
        <div class="instructions-modal" id="instructions-view">
            <div class="instructions-content">
                <h2>Spielanleitung & Klassen</h2>
                <div style="margin-top:15px; color: var(--text-muted); display:flex; flex-direction:column; gap:12px;">
                    <p>Willkommen bei <strong>Fantasy Quest!</strong></p>
                    <p>Der Charakter läuft automatisch vorwärts. Auf deinem Weg begegnest du gefährlichen Monstern. Sobald ein Kampf beginnt, musst du die angezeigte Vokabel richtig übersetzen, um anzugreifen!</p>
                    
                    <h3>⚔️ Die Klassen und ihre Spezialfähigkeiten:</h3>
                    <ul>
                        <li><strong>Krieger (Warrior):</strong> Reduziert jeden erlittenen Schaden durch falsche Antworten um 50% (Schild-Block).</li>
                        <li><strong>Magier (Mage):</strong> Erhält Hilfestellung! Eliminiert automatisch eine falsche Antwortmöglichkeit oder zeigt bei Texteingabe den ersten Buchstaben als Tipp.</li>
                        <li><strong>Waldläufer (Ranger):</strong> Bei einer Serie von 3+ richtigen Antworten verdoppelt sich das gesammelte Gold und die Punktzahl.</li>
                        <li><strong>Schurke (Rogue):</strong> Tarnt sich! Fängt den ersten Fehler pro Runde vollständig ab, ohne Leben zu verlieren.</li>
                        <li><strong>Heiler (Healer):</strong> Regeneriert 1 verlorenes Leben nach jeweils 3 aufeinanderfolgenden korrekten Antworten.</li>
                        <li><strong>Paladin:</strong> Startet das Abenteuer mit einem zusätzlichen heiligen Schild von +2 temporären HP.</li>
                    </ul>

                    <h3>💡 Intelligentes Lernen:</h3>
                    <p>Das Spiel merkt sich Vokabeln, die du falsch beantwortest. Sie tauchen im Verlauf des Abenteuers häufiger auf. Im Ergebnis-Bildschirm kannst du diese schwierigen Wörter gezielt rekapitulieren!</p>
                </div>
            </div>
            <div style="text-align: center; margin-top:20px;">
                <button class="btn btn-gold" id="btn-close-instructions">Verstanden!</button>
            </div>
        </div>

    </div>

    <!-- SOUND ENGINE (Built-in Browser Synthesizer via Web Audio API) -->
    <script>
        class SoundEngine {
            constructor() {
                this.ctx = null;
            }

            init() {
                if (!this.ctx) {
                    this.ctx = new (window.AudioContext || window.webkitAudioContext)();
                }
            }

            playCorrect() {
                this.init();
                if (!this.ctx) return;
                const now = this.ctx.currentTime;
                
                // Beep 1
                const osc1 = this.ctx.createOscillator();
                const gain = this.ctx.createGain();
                osc1.type = 'sine';
                osc1.frequency.setValueAtTime(523.25, now); // C5
                osc1.frequency.setValueAtTime(659.25, now + 0.1); // E5
                osc1.frequency.setValueAtTime(783.99, now + 0.2); // G5
                
                gain.gain.setValueAtTime(0.15, now);
                gain.gain.exponentialRampToValueAtTime(0.01, now + 0.45);
                
                osc1.connect(gain);
                gain.connect(this.ctx.destination);
                osc1.start(now);
                osc1.stop(now + 0.5);
            }

            playHit() {
                this.init();
                if (!this.ctx) return;
                const now = this.ctx.currentTime;
                const osc = this.ctx.createOscillator();
                const gain = this.ctx.createGain();
                
                osc.type = 'sawtooth';
                osc.frequency.setValueAtTime(150, now);
                osc.frequency.linearRampToValueAtTime(40, now + 0.2);
                
                gain.gain.setValueAtTime(0.2, now);
                gain.gain.exponentialRampToValueAtTime(0.01, now + 0.25);
                
                osc.connect(gain);
                gain.connect(this.ctx.destination);
                osc.start(now);
                osc.stop(now + 0.3);
            }

            playWrong() {
                this.init();
                if (!this.ctx) return;
                const now = this.ctx.currentTime;
                const osc = this.ctx.createOscillator();
                const gain = this.ctx.createGain();
                
                osc.type = 'sawtooth';
                osc.frequency.setValueAtTime(180, now);
                osc.frequency.setValueAtTime(130, now + 0.15);
                
                gain.gain.setValueAtTime(0.2, now);
                gain.gain.exponentialRampToValueAtTime(0.01, now + 0.4);
                
                osc.connect(gain);
                gain.connect(this.ctx.destination);
                osc.start(now);
                osc.stop(now + 0.45);
            }

            playVictory() {
                this.init();
                if (!this.ctx) return;
                const now = this.ctx.currentTime;
                const notes = [523.25, 659.25, 783.99, 1046.50]; // C5, E5, G5, C6
                notes.forEach((freq, idx) => {
                    const osc = this.ctx.createOscillator();
                    const gain = this.ctx.createGain();
                    osc.type = 'triangle';
                    osc.frequency.setValueAtTime(freq, now + idx * 0.12);
                    gain.gain.setValueAtTime(0.15, now + idx * 0.12);
                    gain.gain.exponentialRampToValueAtTime(0.01, now + idx * 0.12 + 0.4);
                    osc.connect(gain);
                    gain.connect(this.ctx.destination);
                    osc.start(now + idx * 0.12);
                    osc.stop(now + idx * 0.12 + 0.5);
                });
            }

            playSpell() {
                this.init();
                if (!this.ctx) return;
                const now = this.ctx.currentTime;
                const osc = this.ctx.createOscillator();
                const gain = this.ctx.createGain();
                
                osc.type = 'sine';
                osc.frequency.setValueAtTime(300, now);
                osc.frequency.exponentialRampToValueAtTime(1200, now + 0.35);
                
                gain.gain.setValueAtTime(0.12, now);
                gain.gain.exponentialRampToValueAtTime(0.01, now + 0.4);
                
                osc.connect(gain);
                gain.connect(this.ctx.destination);
                osc.start(now);
                osc.stop(now + 0.45);
            }
        }

        const SFX = new SoundEngine();
    </script>

    <!-- CORE GAME GAMEPLAY LOGIC & STATE MACHINE -->
    <script>
        // Character Classes Configuration
        const HERO_CLASSES = [
            {
                id: 'warrior',
                name: 'Krieger (Warrior)',
                icon: '🛡️',
                desc: 'Ein gepanzerter Frontkämpfer.',
                passive: 'Schild-Block: Reduziert jeglichen Schaden durch falsche Antworten um 50%.'
            },
            {
                id: 'mage',
                name: 'Magier (Mage)',
                icon: '🔮',
                desc: 'Meister der elementaren Zauberei.',
                passive: 'Zauberfokus: Eliminiert eine falsche Choice-Option, oder gibt den ersten Buchstaben als Tipp.'
            },
            {
                id: 'ranger',
                name: 'Waldläufer (Ranger)',
                icon: '🏹',
                desc: 'Treffsicherer Scharfschütze.',
                passive: 'Schnellfeuer: Combo-Serien (3+) verdoppeln das verdiente Gold und die Punkte.'
            },
            {
                id: 'rogue',
                name: 'Schurke (Rogue)',
                icon: '🗡️',
                desc: 'Arbeitet aus den Schatten heraus.',
                passive: 'Schattenmantel: Weicht dem allerersten Fehler pro Abenteuer ohne Schaden aus.'
            },
            {
                id: 'healer',
                name: 'Heiler (Healer)',
                icon: '🌿',
                desc: 'Nutzt die heilenden Mächte der Natur.',
                passive: 'Heilaura: Jede 3 aufeinanderfolgende richtige Antworten heilen 1 HP.'
            },
            {
                id: 'paladin',
                name: 'Paladin (Paladin)',
                icon: '✨',
                desc: 'Heiliger Ritter des Lichts.',
                passive: 'Göttlicher Schild: Startet das Abenteuer mit 2 zusätzlichen blauen Schild-HP.'
            }
        ];

        // Default Flashcards (French Body Parts)
        const DEFAULT_CARDS = [
            { front: 'la tête', back: 'der Kopf', type: 'any' },
            { front: 'le genou', back: 'das Knie', type: 'any' },
            { front: 'les yeux', back: 'die Augen', type: 'any' },
            { front: 'le bras', back: 'der Arm', type: 'any' },
            { front: 'la main', back: 'die Hand', type: 'any' },
            { front: 'le pied', back: 'der Fuß', type: 'any' },
            { front: 'le nez', back: 'die Nase', type: 'any' },
            { front: 'les oreilles', back: 'die Ohren', type: 'any' },
            { front: 'le dos', back: 'der Rücken', type: 'any' },
            { front: 'la jambe', back: 'das Bein', type: 'any' },
            { front: 'le ventre', back: 'der Bauch', type: 'any' },
            { front: 'la cheville', back: 'der Knöchel', type: 'any' }
        ];

        // Monsters configurations
        const MONSTERS = [
            { icon: '🟢', name: 'Schleim' },
            { icon: '👺', name: 'Goblin' },
            { icon: '💀', name: 'Skelett-Krieger' },
            { icon: '🐺', name: 'Schattenwolf' },
            { icon: '🧚', name: 'Irrlicht' }
        ];

        // Boss monster configuration
        const BOSS = { icon: '🐉', name: 'Feuerdrache' };

        // Game State Variables
        let flashcards = [];
        let missedCards = []; // Tracks cards missed in current run
        let selectedClass = null;

        // Current Run Variables
        let currentHp = 4;
        let maxHp = 4;
        let currentShield = 0; // Temporary HP for Paladin
        let currentCoins = 0;
        let currentXp = 0;
        let score = 0;
        let currentCombo = 0;
        let level = 1;
        
        let currentDistance = 0;
        const totalDistance = 500; // Walk limit to reach boss
        let isRunnerActive = false;
        let runnerInterval = null;
        let monsterActive = false;
        let currentQuestionCard = null;
        let comboHighScore = 0;
        let statsCorrectCount = 0;
        let statsTotalCount = 0;

        // Adaptive weights for learning (memorizes incorrect cards to ask them sooner)
        let learningWeights = {};

        // DOM elements
        const screenMenu = document.getElementById('screen-menu');
        const screenClassSelect = document.getElementById('screen-class-select');
        const screenGameplay = document.getElementById('screen-gameplay');
        const screenResults = document.getElementById('screen-results');
        const screenEditor = document.getElementById('screen-editor');
        const viewInstructions = document.getElementById('instructions-view');

        // Sounds enable
        let soundInitialized = false;

        // Initialize Cards
        function initCards() {
            const stored = localStorage.getItem('fantasy_quest_cards');
            if (stored) {
                try {
                    flashcards = JSON.parse(stored);
                } catch(e) {
                    flashcards = [...DEFAULT_CARDS];
                }
            } else {
                flashcards = [...DEFAULT_CARDS];
                localStorage.setItem('fantasy_quest_cards', JSON.stringify(flashcards));
            }
            updateEditorList();
        }

        // Setup View Switcher
        function showScreen(screenId) {
            document.querySelectorAll('.screen').forEach(s => s.classList.remove('active'));
            document.getElementById(screenId).classList.add('active');
        }

        // Render Character Class list
        function renderClassGrid() {
            const container = document.getElementById('class-grid-container');
            container.innerHTML = '';
            HERO_CLASSES.forEach(hc => {
                const card = document.createElement('div');
                card.className = 'class-card';
                card.id = `class-card-${hc.id}`;
                card.innerHTML = `
                    <div>
                        <div class="class-icon">${hc.icon}</div>
                        <div class="class-name">${hc.name}</div>
                        <div class="class-desc">${hc.desc}</div>
                    </div>
                    <div class="class-passive">${hc.passive}</div>
                `;
                card.onclick = () => selectClass(hc);
                container.appendChild(card);
            });
        }

        function selectClass(hc) {
            selectedClass = hc;
            document.querySelectorAll('.class-card').forEach(c => c.classList.remove('selected'));
            document.getElementById(`class-card-${hc.id}`).classList.add('selected');
            document.getElementById('btn-class-confirm').removeAttribute('disabled');
            SFX.playCorrect();
        }

        // Setup Event Listeners
        window.onload = () => {
            initCards();
            renderClassGrid();

            // Menu View buttons
            document.getElementById('btn-start-adventure').onclick = () => {
                showScreen('screen-class-select');
            };

            document.getElementById('btn-card-editor').onclick = () => {
                showScreen('screen-editor');
            };

            document.getElementById('btn-instructions').onclick = () => {
                viewInstructions.classList.add('active');
            };

            document.getElementById('btn-close-instructions').onclick = () => {
                viewInstructions.classList.remove('active');
            };

            // Class Select back button
            document.getElementById('btn-class-back').onclick = () => {
                showScreen('screen-menu');
            };

            // Class Select confirm button
            document.getElementById('btn-class-confirm').onclick = () => {
                startNewRun();
            };

            // Results back buttons
            document.getElementById('btn-results-retry').onclick = () => {
                showScreen('screen-class-select');
            };

            document.getElementById('btn-results-menu').onclick = () => {
                showScreen('screen-menu');
            };

            // Editor back button
            document.getElementById('btn-editor-back').onclick = () => {
                showScreen('screen-menu');
            };

            // Add Card
            document.getElementById('btn-add-card').onclick = addNewCard;

            // Reset Default Cards
            document.getElementById('btn-reset-cards').onclick = resetDefaultCards;

            // Submit typing question
            document.getElementById('btn-submit-typing').onclick = checkTypingAnswer;
            document.getElementById('typing-input').onkeypress = (e) => {
                if (e.key === 'Enter') checkTypingAnswer();
            };
        };

        // Start Adventure Run
        function startNewRun() {
            // Stats resets
            currentHp = 4;
            maxHp = 4;
            currentShield = 0;
            currentCoins = 0;
            currentXp = 0;
            score = 0;
            currentCombo = 0;
            level = 1;
            currentDistance = 0;
            monsterActive = false;
            missedCards = [];
            statsCorrectCount = 0;
            statsTotalCount = 0;
            comboHighScore = 0;

            // Class modifications
            if (selectedClass.id === 'paladin') {
                currentShield = 2;
                document.getElementById('shield-item').style.display = 'flex';
                updateShieldBar();
            } else {
                document.getElementById('shield-item').style.display = 'none';
            }

            // Set player sprite icon
            document.getElementById('player-sprite').innerHTML = selectedClass.icon;

            updateHpBar();
            updateXpBar();
            document.getElementById('game-coins').innerText = currentCoins;
            document.getElementById('game-level').innerText = level;

            showScreen('screen-gameplay');
            startNewRun.rogueShieldTriggered = false; // Reset Rogue Shield state on start
            startRunnerLoop();
        }

        // Runner Loop Logic
        function startRunnerLoop() {
            isRunnerActive = true;
            toggleBackgroundAnimation(true);

            runnerInterval = setInterval(() => {
                if (!isRunnerActive) return;

                // Move forward
                currentDistance += 10;
                document.getElementById('distance-text').innerText = `${currentDistance}m / ${totalDistance}m`;

                // Spawn forest layers bobbing effect (mocked by active animation css class)
                
                // Triggers encounters
                if (currentDistance % 100 === 0 && currentDistance < totalDistance) {
                    triggerMonsterEncounter();
                } else if (currentDistance >= totalDistance) {
                    triggerBossEncounter();
                }
            }, 300);
        }

        function stopRunnerLoop() {
            isRunnerActive = false;
            clearInterval(runnerInterval);
            toggleBackgroundAnimation(false);
        }

        function toggleBackgroundAnimation(play) {
            const playState = play ? 'running' : 'paused';
            document.querySelectorAll('.layer').forEach(layer => {
                layer.style.animationPlayState = playState;
            });
            const player = document.getElementById('player-sprite');
            player.style.animationPlayState = playState;
        }

        // Monster Spawning
        function triggerMonsterEncounter() {
            stopRunnerLoop();
            monsterActive = true;

            // Pick random monster
            const m = MONSTERS[Math.floor(Math.random() * MONSTERS.length)];
            const sprite = document.getElementById('monster-sprite');
            sprite.innerHTML = m.icon;
            sprite.style.opacity = '1';
            sprite.style.right = '20%'; // slide to screen position

            // Ask vocabulary
            setTimeout(() => {
                presentVocabularyQuestion(false);
            }, 600);
        }

        // Boss Spawning
        function triggerBossEncounter() {
            stopRunnerLoop();
            monsterActive = true;

            const sprite = document.getElementById('monster-sprite');
            sprite.innerHTML = BOSS.icon;
            sprite.style.opacity = '1';
            sprite.style.right = '22%';
            sprite.style.transform = 'scale(1.6)'; // Boss looks huge

            // Show alert
            const alert = document.getElementById('combo-alert');
            alert.innerText = "🚨 BOSS KAMPF! 🚨";
            alert.style.color = "var(--accent-red)";
            alert.style.display = 'block';

            setTimeout(() => {
                alert.style.display = 'none';
                presentVocabularyQuestion(true); // Boss mode
            }, 1500);
        }

        // Generate Quiz Item (Choice/Type)
        function presentVocabularyQuestion(isBoss) {
            if (flashcards.length === 0) {
                alert("Bitte füge zuerst Karteikarten im Editor hinzu!");
                showScreen('screen-editor');
                return;
            }

            // Pick a card using weighted random to prioritize missed cards
            currentQuestionCard = selectWeightedCard();

            // Decide question type (choice or text input)
            let qType = currentQuestionCard.type;
            if (qType === 'any') {
                qType = Math.random() < 0.6 ? 'choice' : 'text';
            }

            // Display overlay
            const overlay = document.getElementById('combat-overlay');
            overlay.classList.add('active');

            const lbl = document.getElementById('question-type-label');
            lbl.innerText = isBoss ? "🐉 BOSS-ABFRAGE!" : "⚔️ MONSTER-BEGEGNUNG";
            lbl.style.color = isBoss ? "var(--accent-red)" : "var(--accent-gold)";

            document.getElementById('question-word').innerText = currentQuestionCard.front;
            
            // Clean up hints
            const hintEl = document.getElementById('question-hint');
            hintEl.innerText = '';

            // Mage special passive spell elimination or text hint
            let extraHint = '';
            if (selectedClass.id === 'mage') {
                if (qType === 'text') {
                    // Show first letter as hint
                    const firstLetter = currentQuestionCard.back.trim().substring(0, 1);
                    extraHint = `🧙‍♂️ Magier-Weisheit: Das Wort beginnt mit '${firstLetter}'`;
                } else {
                    extraHint = `🧙‍♂️ Magier-Weisheit: Konzentriere dich auf die Magie!`;
                }
            }

            if (extraHint) {
                hintEl.innerText = extraHint;
            }

            if (qType === 'choice') {
                setupMultipleChoiceQuestion(currentQuestionCard, isBoss);
            } else {
                setupTypingQuestion(currentQuestionCard);
            }
        }

        // Prioritized Selection algorithm based on learning errors
        function selectWeightedCard() {
            // Sort or construct pools
            // For simple weight: list cards with weight > 1 more times
            let pool = [];
            flashcards.forEach(card => {
                const weight = learningWeights[card.front] || 1;
                for (let i = 0; i < weight; i++) {
                    pool.push(card);
                }
            });
            return pool[Math.floor(Math.random() * pool.length)];
        }

        // Build 4 Multiple Choice buttons
        function setupMultipleChoiceQuestion(card, isBoss) {
            document.getElementById('options-container').style.display = 'grid';
            document.getElementById('typing-container').style.display = 'none';

            // Gather options
            const correctText = card.back;
            let optionsPool = flashcards.filter(c => c.back !== correctText).map(c => c.back);
            // Shuffle pool
            optionsPool.sort(() => Math.random() - 0.5);

            // Get 3 random unique wrong choices
            let choices = [];
            for (let i = 0; i < optionsPool.length && choices.length < 3; i++) {
                if (!choices.includes(optionsPool[i])) {
                    choices.push(optionsPool[i]);
                }
            }
            choices.push(correctText);
            choices.sort(() => Math.random() - 0.5); // shuffle list with correct option

            const btns = document.querySelectorAll('.option-btn');
            
            // In case Mage removes an option
            let eliminatedIdx = -1;
            if (selectedClass.id === 'mage' && choices.length > 2) {
                // Eliminate one wrong index
                const wrongIndices = [];
                choices.forEach((c, idx) => {
                    if (c !== correctText) wrongIndices.push(idx);
                });
                eliminatedIdx = wrongIndices[Math.floor(Math.random() * wrongIndices.length)];
            }

            btns.forEach((btn, idx) => {
                btn.style.display = 'block';
                btn.className = 'option-btn'; // reset styles
                if (idx < choices.length) {
                    btn.innerText = choices[idx];
                    btn.onclick = () => checkChoiceAnswer(choices[idx], correctText, btn, btns);
                    
                    if (idx === eliminatedIdx) {
                        btn.style.opacity = '0.35';
                        btn.style.pointerEvents = 'none';
                        btn.innerText = "❌ (Magie-Effekt)";
                    } else {
                        btn.style.opacity = '1';
                        btn.style.pointerEvents = 'auto';
                    }
                } else {
                    btn.style.display = 'none';
                }
            });
        }

        // Build Typing input
        function setupTypingQuestion(card) {
            document.getElementById('options-container').style.display = 'none';
            document.getElementById('typing-container').style.display = 'flex';
            
            const input = document.getElementById('typing-input');
            input.value = '';
            input.className = 'typing-input';
            input.style.borderColor = '#34495e';
            input.focus();
        }

        // Choice checking
        function checkChoiceAnswer(selected, correct, clickedBtn, allBtns) {
            allBtns.forEach(btn => {
                btn.style.pointerEvents = 'none'; // stop further clicks
                if (btn.innerText === correct) {
                    btn.classList.add('correct');
                }
            });

            if (selected === correct) {
                clickedBtn.classList.add('correct');
                handleCorrectAnswer();
            } else {
                clickedBtn.classList.add('wrong');
                handleWrongAnswer(correct);
            }
        }

        // Typing checking with automatic toleration
        function checkTypingAnswer() {
            const input = document.getElementById('typing-input');
            const entered = input.value.trim().toLowerCase();
            const correctText = currentQuestionCard.back.trim().toLowerCase();

            // Simple normalizer (strips accents and articles for toleration scoring)
            const cleanText = (str) => {
                return str.normalize("NFD")
                          .replace(/[\u0300-\u036f]/g, "") // removes accents (properly escaped backslash)
                          .replace(/^(der|die|das|un|une|le|la|les|the|a|an)\s+/i, "") // strips articles
                          .replace(/\s+/g, "");
            };

            const isCorrect = cleanText(entered) === cleanText(correctText);

            if (isCorrect) {
                input.className = 'typing-input correct';
                input.style.borderColor = 'var(--accent-green)';
                handleCorrectAnswer();
            } else {
                input.className = 'typing-input wrong';
                input.style.borderColor = 'var(--accent-red)';
                handleWrongAnswer(currentQuestionCard.back);
            }
        }

        // Core Correct Answer Event
        function handleCorrectAnswer() {
            SFX.playCorrect();
            statsCorrectCount++;
            statsTotalCount++;
            currentCombo++;

            if (currentCombo > comboHighScore) {
                comboHighScore = currentCombo;
            }

            // Lower weight since user answered correctly
            learningWeights[currentQuestionCard.front] = Math.max(1, (learningWeights[currentQuestionCard.front] || 1) - 1);

            // Play spell/projectile effect
            SFX.playSpell();
            const spell = document.getElementById('spell-effect');
            spell.style.display = 'block';
            spell.style.left = '20%';
            spell.style.top = '50%';
            
            // Flying spell animation towards monster
            setTimeout(() => {
                spell.style.left = '75%';
                spell.style.top = '50%';
            }, 50);

            setTimeout(() => {
                spell.style.display = 'none';
                
                // Damage numbers flash
                showDamageNumber("💥 TREFFER!", 'var(--accent-green)');
                
                // Flash monster
                const m = document.getElementById('monster-sprite');
                m.classList.add('hit-flash');

                // Ranger passive combo earnings
                let goldEarned = 10;
                let xpEarned = 25;
                if (selectedClass.id === 'ranger' && currentCombo >= 3) {
                    goldEarned *= 2;
                    xpEarned *= 1.5;
                }

                currentCoins += goldEarned;
                document.getElementById('game-coins').innerText = currentCoins;

                gainXp(xpEarned);

                // Healer passive healing
                if (selectedClass.id === 'healer' && currentCombo % 3 === 0) {
                    if (currentHp < maxHp) {
                        currentHp++;
                        updateHpBar();
                        showDamageNumber("💚 HEILUNG +1 HP!", 'var(--accent-green)');
                    }
                }

                // Combo Fire Alert
                if (currentCombo >= 3) {
                    const alert = document.getElementById('combo-alert');
                    alert.innerText = `${currentCombo}x Combo! 🔥`;
                    alert.style.color = "var(--accent-gold)";
                    alert.style.display = 'block';
                }

                setTimeout(() => {
                    m.classList.remove('hit-flash');
                    m.style.opacity = '0';
                    m.style.right = '-100px'; // hide off screen

                    closeCombatOverlay();

                    // If final distance reached, victory!
                    if (currentDistance >= totalDistance) {
                        SFX.playVictory();
                        endAdventureRun(true);
                    } else {
                        startRunnerLoop();
                    }
                }, 400);

            }, 400);
        }

        // Core Wrong Answer Event
        function handleWrongAnswer(correctTranslation) {
            SFX.playWrong();
            statsTotalCount++;
            currentCombo = 0;
            document.getElementById('combo-alert').style.display = 'none';

            // Increase weight so incorrect card appears sooner
            learningWeights[currentQuestionCard.front] = (learningWeights[currentQuestionCard.front] || 1) + 2;

            // Track card for practice view
            if (!missedCards.some(c => c.front === currentQuestionCard.front)) {
                missedCards.push(currentQuestionCard);
            }

            // Display explanations overlay text
            const feedback = document.getElementById('combat-feedback');
            feedback.innerHTML = `⚠️ Falsch! Richtig: <span style="text-shadow: 0 0 10px rgba(255,255,255,0.2);">${correctTranslation}</span>`;
            feedback.style.display = 'block';

            // Rogue Schattenmantel passive wrong answer avoidance
            if (selectedClass.id === 'rogue' && !startNewRun.rogueShieldTriggered) {
                startNewRun.rogueShieldTriggered = true;
                showDamageNumber("🛡️ AUSGEWICHEN!", 'var(--accent-blue)');
                setTimeout(() => {
                    resumeGameplayAfterWrong();
                }, 2000);
                return;
            }

            // Damage logic
            let baseDamage = 1;
            
            // Warrior shield passive reduces damage
            if (selectedClass.id === 'warrior') {
                baseDamage = 0.5; // Absorbs 50%
            }

            // Deal damage, first to Paladin shield
            if (currentShield > 0) {
                currentShield -= baseDamage;
                if (currentShield < 0) {
                    currentHp += currentShield; // spill over
                    currentShield = 0;
                }
                updateShieldBar();
            } else {
                currentHp -= baseDamage;
            }

            updateHpBar();

            // Flash Screen & Shake player
            const player = document.getElementById('player-sprite');
            player.classList.add('hit');
            showDamageNumber(`💥 SCHADEN -${baseDamage} HP!`, 'var(--accent-red)');

            setTimeout(() => {
                player.classList.remove('hit');
                
                if (currentHp <= 0) {
                    // Death, end run
                    closeCombatOverlay();
                    endAdventureRun(false);
                } else {
                    resumeGameplayAfterWrong();
                }
            }, 2000);
        }

        function resumeGameplayAfterWrong() {
            closeCombatOverlay();
            
            // Monster flies back off-screen
            const m = document.getElementById('monster-sprite');
            m.style.opacity = '0';
            m.style.right = '-100px';

            startRunnerLoop();
        }

        function closeCombatOverlay() {
            document.getElementById('combat-overlay').classList.remove('active');
            document.getElementById('combat-feedback').style.display = 'none';
        }

        // Stats updates
        function updateHpBar() {
            const fill = document.getElementById('hp-bar-fill');
            const text = document.getElementById('hp-text');
            const percent = (currentHp / maxHp) * 100;
            fill.style.width = `${Math.max(0, percent)}%`;
            text.innerText = `${currentHp}/${maxHp}`;
        }

        function updateShieldBar() {
            const fill = document.getElementById('shield-bar-fill');
            const text = document.getElementById('shield-text');
            const percent = (currentShield / 2) * 100;
            fill.style.width = `${Math.max(0, percent)}%`;
            text.innerText = `${currentShield}/2`;
        }

        function updateXpBar() {
            const fill = document.getElementById('xp-bar-fill');
            const percent = (currentXp / 100) * 100;
            fill.style.width = `${percent}%`;
        }

        function gainXp(amount) {
            currentXp += amount;
            if (currentXp >= 100) {
                currentXp -= 100;
                level++;
                document.getElementById('game-level').innerText = level;
                showDamageNumber("🎉 LEVEL UP!", 'var(--accent-gold)');
                SFX.playVictory();
            }
            updateXpBar();
        }

        // Damage display effect
        function showDamageNumber(text, color) {
            const stage = document.getElementById('game-stage');
            const el = document.createElement('div');
            el.className = 'damage-number';
            el.innerText = text;
            el.style.color = color;
            el.style.left = '20%';
            el.style.top = '40%';
            stage.appendChild(el);
            setTimeout(() => el.remove(), 800);
        }

        // Finalize adventure
        function endAdventureRun(victory) {
            stopRunnerLoop();
            
            const headline = document.getElementById('results-headline');
            const status = document.getElementById('res-status');
            const container = document.getElementById('screen-results');

            if (victory) {
                headline.innerText = "🏆 SIEG & ERFOLG!";
                status.innerText = "Abenteuer erfolgreich abgeschlossen!";
                container.className = "screen results-view victory";
            } else {
                headline.innerText = "💀 ABENTEUER GESCHEITERT!";
                status.innerText = "Keine Lebenspunkte mehr übrig.";
                container.className = "screen results-view defeat";
            }

            // Set metric texts
            document.getElementById('res-distance').innerText = `${currentDistance}m`;
            const accuracy = statsTotalCount > 0 ? Math.round((statsCorrectCount / statsTotalCount) * 100) : 0;
            document.getElementById('res-accuracy').innerText = `${accuracy}%`;
            document.getElementById('res-questions').innerText = `${statsCorrectCount} / ${statsTotalCount}`;
            document.getElementById('res-gold').innerText = `${currentCoins} 🪙`;
            document.getElementById('res-xp').innerText = `${level * 100 + currentXp} XP (Stufe ${level})`;

            // Populate Practice vocabulary lists
            const list = document.getElementById('practice-list-container');
            list.innerHTML = '';
            if (missedCards.length === 0) {
                list.innerHTML = `<li style="text-align:center; color:var(--accent-green); padding:20px;">Perfekt gelernt! Keine Fehler in diesem Abenteuer! 🌟</li>`;
            } else {
                missedCards.forEach(card => {
                    const item = document.createElement('li');
                    item.className = 'practice-item';
                    item.innerHTML = `
                        <span>${card.front}</span>
                        <span>&rarr;</span>
                        <span>${card.back}</span>
                    `;
                    list.appendChild(item);
                });
            }

            showScreen('screen-results');
        }

        // CARD EDITOR ENGINE
        function updateEditorList() {
            const body = document.getElementById('editor-card-list-body');
            body.innerHTML = '';
            document.getElementById('card-count').innerText = flashcards.length;

            flashcards.forEach((card, idx) => {
                const tr = document.createElement('tr');
                let typeText = 'Zufällig';
                if (card.type === 'choice') typeText = 'Choice';
                if (card.type === 'text') typeText = 'Schreiben';

                tr.innerHTML = `
                    <td>${card.front}</td>
                    <td>${card.back}</td>
                    <td>${typeText}</td>
                    <td>
                        <span class="action-icon action-delete" onclick="deleteCard(${idx})">🗑️</span>
                    </td>
                `;
                body.appendChild(tr);
            });
        }

        function addNewCard() {
            const front = document.getElementById('input-front').value.trim();
            const back = document.getElementById('input-back').value.trim();
            const type = document.getElementById('input-mode').value;

            if (!front || !back) {
                alert("Bitte fülle beide Felder aus!");
                return;
            }

            const newCard = { front, back, type };
            flashcards.push(newCard);
            localStorage.setItem('fantasy_quest_cards', JSON.stringify(flashcards));
            
            document.getElementById('input-front').value = '';
            document.getElementById('input-back').value = '';
            
            updateEditorList();
            SFX.playCorrect();
        }

        function deleteCard(index) {
            flashcards.splice(index, 1);
            localStorage.setItem('fantasy_quest_cards', JSON.stringify(flashcards));
            updateEditorList();
            SFX.playHit();
        }

        function resetDefaultCards() {
            if (confirm("Möchtest du das Lernset wirklich auf die Standard-Vokabeln (Französische Körperteile) zurücksetzen?")) {
                flashcards = [...DEFAULT_CARDS];
                localStorage.setItem('fantasy_quest_cards', JSON.stringify(flashcards));
                updateEditorList();
                SFX.playHit();
            }
        }
    </script>

</body>
</html>
Gemini Notebook kann fehlerhafte Informationen ausgeben. Bitte überprüfen Sie die Antworten.
Antwort ist fertig.
