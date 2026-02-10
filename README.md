<html lang="ru">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Сканирование штрихкодов на iOS</title>
    <style>
        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
        }
        
        body {
            font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, sans-serif;
            background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
            min-height: 100vh;
            padding: 20px;
        }
        
        .container {
            max-width: 800px;
            margin: 0 auto;
        }
        
        .header {
            text-align: center;
            margin-bottom: 30px;
            color: white;
            text-shadow: 0 2px 4px rgba(0,0,0,0.2);
        }
        
        .header h1 {
            font-size: 28px;
            margin-bottom: 10px;
        }
        
        .card {
            background: white;
            border-radius: 20px;
            padding: 25px;
            box-shadow: 0 10px 30px rgba(0,0,0,0.2);
            margin-bottom: 20px;
        }
        
        .search-input {
            width: 100%;
            padding: 16px 20px;
            font-size: 18px;
            border: 2px solid #e0e0e0;
            border-radius: 12px;
            margin-bottom: 15px;
            background: #f8f9fa;
        }
        
        .buttons {
            display: flex;
            gap: 12px;
            margin-top: 15px;
        }
        
        .btn {
            flex: 1;
            padding: 18px 20px;
            font-size: 17px;
            font-weight: 600;
            border: none;
            border-radius: 12px;
            cursor: pointer;
            text-align: center;
            display: flex;
            align-items: center;
            justify-content: center;
            gap: 10px;
            -webkit-tap-highlight-color: transparent;
        }
        
        .btn-primary {
            background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
            color: white;
        }
        
        .btn-secondary {
            background: #34c759;
            color: white;
        }
        
        .results-container {
            display: none;
            margin-top: 20px;
        }
        
        .results-title {
            font-size: 20px;
            font-weight: 600;
            color: #333;
            margin-bottom: 20px;
            padding-bottom: 15px;
            border-bottom: 2px solid #f0f0f0;
        }
        
        .product-card {
            background: white;
            padding: 20px;
            border-radius: 15px;
            margin-bottom: 15px;
            box-shadow: 0 4px 15px rgba(0,0,0,0.08);
            border-left: 5px solid #667eea;
        }
        
        .product-article {
            font-size: 18px;
            font-weight: 700;
            color: #2d3436;
            background: #f8f9fa;
            padding: 6px 12px;
            border-radius: 8px;
            display: inline-block;
            margin-bottom: 10px;
        }
        
        .product-name {
            font-size: 16px;
            color: #555;
            line-height: 1.5;
            margin-bottom: 15px;
        }
        
        .product-price {
            font-size: 24px;
            font-weight: 700;
            color: #ff3b30;
            margin-bottom: 10px;
        }
        
        .no-results {
            text-align: center;
            padding: 40px 20px;
            color: #666;
        }
        
        /* Модальное окно сканирования - УПРОЩЕНО */
        .modal {
            display: none;
            position: fixed;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            background: #000;
            z-index: 1000;
        }
        
        .scanner-container {
            width: 100%;
            height: 100%;
            position: relative;
            overflow: hidden;
        }
        
        /* Основное видео - теперь видимое */
        #cameraVideo {
            width: 100%;
            height: 100%;
            object-fit: cover;
            display: block;
            transform: scaleX(1); /* Убираем инверсию */
            background: #000;
        }
        
        /* Canvas для Quagga - скрыт */
        #quaggaCanvas {
            display: none;
            position: absolute;
            top: 0;
            left: 0;
        }
        
        .scan-overlay {
            position: absolute;
            top: 50%;
            left: 50%;
            transform: translate(-50%, -50%);
            width: 70%;
            max-width: 350px;
            height: 150px;
            border: 4px solid rgba(52, 199, 89, 0.9);
            border-radius: 15px;
            pointer-events: none;
            z-index: 10;
            box-shadow: 0 0 0 1000px rgba(0, 0, 0, 0.5);
        }
        
        .scan-line {
            position: absolute;
            top: 0;
            left: 0;
            width: 100%;
            height: 4px;
            background: #34c759;
            animation: scan 2s ease-in-out infinite;
            box-shadow: 0 0 10px #34c759;
        }
        
        @keyframes scan {
            0% { top: 0; }
            50% { top: 100%; }
            100% { top: 0; }
        }
        
        .scanner-info {
            position: absolute;
            top: calc(50% + 90px);
            left: 0;
            width: 100%;
            text-align: center;
            color: white;
            font-size: 16px;
            padding: 0 20px;
            text-shadow: 0 1px 3px rgba(0,0,0,0.8);
            z-index: 10;
        }
        
        .modal-controls {
            position: absolute;
            bottom: 30px;
            left: 0;
            width: 100%;
            padding: 0 20px;
            display: flex;
            gap: 12px;
        }
        
        .modal-btn {
            flex: 1;
            padding: 16px;
            border: none;
            border-radius: 12px;
            font-size: 17px;
            font-weight: 600;
            cursor: pointer;
            -webkit-tap-highlight-color: transparent;
        }
        
        .modal-btn-primary {
            background: rgba(52, 199, 89, 0.9);
            color: white;
        }
        
        .modal-btn-danger {
            background: rgba(255, 59, 48, 0.8);
            color: white;
        }
        
        .loader {
            position: absolute;
            top: 50%;
            left: 50%;
            transform: translate(-50%, -50%);
            width: 50px;
            height: 50px;
            border: 5px solid rgba(255,255,255,0.3);
            border-radius: 50%;
            border-top-color: #34c759;
            animation: spin 1s linear infinite;
            z-index: 15;
            display: none;
        }
        
        @keyframes spin {
            100% { transform: translate(-50%, -50%) rotate(360deg); }
        }
        
        .scanned-badge {
            position: absolute;
            top: 50%;
            left: 50%;
            transform: translate(-50%, -50%);
            background: rgba(52, 199, 89, 0.95);
            color: white;
            padding: 20px 40px;
            border-radius: 15px;
            font-size: 24px;
            font-weight: bold;
            display: none;
            z-index: 100;
            box-shadow: 0 10px 30px rgba(0,0,0,0.3);
            text-align: center;
        }
    </style>
