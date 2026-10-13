---
layout: page
title: ECC Public-Key Generation
description: Homework 1 - Toy example of hashing using SECP256K1
img: assets/img/12.jpg
importance: 1
category: work
---

L'obiettivo di questo progetto è dimostrare come una chiave privata generi una chiave pubblica tramite la moltiplicazione scalare su una curva ellittica. Invece della curva usata in Ethereum (secp256k1), utilizziamo una curva "giocattolo" su un campo finito ridotto per poterne visualizzare il comportamento.

**Parametri della Curva Giocattolo:**
* Equazione: $y^2 = x^3 + 7 \pmod{17}$
* Modulo ($P$): $17$
* Punto Base ($G$): $(1, 5)$

<div class="row">
    <!-- Colonna Comandi e Output -->
    <div class="col-sm-4 mt-3 mt-md-0">
        <div class="card">
            <div class="card-body">
                <h5 class="card-title">Generatore di Chiavi</h5>
                <div class="form-group">
                    <label for="privateKeyInput">Chiave Privata (k):</label>
                    <input type="number" id="privateKeyInput" class="form-control" value="6" min="2" max="30">
                </div>
                <button id="calcButton" class="btn btn-primary btn-sm mt-2 w-100">Genera Chiave Pubblica</button>
            </div>
        </div>
        <div class="mt-3">
            <h6>Log Operazioni:</h6>
            <pre id="outputLog" style="height: 220px; overflow-y: auto; background: #f8f9fa; padding: 10px; border-radius: 5px; font-size: 0.85em;"></pre>
        </div>
    </div>

    <!-- Colonna Canvas Animato (Livello Guru) -->
    <div class="col-sm-8 mt-3 mt-md-0 text-center">
        <canvas id="eccCanvas" width="340" height="340" style="border: 1px solid #e0e0e0; background: #fafafa; max-width: 100%; border-radius: 5px; box-shadow: 0 2px 5px rgba(0,0,0,0.05);"></canvas>
        <div class="caption">
            Rappresentazione rallentata dei salti algebrici sul piano cartesiano discreto $17 \times 17$. Il punto rosso finale rappresenta la Chiave Pubblica $Q = kG$.
        </div>
    </div>
</div>

---

### Statistical Exploration

L'esecuzione ripetuta dell'algoritmo con diversi valori di $k$ fa emergere importanti proprietà statistiche e crittografiche intrinseche alla matematica delle curve ellittiche.

<div class="row justify-content-sm-center mt-4">
    <div class="col-sm-12">
        <ul>
            <li><strong>Distribuzione e Uniformità Apparente:</strong> Osservando l'animazione, i punti generati dalla sequenza $1G, 2G, 3G \dots$ non seguono una traiettoria lineare o geometricamente intuitiva sul piano cartesiano. Le coordinate $x$ e $y$ saltano in modo apparentemente pseudo-casuale distribuendosi su tutto il campo finito modulo 17. Questa caoticità visiva è essenziale per nascondere la relazione diretta tra input e output.</li>
            <li><strong>Comportamento Valanga (Avalanche-like behavior):</strong> Una variazione minima nell'input (ad esempio passando da $k=6$ a $k=7$) produce un risultato $Q$ con coordinate completamente diverse, situate in una zona opposta del piano. Non esiste una correlazione lineare sfruttabile tra un incremento dell'input e la variazione dell'output.</li>
            <li><strong>Funzioni Unidirezionali vs Hash:</strong> A differenza di una funzione di hash crittografica (come Keccak-256), che distrugge matematicamente le informazioni di partenza producendo un output caotico ma privo di struttura algebrica, la moltiplicazione scalare ECC mantiene una rigidissima struttura matematica sottostante (è un gruppo abeliano). Questa struttura è ciò che permette di eseguire operazioni avanzate come le firme digitali (ECDSA), mantenendo al contempo la unidirezionalità garantita dalla difficoltà del Problema del Logaritmo Discreto (ECDLP).</li>
        </ul>
    </div>
</div>

