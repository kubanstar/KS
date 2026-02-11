<html lang="ru">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, viewport-fit=cover">
    <title>Сканирование штрихкодов на iOS</title>
    <!-- Html5-QRCode библиотека (работает на iOS) -->
    <script src="https://unpkg.com/html5-qrcode"></script>
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
            background: #000;
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
        
        #qr-reader {
            width: 100%;
            height: 100%;
            position: relative;
        }
        
        /* Кастомные стили для Html5-QRCode */
        #html5-qrcode-anchor-scan-type-change,
        #html5qr-code-full-region__scan_region {
            display: none !important;
        }
        
        #qr-reader__scan_region {
            position: absolute;
            top: 50%;
            left: 50%;
            transform: translate(-50%, -50%);
            width: 80%;
            max-width: 400px;
            height: 200px;
            z-index: 10;
        }
        
        #qr-reader__scan_region img {
            display: none;
        }
        
        #qr-reader__scan_region hr {
            display: none;
        }
        
        /* Наш оверлей поверх сканера */
        .scan-overlay {
            position: absolute;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            pointer-events: none;
            z-index: 5;
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
        
        .scanner-info {
            position: absolute;
            top: calc(50% + 120px);
            left: 0;
            width: 100%;
            text-align: center;
            color: white;
            font-size: 16px;
            padding: 0 20px;
            z-index: 10;
        }
        
        .modal-controls {
            position: fixed;
            bottom: 0;
            left: 0;
            width: 100%;
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
        }
        
        .modal-btn-primary {
            background: rgba(255, 255, 255, 0.2);
            color: white;
        }
        
        .modal-btn-danger {
            background: rgba(255, 59, 48, 0.8);
            color: white;
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
            display: none;
        }
        
        @keyframes spin {
            100% { transform: translate(-50%, -50%) rotate(360deg); }
        }
        
        .ios-permission-hint {
            position: absolute;
            top: 50%;
            left: 50%;
            transform: translate(-50%, -50%);
            background: rgba(0,0,0,0.9);
            color: white;
            padding: 20px;
            border-radius: 15px;
            text-align: center;
            max-width: 300px;
            z-index: 1000;
            display: none;
        }
        
        /* Камера не поддерживается сообщение */
        .no-camera {
            position: absolute;
            top: 50%;
            left: 50%;
            transform: translate(-50%, -50%);
            background: rgba(0,0,0,0.9);
            color: white;
            padding: 30px;
            border-radius: 15px;
            text-align: center;
            max-width: 320px;
            z-index: 1000;
            display: none;
        }
        
        .no-camera h3 {
            margin-bottom: 15px;
            color: #ff3b30;
        }
        
        /* Результаты поиска */
        .results-container {
            background: white;
            border-radius: 20px;
            padding: 25px;
            margin-top: 20px;
            display: none;
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
            background: #f8f9fa;
            padding: 20px;
            border-radius: 15px;
            margin-bottom: 15px;
            border-left: 5px solid #667eea;
        }
        
        .product-article {
            font-size: 18px;
            font-weight: 700;
            color: #2d3436;
            margin-bottom: 10px;
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
            background: white;
            padding: 8px 12px;
            border-radius: 6px;
            margin-top: 10px;
        }
    </style>
</head>
<body>
    <div class="container">
        <div class="header">
            <h1>📦 Сканирование штрихкодов</h1>
            <p>Работает на iOS и Android</p>
        </div>
        
        <div class="card">
            <input type="text" 
                   class="search-input" 
                   id="searchInput" 
                   placeholder="Введите штрихкод вручную..."
                   style="width:100%; padding:16px; font-size:18px; border:2px solid #e0e0e0; border-radius:12px; margin-bottom:15px;">
            
            <div class="buttons">
                <button class="btn btn-primary" id="searchButton">
                    Поиск
                </button>
                <button class="btn btn-secondary" id="scanButton">
                    📷 Сканировать
                </button>
            </div>
        </div>
        
        <div class="results-container" id="resultsContainer">
            <div class="results-title">Результаты поиска</div>
            <div id="productsList"></div>
        </div>
    </div>
    
    <!-- Модальное окно сканирования -->
    <div class="modal" id="scannerModal">
        <div class="modal-content">
            <div class="scanner-container">
                <!-- Контейнер для Html5-QRCode -->
                <div id="qr-reader"></div>
                
                <!-- Наш оверлей -->
                <div class="scan-overlay">
                    <div class="scan-frame">
                        <div class="scan-line"></div>
                    </div>
                </div>
                
                <!-- Сообщения и статусы -->
                <div class="scanner-info">
                    Наведите камеру на штрихкод в рамке
                </div>
                
                <div class="status-message" id="scannerStatus"></div>
                <div class="scanned-badge" id="scannedBadge"></div>
                <div class="loader" id="scannerLoader">Загрузка...</div>
                
                <!-- Подсказка для iOS -->
                <div class="ios-permission-hint" id="iosPermissionHint">
                    📱 Для iOS:<br><br>
                    1. Разрешите доступ к камере<br>
                    2. Нажмите "Разрешить"<br>
                    3. Камера активируется автоматически
                </div>
                
                <!-- Сообщение если камера не поддерживается -->
                <div class="no-camera" id="noCameraMessage">
                    <h3>⚠️ Камера недоступна</h3>
                    <p>Ваш браузер не поддерживает доступ к камере или камера заблокирована.</p>
                    <p style="margin-top:15px; font-size:14px; color:#ccc;">
                        Используйте Safari на iOS или Chrome на Android
                    </p>
                </div>
            </div>
            
            <div class="modal-controls">
                <button class="modal-btn modal-btn-danger" id="closeScanner">
                    ✕ Закрыть сканер
                </button>
            </div>
        </div>
    </div>

    <script>
        // Определяем iOS
        const isIOS = /iPad|iPhone|iPod/.test(navigator.userAgent) && !window.MSStream;
        
        // Глобальные переменные
        let html5QrCode = null;
        let lastScannedCode = '';
        let isScanning = false;
        
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
            console.log('Страница загружена, iOS:', isIOS);
            
            // Назначаем обработчики
            document.getElementById('scanButton').addEventListener('click', openScanner);
            document.getElementById('closeScanner').addEventListener('click', closeScanner);
            document.getElementById('searchButton').addEventListener('click', performSearch);
            
            // Enter в поле поиска
            document.getElementById('searchInput').addEventListener('keypress', function(e) {
                if (e.key === 'Enter') performSearch();
            });
            
            // Для iOS добавляем специальную обработку
            if (isIOS) {
                console.log('Используется iOS, активируем специальные настройки');
                // Показываем подсказку при первом открытии
                localStorage.getItem('iosHintShown') || showIOSHint();
            }
        });
        
        // Показать подсказку для iOS
        function showIOSHint() {
            const hint = document.getElementById('iosPermissionHint');
            hint.style.display = 'block';
            setTimeout(() => {
                hint.style.display = 'none';
                localStorage.setItem('iosHintShown', 'true');
            }, 5000);
        }
        
        // Открыть сканер
        async function openScanner() {
            console.log('Открытие сканера...');
            
            // Показываем модальное окно
            const modal = document.getElementById('scannerModal');
            modal.style.display = 'block';
            
            // Показываем лоадер
            document.getElementById('scannerLoader').style.display = 'block';
            showScannerStatus('Инициализация камеры...');
            
            // Для iOS показываем подсказку
            if (isIOS) {
                setTimeout(() => {
                    document.getElementById('iosPermissionHint').style.display = 'block';
                }, 500);
            }
            
            // Ждем немного перед инициализацией
            setTimeout(() => {
                initBarcodeScanner();
            }, 300);
        }
        
        // Инициализация сканера штрихкодов
        function initBarcodeScanner() {
            try {
                // Останавливаем предыдущий сканер если есть
                if (html5QrCode && isScanning) {
                    html5QrCode.stop().then(() => {
                        html5QrCode.clear();
                        html5QrCode = null;
                    }).catch(() => {
                        html5QrCode = null;
                    });
                }
                
                // Создаем конфигурацию для сканера
                const config = {
                    fps: 10, // Частота кадров
                    qrbox: { 
                        width: 250, 
                        height: 150 
                    }, // Размер области сканирования
                    rememberLastUsedCamera: true,
                    supportedScanTypes: [Html5QrcodeScanType.SCAN_TYPE_CAMERA]
                };
                
                // Создаем экземпляр сканера
                html5QrCode = new Html5Qrcode("qr-reader");
                
                // Начинаем сканирование
                html5QrCode.start(
                    { 
                        facingMode: "environment" // Используем заднюю камеру
                    }, 
                    config,
                    onScanSuccess, // Функция при успешном сканировании
                    onScanError    // Функция при ошибке
                ).then(() => {
                    console.log('Сканирование запущено успешно');
                    isScanning = true;
                    
                    // Скрываем лоадер и сообщения
                    document.getElementById('scannerLoader').style.display = 'none';
                    document.getElementById('iosPermissionHint').style.display = 'none';
                    document.getElementById('noCameraMessage').style.display = 'none';
                    hideScannerStatus();
                    
                    // Скрываем подсказку для iOS
                    setTimeout(() => {
                        document.getElementById('iosPermissionHint').style.display = 'none';
                    }, 2000);
                    
                }).catch(err => {
                    console.error('Ошибка запуска сканера:', err);
                    
                    // Пробуем использовать фронтальную камеру как запасной вариант
                    if (err.toString().includes('environment')) {
                        console.log('Пробуем фронтальную камеру...');
                        showScannerStatus('Пробуем фронтальную камеру...');
                        
                        html5QrCode.start(
                            { facingMode: "user" }, 
                            config,
                            onScanSuccess,
                            onScanError
                        ).then(() => {
                            isScanning = true;
                            document.getElementById('scannerLoader').style.display = 'none';
                            hideScannerStatus();
                        }).catch(err2 => {
                            console.error('Ошибка с фронтальной камерой:', err2);
                            showNoCameraMessage();
                        });
                    } else {
                        showNoCameraMessage();
                    }
                });
                
            } catch (error) {
                console.error('Критическая ошибка инициализации:', error);
                showNoCameraMessage();
            }
        }
        
        // Обработка успешного сканирования
        function onScanSuccess(decodedText, decodedResult) {
            console.log('Сканирование успешно:', decodedText);
            
            // Проверяем чтобы не было дублирования
            if (lastScannedCode === decodedText) {
                return;
            }
            
            // Запоминаем последний отсканированный код
            lastScannedCode = decodedText;
            
            // Показываем визуальную обратную связь
            showScannedBadge(decodedText);
            
            // Останавливаем сканирование
            if (html5QrCode && isScanning) {
                html5QrCode.stop().then(() => {
                    isScanning = false;
                }).catch(() => {
                    isScanning = false;
                });
            }
            
            // Закрываем сканер через 1.5 секунды
            setTimeout(() => {
                closeScanner();
                
                // Заполняем поле ввода
                document.getElementById('searchInput').value = decodedText;
                
                // Выполняем поиск
                performSearch();
                
                // Очищаем последний код через 3 секунды
                setTimeout(() => {
                    lastScannedCode = '';
                }, 3000);
                
            }, 1500);
        }
        
        // Обработка ошибок сканирования
        function onScanError(error) {
            // Игнорируем обычные ошибки сканирования (когда нет кода в кадре)
            if (!error.includes('NotFoundException') && !error.includes('No multi format readers configured')) {
                console.warn('Ошибка сканирования:', error);
            }
        }
        
        // Показать сообщение об отсутствии камеры
        function showNoCameraMessage() {
            document.getElementById('scannerLoader').style.display = 'none';
            document.getElementById('noCameraMessage').style.display = 'block';
            document.getElementById('iosPermissionHint').style.display = 'none';
            hideScannerStatus();
        }
        
        // Закрыть сканер
        function closeScanner() {
            console.log('Закрытие сканера...');
            
            // Останавливаем сканирование
            if (html5QrCode && isScanning) {
                html5QrCode.stop().then(() => {
                    console.log('Сканирование остановлено');
                    html5QrCode.clear();
                    html5QrCode = null;
                    isScanning = false;
                }).catch(err => {
                    console.log('Ошибка остановки:', err);
                    html5QrCode = null;
                    isScanning = false;
                });
            }
            
            // Скрываем модальное окно
            document.getElementById('scannerModal').style.display = 'none';
            document.getElementById('scannedBadge').style.display = 'none';
            document.getElementById('noCameraMessage').style.display = 'none';
            document.getElementById('iosPermissionHint').style.display = 'none';
            hideScannerStatus();
        }
        
        // Показать бейдж сканирования
        function showScannedBadge(code) {
            const badge = document.getElementById('scannedBadge');
            badge.textContent = `✓ Найдено: ${code}`;
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
        
        // Поиск товара
        function performSearch() {
            const searchValue = document.getElementById('searchInput').value.trim();
            if (!searchValue) {
                alert('Введите штрихкод для поиска');
                return;
            }
            
            // Показываем контейнер результатов
            const resultsContainer = document.getElementById('resultsContainer');
            resultsContainer.style.display = 'block';
            
            // Очищаем предыдущие результаты
            const productsList = document.getElementById('productsList');
            productsList.innerHTML = '';
            
            // Ищем товар
            const product = productsData[searchValue];
            
            if (product) {
                // Отображаем найденный товар
                const productCard = document.createElement('div');
                productCard.className = 'product-card';
                productCard.innerHTML = `
                    <div class="product-article">${product.article}</div>
                    <div class="product-name">${product.name}</div>
                    <div class="product-price">${product.price} руб.</div>
                    <div class="product-barcode">Штрихкод: ${searchValue}</div>
                `;
                
                productsList.appendChild(productCard);
            } else {
                // Товар не найден
                productsList.innerHTML = `
                    <div style="text-align: center; padding: 40px 20px; color: #666;">
                        <div style="font-size: 60px; margin-bottom: 20px; opacity: 0.5;">🔍</div>
                        <div style="font-size: 18px; margin-bottom: 10px; color: #333;">
                            Товар не найден
                        </div>
                        <div style="font-size: 15px; color: #666;">
                            Штрихкод: <strong>${searchValue}</strong>
                        </div>
                    </div>
                `;
            }
            
            // Прокручиваем к результатам
            resultsContainer.scrollIntoView({ behavior: 'smooth' });
        }
        
        // Обработчик выхода из полноэкранного режима
        document.addEventListener('visibilitychange', function() {
            if (document.hidden && isScanning) {
                // Если пользователь переключил вкладку, останавливаем сканирование
                closeScanner();
            }
        });
        
        // Обработчик изменения ориентации
        window.addEventListener('orientationchange', function() {
            if (isScanning) {
                // Перезапускаем сканер при изменении ориентации
                setTimeout(() => {
                    if (isScanning) {
                        closeScanner();
                        setTimeout(openScanner, 500);
                    }
                }, 300);
            }
        });
    </script>
</body>
</html>