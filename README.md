<html lang="ru">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Сканирование штрихкодов на iOS</title>
    <!-- Библиотека для сканирования штрихкодов -->
    <script src="https://cdn.jsdelivr.net/npm/@ericblade/quagga2@1.7.5/dist/quagga.min.js"></script>
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
        
        /* Canvas для Quagga */
        #scannerCanvas {
            position: absolute;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            display: none;
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
            <p>Используем Quagga2 для точного сканирования</p>
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
                
                <!-- Canvas для Quagga -->
                <canvas id="scannerCanvas"></canvas>
                
                <!-- Видео элемент для отображения камеры -->
                <video id="cameraVideo" playsinline autoplay muted></video>
                
                <div class="scan-overlay">
                    <div class="scan-line"></div>
                </div>
                
                <div class="scanner-info">
                    Наведите камеру на штрихкод в рамке
                </div>
                
                <div class="status-message" id="scannerStatus"></div>
                <div class="scanned-badge" id="scannedBadge">✓ Найдено!</div>
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
        let lastScanTime = 0;
        let scanCooldown = 2000;
        let useBackCamera = true;
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
            },
            "1234567890128": {
                article: "TEST-001",
                name: "Тестовый товар 1",
                price: "100,00"
            },
            "9876543210128": {
                article: "TEST-002",
                name: "Тестовый товар 2",
                price: "200,00"
            }
        };
        
        // Инициализация при загрузке страницы
        document.addEventListener('DOMContentLoaded', function() {
            console.log('Устройство iOS:', isIOS);
            console.log('Quagga доступен:', typeof Quagga !== 'undefined');
            
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
        
        // Запуск сканера с Quagga
        async function startScanner() {
            try {
                console.log('Запуск сканера Quagga...');
                
                // Останавливаем предыдущий поток если есть
                await stopScanner();
                
                // Скрываем результаты поиска
                document.getElementById('resultsContainer').style.display = 'none';
                
                // Показываем модальное окно и лоадер
                document.getElementById('scannerModal').style.display = 'block';
                document.getElementById('scannerLoader').style.display = 'block';
                showStatus('Инициализация сканера...');
                
                // Настраиваем камеру
                const cameraId = await getCameraId();
                
                // Конфигурация Quagga для iOS
                const config = {
                    inputStream: {
                        name: "Live",
                        type: "LiveStream",
                        target: document.querySelector('#scannerCanvas'),
                        constraints: {
                            deviceId: cameraId ? { exact: cameraId } : undefined,
                            facingMode: useBackCamera ? "environment" : "user",
                            width: { min: 640, ideal: 1280, max: 1920 },
                            height: { min: 480, ideal: 720, max: 1080 },
                            aspectRatio: { min: 1, max: 2 }
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
                            "upc_reader",
                            "upc_e_reader",
                            "codabar_reader"
                        ],
                        multiple: false
                    },
                    locate: true,
                    frequency: 10,
                    numOfWorkers: navigator.hardwareConcurrency ? Math.min(2, navigator.hardwareConcurrency) : 1,
                    debug: {
                        drawBoundingBox: false,
                        showFrequency: false,
                        drawScanline: false,
                        showPattern: false
                    }
                };
                
                // Инициализируем Quagga
                Quagga.init(config, async function(err) {
                    if (err) {
                        console.error("Ошибка инициализации Quagga:", err);
                        showStatus('Ошибка сканера. Пробуем альтернативный метод...');
                        
                        // Пробуем альтернативный метод
                        setTimeout(() => {
                            initAlternativeScanner();
                        }, 1000);
                        return;
                    }
                    
                    console.log("Quagga успешно инициализирован");
                    
                    // Запускаем видео поток отдельно для отображения
                    await startVideoStream();
                    
                    // Настраиваем обработчики Quagga
                    Quagga.onProcessed(function(result) {
                        // Можно добавить визуализацию если нужно
                    });
                    
                    Quagga.onDetected(function(result) {
                        if (result && result.codeResult) {
                            const code = result.codeResult.code;
                            const format = result.codeResult.format;
                            console.log("Найден штрихкод:", code, "Формат:", format);
                            handleScannedCode(code);
                        }
                    });
                    
                    // Запускаем Quagga
                    Quagga.start();
                    
                    // Скрываем лоадер
                    document.getElementById('scannerLoader').style.display = 'none';
                    hideStatus();
                    
                    isScanning = true;
                    console.log('Сканирование начато');
                });
                
            } catch (error) {
                console.error('Ошибка запуска сканера:', error);
                showStatus('Ошибка: ' + getErrorMessage(error));
            }
        }
        
        // Получение ID камеры
        async function getCameraId() {
            try {
                const devices = await navigator.mediaDevices.enumerateDevices();
                const videoDevices = devices.filter(device => device.kind === 'videoinput');
                
                if (videoDevices.length === 0) {
                    console.log('Камеры не найдены');
                    return null;
                }
                
                // Ищем тыловую камеру
                if (useBackCamera) {
                    // На iOS environment камера обычно последняя
                    return videoDevices[videoDevices.length - 1].deviceId;
                } else {
                    // Фронтальная камера обычно первая
                    return videoDevices[0].deviceId;
                }
            } catch (error) {
                console.warn('Не удалось получить список камер:', error);
                return null;
            }
        }
        
        // Запуск видео потока для отображения
        async function startVideoStream() {
            try {
                const video = document.getElementById('cameraVideo');
                
                // Очищаем предыдущий поток
                if (videoStream) {
                    videoStream.getTracks().forEach(track => track.stop());
                }
                
                // Получаем доступ к камере
                const constraints = {
                    video: {
                        facingMode: useBackCamera ? "environment" : "user",
                        width: { ideal: 1280 },
                        height: { ideal: 720 }
                    },
                    audio: false
                };
                
                videoStream = await navigator.mediaDevices.getUserMedia(constraints);
                video.srcObject = videoStream;
                
                // Ждем загрузки видео
                await video.play();
                
                // Для iOS убираем трансформацию
                if (isIOS) {
                    video.style.transform = 'scaleX(1)';
                    video.style.webkitTransform = 'scaleX(1)';
                }
                
                console.log('Видео поток запущен');
                
            } catch (error) {
                console.warn('Не удалось запустить видео поток:', error);
            }
        }
        
        // Альтернативный сканер (если Quagga не работает)
        async function initAlternativeScanner() {
            try {
                console.log('Запуск альтернативного сканера...');
                showStatus('Запуск альтернативного сканера...');
                
                // Останавливаем Quagga если он был запущен
                try {
                    Quagga.stop();
                } catch (e) {}
                
                // Запускаем простой анализ через canvas
                await startVideoStream();
                
                const video = document.getElementById('cameraVideo');
                const canvas = document.createElement('canvas');
                const ctx = canvas.getContext('2d');
                
                // Функция анализа кадров
                function analyzeFrame() {
                    if (!isScanning) return;
                    
                    try {
                        // Проверяем готовность видео
                        if (video.readyState !== video.HAVE_ENOUGH_DATA) {
                            requestAnimationFrame(analyzeFrame);
                            return;
                        }
                        
                        // Устанавливаем размеры canvas
                        canvas.width = video.videoWidth;
                        canvas.height = video.videoHeight;
                        
                        // Рисуем кадр
                        ctx.drawImage(video, 0, 0, canvas.width, canvas.height);
                        
                        // Простой анализ изображения
                        // (Здесь должна быть логика распознавания штрихкодов)
                        // Для демонстрации просто продолжаем цикл
                        
                    } catch (error) {
                        console.warn('Ошибка анализа:', error);
                    }
                    
                    // Продолжаем цикл
                    requestAnimationFrame(analyzeFrame);
                }
                
                // Скрываем лоадер
                document.getElementById('scannerLoader').style.display = 'none';
                hideStatus();
                
                // Запускаем анализ
                analyzeFrame();
                
                isScanning = true;
                
            } catch (error) {
                console.error('Ошибка альтернативного сканера:', error);
                showStatus('Ошибка: ' + getErrorMessage(error));
            }
        }
        
        // Обработка найденного кода
        function handleScannedCode(code) {
            const now = Date.now();
            
            // Проверяем кд
            if (now - lastScanTime < scanCooldown) {
                console.log('Сканирование слишком часто, пропускаем');
                return;
            }
            
            lastScanTime = now;
            
            console.log('Обработка найденного кода:', code);
            
            // Визуальная обратная связь
            showScannedBadge(code);
            
            // Останавливаем сканирование через 1 секунду
            setTimeout(() => {
                stopScanner();
                
                // Заполняем поле ввода
                document.getElementById('searchInput').value = code;
                
                // Выполняем поиск
                setTimeout(() => performSearch(), 300);
            }, 1000);
        }
        
        // Переключение камеры
        async function switchCamera() {
            useBackCamera = !useBackCamera;
            showStatus('Переключаю камеру...');
            
            // Останавливаем текущий сканер
            await stopScanner();
            
            // Запускаем заново с новой камерой
            setTimeout(() => {
                startScanner();
            }, 500);
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
            
            // Останавливаем видео поток
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
            } else if (error.name === 'OverconstrainedError') {
                return 'Проблема с настройками камеры';
            } else if (error.message.includes('NotSupportedError')) {
                return 'Ваш браузер не поддерживает эту функцию';
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