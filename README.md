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
        
        /* Видео элемент */
        #cameraVideo {
            width: 100%;
            height: 100%;
            object-fit: cover;
            display: block;
            transform: scaleX(1);
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
        
        /* Индикатор распознавания */
        .scan-indicator {
            position: absolute;
            top: 20px;
            left: 20px;
            background: rgba(0,0,0,0.7);
            color: white;
            padding: 10px 15px;
            border-radius: 10px;
            font-size: 14px;
            z-index: 10;
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
    </style>
</head>
<body>
    <div class="container">
        <div class="header">
            <h1>📦 Сканирование штрихкодов</h1>
            <p>Простой сканер для iOS</p>
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
                
                <div class="scan-indicator" id="scanIndicator">
                    Сканирование...
                </div>
                
                <div class="scanner-info">
                    Наведите камеру на штрихкод в рамке
                </div>
                
                <div class="status-message" id="scannerStatus"></div>
                <div class="scanned-badge" id="scannedBadge">✓ Найдено!</div>
            </div>
            
            <div class="modal-controls">
                <button class="modal-btn modal-btn-primary" id="takePhoto">
                    <span>📸</span> Сделать фото
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
        let scanInterval = null;
        let lastScanTime = 0;
        let scanCooldown = 2000;
        let isIOS = /iPad|iPhone|iPod/.test(navigator.userAgent) && !window.MSStream;
        let scanAttempts = 0;
        let lastBrightness = 0;
        
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
            document.getElementById('takePhoto').addEventListener('click', takePhotoAndAnalyze);
            
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
                
                // Останавливаем предыдущий поток
                await stopScanner();
                
                // Скрываем результаты поиска
                document.getElementById('resultsContainer').style.display = 'none';
                
                // Показываем модальное окно
                document.getElementById('scannerModal').style.display = 'block';
                document.getElementById('scannerLoader').style.display = 'block';
                showStatus('Запуск камеры...');
                
                // Получаем доступ к камере
                await initCamera();
                
                // Инициализируем canvas
                initCanvas();
                
                isScanning = true;
                scanAttempts = 0;
                
                // Скрываем лоадер
                document.getElementById('scannerLoader').style.display = 'none';
                hideStatus();
                
                // Запускаем сканирование
                startScanning();
                
                console.log('Сканер запущен');
                
            } catch (error) {
                console.error('Ошибка запуска сканера:', error);
                showStatus('Ошибка: ' + getErrorMessage(error));
            }
        }
        
        // Инициализация камеры
        async function initCamera() {
            const video = document.getElementById('cameraVideo');
            
            // Очищаем предыдущий поток
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
            
            // Ждем пока видео будет готово
            await new Promise((resolve) => {
                video.onloadedmetadata = () => {
                    video.play()
                        .then(() => {
                            console.log('Видео запущено');
                            resolve();
                        })
                        .catch(err => {
                            console.error('Ошибка воспроизведения видео:', err);
                            throw err;
                        });
                };
            });
            
            // Для iOS убираем трансформацию
            if (isIOS) {
                video.style.transform = 'scaleX(1)';
                video.style.webkitTransform = 'scaleX(1)';
            }
        }
        
        // Инициализация canvas
        function initCanvas() {
            const canvas = document.getElementById('scanCanvas');
            const video = document.getElementById('cameraVideo');
            
            canvas.width = video.videoWidth || 640;
            canvas.height = video.videoHeight || 480;
        }
        
        // Запуск сканирования
        function startScanning() {
            if (scanInterval) {
                clearInterval(scanInterval);
            }
            
            // Сканируем каждые 500мс
            scanInterval = setInterval(() => {
                if (!isScanning) return;
                
                try {
                    analyzeFrame();
                } catch (error) {
                    console.warn('Ошибка анализа кадра:', error);
                }
            }, 500);
        }
        
        // Анализ кадра
        function analyzeFrame() {
            const video = document.getElementById('cameraVideo');
            const canvas = document.getElementById('scanCanvas');
            const ctx = canvas.getContext('2d');
            
            // Проверяем готовность видео
            if (video.readyState !== video.HAVE_ENOUGH_DATA) {
                return;
            }
            
            // Проверяем кд
            if (Date.now() - lastScanTime < 1000) {
                return;
            }
            
            // Обновляем индикатор
            scanAttempts++;
            document.getElementById('scanIndicator').textContent = 
                `Попытка: ${scanAttempts}`;
            
            // Рисуем видео на canvas
            ctx.drawImage(video, 0, 0, canvas.width, canvas.height);
            
            // Получаем область сканирования (рамка)
            const scanArea = {
                x: canvas.width * 0.25,
                y: canvas.height * 0.35,
                width: canvas.width * 0.5,
                height: canvas.height * 0.3
            };
            
            // Получаем данные изображения
            const imageData = ctx.getImageData(
                scanArea.x, scanArea.y, scanArea.width, scanArea.height
            );
            
            // Анализируем изображение
            analyzeImageForBarcode(imageData, scanArea);
        }
        
        // Анализ изображения на наличие штрихкодов
        function analyzeImageForBarcode(imageData, scanArea) {
            const data = imageData.data;
            const width = imageData.width;
            const height = imageData.height;
            
            // Преобразуем в черно-белое и ищем контрастные линии
            let barcodeLines = [];
            
            // Сканируем по горизонтали в центре рамки
            const centerY = Math.floor(height / 2);
            
            // Получаем строку пикселей в центре
            let lineData = [];
            for (let x = 0; x < width; x++) {
                const index = (centerY * width + x) * 4;
                const r = data[index];
                const g = data[index + 1];
                const b = data[index + 2];
                const brightness = (r + g + b) / 3;
                lineData.push(brightness);
            }
            
            // Анализируем яркость для поиска полос
            const threshold = 128;
            let currentState = lineData[0] > threshold ? 'light' : 'dark';
            let currentLength = 1;
            let bars = [];
            
            for (let i = 1; i < lineData.length; i++) {
                const state = lineData[i] > threshold ? 'light' : 'dark';
                
                if (state === currentState) {
                    currentLength++;
                } else {
                    bars.push({
                        type: currentState,
                        length: currentLength
                    });
                    currentState = state;
                    currentLength = 1;
                }
            }
            bars.push({
                type: currentState,
                length: currentLength
            });
            
            // Проверяем если это похоже на штрихкод
            // У штрихкода должно быть много чередующихся полос
            if (bars.length > 20) {
                // Пробуем декодировать как EAN-13
                const barcode = tryDecodeEAN13(bars);
                
                if (barcode) {
                    console.log('Найден возможный штрихкод:', barcode);
                    handleScannedCode(barcode);
                    return;
                }
            }
            
            // Если не нашли EAN-13, пробуем другие методы
            tryOtherDecodingMethods(lineData, width);
        }
        
        // Попытка декодирования EAN-13
        function tryDecodeEAN13(bars) {
            // Упрощенная логика для демонстрации
            // В реальном приложении здесь была бы полная логика декодирования
            
            // Для начала просто возвращаем тестовый штрихкод после 5 попыток
            if (scanAttempts >= 5) {
                // Можно раскомментировать для тестирования:
                // return "6080010075148";
            }
            
            return null;
        }
        
        // Другие методы декодирования
        function tryOtherDecodingMethods(lineData, width) {
            // Простой алгоритм поиска паттернов
            const threshold = 128;
            let binaryLine = lineData.map(b => b > threshold ? '1' : '0').join('');
            
            // Ищем паттерны начала/конца штрихкода
            const startPatterns = ['101', '1101', '11101'];
            const endPatterns = ['101', '1101', '11101'];
            
            for (const pattern of startPatterns) {
                const startIndex = binaryLine.indexOf(pattern);
                if (startIndex !== -1) {
                    console.log('Найден стартовый паттерн:', pattern, 'в позиции', startIndex);
                    
                    // Пробуем извлечь данные
                    const possibleBarcode = extractPossibleBarcode(binaryLine, startIndex);
                    if (possibleBarcode && possibleBarcode.length >= 8) {
                        console.log('Возможный штрихкод:', possibleBarcode);
                        // Можно добавить валидацию и обработку
                    }
                }
            }
        }
        
        // Извлечение возможного штрихкода
        function extractPossibleBarcode(binaryLine, startIndex) {
            // Извлекаем 95 бит (стандарт для EAN-13)
            const endIndex = Math.min(startIndex + 95, binaryLine.length);
            return binaryLine.substring(startIndex, endIndex);
        }
        
        // Сделать фото и проанализировать
        async function takePhotoAndAnalyze() {
            if (!isScanning) return;
            
            const video = document.getElementById('cameraVideo');
            const canvas = document.getElementById('scanCanvas');
            const ctx = canvas.getContext('2d');
            
            // Останавливаем сканирование
            isScanning = false;
            if (scanInterval) {
                clearInterval(scanInterval);
            }
            
            showStatus('Анализ фото...');
            
            // Делаем фото
            ctx.drawImage(video, 0, 0, canvas.width, canvas.height);
            
            // Получаем все изображение
            const fullImageData = ctx.getImageData(0, 0, canvas.width, canvas.height);
            
            // Пробуем разные методы анализа
            
            // 1. Анализ по горизонтальным линиям
            const possibleCodes = [];
            
            for (let y = 0; y < canvas.height; y += 10) {
                const lineData = [];
                for (let x = 0; x < canvas.width; x++) {
                    const index = (y * canvas.width + x) * 4;
                    const r = fullImageData.data[index];
                    const g = fullImageData.data[index + 1];
                    const b = fullImageData.data[index + 2];
                    const brightness = (r + g + b) / 3;
                    lineData.push(brightness > 128 ? 1 : 0);
                }
                
                const binary = lineData.join('');
                // Простая проверка на наличие паттернов
                if (binary.includes('101') && binary.includes('010')) {
                    possibleCodes.push(binary.substring(0, 100));
                }
            }
            
            console.log('Найдено возможных кодов:', possibleCodes.length);
            
            // Если нашли что-то похожее
            if (possibleCodes.length > 0) {
                // Для тестирования используем ручной ввод
                showStatus('Сфотографировано! Введите код вручную');
                
                // Запрашиваем у пользователя ввод
                setTimeout(() => {
                    const userCode = prompt('Введите штрихкод вручную:', '');
                    if (userCode) {
                        handleScannedCode(userCode);
                    } else {
                        // Продолжаем сканирование
                        isScanning = true;
                        startScanning();
                        hideStatus();
                    }
                }, 1000);
            } else {
                showStatus('Штрихкод не найден');
                setTimeout(() => {
                    isScanning = true;
                    startScanning();
                    hideStatus();
                }, 2000);
            }
        }
        
        // Обработка найденного кода
        function handleScannedCode(code) {
            const now = Date.now();
            
            // Проверяем кд
            if (now - lastScanTime < scanCooldown) {
                return;
            }
            
            lastScanTime = now;
            
            console.log('Обработка кода:', code);
            
            // Визуальная обратная связь
            showScannedBadge(code);
            
            // Останавливаем сканирование
            isScanning = false;
            if (scanInterval) {
                clearInterval(scanInterval);
                scanInterval = null;
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
            
            if (scanInterval) {
                clearInterval(scanInterval);
                scanInterval = null;
            }
            
            if (videoStream) {
                videoStream.getTracks().forEach(track => {
                    track.stop();
                });
                videoStream = null;
            }
            
            const video = document.getElementById('cameraVideo');
            video.srcObject = null;
            
            document.getElementById('scannerModal').style.display = 'none';
            hideStatus();
            document.getElementById('scannedBadge').style.display = 'none';
            document.getElementById('scannerLoader').style.display = 'none';
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
                        <div class="no-results-icon">🔍</div>
                        <div style="font-size: 18px; margin-bottom: 10px; color: #333;">
                            Товар не найден
                        </div>
                        <div style="font-size: 15px; color: #666;">
                            Штрихкод/артикул: <strong>${searchValue}</strong>
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
        
        // Функция для ручного ввода штрихкода
        function manualBarcodeInput() {
            const code = prompt('Введите штрихкод вручную:', '');
            if (code) {
                document.getElementById('searchInput').value = code;
                performSearch();
            }
        }
    </script>
</body>
</html>