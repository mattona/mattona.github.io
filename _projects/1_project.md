---
layout: page
title: Public-Key Cryptography & ECC
description: Toy example of hashing using secp256k1 concepts and statistical exploration.
img: assets/img/hmk1.jpg
importance: 1
category: Statistics
---

L'obiettivo di questo progetto è esplorare i fondamenti matematici della crittografia a chiave pubblica utilizzata in Ethereum, basata sulla moltiplicazione scalare su curve ellittiche (ECC). Senza addentrarci nei grandi numeri della curva `secp256k1`, utilizzeremo un modello ridotto (Toy Curve) per visualizzare come una chiave privata generi una chiave pubblica.

L'equazione della nostra curva giocattolo su campo finito è:  
$$y^2 = x^3 + 7 \pmod{17}$$

### 1. Interactive Toy Example
In questa sezione puoi interagire con l'algoritmo. Seleziona un punto base generatore $$G$$ valido per la nostra curva e inserisci una chiave privata $$k$$. Il grafico mostrerà il percorso della moltiplicazione scalare calcolando in sequenza $$1G, 2G, \dots, kG$$. La posizione finale rappresenta la tua Chiave Pubblica $$Q$$.

<div class="row justify-content-sm-center mt-4">
    <div class="col-sm-4 mt-3 mt-md-0">
        <div class="card p-3 z-depth-1 rounded">
            <h5>Impostazioni</h5>
            <div class="form-group mt-2">
                <label for="basePoint">Punto Base (G):</label>
                <select id="basePoint" class="form-control form-control-sm">
                    <option value="1,5">G = (1, 5)</option>
                    <option value="2,6">G = (2, 6)</option>
                    <option value="3,8">G = (3, 8)</option>
                    <option value="5,8">G = (5, 8)</option>
                </select>
            </div>
            <div class="form-group mt-2">
                <label for="privateKey">Chiave Privata (k):</label>
                <input type="number" id="privateKey" class="form-control form-control-sm" value="6" min="1" max="25">
            </div>
            <button id="calcBtn" class="btn btn-primary btn-sm mt-3 w-100">Genera Chiave Pubblica</button>
            <hr>
            <div id="outputLog" style="font-family: monospace; font-size: 0.9em; max-height: 150px; overflow-y: auto;">
                <!-- Log dei risultati intermedi -->
            </div>
        </div>
    </div>
    <div class="col-sm-8 mt-3 mt-md-0">
        <div class="card p-2 z-depth-1 rounded text-center">
            <canvas id="eccCanvas" width="400" height="400" style="max-width: 100%;"></canvas>
            <div class="caption mt-2">Rappresentazione del campo finito 17x17. I salti mostrano l'addizione dei punti sulla curva.</div>
        </div>
    </div>
</div>

---

### 2. Statistical Exploration
Testando iterativamente l'algoritmo con diversi valori di $$k$$, possiamo analizzarne il comportamento statistico. Ho simulato la moltiplicazione per i primi 18 valori di $$k$$ partendo dal punto $$G = (1,5)$$.

<div class="row mt-4">
    <div class="col-sm-6 mt-3 mt-md-0">
        <canvas id="distChart" class="z-depth-1 rounded p-2 bg-white"></canvas>
        <div class="caption">Distribuzione delle coordinate X e Y. Si nota un'apparente uniformità senza raggruppamenti lineari.</div>
    </div>
    <div class="col-sm-6 mt-3 mt-md-0">
        <canvas id="avalancheChart" class="z-depth-1 rounded p-2 bg-white"></canvas>
        <div class="caption">Effetto Valanga: distanza tra Q generata da k e Q generata da k+1. Piccole variazioni di input causano output lontani.</div>
    </div>
</div>

**Distribuzione e Uniformità**  
Come visibile nel grafico a barre, le coordinate X e Y dei punti generati non seguono una distribuzione normale, ma si disperdono in modo apparentemente pseudo-casuale all'interno del campo finito. Non vi è una correlazione visiva immediata tra l'input (crescente linearmente) e le coordinate di output.

**Correlazione ed Effetto Valanga**  
L'Effetto Valanga (Avalanche Effect) è evidente: incrementare la chiave privata $$k$$ di una sola unità ($$k \to k+1$$) causa un "salto" geometrico drastico sul piano. La distanza euclidea tra due chiavi pubbliche consecutive non è minima né predicibile visivamente, soddisfacendo la necessità crittografica di non rivelare informazioni sull'input partendo da output simili.

---

### 3. Conclusioni: ECC vs Funzioni di Hash
Dall'analisi emergono alcune riflessioni su quali proprietà siano desiderabili per una funzione crittografica unidirezionale (come ECC) rispetto a una classica funzione di Hash (come Keccak256).

