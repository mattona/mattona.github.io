---
layout: page
title: Encryption & Cryptanalysis
description: Comparing ECC and Caesar ciphers, statistical leaks, and key-space attacks.
img: assets/img/hmw3.jpg
importance: 3
category: Statistics
---

For this assignment, I had to take the toy elliptic curve from the first project ($y^2 = x^3 + 7 \pmod{17}$) and actually use it to encrypt and decrypt a message, comparing it to the ancient Caesar cipher. 

Honest admission: encrypting actual text with an Elliptic Curve is a massive headache compared to Caesar. While Caesar just shifts the alphabet (e.g., 'A' becomes 'D'), ECC requires you to first map your text message to a valid $(x, y)$ coordinate on the curve. Once you have that point ($M$), encryption usually follows the ElGamal structure: you pick a random number $r$, compute a shared secret using the recipient's public key $Q$, and send a pair of points: $(rG, M + rQ)$. Decryption is the reverse process using the private key $k$. 

It's mathematically elegant, but implementing it for standard text on a 17x17 grid with only 18 valid points means I can barely encode a single letter, let alone a whole sentence. This structural difference led me to explore how both systems break, but for entirely different reasons.

### The Caesar Cipher and Statistical Leaks

The Caesar cipher is structurally completely different from ECC. Its key space is tiny (only 25 possible shifts for the English alphabet), but its fatal flaw isn't just the key space—it's the fact that it preserves the statistical properties of the underlying language. 

If 'E' is the most common letter in English, and we shift the alphabet by 3, 'H' becomes the most common letter in the ciphertext. To demonstrate this, I built a quick script that encrypts text and then runs a frequency analysis attack. Instead of a simple dot product, this updated version uses the **Chi-Square statistic** to accurately minimize the error between the ciphertext's distribution and the expected English corpus.

<div class="card py-4 my-4 z-depth-1" style="background-color: transparent; border: 1px solid rgba(150,150,150,0.3);">
    <h5 class="text-center mb-4">Caesar Cipher & Frequency Attack</h5>
    
    <!-- Input Text Row -->
    <div class="d-flex flex-column align-items-center mb-3">
        <label class="font-weight-bold mb-1" style="width: 65%; text-align: left;">Input Text:</label>
        <textarea id="caesarInput" class="form-control" rows="3" style="resize: none; width: 65%; background-color: rgba(128,128,128,0.05); color: inherit; border: 1px solid rgba(150,150,150,0.5);">This is a secret message. Cryptography is fascinating because it relies on math to secure data. The quick brown fox jumps over the lazy dog repeatedly to provide enough text for statistical analysis.</textarea>
    </div>
    
    <!-- Shift Key Row -->
    <div class="d-flex flex-column align-items-center mb-4 mt-3">
        <label class="font-weight-bold mb-1" style="width: 65%; text-align: left;">Shift Key: <span id="shiftVal" style="color: #ff9800; font-size: 1.2em;">7</span></label>
        <input type="range" id="caesarShift" class="custom-range" min="1" max="25" value="7" style="width: 65%;" oninput="document.getElementById('shiftVal').innerText = this.value">
    </div>
    
    <!-- Encrypt Button -->
    <div class="text-center mb-4">
        <button onclick="runEncryption()" class="btn font-weight-bold" style="background-color: #ff9800; color: #111; width: 250px; font-size: 1.1em; border-radius: 8px; box-shadow: 0 4px 6px rgba(0,0,0,0.2);">
            Encrypt Text
        </button>
    </div>
    
    <hr class="w-75 mx-auto" style="border-color: rgba(150,150,150,0.2);">
    
    <!-- Ciphertext Row -->
    <div class="d-flex flex-column align-items-center mb-3 mt-4">
        <label class="font-weight-bold mb-1" style="width: 65%; text-align: left;">Ciphertext:</label>
        <textarea id="caesarOutput" class="form-control" rows="3" readonly style="resize: none; width: 65%; background-color: rgba(0,0,0,0.1); color: inherit; border: 1px solid rgba(150,150,150,0.3);"></textarea>
    </div>
    
    <!-- Decrypt Button -->
    <div class="text-center mb-3 mt-3">
        <button onclick="runCryptanalysis()" class="btn font-weight-bold" style="background-color: #e74c3c; color: #fff; width: 250px; font-size: 1.1em; border-radius: 8px; box-shadow: 0 4px 6px rgba(0,0,0,0.2);">
            Run Statistical Cryptanalysis
        </button>
    </div>
    
    <div id="crackResult" class="text-center font-weight-bold mb-3" style="color: #2ecc71; font-size: 1.1em; min-height: 24px;"></div>

    <!-- Decrypted Text Row -->
    <div class="d-flex flex-column align-items-center mb-4">
        <label class="font-weight-bold mb-1" style="width: 65%; text-align: left;">Decrypted Text:</label>
        <textarea id="caesarDecrypted" class="form-control" rows="3" readonly style="resize: none; width: 65%; background-color: rgba(46,204,113,0.1); color: inherit; border: 1px solid rgba(46,204,113,0.3);"></textarea>
    </div>
    
    <!-- Chart -->
    <div class="mx-auto mt-2" style="width: 80%;">
        <canvas id="freqChart" height="100"></canvas>
    </div>
