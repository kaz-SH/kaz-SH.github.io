<!DOCTYPE html>
<html lang="fr">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>KORE | Optimized Runtime Engine</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <link href="https://fonts.googleapis.com/css2?family=Poppins:wght@900&family=Inter:wght@300;400;600;800&family=JetBrains+Mono:wght@400;700&display=swap" rel="stylesheet">
    <style>
        :root {
            --photon: #3b82f6;
            --bg-deep: #050608;
            --glass: rgba(255, 255, 255, 0.02);
            --glass-border: rgba(255, 255, 255, 0.06);
        }

        * { cursor: crosshair; }

        body {
            background-color: #000;
            color: #f1f5f9;
            font-family: 'Inter', sans-serif;
            margin: 0;
            overflow: hidden; /* Désactivé pendant l'intro */
        }

        /* --- SÉQUENCE D'ENTRÉE : WARP SPEED --- */
        #warp-container {
            position: fixed;
            inset: 0;
            z-index: 9999;
            background: #000;
            display: flex;
            align-items: center;
            justify-content: center;
            transition: opacity 1s ease-out;
        }

        #warp-canvas { width: 100%; height: 100%; }

        .flash-overlay {
            position: absolute;
            inset: 0;
            background: white;
            opacity: 0;
            pointer-events: none;
            z-index: 10000;
        }

        /* --- STRUCTURE DU SITE --- */
        #main-site {
            opacity: 0;
            transform: scale(1.1);
            transition: opacity 1.5s ease, transform 1.5s cubic-bezier(0.16, 1, 0.3, 1);
            height: 100vh;
            display: flex;
            flex-direction: column;
            background-color: var(--bg-deep);
            background-image: 
                radial-gradient(circle at 50% 50%, rgba(59, 130, 246, 0.03) 0%, transparent 80%),
                linear-gradient(rgba(255, 255, 255, 0.01) 1px, transparent 1px),
                linear-gradient(90deg, rgba(255, 255, 255, 0.01) 1px, transparent 1px);
            background-size: 100% 100%, 60px 60px, 60px 60px;
        }

        #main-site.revealed {
            opacity: 1;
            transform: scale(1);
            overflow-y: auto;
        }

        /* --- LOGO K-SPLIT & FLUX --- */
        .logo-wrapper {
            position: relative;
            display: inline-block;
            padding: 40px 0;
            will-change: transform;
        }

        .k-split {
            font-family: 'Poppins', sans-serif;
            font-size: 5rem;
            font-weight: 900;
            line-height: 1;
            color: white;
            position: relative;
            /* Découpe laser à 50% */
            clip-path: polygon(0% 0%, 100% 0%, 100% 46%, 0% 46%, 0% 54%, 100% 54%, 100% 100%, 0% 100%);
            z-index: 5;
            -webkit-font-smoothing: antialiased;
        }

        .lightning-container {
            position: absolute;
            top: 50%;
            left: 0;
            width: 100%;
            height: 100%;
            pointer-events: none;
            z-index: 10;
            transform: translateY(-50%);
        }

        .photon-pulse {
            position: absolute;
            top: 50%;
            left: 0;
            width: 120px;
            height: 2px;
            background: linear-gradient(90deg, transparent, var(--photon) 40%, #fff 95%, #fff);
            margin-top: -1px; 
            transform: translate3d(-250%, 0, 0);
            box-shadow: 0 0 20px var(--photon), 0 0 40px rgba(59, 130, 246, 0.4);
            opacity: 0;
            animation: photon-glide 2.2s cubic-bezier(0.8, 0, 0.2, 1) infinite;
            will-change: transform, opacity;
        }

        .photon-pulse::after {
            content: '';
            position: absolute;
            right: -2px;
            top: 50%;
            transform: translateY(-50%);
            width: 6px;
            height: 6px;
            background: white;
            border-radius: 50%;
            box-shadow: 0 0 15px white, 0 0 30px var(--photon);
        }

        @keyframes photon-glide {
            0% { transform: translate3d(-250%, 0, 0); opacity: 0; }
            10% { opacity: 1; }
            45% { transform: translate3d(250%, 0, 0); opacity: 1; }
            55%, 100% { transform: translate3d(300%, 0, 0); opacity: 0; }
        }

        /* --- GLASSMORPHISM CARDS --- */
        .glass-card {
            background: var(--glass);
            backdrop-filter: blur(16px);
            -webkit-backdrop-filter: blur(16px);
            border: 1px solid var(--glass-border);
            border-radius: 1.5rem;
            transition: all 0.5s cubic-bezier(0.19, 1, 0.22, 1);
        }

        .glass-card:hover {
            border-color: rgba(59, 130, 246, 0.3);
            background: rgba(255, 255, 255, 0.04);
            transform: translateY(-5px);
        }

        /* --- UI NAVIGATION --- */
        .nav-link {
            font-family: 'JetBrains Mono', monospace;
            font-size: 0.7rem;
            text-transform: uppercase;
            letter-spacing: 0.2em;
            color: #64748b;
            transition: color 0.3s;
        }
        .nav-link:hover, .nav-link.active { color: var(--photon); }

        .content-section { display: none; }
        .content-section.active { display: block; animation: sectionIn 0.8s ease-out; }

        @keyframes sectionIn {
            from { opacity: 0; transform: translateY(10px); }
            to { opacity: 1; transform: translateY(0); }
        }

        .btn-prime {
            background: #fff;
            color: #000;
            font-family: 'JetBrains Mono', monospace;
            font-weight: 700;
            padding: 1rem 2rem;
            border-radius: 0.75rem;
            text-transform: uppercase;
            font-size: 0.75rem;
            transition: all 0.3s ease;
            width: 100%;
        }

        .btn-prime:hover {
            background: var(--photon);
            color: #fff;
            box-shadow: 0 0 25px var(--photon);
        }

        @keyframes flash-out {
            0% { opacity: 0; }
            20% { opacity: 1; }
            100% { opacity: 0; }
        }
    </style>
</head>
<body>

    <!-- OVERLAY D'INTRODUCTION (WARP DRIVE) -->
    <div id="warp-container">
        <div class="flash-overlay" id="flash"></div>
        <canvas id="warp-canvas"></canvas>
    </div>

    <!-- CONTENU PRINCIPAL -->
    <div id="main-site">
        <nav class="w-full px-12 py-8 flex justify-between items-center z-50">
            <div class="flex items-center gap-4">
                <div class="logo-wrapper scale-50 -ml-12">
                    <div class="lightning-container"><div class="photon-pulse"></div></div>
                    <div class="k-split">K</div>
                </div>
                <span class="font-black tracking-tighter text-xl">KORE</span>
            </div>
            <div class="flex gap-10">
                <button onclick="showSection('home')" class="nav-link active" id="link-home">Console</button>
                <button onclick="showSection('features')" class="nav-link" id="link-features">Architecture</button>
                <button onclick="showSection('download')" class="nav-link" id="link-download">Déploiement</button>
            </div>
            <div class="text-[10px] font-mono text-zinc-600 tracking-widest">BUILD_2026_PTSI</div>
        </nav>

        <main class="flex-1 container mx-auto px-6 flex flex-col items-center justify-center">
            
            <!-- SECTION ACCUEIL -->
            <div id="home" class="content-section active text-center max-w-4xl">
                <div class="logo-wrapper mb-12">
                    <div class="lightning-container"><div class="photon-pulse" style="animation-duration: 1.5s;"></div></div>
                    <div class="k-split" style="font-size: 10rem;">K</div>
                </div>
                <h1 class="text-6xl font-black tracking-tighter mb-6 uppercase leading-tight">
                    LIGHTNING <span class="text-blue-500 italic">FAST</span><br>RUNTIME ENGINE.
                </h1>
                <p class="text-zinc-500 text-lg font-light mb-10 max-w-2xl mx-auto leading-relaxed">
                    Moteur d'exécution ultra-basse latence conçu pour l'interconnexion massive. Performance brute pour architectures critiques.
                </p>
                <div class="flex gap-4 justify-center">
                    <button onclick="showSection('download')" class="btn-prime w-auto">Télécharger le module</button>
                    <button onclick="showSection('features')" class="px-8 py-4 border border-zinc-800 rounded-xl text-xs font-mono uppercase tracking-widest hover:border-white transition-all">Specs_Techniques</button>
                </div>
            </div>

            <!-- SECTION ARCHITECTURE -->
            <div id="features" class="content-section w-full max-w-5xl">
                <div class="grid grid-cols-1 md:grid-cols-3 gap-8">
                    <div class="glass-card p-10">
                        <div class="text-blue-500 font-mono text-[10px] mb-6 tracking-widest">01 // LATENCE</div>
                        <h3 class="text-xl font-bold mb-4">Usinage du Signal</h3>
                        <p class="text-xs text-zinc-500 leading-relaxed font-light">Le Kernel KORE garantit une exécution sub-milliseconde. Pas de gigue dans le bus interne.</p>
                    </div>
                    <div class="glass-card p-10">
                        <div class="text-blue-500 font-mono text-[10px] mb-6 tracking-widest">02 // TOPOLOGIE</div>
                        <h3 class="text-xl font-bold mb-4">Réseau Cristallin</h3>
                        <p class="text-xs text-zinc-500 leading-relaxed font-light">Chaque utilisateur devient une brique de calcul optimisée dans le cluster mondial.</p>
                    </div>
                    <div class="glass-card p-10">
                        <div class="text-blue-500 font-mono text-[10px] mb-6 tracking-widest">03 // OPTIMISATION</div>
                        <h3 class="text-xl font-bold mb-4">Cœur Java 21</h3>
                        <p class="text-xs text-zinc-500 leading-relaxed font-light">Compilation JIT avancée pour une gestion mémoire agressive et une vitesse de calcul brute.</p>
                    </div>
                </div>
            </div>

            <!-- SECTION DEPLOIEMENT -->
            <div id="download" class="content-section w-full max-w-5xl">
                <div class="grid grid-cols-1 md:grid-cols-2 gap-8">
                    <div class="glass-card p-12 flex flex-col">
                        <div class="flex items-center gap-6 mb-8">
                            <div class="p-4 bg-white/5 rounded-2xl text-blue-500">
                                <svg class="w-8 h-8" fill="none" stroke="currentColor" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="1.5" d="M4 4h16v16H4zM4 12h16M12 4v16"></path></svg>
                            </div>
                            <div>
                                <h2 class="font-bold text-xl">KORE Launcher</h2>
                                <span class="text-[10px] font-mono text-zinc-600">WIN_X64_STABLE</span>
                            </div>
                        </div>
                        <button class="btn-prime" onclick="simulateDownload(this)">Déployer le Binaire</button>
                    </div>
                    <div class="glass-card p-12 flex flex-col">
                        <div class="flex items-center gap-6 mb-8">
                            <div class="p-4 bg-white/5 rounded-2xl text-zinc-400">
                                <svg class="w-8 h-8" fill="none" stroke="currentColor" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="1.5" d="M19 11H5m14 0a2 2 0 012 2v6a2 2 0 01-2 2H5a2 2 0 01-2-2v-6a2 2 0 012-2m14 0V9a2 2 0 00-2-2M5 11V9a2 2 0 012-2m0 0V5a2 2 0 012-2h6a2 2 0 012 2v2M7 7h10"></path></svg>
                            </div>
                            <div>
                                <h2 class="font-bold text-xl text-zinc-300">Java 21 Library</h2>
                                <span class="text-[10px] font-mono text-zinc-600">DÉPENDANCE_LTS</span>
                            </div>
                        </div>
                        <button class="px-8 py-4 border border-zinc-800 rounded-xl text-[10px] font-mono uppercase tracking-widest hover:border-white transition-all" onclick="window.open('https://www.oracle.com/java/technologies/downloads/#java21', '_blank')">Lien Oracle JDK 21</button>
                    </div>
                </div>
            </div>
        </main>

        <footer class="w-full py-8 border-t border-white/5 bg-black/40 backdrop-blur-xl">
            <div class="container mx-auto px-12 grid grid-cols-2 md:grid-cols-4 gap-8">
                <div class="flex flex-col gap-1">
                    <span class="text-[9px] text-zinc-600 uppercase tracking-widest">Signal_Stability</span>
                    <span class="text-[11px] font-mono text-blue-400">0.99998_SIGMA</span>
                </div>
                <div class="flex flex-col gap-1">
                    <span class="text-[9px] text-zinc-600 uppercase tracking-widest">Bus_Latency</span>
                    <span class="text-[11px] font-mono text-zinc-400">&lt; 0.05ms</span>
                </div>
                <div class="flex flex-col gap-1">
                    <span class="text-[9px] text-zinc-600 uppercase tracking-widest">System_Status</span>
                    <span class="text-[11px] font-mono text-emerald-500 animate-pulse">OPTIMIZED_READY</span>
                </div>
                <div class="flex flex-col gap-1 items-end">
                    <span class="text-[9px] text-zinc-600 uppercase tracking-widest">Node_ID</span>
                    <span class="text-[11px] font-mono text-zinc-600">NODE_0xAF42</span>
                </div>
            </div>
        </footer>
    </div>

    <script>
        // --- LOGIQUE WARP DRIVE (ENTRÉE) ---
        const canvas = document.getElementById('warp-canvas');
        const ctx = canvas.getContext('2d');
        let w, h, stars = [];
        const count = 450;
        let speed = 0.5;
        let warping = true;

        function initCanvas() {
            w = canvas.width = window.innerWidth;
            h = canvas.height = window.innerHeight;
            stars = [];
            for(let i=0; i<count; i++) {
                stars.push({
                    x: Math.random() * w - w/2,
                    y: Math.random() * h - h/2,
                    z: Math.random() * w,
                });
            }
        }

        function drawWarp() {
            if(!warping) return;
            ctx.fillStyle = '#000';
            ctx.fillRect(0,0,w,h);
            
            if(speed < 45) speed *= 1.06; // Accélération exponentielle

            for(let i=0; i<count; i++) {
                let s = stars[i];
                let x = s.x * (w / s.z);
                let y = s.y * (w / s.z);
                
                s.z -= speed;
                if(s.z <= 0) s.z = w;

                let x2 = s.x * (w / (s.z + speed * 1.5));
                let y2 = s.y * (w / (s.z + speed * 1.5));

                ctx.beginPath();
                ctx.lineWidth = 1.5;
                ctx.strokeStyle = i % 8 === 0 ? '#3b82f6' : '#ffffff';
                ctx.moveTo(x + w/2, y + h/2);
                ctx.lineTo(x2 + w/2, y2 + h/2);
                ctx.stroke();
            }
            requestAnimationFrame(drawWarp);
        }

        window.onload = () => {
            initCanvas();
            drawWarp();

            setTimeout(() => {
                document.getElementById('flash').style.animation = 'flash-out 0.8s ease-out forwards';
                setTimeout(() => {
                    warping = false;
                    document.getElementById('warp-container').style.opacity = '0';
                    document.getElementById('main-site').classList.add('revealed');
                    document.body.style.overflow = 'auto';
                    setTimeout(() => document.getElementById('warp-container').remove(), 1000);
                }, 200);
            }, 2600); // Durée du warp
        };

        window.onresize = initCanvas;

        // --- NAVIGATION SPA ---
        function showSection(sectionId) {
            document.querySelectorAll('.content-section').forEach(s => s.classList.remove('active'));
            document.getElementById(sectionId).classList.add('active');
            document.querySelectorAll('.nav-link').forEach(l => l.classList.remove('active'));
            document.getElementById('link-' + sectionId).classList.add('active');
        }

        function simulateDownload(btn) {
            const originalText = btn.innerText;
            btn.disabled = true;
            btn.innerText = "ACCÈS_BUS...";
            setTimeout(() => {
                btn.innerText = "TRANSFERT_TERMINÉ";
                btn.style.background = "#fff"; btn.style.color = "#000";
                setTimeout(() => {
                    btn.innerText = originalText;
                    btn.disabled = false;
                }, 3000);
            }, 2000);
        }
    </script>
</body>
</html>