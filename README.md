<html lang="ru">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Сканирование штрихкодов</title>
    <style>
        body {
            font-family: -apple-system, BlinkMacSystemFont, sans-serif;
            margin: 0;
            padding: 20px;
            background: #f2f2f7;
        }
        
        .container {
            max-width: 800px;
            margin: 0 auto;
        }
        
        .header {
            text-align: center;
            margin-bottom: 30px;
        }
        
        .header h1 {
            color: #1c1c1e;
            margin: 0 0 10px 0;
        }
        
        .header p {
            color: #8e8e93;
            margin: 0;
        }
        
        .search-box {
            background: white;
            padding: 25px;
            border-radius: 16px;
            box-shadow: 0 4px 20px rgba(0,0,0,0.1);
            margin-bottom: 20px;
        }
        
        .search-input {
            width: 100%;
            padding: 16px;
            font-size: 17px;
            border: 2px solid #e5e5ea;
            border-radius: 12px;
            margin-bottom: 20px;
            box-sizing: border-box;
        }
        
        .buttons {
            display: flex;
            gap: 12px;
        }
        
        .button {
            flex: 1;
            padding: 18px;
            font-size: 17px;
            font-weight: 600;
            border: none;
            border-radius: 12px;
            cursor: pointer;
            text-align: center;
            -webkit-tap-highlight-color: transparent;
            transition: transform 0.1s, opacity 0.1s;
        }
        
        .button:active {
            transform: scale(0.98);
            opacity: 0.8;
        }
        
        .button.search {
            background: #34c759;
            color: white;
        }
        
        .button.scan {
            background: #007aff;
            color: white;
        }
        
        .results {
            background: white;
            padding: 20px;
            border-radius: 16px;
            box-shadow: 0 4px 20px rgba(0,0,0,0.1);
            display: none;
            margin-top: 20px;
        }
        
        .results-title {
            font-size: 20px;
            font-weight: 600;
            color: #1c1c1e;
            margin-bottom: 15px;
            padding-bottom: 10px;
            border-bottom: 1px solid #e5e5ea;
        }
        
        .product-card {
            padding: 16px;
            border-bottom: 1px solid #e5e5ea;
        }
        
        .product-card:last-child {
            border-bottom: none;
        }
        
        .product-article {
            font-weight: 600;
            color: #000;
            font-size: 16px;
        }
        
        .product-name {
            color: #1c1c1e;
            font-size: 15px;
            margin: 8px 0;
            line-height: 1.4;
        }
        
        .product-price {
            color: #ff3b30;
            font-weight: 600;
            font-size: 18px;
        }
        
        .no-results {
            text-align: center;
            padding: 40px 20px;
            color: #8e8e93;
        }
        
        /* Модальное окно камеры */
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
            overflow: hidden;
        }
        
        #cameraVideo {
            width: 100%;
            height: 100%;
            object-fit: cover;
            display: block;
        }
        
        .scan-overlay {
            position: absolute;
            top: 50%;
            left: 50%;
            transform: translate(-50%, -50%);
            width: 80%;
            max-width: 400px;
            height: 180px;
            border: 3px solid rgba(52, 199, 89, 0.8);
            border-radius: 12px;
            pointer-events: none;
            box-shadow: 0 0 0 1000px rgba(0, 0, 0, 0.5);
        }
        
        .scan-line {
            position: absolute;
            top: 0;
            left: 0;
            width: 100%;
            height: 3px;
            background: #34c759;
            box-shadow: 0 0 10px rgba(52, 199, 89, 0.8);
            animation: scan 2s ease-in-out infinite;
        }
        
        @keyframes scan {
            0% { top: 0; }
            50% { top: 100%; }
            100% { top: 0; }
        }
        
        .scan-hint {
            position: absolute;
            top: calc(50% + 110px);
            left: 0;
            width: 100%;
            text-align: center;
            color: white;
            font-size: 14px;
            text-shadow: 0 1px 3px rgba(0,0,0,0.5);
            padding: 0 20px;
            box-sizing: border-box;
        }
        
        .modal-controls {
            position: absolute;
            bottom: 30px;
            left: 0;
            width: 100%;
            padding: 0 20px;
            box-sizing: border-box;
        }
        
        .modal-buttons {
            display: flex;
            gap: 12px;
            max-width: 400px;
            margin: 0 auto;
        }
        
        .modal-button {
            flex: 1;
            padding: 16px;
            background: rgba(255,255,255,0.15);
            backdrop-filter: blur(20px);
            border: 1px solid rgba(255,255,255,0.2);
            border-radius: 12px;
            color: white;
            font-size: 16px;
            font-weight: 600;
            cursor: pointer;
            -webkit-tap-highlight-color: transparent;
        }
        
        .modal-button:active {
            background: rgba(255,255,255,0.25);
        }
        
        .modal-button.close {
            background: rgba(255,59,48,0.8);
        }
        
        .modal-button.close:active {
            background: rgba(255,59,48,0.9);
        }
        
        .error-message {
            position: absolute;
            top: 50%;
            left: 20px;
            right: 20px;
            transform: translateY(-50%);
            background: rgba(255,59,48,0.95);
            color: white;
            padding: 20px;
            border-radius: 12px;
            text-align: center;
            display: none;
            z-index: 1001;
        }
        
        .debug-info {
            position: absolute;
            top: 20px;
            left: 20px;
            background: rgba(0,0,0,0.7);
            color: white;
            padding: 10px;
            border-radius: 8px;
            font-size: 12px;
            display: none;
        }
        
        .status-message {
            position: absolute;
            top: 20px;
            left: 0;
            width: 100%;
            text-align: center;
            color: white;
            font-size: 16px;
            font-weight: 500;
            text-shadow: 0 1px 3px rgba(0,0,0,0.5);
            padding: 10px 20px;
            box-sizing: border-box;
            display: none;
        }
        
        .success-overlay {
            position: absolute;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            background: rgba(52, 199, 89, 0.3);
            display: none;
            justify-content: center;
            align-items: center;
            z-index: 100;
        }
        
        .success-icon {
            font-size: 80px;
            color: white;
            text-shadow: 0 0 20px rgba(0,0,0,0.5);
        }
    </style>