</head>
<body>
    <div class="container">
        <div class="header">
            <h1>📦 Сканирование товаров</h1>
            <p style="color: white; opacity: 0.9;">Автоматическое сканирование камерой</p>
        </div>
        
        <div class="card search-section">
            <input type="text" 
                   class="search-input" 
                   id="searchInput" 
                   placeholder="Штрихкод появится здесь после сканирования..."
                   readonly>
            
            <div class="buttons">
                <button class="btn btn-primary" id="searchButton">
                    <span>🔍</span> Найти
                </button>
                <button class="btn btn-secondary" id="scanButton">
                    <span>📷</span> Сканировать
                </button>
            </div>
        </div>
        
        <div class="card results-container" id="resultsContainer">
            <div class="results-title">Результаты поиска</div>
            <div id="productsList"></div>
        </div>
    </div>
    
    <!-- Модальное окно сканирования - УПРОЩЕНО -->
    <div class="modal" id="scannerModal">
        <div class="scanner-container">
            <div class="loader" id="scannerLoader"></div>
            
            <!-- Основное видео для отображения -->
            <video id="cameraVideo" playsinline autoplay muted></video>
            
            <!-- Canvas для Quagga (скрыт) -->
            <canvas id="quaggaCanvas"></canvas>
            
            <div class="scan-overlay">
                <div class="scan-line"></div>
            </div>
            
            <div class="scanner-info">
                Наведите камеру на штрихкод в рамке
            </div>
            
            <div class="scanned-badge" id="scannedBadge">✓ Найдено!</div>
            
            <div class="modal-controls">
                <button class="modal-btn modal-btn-primary" id="closeScanner">
                    ✕ Закрыть
                </button>
            </div>
        </div>
    </div>

    <!-- Подключаем QuaggaJS -->
    <script src="https://cdn.jsdelivr.net/npm/quagga@0.12.1/dist/quagga.min.js"></script>
    
    <script>
        // Глобальные переменные
        let isScanning = false;
        let videoStream = null;
        let lastScannedCode = '';
        let scanCooldown = 2000;
        
        // Данные товаров
        const productsData = {
            "6970787612595": {
                article: "KS-8001",
                name: "Набор для творчества 'ЧАСТИЧНАЯ ВЫКЛАДКА СТРАЗАМИ' 10*15 в пакете",
                price: "70,00"
            },
            "6132588301003": {
                article: "TS-MY88301",
                name: "Конструктор 'Гоночная машина'",
                price: "1780,00"
            },
            "4606782423066": {
                article: "30Б5Aгр_26515",
                name: "Блокнот SketchBook 30л А5ф КРАФТ без линовки жесткая подложка",
                price: "101,00"
            },
            "4606782424018": {
                article: "30Б5Aгр_26515",
                name: "Блокнот SketchBook 30л А5ф КРАФТ без линовки жесткая подложка",
                price: "101,00"
            },
            "4600797004630": {
                article: "ФЗ-407057",
                name: "Фреска с блестками Морской конек",
                price: "204,00"
            }
        };
        
        // Инициализация при загрузке
        document.addEventListener('DOMContentLoaded', function() {
            document.getElementById('searchButton').addEventListener('click', performSearch);
            document.getElementById('scanButton').addEventListener('click', startScanner);
            document.getElementById('closeScanner').addEventListener('click', stopScanner);
        });
        
        // Запуск сканера
        async function startScanner() {
            try {
                console.log('Запуск сканера...');
                
                // Останавливаем предыдущий сканер
                await stopScanner();
                
                // Скрываем результаты
                document.getElementById('resultsContainer').style.display = 'none';
                
                // Показываем модальное окно и лоадер
                document.getElementById('scannerModal').style.display = 'block';
                document.getElementById('scannerLoader').style.display = 'block';
                
                // 1. Сначала запускаем камеру отдельно
                await startCamera();
                
                // 2. Затем инициализируем Quagga с нашим видеопотоком
                await initQuagga();
                
                isScanning = true;
                
                // Скрываем лоадер
                document.getElementById('scannerLoader').style.display = 'none';
                
                console.log('Сканер запущен');
                
            } catch (error) {
                console.error('Ошибка запуска сканера:', error);
                alert('Ошибка: ' + getErrorMessage(error));
            }
        }
        
        // Запуск камеры (отдельно от Quagga)
        async function startCamera() {
            const video = document.getElementById('cameraVideo');
            
            // Останавливаем предыдущий поток
            if (videoStream) {
                videoStream.getTracks().forEach(track => track.stop());
            }
            
            // Для iOS используем тыловую камеру
            const constraints = {
                video: {
                    facingMode: 'environment',
                    width: { ideal: 1280 },
                    height: { ideal: 720 },
                    frameRate: { ideal: 24 }
                },
                audio: false
            };
            
            // Получаем доступ к камере
            videoStream = await navigator.mediaDevices.getUserMedia(constraints);
            
            // Подключаем поток к видео элементу
            video.srcObject = videoStream;
            
            // Ждем загрузки видео
            await new Promise((resolve) => {
                video.onloadedmetadata = () => {
                    video.play()
                        .then(resolve)
                        .catch(err => {
                            console.error('Ошибка воспроизведения видео:', err);
                            throw err;
                        });
                };
            });
            
            console.log('Камера запущена');
        }
        
        // Инициализация Quagga с нашим видеопотоком
        async function initQuagga() {
            return new Promise((resolve, reject) => {
                const video = document.getElementById('cameraVideo');
                const canvas = document.getElementById('quaggaCanvas');
                
                // Настраиваем canvas как у видео
                canvas.width = video.videoWidth;
                canvas.height = video.videoHeight;
                
                // Конфигурация Quagga
                const config = {
                    inputStream: {
                        name: "Live",
                        type: "LiveStream",
                        target: canvas, // Используем canvas вместо video
                        constraints: {
                            width: video.videoWidth,
                            height: video.videoHeight,
                            facingMode: 'environment'
                        },
                        area: {
                            top: "30%",
                            right: "15%",
                            left: "15%",
                            bottom: "30%"
                        }
                    },
                    decoder: {
                        readers: [
                            "ean_reader",
                            "ean_8_reader",
                            "code_128_reader"
                        ],
                        multiple: false
                    },
                    locate: true,
                    frequency: 5, // Меньше кадров для производительности
                    numOfWorkers: 1, // Один воркер для iOS
                    debug: {
                        drawBoundingBox: false,
                        showFrequency: false,
                        drawScanline: false,
                        showPattern: false
                    }
                };
                
                // Инициализация Quagga
                Quagga.init(config, function(err) {
                    if (err) {
                        console.error("Ошибка инициализации Quagga:", err);
                        reject(err);
                        return;
                    }
                    
                    console.log("Quagga инициализирован");
                    
                    // Обработка обнаруженных штрихкодов
                    Quagga.onDetected(function(result) {
                        if (result && result.codeResult) {
                            const code = result.codeResult.code;
                            console.log("Найден штрихкод:", code, "Формат:", result.codeResult.format);
                            handleScannedCode(code);
                        }
                    });
                    
                    // Запускаем Quagga
                    Quagga.start();
                    resolve();
                });
            });
        }
        
        // Обработка найденного штрихкода
        function handleScannedCode(code) {
            const now = Date.now();
            
            // Проверяем кд
            if (now - lastScannedCode.timestamp < scanCooldown && 
                lastScannedCode.code === code) {
                console.log('Дубликат, пропускаем');
                return;
            }
            
            // Обновляем время последнего сканирования
            lastScannedCode = {
                code: code,
                timestamp: now
            };
            
            console.log('Обработка штрихкода:', code);
            
            // Визуальная обратная связь
            showScannedBadge(code);
            
            // Останавливаем сканирование
            if (isScanning) {
                Quagga.stop();
                isScanning = false;
            }
            
            // Через 1.5 секунды закрываем сканер
            setTimeout(() => {
                stopScanner();
                
                // Заполняем поле ввода
                document.getElementById('searchInput').value = code;
                
                // Выполняем поиск
                setTimeout(() => performSearch(), 300);
            }, 1500);
        }
        
        // Остановка сканера
        async function stopScanner() {
            isScanning = false;
            
            // Останавливаем Quagga
            try {
                Quagga.stop();
            } catch (e) {
                console.log('Quagga уже остановлен');
            }
            
            // Останавливаем поток камеры
            if (videoStream) {
                videoStream.getTracks().forEach(track => {
                    track.stop();
                });
                videoStream = null;
            }
            
            const video = document.getElementById('cameraVideo');
            video.srcObject = null;
            
            document.getElementById('scannerModal').style.display = 'none';
            document.getElementById('scannedBadge').style.display = 'none';
            document.getElementById('scannerLoader').style.display = 'none';
        }
        
        // Визуальная обратная связь
        function showScannedBadge(code) {
            const badge = document.getElementById('scannedBadge');
            badge.textContent = `✓ ${code}`;
            badge.style.display = 'block';
        }
        
        // Поиск товара
        function performSearch() {
            const searchValue = document.getElementById('searchInput').value.trim();
            if (!searchValue) {
                alert('Введите штрихкод для поиска');
                return;
            }
            
            const productsList = document.getElementById('productsList');
            productsList.innerHTML = '';
            
            const resultsContainer = document.getElementById('resultsContainer');
            resultsContainer.style.display = 'block';
            
            let found = false;
            
            if (productsData[searchValue]) {
                const product = productsData[searchValue];
                displayProduct(product, searchValue);
                found = true;
            } else {
                for (const [barcode, product] of Object.entries(productsData)) {
                    if (product.article.toLowerCase().includes(searchValue.toLowerCase())) {
                        displayProduct(product, barcode);
                        found = true;
                    }
                }
            }
            
            if (!found) {
                productsList.innerHTML = `
                    <div class="no-results">
                        <div style="font-size: 48px; margin-bottom: 20px;">🔍</div>
                        <div style="font-size: 18px; margin-bottom: 10px; color: #333;">
                            Товар не найден
                        </div>
                        <div style="font-size: 15px; color: #666;">
                            Штрихкод: <strong>${searchValue}</strong>
                        </div>
                    </div>
                `;
            }
            
            resultsContainer.scrollIntoView({ behavior: 'smooth' });
        }
        
        // Отображение товара
        function displayProduct(product, barcode) {
            const productsList = document.getElementById('productsList');
            
            const productCard = document.createElement('div');
            productCard.className = 'product-card';
            productCard.innerHTML = `
                <div class="product-article">${product.article}</div>
                <div class="product-name">${product.name}</div>
                <div class="product-price">${product.price} руб.</div>
                <div style="font-family: 'Courier New', monospace; font-size: 14px; color: #666; margin-top: 10px;">
                    Штрихкод: ${barcode}
                </div>
            `;
            
            productsList.appendChild(productCard);
        }
        
        // Получить понятное сообщение об ошибке
        function getErrorMessage(error) {
            if (error.name === 'NotAllowedError') {
                return 'Разрешите доступ к камере в настройках';
            } else if (error.name === 'NotFoundError') {
                return 'Камера не найдена';
            } else if (error.name === 'NotReadableError') {
                return 'Камера используется другим приложением';
            } else {
                return error.message || 'Неизвестная ошибка';
            }
        }
    </script>
</body>
</html>