<!-- Logica JavaScript del progetto -->
<script>
    const P = 17;
    const G = { x: 1, y: 5 };
    
    // Funzione modulo per gestire correttamente i numeri negativi in JS
    function mod(n, p) {
        return ((n % p) + p) % p;
    }

    // Calcolo dell'inverso moltiplicativo (metodo iterativo semplice per moduli piccoli)
    function modInverse(a, p) {
        a = mod(a, p);
        for (let i = 1; i < p; i++) {
            if (mod(a * i, p) === 1) return i;
        }
        return null; 
    }

    // Somma algebrica di due punti sulla curva ellittica
    function addPoints(P1, P2) {
        let m;
        if (P1.x === P2.x && P1.y === P2.y) {
            // Point Doubling
            let num = 3 * mod(Math.pow(P1.x, 2), P);
            let den = 2 * P1.y;
            m = mod(num * modInverse(den, P), P);
        } else {
            // Point Addition
            let num = P2.y - P1.y;
            let den = P2.x - P1.x;
            m = mod(num * modInverse(den, P), P);
        }

        let x3 = mod(Math.pow(m, 2) - P1.x - P2.x, P);
        let y3 = mod(m * (P1.x - x3) - P1.y, P);

        return { x: x3, y: y3 };
    }

    // --- LOGICA DI DISEGNO CANVAS (LIVELLO GURU) ---
    const canvas = document.getElementById('eccCanvas');
    const ctx = canvas.getContext('2d');
    const cellSize = 20;

    function drawGrid() {
        ctx.clearRect(0, 0, canvas.width, canvas.height);
        ctx.strokeStyle = '#eee';
        ctx.lineWidth = 1;
        for (let i = 0; i <= P; i++) {
            ctx.beginPath();
            ctx.moveTo(i * cellSize, 0);
            ctx.lineTo(i * cellSize, P * cellSize);
            ctx.stroke();
            ctx.beginPath();
            ctx.moveTo(0, i * cellSize);
            ctx.lineTo(P * cellSize, i * cellSize);
            ctx.stroke();
        }
    }

    function drawPoint(point, color, label) {
        // Invertiamo l'asse Y per avere lo 0 in basso a sinistra
        let cx = point.x * cellSize;
        let cy = (P - point.y) * cellSize;
        
        ctx.beginPath();
        ctx.arc(cx, cy, 4, 0, 2 * Math.PI);
        ctx.fillStyle = color;
        ctx.fill();
        ctx.strokeStyle = '#333';
        ctx.stroke();
        
        ctx.fillStyle = '#333';
        ctx.font = '10px Arial';
        ctx.fillText(label, cx + 6, cy - 6);
    }

    function drawLine(p1, p2) {
        ctx.beginPath();
        ctx.moveTo(p1.x * cellSize, (P - p1.y) * cellSize);
        ctx.lineTo(p2.x * cellSize, (P - p2.y) * cellSize);
        ctx.strokeStyle = 'rgba(0, 123, 255, 0.3)';
        ctx.lineWidth = 2;
        ctx.stroke();
    }

    // Funzione asincrona per animare il calcolo
    async function calculateEcc() {
        const k = parseInt(document.getElementById('privateKeyInput').value);
        const logArea = document.getElementById('outputLog');
        const btn = document.getElementById('calcButton');
        
        btn.disabled = true;
        logArea.innerHTML = "";
        drawGrid();

        let currentPoint = G;
        drawPoint(currentPoint, '#007bff', '1G');
        logArea.innerHTML += `1G = (${currentPoint.x}, ${currentPoint.y})\n`;

        for (let i = 2; i <= k; i++) {
            // Pausa per creare l'animazione visiva (500 millisecondi)
            await new Promise(r => setTimeout(r, 500));
            
            let nextPoint = addPoints(currentPoint, G);
            drawLine(currentPoint, nextPoint);
            
            let color = (i === k) ? '#dc3545' : '#28a745'; // Rosso per il punto finale
            let label = (i === k) ? `Q=${i}G` : `${i}G`;
            
            drawPoint(nextPoint, color, label);
            logArea.innerHTML += `${i}G = (${nextPoint.x}, ${nextPoint.y})\n`;
            logArea.scrollTop = logArea.scrollHeight;
            
            currentPoint = nextPoint;
        }
        
        logArea.innerHTML += `\nCHIAVE PUBBLICA (Q) = (${currentPoint.x}, ${currentPoint.y})\n`;
        btn.disabled = false;
    }

    document.getElementById('calcButton').addEventListener('click', calculateEcc);
    
    // Disegna la griglia vuota all'avvio
    drawGrid();
</script>
