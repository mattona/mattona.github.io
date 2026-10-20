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

If 'E' is the most common letter in English, and we shift the alphabet by 3, 'H' becomes the most common letter in the ciphertext. To demonstrate this, I built a quick script that encrypts text and then runs a basic frequency analysis attack to recover the key without any brute-forcing.

<div class="card py-4 my-4 z-depth-1" style="background-color: transparent; border: 1px solid rgba(150,150,150,0.3);">
    <h5 class="text-center mb-4">Caesar Cipher & Frequency Attack</h5>
    
    <!-- Input Text Row -->
    <div class="form-group text-center mb-3">
        <label class="font-weight-bold">Input Text:</label>
        <textarea id="caesarInput" class="form-control mx-auto" rows="3" style="resize: none; width: 60%; background-color: rgba(128,128,128,0.05); color: inherit; border: 1px solid rgba(150,150,150,0.5);">This is a secret message. Cryptography is fascinating because it relies on math to secure data. The quick brown fox jumps over the lazy dog repeatedly to provide enough text for statistical analysis.</textarea>
    </div>
    
    <!-- Shift Key Row -->
    <div class="form-group text-center mb-3 mt-4">
        <label class="font-weight-bold">Shift Key: <span id="shiftVal" style="color: #ff9800; font-size: 1.2em;">7</span></label>
        <input type="range" id="caesarShift" class="custom-range mx-auto" min="1" max="25" value="7" style="width: 60%; display: block;" oninput="document.getElementById('shiftVal').innerText = this.value">
    </div>
    
    <!-- Encrypt Button -->
    <div class="text-center mb-4 mt-4">
        <button onclick="runEncryption()" class="btn font-weight-bold" style="background-color: #ff9800; color: #111; width: 250px; font-size: 1.1em; border-radius: 8px; box-shadow: 0 4px 6px rgba(0,0,0,0.2);">
            Encrypt Text
        </button>
    </div>
    
    <hr class="w-75 mx-auto" style="border-color: rgba(150,150,150,0.2);">
    
    <!-- Ciphertext Row -->
    <div class="form-group text-center mb-4 mt-4">
        <label class="font-weight-bold">Ciphertext:</label>
        <textarea id="caesarOutput" class="form-control mx-auto" rows="3" readonly style="resize: none; width: 60%; background-color: rgba(0,0,0,0.1); color: inherit; border: 1px solid rgba(150,150,150,0.3);"></textarea>
    </div>
    
    <!-- Decrypt Button -->
    <div class="text-center mb-3 mt-4">
        <button onclick="runCryptanalysis()" class="btn font-weight-bold" style="background-color: #e74c3c; color: #fff; width: 250px; font-size: 1.1em; border-radius: 8px; box-shadow: 0 4px 6px rgba(0,0,0,0.2);">
            Run Statistical Cryptanalysis
        </button>
    </div>
    
    <div id="crackResult" class="text-center font-weight-bold mb-4" style="color: #2ecc71; font-size: 1.1em;"></div>

    <!-- Decrypted Text Row -->
    <div class="form-group text-center mb-4">
        <label class="font-weight-bold">Decrypted Text:</label>
        <textarea id="caesarDecrypted" class="form-control mx-auto" rows="3" readonly style="resize: none; width: 60%; background-color: rgba(46,204,113,0.1); color: inherit; border: 1px solid rgba(46,204,113,0.3);"></textarea>
    </div>
    
    <!-- Chart -->
    <div class="mx-auto mt-2" style="width: 80%;">
        <canvas id="freqChart" height="100"></canvas>
    </div>
</div>

Playing around with the tool, I noticed the algorithm's effectiveness heavily depends on the ciphertext length. Give it a three-word sentence, and it fails because short texts don't normalize to standard English frequencies. But give it a decent paragraph, and the letter frequencies naturally align with the English corpus, making the attack trivial. You could theoretically use exhaustive key search (trying all 25 shifts manually), but frequency analysis is programmatic and instantaneous, making it the standard approach for substitution ciphers.

### Cracking the Toy ECC

