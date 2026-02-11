<html lang="ru">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Сканирование штрихкодов на iOS</title>
    <!-- Библиотека QuaggaJS для сканирования штрихкодов -->
    <script src="https://cdn.jsdelivr.net/npm/quagga@0.12.1/dist/quagga.min.js"></script>
    <!-- Библиотека Instascan как fallback -->
    <script src="https://rawgit.com/schmich/instascan-builds/master/instascan.min.js"></script>
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
            display: flex;
            justify-content: center;
            align-items: center;
        }
        
        #scanner {
            width: 100%;
            height: 100%;
            object-fit: cover;
        }
        
        .video-wrapper {
            width: 100%;
            height: 100%;
            position: relative;
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
            z-index: 10;
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
            z-index: 10;
        }
        
        .modal-controls {
            padding: 20px;
            background: rgba(0, 0, 0, 0.8);
            display: flex;
            gap: 12px;
            z-index: 100;
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
            z-index: 100;
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
            z-index: 100;
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
            z-index: 100;
        }
        
        .camera-selector {
            position: absolute;
            top: 20px;
            left: 20px;
            z-index: 100;
        }
        
        .camera-select {
            background: rgba(0,0,0,0.7);
            color: white;
            border: 1px solid rgba(255,255,255,0.3);
            padding: 8px 12px;
            border-radius: 8px;
            font-size: 14px;
        }
        
        .ios-warning {
            display: none;
            position: absolute;
            top: 50%;
            left: 50%;
            transform: translate(-50%, -50%);
            background: rgba(0,0,0,0.9);
            color: white;
            padding: 20px;
            border-radius: 10px;
            text-align: center;
            z-index: 1000;
            max-width: 300px;
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
            z-index: 100;
        }
        
        @keyframes spin {
            100% { transform: translate(-50%, -50%) rotate(360deg); }
        }
        
        .fallback-message {
            display: none;
            position: absolute;
            top: 50%;
            left: 50%;
            transform: translate(-50%, -50%);
            background: rgba(0,0,0,0.8);
            color: white;
            padding: 20px;
            border-radius: 10px;
            text-align: center;
            z-index: 100;
            max-width: 300px;
        }
        
        .preview-image {
            position: absolute;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            object-fit: cover;
            display: none;
        }
    </style>
