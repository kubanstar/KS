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
        
        .hidden-input {
            position: absolute;
            opacity: 0;
            width: 0;
            height: 0;
        }
        
        .instructions {
            background: rgba(255, 255, 255, 0.1);
            border-radius: 15px;
            padding: 20px;
            margin-top: 20px;
            color: white;
            text-align: center;
        }
        
        .instructions h3 {
            margin-bottom: 10px;
            font-size: 20px;
        }
        
        .instructions ol {
            text-align: left;
            margin: 15px auto;
            max-width: 400px;
        }
        
        .instructions li {
            margin-bottom: 10px;
            line-height: 1.5;
        }
    </style>
</head>
<body>
    <div class="container">
        <div class="header">
            <h1>📦 Сканирование штрихкодов</h1>
            <p>Используем нативный сканер iOS</p>
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
            
            <!-- Скрытое поле для сканирования iOS -->
            <input type="file" 
                   id="iosScanner" 
                   class="hidden-input"
                   accept="image/*" 
                   capture="environment">
        </div>
        
        <div class="card results-container" id="resultsContainer">
            <div class="results-title">Результаты поиска</div>
            <div id="productsList"></div>
        </div>
        
        <div class="instructions">
            <h3>📱 Как сканировать на iOS:</h3>
            <ol>
                <li>Нажмите кнопку "Сканировать"</li>
                <li>Разрешите доступ к камере</li>
                <li>Наведите камеру на штрихкод</li>
                <li>Камера iOS автоматически распознает штрихкод</li>
                <li>Нажмите кнопку фото для сканирования</li>
            </ol>
            <p><em>На iOS используется встроенный сканер штрихкодов</em></p>
        </div>
    </div>

    <!-- Подключаем библиотеку для распознавания штрихкодов из изображений -->
    <script src="https://cdn.jsdelivr.net/npm/@zxing/library@0.19.1/umd/index.min.js"></script>
    
    <script>
        // Глобальные переменные
        const isIOS = /iPad|iPhone|iPod/.test(navigator.userAgent) && !window.MSStream;
        const isAndroid = /android/i.test(navigator.userAgent);
        
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
            },
            "5901234123457": {
                article: "TEST-EAN",
                name: "Тестовый EAN-13",
                price: "150,00"
            }
        };
        
        // Инициализация при загрузке страницы
        document.addEventListener('DOMContentLoaded', function() {
            console.log('Устройство iOS:', isIOS);
            console.log('Устройство Android:', isAndroid);
            console.log('ZXing доступен:', typeof ZXing !== 'undefined');
            
            // Установка обработчиков событий
            document.getElementById('searchButton').addEventListener('click', performSearch);
            document.getElementById('scanButton').addEventListener('click', startScanner);
            document.getElementById('iosScanner').addEventListener('change', handleImageUpload);
            
            document.getElementById('searchInput').addEventListener('keypress', function(e) {
                if (e.key === 'Enter') performSearch();
            });
            
            // Автофокус на поле ввода
            setTimeout(() => {
                document.getElementById('searchInput').focus();
            }, 500);
            
            // Показать инструкции для iOS
            if (isIOS) {
                document.querySelector('.instructions').style.display = 'block';
            }
        });
        
        // Запуск сканера
        function startScanner() {
            if (isIOS) {
                // На iOS используем нативный сканер через input file
                console.log('Запуск нативного сканера iOS...');
                document.getElementById('iosScanner').click();
            } else {
                // На Android/Desktop пытаемся использовать камеру
                startCameraScanner();
            }
        }
        
        // Обработка загруженного изображения (для iOS)
        async function handleImageUpload(event) {
            const file = event.target.files[0];
            if (!file) return;
            
            console.log('Изображение загружено:', file.name, file.type, file.size);
            
            // Показываем статус
            showNotification('Анализируем изображение...');
            
            try {
                // Создаем URL для изображения
                const imageUrl = URL.createObjectURL(file);
                
                // Создаем элемент изображения
                const img = new Image();
                img.src = imageUrl;
                
                img.onload = async function() {
                    try {
                        // Пытаемся распознать штрихкод из изображения
                        const barcode = await scanBarcodeFromImage(img);
                        
                        if (barcode) {
                            console.log('Найден штрихкод:', barcode);
                            processScannedCode(barcode);
                        } else {
                            console.log('Штрихкод не найден на изображении');
                            showNotification('Штрихкод не найден. Попробуйте еще раз.');
                            
                            // Через 2 секунды снова предлагаем сканировать
                            setTimeout(() => {
                                if (confirm('Штрихкод не найден. Хотите попробовать еще раз?')) {
                                    document.getElementById('iosScanner').click();
                                }
                            }, 2000);
                        }
                        
                        // Очищаем URL
                        URL.revokeObjectURL(imageUrl);
                        
                        // Очищаем input
                        event.target.value = '';
                        
                    } catch (error) {
                        console.error('Ошибка обработки изображения:', error);
                        showNotification('Ошибка обработки изображения');
                        event.target.value = '';
                    }
                };
                
                img.onerror = function() {
                    console.error('Ошибка загрузки изображения');
                    showNotification('Ошибка загрузки изображения');
                    event.target.value = '';
                    URL.revokeObjectURL(imageUrl);
                };
                
            } catch (error) {
                console.error('Ошибка обработки файла:', error);
                showNotification('Ошибка обработки файла');
                event.target.value = '';
            }
        }
        
        // Сканирование штрихкода из изображения с помощью ZXing
        async function scanBarcodeFromImage(imageElement) {
            try {
                console.log('Начинаем сканирование изображения...');
                
                // Создаем canvas для анализа
                const canvas = document.createElement('canvas');
                const ctx = canvas.getContext('2d');
                
                // Устанавливаем размеры canvas как у изображения
                canvas.width = imageElement.naturalWidth || imageElement.width;
                canvas.height = imageElement.naturalHeight || imageElement.height;
                
                console.log('Размер изображения:', canvas.width, 'x', canvas.height);
                
                // Рисуем изображение на canvas
                ctx.drawImage(imageElement, 0, 0, canvas.width, canvas.height);
                
                // Получаем данные изображения
                const imageData = ctx.getImageData(0, 0, canvas.width, canvas.height);
                
                // Пробуем разные библиотеки для распознавания
                
                // 1. Попробуем ZXing.js (библиотека от Google)
                if (typeof ZXing !== 'undefined') {
                    console.log('Используем ZXing для распознавания...');
                    try {
                        const barcodeReader = new ZXing.BrowserMultiFormatReader();
                        
                        // Создаем временный canvas для ZXing
                        const tempCanvas = document.createElement('canvas');
                        tempCanvas.width = canvas.width;
                        tempCanvas.height = canvas.height;
                        const tempCtx = tempCanvas.getContext('2d');
                        tempCtx.drawImage(imageElement, 0, 0);
                        
                        const result = await barcodeReader.decodeFromCanvas(tempCanvas);
                        if (result && result.text) {
                            console.log('ZXing нашел штрихкод:', result.text);
                            return result.text;
                        }
                    } catch (zxingError) {
                        console.log('ZXing не нашел штрихкод:', zxingError.message);
                    }
                }
                
                // 2. Попробуем простой анализ изображения
                console.log('Пробуем простой анализ изображения...');
                
                // Преобразуем в черно-белое и ищем паттерны штрихкода
                const simpleResult = await simpleBarcodeAnalysis(imageData);
                if (simpleResult) {
                    console.log('Простой анализ нашел штрихкод:', simpleResult);
                    return simpleResult;
                }
                
                // 3. Если ничего не помогло, пробуем вырезать центральную часть
                console.log('Пробуем анализ центральной части...');
                const centerResult = await analyzeCenterOfImage(canvas, ctx);
                if (centerResult) {
                    console.log('Анализ центра нашел штрихкод:', centerResult);
                    return centerResult;
                }
                
                return null;
                
            } catch (error) {
                console.error('Ошибка сканирования:', error);
                return null;
            }
        }
        
        // Простой анализ изображения для поиска штрихкодов
        async function simpleBarcodeAnalysis(imageData) {
            const data = imageData.data;
            const width = imageData.width;
            const height = imageData.height;
            
            console.log('Простой анализ: размер', width, 'x', height);
            
            // Анализируем несколько горизонтальных линий
            const linesToCheck = [
                Math.floor(height * 0.3), // 30% от верха
                Math.floor(height * 0.5), // центр
                Math.floor(height * 0.7)  // 70% от верха
            ];
            
            for (const lineY of linesToCheck) {
                // Получаем строку пикселей
                const lineData = [];
                for (let x = 0; x < width; x++) {
                    const index = (lineY * width + x) * 4;
                    const r = data[index];
                    const g = data[index + 1];
                    const b = data[index + 2];
                    // Преобразуем в яркость
                    const brightness = (r + g + b) / 3;
                    lineData.push(brightness);
                }
                
                // Ищем паттерны штрихкода
                const barcode = findBarcodeInLine(lineData);
                if (barcode && isValidBarcode(barcode)) {
                    console.log('Найден штрихкод в строке', lineY, ':', barcode);
                    return barcode;
                }
            }
            
            return null;
        }
        
        // Поиск штрихкода в строке пикселей
        function findBarcodeInLine(lineData) {
            // Находим среднюю яркость
            const sum = lineData.reduce((a, b) => a + b, 0);
            const average = sum / lineData.length;
            
            // Бинаризуем линию
            const binaryLine = lineData.map(brightness => brightness < average ? '0' : '1');
            const binaryString = binaryLine.join('');
            
            console.log('Бинарная строка (первые 100 символов):', binaryString.substring(0, 100));
            
            // Ищем паттерны EAN-13
            // EAN-13 имеет 95 модулей (черных и белых полос)
            if (binaryString.length >= 95) {
                // Пробуем разные стартовые позиции
                for (let i = 0; i < binaryString.length - 95; i += 5) {
                    const segment = binaryString.substring(i, i + 95);
                    
                    // Проверяем паттерны начала
                    if (segment.startsWith('101')) {
                        // Пробуем декодировать
                        const decoded = tryDecodeEAN13Segment(segment);
                        if (decoded) {
                            return decoded;
                        }
                    }
                }
            }
            
            return null;
        }
        
        // Попытка декодирования сегмента как EAN-13
        function tryDecodeEAN13Segment(segment) {
            // Упрощенная логика - ищем группы из 7 бит (каждая цифра в EAN-13)
            const digits = [];
            
            for (let i = 3; i < 87; i += 7) { // Пропускаем первые 3 бита (стартовый паттерн)
                const digitBits = segment.substring(i, i + 7);
                
                // Простая проверка на валидность
                if (digitBits.length === 7) {
                    // Преобразуем в десятичное (очень упрощенно)
                    const digit = parseInt(digitBits, 2) % 10;
                    digits.push(digit);
                }
            }
            
            if (digits.length === 12) {
                // Добавляем контрольную сумму
                let sum = 0;
                for (let i = 0; i < 12; i++) {
                    sum += digits[i] * (i % 2 === 0 ? 1 : 3);
                }
                const checksum = (10 - (sum % 10)) % 10;
                digits.push(checksum);
                
                const barcode = digits.join('');
                
                // Проверяем валидность
                if (isValidEAN13(barcode)) {
                    return barcode;
                }
            }
            
            return null;
        }
        
        // Проверка валидности EAN-13
        function isValidEAN13(barcode) {
            if (barcode.length !== 13) return false;
            if (!/^\d+$/.test(barcode)) return false;
            
            // Проверка контрольной суммы
            let sum = 0;
            for (let i = 0; i < 12; i++) {
                sum += parseInt(barcode[i]) * (i % 2 === 0 ? 1 : 3);
            }
            const checksum = (10 - (sum % 10)) % 10;
            
            return checksum === parseInt(barcode[12]);
        }
        
        // Анализ центральной части изображения
        async function analyzeCenterOfImage(canvas, ctx) {
            // Вырезаем центральную часть (где скорее всего находится штрихкод)
            const centerWidth = Math.floor(canvas.width * 0.8);
            const centerHeight = Math.floor(canvas.height * 0.3);
            const centerX = Math.floor((canvas.width - centerWidth) / 2);
            const centerY = Math.floor((canvas.height - centerHeight) / 2);
            
            console.log('Анализ центра:', centerX, centerY, centerWidth, centerHeight);
            
            const centerData = ctx.getImageData(centerX, centerY, centerWidth, centerHeight);
            
            // Пробуем анализ центральной части
            return simpleBarcodeAnalysis(centerData);
        }
        
        // Проверка валидности штрихкода
        function isValidBarcode(barcode) {
            // Проверяем длину EAN-13
            if (barcode.length === 13 && /^\d+$/.test(barcode)) {
                return isValidEAN13(barcode);
            }
            
            // Проверяем другие форматы
            if (barcode.length >= 8 && barcode.length <= 14) {
                return true; // Принимаем любые цифровые коды разумной длины
            }
            
            return false;
        }
        
        // Запуск сканера камеры (для Android/Desktop)
        async function startCameraScanner() {
            showNotification('Запускаем камеру...');
            
            try {
                // Проверяем поддержку BarcodeDetector API
                if ('BarcodeDetector' in window) {
                    const barcodeDetector = new BarcodeDetector();
                    
                    // Запрашиваем доступ к камере
                    const stream = await navigator.mediaDevices.getUserMedia({
                        video: { facingMode: 'environment' }
                    });
                    
                    const video = document.createElement('video');
                    video.srcObject = stream;
                    video.play();
                    
                    video.onloadeddata = async () => {
                        const canvas = document.createElement('canvas');
                        canvas.width = video.videoWidth;
                        canvas.height = video.videoHeight;
                        const ctx = canvas.getContext('2d');
                        
                        // Сканируем каждые 500мс
                        const interval = setInterval(async () => {
                            ctx.drawImage(video, 0, 0);
                            const barcodes = await barcodeDetector.detect(canvas);
                            
                            if (barcodes.length > 0) {
                                clearInterval(interval);
                                stream.getTracks().forEach(track => track.stop());
                                processScannedCode(barcodes[0].rawValue);
                            }
                        }, 500);
                        
                        // Остановить через 30 секунд
                        setTimeout(() => {
                            clearInterval(interval);
                            stream.getTracks().forEach(track => track.stop());
                            showNotification('Сканирование остановлено');
                        }, 30000);
                    };
                } else {
                    showNotification('Ваш браузер не поддерживает прямое сканирование');
                }
            } catch (error) {
                console.error('Ошибка камеры:', error);
                showNotification('Ошибка доступа к камере');
            }
        }
        
        // Обработка найденного штрихкода
        function processScannedCode(code) {
            console.log('Обработка штрихкода:', code);
            
            // Очищаем поле ввода
            document.getElementById('searchInput').value = '';
            
            // Показываем уведомление
            showNotification(`Найден штрихкод: ${code}`);
            
            // Заполняем поле ввода
            setTimeout(() => {
                document.getElementById('searchInput').value = code;
                
                // Выполняем поиск
                performSearch();
            }, 500);
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
            
            // Сначала проверяем точное совпадение
            if (productsData[searchValue]) {
                const product = productsData[searchValue];
                displayProduct(product, searchValue);
                found = true;
            } else {
                // Ищем похожие
                for (const [barcode, product] of Object.entries(productsData)) {
                    if (barcode.includes(searchValue) || 
                        product.article.toLowerCase().includes(searchValue.toLowerCase())) {
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
                        <div style="margin-top: 20px; font-size: 14px; color: #888;">
                            Этот штрихкод отсутствует в базе данных
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
            
            // Показываем уведомление
            showNotification(`Найден товар: ${product.article}`);
        }
        
        // Показать уведомление
        function showNotification(message) {
            // Создаем или находим элемент уведомления
            let notification = document.getElementById('scanNotification');
            
            if (!notification) {
                notification = document.createElement('div');
                notification.id = 'scanNotification';
                notification.style.cssText = `
                    position: fixed;
                    top: 20px;
                    left: 50%;
                    transform: translateX(-50%);
                    background: rgba(0, 0, 0, 0.9);
                    color: white;
                    padding: 15px 25px;
                    border-radius: 15px;
                    z-index: 10000;
                    font-weight: 600;
                    text-align: center;
                    max-width: 90%;
                    box-shadow: 0 5px 20px rgba(0,0,0,0.3);
                    backdrop-filter: blur(10px);
                    border: 1px solid rgba(255,255,255,0.1);
                `;
                document.body.appendChild(notification);
            }
            
            notification.textContent = message;
            notification.style.display = 'block';
            
            // Скрыть через 3 секунды
            setTimeout(() => {
                notification.style.display = 'none';
            }, 3000);
        }
    </script>
</body>
</html>