</div>

Playing around with the tool, I noticed the algorithm's effectiveness heavily depends on the ciphertext length. Give it a three-word sentence, and it fails because short texts don't normalize to standard English frequencies. But give it a decent paragraph, and the letter frequencies naturally align with the English corpus, making the attack trivial. 

### Cracking the Toy ECC

Unlike the Caesar cipher, the ECC ciphertext leaks absolutely zero statistical information about the plaintext. The coordinates are pseudo-randomly distributed over the finite field. You cannot map the frequency of $X$ coordinates to the English alphabet.

However, the toy scheme I built in Part 1 has a fatal flaw of its own: the private key space is ridiculously small. Because the curve operates modulo 17, there are only 18 valid points. That means the private key $k$ can only be an integer between 1 and 18. 

I wrote a simple loop that exploits this restricted key space. It simply calculates $1G, 2G, 3G...$ and stops when the output matches the target public key $Q$.

<div class="card py-4 my-4 z-depth-1" style="background-color: transparent; border: 1px solid rgba(150,150,150,0.3);">
    <h5 class="text-center mb-2">ECC Toy Curve Exhaustive Search</h5>
    <p class="text-center" style="font-size: 0.9em; color: #888;">Targeting Public Key Q on curve $y^2 = x^3 + 7 \pmod{17}$ with Base $G=(1,5)$</p>
    
    <!-- Target Q Row -->
    <div class="d-flex flex-column align-items-center mt-3 mb-4">
        <label class="font-weight-bold mb-1" style="width: 250px; text-align: left;">Intercepted Public Key (Q):</label>
        <select id="targetQ" class="form-control" style="width: 250px; background-color: transparent; color: inherit; border: 1px solid rgba(150,150,150,0.5);">
            <option value="5,9">Q = (5, 9)</option>
            <option value="5,8">Q = (5, 8)</option>
            <option value="2,7">Q = (2, 7)</option>
            <option value="12,16">Q = (12, 16)</option>
        </select>
    </div>
    
    <!-- Brute Force Button -->
    <div class="text-center mb-3">
        <button onclick="runEccBruteForce()" class="btn font-weight-bold" style="background-color: #9b59b6; color: #fff; width: 250px; font-size: 1.1em; border-radius: 8px; box-shadow: 0 4px 6px rgba(0,0,0,0.2);">
            Start Exhaustive Search
        </button>
    </div>
    
    <div class="mt-3 p-3 rounded mx-auto text-left" style="width: 65%; background-color: rgba(0,0,0,0.1); font-family: monospace; max-height: 150px; overflow-y: auto; border: 1px solid rgba(150,150,150,0.2);" id="eccLog">
        <i>Awaiting execution...</i>
    </div>
</div>

### Beyond Brute Force: Alternative Attacks & Design Flaws

While writing the brute-force script, I started looking into the optional prompt about alternative attacks and deeper vulnerabilities. It turns out, my toy scheme is broken on multiple levels—not just because the key space is small, but fundamentally in how it is designed.

