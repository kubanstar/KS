<html lang="ru">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Сканирование штрихкодов на iOS</title>
    <!-- Библиотека QuaggaJS с исправлениями для iOS -->
    <script src="https://cdn.jsdelivr.net/npm/quagga@0.12.1/dist/quagga.min.js"></script>
    <style>
        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
            -webkit-tap-highlight-color: transparent;
        }
        
        body {
            font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, sans-serif;
            background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
            min-height: 100vh;
            padding: 20px;
            overflow-x: hidden;
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
            transition: all 0.3s;
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
        
        /* Модальное окно сканирования */
        .modal {
            display: none;
            position: fixed;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            background: black;
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
            background: black;
        }
        
        #scanner {
            width: 100%;
            height: 100%;
            object-fit: cover;
            background: black;
            display: block;
        }
        
        /* Оверлей для улучшения видимости */
        .scan-overlay {
            position: absolute;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            pointer-events: none;
            z-index: 2;
        }
        
        .scan-frame {
            position: absolute;
            top: 50%;
            left: 50%;
            transform: translate(-50%, -50%);
            width: 80%;
            max-width: 400px;
            height: 200px;
            border: 4px solid rgba(52, 199, 89, 0.8);
            border-radius: 15px;
            box-shadow: 0 0 0 1000px rgba(0, 0, 0, 0.5);
            overflow: hidden;
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
        
        /* Превью область - всегда видно */
        .preview-area {
            position: absolute;
            top: 20%;
            left: 10%;
            width: 80%;
            height: 60%;
            border: 2px solid rgba(255,255,255,0.3);
            border-radius: 10px;
            z-index: 1;
            pointer-events: none;
        }
        
        .scanner-info {
            position: absolute;
            bottom: 120px;
            left: 0;
            width: 100%;
            text-align: center;
            color: white;
            font-size: 16px;
            padding: 0 20px;
            text-shadow: 0 1px 3px rgba(0,0,0,0.8);
            z-index: 3;
        }
        
        .modal-controls {
            position: absolute;
            bottom: 0;
            left: 0;
            width: 100%;
            padding: 20px;
            background: rgba(0, 0, 0, 0.7);
            display: flex;
            gap: 12px;
            z-index: 10;
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
        }
        
        .modal-btn-primary {
            background: rgba(255, 255, 255, 0.2);
            color: white;
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
            z-index: 100;
        }
        
        @keyframes spin {
            100% { transform: translate(-50%, -50%) rotate(360deg); }
        }
        
        .ios-hint {
            position: absolute;
            top: 40px;
            left: 0;
            width: 100%;
            text-align: center;
            color: white;
            font-size: 14px;
            padding: 10px;
            background: rgba(0,0,0,0.7);
            z-index: 100;
        }
        
        .preview-grid {
            position: absolute;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            background: 
                linear-gradient(90deg, transparent 49%, rgba(255,255,255,0.1) 50%, transparent 51%),
                linear-gradient(transparent 49%, rgba(255,255,255,0.1) 50%, transparent 51%);
            background-size: 50px 50px;
            opacity: 0.3;
            pointer-events: none;
            z-index: 1;
        }
        
        .video-placeholder {
            position: absolute;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            background: linear-gradient(45deg, #222, #000);
            display: flex;
            align-items: center;
            justify-content: center;
            color: white;
            font-size: 18px;
            text-align: center;
            padding: 20px;
            z-index: 0;
        }
    </style>
</head>
<body>
    <div class="container">
        <div class="header">
            <h1>📦 Сканирование штрихкодов</h1>
            <p>Оптимизировано для мобильных устройств</p>
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
                    Найти
                </button>
                <button class="btn btn-secondary" id="scanButton">
                    Сканировать
                </button>
            </div>
        </div>
    </div>
    
    <!-- Модальное окно сканирования -->
    <div class="modal" id="scannerModal">
        <div class="modal-content">
            <div class="scanner-container">
                <!-- Placeholder пока не загрузилось видео -->
                <div class="video-placeholder" id="videoPlaceholder">
                    <div>
                        <div style="font-size: 48px; margin-bottom: 20px;">📷</div>
                        <div>Инициализация камеры...</div>
                    </div>
                </div>
                
                <!-- Элемент видео -->
                <video id="scanner" playsinline></video>
                
                <!-- Оверлей и элементы интерфейса -->
                <div class="preview-grid"></div>
                <div class="preview-area"></div>
                
                <div class="scan-overlay">
                    <div class="scan-frame">
                        <div class="scan-line"></div>
                    </div>
                </div>
                
                <div class="ios-hint" id="iosHint" style="display: none;">
                    Для iOS: Нажмите на экран для активации камеры
                </div>
                
                <div class="scanner-info">
                    Наведите камеру на штрихкод
                </div>
                
                <div class="status-message" id="scannerStatus"></div>
                <div class="scanned-badge" id="scannedBadge">✓ Найдено!</div>
                <div class="loader" id="scannerLoader"></div>
            </div>
            
            <div class="modal-controls">
                <button class="modal-btn modal-btn-primary" id="closeScanner">
                    ✕ Закрыть
                </button>
            </div>
        </div>
    </div>

    <script>
        // Определяем iOS
        const isIOS = /iPad|iPhone|iPod/.test(navigator.userAgent) && !window.MSStream;
        const isSafari = /^((?!chrome|android).)*safari/i.test(navigator.userAgent);
        
        // Глобальные переменные
        let isScanning = false;
        let lastScannedCode = '';
        let currentStream = null;
        let scanTimeout = null;
        
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
            }
        };
        
        document.addEventListener('DOMContentLoaded', function() {
            console.log('iOS:', isIOS, 'Safari:', isSafari);
            
            document.getElementById('scanButton').addEventListener('click', openScanner);
            document.getElementById('closeScanner').addEventListener('click', closeScanner);
            document.getElementById('searchButton').addEventListener('click', performSearch);
            
            document.getElementById('searchInput').addEventListener('keypress', function(e) {
                if (e.key === 'Enter') performSearch();
            });
            
            // Для iOS добавляем обработчик тапа по экрану
            if (isIOS) {
                document.getElementById('scannerModal').addEventListener('click', function() {
                    const video = document.getElementById('scanner');
                    if (video && video.play) {
                        video.play().catch(e => console.log('Ошибка play:', e));
                    }
                });
            }
        });
        
        // Открытие сканера
        async function openScanner() {
            console.log('Открытие сканера...');
            
            // Показываем модальное окно
            const modal = document.getElementById('scannerModal');
            modal.style.display = 'block';
            
            // Показываем лоадер
            document.getElementById('scannerLoader').style.display = 'block';
            document.getElementById('videoPlaceholder').style.display = 'flex';
            document.getElementById('scanner').style.display = 'none';
            
            // Для iOS показываем подсказку
            if (isIOS) {
                document.getElementById('iosHint').style.display = 'block';
                setTimeout(() => {
                    document.getElementById('iosHint').style.display = 'none';
                }, 3000);
            }
            
            try {
                // Получаем доступ к камере
                await startCamera();
                
                // Инициализируем Quagga
                await initQuagga();
                
                // Скрываем лоадер
                document.getElementById('scannerLoader').style.display = 'none';
                document.getElementById('videoPlaceholder').style.display = 'none';
                
                console.log('Сканер готов к работе');
                
            } catch (error) {
                console.error('Ошибка инициализации:', error);
                showScannerStatus('Ошибка: ' + error.message);
                
                // Пробуем альтернативный способ
                setTimeout(() => {
                    initSimpleScanner();
                }, 1000);
            }
        }
        
        // Запуск камеры с гарантированным показом видео
        async function startCamera() {
            return new Promise(async (resolve, reject) => {
                try {
                    const video = document.getElementById('scanner');
                    
                    // Очищаем предыдущий поток
                    if (currentStream) {
                        currentStream.getTracks().forEach(track => track.stop());
                    }
                    
                    // Получаем доступ к камере
                    const constraints = {
                        video: {
                            facingMode: 'environment',
                            width: { ideal: 1280 },
                            height: { ideal: 720 },
                            frameRate: { ideal: 30 }
                        },
                        audio: false
                    };
                    
                    // Для iOS используем более простые настройки
                    if (isIOS) {
                        constraints.video = {
                            facingMode: 'environment',
                            width: { min: 640, ideal: 1280 },
                            height: { min: 480, ideal: 720 }
                        };
                    }
                    
                    const stream = await navigator.mediaDevices.getUserMedia(constraints);
                    currentStream = stream;
                    
                    // Настраиваем видео элемент
                    video.srcObject = stream;
                    video.style.display = 'block';
                    
                    // Для iOS обязательно ждем события loadedmetadata
                    video.onloadedmetadata = function() {
                        console.log('Видео загружено:', video.videoWidth, 'x', video.videoHeight);
                        
                        // Пытаемся воспроизвести видео
                        video.play().then(() => {
                            console.log('Видео воспроизводится');
                            resolve(stream);
                        }).catch(e => {
                            console.warn('Ошибка воспроизведения:', e);
                            // Все равно продолжаем, иногда видео работает без play()
                            resolve(stream);
                        });
                    };
                    
                    // Таймаут на случай если событие не сработает
                    setTimeout(() => {
                        if (video.readyState >= 2) {
                            video.play().catch(e => console.log('Play catch:', e));
                            resolve(stream);
                        }
                    }, 1000);
                    
                } catch (error) {
                    reject(error);
                }
            });
        }
        
        // Инициализация Quagga с упрощенной конфигурацией
        function initQuagga() {
            return new Promise((resolve, reject) => {
                try {
                    const config = {
                        inputStream: {
                            name: "Live",
                            type: "LiveStream",
                            target: document.querySelector('#scanner'),
                            constraints: {
                                facingMode: "environment",
                                width: 640,
                                height: 480
                            },
                            area: { // Область сканирования
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
                                "code_39_reader"
                            ],
                            multiple: false
                        },
                        locate: true,
                        frequency: 10,
                        numOfWorkers: 1, // Минимум воркеров для iOS
                        debug: {
                            drawBoundingBox: true,
                            showFrequency: false,
                            drawScanline: true,
                            showPattern: true
                        }
                    };
                    
                    Quagga.init(config, function(err) {
                        if (err) {
                            console.log("Quagga init error:", err);
                            reject(err);
                            return;
                        }
                        
                        console.log("Quagga initialized");
                        
                        // Обработчик найденных кодов
                        Quagga.onDetected(function(result) {
                            if (result && result.codeResult) {
                                const code = result.codeResult.code;
                                console.log("Найден штрихкод:", code);
                                handleScannedCode(code);
                            }
                        });
                        
                        // Запускаем сканирование
                        Quagga.start();
                        isScanning = true;
                        
                        // Периодически перерисовываем для видимости
                        startVisualUpdates();
                        
                        resolve();
                    });
                    
                } catch (error) {
                    reject(error);
                }
            });
        }
        
        // Альтернативный простой сканер
        function initSimpleScanner() {
            console.log('Запуск простого сканера...');
            
            const video = document.getElementById('scanner');
            const canvas = document.createElement('canvas');
            const ctx = canvas.getContext('2d');
            
            canvas.width = 640;
            canvas.height = 480;
            
            // Периодический анализ кадров
            function analyzeFrame() {
                if (!isScanning) return;
                
                try {
                    if (video.videoWidth > 0 && video.videoHeight > 0) {
                        canvas.width = video.videoWidth;
                        canvas.height = video.videoHeight;
                        ctx.drawImage(video, 0, 0, canvas.width, canvas.height);
                        
                        // Пытаемся использовать Quagga для статичного анализа
                        Quagga.decodeSingle({
                            src: canvas.toDataURL('image/jpeg'),
                            numOfWorkers: 0,
                            decoder: {
                                readers: ["ean_reader", "code_128_reader"]
                            }
                        }, function(result) {
                            if (result && result.codeResult) {
                                console.log("Простой сканер найден:", result.codeResult.code);
                                handleScannedCode(result.codeResult.code);
                            }
                        });
                    }
                } catch (e) {
                    console.warn('Ошибка анализа:', e);
                }
                
                // Повторяем через 500мс
                setTimeout(analyzeFrame, 500);
            }
            
            // Запускаем анализ
            isScanning = true;
            analyzeFrame();
            
            // Запускаем визуальные обновления
            startVisualUpdates();
        }
        
        // Визуальные обновления для показа активности
        function startVisualUpdates() {
            let counter = 0;
            const statusEl = document.getElementById('scannerStatus');
            
            function update() {
                if (!isScanning) return;
                
                counter++;
                if (counter % 20 === 0) {
                    statusEl.textContent = 'Сканирование... ' + (counter / 10);
                    statusEl.style.display = 'block';
                    setTimeout(() => {
                        if (isScanning) statusEl.style.display = 'none';
                    }, 1000);
                }
                
                // Периодически пытаемся перезапустить видео если оно не показывает
                if (counter % 50 === 0) {
                    const video = document.getElementById('scanner');
                    if (video && video.paused) {
                        video.play().catch(e => console.log('Автоплей:', e));
                    }
                }
                
                requestAnimationFrame(update);
            }
            
            update();
        }
        
        // Обработка найденного штрихкода
        function handleScannedCode(code) {
            const now = Date.now();
            
            // Защита от дублирования
            if (lastScannedCode && now - lastScannedCode.timestamp < 2000) {
                return;
            }
            
            lastScannedCode = {
                code: code,
                timestamp: now
            };
            
            console.log('Обработка штрихкода:', code);
            
            // Показываем уведомление
            showScannedBadge(code);
            
            // Останавливаем сканирование
            stopScanner();
            
            // Через секунду закрываем
            setTimeout(() => {
                closeScanner();
                document.getElementById('searchInput').value = code;
                setTimeout(() => {
                    alert('Найден штрихкод: ' + code);
                }, 300);
            }, 1000);
        }
        
        // Остановка сканера
        function stopScanner() {
            if (isScanning) {
                isScanning = false;
                try {
                    Quagga.stop();
                } catch (e) {
                    console.log('Quagga уже остановлен');
                }
            }
            
            // Очищаем таймауты
            if (scanTimeout) {
                clearTimeout(scanTimeout);
            }
        }
        
        // Закрытие сканера
        function closeScanner() {
            console.log('Закрытие сканера');
            
            stopScanner();
            
            // Останавливаем поток камеры
            if (currentStream) {
                currentStream.getTracks().forEach(track => {
                    track.stop();
                    track.enabled = false;
                });
                currentStream = null;
            }
            
            // Очищаем видео
            const video = document.getElementById('scanner');
            video.srcObject = null;
            video.style.display = 'none';
            
            // Скрываем модальное окно
            document.getElementById('scannerModal').style.display = 'none';
            document.getElementById('videoPlaceholder').style.display = 'flex';
            document.getElementById('scannerStatus').style.display = 'none';
            document.getElementById('scannedBadge').style.display = 'none';
            document.getElementById('scannerLoader').style.display = 'none';
        }
        
        // Показать бейдж сканирования
        function showScannedBadge(code) {
            const badge = document.getElementById('scannedBadge');
            badge.textContent = `✓ ${code}`;
            badge.style.display = 'block';
        }
        
        // Показать статус
        function showScannerStatus(message) {
            const status = document.getElementById('scannerStatus');
            status.textContent = message;
            status.style.display = 'block';
        }
        
        // Поиск товара
        function performSearch() {
            const code = document.getElementById('searchInput').value.trim();
            if (!code) {
                alert('Введите штрихкод');
                return;
            }
            
            const product = productsData[code];
            if (product) {
                alert(`Найден товар:\nАртикул: ${product.article}\nНазвание: ${product.name}\nЦена: ${product.price} руб.`);
            } else {
                alert('Товар с таким штрихкодом не найден');
            }
        }
        
        // Автозапуск видео при клике (для iOS)
        document.addEventListener('click', function(e) {
            if (document.getElementById('scannerModal').style.display === 'block') {
                const video = document.getElementById('scanner');
                if (video && video.paused) {
                    video.play().catch(e => console.log('Клик-плей:', e));
                }
            }
        });
    </script>
</body>
</html>