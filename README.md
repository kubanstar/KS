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
        
        .card {
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
        
        /* Модальное окно сканирования - ПРАВИЛЬНОЕ */
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
            overflow: hidden;
        }
        
        /* Видео элемент - фон ВНЕ рамки */
        #cameraVideo {
            width: 100%;
            height: 100%;
            object-fit: cover;
            display: block;
            transform: scaleX(1);
            -webkit-transform: scaleX(1);
            background: #000;
            filter: brightness(0.3) blur(5px); /* Затемняем и размываем фон */
        }
        
        /* Область сканирования - СВЕТЛАЯ внутри рамки */
        .scan-area {
            position: absolute;
            top: 50%;
            left: 50%;
            transform: translate(-50%, -50%);
            width: 70%;
            max-width: 350px;
            height: 150px;
            background: rgba(255, 255, 255, 0.9); /* Белый фон внутри рамки */
            border-radius: 15px;
            overflow: hidden; /* Обрезаем видео внутри */
            z-index: 10;
            box-shadow: 0 0 0 1000px rgba(0, 0, 0, 0.8); /* Тёмная маска вокруг */
        }
        
        /* Видео внутри области сканирования - НЕ затемнено */
        .scan-area-video {
            width: 100%;
            height: 100%;
            object-fit: cover;
            display: block;
            transform: scaleX(1);
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
            z-index: 11;
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
        
        .loader {
            position: absolute;
            top: 50%;
            left: 50%;
            transform: translate(-50%, -50%);
            width: 50px;
            height: 50px;
            border: 5px solid rgba(255,255,255,0.3);
            border-radius: 50%;
            border-top-color: #26d0ce;
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
            background: rgba(38, 208, 206, 0.95);
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
            <p style="color: white; opacity: 0.9;">Используем BarcodeDetector</p>
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
                <button class="btn btn-scan" id="scanButton">
                    <span>📷</span> Сканировать
                </button>
            </div>
        </div>
        
        <div class="card results-container" id="resultsContainer">
            <div class="results-title">Результаты поиска</div>
            <div id="productsList"></div>
        </div>
    </div>
    
    <!-- Модальное окно сканера -->
    <div class="scanner-modal" id="scannerModal">
        <div class="scanner-container">
            <div class="loader" id="scannerLoader"></div>
            
            <!-- Фоновое видео (затемненное) -->
            <video id="cameraVideo" playsinline autoplay muted></video>
            
            <!-- Область сканирования (светлая) -->
            <div class="scan-area">
                <video id="scanAreaVideo" playsinline autoplay muted class="scan-area-video"></video>
                <div class="scan-line"></div>
            </div>
            
            <div class="scanner-info">
                Наведите область сканирования на штрихкод
            </div>
            
            <div class="scanned-badge" id="scannedBadge">✓ Найдено!</div>
            
            <div class="scanner-controls">
                <button class="scanner-btn" id="toggleFlash">
                    💡 Вкл/Выкл свет
                </button>
                <button class="scanner-btn scanner-btn-close" id="closeScanner">
                    ✕ Закрыть
                </button>
            </div>
        </div>
    </div>
    
    <div class="notification" id="notification"></div>

    <!-- Подключаем polyfill для BarcodeDetector -->
    <script src="https://cdn.jsdelivr.net/npm/@ericblade/quagga2@1.7.5/dist/quagga.min.js"></script>
    <script>
        // Создаем полифилл для BarcodeDetector если его нет
        if (!window.BarcodeDetector) {
            console.log('BarcodeDetector не поддерживается, используем Quagga2 как fallback');
            
            // Создаем полифилл
            window.BarcodeDetector = class {
                constructor(options = {}) {
                    this.formats = options.formats || ['ean_13', 'ean_8', 'code_128', 'code_39'];
                    this.quaggaInitialized = false;
                }
                
                static async getSupportedFormats() {
                    return ['ean_13', 'ean_8', 'code_128', 'code_39', 'upc_a', 'upc_e'];
                }
                
                async detect(imageBitmapSource) {
                    if (!this.quaggaInitialized) {
                        await this.initQuagga();
                    }
                    
                    return new Promise((resolve) => {
                        // Создаем временный canvas для анализа
                        const canvas = document.createElement('canvas');
                        const ctx = canvas.getContext('2d');
                        
                        if (imageBitmapSource instanceof HTMLVideoElement) {
                            canvas.width = imageBitmapSource.videoWidth;
                            canvas.height = imageBitmapSource.videoHeight;
                            ctx.drawImage(imageBitmapSource, 0, 0);
                        } else if (imageBitmapSource instanceof HTMLCanvasElement) {
                            canvas.width = imageBitmapSource.width;
                            canvas.height = imageBitmapSource.height;
                            ctx.drawImage(imageBitmapSource, 0, 0);
                        } else {
                            canvas.width = imageBitmapSource.width;
                            canvas.height = imageBitmapSource.height;
                            ctx.drawImage(imageBitmapSource, 0, 0);
                        }
                        
                        // Анализируем с Quagga
                        Quagga.decodeSingle({
                            decoder: {
                                readers: this.formats.filter(f => 
                                    ['ean_reader', 'ean_8_reader', 'code_128_reader', 'code_39_reader', 'upc_reader', 'upc_e_reader']
                                    .includes(f + '_reader')
                                )
                            },
                            locate: true,
                            src: canvas.toDataURL()
                        }, function(result) {
                            if (result && result.codeResult) {
                                resolve([{
                                    rawValue: result.codeResult.code,
                                    format: result.codeResult.format
                                }]);
                            } else {
                                resolve([]);
                            }
                        });
                    });
                }
                
                async initQuagga() {
                    return new Promise((resolve) => {
                        // Quagga уже загружен через CDN
                        this.quaggaInitialized = true;
                        resolve();
                    });
                }
            };
        }
        
        // Глобальные переменные
        let videoStream = null;
        let barcodeDetector = null;
        let scanInterval = null;
        let isScanning = false;
        let lastScannedCode = '';
        let lastScanTime = 0;
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
            }
        };
        
        // Инициализация при загрузке
        document.addEventListener('DOMContentLoaded', function() {
            initBarcodeDetector();
            
            document.getElementById('searchButton').addEventListener('click', performSearch);
            document.getElementById('scanButton').addEventListener('click', startScanner);
            document.getElementById('closeScanner').addEventListener('click', stopScanner);
            document.getElementById('toggleFlash').addEventListener('click', toggleFlash);
        });
        
        // Инициализация BarcodeDetector
        async function initBarcodeDetector() {
            try {
                // Проверяем поддержку
                if (!('BarcodeDetector' in window)) {
                    showNotification('Используем совместимый сканер');
                }
                
                // Получаем поддерживаемые форматы
                const formats = await BarcodeDetector.getSupportedFormats();
                console.log('Поддерживаемые форматы:', formats);
                
                // Используем только популярные форматы
                const popularFormats = formats.filter(f => 
                    ['ean_13', 'ean_8', 'code_128', 'code_39', 'upc_a', 'upc_e'].includes(f)
                );
                
                barcodeDetector = new BarcodeDetector({ 
                    formats: popularFormats.length > 0 ? popularFormats : formats 
                });
                
                console.log('BarcodeDetector инициализирован');
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
                    await initBarcodeDetector();
                }
                
                // Запускаем камеру
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
            const backgroundVideo = document.getElementById('cameraVideo');
            const scanVideo = document.getElementById('scanAreaVideo');
            
            // Останавливаем предыдущий поток
            if (videoStream) {
                videoStream.getTracks().forEach(track => track.stop());
            }
            
            // Настройки для iOS
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
            
            // Подключаем поток к обоим видео элементам
            backgroundVideo.srcObject = videoStream;
            scanVideo.srcObject = videoStream;
            
            // Запускаем видео
            await Promise.all([
                backgroundVideo.play(),
                scanVideo.play()
            ]);
            
            console.log('Камера запущена');
        }
        
        // Запуск цикла сканирования
        function startScanningLoop() {
            if (scanInterval) clearInterval(scanInterval);
            
            const scanVideo = document.getElementById('scanAreaVideo');
            const canvas = document.createElement('canvas');
            const ctx = canvas.getContext('2d');
            
            scanInterval = setInterval(async () => {
                try {
                    if (!isScanning || scanVideo.readyState !== scanVideo.HAVE_ENOUGH_DATA) {
                        return;
                    }
                    
                    // Проверяем кд
                    if (Date.now() - lastScanTime < 500) return;
                    
                    // Устанавливаем размеры canvas
                    canvas.width = scanVideo.videoWidth;
                    canvas.height = scanVideo.videoHeight;
                    
                    // Рисуем область сканирования
                    ctx.drawImage(scanVideo, 0, 0, canvas.width, canvas.height);
                    
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
            }, 300); // 3 раза в секунду
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
            
            // Визуальная обратная связь
            showScannedBadge(code);
            
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
        function toggleFlash() {
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
            
            const backgroundVideo = document.getElementById('cameraVideo');
            const scanVideo = document.getElementById('scanAreaVideo');
            backgroundVideo.srcObject = null;
            scanVideo.srcObject = null;
            
            document.getElementById('scannerModal').style.display = 'none';
            document.getElementById('scannedBadge').style.display = 'none';
            
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
        
        // Визуальная обратная связь
        function showScannedBadge(code) {
            const badge = document.getElementById('scannedBadge');
            badge.textContent = `✓ ${code}`;
            badge.style.display = 'block';
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
    </script>
</body>
</html>