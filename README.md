<html lang="ru">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Сканирование штрихкодов на iOS</title>
    <!-- Библиотека QuaggaJS для сканирования штрихкодов -->
    <script src="https://cdn.jsdelivr.net/npm/quagga@0.12.1/dist/quagga.min.js"></script>
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
        
        .header p {
            font-size: 16px;
            opacity: 0.9;
        }
        
        .card {
            background: white;
            border-radius: 20px;
            padding: 25px;
            box-shadow: 0 10px 30px rgba(0,0,0,0.2);
            margin-bottom: 20px;
            backdrop-filter: blur(10px);
        }
        
        .search-section {
            margin-bottom: 20px;
        }
        
        .search-input {
            width: 100%;
            padding: 16px 20px;
            font-size: 18px;
            border: 2px solid #e0e0e0;
            border-radius: 12px;
            margin-bottom: 15px;
            transition: all 0.3s;
            background: #f8f9fa;
        }
        
        .search-input:focus {
            outline: none;
            border-color: #667eea;
            box-shadow: 0 0 0 3px rgba(102, 126, 234, 0.1);
            background: white;
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
            transition: all 0.3s;
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
        
        .btn-primary:active {
            transform: scale(0.98);
            opacity: 0.9;
        }
        
        .btn-secondary {
            background: #34c759;
            color: white;
        }
        
        .btn-secondary:active {
            transform: scale(0.98);
            opacity: 0.9;
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
            transition: transform 0.3s;
        }
        
        .product-card:hover {
            transform: translateY(-2px);
        }
        
        .product-header {
            display: flex;
            justify-content: space-between;
            align-items: flex-start;
            margin-bottom: 12px;
        }
        
        .product-article {
            font-size: 18px;
            font-weight: 700;
            color: #2d3436;
            background: #f8f9fa;
            padding: 6px 12px;
            border-radius: 8px;
            display: inline-block;
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
        
        .product-barcode {
            font-family: 'Courier New', monospace;
            font-size: 14px;
            color: #666;
            background: #f8f9fa;
            padding: 8px 12px;
            border-radius: 6px;
            margin-top: 10px;
        }
        
        .no-results {
            text-align: center;
            padding: 40px 20px;
            color: #666;
        }
        
        .no-results-icon {
            font-size: 60px;
            margin-bottom: 20px;
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
            background: rgba(0, 0, 0, 0.95);
            z-index: 1000;
        }
        
        .modal-content {
            width: 100%;
            height: 100%;
            position: relative;
            display: flex;
            flex-direction: column;
        }
        
        .scanner-container {
            flex: 1;
            position: relative;
            overflow: hidden;
        }
        
        #scanner {
            width: 100%;
            height: 100%;
            object-fit: cover;
        }
        
        .scan-overlay {
            position: absolute;
            top: 50%;
            left: 50%;
            transform: translate(-50%, -50%);
            width: 80%;
            max-width: 400px;
            height: 200px;
            border: 4px solid rgba(52, 199, 89, 0.8);
            border-radius: 15px;
            pointer-events: none;
            box-shadow: 0 0 0 1000px rgba(0, 0, 0, 0.5);
        }
        
        .scan-line {
            position: absolute;
            top: 0;
            left: 0;
            width: 100%;
            height: 4px;
            background: linear-gradient(90deg, 
                transparent, 
                #34c759, 
                transparent);
            animation: scan 2s ease-in-out infinite;
            box-shadow: 0 0 10px #34c759;
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
            font-size: 16px;
            padding: 0 20px;
            text-shadow: 0 1px 3px rgba(0,0,0,0.8);
        }
        
        .modal-controls {
            padding: 20px;
            background: rgba(0, 0, 0, 0.8);
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
            transition: all 0.3s;
            -webkit-tap-highlight-color: transparent;
        }
        
        .modal-btn-primary {
            background: rgba(255, 255, 255, 0.2);
            color: white;
            backdrop-filter: blur(10px);
        }
        
        .modal-btn-primary:active {
            background: rgba(255, 255, 255, 0.3);
        }
        
        .modal-btn-danger {
            background: rgba(255, 59, 48, 0.8);
            color: white;
        }
        
        .modal-btn-danger:active {
            background: rgba(255, 59, 48, 0.9);
        }
        
        .flash-indicator {
            position: absolute;
            top: 20px;
            right: 20px;
            width: 50px;
            height: 50px;
            background: rgba(255, 204, 0, 0.9);
            border-radius: 50%;
            display: none;
            align-items: center;
            justify-content: center;
            font-size: 24px;
            box-shadow: 0 0 20px rgba(255, 204, 0, 0.5);
            animation: pulse 1s infinite;
        }
        
        @keyframes pulse {
            0% { transform: scale(1); }
            50% { transform: scale(1.1); }
            100% { transform: scale(1); }
        }
        
        .status-message {
            position: absolute;
            top: 20px;
            left: 0;
            width: 100%;
            text-align: center;
            color: white;
            font-size: 18px;
            font-weight: 600;
            padding: 10px;
            background: rgba(0, 0, 0, 0.5);
            backdrop-filter: blur(10px);
            display: none;
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
            animation: badgeAppear 0.5s ease-out;
        }
        
        @keyframes badgeAppear {
            0% { transform: translate(-50%, -50%) scale(0); opacity: 0; }
            70% { transform: translate(-50%, -50%) scale(1.1); opacity: 1; }
            100% { transform: translate(-50%, -50%) scale(1); opacity: 1; }
        }
        
        .debug-info {
            position: absolute;
            bottom: 80px;
            left: 10px;
            background: rgba(0,0,0,0.7);
            color: #34c759;
            padding: 8px 12px;
            border-radius: 6px;
            font-size: 12px;
            font-family: monospace;
            display: none;
        }
        
        .camera-selector {
            position: absolute;
            top: 20px;
            left: 20px;
            z-index: 10;
        }
        
        .camera-select {
            background: rgba(0,0,0,0.7);
            color: white;
            border: 1px solid rgba(255,255,255,0.3);
            padding: 8px 12px;
            border-radius: 8px;
            font-size: 14px;
        }
        
        /* Адаптивность */
        @media (max-width: 600px) {
            .header h1 {
                font-size: 24px;
            }
            
            .btn {
                padding: 16px;
                font-size: 16px;
            }
            
            .card {
                padding: 20px;
            }
            
            .scan-overlay {
                width: 90%;
                height: 180px;
            }
        }
        
        .loader {
            display: none;
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
        }
        
        @keyframes spin {
            100% { transform: translate(-50%, -50%) rotate(360deg); }
        }
    </style>
</head>
<body>
    <div class="container">
        <div class="header">
            <h1>📦 Сканирование товаров</h1>
            <p>Используется QuaggaJS для работы на iOS</p>
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
                <video id="scanner" playsinline></video>
                <canvas id="scannerCanvas" style="display:none;"></canvas>
                
                <div class="scan-overlay">
                    <div class="scan-line"></div>
                </div>
                
                <div class="scanner-info">
                    Наведите камеру на штрихкод в рамке
                </div>
                
                <div class="status-message" id="scannerStatus"></div>
                <div class="scanned-badge" id="scannedBadge">✓ Найдено!</div>
                <div class="flash-indicator" id="flashIndicator">⚡</div>
                <div class="debug-info" id="debugInfo"></div>
                
                <div class="camera-selector">
                    <select class="camera-select" id="cameraSelect">
                        <option value="environment">Тыловая камера</option>
                        <option value="user">Фронтальная камера</option>
                    </select>
                </div>
            </div>
            
            <div class="modal-controls">
                <button class="modal-btn modal-btn-primary" id="toggleTorch">
                    💡 Фонарик
                </button>
                <button class="modal-btn modal-btn-danger" id="closeScanner">
                    ✕ Закрыть
                </button>
            </div>
        </div>
    </div>

    <script>
        // Глобальные переменные
        let isScannerInitialized = false;
        let isScanning = false;
        let lastScannedCode = '';
        let scanCooldown = 2000; // 2 секунды между сканированиями
        let torchEnabled = false;
        let currentStream = null;
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
            console.log('Quagga доступен:', typeof Quagga !== 'undefined');
            
            // Установка обработчиков событий
            document.getElementById('searchButton').addEventListener('click', performSearch);
            document.getElementById('scanButton').addEventListener('click', openScanner);
            document.getElementById('closeScanner').addEventListener('click', closeScanner);
            document.getElementById('toggleTorch').addEventListener('click', toggleTorch);
            document.getElementById('cameraSelect').addEventListener('change', changeCamera);
            
            document.getElementById('searchInput').addEventListener('keypress', function(e) {
                if (e.key === 'Enter') performSearch();
            });
            
            // Автофокус на поле ввода
            setTimeout(() => {
                document.getElementById('searchInput').focus();
            }, 500);
            
            // Показать сообщение о поддержке
            if (isIOS) {
                console.log('Используется iOS, активирован QuaggaJS');
            }
        });
        
        // Открытие сканера
        async function openScanner() {
            try {
                console.log('Открытие сканера QuaggaJS...');
                
                // Показываем модальное окно и лоадер
                document.getElementById('scannerModal').style.display = 'block';
                document.getElementById('scannerLoader').style.display = 'block';
                showScannerStatus('Инициализация сканера...');
                
                // Инициализируем Quagga
                await initQuagga();
                
                // Запускаем сканирование
                Quagga.start();
                isScanning = true;
                
                // Скрываем лоадер
                document.getElementById('scannerLoader').style.display = 'none';
                hideScannerStatus();
                
                console.log('Сканер запущен');
                
            } catch (error) {
                console.error('Ошибка инициализации сканера:', error);
                showScannerStatus('Ошибка: ' + error.message);
                
                // Пробуем альтернативный метод
                setTimeout(() => {
                    initAlternativeScanner();
                }, 1000);
            }
        }
        
        // Инициализация QuaggaJS
        function initQuagga() {
            return new Promise((resolve, reject) => {
                // Получаем выбранную камеру
                const cameraId = document.getElementById('cameraSelect').value;
                
                // Настройки Quagga для iOS
                const config = {
                    inputStream: {
                        name: "Live",
                        type: "LiveStream",
                        target: document.querySelector('#scanner'),
                        constraints: {
                            facingMode: cameraId,
                            width: { min: 640, ideal: 1280, max: 1920 },
                            height: { min: 480, ideal: 720, max: 1080 },
                            aspectRatio: { ideal: 4/3 }
                        },
                        area: {
                            top: "25%",
                            right: "10%",
                            left: "10%",
                            bottom: "25%"
                        }
                    },
                    decoder: {
                        readers: [
                            "ean_reader",
                            "ean_8_reader",
                            "code_128_reader",
                            "code_39_reader",
                            "codabar_reader",
                            "upc_reader",
                            "upc_e_reader"
                        ],
                        multiple: false
                    },
                    locate: true,
                    frequency: 10, // Частота проверки (кадров в секунду)
                    numOfWorkers: navigator.hardwareConcurrency || 2,
                    debug: {
                        drawBoundingBox: true,
                        showFrequency: false,
                        drawScanline: true,
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
                    
                    console.log("Quagga успешно инициализирован");
                    isScannerInitialized = true;
                    
                    // Получаем поток для управления фонариком
                    const video = document.getElementById('scanner');
                    if (video.srcObject) {
                        currentStream = video.srcObject;
                    }
                    
                    // Обработка обнаруженных штрихкодов
                    Quagga.onProcessed(function(result) {
                        drawDebugOverlay(result);
                    });
                    
                    Quagga.onDetected(function(result) {
                        if (result && result.codeResult) {
                            const code = result.codeResult.code;
                            console.log("Найден штрихкод:", code, "Формат:", result.codeResult.format);
                            handleScannedCode(code);
                        }
                    });
                    
                    resolve();
                });
            });
        }
        
        // Альтернативный метод сканирования (если Quagga не работает)
        function initAlternativeScanner() {
            console.log('Запуск альтернативного сканера...');
            showScannerStatus('Используется альтернативный метод...');
            
            // Простая реализация с canvas и анализом изображения
            const video = document.getElementById('scanner');
            const canvas = document.getElementById('scannerCanvas');
            const ctx = canvas.getContext('2d');
            
            // Запрашиваем доступ к камере
            navigator.mediaDevices.getUserMedia({
                video: {
                    facingMode: document.getElementById('cameraSelect').value,
                    width: { ideal: 1280 },
                    height: { ideal: 720 }
                },
                audio: false
            }).then(stream => {
                currentStream = stream;
                video.srcObject = stream;
                video.play();
                
                document.getElementById('scannerLoader').style.display = 'none';
                showScannerStatus('Наведите на штрихкод...');
                
                // Запускаем простой анализ изображений
                startSimpleScanning(video, canvas, ctx);
                
            }).catch(err => {
                console.error('Ошибка доступа к камере:', err);
                showScannerStatus('Ошибка камеры: ' + err.message);
            });
        }
        
        // Простой метод сканирования (запасной вариант)
        function startSimpleScanning(video, canvas, ctx) {
            let scanning = true;
            
            function scanFrame() {
                if (!scanning) return;
                
                try {
                    // Устанавливаем размеры canvas
                    canvas.width = video.videoWidth;
                    canvas.height = video.videoHeight;
                    
                    // Рисуем текущий кадр
                    ctx.drawImage(video, 0, 0, canvas.width, canvas.height);
                    
                    // Получаем данные изображения
                    const imageData = ctx.getImageData(0, 0, canvas.width, canvas.height);
                    
                    // Простой алгоритм поиска штрихкода
                    // (Это упрощенная реализация, в реальном приложении нужна библиотека)
                    
                    // Показываем в debug
                    updateDebugInfo(`Анализ: ${canvas.width}x${canvas.height}`);
                    
                } catch (error) {
                    console.warn('Ошибка анализа:', error);
                }
                
                // Рекурсивный вызов
                requestAnimationFrame(scanFrame);
            }
            
            // Запускаем сканирование
            scanFrame();
            
            // Возвращаем функцию остановки
            return () => {
                scanning = false;
            };
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
            
            // Через 1.5 секунды закрываем сканер и показываем результат
            setTimeout(() => {
                closeScanner();
                
                // Заполняем поле ввода
                document.getElementById('searchInput').value = code;
                
                // Выполняем поиск
                setTimeout(() => performSearch(), 300);
            }, 1500);
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
        
        // Закрытие сканера
        function closeScanner() {
            console.log('Закрытие сканера...');
            
            // Останавливаем Quagga
            if (isScannerInitialized && isScanning) {
                Quagga.stop();
                isScanning = false;
            }
            
            // Останавливаем поток камеры
            if (currentStream) {
                currentStream.getTracks().forEach(track => track.stop());
                currentStream = null;
            }
            
            // Скрываем модальное окно
            document.getElementById('scannerModal').style.display = 'none';
            
            // Скрываем все вспомогательные элементы
            hideScannerStatus();
            document.getElementById('scannedBadge').style.display = 'none';
            document.getElementById('flashIndicator').style.display = 'none';
            
            // Выключаем фонарик
            torchEnabled = false;
        }
        
        // Переключение фонарика
        function toggleTorch() {
            if (!currentStream) return;
            
            const videoTrack = currentStream.getVideoTracks()[0];
            
            if (videoTrack && 'applyConstraints' in videoTrack) {
                try {
                    torchEnabled = !torchEnabled;
                    
                    // Пытаемся включить фонарик
                    videoTrack.applyConstraints({
                        advanced: [{ torch: torchEnabled }]
                    }).then(() => {
                        console.log('Фонарик:', torchEnabled ? 'включен' : 'выключен');
                        
                        // Показываем индикатор
                        const indicator = document.getElementById('flashIndicator');
                        indicator.style.display = torchEnabled ? 'flex' : 'none';
                        indicator.textContent = torchEnabled ? '⚡ ON' : '⚡';
                        
                    }).catch(err => {
                        console.warn('Фонарик не поддерживается:', err);
                        torchEnabled = false;
                        document.getElementById('flashIndicator').style.display = 'none';
                    });
                } catch (error) {
                    console.warn('Ошибка управления фонариком:', error);
                }
            }
        }
        
        // Смена камеры
        function changeCamera() {
            if (isScanning) {
                // Перезапускаем сканер с новой камерой
                closeScanner();
                setTimeout(openScanner, 500);
            }
        }
        
        // Визуальная обратная связь при успешном сканировании
        function showScannedBadge(code) {
            const badge = document.getElementById('scannedBadge');
            badge.textContent = `✓ ${code}`;
            badge.style.display = 'block';
        }
        
        // Показать статус сканера
        function showScannerStatus(message) {
            const status = document.getElementById('scannerStatus');
            status.textContent = message;
            status.style.display = 'block';
        }
        
        function hideScannerStatus() {
            document.getElementById('scannerStatus').style.display = 'none';
        }
        
        // Отладочная информация
        function updateDebugInfo(message) {
            const debugEl = document.getElementById('debugInfo');
            debugEl.textContent = message;
            debugEl.style.display = 'block';
        }
        
        // Отрисовка отладочной информации от Quagga
        function drawDebugOverlay(result) {
            const canvas = document.querySelector("canvas");
            const ctx = canvas.getContext('2d');
            
            if (!result) return;
            
            ctx.clearRect(0, 0, canvas.width, canvas.height);
            
            if (result.boxes) {
                ctx.strokeStyle = '#0F0';
                ctx.lineWidth = 2;
                
                result.boxes.filter(box => box !== result.box).forEach(box => {
                    ctx.strokeRect(box[0], box[1], box[2] - box[0], box[3] - box[1]);
                });
            }
            
            if (result.box) {
                ctx.strokeStyle = '#00F';
                ctx.lineWidth = 4;
                ctx.strokeRect(result.box[0], result.box[1], 
                             result.box[2] - result.box[0], 
                             result.box[3] - result.box[1]);
            }
            
            if (result.codeResult && result.codeResult.code) {
                ctx.font = '18px Arial';
                ctx.fillStyle = '#FFF';
                ctx.textAlign = 'center';
                ctx.fillText(result.codeResult.code, canvas.width / 2, canvas.height - 10);
            }
        }
        
        // Горячие клавиши для отладки
        document.addEventListener('keydown', function(e) {
            // Ctrl+D для отладки
            if (e.ctrlKey && e.key === 'd') {
                e.preventDefault();
                const debugEl = document.getElementById('debugInfo');
                debugEl.style.display = debugEl.style.display === 'none' ? 'block' : 'none';
            }
            
            // Escape для закрытия сканера
            if (e.key === 'Escape' && document.getElementById('scannerModal').style.display === 'block') {
                closeScanner();
            }
            
            // Enter в поле ввода для поиска
            if (e.key === 'Enter' && document.activeElement.id === 'searchInput') {
                performSearch();
            }
        });
        
        // Обработка вставки из буфера обмена
        document.getElementById('searchInput').addEventListener('paste', function(e) {
            setTimeout(() => {
                performSearch();
            }, 100);
        });
        
        // Проверка поддержки камеры
        function checkCameraSupport() {
            if (!navigator.mediaDevices || !navigator.mediaDevices.getUserMedia) {
                alert('Ваш браузер не поддерживает доступ к камере. Пожалуйста, используйте Safari на iOS или Chrome на Android.');
                return false;
            }
            return true;
        }
    </script>
</body>
</html>