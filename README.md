<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>root@sentinyl:~</title>
    <style>
        * { margin: 0; padding: 0; box-sizing: border-box; }
        
        body {
            font-family: 'JetBrains Mono', 'Fira Code', 'Courier New', monospace;
            line-height: 1.6;
            color: #e6edf3;
            background: #0d0d0d; /* Darker background for contrast */
            overflow-x: hidden;
        }

        /* --- BACKGROUND FX --- */
        #matrix-bg {
            position: fixed; top: 0; left: 0; width: 100%; height: 100%;
            z-index: -1; opacity: 0.15; /* Lowered opacity for readability */
        }
        
        .cyber-grid {
            position: fixed; top: 0; left: 0; width: 100%; height: 100%;
            background-image: linear-gradient(rgba(0, 255, 65, 0.03) 1px, transparent 1px), 
                              linear-gradient(90deg, rgba(0, 255, 65, 0.03) 1px, transparent 1px);
            background-size: 50px 50px;
            z-index: -1;
            animation: gridMove 20s linear infinite;
        }
        
        @keyframes gridMove {
            0% { transform: perspective(500px) rotateX(60deg) translateY(0); }
            100% { transform: perspective(500px) rotateX(60deg) translateY(50px); }
        }

        .container { max-width: 1200px; margin: 0 auto; padding: 0 20px; position: relative; z-index: 1; }

        /* --- BOOT SEQUENCE OVERLAY --- */
        #boot-screen {
            position: fixed; top: 0; left: 0; width: 100%; height: 100%;
            background: #000;
            color: #00ff41;
            z-index: 9999;
            padding: 40px;
            font-family: 'Courier New', monospace;
            overflow: hidden;
            display: flex;
            flex-direction: column;
            justify-content: flex-end;
        }
        .log-entry { margin-bottom: 5px; }
        .hidden { display: none !important; }

        /* --- TERMINAL HEADER (INTERACTIVE) --- */
        header { min-height: 90vh; display: flex; align-items: center; justify-content: center; }

        .terminal-window {
            background: rgba(13, 17, 23, 0.95);
            border: 1px solid #00ff41;
            border-radius: 8px;
            width: 100%; max-width: 900px;
            box-shadow: 0 0 40px rgba(0, 255, 65, 0.15);
            overflow: hidden;
        }

        .terminal-header {
            background: #1a1a1a;
            padding: 10px 15px;
            display: flex; align-items: center; gap: 8px;
            border-bottom: 1px solid #333;
        }
        .terminal-btn { width: 12px; height: 12px; border-radius: 50%; }
        .btn-r { background: #ff5f56; } .btn-y { background: #ffbd2e; } .btn-g { background: #27c93f; }

        .terminal-content { padding: 30px; font-size: 1.1em; min-height: 400px; }
        
        .prompt { color: #00ff41; margin-right: 10px; font-weight: bold; }
        .command-output { color: #e6edf3; margin-bottom: 20px; white-space: pre-wrap; }
        .input-line { display: flex; align-items: center; }
        
        input#cmd-input {
            background: transparent; border: none; color: #fff;
            font-family: inherit; font-size: inherit; width: 100%; outline: none;
        }

        .glitch { position: relative; display: inline-block; font-weight: bold; }
        
        /* --- SECTIONS & CARDS --- */
        section { padding: 100px 0; }
        .section-title { font-size: 2.5em; margin-bottom: 50px; border-bottom: 2px solid #00ff41; display: inline-block; }

        .project-grid { display: grid; grid-template-columns: repeat(auto-fit, minmax(350px, 1fr)); gap: 30px; }

        .card-3d {
            background: rgba(20, 20, 20, 0.8);
            border: 1px solid #333;
            border-left: 3px solid #00ff41;
            padding: 30px;
            border-radius: 10px;
            transition: all 0.3s ease;
            position: relative;
            overflow: hidden;
        }
        .card-3d:hover {
            transform: translateY(-5px);
            box-shadow: 0 10px 30px rgba(0, 255, 65, 0.1);
            border-color: #00ff41;
        }

        .tech-badge {
            display: inline-block;
            padding: 4px 12px;
            margin: 5px 5px 5px 0;
            border: 1px solid #00ff41;
            color: #00ff41;
            font-size: 0.85em;
            border-radius: 15px;
        }

        /* --- SKILLS WALL --- */
        .brick-wall { display: flex; flex-wrap: wrap; gap: 10px; margin-top: 30px; }
        .brick {
            background: #111;
            border: 1px solid #333;
            color: #ccc;
            padding: 10px 20px;
            font-size: 0.9em;
            transition: 0.2s;
            cursor: default;
        }
        .brick:hover {
            background: #00ff41; color: #000; border-color: #00ff41;
            box-shadow: 0 0 15px rgba(0, 255, 65, 0.5);
        }

        /* --- NAV --- */
        nav {
            position: fixed; top: 20px; right: 40px; z-index: 100;
            background: rgba(0,0,0,0.8); padding: 10px 20px;
            border: 1px solid #333; border-radius: 30px;
            backdrop-filter: blur(5px);
        }
        nav a { color: #fff; text-decoration: none; margin-left: 20px; font-size: 0.9em; transition: 0.3s; }
        nav a:hover { color: #00ff41; }

        /* --- FOOTER --- */
        footer { padding: 50px 0; text-align: center; color: #666; font-size: 0.9em; }
    </style>
</head>

<body>
    <div id="boot-screen">
        <div id="boot-log"></div>
    </div>

    <canvas id="matrix-bg"></canvas>
    <div class="cyber-grid"></div>

    <nav>
        <a href="#terminal">Terminal</a>
        <a href="#projects">Projects</a>
        <a href="#skills">Arsenal</a>
    </nav>

    <header id="terminal">
        <div class="terminal-window">
            <div class="terminal-header">
                <div class="terminal-btn btn-r"></div>
                <div class="terminal-btn btn-y"></div>
                <div class="terminal-btn btn-g"></div>
                <span style="margin-left: 10px; color: #666; font-size: 0.8em;">bash — 80x24</span>
            </div>
            <div class="terminal-content" id="terminal-output">
                <div class="command-output">
Welcome to Sentinyl OS v1.0.4
System Integrity: <span style="color: #00ff41;">SECURE</span>
User: root@sentinyl

Type 'help' to see available commands.
                </div>
                <div class="input-line">
                    <span class="prompt">root@sentinyl:~#</span>
                    <input type="text" id="cmd-input" autofocus>
                </div>
            </div>
        </div>
    </header>

    <section id="projects">
        <div class="container">
            <h2 class="section-title">MISSION LOGS</h2>
            
            <div class="project-grid">
                <div class="card-3d">
                    <h3 style="color: #00ff41; margin-bottom: 10px;">🛡️ SENTINYL ENTERPRISE</h3>
                    <p style="font-size: 0.9em; margin-bottom: 15px; color: #888;">AI-Powered Digital Risk Protection</p>
                    <p style="margin-bottom: 20px;">
                        A Zero-Liability SaaS platform that automates threat reconnaissance. It scans for typosquatting domains and exposed secrets, using a graph database to map attack vectors.
                    </p>
                    <div style="margin-bottom: 20px;">
                        <span class="tech-badge">Python (FastAPI)</span>
                        <span class="tech-badge">Neo4j</span>
                        <span class="tech-badge">Docker</span>
                        <span class="tech-badge">Redis</span>
                    </div>
                    <p style="font-size: 0.85em; color: #00bfff;">>> Features: Teams Alerts, MITRE Mapping, Async Scanning.</p>
                </div>

                <div class="card-3d">
                    <h3 style="color: #ff5f56; margin-bottom: 10px;">🦀 RUST TRAFFIC INSPECTOR</h3>
                    <p style="font-size: 0.9em; margin-bottom: 15px; color: #888;">Low-Level Packet Filter</p>
                    <p style="margin-bottom: 20px;">
                        A manual implementation of a Layer 7 traffic filter built from scratch in Rust. Intercepts raw TCP streams and filters HTTP packets in user-space without root privileges.
                    </p>
                    <div style="margin-bottom: 20px;">
                        <span class="tech-badge">Rust</span>
                        <span class="tech-badge">TCP/IP</span>
                        <span class="tech-badge">Systems Programming</span>
                    </div>
                    <p style="font-size: 0.85em; color: #00bfff;">>> Outcome: 1024-byte buffer capture, Manual HTTP Decoding.</p>
                </div>

                <div class="card-3d">
                    <h3 style="color: #00bfff; margin-bottom: 10px;">⚡ VERBA</h3>
                    <p style="font-size: 0.9em; margin-bottom: 15px; color: #888;">Real-Time Audio Transcription</p>
                    <p style="margin-bottom: 20px;">
                        Full-stack AI application for real-time audio processing. Implements WebSocket streaming for sub-100ms latency transcription.
                    </p>
                    <div style="margin-bottom: 20px;">
                        <span class="tech-badge">React</span>
                        <span class="tech-badge">WebSockets</span>
                        <span class="tech-badge">Node.js</span>
                    </div>
                </div>
            </div>
        </div>
    </section>

    <section id="skills">
        <div class="container">
            <h2 class="section-title">ARSENAL</h2>
            <div class="card-3d">
                <h3 style="color: #00ff41; margin-bottom: 20px;">CORE COMPETENCIES</h3>
                <div class="brick-wall">
                    <div class="brick">Rust</div>
                    <div class="brick">Python</div>
                    <div class="brick">Docker</div>
                    <div class="brick">Neo4j</div>
                    <div class="brick">FastAPI</div>
                    <div class="brick">React</div>
                    <div class="brick">Linux (Hardening)</div>
                    <div class="brick">Git</div>
                    <div class="brick">PostgreSQL</div>
                    <div class="brick">DevSecOps</div>
                    <div class="brick">Redis</div>
                    <div class="brick">Bash Scripting</div>
                </div>
            </div>
        </div>
    </section>

    <footer>
        <div class="container">
            <p>MARK M. MUNYIRI | SECURITY ENGINEER</p>
            <p style="font-size: 0.8em; color: #444; margin-top: 10px;">[ PGP KEY AVAILABLE UPON REQUEST ]</p>
        </div>
    </footer>

    <script>
        // --- 1. BOOT SEQUENCE ---
        const bootLogs = [
            "Initializing kernel...",
            "Mounting root filesystem (read-only)... [OK]",
            "Loading security modules... [OK]",
            "Connecting to Sentinel Network...",
            "Established secure connection.",
            "Starting visual interface..."
        ];
        
        const bootScreen = document.getElementById('boot-screen');
        const bootLogDiv = document.getElementById('boot-log');
        
        let delay = 0;
        bootLogs.forEach((log, index) => {
            delay += Math.floor(Math.random() * 300) + 100;
            setTimeout(() => {
                const p = document.createElement('div');
                p.className = 'log-entry';
                p.innerHTML = `<span style="color: #666;">[${new Date().toLocaleTimeString()}]</span> ${log}`;
                bootLogDiv.appendChild(p);
                
                if (index === bootLogs.length - 1) {
                    setTimeout(() => {
                        bootScreen.style.opacity = '0';
                        setTimeout(() => bootScreen.classList.add('hidden'), 500);
                    }, 800);
                }
            }, delay);
        });

        // --- 2. TERMINAL LOGIC ---
        const input = document.getElementById('cmd-input');
        const output = document.getElementById('terminal-output');
        const prompt = "root@sentinyl:~# ";

        input.addEventListener('keypress', function(e) {
            if (e.key === 'Enter') {
                const cmd = this.value.trim().toLowerCase();
                const oldInput = document.createElement('div');
                oldInput.innerHTML = `<span class="prompt">${prompt}</span>${this.value}`;
                oldInput.style.marginBottom = '10px';
                
                // Insert before the input line
                output.insertBefore(oldInput, document.querySelector('.input-line'));
                
                let response = "";
                
                // COMMANDS
                switch(cmd) {
                    case 'help':
                        response = "Available commands:\n  whoami    - Display user profile\n  ls        - List sections\n  cat [arg] - View content (try 'cat projects')\n  clear     - Clear terminal";
                        break;
                    case 'whoami':
                        response = "Mark M. Munyiri\nSecurity Engineer & Systems Architect.\nFocus: Hardened Systems, Rust, DevSecOps.";
                        break;
                    case 'ls':
                        response = "about/  projects/  arsenal/  contact.txt";
                        break;
                    case 'cat projects':
                        document.getElementById('projects').scrollIntoView({ behavior: 'smooth' });
                        response = "Navigating to Mission Logs...";
                        break;
                    case 'cat about':
                        response = "I build hardened systems from the byte level up.";
                        break;
                    case 'clear':
                        // Remove all previous output divs
                        document.querySelectorAll('#terminal-output > div:not(.input-line)').forEach(el => el.remove());
                        this.value = '';
                        return; // Exit early
                    default:
                        if (cmd !== "") response = `Command not found: ${cmd}. Type 'help'.`;
                }

                if (response) {
                    const respDiv = document.createElement('div');
                    respDiv.className = 'command-output';
                    respDiv.innerText = response;
                    output.insertBefore(respDiv, document.querySelector('.input-line'));
                }

                this.value = '';
                // Keep scroll at bottom
                output.scrollTop = output.scrollHeight;
            }
        });

        // Focus input on click anywhere in terminal
        document.querySelector('.terminal-window').addEventListener('click', () => input.focus());

        // --- 3. MATRIX BACKGROUND (Optimized) ---
        const canvas = document.getElementById('matrix-bg');
        const ctx = canvas.getContext('2d');
        canvas.width = window.innerWidth;
        canvas.height = window.innerHeight;
        
        const chars = '01';
        const fontSize = 14;
        const columns = canvas.width / fontSize;
        const drops = Array(Math.floor(columns)).fill(1);

        function drawMatrix() {
            ctx.fillStyle = 'rgba(13, 13, 13, 0.05)';
            ctx.fillRect(0, 0, canvas.width, canvas.height);
            ctx.fillStyle = '#00ff41';
            ctx.font = fontSize + 'px monospace';
            
            for (let i = 0; i < drops.length; i++) {
                const text = chars[Math.floor(Math.random() * chars.length)];
                ctx.fillText(text, i * fontSize, drops[i] * fontSize);
                if (drops[i] * fontSize > canvas.height && Math.random() > 0.975) drops[i] = 0;
                drops[i]++;
            }
        }
        setInterval(drawMatrix, 50);
        window.addEventListener('resize', () => { canvas.width = window.innerWidth; canvas.height = window.innerHeight; });
    </script>
</body>
</html>