Unlike the Caesar cipher, the ECC ciphertext leaks absolutely zero statistical information about the plaintext. The coordinates are pseudo-randomly distributed over the finite field. You cannot map the frequency of $X$ coordinates to the English alphabet.

However, the toy scheme I built in Part 1 has a fatal flaw of its own: the private key space is ridiculously small. Because the curve operates modulo 17, there are only 18 valid points. That means the private key $k$ can only be an integer between 1 and 18. 

I wrote a simple loop that exploits this restricted key space. It simply calculates $1G, 2G, 3G...$ and stops when the output matches the target public key $Q$.

<div class="card py-4 my-4 z-depth-1" style="background-color: transparent; border: 1px solid rgba(150,150,150,0.3);">
    <h5 class="text-center mb-2">ECC Toy Curve Exhaustive Search</h5>
    <p class="text-center" style="font-size: 0.9em; color: #888;">Targeting Public Key Q on curve $y^2 = x^3 + 7 \pmod{17}$ with Base $G=(1,5)$</p>
    
    <!-- Target Q Row -->
    <div class="form-group text-center mt-3">
        <label class="font-weight-bold">Intercepted Public Key (Q):</label>
        <select id="targetQ" class="form-control mx-auto" style="width: 250px; background-color: transparent; color: inherit; border: 1px solid rgba(150,150,150,0.5);">
            <option value="5,9">Q = (5, 9)</option>
            <option value="5,8">Q = (5, 8)</option>
            <option value="2,7">Q = (2, 7)</option>
            <option value="12,16">Q = (12, 16)</option>
        </select>
    </div>
    
    <!-- Brute Force Button -->
    <div class="text-center mt-4">
        <button onclick="runEccBruteForce()" class="btn font-weight-bold" style="background-color: #9b59b6; color: #fff; width: 250px; font-size: 1.1em; border-radius: 8px; box-shadow: 0 4px 6px rgba(0,0,0,0.2);">
            Start Exhaustive Search
        </button>
    </div>
    
    <div class="mt-4 p-3 rounded mx-auto text-left" style="width: 60%; background-color: rgba(0,0,0,0.1); font-family: monospace; max-height: 150px; overflow-y: auto; border: 1px solid rgba(150,150,150,0.2);" id="eccLog">
        <i>Awaiting execution...</i>
    </div>
</div>

### Comparing the Vulnerabilities 

The differences between these two attacks highlight exactly why modern cryptography relies on elliptic curves over massive fields.

With the Caesar cipher, the encryption logic itself is flawed. The statistical attack works regardless of how "large" the alphabet is; even if you applied substitution to a 10,000-character language, letter frequency analysis would eventually crack it because the mapping is static and data patterns leak through. 

With ECC, the logic is sound, but my specific implementation failed because of the *parameters*. The exhaustive search attack I wrote above only works because iterating 18 times takes my browser about two milliseconds. This is known as solving the Elliptic Curve Discrete Logarithm Problem (ECDLP) by brute force. 

If this were a properly configured scheme using Ethereum's `secp256k1` curve, the prime modulus wouldn't be 17; it would be a 256-bit number. The key space jumps from 18 to roughly $2^{256}$. Running that same simple exhaustive search loop on a modern CPU would take longer than the current age of the universe. Since ECC doesn't leak statistical patterns, and the key space is too large to brute force, the encrypted message remains secure.

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
    let maxDotProduct = -1;

    for(let s = 0; s < 26; s++) {
        let decryptedAttempt = caesarShiftText(cipher, 26 - s);
        
        let counts = Array(26).fill(0);
        let total = 0;
        decryptedAttempt.toUpperCase().replace(/[^A-Z]/g, '').split('').forEach(char => {
            counts[char.charCodeAt(0) - 65]++;
            total++;
        });
        let attemptFreqs = counts.map(c => total === 0 ? 0 : (c / total) * 100);
        
        let dotProduct = 0;
        for(let i=0; i<26; i++) {
            dotProduct += attemptFreqs[i] * englishFreq[i];
        }

        if(dotProduct > maxDotProduct) {
            maxDotProduct = dotProduct;
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