**Alternative Attacks (Beyond Exhaustive Search)**  
If the key space were slightly larger (say, taking a few days to brute force), an exhaustive search would be inefficient. Instead, attackers use algorithmic shortcuts to solve the Elliptic Curve Discrete Logarithm Problem (ECDLP):
*   **Baby-step Giant-step (BSGS) & Pollard's rho:** These are time-memory trade-off algorithms that reduce the time complexity from $O(n)$ to roughly $O(\sqrt{n})$. For a massive curve, it's still computationally unfeasible, but it mathematically halves the effective bit-length of the security.
*   **Pohlig-Hellman algorithm:** This attack specifically targets curves where the number of points (the group order) has small prime factors. 

**Weaknesses Arising from the Restricted Key Space**  
The most obvious flaw is the prime modulus ($p=17$). Because the field is so small, the total number of valid points is only 18. This tiny key space makes brute force instantaneous. In a real-world scenario using `secp256k1`, the key space is roughly $2^{256}$. At that scale, even advanced $O(\sqrt{n})$ attacks like Pollard's rho require impossible amounts of computing power, keeping the encrypted message secure.

**Weaknesses Arising from the Toy Scheme Design Itself**  
Even if we artificially scaled this specific curve up, the *design* of the toy scheme is fundamentally flawed:
1.  **Weak Group Order (Pohlig-Hellman Vulnerability):** The number of points on my curve $y^2 = x^3 + 7 \pmod{17}$ is exactly 18. The prime factors of 18 are 2, 3, and 3. Because these factors are incredibly small, a Pohlig-Hellman attack would completely shred this curve in fractions of a second. Real curves are strictly designed to have a large prime order to prevent this exact mathematical exploit.
2.  **Deterministic Mapping (No Padding):** In my conceptual scheme, I mapped text directly to coordinates. If you map 'A' to $(1,5)$ and encrypt it deterministically, the same plaintext always produces the same ciphertext point. This leaks metadata and fails IND-CPA (Indistinguishability under Chosen Plaintext Attack). Modern ECC schemes use robust padding frameworks (like ECIES) and introduce fresh randomness for every single encryption to ensure identical messages look completely different when encrypted.

<!-- Scripts for interactive tools -->
<script src="https://cdn.jsdelivr.net/npm/chart.js"></script>
{% raw %}
<script>
// --- Global Variables & Initialization ---
const englishFreq = [8.167, 1.492, 2.782, 4.253, 12.702, 2.228, 2.015, 6.094, 6.966, 0.153, 0.772, 4.025, 2.406, 6.749, 7.507, 1.929, 0.095, 5.987, 6.327, 9.056, 2.758, 0.978, 2.360, 0.150, 1.974, 0.074];
let freqChart;

document.addEventListener("DOMContentLoaded", function() {
    const ctx = document.getElementById('freqChart').getContext('2d');
    Chart.defaults.color = "#888888";
    freqChart = new Chart(ctx, {
        type: 'bar',
        data: {
            labels: "ABCDEFGHIJKLMNOPQRSTUVWXYZ".split(""),
            datasets: [{
                label: 'Ciphertext Freq (%)',
                data: Array(26).fill(0),
                backgroundColor: 'rgba(231, 76, 60, 0.8)',
                borderRadius: 2
            }, {
                label: 'English Expected (%)',
                data: englishFreq,
                backgroundColor: 'rgba(150, 150, 150, 0.3)',
                borderRadius: 2
            }]
        },
        options: { responsive: true, scales: { y: { beginAtZero: true } } }
    });
});

// --- CAESAR CIPHER LOGIC ---
function caesarShiftText(text, shift) {
    return text.split('').map(char => {
        if (char.match(/[a-z]/i)) {
            let code = char.charCodeAt(0);
            let base = (code >= 65 && code <= 90) ? 65 : 97;
            return String.fromCharCode(((code - base + shift) % 26) + base);
        }
        return char;
    }).join('');
}

function updateFrequencies(text) {
    let counts = Array(26).fill(0);
    let total = 0;
    text.toUpperCase().replace(/[^A-Z]/g, '').split('').forEach(char => {
        counts[char.charCodeAt(0) - 65]++;
        total++;
    });
    let freqs = counts.map(c => total === 0 ? 0 : (c / total) * 100);
    if(freqChart) {
        freqChart.data.datasets[0].data = freqs;
        freqChart.update();
    }
    return freqs;
}