*   **Proprietà Condivise:** Entrambe richiedono unidirezionalità (facilità di calcolo in una direzione, intrattabilità nell'altra) e presentano un forte effetto valanga per evitare la deduzione dell'input. Nel caso di ECC, risalire a $$k$$ conoscendo $$Q$$ e $$G$$ richiede la risoluzione del Problema del Logaritmo Discreto (ECDLP), computazionalmente proibitivo per i grandi moduli usati in Ethereum.
*   **Differenze Chiave:** Una funzione di hash deve distruggere ogni legame matematico e non ha invertibilità strutturale. Al contrario, la moltiplicazione scalare ECC **deve preservare una struttura algebrica rigorosa** (omomorfismo). Questa struttura matematica sottostante, che sfugge all'analisi statistica visiva, è ciò che permette la verifica formale delle Firme Digitali.

<!-- Script per la logica matematica, interazione Canvas e grafici Chart.js -->
<script src="https://cdn.jsdelivr.net/npm/chart.js"></script>

{% raw %}
<script>
document.addEventListener("DOMContentLoaded", function() {
    const P = 17;

    function mod(n, p) { return ((n % p) + p) % p; }

    function modInverse(a, p) {
        a = mod(a, p);
        for (let i = 1; i < p; i++) {
            if (mod(a * i, p) === 1) return i;
        }
        return null;
    }

    function addPoints(x1, y1, x2, y2) {
        if (x1 === null) return {x: x2, y: y2};
        if (x1 === x2 && y1 === mod(-y2, P)) return {x: null, y: null}; // Infinito

        let m;
        if (x1 === x2 && y1 === y2) {
            let num = 3 * Math.pow(x1, 2);
            let den = 2 * y1;
            m = mod(num * modInverse(den, P), P);
        } else {
            let num = y2 - y1;
            let den = x2 - x1;
            m = mod(num * modInverse(den, P), P);
        }

        let x3 = mod(Math.pow(m, 2) - x1 - x2, P);
        let y3 = mod(m * (x1 - x3) - y1, P);
        return { x: x3, y: y3 };
    }

    // --- Disegno su Canvas ---
    const canvas = document.getElementById("eccCanvas");
    const ctx = canvas.getContext("2d");
    const gridSize = canvas.width / P;

    function drawGrid() {
        ctx.clearRect(0, 0, canvas.width, canvas.height);
        ctx.strokeStyle = "#e0e0e0";
        ctx.lineWidth = 1;
        for (let i = 0; i <= P; i++) {
            ctx.beginPath();
            ctx.moveTo(i * gridSize, 0);
            ctx.lineTo(i * gridSize, canvas.height);
            ctx.stroke();
            ctx.beginPath();
            ctx.moveTo(0, i * gridSize);
            ctx.lineTo(canvas.width, i * gridSize);
            ctx.stroke();
        }
    }

    function plotPoint(x, y, color = "blue", label = "") {
        const px = x * gridSize;
        const py = canvas.height - (y * gridSize); 
        ctx.beginPath();
        ctx.arc(px, py, 5, 0, 2 * Math.PI);
        ctx.fillStyle = color;
        ctx.fill();
        if (label) {
            ctx.fillStyle = "black";
            ctx.font = "12px Arial";
            ctx.fillText(label, px + 8, py - 8);
        }
    }

    function drawLine(p1, p2) {
        ctx.beginPath();
        ctx.moveTo(p1.x * gridSize, canvas.height - (p1.y * gridSize));
        ctx.lineTo(p2.x * gridSize, canvas.height - (p2.y * gridSize));
        ctx.strokeStyle = "rgba(0, 0, 255, 0.3)";
        ctx.stroke();
    }

    // --- Interazione UI ---
    document.getElementById("calcBtn").addEventListener("click", () => {
        const baseVal = document.getElementById("basePoint").value.split(",");
        const G = { x: parseInt(baseVal[0]), y: parseInt(baseVal[1]) };
        const k = parseInt(document.getElementById("privateKey").value);
        const log = document.getElementById("outputLog");
        
        log.innerHTML = "";
        drawGrid();
        plotPoint(G.x, G.y, "green", "G");

        let current = G;
        log.innerHTML += `1G = (${current.x}, ${current.y})<br>`;

        let prev = current;
        let step = 2;

        function animateStep() {
            if (step > k) return;
            current = addPoints(current.x, current.y, G.x, G.y);
            if (current.x !== null) {
                plotPoint(current.x, current.y, step === k ? "red" : "blue", step === k ? "Q" : "");
                drawLine(prev, current);
                log.innerHTML += `${step}G = (${current.x}, ${current.y})<br>`;
                log.scrollTop = log.scrollHeight;
                prev = current;
            }
            step++;
            setTimeout(animateStep, 300); // Ritardo per visualizzazione umana
        }
        
        if(k > 1) animateStep();
    });

    // Inizializza
    drawGrid();
    plotPoint(1, 5, "green", "G");

    // --- Grafici Statistici ---
    // Genero dati finti basati sulla logica ECC reale per i grafici
    let xCounts = Array(17).fill(0);
    let yCounts = Array(17).fill(0);
    let distances = [];
    let pt = {x: 1, y: 5};
    let prevPt = pt;

    for(let i=1; i<=18; i++) {
        if(pt.x !== null) {
            xCounts[pt.x]++;
            yCounts[pt.y]++;
            if(i>1) {
                let d = Math.sqrt(Math.pow(pt.x - prevPt.x, 2) + Math.pow(pt.y - prevPt.y, 2));
                distances.push(d.toFixed(2));
            }
        }
        prevPt = pt;
        pt = addPoints(pt.x, pt.y, 1, 5);
    }

    new Chart(document.getElementById('distChart'), {
        type: 'bar',
        data: {
            labels: Array.from({length: 17}, (_, i) => i),
            datasets: [
                { label: 'Freq. Coord X', data: xCounts, backgroundColor: 'rgba(54, 162, 235, 0.6)' },
                { label: 'Freq. Coord Y', data: yCounts, backgroundColor: 'rgba(255, 99, 132, 0.6)' }
            ]
        },
        options: { plugins: { title: { display: true, text: 'Frequenza Coordinate (k da 1 a 18)' } } }
    });

    new Chart(document.getElementById('avalancheChart'), {
        type: 'line',
        data: {
            labels: Array.from({length: distances.length}, (_, i) => `k=${i+1}→${i+2}`),
            datasets: [{
                label: 'Distanza dal punto precedente',
                data: distances,
                borderColor: 'rgba(75, 192, 192, 1)',
                tension: 0.1,
                fill: false
            }]
        },
        options: { plugins: { title: { display: true, text: 'Variazione Distanza tra (k)G e (k+1)G' } } }
    });
});
</script>
{% endraw %}