</head>
<body>
    <div class="container">
        <div class="header">
            <h1>📦 Сканирование товаров</h1>
            <p>Оптимизировано для iOS</p>
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
                
                <div class="video-wrapper">
                    <video id="scanner" playsinline autoplay muted></video>
                    <!-- Fallback изображение для предпросмотра -->
                    <canvas id="scannerCanvas" style="display:none;"></canvas>
                    <img id="previewImage" class="preview-image" alt="Предпросмотр">
                </div>
                
                <div class="scan-overlay">
                    <div class="scan-line"></div>
                </div>
                
                <div class="scanner-info">
                    Наведите камеру на штрихкод в рамке
                </div>
                
                <div class="ios-warning" id="iosWarning">
                    Для работы сканера на iOS:<br><br>
                    1. Разрешите доступ к камере<br>
                    2. Нажмите "Разрешить"<br>
                    3. Возможно, потребуется нажать на экран
                </div>
                
                <div class="fallback-message" id="fallbackMessage">
                    Используется режим фотосканирования.<br>
                    Сфотографируйте штрихкод и нажмите "Анализировать"
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
                <button class="modal-btn modal-btn-primary" id="takePhotoBtn">
                    📸 Сделать фото
                </button>
                <button class="modal-btn modal-btn-primary" id="toggleTorch" style="display:none;">
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
        let scanCooldown = 2000;
        let torchEnabled = false;
        let currentStream = null;
        let currentScanner = null; // 'quagga' или 'instascan'
        let fallbackMode = false;
        
        // Определяем iOS
        const isIOS = /iPad|iPhone|iPod/.test(navigator.userAgent) && !window.MSStream;
        const isSafari = /^((?!chrome|android).)*safari/i.test(navigator.userAgent);
        
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
            console.log('Браузер Safari:', isSafari);
            console.log('Quagga доступен:', typeof Quagga !== 'undefined');
            console.log('Instascan доступен:', typeof Instascan !== 'undefined');
            
            // Установка обработчиков событий
            document.getElementById('searchButton').addEventListener('click', performSearch);
            document.getElementById('scanButton').addEventListener('click', openScanner);
            document.getElementById('closeScanner').addEventListener('click', closeScanner);
            document.getElementById('takePhotoBtn').addEventListener('click', takePhotoForAnalysis);
            
            document.getElementById('searchInput').addEventListener('keypress', function(e) {
                if (e.key === 'Enter') performSearch();
            });
            
            // Для iOS показываем предупреждение
            if (isIOS) {
                console.log('Используется iOS, активируем специальный режим');
            }
            
            // Автофокус на поле ввода
            setTimeout(() => {
                document.getElementById('searchInput').focus();
            }, 500);
        });
        
        // Открытие сканера
        async function openScanner() {
            try {
                console.log('Открытие сканера...');
                
                // Показываем модальное окно и лоадер
                document.getElementById('scannerModal').style.display = 'block';
                document.getElementById('scannerLoader').style.display = 'block';
                
                // Сбрасываем состояние
                fallbackMode = false;
                document.getElementById('fallbackMessage').style.display = 'none';
                document.getElementById('previewImage').style.display = 'none';
                document.getElementById('takePhotoBtn').innerHTML = '📸 Сделать фото';
                document.getElementById('toggleTorch').style.display = 'none';
                
                // Проверяем поддержку
                if (!checkCameraSupport()) {
                    showScannerStatus('Камера не поддерживается браузером');
                    return;
                }
                
                // Для iOS используем Instascan как основной вариант
                if (isIOS && typeof Instascan !== 'undefined') {
                    await initInstascan();
                } else if (typeof Quagga !== 'undefined') {
                    await initQuagga();
                } else {
                    throw new Error('Библиотеки сканирования не загружены');
                }
                
                // Показываем инструкцию для iOS
                if (isIOS) {
                    setTimeout(() => {
                        document.getElementById('iosWarning').style.display = 'block';
                        setTimeout(() => {
                            document.getElementById('iosWarning').style.display = 'none';
                        }, 3000);
                    }, 1000);
                }
                
                document.getElementById('scannerLoader').style.display = 'none';
                
            } catch (error) {
                console.error('Ошибка инициализации сканера:', error);
                showScannerStatus('Ошибка: ' + error.message);
                
                // Переходим в fallback режим
                setTimeout(() => {
                    initFallbackMode();
                }, 1000);
            }
        }
        
        // Инициализация Instascan (лучше работает на iOS)
        async function initInstascan() {
            return new Promise((resolve, reject) => {
                try {
                    const scanner = new Instascan.Scanner({
                        video: document.getElementById('scanner'),
                        mirror: false,
                        scanPeriod: 1,
                        backgroundScan: false
                    });
                    
                    scanner.addListener('scan', function(content) {
                        console.log('Instascan найден штрихкод:', content);
                        handleScannedCode(content);
                    });
                    
                    // Получаем доступные камеры
                    Instascan.Camera.getCameras().then(function(cameras) {
                        if (cameras.length > 0) {
                            let selectedCamera = cameras[0];
                            const cameraType = document.getElementById('cameraSelect').value;
                            
                            // Выбираем камеру по типу
                            if (cameraType === 'environment') {
                                selectedCamera = cameras.find(c => c.name.toLowerCase().includes('back') || 
                                    c.name.toLowerCase().includes('rear') || 
                                    c.name.includes('2')) || cameras[0];
                            } else {
                                selectedCamera = cameras.find(c => c.name.toLowerCase().includes('front') || 
                                    c.name.includes('1')) || cameras[0];
                            }
                            
                            scanner.start(selectedCamera).then(() => {
                                console.log('Instascan запущен');
                                currentScanner = 'instascan';
                                currentStream = scanner.activeTrack;
                                isScanning = true;
                                
                                // Показываем кнопку фонарика если доступно
                                if (selectedCamera && selectedCamera.torch) {
                                    document.getElementById('toggleTorch').style.display = 'block';
                                    document.getElementById('toggleTorch').addEventListener('click', function() {
                                        scanner.torch = !scanner.torch;
                                        torchEnabled = scanner.torch;
                                        updateTorchIndicator();
                                    });
                                }
                                
                                resolve();
                            }).catch(err => {
                                console.error('Ошибка запуска Instascan:', err);
                                reject(err);
                            });
                        } else {
                            reject(new Error('Камеры не найдены'));
                        }
                    }).catch(err => {
                        console.error('Ошибка получения камер:', err);
                        reject(err);
                    });
                    
                } catch (error) {
                    reject(error);
                }
            });
        }
        
        // Инициализация QuaggaJS
        function initQuagga() {
            return new Promise((resolve, reject) => {
                const cameraId = document.getElementById('cameraSelect').value;
                
                // Упрощенная конфигурация для iOS
                const config = {
                    inputStream: {
                        name: "Live",
                        type: "LiveStream",
                        target: document.querySelector('#scanner'),
                        constraints: {
                            facingMode: cameraId,
                            width: { min: 320, ideal: 640 },
                            height: { min: 240, ideal: 480 }
                        }
                    },
                    decoder: {
                        readers: ["ean_reader", "code_128_reader", "code_39_reader"],
                        multiple: false
                    },
                    locate: false, // Отключаем для iOS
                    frequency: 5,
                    numOfWorkers: 1, // Минимум воркеров для iOS
                    debug: {
                        drawBoundingBox: false,
                        showFrequency: false,
                        drawScanline: false,
                        showPattern: false
                    }
                };
                
                Quagga.init(config, function(err) {
                    if (err) {
                        console.error("Ошибка инициализации Quagga:", err);
                        reject(err);
                        return;
                    }
                    
                    console.log("Quagga успешно инициализирован");
                    currentScanner = 'quagga';
                    
                    // Получаем поток видео
                    const video = document.getElementById('scanner');
                    if (video.srcObject) {
                        currentStream = video.srcObject;
                    }
                    
                    // Обработка обнаруженных штрихкодов
                    Quagga.onDetected(function(result) {
                        if (result && result.codeResult) {
                            const code = result.codeResult.code;
                            console.log("Quagga найден штрихкод:", code);
                            handleScannedCode(code);
                        }
                    });
                    
                    Quagga.start();
                    isScanning = true;
                    resolve();
                });
            });
        }
        
        // Fallback режим для iOS
        function initFallbackMode() {
            console.log('Активация fallback режима');
            fallbackMode = true;
            
            document.getElementById('scannerLoader').style.display = 'none';
            document.getElementById('fallbackMessage').style.display = 'block';
            
            // Показываем кнопку для фото
            document.getElementById('takePhotoBtn').innerHTML = '📸 Сделать фото';
            
            // Получаем доступ к камере для предпросмотра
            navigator.mediaDevices.getUserMedia({
                video: {
                    facingMode: document.getElementById('cameraSelect').value,
                    width: { ideal: 640 },
                    height: { ideal: 480 }
                },
                audio: false
            }).then(stream => {
                currentStream = stream;
                const video = document.getElementById('scanner');
                video.srcObject = stream;
                video.play();
                
                showScannerStatus('Готов к фотосканированию');
                
            }).catch(err => {
                console.error('Ошибка доступа к камере:', err);
                showScannerStatus('Ошибка камеры: ' + err.message);
            });
        }
        
        // Сделать фото для анализа
        function takePhotoForAnalysis() {
            if (!fallbackMode) {
                // Если не в fallback режиме, просто делаем фото
                captureAndAnalyzeFrame();
                return;
            }
            
            const video = document.getElementById('scanner');
            const canvas = document.getElementById('scannerCanvas');
            const ctx = canvas.getContext('2d');
            const previewImg = document.getElementById('previewImage');
            
            // Устанавливаем размеры canvas
            canvas.width = video.videoWidth;
            canvas.height = video.videoHeight;
            
            // Делаем снимок
            ctx.drawImage(video, 0, 0, canvas.width, canvas.height);
            
            // Показываем превью
            previewImg.src = canvas.toDataURL('image/jpeg');
            previewImg.style.display = 'block';
            video.style.display = 'none';
            
            // Меняем кнопку
            document.getElementById('takePhotoBtn').innerHTML = '🔍 Анализировать';
            
            // Меняем обработчик
            document.getElementById('takePhotoBtn').onclick = function() {
                analyzeCapturedImage(canvas);
            };
            
            showScannerStatus('Фото сделано. Нажмите "Анализировать"');
        }
        
        // Анализ захваченного изображения
        function analyzeCapturedImage(canvas) {
            showScannerStatus('Анализ изображения...');
            
            // Создаем новый canvas для обработки
            const tempCanvas = document.createElement('canvas');
            const tempCtx = tempCanvas.getContext('2d');
            
            // Уменьшаем изображение для быстрой обработки
            const scale = 0.5;
            tempCanvas.width = canvas.width * scale;
            tempCanvas.height = canvas.height * scale;
            
            tempCtx.drawImage(canvas, 0, 0, tempCanvas.width, tempCanvas.height);
            
            // Получаем данные изображения
            const imageData = tempCtx.getImageData(0, 0, tempCanvas.width, tempCanvas.height);
            
            // Простая попытка найти штрихкод (это упрощенная реализация)
            // В реальном приложении нужно использовать серверную обработку
            // или более продвинутую библиотеку
            
            // Показываем заглушку
            setTimeout(() => {
                showScannerStatus('Штрихкод не найден. Попробуйте еще раз');
                
                // Возвращаемся к видео
                document.getElementById('previewImage').style.display = 'none';
                document.getElementById('scanner').style.display = 'block';
                document.getElementById('takePhotoBtn').innerHTML = '📸 Сделать фото';
                document.getElementById('takePhotoBtn').onclick = takePhotoForAnalysis;
            }, 1500);
        }
        
        // Захват и анализ кадра (для обычного режима)
        function captureAndAnalyzeFrame() {
            const video = document.getElementById('scanner');
            const canvas = document.createElement('canvas');
            const ctx = canvas.getContext('2d');
            
            canvas.width = video.videoWidth;
            canvas.height = video.videoHeight;
            ctx.drawImage(video, 0, 0, canvas.width, canvas.height);
            
            // Используем Quagga для анализа статичного изображения
            if (typeof Quagga !== 'undefined') {
                Quagga.decodeSingle({
                    src: canvas.toDataURL('image/jpeg'),
                    numOfWorkers: 0,
                    inputStream: {
                        size: 800
                    },
                    decoder: {
                        readers: ["ean_reader", "code_128_reader"]
                    }
                }, function(result) {
                    if (result && result.codeResult) {
                        console.log("Статичный анализ найден:", result.codeResult.code);
                        handleScannedCode(result.codeResult.code);
                    } else {
                        showScannerStatus('Штрихкод не найден. Попробуйте еще раз');
                    }
                });
            }
        }
        
        // Обработка найденного штрихкода
        function handleScannedCode(code) {
            const now = Date.now();
            
            if (lastScannedCode && now - lastScannedCode.timestamp < scanCooldown) {
                console.log('Дубликат, пропускаем');
                return;
            }
            
            lastScannedCode = {
                code: code,
                timestamp: now
            };
            
            console.log('Обработка штрихкода:', code);
            
            // Визуальная обратная связь
            showScannedBadge(code);
            
            // Останавливаем сканирование
            stopAllScanners();
            
            // Через 1.5 секунды закрываем сканер
            setTimeout(() => {
                closeScanner();
                document.getElementById('searchInput').value = code;
                setTimeout(() => performSearch(), 300);
            }, 1500);
        }
        
        // Остановка всех сканеров
        function stopAllScanners() {
            if (currentScanner === 'quagga' && isScanning) {
                Quagga.stop();
            }
            
            if (currentScanner === 'instascan' && typeof Instascan !== 'undefined') {
                // Instascan не имеет явного метода stop, останавливаем поток
                if (currentStream) {
                    currentStream.stop();
                }
            }
            
            isScanning = false;
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
        
        // Закрытие сканера
        function closeScanner() {
            console.log('Закрытие сканера...');
            
            stopAllScanners();
            
            if (currentStream) {
                currentStream.getTracks().forEach(track => track.stop());
                currentStream = null;
            }
            
            document.getElementById('scannerModal').style.display = 'none';
            hideScannerStatus();
            document.getElementById('scannedBadge').style.display = 'none';
            document.getElementById('flashIndicator').style.display = 'none';
            document.getElementById('previewImage').style.display = 'none';
            document.getElementById('scanner').style.display = 'block';
            
            torchEnabled = false;
            currentScanner = null;
        }
        
        // Обновление индикатора фонарика
        function updateTorchIndicator() {
            const indicator = document.getElementById('flashIndicator');
            indicator.style.display = torchEnabled ? 'flex' : 'none';
        }
        
        // Визуальная обратная связь
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
        
        // Проверка поддержки камеры
        function checkCameraSupport() {
            if (!navigator.mediaDevices || !navigator.mediaDevices.getUserMedia) {
                alert('Ваш браузер не поддерживает доступ к камере. Пожалуйста, используйте Safari на iOS.');
                return false;
            }
            return true;
        }
        
        // Горячие клавиши
        document.addEventListener('keydown', function(e) {
            if (e.key === 'Escape' && document.getElementById('scannerModal').style.display === 'block') {
                closeScanner();
            }
            if (e.key === 'Enter' && document.activeElement.id === 'searchInput') {
                performSearch();
            }
        });
        
        // Обработка вставки из буфера
        document.getElementById('searchInput').addEventListener('paste', function(e) {
            setTimeout(() => {
                performSearch();
            }, 100);
        });
    </script>
</body>
</html>