function runEncryption() {
    let text = document.getElementById("caesarInput").value;
    let shift = parseInt(document.getElementById("caesarShift").value);
    let cipher = caesarShiftText(text, shift);
    document.getElementById("caesarOutput").value = cipher;
    updateFrequencies(cipher);
    document.getElementById("crackResult").innerText = "";
    document.getElementById("caesarDecrypted").value = "";
}

function runCryptanalysis() {
    let cipher = document.getElementById("caesarOutput").value;
    if(!cipher) return;
    
    let bestShift = 0;
    let minChiSquare = Infinity;

    for(let s = 0; s < 26; s++) {
        let decryptedAttempt = caesarShiftText(cipher, 26 - s);
        
        let counts = Array(26).fill(0);
        let total = 0;
        decryptedAttempt.toUpperCase().replace(/[^A-Z]/g, '').split('').forEach(char => {
            counts[char.charCodeAt(0) - 65]++;
            total++;
        });
        let attemptFreqs = counts.map(c => total === 0 ? 0 : (c / total) * 100);
        
        let chiSquare = 0;
        for(let i=0; i<26; i++) {
            let expected = englishFreq[i];
            let observed = attemptFreqs[i];
            if(expected > 0) {
                chiSquare += Math.pow(observed - expected, 2) / expected;
            }
        }

        if(chiSquare < minChiSquare) {
            minChiSquare = chiSquare;
            bestShift = s;
        }
    }

    document.getElementById("crackResult").innerText = `Analysis complete! Probable key found: Shift = ${bestShift}.`;
    document.getElementById("caesarDecrypted").value = caesarShiftText(cipher, 26 - bestShift);
    document.getElementById("caesarShift").value = bestShift;
    document.getElementById("shiftVal").innerText = bestShift;
}

// --- ECC BRUTE FORCE LOGIC ---
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
    if (x2 === null) return {x: x1, y: y1};
    if (x1 === x2 && mod(y1 + y2, P) === 0) return {x: null, y: null}; // Point at infinity
    
    let m;
    if (x1 === x2 && y1 === y2) {
        if (y1 === 0) return {x: null, y: null};
        let num = 3 * Math.pow(x1, 2);
        let den = 2 * y1;
        let inv = modInverse(den, P);
        if(inv === null) return {x: null, y: null};
        m = mod(num * inv, P);
    } else {
        let num = y2 - y1;
        let den = x2 - x1;
        let inv = modInverse(den, P);
        if(inv === null) return {x: null, y: null};
        m = mod(num * inv, P);
    }
    
    let x3 = mod(Math.pow(m, 2) - x1 - x2, P);
    let y3 = mod(m * (x1 - x3) - y1, P);
    return { x: x3, y: y3 };
}

function runEccBruteForce() {
    let targetArr = document.getElementById("targetQ").value.split(",");
    let targetQ = { x: parseInt(targetArr[0]), y: parseInt(targetArr[1]) };
    let G = { x: 1, y: 5 };
    let log = document.getElementById("eccLog");
    
    log.innerHTML = "<span style='color: #9b59b6;'>Starting brute force attack...</span><br>";
    
    let current = G;
    let step = 1;
    
    function forceStep() {
        if(current.x === null) {
            log.innerHTML += `Trying k=${step}... Computed ${step}G = Point at Infinity<br>`;
        } else {
            log.innerHTML += `Trying k=${step}... Computed ${step}G = (${current.x}, ${current.y})<br>`;
        }
        
        if(current.x === targetQ.x && current.y === targetQ.y) {
            log.innerHTML += `<br><span style="color: #2ecc71; font-size: 1.1em; font-weight: bold;">[!] Success! Match found at k = ${step}. Private key compromised.</span>`;
            log.scrollTop = log.scrollHeight;
            return;
        }
        
        if(step > 25) {
            log.innerHTML += `<br><span style="color: #e74c3c;">Limit reached. Point not found in this subgroup.</span>`;
            log.scrollTop = log.scrollHeight;
            return;
        }

        current = addPoints(current.x, current.y, G.x, G.y);
        step++;
        log.scrollTop = log.scrollHeight;
        
        setTimeout(forceStep, 150); 
    }
    
    forceStep();
}
</script>
{% endraw %}
