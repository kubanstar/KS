<html lang="ru">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Сканирование штрихкодов</title>
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
        
        .search-section {
            background: rgba(255, 255, 255, 0.95);
            border-radius: 20px;
            padding: 25px;
            box-shadow: 0 10px 40px rgba(0,0,0,0.2);
            margin-bottom: 20px;
            backdrop-filter: blur(10px);
            border: 1px solid rgba(255, 255, 255, 0.2);
        }
        
        .search-input {
            width: 100%;
            padding: 18px 20px;
            font-size: 18px;
            border: 2px solid #e0e0e0;
            border-radius: 15px;
            margin-bottom: 20px;
            background: white;
            box-shadow: 0 2px 10px rgba(0,0,0,0.05);
        }
        
        .buttons {
            display: flex;
            gap: 15px;
        }
        
        .btn {
            flex: 1;
            padding: 18px;
            font-size: 17px;
            font-weight: 600;
            border: none;
            border-radius: 15px;
            cursor: pointer;
            text-align: center;
            display: flex;
            align-items: center;
            justify-content: center;
            gap: 10px;
            -webkit-tap-highlight-color: transparent;
        }
        
        .btn-primary {
            background: linear-gradient(135deg, #1a2980 0%, #26d0ce 100%);
            color: white;
        }
        
        .btn-scan {
            background: linear-gradient(135deg, #FF416C 0%, #FF4B2B 100%);
            color: white;
        }
        
        .results-container {
            background: rgba(255, 255, 255, 0.95);
            border-radius: 20px;
            padding: 25px;
            box-shadow: 0 10px 40px rgba(0,0,0,0.2);
            margin-top: 20px;
            display: none;
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
        }
        
        .product-article {
            font-size: 20px;
            font-weight: 700;
            color: #1a2980;
            background: #f5f7ff;
            padding: 8px 16px;
            border-radius: 10px;
            display: inline-block;
            margin-bottom: 10px;
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
            color: #FF416C;
            margin-bottom: 10px;
        }
        
        .no-results {
            text-align: center;
            padding: 40px 20px;
            color: #666;
        }
        
        /* Модальное окно сканирования */
        .scanner-modal {
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
            width: 70%;
            height: 150px;
            border: 3px solid rgba(38, 208, 206, 0.9);
            border-radius: 15px;
            pointer-events: none;
            box-shadow: 0 0 0 1000px rgba(0, 0, 0, 0.6);
        }
        
        .scan-line {
            position: absolute;
            top: 0;
            left: 0;
            width: 100%;
            height: 3px;
            background: #26d0ce;
            animation: scan 2s ease-in-out infinite;
            box-shadow: 0 0 10px #26d0ce;
        }
        
        @keyframes scan {
            0% { top: 0; }
            50% { top: 100%; }
            100% { top: 0; }
        }
        
        .scanner-controls {
            position: absolute;
            bottom: 30px;
            left: 0;
            width: 100%;
            padding: 0 20px;
            display: flex;
            gap: 15px;
        }
        
        .scanner-btn {
            flex: 1;
            padding: 16px;
            border: none;
            border-radius: 12px;
            font-size: 16px;
            font-weight: 600;
            cursor: pointer;
            background: rgba(255, 255, 255, 0.2);
            color: white;
            backdrop-filter: blur(10px);
        }
        
        .scanner-btn-close {
            background: rgba(255, 65, 108, 0.8);
        }
        
        .debug-info {
            position: absolute;
            top: 20px;
            left: 20px;
            background: rgba(0,0,0,0.7);
            color: #26d0ce;
            padding: 10px;
            border-radius: 8px;
            font-size: 12px;
            font-family: monospace;
            max-width: 200px;
        }
        
        .notification {
            position: fixed;
            top: 20px;
            left: 50%;
            transform: translateX(-50%);
            background: rgba(0,0,0,0.9);
            color: white;
            padding: 15px 25px;
            border-radius: 12px;
            z-index: 10000;
            font-weight: 600;
            display: none;
            text-align: center;
            max-width: 90%;
        }
    </style>
</head>
<body>
    <div class="container">
        <div class="header">
            <h1>📦 Сканирование штрихкодов</h1>
            <p style="color: white; opacity: 0.9;">Автоматическое сканирование камерой</p>
        </div>
        
        <div class="search-section">
            <input type="text" 
                   class="search-input" 
                   id="searchInput" 
                   placeholder="Штрихкод появится здесь после сканирования..."
                   readonly>
            
            <div class="buttons">
                <button class="btn btn-primary" id="searchButton">
                    <span>🔍</span> Найти в базе
                </button>
                <button class="btn btn-scan" id="scanButton">
                    <span>📷</span> Сканировать
                </button>
            </div>
        </div>
        
        <div class="results-container" id="resultsContainer">
            <div class="results-title">Результаты поиска</div>
            <div id="productsList"></div>
        </div>
    </div>
    
    <!-- Модальное окно сканера -->
    <div class="scanner-modal" id="scannerModal">
        <div class="scanner-container">
            <video id="cameraVideo" playsinline autoplay muted></video>
            
            <div class="scan-overlay">
                <div class="scan-line"></div>
            </div>
            
            <div class="debug-info" id="debugInfo"></div>
            
            <div class="scanner-controls">
                <button class="scanner-btn" id="toggleTorch">
                    💡 Вкл/Выкл свет
                </button>
                <button class="scanner-btn scanner-btn-close" id="closeScanner">
                    ✕ Закрыть
                </button>
            </div>
        </div>
    </div>
    
    <div class="notification" id="notification"></div>

    <script>
        // Глобальные переменные
        let videoStream = null;
        let barcodeDetector = null;
        let scanInterval = null;
        let isScanning = false;
        let lastScannedCode = '';
        let lastScanTime = 0;
        let debugMode = true;
        let torchEnabled = false;
        
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
            },
            "5901234123457": {
                article: "TEST-001",
                name: "Тестовый товар",
                price: "100,00"
            }
        };
        
        // Инициализация при загрузке
        document.addEventListener('DOMContentLoaded', function() {
            initBarcodeDetector();
            
            document.getElementById('searchButton').addEventListener('click', performSearch);
            document.getElementById('scanButton').addEventListener('click', startScanner);
            document.getElementById('closeScanner').addEventListener('click', stopScanner);
            document.getElementById('toggleTorch').addEventListener('click', toggleTorch);
            
            // Для отладки
            document.addEventListener('keydown', function(e) {
                if (e.key === 'd' && e.ctrlKey) {
                    debugMode = !debugMode;
                    showNotification(`Режим отладки: ${debugMode ? 'ВКЛ' : 'ВЫКЛ'}`);
                }
            });
        });
        
        // Инициализация BarcodeDetector
        async function initBarcodeDetector() {
            if (!('BarcodeDetector' in window)) {
                showNotification('Ваш браузер не поддерживает BarcodeDetector API');
                return false;
            }
            
            try {
                // Получаем поддерживаемые форматы
                const formats = await BarcodeDetector.getSupportedFormats();
                
                if (formats.length === 0) {
                    showNotification('Нет поддержки форматов штрихкодов');
                    return false;
                }
                
                // Используем только EAN форматы для точности
                const eanFormats = formats.filter(f => 
                    f.includes('ean') || f.includes('upc') || f.includes('code_128')
                );
                
                const formatsToUse = eanFormats.length > 0 ? eanFormats : formats;
                
                console.log('Используем форматы:', formatsToUse);
                barcodeDetector = new BarcodeDetector({ formats: formatsToUse });
                
                return true;
            } catch (error) {
                console.error('Ошибка инициализации BarcodeDetector:', error);
                showNotification('Ошибка инициализации сканера');
                return false;
            }
        }
        
        // Запуск сканера
        async function startScanner() {
            try {
                showNotification('Запуск сканера...');
                
                // Останавливаем предыдущий сканер
                await stopScanner();
                
                // Инициализируем BarcodeDetector если нужно
                if (!barcodeDetector) {
                    const initialized = await initBarcodeDetector();
                    if (!initialized) return;
                }
                
                // Получаем доступ к камере
                await startCamera();
                
                // Показываем модальное окно
                document.getElementById('scannerModal').style.display = 'block';
                
                // Начинаем сканирование
                startScanningLoop();
                
                isScanning = true;
                showNotification('Сканирование начато');
                
            } catch (error) {
                console.error('Ошибка запуска сканера:', error);
                showNotification('Ошибка: ' + getErrorMessage(error));
            }
        }
        
        // Запуск камеры
        async function startCamera() {
            const video = document.getElementById('cameraVideo');
            
            // Останавливаем предыдущий поток
            if (videoStream) {
                videoStream.getTracks().forEach(track => track.stop());
            }
            
            // Пробуем разные настройки для iOS
            const constraints = {
                video: {
                    facingMode: 'environment',
                    width: { ideal: 1280, max: 1920 },
                    height: { ideal: 720, max: 1080 },
                    frameRate: { ideal: 24, max: 30 },
                    aspectRatio: { ideal: 4/3 }
                },
                audio: false
            };
            
            // Пробуем получить поток
            try {
                videoStream = await navigator.mediaDevices.getUserMedia(constraints);
            } catch (err) {
                console.log('Пробуем альтернативные настройки...', err);
                // Пробуем проще
                videoStream = await navigator.mediaDevices.getUserMedia({
                    video: {
                        facingMode: 'environment',
                        width: { min: 640, ideal: 1280 },
                        height: { min: 480, ideal: 720 }
                    },
                    audio: false
                });
            }
            
            video.srcObject = videoStream;
            
            // Ждем загрузки видео
            await new Promise((resolve) => {
                video.onloadedmetadata = () => {
                    video.play().then(resolve).catch(console.error);
                };
            });
            
            // Для iOS: исправляем ориентацию
            if (/iPad|iPhone|iPod/.test(navigator.userAgent)) {
                video.style.transform = 'scaleX(1)';
            }
        }
        
        // Запуск цикла сканирования
        function startScanningLoop() {
            if (scanInterval) clearInterval(scanInterval);
            
            const video = document.getElementById('cameraVideo');
            const canvas = document.createElement('canvas');
            const ctx = canvas.getContext('2d', { willReadFrequently: true });
            
            let frameCount = 0;
            let lastFpsUpdate = Date.now();
            
            scanInterval = setInterval(async () => {
                try {
                    if (!isScanning || video.readyState !== video.HAVE_ENOUGH_DATA) return;
                    
                    frameCount++;
                    
                    // Устанавливаем размеры canvas
                    canvas.width = video.videoWidth;
                    canvas.height = video.videoHeight;
                    
                    // Рисуем текущий кадр
                    ctx.drawImage(video, 0, 0, canvas.width, canvas.height);
                    
                    // Обновляем FPS каждую секунду
                    const now = Date.now();
                    if (now - lastFpsUpdate >= 1000) {
                        const fps = Math.round((frameCount * 1000) / (now - lastFpsUpdate));
                        updateDebugInfo(`FPS: ${fps}\nРазмер: ${canvas.width}x${canvas.height}`);
                        frameCount = 0;
                        lastFpsUpdate = now;
                    }
                    
                    // Проверяем кд между сканированиями
                    if (Date.now() - lastScanTime < 500) return;
                    
                    // Детектируем штрихкоды
                    const barcodes = await barcodeDetector.detect(canvas);
                    
                    if (barcodes && barcodes.length > 0) {
                        const barcode = barcodes[0];
                        
                        // Фильтруем ложные срабатывания
                        if (isValidBarcode(barcode.rawValue) && barcode.rawValue !== lastScannedCode) {
                            console.log('Найден штрихкод:', barcode.rawValue, 'Формат:', barcode.format);
                            handleScannedBarcode(barcode.rawValue);
                        }
                    }
                    
                } catch (error) {
                    console.warn('Ошибка сканирования:', error);
                }
            }, 100); // 10 FPS
        }
        
        // Проверка валидности штрихкода
        function isValidBarcode(code) {
            if (!code || code.length < 8 || code.length > 14) return false;
            
            // Проверяем что это цифры
            if (!/^\d+$/.test(code)) return false;
            
            // Для EAN-13 проверяем контрольную сумму
            if (code.length === 13) {
                return isValidEAN13(code);
            }
            
            return true;
        }
        
        // Проверка EAN-13
        function isValidEAN13(code) {
            let sum = 0;
            for (let i = 0; i < 12; i++) {
                sum += parseInt(code[i]) * (i % 2 === 0 ? 1 : 3);
            }
            const checksum = (10 - (sum % 10)) % 10;
            return checksum === parseInt(code[12]);
        }
        
        // Обработка найденного штрихкода
        function handleScannedBarcode(code) {
            lastScannedCode = code;
            lastScanTime = Date.now();
            
            console.log('Обработка штрихкода:', code);
            
            // Показываем уведомление
            showNotification(`Найден штрихкод: ${code}`);
            
            // Останавливаем сканирование
            stopScanner();
            
            // Заполняем поле ввода
            document.getElementById('searchInput').value = code;
            
            // Автоматически ищем товар
            setTimeout(() => {
                performSearch();
            }, 300);
        }
        
        // Переключение фонарика
        function toggleTorch() {
            if (!videoStream) return;
            
            const videoTrack = videoStream.getVideoTracks()[0];
            
            if (videoTrack && videoTrack.getCapabilities && videoTrack.getCapabilities().torch) {
                torchEnabled = !torchEnabled;
                
                videoTrack.applyConstraints({
                    advanced: [{ torch: torchEnabled }]
                }).then(() => {
                    showNotification(`Фонарик: ${torchEnabled ? 'ВКЛ' : 'ВЫКЛ'}`);
                }).catch(err => {
                    console.warn('Фонарик не поддерживается:', err);
                    torchEnabled = false;
                });
            } else {
                showNotification('Фонарик не поддерживается на этом устройстве');
            }
        }
        
        // Остановка сканера
        async function stopScanner() {
            isScanning = false;
            
            if (scanInterval) {
                clearInterval(scanInterval);
                scanInterval = null;
            }
            
            if (videoStream) {
                videoStream.getTracks().forEach(track => track.stop());
                videoStream = null;
            }
            
            const video = document.getElementById('cameraVideo');
            video.srcObject = null;
            
            document.getElementById('scannerModal').style.display = 'none';
            updateDebugInfo('');
            
            torchEnabled = false;
        }
        
        // Поиск товара
        function performSearch() {
            const searchValue = document.getElementById('searchInput').value.trim();
            if (!searchValue) {
                showNotification('Введите штрихкод для поиска');
                return;
            }
            
            const productsList = document.getElementById('productsList');
            productsList.innerHTML = '';
            
            const resultsContainer = document.getElementById('resultsContainer');
            resultsContainer.style.display = 'block';
            
            let found = false;
            
            // Проверяем точное совпадение
            if (productsData[searchValue]) {
                const product = productsData[searchValue];
                displayProduct(product, searchValue);
                found = true;
            } else {
                // Ищем по части
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
        
        // Обновление отладочной информации
        function updateDebugInfo(info) {
            if (!debugMode) {
                document.getElementById('debugInfo').style.display = 'none';
                return;
            }
            
            const debugEl = document.getElementById('debugInfo');
            debugEl.textContent = info;
            debugEl.style.display = 'block';
        }
        
        // Показать уведомление
        function showNotification(message) {
            const notification = document.getElementById('notification');
            notification.textContent = message;
            notification.style.display = 'block';
            
            setTimeout(() => {
                notification.style.display = 'none';
            }, 3000);
        }
        
        // Получить понятное сообщение об ошибке
        function getErrorMessage(error) {
            if (error.name === 'NotAllowedError') {
                return 'Разрешите доступ к камере';
            } else if (error.name === 'NotFoundError') {
                return 'Камера не найдена';
            } else if (error.name === 'NotReadableError') {
                return 'Камера используется другим приложением';
            } else {
                return error.message || 'Неизвестная ошибка';
            }
        }
        
        // Автоматический поиск при вставке
        document.getElementById('searchInput').addEventListener('paste', function(e) {
            setTimeout(() => {
                performSearch();
            }, 100);
        });
    </script>
</body>
</html>