<html lang="ru">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Поиск товаров</title>
    <style>
        body {
            font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, sans-serif;
            max-width: 800px;
            margin: 0 auto;
            padding: 20px;
            background-color: #f5f5f5;
        }
        
        .search-container {
            background: white;
            padding: 30px;
            border-radius: 10px;
            box-shadow: 0 2px 10px rgba(0,0,0,0.1);
            text-align: center;
        }
        
        .search-input-wrapper {
            position: relative;
            width: 100%;
            margin-bottom: 20px;
        }
        
        .search-input {
            width: 100%;
            padding: 15px 40px 15px 15px;
            font-size: 16px;
            border: 2px solid #ddd;
            border-radius: 5px;
            box-sizing: border-box;
            -webkit-appearance: none;
            appearance: none;
        }
        
        .search-input:focus {
            border-color: #007AFF;
            outline: none;
        }
        
        .clear-search-btn {
            position: absolute;
            right: 10px;
            top: 50%;
            transform: translateY(-50%);
            background: none;
            border: none;
            color: #999;
            font-size: 18px;
            cursor: pointer;
            padding: 5px;
            display: none;
            transition: color 0.3s;
            line-height: 1;
        }
        
        .buttons-container {
            display: flex;
            gap: 10px;
            margin-bottom: 20px;
            flex-wrap: wrap;
            justify-content: center;
        }
        
        .search-button {
            background-color: #34C759;
            color: white;
            border: none;
            padding: 15px 30px;
            font-size: 16px;
            border-radius: 5px;
            cursor: pointer;
            transition: background-color 0.3s;
            flex-grow: 1;
            max-width: 200px;
            -webkit-tap-highlight-color: transparent;
        }
        
        .scan-button {
            background-color: #007AFF;
            color: white;
            border: none;
            padding: 15px 30px;
            font-size: 16px;
            border-radius: 5px;
            cursor: pointer;
            transition: background-color 0.3s;
            flex-grow: 1;
            max-width: 200px;
            -webkit-tap-highlight-color: transparent;
        }
        
        .scan-button:active {
            background-color: #0056CC;
        }
        
        .scan-button.ios {
            background-color: #FF9500;
        }
        
        .scan-button.ios:active {
            background-color: #E58500;
        }
        
        .results-container {
            margin-top: 20px;
            display: none;
        }
        
        .product-card {
            background: white;
            padding: 20px;
            margin-bottom: 10px;
            border-radius: 5px;
            box-shadow: 0 1px 5px rgba(0,0,0,0.1);
            border-left: 4px solid #34C759;
        }
        
        .article {
            font-weight: bold;
            color: #333;
        }
        
        .name {
            font-size: 16px;
            color: #222;
            margin: 10px 0;
        }
        
        .price {
            color: #FF3B30;
            font-weight: bold;
            font-size: 20px;
        }
        
        .modal-overlay {
            position: fixed;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            background-color: rgba(0, 0, 0, 0.9);
            display: none;
            justify-content: center;
            align-items: center;
            z-index: 1000;
        }
        
        .modal-frame {
            background: white;
            padding: 20px;
            border-radius: 15px;
            text-align: center;
            max-width: 90%;
            width: 400px;
            max-height: 80vh;
            overflow-y: auto;
            box-shadow: 0 10px 30px rgba(0,0,0,0.3);
        }
        
        .video-wrapper {
            position: relative;
            width: 100%;
            max-width: 400px;
            margin: 20px auto;
            overflow: hidden;
            border-radius: 10px;
        }
        
        #cameraVideo {
            width: 100%;
            height: auto;
            display: block;
            background: black;
        }
        
        .camera-controls {
            margin-top: 20px;
            display: flex;
            gap: 10px;
            justify-content: center;
            flex-wrap: wrap;
        }
        
        .camera-btn {
            padding: 12px 20px;
            border: none;
            border-radius: 8px;
            cursor: pointer;
            font-size: 16px;
            font-weight: bold;
            background-color: #007AFF;
            color: white;
            flex: 1;
            min-width: 120px;
            -webkit-tap-highlight-color: transparent;
        }
        
        .close-btn {
            background-color: #FF3B30;
            color: white;
            border: none;
            padding: 12px 20px;
            border-radius: 8px;
            cursor: pointer;
            font-size: 16px;
            font-weight: bold;
            margin-top: 10px;
            width: 100%;
            max-width: 200px;
            -webkit-tap-highlight-color: transparent;
        }
        
        .scan-box {
            position: absolute;
            top: 50%;
            left: 50%;
            transform: translate(-50%, -50%);
            width: 80%;
            height: 150px;
            border: 3px solid #34C759;
            border-radius: 10px;
            z-index: 1;
            pointer-events: none;
        }
        
        .scan-line {
            position: absolute;
            top: 0;
            left: 0;
            width: 100%;
            height: 3px;
            background-color: #34C759;
            animation: scan 2s linear infinite;
        }
        
        @keyframes scan {
            0% { top: 0; }
            50% { top: 100%; }
            100% { top: 0; }
        }
        
        .error-message {
            color: #FF3B30;
            padding: 10px;
            margin: 10px 0;
            border-radius: 5px;
            background-color: rgba(255, 59, 48, 0.1);
            font-size: 14px;
        }
    </style>
