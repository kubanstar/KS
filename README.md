<html lang="ru">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Сканирование штрихкодов на iOS</title>
    <!-- Библиотека jsQR для QR-кодов -->
    <script src="https://cdn.jsdelivr.net/npm/jsqr@1.4.0/dist/jsQR.js"></script>
    <style>
        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
        }
        
        body {
            font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, sans-serif;
            background: linear-gradient(135deg, #1a2980 0%, #26d0ce 100%);
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
            text-shadow: 0 2px 4px rgba(0,0,0,0.3);
        }
        
        .header h1 {
            font-size: 32px;
            margin-bottom: 10px;
            font-weight: 700;
        }
        
        .header p {
            font-size: 16px;
            opacity: 0.9;
        }
        
        .card {
            background: rgba(255, 255, 255, 0.95);
            border-radius: 20px;
            padding: 25px;
            box-shadow: 0 10px 40px rgba(0,0,0,0.2);
            margin-bottom: 20px;
            backdrop-filter: blur(10px);
            border: 1px solid rgba(255, 255, 255, 0.2);
        }
        
        .search-section {
            margin-bottom: 20px;
        }
        
        .search-input {
            width: 100%;
            padding: 18px 20px;
            font-size: 18px;
            border: 2px solid #e0e0e0;
            border-radius: 15px;
            margin-bottom: 20px;
            transition: all 0.3s;
            background: white;
            box-shadow: 0 2px 10px rgba(0,0,0,0.05);
        }
        
        .search-input:focus {
            outline: none;
            border-color: #1a2980;
            box-shadow: 0 0 0 3px rgba(26, 41, 128, 0.1);
        }
        
        .buttons {
            display: flex;
            gap: 15px;
            margin-top: 20px;
        }
        
        .btn {
            flex: 1;
            padding: 20px;
            font-size: 18px;
            font-weight: 600;
            border: none;
            border-radius: 15px;
            cursor: pointer;
            text-align: center;
            transition: all 0.3s;
            display: flex;
            align-items: center;
            justify-content: center;
            gap: 12px;
            -webkit-tap-highlight-color: transparent;
            box-shadow: 0 4px 15px rgba(0,0,0,0.1);
        }
        
        .btn:active {
            transform: translateY(2px);
            box-shadow: 0 2px 8px rgba(0,0,0,0.1);
        }
        
        .btn-primary {
            background: linear-gradient(135deg, #1a2980 0%, #26d0ce 100%);
            color: white;
        }
        
        .btn-secondary {
            background: linear-gradient(135deg, #FF416C 0%, #FF4B2B 100%);
            color: white;
        }
        
        .results-container {
            display: none;
            margin-top: 20px;
            animation: fadeIn 0.5s ease;
        }
        
        @keyframes fadeIn {
            from { opacity: 0; transform: translateY(10px); }
            to { opacity: 1; transform: translateY(0); }
        }
        
        .results-title {
            font-size: 22px;
            font-weight: 700;
            color: #1a2980;
            margin-bottom: 20px;
            padding-bottom: 15px;
            border-bottom: 3px solid #26d0ce;
        }
        
        .product-card {
            background: white;
            padding: 20px;
            border-radius: 15px;
            margin-bottom: 15px;
            box-shadow: 0 5px 20px rgba(0,0,0,0.08);
            border-left: 5px solid #1a2980;
            transition: all 0.3s;
        }
        
        .product-card:hover {
            transform: translateY(-3px);
            box-shadow: 0 8px 25px rgba(0,0,0,0.12);
        }
        
        .product-header {
            display: flex;
            justify-content: space-between;
            align-items: flex-start;
            margin-bottom: 12px;
        }
        
        .product-article {
            font-size: 20px;
            font-weight: 700;
            color: #1a2980;
            background: linear-gradient(135deg, #f5f7ff 0%, #e3f2fd 100%);
            padding: 8px 16px;
            border-radius: 10px;
            display: inline-block;
        }
        
        .product-name {
            font-size: 17px;
            color: #333;
            line-height: 1.6;
            margin-bottom: 15px;
        }
        
        .product-price {
            font-size: 28px;
            font-weight: 800;
            background: linear-gradient(135deg, #FF416C 0%, #FF4B2B 100%);
            -webkit-background-clip: text;
            -webkit-text-fill-color: transparent;
            margin-bottom: 10px;
        }
        
        .product-barcode {
            font-family: 'Courier New', monospace;
            font-size: 15px;
            color: #666;
            background: #f8f9fa;
            padding: 10px 15px;
            border-radius: 8px;
            margin-top: 10px;
            border: 1px solid #e9ecef;
        }
        
        .no-results {
            text-align: center;
            padding: 50px 20px;
            color: #666;
        }
        
        .no-results-icon {
            font-size: 70px;
            margin-bottom: 25px;
            opacity: 0.5;
        }
        
        /* Модальное окно сканирования */
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
        
        .modal-content {
            width: 100%;
            height: 100%;
            position: relative;
            display: flex;
            flex-direction: column;
            overflow: hidden;
        }
        
        .scanner-container {
            flex: 1;
            position: relative;
            width: 100%;
            height: 100%;
            overflow: hidden;
            background: #000;
        }
        
        /* Видео элемент - исправлено для iOS */
        #cameraVideo {
            width: 100%;
            height: 100%;
            object-fit: cover;
            display: block;
            transform: scaleX(1); /* Убираем зеркальное отображение */
            -webkit-transform: scaleX(1);
            background: #000;
        }
        
        /* Canvas для анализа */
        #scanCanvas {
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
            width: 80%;
            max-width: 350px;
            height: 200px;
            border: 4px solid rgba(38, 208, 206, 0.9);
            border-radius: 20px;
            pointer-events: none;
            z-index: 10;
            box-shadow: 
                0 0 0 1000px rgba(0, 0, 0, 0.8),
                inset 0 0 20px rgba(38, 208, 206, 0.3);
        }
        
        .scan-line {
            position: absolute;
            top: 0;
            left: 0;
            width: 100%;
            height: 4px;
            background: linear-gradient(90deg, 
                transparent, 
                #26d0ce, 
                transparent);
            animation: scan 2.5s ease-in-out infinite;
            box-shadow: 0 0 15px #26d0ce;
        }
        
        @keyframes scan {
            0% {
                top: 0;
                opacity: 1;
            }
            50% {
                top: 100%;
                opacity: 1;
            }
            51% {
                opacity: 0;
            }
            100% {
                top: 0;
                opacity: 0;
            }
        }
        
        .scanner-info {
            position: absolute;
            top: calc(50% + 120px);
            left: 0;
            width: 100%;
            text-align: center;
            color: white;
            font-size: 18px;
            padding: 0 20px;
            text-shadow: 0 2px 4px rgba(0,0,0,0.8);
            z-index: 10;
            font-weight: 500;
        }
        
        .modal-controls {
            padding: 25px 20px;
            background: rgba(0, 0, 0, 0.85);
            display: flex;
            gap: 15px;
            z-index: 20;
            border-top: 1px solid rgba(255, 255, 255, 0.1);
        }
        
        .modal-btn {
            flex: 1;
            padding: 18px;
            border: none;
            border-radius: 15px;
            font-size: 18px;
            font-weight: 600;
            cursor: pointer;
            transition: all 0.3s;
            -webkit-tap-highlight-color: transparent;
            display: flex;
            align-items: center;
            justify-content: center;
            gap: 10px;
        }
        
        .modal-btn:active {
            transform: scale(0.98);
        }
        
        .modal-btn-primary {
            background: rgba(38, 208, 206, 0.9);
            color: white;
            box-shadow: 0 4px 15px rgba(38, 208, 206, 0.3);
        }
        
        .modal-btn-primary:active {
            background: rgba(38, 208, 206, 1);
        }
        
        .modal-btn-danger {
            background: rgba(255, 65, 108, 0.9);
            color: white;
            box-shadow: 0 4px 15px rgba(255, 65, 108, 0.3);
        }
        
        .modal-btn-danger:active {
            background: rgba(255, 65, 108, 1);
        }
        
        .status-message {
            position: absolute;
            top: 50%;
            left: 50%;
            transform: translate(-50%, -50%);
            color: white;
            font-size: 22px;
            font-weight: 600;
            padding: 25px 35px;
            background: rgba(0, 0, 0, 0.85);
            border-radius: 20px;
            text-align: center;
            z-index: 15;
            backdrop-filter: blur(20px);
            border: 1px solid rgba(255, 255, 255, 0.1);
            display: none;
            box-shadow: 0 10px 30px rgba(0,0,0,0.4);
            max-width: 90%;
        }
        
        .scanned-badge {
            position: absolute;
            top: 50%;
            left: 50%;
            transform: translate(-50%, -50%);
            background: linear-gradient(135deg, #26d0ce 0%, #1a2980 100%);
            color: white;
            padding: 25px 45px;
            border-radius: 20px;
            font-size: 26px;
            font-weight: bold;
            display: none;
            z-index: 100;
            box-shadow: 0 15px 40px rgba(0,0,0,0.4);
            animation: badgeAppear 0.6s ease-out;
            text-align: center;
            border: 2px solid rgba(255, 255, 255, 0.2);
        }
        
        @keyframes badgeAppear {
            0% { transform: translate(-50%, -50%) scale(0); opacity: 0; }
            70% { transform: translate(-50%, -50%) scale(1.1); opacity: 1; }
            100% { transform: translate(-50%, -50%) scale(1); opacity: 1; }
        }
        
        .loader {
            position: absolute;
            top: 50%;
            left: 50%;
            transform: translate(-50%, -50%);
            width: 70px;
            height: 70px;
            border: 5px solid rgba(38, 208, 206, 0.3);
            border-radius: 50%;
            border-top-color: #26d0ce;
            animation: spin 1.2s linear infinite;
            z-index: 15;
            display: none;
        }
        
        @keyframes spin {
            100% { transform: translate(-50%, -50%) rotate(360deg); }
        }
        
        .debug-info {
            position: absolute;
            bottom: 100px;
            left: 20px;
            background: rgba(0,0,0,0.7);
            color: #26d0ce;
            padding: 10px 15px;
            border-radius: 10px;
            font-size: 12px;
            font-family: monospace;
            display: none;
            z-index: 10;
        }
        
        /* Индикаторы */
        .indicators {
            position: absolute;
            top: 30px;
            left: 0;
            width: 100%;
            display: flex;
            justify-content: center;
            gap: 15px;
            z-index: 10;
            padding: 0 20px;
        }
        
        .indicator {
            background: rgba(0,0,0,0.7);
            color: white;
            padding: 10px 20px;
            border-radius: 25px;
            font-size: 14px;
            font-weight: 500;
            backdrop-filter: blur(10px);
            border: 1px solid rgba(255,255,255,0.1);
        }
        
        /* Адаптивность */
        @media (max-width: 600px) {
            .header h1 {
                font-size: 26px;
            }
            
            .btn {
                padding: 18px;
                font-size: 16px;
            }
            
            .card {
                padding: 20px;
            }
            
            .scan-overlay {
                width: 85%;
                height: 180px;
            }
            
            .modal-controls {
                padding: 20px 15px;
            }
            
            .modal-btn {
                padding: 16px;
                font-size: 16px;
            }
        }
        
        @media (max-height: 700px) {
            .scan-overlay {
                height: 160px;
            }
            
            .scanner-info {
                top: calc(50% + 100px);
                font-size: 16px;
            }
        }
    </style>
</head>
<body>
    <div class="container">
        <div class="header">
            <h1>📦 Сканирование штрихкодов</h1>
            <p>Оптимизировано для iOS 12+</p>
        </div>
        
        <div class="card search-section">
            <input type="text" 
                   class="search-input" 
                   id="searchInput" 
                   placeholder="Введите штрихкод или нажмите кнопку сканирования..."
                   autocomplete="off"
                   autocapitalize="none"
                   autocorrect="off">
            
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
    
    <!-- Модальное окно сканирования -->
    <div class="modal" id="scannerModal">
        <div class="modal-content">
            <div class="scanner-container">
                <div class="loader" id="scannerLoader"></div>
                
                <!-- Видео элемент для камеры -->
                <video id="cameraVideo" playsinline autoplay muted></video>
                
                <!-- Canvas для анализа -->
                <canvas id="scanCanvas"></canvas>
                
                <div class="scan-overlay">
                    <div class="scan-line"></div>
                </div>
                
                <div class="indicators">
                    <div class="indicator" id="fpsIndicator">0 FPS</div>
                    <div class="indicator" id="statusIndicator">Готов</div>
                </div>
                
                <div class="scanner-info">
                    Наведите камеру на штрихкод в рамке
                </div>
                
                <div class="status-message" id="scannerStatus"></div>
                <div class="scanned-badge" id="scannedBadge">✓ Найдено!</div>
                
                <div class="debug-info" id="debugInfo"></div>
            </div>
            
            <div class="modal-controls">
                <button class="modal-btn modal-btn-primary" id="switchCamera">
                    <span>🔄</span> Камера
                </button>
                <button class="modal-btn modal-btn-danger" id="closeScanner">
                    <span>✕</span> Закрыть
                </button>
            </div>
        </div>
    </div>

    <script>
        // Глобальные переменные
        let isScanning = false;
        let videoStream = null;
        let canvasContext = null;
        let lastScanTime = 0;
        let scanCooldown = 1500; // 1.5 секунды между сканированиями
        let useBackCamera = true;
        let frameCount = 0;
        let lastFpsTime = 0;
        let currentFps = 0;
        let isIOS = /iPad|iPhone|iPod/.test(navigator.userAgent) && !window.MSStream;
        
        // Тестовые данные товаров
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
        
        // Инициализация при загрузке страницы
        document.addEventListener('DOMContentLoaded', function() {
            console.log('Устройство iOS:', isIOS);
            
            // Установка обработчиков событий
            document.getElementById('searchButton').addEventListener('click', performSearch);
            document.getElementById('scanButton').addEventListener('click', startScanner);
            document.getElementById('closeScanner').addEventListener('click', stopScanner);
            document.getElementById('switchCamera').addEventListener('click', switchCamera);
            
            document.getElementById('searchInput').addEventListener('keypress', function(e) {
                if (e.key === 'Enter') performSearch();
            });
            
            // Автофокус на поле ввода
            setTimeout(() => {
                document.getElementById('searchInput').focus();
            }, 500);
        });
        
        // Запуск сканера
        async function startScanner() {
            try {
                console.log('Запуск сканера...');
                
                // Останавливаем предыдущий поток если есть
                await stopScanner();
                
                // Скрываем результаты поиска
                document.getElementById('resultsContainer').style.display = 'none';
                
                // Показываем модальное окно и лоадер
                document.getElementById('scannerModal').style.display = 'block';
                document.getElementById('scannerLoader').style.display = 'block';
                showStatus('Подготовка камеры...');
                
                // Получаем доступ к камере
                await initCamera();
                
                // Инициализируем canvas
                initCanvas();
                
                isScanning = true;
                
                // Скрываем лоадер
                document.getElementById('scannerLoader').style.display = 'none';
                hideStatus();
                
                // Запускаем сканирование
                startScanLoop();
                
                console.log('Сканер запущен успешно');
                
            } catch (error) {
                console.error('Ошибка запуска сканера:', error);
                showStatus('Ошибка: ' + getErrorMessage(error));
                
                // Пробуем еще раз через 2 секунды
                setTimeout(() => {
                    if (!isScanning) {
                        startScanner();
                    }
                }, 2000);
            }
        }
        
        // Инициализация камеры
        async function initCamera() {
            const video = document.getElementById('cameraVideo');
            
            // Очищаем предыдущий поток
            if (videoStream) {
                videoStream.getTracks().forEach(track => track.stop());
            }
            
            // Опции для камеры
            const constraints = {
                video: {
                    facingMode: useBackCamera ? 'environment' : 'user',
                    width: { ideal: 1280, max: 1920 },
                    height: { ideal: 720, max: 1080 },
                    frameRate: { ideal: 24, max: 30 }
                },
                audio: false
            };
            
            // Получаем доступ к камере
            videoStream = await navigator.mediaDevices.getUserMedia(constraints);
            
            // Подключаем поток к видео элементу
            video.srcObject = videoStream;
            
            // Ждем пока видео будет готово
            await new Promise((resolve) => {
                video.onloadedmetadata = () => {
                    video.play()
                        .then(() => {
                            console.log('Видео запущено, размеры:', 
                                video.videoWidth, 'x', video.videoHeight);
                            
                            // Для iOS: убираем любые трансформации
                            if (isIOS) {
                                video.style.transform = 'scaleX(1)';
                                video.style.webkitTransform = 'scaleX(1)';
                            }
                            
                            resolve();
                        })
                        .catch(err => {
                            console.error('Ошибка воспроизведения видео:', err);
                            reject(err);
                        });
                };
            });
        }
        
        // Инициализация canvas
        function initCanvas() {
            const canvas = document.getElementById('scanCanvas');
            const video = document.getElementById('cameraVideo');
            
            // Устанавливаем размеры canvas как у видео
            canvas.width = video.videoWidth;
            canvas.height = video.videoHeight;
            
            // Получаем контекст
            canvasContext = canvas.getContext('2d', { willReadFrequently: true });
        }
        
        // Запуск цикла сканирования
        function startScanLoop() {
            if (!isScanning) return;
            
            // Обновляем FPS
            updateFps();
            
            // Рисуем видео на canvas
            drawVideoToCanvas();
            
            // Анализируем изображение
            analyzeImage();
            
            // Рекурсивный вызов
            requestAnimationFrame(startScanLoop);
        }
        
        // Отрисовка видео на canvas
        function drawVideoToCanvas() {
            const video = document.getElementById('cameraVideo');
            const canvas = document.getElementById('scanCanvas');
            
            if (video.readyState !== video.HAVE_ENOUGH_DATA) return;
            
            // Очищаем canvas
            canvasContext.clearRect(0, 0, canvas.width, canvas.height);
            
            // Рисуем видео
            canvasContext.drawImage(video, 0, 0, canvas.width, canvas.height);
        }
        
        // Анализ изображения на наличие штрихкодов
        function analyzeImage() {
            if (Date.now() - lastScanTime < scanCooldown) {
                return;
            }
            
            const canvas = document.getElementById('scanCanvas');
            
            // Получаем область сканирования (центральная часть)
            const scanArea = {
                x: canvas.width * 0.2,
                y: canvas.height * 0.3,
                width: canvas.width * 0.6,
                height: canvas.height * 0.4
            };
            
            // Получаем данные изображения из области сканирования
            const imageData = canvasContext.getImageData(
                scanArea.x, scanArea.y, scanArea.width, scanArea.height
            );
            
            // Пытаемся найти QR-код с помощью jsQR
            try {
                const qrCode = jsQR(imageData.data, imageData.width, imageData.height, {
                    inversionAttempts: "dontInvert",
                });
                
                if (qrCode) {
                    console.log('Найден QR-код:', qrCode.data);
                    handleScannedCode(qrCode.data);
                    return;
                }
            } catch (error) {
                console.log('jsQR не нашел QR-код');
            }
            
            // Простой анализ штрихкодов (EAN-13, Code-128)
            analyzeBarcode(imageData);
        }
        
        // Простой анализ штрихкодов
        function analyzeBarcode(imageData) {
            // Упрощенный алгоритм поиска штрихкодов
            // В реальном приложении здесь была бы полноценная логика распознавания
            
            // Преобразуем изображение в черно-белое и ищем контрастные линии
            const width = imageData.width;
            const height = imageData.height;
            const data = imageData.data;
            
            // Ищем горизонтальные линии (вероятные штрихкоды)
            for (let y = 0; y < height; y += 3) {
                let lineData = [];
                
                // Собираем данные о яркости пикселей в строке
                for (let x = 0; x < width; x++) {
                    const index = (y * width + x) * 4;
                    const r = data[index];
                    const g = data[index + 1];
                    const b = data[index + 2];
                    const brightness = (r + g + b) / 3;
                    lineData.push(brightness > 128 ? 1 : 0); // 1 - светлый, 0 - темный
                }
                
                // Ищем паттерн штрихкода (чередование черных и белых полос)
                const pattern = findBarcodePattern(lineData);
                
                if (pattern) {
                    // Пробуем декодировать найденный паттерн
                    const barcode = decodeBarcodePattern(pattern);
                    
                    if (barcode && isValidBarcode(barcode)) {
                        console.log('Найден штрихкод:', barcode);
                        handleScannedCode(barcode);
                        break;
                    }
                }
            }
        }
        
        // Поиск паттерна штрихкода
        function findBarcodePattern(lineData) {
            let patterns = [];
            let currentRun = 1;
            let currentColor = lineData[0];
            
            // Находим последовательности одинаковых цветов
            for (let i = 1; i < lineData.length; i++) {
                if (lineData[i] === currentColor) {
                    currentRun++;
                } else {
                    patterns.push(currentRun);
                    currentRun = 1;
                    currentColor = lineData[i];
                }
            }
            patterns.push(currentRun);
            
            // Ищем паттерн EAN-13 (13 цифр, каждая состоит из 2 черных и 2 белых полос)
            if (patterns.length >= 59) { // 59 полос для EAN-13
                return patterns;
            }
            
            return null;
        }
        
        // Декодирование паттерна штрихкода (упрощенное)
        function decodeBarcodePattern(patterns) {
            // Упрощенная логика для демонстрации
            // В реальном приложении здесь была бы полная логика декодирования
            
            // Для тестирования возвращаем тестовый штрихкод
            if (patterns && patterns.length > 50) {
                return "6080010075148"; // Тестовый EAN-13
            }
            
            return null;
        }
        
        // Проверка валидности штрихкода
        function isValidBarcode(barcode) {
            // Проверяем длину EAN-13
            if (barcode.length === 13 && /^\d+$/.test(barcode)) {
                // Проверка контрольной суммы для EAN-13
                let sum = 0;
                for (let i = 0; i < 12; i++) {
                    sum += parseInt(barcode[i]) * (i % 2 === 0 ? 1 : 3);
                }
                const checksum = (10 - (sum % 10)) % 10;
                return checksum === parseInt(barcode[12]);
            }
            
            // Для других форматов просто проверяем что это строка
            return barcode && barcode.length > 0;
        }
        
        // Обработка найденного кода
        function handleScannedCode(code) {
            const now = Date.now();
            
            // Проверяем кд
            if (now - lastScanTime < scanCooldown) {
                return;
            }
            
            lastScanTime = now;
            
            console.log('Обработка найденного кода:', code);
            
            // Визуальная обратная связь
            showScannedBadge(code);
            
            // Через 1.5 секунды закрываем сканер и показываем результат
            setTimeout(() => {
                stopScanner();
                
                // Заполняем поле ввода
                document.getElementById('searchInput').value = code;
                
                // Выполняем поиск
                setTimeout(() => performSearch(), 300);
            }, 1500);
        }
        
        // Переключение камеры
        async function switchCamera() {
            useBackCamera = !useBackCamera;
            showStatus('Переключаю камеру...');
            await startScanner();
        }
        
        // Остановка сканера
        async function stopScanner() {
            isScanning = false;
            
            // Останавливаем поток камеры
            if (videoStream) {
                videoStream.getTracks().forEach(track => {
                    track.stop();
                    track.enabled = false;
                });
                videoStream = null;
            }
            
            // Очищаем видео элемент
            const video = document.getElementById('cameraVideo');
            video.srcObject = null;
            
            // Скрываем модальное окно
            document.getElementById('scannerModal').style.display = 'none';
            
            // Скрываем все вспомогательные элементы
            hideStatus();
            document.getElementById('scannedBadge').style.display = 'none';
            document.getElementById('scannerLoader').style.display = 'none';
        }
        
        // Обновление FPS
        function updateFps() {
            frameCount++;
            const now = Date.now();
            
            if (now - lastFpsTime >= 1000) {
                currentFps = Math.round((frameCount * 1000) / (now - lastFpsTime));
                frameCount = 0;
                lastFpsTime = now;
                
                document.getElementById('fpsIndicator').textContent = `${currentFps} FPS`;
                document.getElementById('statusIndicator').textContent = 'Сканирование...';
            }
        }
        
        // Поиск товара
        function performSearch() {
            const searchValue = document.getElementById('searchInput').value.trim();
            if (!searchValue) {
                alert('Введите штрихкод для поиска');
                return;
            }
            
            // Очищаем предыдущие результаты
            const productsList = document.getElementById('productsList');
            productsList.innerHTML = '';
            
            // Показываем контейнер результатов
            const resultsContainer = document.getElementById('resultsContainer');
            resultsContainer.style.display = 'block';
            
            // Ищем товар
            let found = false;
            
            // Проверяем точное совпадение штрихкода
            if (productsData[searchValue]) {
                const product = productsData[searchValue];
                displayProduct(product, searchValue);
                found = true;
            } else {
                // Ищем по части артикула
                for (const [barcode, product] of Object.entries(productsData)) {
                    if (product.article.toLowerCase().includes(searchValue.toLowerCase())) {
                        displayProduct(product, barcode);
                        found = true;
                    }
                }
            }
            
            // Если ничего не найдено
            if (!found) {
                productsList.innerHTML = `
                    <div class="no-results">
                        <div class="no-results-icon">🔍</div>
                        <div style="font-size: 18px; margin-bottom: 10px; color: #333;">
                            Товар не найден
                        </div>
                        <div style="font-size: 15px; color: #666;">
                            Штрихкод/артикул: <strong>${searchValue}</strong>
                        </div>
                        <div style="margin-top: 20px; font-size: 14px; color: #888;">
                            Попробуйте другой штрихкод или отсканируйте заново
                        </div>
                    </div>
                `;
            }
            
            // Прокручиваем к результатам
            resultsContainer.scrollIntoView({ behavior: 'smooth' });
        }
        
        // Отображение товара
        function displayProduct(product, barcode) {
            const productsList = document.getElementById('productsList');
            
            const productCard = document.createElement('div');
            productCard.className = 'product-card';
            productCard.innerHTML = `
                <div class="product-header">
                    <div class="product-article">${product.article}</div>
                </div>
                <div class="product-name">${product.name}</div>
                <div class="product-price">${product.price} руб.</div>
                <div class="product-barcode">
                    Штрихкод: ${barcode}
                </div>
            `;
            
            productsList.appendChild(productCard);
        }
        
        // Показать статус
        function showStatus(message) {
            const status = document.getElementById('scannerStatus');
            status.textContent = message;
            status.style.display = 'block';
        }
        
        function hideStatus() {
            document.getElementById('scannerStatus').style.display = 'none';
        }
        
        // Визуальная обратная связь
        function showScannedBadge(code) {
            const badge = document.getElementById('scannedBadge');
            badge.textContent = `✓ ${code}`;
            badge.style.display = 'block';
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
        
        // Горячие клавиши
        document.addEventListener('keydown', function(e) {
            if (e.key === 'Escape') {
                stopScanner();
            }
        });
    </script>
</body>
</html>