</head>
<body>
    <div class="container">
        <div class="header">
            <h1>📦 Сканирование товаров</h1>
            <p>Наведите камеру на штрихкод EAN-13 или Code-128</p>
        </div>
        
        <div class="search-box">
            <input type="text" 
                   class="search-input" 
                   id="searchInput" 
                   placeholder="Штрихкод или артикул..."
                   autocomplete="off"
                   autocapitalize="none"
                   autocorrect="off">
            
            <div class="buttons">
                <button class="button search" id="searchButton">
                    🔍 Найти
                </button>
                <button class="button scan" id="scanButton">
                    📷 Сканировать
                </button>
            </div>
        </div>
        
        <div class="results" id="resultsContainer">
            <div class="results-title" id="resultsTitle">Результаты поиска</div>
            <div id="productsList"></div>
        </div>
    </div>
    
    <!-- Модальное окно камеры -->
    <div class="modal" id="cameraModal">
        <div class="modal-content">
            <video id="cameraVideo" playsinline autoplay></video>
            
            <div class="scan-overlay">
                <div class="scan-line"></div>
            </div>
            
            <div class="scan-hint">
                Наведите на штрихкод в рамке
            </div>
            
            <div class="status-message" id="statusMessage">
                Сканирование...
            </div>
            
            <div class="success-overlay" id="successOverlay">
                <div class="success-icon">✓</div>
            </div>
            
            <div class="debug-info" id="debugInfo"></div>
            
            <div class="modal-controls">
                <div class="modal-buttons">
                    <button class="modal-button" id="toggleTorch">
                        💡 Фонарик
                    </button>
                    <button class="modal-button close" id="closeCameraModal">
                        ✕ Закрыть
                    </button>
                </div>
            </div>
            
            <div class="error-message" id="cameraError"></div>
        </div>
    </div>

    <script>
        // Глобальные переменные
        let stream = null;
        let barcodeDetector = null;
        let scanInterval = null;
        let isIOS = /iPad|iPhone|iPod/.test(navigator.userAgent) && !window.MSStream;
        let lastScanTime = 0;
        let scanCooldown = 2000; // 2 секунды между сканированиями
        let torchEnabled = false;
        
        // Тестовые данные
        const productsData = `6970787612595;KS-8001;Набор для творчества "ЧАСТИЧНАЯ ВЫКЛАДКА СТРАЗАМИ" 10*15 в пакете;70,00;70,00;7;;10;2;0,035;;0,050;0,010;;Cb010003474_1;;;200;Cb010003474_1;`;
        
        // Инициализация при загрузке
        document.addEventListener('DOMContentLoaded', async function() {
            console.log('Устройство:', isIOS ? 'iOS' : 'Android/Другое');
            
            // Инициализация BarcodeDetector с конкретными форматами
            await initBarcodeDetector();
            
            // Обработчики событий
            document.getElementById('searchButton').addEventListener('click', performSearch);
            document.getElementById('scanButton').addEventListener('click', openCamera);
            document.getElementById('closeCameraModal').addEventListener('click', closeCamera);
            document.getElementById('toggleTorch').addEventListener('click', toggleTorch);
            
            document.getElementById('searchInput').addEventListener('keypress', function(e) {
                if (e.key === 'Enter') performSearch();
            });
            
            // Автофокус на поле ввода
            setTimeout(() => {
                document.getElementById('searchInput').focus();
            }, 500);
        });
        
        // Инициализация BarcodeDetector
        async function initBarcodeDetector() {
            if (!('BarcodeDetector' in window)) {
                console.warn('BarcodeDetector API не поддерживается');
                showMessage('Ваш браузер не поддерживает прямое сканирование');
                return;
            }
            
            try {
                // Получаем поддерживаемые форматы
                const supportedFormats = await BarcodeDetector.getSupportedFormats();
                console.log('Поддерживаемые форматы штрихкодов:', supportedFormats);
                
                // Создаем детектор с наиболее распространенными форматами
                const formats = [
                    'ean_13',   // EAN-13 (самый распространенный)
                    'ean_8',    // EAN-8
                    'upc_a',    // UPC-A
                    'upc_e',    // UPC-E
                    'code_128', // Code 128
                    'code_39',  // Code 39
                    'codabar'   // Codabar
                ].filter(format => supportedFormats.includes(format));
                
                if (formats.length === 0) {
                    console.warn('Нет поддержки нужных форматов штрихкодов');
                    showMessage('Нет поддержки форматов штрихкодов');
                    return;
                }
                
                barcodeDetector = new BarcodeDetector({ formats });
                console.log('BarcodeDetector инициализирован с форматами:', formats);
                
            } catch (error) {
                console.error('Ошибка инициализации BarcodeDetector:', error);
                showMessage('Ошибка инициализации сканера');
            }
        }
        
        // Открытие камеры
        async function openCamera() {
            try {
                console.log('Открытие камеры...');
                
                // Скрываем сообщение об ошибке
                hideError();
                
                // Показываем статус
                showStatus('Подготовка камеры...');
                
                // Останавливаем предыдущий поток
                if (stream) {
                    stream.getTracks().forEach(track => track.stop());
                    stream = null;
                }
                
                // Определяем параметры камеры
                const constraints = {
                    video: {
                        facingMode: { exact: 'environment' },
                        width: { ideal: 1280 },
                        height: { ideal: 720 },
                        frameRate: { ideal: 24, max: 30 }
                    },
                    audio: false
                };
                
                // Пробуем получить доступ к камере
                try {
                    stream = await navigator.mediaDevices.getUserMedia(constraints);
                    console.log('Камера подключена');
                } catch (err) {
                    console.log('Пробуем альтернативные настройки:', err);
                    
                    // Пробуем без exact, чтобы получить любую камеру
                    stream = await navigator.mediaDevices.getUserMedia({
                        video: {
                            facingMode: 'environment',
                            width: { min: 640, ideal: 1280, max: 1920 },
                            height: { min: 480, ideal: 720, max: 1080 }
                        },
                        audio: false
                    });
                }
                
                const video = document.getElementById('cameraVideo');
                video.srcObject = stream;
                
                // Ждем готовности видео
                await new Promise((resolve) => {
                    video.onloadedmetadata = () => {
                        video.play()
                            .then(() => {
                                console.log('Видео запущено');
                                
                                // Настройки для iOS
                                if (isIOS) {
                                    video.style.transform = 'none';
                                    video.style.webkitTransform = 'none';
                                }
                                
                                resolve();
                            })
                            .catch(err => {
                                console.error('Ошибка воспроизведения видео:', err);
                                showError('Ошибка запуска камеры');
                                reject(err);
                            });
                    };
                });
                
                // Показываем модальное окно
                document.getElementById('cameraModal').style.display = 'block';
                hideStatus();
                
                // Запускаем сканирование
                setTimeout(() => startScanning(), 500);
                
            } catch (error) {
                console.error('Ошибка камеры:', error);
                showError(getCameraErrorMessage(error));
            }
        }
        
        // Запуск сканирования с оптимизацией для iOS
        function startScanning() {
            if (!barcodeDetector) {
                showError('Сканер штрихкодов недоступен');
                return;
            }
            
            const video = document.getElementById('cameraVideo');
            
            // Очищаем предыдущий интервал
            if (scanInterval) {
                clearInterval(scanInterval);
                scanInterval = null;
            }
            
            // Создаем canvas для анализа
            const canvas = document.createElement('canvas');
            const context = canvas.getContext('2d');
            
            // Оптимизация для iOS
            const scanDelay = isIOS ? 800 : 400; // iOS требует больше времени
            
            scanInterval = setInterval(async () => {
                try {
                    // Проверяем готовность видео
                    if (video.readyState !== video.HAVE_ENOUGH_DATA) {
                        return;
                    }
                    
                    // Проверяем кд между сканированиями
                    const now = Date.now();
                    if (now - lastScanTime < 1000) {
                        return;
                    }
                    
                    // Устанавливаем размеры canvas (уменьшаем для производительности)
                    canvas.width = video.videoWidth * 0.7;
                    canvas.height = video.videoHeight * 0.7;
                    
                    // Рисуем кадр видео на canvas
                    context.drawImage(
                        video, 
                        0, 0, video.videoWidth, video.videoHeight,
                        0, 0, canvas.width, canvas.height
                    );
                    
                    // Обновляем debug информацию
                    updateDebugInfo(canvas.width, canvas.height);
                    
                    // Детектируем штрихкоды
                    try {
                        const barcodes = await barcodeDetector.detect(canvas);
                        
                        if (barcodes && barcodes.length > 0) {
                            const barcode = barcodes[0];
                            console.log('Найден штрихкод:', barcode.rawValue, 'Тип:', barcode.format);
                            handleScannedBarcode(barcode.rawValue);
                        }
                    } catch (detectError) {
                        console.warn('Ошибка детектирования:', detectError);
                    }
                    
                } catch (error) {
                    console.error('Ошибка в цикле сканирования:', error);
                }
            }, scanDelay);
            
            console.log('Сканирование запущено, интервал:', scanDelay, 'мс');
            showStatus('Наведите на штрихкод...');
        }
        
        // Обработка отсканированного штрихкода
        function handleScannedBarcode(barcode) {
            const now = Date.now();
            
            // Проверяем кд
            if (now - lastScanTime < scanCooldown) {
                console.log('Сканирование слишком часто, пропускаем');
                return;
            }
            
            lastScanTime = now;
            
            // Визуальная обратная связь
            showSuccessFeedback();
            
            // Останавливаем сканирование
            if (scanInterval) {
                clearInterval(scanInterval);
                scanInterval = null;
            }
            
            // Через 1 секунду закрываем камеру и показываем результат
            setTimeout(() => {
                closeCamera();
                
                // Заполняем поле ввода
                document.getElementById('searchInput').value = barcode;
                
                // Выполняем поиск
                setTimeout(() => performSearch(), 300);
            }, 1000);
        }
        
        // Поиск товара
        function performSearch() {
            const searchValue = document.getElementById('searchInput').value.trim();
            if (!searchValue) return;
            
            // Очищаем предыдущие результаты
            document.getElementById('productsList').innerHTML = '';
            
            // Ищем в данных
            const results = searchProduct(searchValue);
            
            if (results.length === 0) {
                document.getElementById('productsList').innerHTML = `
                    <div class="no-results">
                        <div style="font-size: 48px; margin-bottom: 20px;">🔍</div>
                        <div style="font-size: 17px; margin-bottom: 10px;">
                            Товар не найден
                        </div>
                        <div style="font-size: 15px; color: #8e8e93;">
                            Штрихкод: <strong>${searchValue}</strong>
                        </div>
                    </div>
                `;
            } else {
                let html = '';
                results.forEach(product => {
                    html += `
                        <div class="product-card">
                            <div class="product-article">${product.article}</div>
                            <div class="product-name">${product.name}</div>
                            <div class="product-price">${product.price} руб.</div>
                            <div style="font-size: 13px; color: #8e8e93; margin-top: 8px;">
                                Штрихкод: ${product.barcode}
                            </div>
                        </div>
                    `;
                });
                document.getElementById('productsList').innerHTML = html;
            }
            
            document.getElementById('resultsContainer').style.display = 'block';
            document.getElementById('resultsContainer').scrollIntoView({ behavior: 'smooth' });
        }
        
        // Поиск в данных (по штрихкоду или части артикула)
        function searchProduct(searchValue) {
            const lines = productsData.split('\n');
            const results = [];
            
            for (const line of lines) {
                if (!line.trim()) continue;
                
                const parts = line.split(';');
                const barcode = parts[0];
                const article = parts[1];
                
                // Ищем по полному совпадению штрихкода или части артикула
                if (barcode === searchValue || 
                    article.toLowerCase().includes(searchValue.toLowerCase())) {
                    results.push({
                        barcode: barcode,
                        article: article,
                        name: parts[2],
                        price: parts[3]
                    });
                }
            }
            
            return results;
        }
        
        // Остановка камеры
        function stopCamera() {
            if (scanInterval) {
                clearInterval(scanInterval);
                scanInterval = null;
            }
            
            if (stream) {
                stream.getTracks().forEach(track => {
                    track.stop();
                });
                stream = null;
            }
            
            const video = document.getElementById('cameraVideo');
            if (video) {
                video.srcObject = null;
            }
            
            torchEnabled = false;
        }
        
        // Закрытие камеры
        function closeCamera() {
            stopCamera();
            document.getElementById('cameraModal').style.display = 'none';
            hideStatus();
            hideError();
            hideSuccessFeedback();
        }
        
        // Управление фонариком
        function toggleTorch() {
            if (!stream) return;
            
            const videoTrack = stream.getVideoTracks()[0];
            
            if ('applyConstraints' in videoTrack) {
                try {
                    torchEnabled = !torchEnabled;
                    
                    videoTrack.applyConstraints({
                        advanced: [{ torch: torchEnabled }]
                    }).then(() => {
                        console.log('Фонарик:', torchEnabled ? 'включен' : 'выключен');
                        document.getElementById('toggleTorch').textContent = 
                            torchEnabled ? '💡 Выключить' : '💡 Фонарик';
                    }).catch(err => {
                        console.warn('Фонарик не поддерживается:', err);
                        torchEnabled = false;
                    });
                } catch (error) {
                    console.warn('Ошибка управления фонариком:', error);
                }
            }
        }
        
        // Визуальная обратная связь при успешном сканировании
        function showSuccessFeedback() {
            const overlay = document.getElementById('successOverlay');
            overlay.style.display = 'flex';
            
            setTimeout(() => {
                overlay.style.display = 'none';
            }, 1000);
        }
        
        function hideSuccessFeedback() {
            document.getElementById('successOverlay').style.display = 'none';
        }
        
        // Показать статус
        function showStatus(message) {
            const status = document.getElementById('statusMessage');
            status.textContent = message;
            status.style.display = 'block';
        }
        
        function hideStatus() {
            document.getElementById('statusMessage').style.display = 'none';
        }
        
        // Показать ошибку
        function showError(message) {
            const errorEl = document.getElementById('cameraError');
            errorEl.textContent = message;
            errorEl.style.display = 'block';
        }
        
        function hideError() {
            document.getElementById('cameraError').style.display = 'none';
        }
        
        function showMessage(message) {
            alert(message);
        }
        
        // Получить понятное сообщение об ошибке камеры
        function getCameraErrorMessage(error) {
            if (error.name === 'NotAllowedError') {
                return 'Доступ к камере запрещен. Разрешите доступ в настройках Safari.';
            } else if (error.name === 'NotFoundError') {
                return 'Камера не найдена на устройстве.';
            } else if (error.name === 'NotReadableError') {
                return 'Камера используется другим приложением. Закройте другие приложения, использующие камеру.';
            } else if (error.name === 'OverconstrainedError') {
                return 'Запрошенные настройки камеры не поддерживаются.';
            } else {
                return `Ошибка: ${error.message || 'Неизвестная ошибка'}`;
            }
        }
        
        // Обновление debug информации
        function updateDebugInfo(width, height) {
            const debugEl = document.getElementById('debugInfo');
            if (debugEl.style.display === 'block') {
                debugEl.innerHTML = `
                    Размер: ${width}x${height}<br>
                    iOS: ${isIOS ? 'Да' : 'Нет'}<br>
                    Детектор: ${barcodeDetector ? 'Да' : 'Нет'}
                `;
            }
        }
        
        // Включение debug информации (для тестирования)
        document.addEventListener('keydown', function(e) {
            if (e.key === 'd' && e.ctrlKey) {
                const debugEl = document.getElementById('debugInfo');
                debugEl.style.display = debugEl.style.display === 'none' ? 'block' : 'none';
            }
        });
        
        // Автоматический поиск при вставке
        document.getElementById('searchInput').addEventListener('paste', function(e) {
            setTimeout(() => {
                performSearch();
            }, 100);
        });
    </script>
</body>
</html>