</head>
<body>
    <div class="search-container">
        <h2>Поиск товаров</h2>
        
        <div class="search-input-wrapper">
            <input type="text" 
                   class="search-input" 
                   id="searchInput" 
                   placeholder="Введите штрихкод для поиска..."
                   autocomplete="off">
            <button class="clear-search-btn" id="clearSearchBtn" title="Очистить поле поиска">✕</button>
        </div>
        
        <div class="buttons-container">
            <button class="search-button" id="searchButton">Найти</button>
            <button class="scan-button ios" id="scanButton">
                📷 Сканировать
            </button>
        </div>
        
        <div class="results-container" id="resultsContainer">
            <!-- Результаты поиска будут здесь -->
        </div>
    </div>

    <!-- Модальное окно камеры -->
    <div class="modal-overlay" id="cameraModal">
        <div class="modal-frame">
            <h3>Сканирование штрихкода</h3>
            <div class="video-wrapper" id="videoContainer">
                <div class="scan-box">
                    <div class="scan-line"></div>
                </div>
                <video id="cameraVideo" playsinline></video>
            </div>
            <div id="cameraError" class="error-message" style="display: none;"></div>
            <div class="camera-controls">
                <button class="camera-btn" id="stopCamera">Остановить</button>
                <button class="close-btn" id="closeCameraModal">Закрыть</button>
            </div>
        </div>
    </div>

    <script>
        // Глобальные переменные
        let stream = null;
        let barcodeDetector = null;
        let scanInterval = null;
        let isIOSDevice = false;
        
        // Пример данных
        const productsData = `6970787612595;KS-8001;Набор для творчества "ЧАСТИЧНАЯ ВЫКЛАДКА СТРАЗАМИ" 10*15 в пакете;70,00;70,00;7;;10;2;0,035;;0,050;0,010;;Cb010003474_1;;;200;Cb010003474_1;`;

        // Определяем платформу
        function detectPlatform() {
            const userAgent = navigator.userAgent || navigator.vendor || window.opera;
            isIOSDevice = /iPad|iPhone|iPod/.test(userAgent) && !window.MSStream;
            console.log('iOS устройство:', isIOSDevice);
        }

        // Парсинг данных
        function parseProductsData(data) {
            const lines = data.trim().split('\n');
            const products = [];
            
            for (const line of lines) {
                if (!line.trim()) continue;
                
                const parts = line.split(';');
                
                products.push({
                    barcode: parts[0] || '',
                    article: parts[1] || '',
                    name: parts[2] || '',
                    price: parts[3] || ''
                });
            }
            
            return products;
        }

        // Поиск по штрихкоду
        function searchByBarcode(barcode) {
            const products = parseProductsData(productsData);
            return products.filter(product => product.barcode === barcode);
        }

        // Отображение результатов
        function displayResults(results, barcode) {
            const resultsContainer = document.getElementById('resultsContainer');
            resultsContainer.innerHTML = '';

            if (results.length === 0) {
                resultsContainer.innerHTML = `<div style="text-align: center; padding: 20px; color: #666; font-style: italic;">
                    Товар со штрихкодом <strong>${barcode}</strong> не найден
                </div>`;
            } else {
                results.forEach(product => {
                    const productCard = document.createElement('div');
                    productCard.className = 'product-card';
                    productCard.innerHTML = `
                        <div class="article">Артикул: ${product.article}</div>
                        <div class="name">${product.name}</div>
                        <div class="price">Цена: ${product.price} руб.</div>
                        <div style="margin-top: 10px; font-size: 14px; color: #666;">
                            Штрихкод: ${product.barcode}
                        </div>
                    `;
                    resultsContainer.appendChild(productCard);
                });
            }

            resultsContainer.style.display = 'block';
            resultsContainer.scrollIntoView({ behavior: 'smooth' });
        }

        // Проверка поддержки BarcodeDetector
        function isBarcodeDetectorSupported() {
            return ('BarcodeDetector' in window);
        }

        // Инициализация детектора штрихкодов
        async function initBarcodeDetector() {
            if (!isBarcodeDetectorSupported()) {
                console.warn('BarcodeDetector API не поддерживается');
                return null;
            }
            
            try {
                const formats = await BarcodeDetector.getSupportedFormats();
                console.log('Поддерживаемые форматы:', formats);
                return new BarcodeDetector({ formats: formats });
            } catch (error) {
                console.error('Ошибка инициализации BarcodeDetector:', error);
                return null;
            }
        }

        // Исправление трансформации видео для iOS
        function fixVideoTransform(videoElement) {
            if (!isIOSDevice) return;
            
            // Сбрасываем любые существующие трансформации
            videoElement.style.transform = 'scaleX(1)';
            videoElement.style.webkitTransform = 'scaleX(1)';
            
            // Применяем правильную трансформацию
            setTimeout(() => {
                videoElement.style.transform = 'scaleX(-1)';
                videoElement.style.webkitTransform = 'scaleX(-1)';
                console.log('Применена трансформация видео для iOS');
            }, 100);
        }

        // Открытие камеры (оптимизировано для iOS)
        async function openCamera() {
            try {
                // Останавливаем предыдущий поток
                stopCameraStream();
                
                // Прячем ошибку
                document.getElementById('cameraError').style.display = 'none';
                
                // Запрашиваем разрешение на камеру
                const constraints = {
                    video: {
                        facingMode: 'environment',
                        width: { ideal: 1280 },
                        height: { ideal: 720 }
                    },
                    audio: false
                };
                
                console.log('Запрашиваю доступ к камере...');
                stream = await navigator.mediaDevices.getUserMedia(constraints);
                console.log('Доступ к камере получен');
                
                // Настраиваем видео элемент
                const video = document.getElementById('cameraVideo');
                video.srcObject = stream;
                
                // Применяем исправление трансформации
                fixVideoTransform(video);
                
                // Показываем модальное окно
                document.getElementById('cameraModal').style.display = 'flex';
                
                // Ждем готовности видео
                await new Promise((resolve) => {
                    video.onloadedmetadata = () => {
                        video.play().then(resolve).catch(console.error);
                    };
                });
                
                // Инициализируем детектор штрихкодов
                if (!barcodeDetector && isBarcodeDetectorSupported()) {
                    barcodeDetector = await initBarcodeDetector();
                }
                
                // Запускаем обнаружение штрихкодов
                if (barcodeDetector) {
                    startBarcodeScanning();
                } else {
                    console.log('Детектор штрихкодов не доступен, используем fallback');
                }
                
            } catch (error) {
                console.error('Ошибка камеры:', error);
                showCameraError(error);
            }
        }

        // Запуск сканирования штрихкодов
        function startBarcodeScanning() {
            if (!barcodeDetector) return;
            
            const video = document.getElementById('cameraVideo');
            const canvas = document.createElement('canvas');
            const context = canvas.getContext('2d');
            
            // Очищаем предыдущий интервал
            if (scanInterval) clearInterval(scanInterval);
            
            scanInterval = setInterval(async () => {
                try {
                    if (video.readyState === video.HAVE_ENOUGH_DATA) {
                        canvas.width = video.videoWidth;
                        canvas.height = video.videoHeight;
                        
                        // Рисуем видео на canvas
                        context.drawImage(video, 0, 0, canvas.width, canvas.height);
                        
                        // Детектируем штрихкоды
                        const barcodes = await barcodeDetector.detect(canvas);
                        
                        if (barcodes && barcodes.length > 0) {
                            const barcode = barcodes[0];
                            console.log('Найден штрихкод:', barcode.rawValue);
                            handleScannedCode(barcode.rawValue);
                        }
                    }
                } catch (error) {
                    console.error('Ошибка сканирования:', error);
                }
            }, 300);
        }

        // Обработка отсканированного кода
        function handleScannedCode(code) {
            if (!code || code.trim().length === 0) return;
            
            console.log('Обработка штрихкода:', code);
            
            // Останавливаем камеру
            stopCameraStream();
            
            // Закрываем модальное окно
            document.getElementById('cameraModal').style.display = 'none';
            
            // Заполняем поле поиска
            document.getElementById('searchInput').value = code;
            document.getElementById('clearSearchBtn').style.display = 'block';
            
            // Выполняем поиск
            performSearch();
        }

        // Выполнение поиска
        function performSearch() {
            const barcode = document.getElementById('searchInput').value.trim();
            if (!barcode) return;
            
            const results = searchByBarcode(barcode);
            displayResults(results, barcode);
        }

        // Остановка потока камеры
        function stopCameraStream() {
            if (stream) {
                stream.getTracks().forEach(track => track.stop());
                stream = null;
            }
            if (scanInterval) {
                clearInterval(scanInterval);
                scanInterval = null;
            }
            const video = document.getElementById('cameraVideo');
            video.srcObject = null;
        }

        // Показ ошибки камеры
        function showCameraError(error) {
            const errorElement = document.getElementById('cameraError');
            let errorMessage = 'Ошибка камеры';
            
            if (error.name === 'NotAllowedError') {
                errorMessage = 'Доступ к камере запрещен. Разрешите доступ в настройках Safari.';
            } else if (error.name === 'NotFoundError') {
                errorMessage = 'Камера не найдена';
            } else if (error.name === 'NotReadableError') {
                errorMessage = 'Камера уже используется другим приложением';
            } else if (error.name === 'OverconstrainedError') {
                errorMessage = 'Запрошенные настройки камеры не поддерживаются';
            } else {
                errorMessage = `Ошибка: ${error.message || 'Неизвестная ошибка'}`;
            }
            
            errorElement.textContent = errorMessage;
            errorElement.style.display = 'block';
        }

        // Очистка поля поиска
        function clearSearchField() {
            document.getElementById('searchInput').value = '';
            document.getElementById('clearSearchBtn').style.display = 'none';
            document.getElementById('resultsContainer').style.display = 'none';
            document.getElementById('searchInput').focus();
        }

        // Инициализация при загрузке страницы
        window.addEventListener('DOMContentLoaded', () => {
            detectPlatform();
            
            // Настройка элементов
            const searchInput = document.getElementById('searchInput');
            const clearSearchBtn = document.getElementById('clearSearchBtn');
            const searchButton = document.getElementById('searchButton');
            const scanButton = document.getElementById('scanButton');
            const cameraModal = document.getElementById('cameraModal');
            const closeCameraModal = document.getElementById('closeCameraModal');
            const stopCameraBtn = document.getElementById('stopCamera');
            
            // Обработчики событий
            searchButton.addEventListener('click', performSearch);
            
            scanButton.addEventListener('click', (e) => {
                e.preventDefault();
                openCamera();
            });
            
            searchInput.addEventListener('keydown', (e) => {
                if (e.key === 'Enter') performSearch();
                if (e.key === 'Escape') clearSearchField();
            });
            
            searchInput.addEventListener('input', () => {
                clearSearchBtn.style.display = searchInput.value ? 'block' : 'none';
            });
            
            clearSearchBtn.addEventListener('click', clearSearchField);
            
            closeCameraModal.addEventListener('click', () => {
                stopCameraStream();
                cameraModal.style.display = 'none';
            });
            
            stopCameraBtn.addEventListener('click', () => {
                stopCameraStream();
                cameraModal.style.display = 'none';
            });
            
            cameraModal.addEventListener('click', (e) => {
                if (e.target === cameraModal) {
                    stopCameraStream();
                    cameraModal.style.display = 'none';
                }
            });
            
            // Предварительная инициализация детектора
            if (isBarcodeDetectorSupported()) {
                initBarcodeDetector().then(detector => {
                    barcodeDetector = detector;
                    console.log('Детектор штрихкодов инициализирован');
                });
            }
            
            // Фокус на поле ввода
            searchInput.focus();
        });
    </script>
</body>
</html>