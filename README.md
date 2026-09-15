	<!DOCTYPE html>
<html lang="tr">
<head>
    <meta charset="UTF-8">
    <title>8-Haneli ve 2-Haneli Ekranlı Röle Bilgisayar Simülasyonu</title>
    <link rel="stylesheet" href="style.css">
</head>
<body>

    <div class="pcb-board">
        <div style="font-size: 12px; color: #78909c; text-transform: uppercase; letter-spacing: 1px;">ARDA-RELE LOGIC UNIT // 8+2 DISPLAY SYSTEM</div>

        <!-- 1. 4x12 Röle Matrisi -->
        <div class="relay-grid" id="relayGrid"></div>

        <!-- 2. LED Hatları -->
        <div class="led-bus" id="ledBus"></div>

        <!-- 3. Alt Kontrol Paneli (8 Haneli + 2 Haneli Ekranlar) -->
        <div class="bottom-panel">
            <div class="screens-wrapper">
                <!-- Ana Ekran (8 Haneli) -->
                <div class="display-container" id="mainDisplay">
                    <div class="digit">n</div>
                    <div class="digit">u</div>
                    <div class="digit">l</div>
                    <div class="digit">l</div>
                    <div class="digit"></div>
                    <div class="digit"></div>
                    <div class="digit"></div>
                    <div class="digit"></div>
                </div>
                <!-- İkinci Ekran (2 Haneli) -->
                <div class="secondary-display-container" id="secDisplay">
                    <div class="digit-small"></div>
                    <div class="digit-small"></div>
                </div>
            </div>

            <div class="controls">
                <button class="btn btn-start" id="runBtn">Sayacı Başlat</button>
                <button class="btn" id="resetBtn">Sıfırla</button>
            </div>
        </div>
    </div>

    <script>
        const totalRelays = 48;
const totalLeds = 36;

const harfler_sayilar = "qwertyuıopğüasghjklşizxcvbnmöçQWERTYUIOPĞÜASDFGHJKLŞİZXCVBNMÖÇ123456789/*-+";

const relayGrid = document.getElementById('relayGrid');
const ledBus = document.getElementById('ledBus');

const mainDigits = document.querySelectorAll('#mainDisplay .digit');
const secDigits = document.querySelectorAll('#secDisplay .digit-small');

const runBtn = document.getElementById('runBtn');
const resetBtn = document.getElementById('resetBtn');

let mainCounter = 0;
let secCounter = 1;

let isRunning = false;
let intervalId = null;


// Röleleri Oluştur
for (let i = 0; i < totalRelays; i++) {
    const relay = document.createElement('div');

    relay.classList.add('relay');

    relay.addEventListener('click', () => {
        relay.classList.toggle('active');
    });

    relayGrid.appendChild(relay);
}


// LED'leri Oluştur
for (let i = 0; i < totalLeds; i++) {
    const led = document.createElement('div');

    led.classList.add('led');

    ledBus.appendChild(led);
}

const leds = document.querySelectorAll('.led');


// Ekranları Güncelle
function updateDisplays(mainVal, secVal) {

    // 8 haneli ekran
    let metin = "";

    for (let i = 0; i < 8; i++) {
        metin += harfler_sayilar[
            (mainVal + i) % harfler_sayilar.length
        ];
    }

    for (let i = 0; i < 8; i++) {
        mainDigits[i].textContent = metin[i];
    }


    // 2 haneli ekran
    const sayi = secVal.toString().padStart(2, "0");

    for (let i = 0; i < 2; i++) {
        secDigits[i].textContent = sayi[i];
    }
}


// LED'leri Rastgele Yak
function flashLeds() {

    leds.forEach(led => {
        led.classList.remove('active');
    });

    const activeCount = (mainCounter % 15) + 10;

    for (let i = 0; i < activeCount; i++) {

        const randomIndex =
            Math.floor(Math.random() * leds.length);

        leds[randomIndex].classList.add('active');
    }
}


// Sayacı Başlat
function startCounting() {

    if (isRunning) return;

    isRunning = true;

    runBtn.textContent = "Durdur";
    runBtn.classList.remove('btn-start');


    intervalId = setInterval(() => {

        // 8 haneli ekran için karakter ilerlet
        mainCounter++;

        if (mainCounter >= harfler_sayilar.length) {
            mainCounter = 0;
        }


        // 2 haneli ekran için sayı ilerlet
        secCounter++;

        if (secCounter > 99) {
            secCounter = 0;
        }


        // Ekranları güncelle
        updateDisplays(mainCounter, secCounter);


        // LED'leri değiştir
        flashLeds();


        // Rastgele röle değiştir
        if (mainCounter % 6 === 0) {

            const relayToToggle =
                document.querySelector(
                    `.relay:nth-child(${
                        Math.floor(Math.random() * totalRelays) + 1
                    })`
                );

            if (relayToToggle) {
                relayToToggle.classList.toggle('active');
            }
        }

    }, 120);
}


// Sayacı Durdur
function stopCounting() {

    isRunning = false;

    runBtn.textContent = "Sayacı Başlat";
    runBtn.classList.add('btn-start');

    clearInterval(intervalId);

    intervalId = null;
}


// Sıfırla
function resetCounter() {

    stopCounting();

    mainCounter = 0;
    secCounter = 20;


    // Ekranları sıfırla
    updateDisplays(mainCounter, secCounter);


    // LED'leri kapat
    leds.forEach(led => {
        led.classList.remove('active');
    });


    // Röleleri kapat
    document.querySelectorAll('.relay.active').forEach(relay => {
        relay.classList.remove('active');
    });
}


// Başlat / Durdur
runBtn.addEventListener('click', () => {

    if (isRunning) {
        stopCounting();
    } else {
        startCounting();
    }

});


// Sıfırla
resetBtn.addEventListener('click', resetCounter);


// İlk ekran görüntüsü
updateDisplays(mainCounter, secCounter);
    </script>
</body>
</html>
