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
        
        .search-box {
            background: white;
            padding: 30px;
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
        }
        
        .product-price {
            color: #ff3b30;
            font-weight: 600;
            font-size: 18px;
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
        }
        
        #cameraVideo {
            width: 100%;
            height: 100%;
            object-fit: cover;
        }
        
        .scan-overlay {
            position: absolute;
            top: 50%;
            left: 50%;
            transform: translate(-50%, -50%);
            width: 70%;
            height: 200px;
            border: 4px solid #34c759;
            border-radius: 16px;
            pointer-events: none;
        }
        
        .scan-line {
            position: absolute;
            top: 0;
            left: 0;
            width: 100%;
            height: 4px;
            background: linear-gradient(90deg, transparent, #34c759, transparent);
            animation: scan 2s linear infinite;
        }
        
        @keyframes scan {
            0% { top: 0; opacity: 1; }
            50% { top: calc(100% - 4px); opacity: 1; }
            51% { opacity: 0; }
            100% { top: 0; opacity: 0; }
        }
        
        .modal-controls {
            position: absolute;
            bottom: 40px;
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
            background: rgba(255,255,255,0.2);
            backdrop-filter: blur(20px);
            border: none;
            border-radius: 12px;
            color: white;
            font-size: 17px;
            font-weight: 600;
            cursor: pointer;
            -webkit-tap-highlight-color: transparent;
        }
        
        .modal-button.close {
            background: rgba(255,59,48,0.8);
        }
        
        .error-message {
            position: absolute;
            top: 50%;
            left: 20px;
            right: 20px;
            transform: translateY(-50%);
            background: rgba(255,59,48,0.9);
            color: white;
            padding: 20px;
            border-radius: 12px;
            text-align: center;
            display: none;
            z-index: 1001;
        }
    </style>
</head>
<body>
    <div class="container">
        <div class="header">
            <h1>Сканирование штрихкодов</h1>
        </div>
        
        <div class="search-box">
            <input type="text" 
                   class="search-input" 
                   id="searchInput" 
                   placeholder="Введите штрихкод или отсканируйте"
                   autocomplete="off">
            
            <div class="buttons">
                <button class="button search" id="searchButton">
                    Найти
                </button>
                <button class="button scan" id="scanButton">
                    📷 Сканировать
                </button>
            </div>
        </div>
        
        <div class="results" id="resultsContainer">
            <!-- Результаты будут здесь -->
        </div>
    </div>
    
    <!-- Модальное окно камеры -->
    <div class="modal" id="cameraModal">
        <div class="modal-content">
            <video id="cameraVideo" playsinline></video>
            
            <div class="scan-overlay">
                <div class="scan-line"></div>
            </div>
            
            <div class="modal-controls">
                <div class="modal-buttons">
                    <button class="modal-button" id="stopCamera">
                        Стоп
                    </button>
                    <button class="modal-button close" id="closeCameraModal">
                        Закрыть
                    </button>
                </div>
            </div>
            
            <div class="error-message" id="cameraError">
                <!-- Сообщения об ошибках -->
            </div>
        </div>
    </div>

    <script>
        // Глобальные переменные
        let stream = null;
        let barcodeDetector = null;
        let scanInterval = null;
        let isIOS = /iPad|iPhone|iPod/.test(navigator.userAgent) && !window.MSStream;
        let isAndroid = /android/i.test(navigator.userAgent);
        
        // Тестовые данные
        const productsData = `6970787612595;KS-8001;Набор для творчества "ЧАСТИЧНАЯ ВЫКЛАДКА СТРАЗАМИ" 10*15 в пакете;70,00;70,00;7;;10;2;0,035;;0,050;0,010;;Cb010003474_1;;;200;Cb010003474_1;`;
        
        // Инициализация при загрузке
        document.addEventListener('DOMContentLoaded', function() {
            console.log('Устройство:', isIOS ? 'iOS' : isAndroid ? 'Android' : 'Другое');
            
            // Инициализация BarcodeDetector
            if ('BarcodeDetector' in window) {
                BarcodeDetector.getSupportedFormats()
                    .then(formats => {
                        console.log('Поддерживаемые форматы:', formats);
                        barcodeDetector = new BarcodeDetector({ formats });
                    })
                    .catch(err => console.warn('BarcodeDetector не поддерживается:', err));
            }
            
            // Обработчики событий
            document.getElementById('searchButton').addEventListener('click', performSearch);
            document.getElementById('scanButton').addEventListener('click', openCamera);
            document.getElementById('stopCamera').addEventListener('click', stopCamera);
            document.getElementById('closeCameraModal').addEventListener('click', closeCamera);
            
            document.getElementById('searchInput').addEventListener('keypress', function(e) {
                if (e.key === 'Enter') performSearch();
            });
            
            // Закрытие модального окна по клику вне области
            document.getElementById('cameraModal').addEventListener('click', function(e) {
                if (e.target === this) closeCamera();
            });
        });
        
        // Открытие камеры
        async function openCamera() {
            try {
                console.log('Открытие камеры...');
                
                // Останавливаем предыдущий поток
                if (stream) {
                    stream.getTracks().forEach(track => track.stop());
                }
                
                // Определяем параметры камеры
                const constraints = {
                    video: {
                        facingMode: { exact: 'environment' }, // Только тыловая камера
                        width: { ideal: 1920, max: 1920 },
                        height: { ideal: 1080, max: 1080 },
                        frameRate: { ideal: 30 }
                    },
                    audio: false
                };
                
                // Пытаемся получить доступ к тыловой камере
                try {
                    stream = await navigator.mediaDevices.getUserMedia(constraints);
                    console.log('Тыловая камера подключена');
                } catch (err) {
                    console.log('Тыловая камера недоступна, пробуем любую камеру:', err);
                    // Пробуем любую камеру
                    stream = await navigator.mediaDevices.getUserMedia({
                        video: true,
                        audio: false
                    });
                }
                
                const video = document.getElementById('cameraVideo');
                video.srcObject = stream;
                
                // Ждем загрузки видео
                await video.play();
                
                // Адаптируем видео для iOS
                if (isIOS) {
                    // Для iOS убираем трансформацию - она не нужна для тыловой камеры
                    video.style.transform = 'none';
                    video.style.webkitTransform = 'none';
                    console.log('iOS: трансформация убрана');
                }
                
                // Показываем модальное окно
                document.getElementById('cameraModal').style.display = 'block';
                document.getElementById('cameraError').style.display = 'none';
                
                // Запускаем сканирование
                startScanning();
                
            } catch (error) {
                console.error('Ошибка камеры:', error);
                showCameraError(error);
            }
        }
        
        // Запуск сканирования
        function startScanning() {
            if (!barcodeDetector) {
                console.warn('BarcodeDetector не инициализирован');
                return;
            }
            
            const video = document.getElementById('cameraVideo');
            
            // Очищаем предыдущий интервал
            if (scanInterval) clearInterval(scanInterval);
            
            // Создаем canvas для анализа
            const canvas = document.createElement('canvas');
            const context = canvas.getContext('2d');
            
            scanInterval = setInterval(async () => {
                try {
                    if (video.readyState !== video.HAVE_ENOUGH_DATA) return;
                    
                    // Устанавливаем размеры canvas
                    canvas.width = video.videoWidth;
                    canvas.height = video.videoHeight;
                    
                    // Рисуем кадр видео на canvas
                    context.drawImage(video, 0, 0, canvas.width, canvas.height);
                    
                    // Детектируем штрихкоды
                    const barcodes = await barcodeDetector.detect(canvas);
                    
                    if (barcodes.length > 0) {
                        const barcode = barcodes[0];
                        console.log('Найден штрихкод:', barcode.rawValue);
                        handleScannedBarcode(barcode.rawValue);
                    }
                    
                } catch (error) {
                    console.error('Ошибка сканирования:', error);
                }
            }, 500); // Проверяем каждые 500мс
        }
        
        // Обработка отсканированного штрихкода
        function handleScannedBarcode(barcode) {
            // Останавливаем сканирование
            stopCamera();
            
            // Заполняем поле ввода
            document.getElementById('searchInput').value = barcode;
            
            // Выполняем поиск
            performSearch();
        }
        
        // Поиск товара
        function performSearch() {
            const barcode = document.getElementById('searchInput').value.trim();
            if (!barcode) return;
            
            const results = searchProduct(barcode);
            displayResults(results, barcode);
        }
        
        // Поиск в данных
        function searchProduct(barcode) {
            const lines = productsData.split('\n');
            const results = [];
            
            for (const line of lines) {
                if (!line.trim()) continue;
                
                const parts = line.split(';');
                if (parts[0] === barcode) {
                    results.push({
                        barcode: parts[0],
                        article: parts[1],
                        name: parts[2],
                        price: parts[3]
                    });
                }
            }
            
            return results;
        }
        
        // Отображение результатов
        function displayResults(results, searchedBarcode) {
            const container = document.getElementById('resultsContainer');
            container.innerHTML = '';
            
            if (results.length === 0) {
                container.innerHTML = `
                    <div style="text-align: center; padding: 30px; color: #666;">
                        <div style="font-size: 48px; margin-bottom: 20px;">🔍</div>
                        <div style="font-size: 17px; margin-bottom: 10px;">
                            Товар со штрихкодом
                        </div>
                        <div style="font-size: 20px; font-weight: 600; color: #007aff;">
                            ${searchedBarcode}
                        </div>
                        <div style="font-size: 15px; margin-top: 10px;">
                            не найден в базе данных
                        </div>
                    </div>
                `;
            } else {
                results.forEach(product => {
                    const productDiv = document.createElement('div');
                    productDiv.className = 'product-card';
                    productDiv.innerHTML = `
                        <div class="product-article">${product.article}</div>
                        <div class="product-name">${product.name}</div>
                        <div class="product-price">${product.price} руб.</div>
                        <div style="font-size: 13px; color: #8e8e93; margin-top: 8px;">
                            Штрихкод: ${product.barcode}
                        </div>
                    `;
                    container.appendChild(productDiv);
                });
            }
            
            container.style.display = 'block';
            container.scrollIntoView({ behavior: 'smooth' });
        }
        
        // Остановка камеры
        function stopCamera() {
            if (scanInterval) {
                clearInterval(scanInterval);
                scanInterval = null;
            }
            
            if (stream) {
                stream.getTracks().forEach(track => track.stop());
                stream = null;
            }
            
            const video = document.getElementById('cameraVideo');
            video.srcObject = null;
            
            document.getElementById('cameraModal').style.display = 'none';
        }
        
        // Закрытие камеры
        function closeCamera() {
            stopCamera();
        }
        
        // Показать ошибку камеры
        function showCameraError(error) {
            const errorEl = document.getElementById('cameraError');
            let message = 'Ошибка доступа к камере';
            
            if (error.name === 'NotAllowedError') {
                message = 'Разрешите доступ к камере в настройках Safari';
            } else if (error.name === 'NotFoundError') {
                message = 'Камера не найдена';
            } else if (error.name === 'NotReadableError') {
                message = 'Камера используется другим приложением';
            } else {
                message = `Ошибка: ${error.message}`;
            }
            
            errorEl.textContent = message;
            errorEl.style.display = 'block';
        }
        
        // Автоматическая обработка штрихкодов при вставке
        document.getElementById('searchInput').addEventListener('paste', function(e) {
            setTimeout(() => {
                performSearch();
            }, 100);
        });
    </script>
</body>
</html>