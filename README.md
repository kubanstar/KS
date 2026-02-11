<html lang="ru">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Поиск товаров</title>
    <script src="https://unpkg.com/html5-qrcode"></script>
    <style>
        body {
            font-family: Arial, sans-serif;
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
        
        .combined-search-fields {
            display: none;
            gap: 10px;
            margin: 15px 0;
            flex-wrap: wrap;
        }
        
        .combined-field {
            flex: 1;
            min-width: 200px;
        }
        
        .combined-field label {
            display: block;
            text-align: left;
            margin-bottom: 5px;
            font-size: 14px;
            color: #333;
            font-weight: bold;
        }
        
        .combined-input {
            width: 100%;
            padding: 12px;
            font-size: 14px;
            border: 2px solid #ddd;
            border-radius: 5px;
            box-sizing: border-box;
        }
        
        .combined-input:focus {
            border-color: #4CAF50;
            outline: none;
        }
        
        .search-input-wrapper {
            position: relative;
            width: 100%;
            margin-bottom: 10px;
        }
        
        .search-input {
            width: 100%;
            padding: 15px 40px 15px 15px;
            font-size: 16px;
            border: 2px solid #ddd;
            border-radius: 5px;
            box-sizing: border-box;
        }
        
        .search-input:focus {
            border-color: #4CAF50;
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
        
        .clear-search-btn:hover {
            color: #ff4444;
        }
        
        .buttons-container {
            display: flex;
            gap: 10px;
            margin-bottom: 10px;
            flex-wrap: wrap;
            justify-content: center;
        }
        
        .search-button {
            background-color: #4CAF50;
            color: white;
            border: none;
            padding: 15px 30px;
            font-size: 16px;
            border-radius: 5px;
            cursor: pointer;
            transition: background-color 0.3s;
            flex-grow: 1;
            max-width: 200px;
        }
        
        .scan-button {
            background-color: #2196F3;
            color: white;
            border: none;
            padding: 15px 30px;
            font-size: 16px;
            border-radius: 5px;
            cursor: pointer;
            transition: background-color 0.3s;
            display: flex;
            align-items: center;
            justify-content: center;
            gap: 8px;
            flex-grow: 1;
            max-width: 200px;
        }
        
        .search-button:hover {
            background-color: #45a049;
        }
        
        .scan-button:hover {
            background-color: #0b7dda;
        }
        
        .search-button:active {
            background-color: #3d8b40;
        }
        
        .scan-button:active {
            background-color: #0a6ebd;
        }
        
        .scan-icon {
            font-size: 18px;
        }
        
        .search-mode-selector {
            display: flex;
            justify-content: center;
            gap: 20px;
            margin: 15px 0;
            flex-wrap: wrap;
        }
        
        .mode-option {
            display: flex;
            align-items: center;
            cursor: pointer;
        }
        
        .mode-radio {
            margin-right: 8px;
            cursor: pointer;
        }
        
        .mode-label {
            font-size: 14px;
            color: #333;
            cursor: pointer;
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
            border-left: 4px solid #4CAF50;
        }
        
        .product-field {
            margin-bottom: 5px;
        }
        
        .barcode {
            color: #666;
            font-size: 14px;
        }
        
        .multiple-barcodes {
            color: #2196F3;
            cursor: pointer;
            text-decoration: underline;
            font-weight: bold;
        }
        
        .barcode-tooltip {
            display: none;
            position: absolute;
            background: white;
            border: 1px solid #ddd;
            border-radius: 5px;
            padding: 10px;
            box-shadow: 0 2px 10px rgba(0,0,0,0.1);
            z-index: 100;
            max-width: 300px;
            font-size: 12px;
        }
        
        .barcode-list {
            max-height: 150px;
            overflow-y: auto;
        }
        
        .barcode-item {
            padding: 2px 0;
            font-family: monospace;
            border-bottom: 1px solid #f0f0f0;
        }
        
        .article {
            font-weight: bold;
            color: #333;
            display: flex;
            align-items: center;
        }
        
        .name {
            font-size: 16px;
            color: #222;
            margin: 10px 0;
        }
        
        .price-container {
            display: flex;
            flex-direction: column;
            align-items: center;
            text-align: center;
        }

        .old-price-container {
            display: flex;
            gap: 8px;
            margin-top: 4px;
            text-align: center;
            justify-content: center;
            position: relative;
            left: 43px;
        }
 
        .scan-old-price-container {
            display: flex;
            gap: 8px;
            margin-top: 4px;
            text-align: center;
            justify-content: center;
            position: relative;
            left: 43px;
        }
 
        .discount-price {
            color: #e74c3c;
            font-weight: bold;
            font-size: 20px;
            display: block;
            position: relative;
            text-align: center;
        }
        
        .original-price {
            color: #333;
            font-weight: bold;
            font-size: 16px;
            text-decoration: line-through;
        }
        
        .discount-percent {
            color: #333;
            font-weight: bold;			
            font-size: 16px;
        }
        
        .stock-info {
            margin-top: 15px;
            padding: 10px;
            background-color: #f8f9fa;
            border-radius: 5px;
            border-left: 4px solid #2196F3;
        }
        
        .stock-title {
            font-weight: bold;
            color: #333;
            margin-bottom: 8px;
            font-size: 14px;
        }
        
        .stock-item {
            display: flex;
            justify-content: space-between;
            padding: 3px 0;
            font-size: 13px;
            border-bottom: 1px solid #eee;
        }
        
        .stock-name {
            color: #555;
        }
        
        .stock-quantity {
            font-weight: bold;
            text-align: right;
        }
        
        .stock-quantity.positive {
            color: #2e7d32;
        }
        
        .stock-quantity.negative {
            color: #f44336;
        }
        
        .box-coefficient {
            color: #666;
            font-size: 11px;
            margin-left: 5px;
        }
        
        .box-quantity-info {
            margin-top: 10px;
            padding: 8px;
            background-color: #e8f5e9;
            border-radius: 5px;
            border-left: 4px solid #4CAF50;
            font-size: 13px;
        }
        
        .box-quantity-title {
            font-weight: bold;
            color: #2e7d32;
            margin-bottom: 3px;
        }
        
        .box-quantity-value {
            color: #333;
            font-weight: bold;
        }
        
        .storage-location {
            margin-top: 10px;
            padding: 8px;
            background-color: #fff3cd;
            border-radius: 5px;
            border-left: 4px solid #ffc107;
            font-size: 13px;
        }
        
        .storage-title {
            font-weight: bold;
            color: #856404;
            margin-bottom: 3px;
        }
        
        .storage-value {
            color: #333;
            font-weight: bold;
        }
        
        .no-results {
            text-align: center;
            padding: 20px;
            color: #666;
            font-style: italic;
        }
        
        .results-count {
            color: #666;
            font-size: 14px;
            margin-bottom: 10px;
            text-align: left;
        }
        
        .search-mode {
            color: #4CAF50;
            font-size: 12px;
            margin-top: 5px;
            text-align: left;
        }
        
        .print-button {
            background: none;
            border: none;
            cursor: pointer;
            padding: 5px;
            border-radius: 50%;
            transition: all 0.3s;
            width: 42px;
            height: 42px;
            display: flex;
            align-items: center;
            justify-content: center;
            font-size: 24px;
            margin-left: auto;
        }

        .print-button:hover {
            background-color: #f0f0f0;
            transform: scale(1.1);
        }

        .image-button {
            background: none;
            border: none;
            cursor: pointer;
            padding: 5px;
            margin-left: 10px;
            border-radius: 50%;
            transition: all 0.3s;
            width: 42px;
            height: 42px;
            display: inline-flex;
            align-items: center;
            justify-content: center;
            font-size: 24px;
        }

        .image-button:hover {
            background-color: #f0f0f0;
            transform: scale(1.1);
        }

        .no-image-text {
            color: #999;
            font-size: 12px;
            margin-left: 10px;
            font-style: italic;
        }
        
        .scroll-to-top-btn {
            position: fixed;
            bottom: 30px;
            right: 30px;
            width: 60px;
            height: 60px;
            border-radius: 50%;
            background-color: #4CAF50;
            color: white;
            border: none;
            cursor: pointer;
            display: none;
            justify-content: center;
            align-items: center;
            font-size: 32px;
            box-shadow: 0 4px 15px rgba(0,0,0,0.2);
            transition: all 0.3s;
            z-index: 1000;
        }
        
        .scroll-to-top-btn:hover {
            background-color: #45a049;
            transform: translateY(-3px);
            box-shadow: 0 6px 20px rgba(0,0,0,0.25);
        }
        
        .scroll-to-top-btn:active {
            transform: translateY(-1px);
        }
        
        .scroll-to-top-btn.show {
            display: flex;
        }
        
        .print-modal-new {
            position: fixed;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            background-color: rgba(0, 0, 0, 0.8);
            display: none;
            justify-content: center;
            align-items: center;
            z-index: 2000;
        }
        
        .print-modal-content-new {
            background: white;
            padding: 25px;
            border-radius: 15px;
            text-align: center;
            max-width: 450px;
            width: 90%;
            box-shadow: 0 10px 30px rgba(0,0,0,0.3);
        }
        
        .print-status {
            margin-top: 15px;
            padding: 10px;
            border-radius: 5px;
            display: none;
        }
        
        .print-status.success {
            background-color: #d4edda;
            color: #155724;
            border: 1px solid #c3e6cb;
        }
        
        .print-status.error {
            background-color: #f8d7da;
            color: #721c24;
            border: 1px solid #f5c6cb;
        }
        
        .print-status.info {
            background-color: #d1ecf1;
            color: #0c5460;
            border: 1px solid #bee5eb;
        }
        
        .price-tag-preview {
            margin: 20px 0;
            border: 2px dashed #ddd;
            border-radius: 8px;
            padding: 15px;
            background: white;
            position: relative;
            overflow: hidden;
            min-height: 200px;
        }
        
        .price-tag-canvas {
            width: 100%;
            max-width: 400px;
            margin: 0 auto;
            border: 1px solid #eee;
            border-radius: 5px;
            background: white;
        }
        
        .print-action-btn {
            background-color: #28a745;
            color: white;
            border: none;
            padding: 12px 25px;
            font-size: 16px;
            font-weight: bold;
            border-radius: 8px;
            cursor: pointer;
            transition: all 0.3s;
            margin-top: 15px;
            width: 100%;
            max-width: 250px;
        }
        
        .print-action-btn:hover {
            background-color: #218838;
            transform: translateY(-2px);
        }
        
        .print-action-btn:disabled {
            background-color: #6c757d;
            cursor: not-allowed;
            transform: none;
        }
        
        .printer-status {
            padding: 8px 12px;
            border-radius: 5px;
            margin-bottom: 15px;
            font-size: 14px;
            font-weight: bold;
        }
        
        .printer-connected {
            background-color: #d4edda;
            color: #155724;
            border: 1px solid #c3e6cb;
        }
        
        .printer-disconnected {
            background-color: #f8d7da;
            color: #721c24;
            border: 1px solid #f5c6cb;
        }
        
        .printer-connecting {
            background-color: #fff3cd;
            color: #856404;
            border: 1px solid #ffeaa7;
        }
        
        .price-tag-type-selector {
            margin: 20px 0;
            display: flex;
            gap: 20px;
            justify-content: center;
            align-items: center;
        }
        
        .price-tag-type-option {
            display: flex;
            align-items: center;
            cursor: pointer;
            padding: 8px 16px;
            border-radius: 5px;
            border: 2px solid #ddd;
            background: white;
            transition: all 0.3s;
        }
        
        .price-tag-type-option:hover {
            border-color: #4CAF50;
            background-color: #f9f9f9;
        }
        
        .price-tag-type-option.selected {
            border-color: #4CAF50;
            background-color: #e8f5e9;
        }
        
        .price-tag-type-radio {
            margin-right: 8px;
            cursor: pointer;
        }
        
        .price-tag-type-label {
            font-size: 14px;
            font-weight: bold;
            color: #333;
            cursor: pointer;
        }

        /* Стили для модального окна iOS сканирования */
        .ios-scanner-modal {
            display: none;
            position: fixed;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            background: #000;
            z-index: 1000;
        }
        
        .ios-scanner-content {
            width: 100%;
            height: 100%;
            position: relative;
            display: flex;
            flex-direction: column;
        }
        
        .ios-scanner-container {
            flex: 1;
            position: relative;
            overflow: hidden;
        }
        
        #ios-qr-reader {
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
        
        /* Наш оверлей поверх сканера iOS */
        .ios-scan-overlay {
            position: absolute;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            pointer-events: none;
            z-index: 5;
        }
        
        .ios-scan-frame {
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
        
        .ios-scan-line {
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
        
        .ios-scanner-info {
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
        
        .ios-modal-controls {
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
        
        .ios-modal-btn {
            flex: 1;
            padding: 16px;
            border: none;
            border-radius: 12px;
            font-size: 17px;
            font-weight: 600;
            cursor: pointer;
            transition: all 0.3s;
        }
        
        .ios-modal-btn-danger {
            background: rgba(255, 59, 48, 0.8);
            color: white;
        }
        
        .ios-scanner-status {
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
        
        .ios-scanned-badge {
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
        
        .ios-loader {
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
        
        /* Обычное модальное окно камеры для Android/Desktop */
        .modal-overlay {
            position: fixed;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            background-color: rgba(0, 0, 0, 0.8);
            display: none;
            justify-content: center;
            align-items: center;
            z-index: 1000;
        }
        
        .modal-frame {
            background: white;
            padding: 30px;
            border-radius: 15px;
            text-align: center;
            max-width: 500px;
            width: 90%;
            max-height: 80vh;
            overflow-y: auto;
            box-shadow: 0 10px 30px rgba(0,0,0,0.3);
        }
        
        .video-wrapper {
            position: relative;
            width: 100%;
            max-width: 400px;
            margin: 20px auto;
        }
        
        #cameraVideo {
            width: 100%;
            height: auto;
            border-radius: 8px;
        }
        
        .camera-controls {
            margin-top: 10px;
            display: flex;
            gap: 10px;
            justify-content: center;
            flex-wrap: wrap;
        }
        
        .camera-btn {
            padding: 10px 15px;
            border: none;
            border-radius: 5px;
            cursor: pointer;
            font-size: 14px;
            background-color: #2196F3;
            color: white;
            flex: 1;
            min-width: 120px;
        }
        
        .close-modal {
            background-color: #f44336;
            color: white;
            border: none;
            padding: 10px 20px;
            border-radius: 5px;
            cursor: pointer;
            margin-top: 10px;
        }
        
        .scan-box {
            position: absolute;
            top: 50%;
            left: 50%;
            transform: translate(-50%, -50%);
            width: 80%;
            height: 150px;
            border: 3px solid #4CAF50;
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
            background-color: #4CAF50;
            animation: scan 2s linear infinite;
        }
        
        .barcode-supported {
            margin-top: 10px;
            font-size: 12px;
            color: #666;
        }

        .barcode-format {
            display: inline-block;
            background: #e8f5e9;
            color: #2e7d32;
            padding: 2px 6px;
            border-radius: 3px;
            margin: 0 2px;
            font-size: 11px;
            cursor: pointer;
        }

        .scan-result-frame {
            max-width: 550px;
            animation: successSlide 0.5s ease-out;
        }
        
        @keyframes successSlide {
            0% { transform: translateY(-30px); opacity: 0; }
            100% { transform: translateY(0); opacity: 1; }
        }
        
        .scan-result-title {
            font-size: 24px;
            color: #4CAF50;
            margin-bottom: 20px;
            font-weight: bold;
        }
        
        .scan-result-count {
            color: #666;
            font-size: 14px;
            margin-bottom: 20px;
        }
        
        .scan-result-products {
            max-height: 400px;
            overflow-y: auto;
            margin: 15px 0;
            text-align: left;
        }
        
        .scan-result-card {
            background: #f9f9f9;
            padding: 15px;
            margin-bottom: 10px;
            border-radius: 8px;
            border-left: 4px solid #2196F3;
        }
        
        .scan-result-stock {
            margin-top: 10px;
            padding: 8px;
            background-color: #f0f8ff;
            border-radius: 5px;
            border-left: 3px solid #4CAF50;
        }
        
        .scan-result-storage {
            margin-top: 8px;
            padding: 6px;
            background-color: #fff3cd;
            border-radius: 5px;
            border-left: 3px solid #ffc107;
        }
        
        .scan-result-barcodes {
            margin-top: 8px;
            padding: 6px;
            background-color: #e8f5e9;
            border-radius: 5px;
            border-left: 3px solid #4CAF50;
            cursor: pointer;
        }
        
        .scan-result-barcodes-title {
            font-weight: bold;
            color: #2e7d32;
            margin-bottom: 3px;
            font-size: 12px;
            display: flex;
            justify-content: space-between;
            align-items: center;
        }
        
        .scan-result-barcodes-list {
            max-height: 0;
            overflow: hidden;
            transition: max-height 0.3s ease-out;
        }
        
        .scan-result-barcodes-list.expanded {
            max-height: 200px;
            overflow-y: auto;
        }
        
        .scan-result-barcode-item {
            padding: 2px 0;
            font-family: monospace;
            font-size: 11px;
            border-bottom: 1px solid #d0f0c0;
        }
        
        .scan-result-actions {
            display: flex;
            gap: 10px;
            justify-content: center;
            margin-top: 20px;
            flex-wrap: wrap;
        }
        
        .action-btn {
            padding: 12px 25px;
            border: none;
            border-radius: 8px;
            cursor: pointer;
            font-size: 16px;
            font-weight: bold;
            transition: all 0.3s;
            flex: 1;
            min-width: 140px;
            text-align: center;
        }
        
        .continue-scan-btn {
            background-color: #2196F3;
            color: white;
        }
        
        .continue-scan-btn:hover {
            background-color: #0b7dda;
            transform: translateY(-2px);
        }
        
        .close-result-btn {
            background-color: #f44336;
            color: white;
        }
        
        .close-result-btn:hover {
            background-color: #d32f2f;
            transform: translateY(-2px);
        }
        
        .scan-price-container {
            display: flex;
            flex-direction: column;
            align-items: center;
            text-align: center;
        }
        
        .scan-discount-price {
            font-size: 30px;
            color: #e74c3c;
            font-weight: bold;
            display: block;
        }
        
        .scan-original-price {
            color: #888;
            font-size: 20px;
            text-decoration: line-through;
        }
        
        .scan-discount-percent {
            color: #888;
            font-size: 20px;
        }
        
        .dates-modal {
            max-width: 500px;
            animation: successSlide 0.5s ease-out;
        }
        
        .dates-content {
            text-align: left;
            margin: 20px 0;
            max-height: 400px;
            overflow-y: auto;
        }
        
        .date-section {
            margin-bottom: 20px;
        }
        
        .date-section-title {
            font-size: 18px;
            font-weight: bold;
            color: #333;
            margin-bottom: 10px;
            padding-bottom: 5px;
            border-bottom: 2px solid #4CAF50;
        }
        
        .date-item {
            margin-bottom: 8px;
            padding: 8px;
            background-color: #f9f9f9;
            border-radius: 5px;
            border-left: 3px solid #2196F3;
        }
        
        .date-item-row {
            display: flex;
            align-items: center;
        }
        
        .date-item-label {
            font-weight: bold;
            color: #555;
            min-width: 100px;
            font-size: 13px;
        }
        
        .date-item-time {
            font-size: 13px;
            color: #333;
            font-weight: bold;
        }
        
        .no-dates-info {
            text-align: center;
            padding: 20px;
            color: #666;
            font-style: italic;
        }
        
        .dates-header {
            text-align: center;
            margin-bottom: 15px;
            padding-bottom: 10px;
            border-bottom: 2px solid #4CAF50;
        }
        
        .current-date-display {
            font-size: 16px;
            color: #2e7d32;
            font-weight: bold;
            margin-bottom: 5px;
        }
        
        .data-update-container {
            font-size: 18px;
            color: #e74c3c;
            font-weight: bold;
            margin-bottom: 5px;
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
                   placeholder="Введите артикул для поиска..."
                   autocomplete="off">
            <button class="clear-search-btn" id="clearSearchBtn" title="Очистить поле поиска">&#10060;</button>
        </div>
        
        <div class="combined-search-fields" id="combinedSearchFields">
            <div class="combined-field">
                <label for="articleInput">Артикул:</label>
                <input type="text" 
                       class="combined-input" 
                       id="articleInput" 
                       placeholder="Часть артикула..."
                       autocomplete="off">
            </div>
            <div class="combined-field">
                <label for="nameInput">Наименование:</label>
                <input type="text" 
                       class="combined-input" 
                       id="nameInput" 
                       placeholder="Часть наименования..."
                       autocomplete="off">
            </div>
            <div class="combined-field">
                <label for="barcodeInput">Штрихкод:</label>
                <input type="text" 
                       class="combined-input" 
                       id="barcodeInput" 
                       placeholder="Часть штрихкода..."
                       autocomplete="off">
            </div>
        </div>
        
        <div class="search-mode-selector">
            <div class="mode-option">
                <input type="radio" id="modeArticle" name="searchMode" class="mode-radio" value="article" checked>
                <label for="modeArticle" class="mode-label">Артикул</label>
            </div>
            <div class="mode-option">
                <input type="radio" id="modeBarcode" name="searchMode" class="mode-radio" value="barcode">
                <label for="modeBarcode" class="mode-label">Штрихкод</label>
            </div>
            <div class="mode-option">
                <input type="radio" id="modeName" name="searchMode" class="mode-radio" value="name">
                <label for="modeName" class="mode-label">Наименование</label>
            </div>
            <div class="mode-option">
                <input type="radio" id="modeCombined" name="searchMode" class="mode-radio" value="combined">
                <label for="modeCombined" class="mode-label">Комбинированный</label>
            </div>
        </div>
        
        <div class="buttons-container">
            <button class="search-button" id="searchButton">Найти</button>
            <button class="scan-button" id="scanButton" style="display: none;">
                <span class="scan-icon">&#128247;</span> Сканировать штрихкод
            </button>
        </div>

		<div class="barcode-supported">
			<span class="barcode-format" id="current-date" title="Нажмите для просмотра дат изменения файлов"></span>
		</div>

        <div id="printStatus" class="print-status"></div>
        
        <div class="results-container" id="resultsContainer">
            <!-- Результаты поиска будут здесь -->
        </div>
    </div>

    <!-- Кнопка "Наверх" -->
    <button class="scroll-to-top-btn" id="scrollToTopBtn" title="Наверх">&#9650;</button>

    <!-- Модальное окно камеры для Android/Desktop -->
    <div class="modal-overlay" id="cameraModal">
        <div class="modal-frame">
            <h3>Сканирование штрихкода</h3>
            <div class="video-wrapper" id="videoContainer">
                <div class="scan-box">
                    <div class="scan-line"></div>
                </div>
                <video id="cameraVideo" playsinline></video>
                <div class="camera-controls">
                    <button class="camera-btn" id="stopCamera">Остановить</button>
                </div>
            </div>
            <button class="close-modal" id="closeCameraModal">Закрыть</button>
        </div>
    </div>

    <!-- Модальное окно iOS сканирования -->
    <div class="ios-scanner-modal" id="iosScannerModal">
        <div class="ios-scanner-content">
            <div class="ios-scanner-container">
                <!-- Контейнер для Html5-QRCode -->
                <div id="ios-qr-reader"></div>
                
                <!-- Наш оверлей -->
                <div class="ios-scan-overlay">
                    <div class="ios-scan-frame">
                        <div class="ios-scan-line"></div>
                    </div>
                </div>
                
                <!-- Сообщения и статусы -->
                <div class="ios-scanner-info">
                    Наведите камеру на штрихкод в рамке
                </div>
                
                <div class="ios-scanner-status" id="iosScannerStatus"></div>
                <div class="ios-scanned-badge" id="iosScannedBadge"></div>
                <div class="ios-loader" id="iosScannerLoader">Загрузка...</div>
                
                <!-- Подсказка для iOS -->
                <div class="ios-permission-hint" id="iosPermissionHint">
                    📱 Для iOS:<br><br>
                    1. Разрешите доступ к камере<br>
                    2. Нажмите "Разрешить"<br>
                    3. Камера активируется автоматически
                </div>
            </div>
            
            <div class="ios-modal-controls">
                <button class="ios-modal-btn ios-modal-btn-danger" id="closeIosScanner">
                    ✕ Закрыть сканер
                </button>
            </div>
        </div>
    </div>

    <!-- Модальное окно результатов сканирования -->
    <div class="modal-overlay" id="resultModal">
        <div class="modal-frame scan-result-frame">
            <div class="scan-result-products" id="resultProducts">
                <!-- Список товаров будет здесь -->
            </div>
            <div class="scan-result-count" id="resultCount">
                <!-- Количество найденных товаров -->
            </div>
            <div class="scan-result-actions">
                <button class="action-btn continue-scan-btn" id="continueScanBtn">
                    &#128247; Сканировать еще
                </button>
                <button class="action-btn close-result-btn" id="closeResultBtn">
                    Закрыть
                </button>
            </div>
        </div>
    </div>

    <!-- Новое модальное окно печати -->
    <div class="print-modal-new" id="printModal">
        <div class="print-modal-content-new">
            <h3>Печать ценника</h3>
            
            <div id="printerStatus" class="printer-status printer-connecting">
                Подключаюсь к принтеру...
            </div>
            
            <!-- НОВЫЙ БЛОК: Выбор типа ценника -->
            <div class="price-tag-type-selector" id="priceTagTypeSelector">
                <div class="price-tag-type-option selected" data-type="regular">
                    <input type="radio" id="typeRegular" name="priceTagType" class="price-tag-type-radio" value="regular" checked>
                    <label for="typeRegular" class="price-tag-type-label">Обычный</label>
                </div>
                <div class="price-tag-type-option" data-type="large">
                    <input type="radio" id="typeLarge" name="priceTagType" class="price-tag-type-radio" value="large">
                    <label for="typeLarge" class="price-tag-type-label">Большой</label>
                </div>
            </div>
            
            <div class="price-tag-preview">
                <canvas id="priceTagPreviewCanvas" class="price-tag-canvas" width="440" height="284"></canvas>
            </div>
            
            <button class="print-action-btn" id="printActionBtn" disabled>
                Распечатать
            </button>
            
            <button class="close-modal" id="closePrintModal" style="margin-top: 15px;">
                Закрыть
            </button>
        </div>
    </div>

    <!-- Модальное окно с датами изменения файлов -->
    <div class="modal-overlay" id="datesModal">
        <div class="modal-frame dates-modal">
            <div class="dates-header">
                <div class="current-date-display" id="modalCurrentDate">Дата обновления: 04.02.2026</div>
                <div class="data-update-container" id="dataUpdateContainer">Данные на : 14:07</div>
            </div>
            <div id="datesContent" class="dates-content">
                <!-- Содержимое будет сгенерировано JavaScript -->
            </div>
            <button class="close-modal" id="closeDatesModal" style="margin-top: 15px;">
                Закрыть
            </button>
        </div>
    </div>

    <script>
		// ===== ДАТА =====
        const DATA_UPDATE_DATE = ""; // Будет заполнена AHK скриптом: "04.02.2026"
        
        // ===== ДАТЫ ИЗМЕНЕНИЯ ФАЙЛОВ =====
        // Эти данные будут заполняться AHK скриптом
        const URAL_OFFICE_DATE = ""; // Будет заполнена AHK скриптом: "03.02.2026 14:32"
        const URAL_DATE = ""; // Будет заполнена AHK скриптом: "04.02.2026 8:19"
        const SHEVCHENKO_OFFICE_DATE = ""; // Будет заполнена AHK скриптом: "04.02.2026 07:33"
        const SHEVCHENKO_DATE = ""; // Будет заполнена AHK скриптом: "04.02.2026 09:02"

        // ===== ГЛОБАЛЬНЫЕ ПЕРЕМЕННЫЕ =====
        let stream = null;
        let barcodeDetector = null;
        let scanInterval = null;
        let lastScannedCode = '';
        
        // Переменные для печати
        let serialPort = null;
        let serialWriter = null;
        let isPrinterConnected = false;
        
        // Текущий товар для печати
        let currentProductForPrint = null;
        
        // НОВАЯ ПЕРЕМЕННАЯ: тип ценника (по умолчанию обычный)
        let currentPriceTagType = 'regular';
        
        // Переменные для iOS сканирования
        let iosHtml5QrCode = null;
        let isIosScanning = false;
        
        // Определяем платформу
        const isIOS = /iPad|iPhone|iPod/.test(navigator.userAgent) && !window.MSStream;
        const isAndroid = /Android/.test(navigator.userAgent);
        
        // Пример данных
        const productsData = `6080010075148;KS-8001;Набор для творчества "ЧАСТИЧНАЯ ВЫКЛАДКА СТРАЗАМИ" 10*15 в пакете;70,00;70,00;7;;10;2;0,035;;0,050;0,010;;Cb010003474_1;;;200;Cb010003474_1;
ЦБ010003475;Q-А998;Парусник на радиоуправлении на батарейках с рулём.;355,00;355,00;;;8;;;;0,167;;;;50;177,50;48;Cb010003475_1;
6132588301003;TS-MY88301;Конструктор " Гоночная машина";1780,00;1780,00;;;1;1;;;0,167;0,167;У/Ж2;KS-402-24;;;6;Cb010003476_1;
6029111127158;KS-291A;Пакет подарочный бумажный «Сердечки» 30x40x12 (4 расцветки)                             ;45,00;45,00;;;;5;;;;0,021;;KS-291A;;;240;Cb010003477_1;
4606782423066;30Б5Aгр_26515;Блокнот SketchBook 30л А5ф КРАФТ без линовки жесткая подложка на гребне-День-ночь-;101,00;101,00;20;;36;3;0,833;;1,500;0,125;5/53;Cb010003478_1;;;24;Cb010003478_1;
4606782424018;30Б5Aгр_26515;Блокнот SketchBook 30л А5ф КРАФТ без линовки жесткая подложка на гребне-День-ночь-;101,00;101,00;20;;36;3;0,833;;1,500;0,125;5/53;Cb010003478_1;;;24;Cb010003478_1;
4600797004630;ФЗ-407057;Фреска с блестками Морской конек;204,00;204,00;;;8;;;;0,400;;;Cb010003481_1;;;20;Cb010003481_1;
`;

        // ===== ОСНОВНЫЕ ФУНКЦИИ =====
        function parseStockValue(value) {
            if (!value) return 0;
            const cleanValue = value.toString().replace(/\s/g, '').replace(/\u00A0/g, '');
            return parseInt(cleanValue) || 0;
        }

        function parseFloatValue(value) {
            if (!value) return 0;
            const cleanValue = value.toString().replace(',', '.').replace(/\s/g, '');
            return parseFloat(cleanValue) || 0;
        }

        function formatNumber(num, isPrice = false) {
            if (num === null || num === undefined) return '0';
            
            let number;
            if (typeof num === 'string') {
                const cleanString = num.replace(',', '.').replace(/\s/g, '');
                number = parseFloat(cleanString);
                if (isNaN(number)) return '0';
            } else {
                number = num;
            }
            
            if (isPrice) {
                const rounded = Math.round(number * 100) / 100;
                const hasKopecks = Math.abs(rounded - Math.round(rounded)) > 0.001;
                
                if (hasKopecks) {
                    return rounded.toFixed(2).replace('.', ',').replace(/\B(?=(\d{3})+(?!\d))/g, ' ');
                } else {
                    return Math.round(rounded).toString().replace(/\B(?=(\d{3})+(?!\d))/g, ' ');
                }
            }
            
            if (number < 0) {
                return Math.round(number).toString().replace(/\B(?=(\d{3})+(?!\d))/g, ' ');
            }
            return Math.round(number).toString().replace(/\B(?=(\d{3})+(?!\d))/g, ' ');
        }

        function formatCoefficient(num) {
            return num.toFixed(3).replace('.', ',');
        }

        function parseProductsData(data) {
            const lines = data.trim().split('\n');
            const products = [];
            
            for (const line of lines) {
                if (!line.trim()) continue;
                
                const parts = line.split(';');
                
                let imageCode = '';
                if (parts.length >= 16) {
                    imageCode = parts[14].trim();
                } else if (parts.length === 15) {
                    imageCode = parts[13].trim();
                }
                
                imageCode = imageCode.replace(/;+$/, '');
                
                let alternativeImageCode = '';
                if (parts.length > 18) {
                    alternativeImageCode = parts[parts.length - 2].trim();
                }
                
                let discountPercent = '';
                let discountPrice = '';
                let boxQuantity = '';
                
                if (parts.length >= 17) {
                    discountPercent = parts[15] || '';
                    discountPrice = parts[16] || '';
                    boxQuantity = parts[17] || '';
                } else if (parts.length === 16) {
                    discountPercent = parts[14] || '';
                    discountPrice = parts[15] || '';
                } else if (parts.length === 15) {
                    discountPercent = parts[14] || '';
                }
                
                discountPercent = discountPercent.trim();
                discountPrice = discountPrice ? discountPrice.trim() : '';
                boxQuantity = boxQuantity ? boxQuantity.trim() : '';
                
                products.push({
                    barcode: parts[0] || '',
                    article: parts[1] || '',
                    name: parts[2] || '',
                    wholesalePrice: parts[3] || '',
                    retailPrice: parts[4] || '',
                    stocks: {
                        warehouse1: parseStockValue(parts[5]),
                        warehouse2: parseStockValue(parts[6]),
                        warehouse3: parseStockValue(parts[7]),
                        warehouse4: parseStockValue(parts[8])
                    },
                    coefficients: {
                        warehouse1: parseFloatValue(parts[9]),
                        warehouse2: parseFloatValue(parts[10]),
                        warehouse3: parseFloatValue(parts[11]),
                        warehouse4: parseFloatValue(parts[12])
                    },
                    storageLocation: parts[13] || '',
                    imageCode: imageCode,
                    alternativeImageCode: alternativeImageCode,
                    discountPercent: discountPercent,
                    discountPrice: discountPrice,
                    boxQuantity: boxQuantity
                });
            }
            
            return products;
        }

        function createProductKey(product) {
            return `${product.article}|${product.name}|${product.wholesalePrice}|${product.retailPrice}|${product.stocks.warehouse1}|${product.stocks.warehouse2}|${product.stocks.warehouse3}|${product.stocks.warehouse4}|${product.coefficients.warehouse1}|${product.coefficients.warehouse2}|${product.coefficients.warehouse3}|${product.coefficients.warehouse4}|${product.storageLocation}|${product.imageCode}|${product.alternativeImageCode}|${product.discountPercent}|${product.discountPrice}|${product.boxQuantity}`;
        }

        function groupProductsByKey(products) {
            const groups = {};
            
            products.forEach(product => {
                const key = createProductKey(product);
                
                if (!groups[key]) {
                    groups[key] = {
                        ...product,
                        barcodes: [product.barcode],
                        count: 1
                    };
                } else {
                    groups[key].barcodes.push(product.barcode);
                    groups[key].count++;
                }
            });
            
            return Object.values(groups);
        }

        // ===== ФУНКЦИИ ДЛЯ СКАНИРОВАНИЯ (Android/Desktop) =====
        function isBarcodeDetectorSupported() {
            return ('BarcodeDetector' in window);
        }

        async function initBarcodeDetector() {
            if (!isBarcodeDetectorSupported()) {
                console.warn('BarcodeDetector API не поддерживается в этом браузере');
                return null;
            }
            
            try {
                const formats = await BarcodeDetector.getSupportedFormats();
                const supportedFormats = formats.filter(format => 
                    ['ean_13', 'ean_8', 'upc_a', 'upc_e', 'code_39', 'code_128', 'codabar'].includes(format)
                );
                
                if (supportedFormats.length === 0) {
                    console.warn('Нет поддержки нужных форматов штрихкодов');
                    return null;
                }
                
                return new BarcodeDetector({ formats: supportedFormats });
            } catch (error) {
                console.error('Ошибка инициализации BarcodeDetector:', error);
                return null;
            }
        }

        async function openCamera() {
            try {
                stopCameraStream();
                
                const constraints = {
                    video: {
                        facingMode: 'environment',
                        width: { ideal: 1280 },
                        height: { ideal: 720 }
                    },
                    audio: false
                };
                
                stream = await navigator.mediaDevices.getUserMedia(constraints);
                
                cameraVideo.srcObject = stream;
                cameraModal.style.display = 'flex';
                
                await cameraVideo.play();
                
                if (!barcodeDetector) {
                    barcodeDetector = await initBarcodeDetector();
                }
                
                if (!barcodeDetector) {
                    alert('Ваш браузер не поддерживает прямое сканирование штрихкодов.');
                    stopCameraStream();
                    return;
                }
                
                startBarcodeDetection(barcodeDetector);
                
            } catch (error) {
                console.error('Ошибка доступа к камере:', error);
                alert('Не удалось получить доступ к камере. Пожалуйста, разрешите доступ к камере в настройках браузера.');
            }
        }

        function startBarcodeDetection(detector) {
            const canvas = document.createElement('canvas');
            const context = canvas.getContext('2d');
            
            scanInterval = setInterval(async () => {
                if (cameraVideo.readyState === cameraVideo.HAVE_ENOUGH_DATA) {
                    canvas.width = cameraVideo.videoWidth;
                    canvas.height = cameraVideo.videoHeight;
                    
                    context.drawImage(cameraVideo, 0, 0, canvas.width, canvas.height);
                    
                    try {
                        const barcodes = await detector.detect(canvas);
                        
                        if (barcodes && barcodes.length > 0) {
                            const barcode = barcodes[0];
                            handleScannedCode(barcode.rawValue);
                            return;
                        }
                        
                    } catch (error) {
                        console.error('Ошибка детектирования штрихкода:', error);
                    }
                }
            }, 300);
        }

        function stopCameraStream() {
            if (stream) {
                stream.getTracks().forEach(track => track.stop());
                stream = null;
            }
            if (scanInterval) {
                clearInterval(scanInterval);
                scanInterval = null;
            }
            cameraVideo.srcObject = null;
        }

        // ===== ФУНКЦИИ ДЛЯ iOS СКАНИРОВАНИЯ =====
        async function openIosScanner() {
            console.log('Открытие iOS сканера...');
            
            const modal = document.getElementById('iosScannerModal');
            modal.style.display = 'block';
            
            document.getElementById('iosScannerLoader').style.display = 'block';
            showIosScannerStatus('Инициализация камеры...');
            
            setTimeout(() => {
                initIosBarcodeScanner();
            }, 300);
        }

        function initIosBarcodeScanner() {
            try {
                if (iosHtml5QrCode && isIosScanning) {
                    iosHtml5QrCode.stop().then(() => {
                        iosHtml5QrCode.clear();
                        iosHtml5QrCode = null;
                    }).catch(() => {
                        iosHtml5QrCode = null;
                    });
                }
                
                const config = {
                    fps: 10,
                    qrbox: { 
                        width: 250, 
                        height: 150 
                    },
                    rememberLastUsedCamera: true,
                    supportedScanTypes: [Html5QrcodeScanType.SCAN_TYPE_CAMERA]
                };
                
                iosHtml5QrCode = new Html5Qrcode("ios-qr-reader");
                
                iosHtml5QrCode.start(
                    { 
                        facingMode: "environment"
                    }, 
                    config,
                    onIosScanSuccess,
                    onIosScanError
                ).then(() => {
                    console.log('iOS сканирование запущено успешно');
                    isIosScanning = true;
                    
                    document.getElementById('iosScannerLoader').style.display = 'none';
                    document.getElementById('iosPermissionHint').style.display = 'none';
                    hideIosScannerStatus();
                    
                    setTimeout(() => {
                        document.getElementById('iosPermissionHint').style.display = 'none';
                    }, 2000);
                    
                }).catch(err => {
                    console.error('Ошибка запуска iOS сканера:', err);
                    
                    if (err.toString().includes('environment')) {
                        console.log('Пробуем фронтальную камеру iOS...');
                        showIosScannerStatus('Пробуем фронтальную камеру...');
                        
                        iosHtml5QrCode.start(
                            { facingMode: "user" }, 
                            config,
                            onIosScanSuccess,
                            onIosScanError
                        ).then(() => {
                            isIosScanning = true;
                            document.getElementById('iosScannerLoader').style.display = 'none';
                            hideIosScannerStatus();
                        }).catch(err2 => {
                            console.error('Ошибка с фронтальной камерой iOS:', err2);
                            showIosScannerStatus('Ошибка камеры: ' + err2.message);
                        });
                    } else {
                        showIosScannerStatus('Ошибка: ' + err.message);
                    }
                });
                
            } catch (error) {
                console.error('Критическая ошибка iOS инициализации:', error);
                showIosScannerStatus('Ошибка: ' + error.message);
            }
        }

        function onIosScanSuccess(decodedText, decodedResult) {
            console.log('iOS сканирование успешно:', decodedText);
            
            if (lastScannedCode === decodedText) {
                return;
            }
            
            lastScannedCode = decodedText;
            
            showIosScannedBadge(decodedText);
            
            if (iosHtml5QrCode && isIosScanning) {
                iosHtml5QrCode.stop().then(() => {
                    isIosScanning = false;
                }).catch(() => {
                    isIosScanning = false;
                });
            }
            
            setTimeout(() => {
                closeIosScanner();
                
                document.getElementById('searchInput').value = decodedText;
                
                performSearch();
                
                setTimeout(() => {
                    lastScannedCode = '';
                }, 3000);
                
            }, 1500);
        }

        function onIosScanError(error) {
            if (!error.includes('NotFoundException') && !error.includes('No multi format readers configured')) {
                console.warn('Ошибка iOS сканирования:', error);
            }
        }

        function closeIosScanner() {
            console.log('Закрытие iOS сканера...');
            
            if (iosHtml5QrCode && isIosScanning) {
                iosHtml5QrCode.stop().then(() => {
                    console.log('iOS сканирование остановлено');
                    iosHtml5QrCode.clear();
                    iosHtml5QrCode = null;
                    isIosScanning = false;
                }).catch(err => {
                    console.log('Ошибка остановки iOS:', err);
                    iosHtml5QrCode = null;
                    isIosScanning = false;
                });
            }
            
            document.getElementById('iosScannerModal').style.display = 'none';
            document.getElementById('iosScannedBadge').style.display = 'none';
            document.getElementById('iosPermissionHint').style.display = 'none';
            hideIosScannerStatus();
        }

        function showIosScannedBadge(code) {
            const badge = document.getElementById('iosScannedBadge');
            badge.textContent = `✓ Найдено: ${code}`;
            badge.style.display = 'block';
        }

        function showIosScannerStatus(message) {
            const status = document.getElementById('iosScannerStatus');
            status.textContent = message;
            status.style.display = 'block';
        }

        function hideIosScannerStatus() {
            document.getElementById('iosScannerStatus').style.display = 'none';
        }

        // ===== ОБЩАЯ ФУНКЦИЯ ОБРАБОТКИ СКАНИРОВАНИЯ =====
        function handleScannedCode(code) {
            if (!code || code.trim().length === 0) return;
            
            stopCameraStream();
            document.getElementById('modeBarcode').checked = true;
            updateSearchUI();
            
            const cleanCode = code.toString().trim();
            searchInput.value = cleanCode;
            updateClearButton();
            
            const results = performSimpleSearch(cleanCode, 'barcode');
            showScanResults(cleanCode, results);
        }

        function setupPlatformUI() {
            if (isIOS) {
                // iOS - используем Html5-QRCode
                scanButton.style.display = 'flex';
                searchButton.style.maxWidth = '300px';
            } else if (isAndroid) {
                // Android - используем обычное сканирование
                scanButton.style.display = 'flex';
                setTimeout(() => {
                    initBarcodeDetector();
                }, 1000);
            } else {
                // Desktop - используем обычное сканирование
                scanButton.style.display = 'flex';
                setTimeout(() => {
                    initBarcodeDetector();
                }, 1000);
            }
        }

        function openScanner() {
            if (isIOS) {
                openIosScanner();
            } else {
                openCamera();
            }
        }

        function closeScanner() {
            if (isIOS) {
                closeIosScanner();
            } else {
                stopCameraStream();
                cameraModal.style.display = 'none';
            }
        }

        // ===== ФУНКЦИИ ДЛЯ ПОИСКА И ОТОБРАЖЕНИЯ =====
        function formatPriceWithDiscount(product) {
            const hasDiscount = product.discountPercent && product.discountPercent.trim() !== '';
            
            if (!hasDiscount) {
                return `
                    <div class="price-container">
                        <span class="discount-price">Цена: ${product.wholesalePrice} руб.</span>
                    </div>
                `;
            }
            
            const discountPercent = product.discountPercent;
            const discountPrice = product.discountPrice || product.wholesalePrice;
            
            return `
                <div class="price-container">
                    <span class="discount-price">Цена: ${discountPrice} руб.</span>
                    <div class="old-price-container">
                        <span class="original-price">${product.wholesalePrice} </span>
                        <span class="discount-percent">-${discountPercent}% &#128165;</span>
                    </div>
                </div>
            `;
        }

        function formatPriceWithDiscountModal(product) {
            const hasDiscount = product.discountPercent && product.discountPercent.trim() !== '';
            
            if (!hasDiscount) {
                return `
                    <div class="scan-price-container">
                        <span class="scan-discount-price">Цена: ${product.wholesalePrice} руб.</span>
                    </div>
                `;
            }
            
            const discountPercent = product.discountPercent;
            const discountPrice = product.discountPrice || product.wholesalePrice;
            
            return `
                <div class="scan-price-container">
                    <span class="scan-discount-price">Цена: ${discountPrice} руб.</span>
                    <div class="scan-old-price-container">
                        <span class="scan-original-price">${product.wholesalePrice}</span>
                        <span class="scan-discount-percent">-${discountPercent}% &#128165;</span>
                    </div>
                </div>
            `;
        }

        function getCurrentSearchMode() {
            const selectedRadio = document.querySelector('input[name="searchMode"]:checked');
            return selectedRadio ? selectedRadio.value : 'article';
        }

        function getSearchModeDisplayName(mode) {
            switch(mode) {
                case 'article': return 'по артикулу';
                case 'barcode': return 'по штрихкоду';
                case 'name': return 'по наименованию';
                case 'combined': return 'комбинированный';
                default: return 'по артикулу';
            }
        }

        function performCombinedSearch(articlePart, namePart, barcodePart) {
            const products = parseProductsData(productsData);
            return products.filter(product => {
                let matches = 0;
                let totalConditions = 0;
                
                if (articlePart && articlePart.trim() !== '') {
                    totalConditions++;
                    if (product.article.toLowerCase().includes(articlePart.toLowerCase())) {
                        matches++;
                    }
                }
                
                if (namePart && namePart.trim() !== '') {
                    totalConditions++;
                    if (product.name.toLowerCase().includes(namePart.toLowerCase())) {
                        matches++;
                    }
                }
                
                if (barcodePart && barcodePart.trim() !== '') {
                    totalConditions++;
                    if (product.barcode.includes(barcodePart)) {
                        matches++;
                    }
                }
                
                return totalConditions > 0 && matches === totalConditions;
            });
        }

        function performSimpleSearch(searchTerm, mode) {
            const products = parseProductsData(productsData);
            return products.filter(product => {
                switch(mode) {
                    case 'article':
                        return product.article.toLowerCase().includes(searchTerm.toLowerCase());
                    
                    case 'barcode':
                        return product.barcode.includes(searchTerm);
                    
                    case 'name':
                        return product.name.toLowerCase().includes(searchTerm.toLowerCase());
                    
                    default:
                        return product.article.toLowerCase().includes(searchTerm.toLowerCase());
                }
            });
        }

        function showScanResults(code, results) {
            lastScannedCode = code;
            
            if (results.length === 0) {
                resultCount.textContent = 'Товары не найдены';
                resultCount.style.color = '#f44336';
                resultProducts.innerHTML = '<div class="scan-result-card" style="text-align: center; color: #666; font-style: italic;">По этому штрихкоду товары не найдены в базе данных</div>';
            } else {
                const groupedResults = groupProductsByKey(results);
                resultCount.textContent = `Найдено товаров: ${results.length} (${groupedResults.length} уникальных)`;
                resultCount.style.color = '#4CAF50';
                
                resultProducts.innerHTML = '';
                
                groupedResults.forEach(product => {
                    const productCard = document.createElement('div');
                    productCard.className = 'scan-result-card';
                    
                    const hasImage = product.imageCode && product.imageCode.trim() !== '';
                    const printButtonHTML = `<button class="print-button" onclick="openPrintModal(${JSON.stringify(product).replace(/"/g, '&quot;')})" title="Печать ценника">&#129534;</button>`;

                    productCard.innerHTML = `
                        <div style="font-size: 12px; color: #666; margin-bottom: 5px;">
                            <strong>Штрихкод:</strong> ${product.count > 1 ? `Несколько (${product.count})` : product.barcode}
                        </div>
                        <div style="font-weight: bold; color: #333; margin-bottom: 5px; display: flex; align-items: center; justify-content: space-between;">
                            <div style="display: flex; align-items: center;" id="articleContainer_${product.article.replace(/[^a-zA-Z0-9]/g, '_')}">
                                <strong>Артикул:</strong> ${product.article}
                                ${hasImage ? '<button class="image-button" style="margin-left: 10px;">&#127750;</button>' : '<span class="no-image-text">(без изображения)</span>'}
                            </div>
                            ${printButtonHTML}
                        </div>
                        <div style="font-size: 16px; color: #222; margin-bottom: 8px;">
                            ${product.name}
                        </div>
                        ${formatPriceWithDiscountModal(product)}
                        ${formatStockInfoModal(product)}
                    `;
                    
                    if (hasImage) {
                        setTimeout(() => {
                            const container = productCard.querySelector(`#articleContainer_${product.article.replace(/[^a-zA-Z0-9]/g, '_')}`);
                            if (container) {
                                const imageButton = container.querySelector('.image-button');
                                if (imageButton) {
                                    imageButton.onclick = function() {
                                        showProductImage(product);
                                    };
                                }
                            }
                        }, 0);
                    }
                    
                    resultProducts.appendChild(productCard);
                });
            }
            
            // Закрываем оба типа модальных окон
            if (isIOS) {
                closeIosScanner();
            } else {
                cameraModal.style.display = 'none';
            }
            
            resultModal.style.display = 'flex';
        }

        function formatStockInfoModal(product) {
            const stocks = product.stocks;
            const coefficients = product.coefficients;
            const storageLocation = product.storageLocation;
            const barcodes = product.barcodes;
            const scannedCode = lastScannedCode;
            const boxQuantity = product.boxQuantity || '';
            
            let html = '<div class="scan-result-stock">';
            html += '<div style="font-weight: bold; color: #333; margin-bottom: 5px; font-size: 13px;">Остатки:</div>';
            
            [1, 2, 3, 4].forEach(i => {
                const quantity = stocks[`warehouse${i}`];
                const coeff = coefficients[`warehouse${i}`];
                const color = quantity < 0 ? '#f44336' : '#2e7d32';
                const names = ['Уральская 97:', 'ОСНОВНОЙ СКЛАД:', 'Шевченко 139:', 'МАГАЗИН 234:'];
                
                html += `<div style="display: flex; justify-content: space-between; padding: 2px 0; font-size: 12px;">
                    <span style="color: #555;">${names[i-1]}</span>
                    <span style="color: ${color}; font-weight: bold;">${formatNumber(quantity)} шт. 
                        <span style="color: #666; font-size: 11px;">(${formatCoefficient(coeff)} кор.)</span>
                    </span>
                </div>`;
            });
            
            html += '</div>';
            
            if (boxQuantity && boxQuantity.trim() !== '') {
                html += `<div class="scan-result-storage" style="background-color: #e8f5e9; border-left-color: #4CAF50;">
                    <div style="font-weight: bold; color: #2e7d32; margin-bottom: 3px; font-size: 12px;">Кол-во в коробке:</div>
                    <div style="color: #333; font-weight: bold; font-size: 13px;">${boxQuantity} шт.</div>
                </div>`;
            }
            
            if (barcodes && barcodes.length > 1) {
                html += createBarcodesListHTML(barcodes, scannedCode);
            }
            
            if (storageLocation && storageLocation.trim() !== '') {
                html += `<div class="scan-result-storage">
                    <div style="font-weight: bold; color: #856404; margin-bottom: 3px; font-size: 12px;">Место хранения:</div>
                    <div style="color: #333; font-weight: bold; font-size: 13px;">${storageLocation}</div>
                </div>`;
            }
            
            return html;
        }

        function createProductCard(product, query, searchMode) {
            const productCard = document.createElement('div');
            productCard.className = 'product-card';
            
            let highlightedName = product.name;
            let highlightedArticle = product.article;
            let highlightedBarcode = '';
            
            if (searchMode === 'комбинированный') {
                if (query.article) {
                    highlightedArticle = highlightMatch(product.article, query.article);
                }
                if (query.name) {
                    highlightedName = highlightMatch(product.name, query.name);
                }
                if (query.barcode) {
                    if (product.count > 1) {
                        highlightedBarcode = createMultipleBarcodesHTML(product.barcodes, query.barcode);
                    } else {
                        highlightedBarcode = highlightMatch(product.barcode, query.barcode);
                    }
                }
            } else {
                if (searchMode === 'по артикулу' || searchMode === 'комбинированный') {
                    highlightedArticle = highlightMatch(product.article, query);
                }
                if (searchMode === 'по наименованию' || searchMode === 'комбинированный') {
                    highlightedName = highlightMatch(product.name, query);
                }
                if (searchMode === 'по штрихкоду') {
                    if (product.count > 1) {
                        highlightedBarcode = createMultipleBarcodesHTML(product.barcodes, query);
                    } else {
                        highlightedBarcode = highlightMatch(product.barcode, query);
                    }
                }
            }
            
            if (!highlightedBarcode) {
                if (product.count > 1) {
                    highlightedBarcode = createMultipleBarcodesHTML(product.barcodes, '');
                } else {
                    highlightedBarcode = product.barcode;
                }
            }
            
            const container = document.createElement('div');
            
            const articleRow = document.createElement('div');
            articleRow.className = 'article';
            articleRow.innerHTML = `Артикул: ${highlightedArticle}`;
            
            const hasImage = product.imageCode && product.imageCode.trim() !== '';
            
            if (hasImage) {
                const imageButton = document.createElement('button');
                imageButton.className = 'image-button';
                imageButton.title = 'Показать изображение товара';
                imageButton.innerHTML = '&#127750;';
                imageButton.onclick = function() {
                    showProductImage(product);
                };
                articleRow.appendChild(imageButton);
            } else {
                const noImageSpan = document.createElement('span');
                noImageSpan.className = 'no-image-text';
                noImageSpan.textContent = '(без изображения)';
                articleRow.appendChild(noImageSpan);
            }
            
            const printButton = document.createElement('button');
            printButton.className = 'print-button';
            printButton.title = 'Печать ценника';
            printButton.innerHTML = '&#129534;';
            printButton.onclick = function() {
                openPrintModal(product);
            };
            
            const articleContainer = document.createElement('div');
            articleContainer.style.display = 'flex';
            articleContainer.style.justifyContent = 'space-between';
            articleContainer.style.alignItems = 'center';
            articleContainer.style.marginBottom = '5px';
            
            articleContainer.appendChild(articleRow);
            articleContainer.appendChild(printButton);
            
            container.innerHTML = `
                <div class="product-field barcode">Штрихкод: ${highlightedBarcode}</div>
                <div class="product-field name">${highlightedName}</div>
                ${formatPriceWithDiscount(product)}
            `;
            
            container.insertBefore(articleContainer, container.firstChild);
            
            const stockInfo = document.createElement('div');
            stockInfo.innerHTML = `
                <div class="stock-info">
                    <div class="stock-title">Остатки:</div>
                    <div class="stock-item">
                        <span class="stock-name">Уральская 97:</span>
                        <span class="stock-quantity ${product.stocks.warehouse1 < 0 ? 'negative' : 'positive'}">
                            ${formatNumber(product.stocks.warehouse1)} шт. 
                            <span class="box-coefficient">(${formatCoefficient(product.coefficients.warehouse1)} кор.)</span>
                        </span>
                    </div>
                    <div class="stock-item">
                        <span class="stock-name">ОСНОВНОЙ СКЛАД:</span>
                        <span class="stock-quantity ${product.stocks.warehouse2 < 0 ? 'negative' : 'positive'}">
                            ${formatNumber(product.stocks.warehouse2)} шт. 
                            <span class="box-coefficient">(${formatCoefficient(product.coefficients.warehouse2)} кор.)</span>
                        </span>
                    </div>
                    <div class="stock-item">
                        <span class="stock-name">Шевченко 139:</span>
                        <span class="stock-quantity ${product.stocks.warehouse3 < 0 ? 'negative' : 'positive'}">
                            ${formatNumber(product.stocks.warehouse3)} шт. 
                            <span class="box-coefficient">(${formatCoefficient(product.coefficients.warehouse3)} кор.)</span>
                        </span>
                    </div>
                    <div class="stock-item">
                        <span class="stock-name">МАГАЗИН 234:</span>
                        <span class="stock-quantity ${product.stocks.warehouse4 < 0 ? 'negative' : 'positive'}">
                            ${formatNumber(product.stocks.warehouse4)} шт. 
                            <span class="box-coefficient">(${formatCoefficient(product.coefficients.warehouse4)} кор.)</span>
                        </span>
                    </div>
                </div>
            `;
            
            if (product.boxQuantity && product.boxQuantity.trim() !== '') {
                stockInfo.innerHTML += `
                    <div class="box-quantity-info">
                        <div class="box-quantity-title">Кол-во в коробке:</div>
                        <div class="box-quantity-value">${product.boxQuantity} шт.</div>
                    </div>
                `;
            }
            
            if (product.storageLocation && product.storageLocation.trim() !== '') {
                stockInfo.innerHTML += `
                    <div class="storage-location">
                        <div class="storage-title">Место хранения:</div>
                        <div class="storage-value">${product.storageLocation}</div>
                    </div>
                `;
            }
            
            productCard.appendChild(container);
            productCard.appendChild(stockInfo);
            
            return productCard;
        }

        function displayResults(results, query, searchMode) {
            const resultsContainer = document.getElementById('resultsContainer');
            resultsContainer.innerHTML = '';

            if (results.length === 0) {
                resultsContainer.innerHTML = '<div class="no-results">Товары не найдены</div>';
                resultsContainer.style.display = 'block';
                scrollToResults();
                return;
            }

            const groupedResults = groupProductsByKey(results);
            const totalCount = results.length;
            const uniqueCount = groupedResults.length;

            const countElement = document.createElement('div');
            countElement.className = 'results-count';
            countElement.textContent = `Найдено товаров: ${totalCount} (${uniqueCount} уникальных)`;
            resultsContainer.appendChild(countElement);

            const modeElement = document.createElement('div');
            modeElement.className = 'search-mode';
            modeElement.textContent = `Режим поиска: ${searchMode}`;
            resultsContainer.appendChild(modeElement);

            groupedResults.forEach(product => {
                const productCard = createProductCard(product, query, searchMode);
                resultsContainer.appendChild(productCard);
            });

            resultsContainer.style.display = 'block';
            scrollToResults();
        }

        function searchProducts() {
            const searchMode = getCurrentSearchMode();
            
            let results = [];
            let query = '';
            let displaySearchMode = getSearchModeDisplayName(searchMode);

            if (searchMode === 'combined') {
                const articlePart = articleInput.value.trim();
                const namePart = nameInput.value.trim();
                const barcodePart = barcodeInput.value.trim();
                
                query = {
                    article: articlePart,
                    name: namePart,
                    barcode: barcodePart
                };
                
                results = performCombinedSearch(articlePart, namePart, barcodePart);
            } else {
                query = searchInput.value.trim();
                
                if (!query) {
                    resultsContainer.style.display = 'none';
                    return;
                }
                
                results = performSimpleSearch(query, searchMode);
            }

            displayResults(results, query, displaySearchMode);
        }

        // ===== ВСПОМОГАТЕЛЬНЫЕ ФУНКЦИИ =====
        function createMultipleBarcodesHTML(barcodes, query) {
            const uniqueBarcodes = [...new Set(barcodes)];
            const barcodesCount = uniqueBarcodes.length;
            
            let html = `<span class="multiple-barcodes" onclick="showBarcodeTooltip(event, this)">Несколько (${barcodesCount})</span>`;
            html += `<div class="barcode-tooltip">`;
            html += `<div class="barcode-list">`;
            
            uniqueBarcodes.forEach(barcode => {
                const highlightedBarcode = highlightMatch(barcode, query);
                html += `<div class="barcode-item">${highlightedBarcode}</div>`;
            });
            
            html += `</div>`;
            html += `</div>`;
            
            return html;
        }

        function createBarcodesListHTML(barcodes, scannedCode) {
            const uniqueBarcodes = [...new Set(barcodes)];
            const barcodesCount = uniqueBarcodes.length;
            
            let html = `<div class="scan-result-barcodes" onclick="toggleBarcodesList(this)">`;
            html += `<div class="scan-result-barcodes-title">`;
            html += `<span>Штрихкоды (${barcodesCount}):</span>`;
            html += `<span style="font-size: 10px; color: #666;">нажмите для просмотра</span>`;
            html += `</div>`;
            html += `<div class="scan-result-barcodes-list">`;
            
            uniqueBarcodes.forEach(barcode => {
                const isScanned = barcode === scannedCode;
                const barcodeClass = isScanned ? 'style="color: #e74c3c; font-weight: bold;"' : '';
                html += `<div class="scan-result-barcode-item" ${barcodeClass}>${barcode}${isScanned ? ' ?' : ''}</div>`;
            });
            
            html += `</div>`;
            html += `</div>`;
            
            return html;
        }

        function showBarcodeTooltip(event, element) {
            event.stopPropagation();
            
            document.querySelectorAll('.barcode-tooltip').forEach(tooltip => {
                tooltip.style.display = 'none';
            });
            
            const tooltip = element.nextElementSibling;
            if (tooltip && tooltip.classList.contains('barcode-tooltip')) {
                tooltip.style.display = 'block';
                
                const rect = element.getBoundingClientRect();
                tooltip.style.position = 'fixed';
                tooltip.style.left = Math.min(rect.left, window.innerWidth - 320) + 'px';
                tooltip.style.top = (rect.bottom + 5) + 'px';
                
                const closeTooltip = (e) => {
                    if (!tooltip.contains(e.target) && e.target !== element) {
                        tooltip.style.display = 'none';
                        document.removeEventListener('click', closeTooltip);
                    }
                };
                
                setTimeout(() => {
                    document.addEventListener('click', closeTooltip);
                }, 100);
            }
        }

        function toggleBarcodesList(element) {
            const list = element.querySelector('.scan-result-barcodes-list');
            list.classList.toggle('expanded');
            
            const title = element.querySelector('.scan-result-barcodes-title span:last-child');
            if (list.classList.contains('expanded')) {
                title.textContent = 'нажмите для скрытия';
            } else {
                title.textContent = 'нажмите для просмотра';
            }
        }

        function highlightMatch(text, searchTerm) {
            if (!searchTerm || !text) return text;
            const regex = new RegExp(`(${searchTerm.replace(/[.*+?^${}()|[\]\\]/g, '\\$&')})`, 'gi');
            return text.toString().replace(regex, '<mark>$1</mark>');
        }

        function updateClearButton() {
            const mode = getCurrentSearchMode();
            let hasText = false;
            
            if (mode === 'combined') {
                hasText = articleInput.value.trim() !== '' || 
                          nameInput.value.trim() !== '' || 
                          barcodeInput.value.trim() !== '';
            } else {
                hasText = searchInput.value.trim() !== '';
            }
            
            if (hasText) {
                clearSearchBtn.style.display = 'block';
            } else {
                clearSearchBtn.style.display = 'none';
            }
        }

        function clearSearchFields() {
            const mode = getCurrentSearchMode();
            
            if (mode === 'combined') {
                articleInput.value = '';
                nameInput.value = '';
                barcodeInput.value = '';
            } else {
                searchInput.value = '';
                searchInput.focus();
            }
            
            updateClearButton();
            resultsContainer.style.display = 'none';
        }

        function updateSearchUI() {
            const mode = getCurrentSearchMode();
            
            if (mode === 'combined') {
                combinedSearchFields.style.display = 'flex';
                searchInput.style.display = 'none';
            } else {
                combinedSearchFields.style.display = 'none';
                searchInput.style.display = 'block';
            }
            
            updateClearButton();
        }

        function scrollToResults() {
            const resultsContainer = document.getElementById('resultsContainer');
            if (resultsContainer.style.display === 'block') {
                setTimeout(() => {
                    resultsContainer.scrollIntoView({
                        behavior: 'smooth',
                        block: 'start'
                    });
                }, 100);
            }
        }

        // ===== ФУНКЦИИ ДЛЯ ИЗОБРАЖЕНИЙ =====
        function showProductImage(product) {
            const modal = document.createElement('div');
            modal.className = 'modal-overlay';
            modal.id = 'imageModal';
            modal.style.display = 'flex';
            
            let imageCode = product.imageCode || '';
            let alternativeImageCode = product.alternativeImageCode || '';
            
            let imageUrl = '';
            
            if (imageCode) {
                const cleanCode = imageCode.trim();
                let fileName = cleanCode;
                if (!fileName.includes('.jpg') && !fileName.includes('.jpeg') && 
                    !fileName.includes('.png') && !fileName.includes('.gif')) {
                    fileName += '.jpg';
                }
                imageUrl = `https://kubanstar.ru/images/virtuemart/product/${fileName}`;
            }
            
            modal.innerHTML = `
                <div class="modal-frame" style="max-width: 90%; max-height: 90%;">
                    <div style="text-align: center; padding: 20px;">
                        <h3 style="margin-bottom: 20px;">${product.article} - ${product.name}</h3>
                        <div style="max-height: 70vh; overflow: auto; margin: 20px 0;" id="imageContainer">
                            <img id="productImage" 
                                 src="${imageUrl || 'data:image/svg+xml,<svg xmlns=%22http://www.w3.org/2000/svg%22 viewBox=%220 0 100 100%22><text x=%2250%25%22 y=%2250%25%22 font-size=%2250%22 text-anchor=%22middle%22 dy=%22.3em%22>&#128247;</text></svg>'}" 
                                 style="max-width: 100%; max-height: 60vh; border-radius: 8px; display: block; margin: 0 auto;"
                                 onerror="handleImageError(this, '${alternativeImageCode.replace(/'/g, "\\'")}')"
                                 alt="Изображение товара">
                            <div id="imageError" style="display: none; padding: 40px; color: #999;">
                                <div style="font-size: 48px; margin-bottom: 20px;">&#128247;</div>
                                <div style="font-size: 18px; font-weight: bold; color: #666;">Изображение не найдено</div>
                                <div style="font-size: 12px; margin-top: 10px; color: #999;">Пробуем альтернативный код...</div>
                            </div>
                        </div>
                        <div style="margin-top: 15px; text-align: center;">
                            <button onclick="this.closest('.modal-overlay').style.display='none'" 
                                    class="camera-btn" 
                                    style="background-color: #f44336; min-width: 200px;">
                                Закрыть
                            </button>
                        </div>
                    </div>
                </div>
            `;
            
            const oldModal = document.getElementById('imageModal');
            if (oldModal) {
                oldModal.remove();
            }
            
            document.body.appendChild(modal);
            
            modal.onclick = function(e) {
                if (e.target === modal) {
                    modal.style.display = 'none';
                }
            };
        }

        function handleImageError(imgElement, alternativeImageCode) {
            const errorDiv = document.getElementById('imageError');
            
            if (alternativeImageCode && alternativeImageCode.trim() !== '') {
                const cleanCode = alternativeImageCode.trim();
                let fileName = cleanCode;
                if (!fileName.includes('.jpg') && !fileName.includes('.jpeg') && 
                    !fileName.includes('.png') && !fileName.includes('.gif')) {
                    fileName += '.jpg';
                }
                const alternativeUrl = `https://kubanstar.ru/images/virtuemart/product/${fileName}`;
                
                if (errorDiv) {
                    errorDiv.style.display = 'block';
                    errorDiv.innerHTML = `
                        <div style="font-size: 48px; margin-bottom: 20px;">&#128260;</div>
                        <div style="font-size: 18px; font-weight: bold; color: #666;">Загружаем альтернативное изображение...</div>
                        <div style="font-size: 12px; margin-top: 10px; color: #999;">Код: ${alternativeImageCode}</div>
                    `;
                }
                
                const newImg = new Image();
                newImg.onload = function() {
                    imgElement.src = alternativeUrl;
                    imgElement.style.display = 'block';
                    if (errorDiv) errorDiv.style.display = 'none';
                };
                newImg.onerror = function() {
                    if (errorDiv) {
                        errorDiv.style.display = 'block';
                        errorDiv.innerHTML = `
                            <div style="font-size: 48px; margin-bottom: 20px;">&#10060;</div>
                            <div style="font-size: 18px; font-weight: bold; color: #666;">Изображение не найдено</div>
                            <div style="font-size: 12px; margin-top: 10px; color: #999;">
                                Основной код: ${imgElement.src.includes('kubanstar.ru') ? imgElement.src.split('/').pop() : 'не указан'}<br>
                                Альтернативный код: ${alternativeImageCode || 'не указан'}
                            </div>
                        `;
                    }
                    imgElement.style.display = 'none';
                };
                newImg.src = alternativeUrl;
            } else {
                if (errorDiv) {
                    errorDiv.style.display = 'block';
                    errorDiv.innerHTML = `
                        <div style="font-size: 48px; margin-bottom: 20px;">&#10060;</div>
                        <div style="font-size: 18px; font-weight: bold; color: #666;">Изображение не найдено</div>
                        <div style="font-size: 12px; margin-top: 10px; color: #999;">
                            Код изображения: ${imgElement.src.includes('kubanstar.ru') ? imgElement.src.split('/').pop() : 'не указан'}
                        </div>
                    `;
                }
                imgElement.style.display = 'none';
            }
        }

        // ===== ФУНКЦИИ ДЛЯ ПЕЧАТИ =====
        function updatePrinterStatus(message, type = 'connecting') {
            const statusEl = document.getElementById('printerStatus');
            statusEl.textContent = message;
            
            statusEl.classList.remove('printer-connected', 'printer-disconnected', 'printer-connecting');
            
            switch(type) {
                case 'connected':
                    statusEl.classList.add('printer-connected');
                    statusEl.innerHTML = '&#9989; ' + message;
                    break;
                case 'disconnected':
                    statusEl.classList.add('printer-disconnected');
                    statusEl.innerHTML = '&#10060; ' + message;
                    break;
                case 'connecting':
                    statusEl.classList.add('printer-connecting');
                    statusEl.innerHTML = '&#9203; ' + message;
                    break;
            }
        }

        async function connectToPrinter() {
            try {
                updatePrinterStatus('Подключаюсь к принтеру...', 'connecting');
                
                if (!navigator.serial) {
                    throw new Error('Ваш браузер не поддерживает Web Serial. Используйте Chrome/Edge 89+');
                }
                
                const ports = await navigator.serial.getPorts();
                
                if (ports.length > 0) {
                    serialPort = ports[0];
                } else {
                    serialPort = await navigator.serial.requestPort();
                }
                
                await serialPort.open({
                    baudRate: 115200,
                    dataBits: 8,
                    stopBits: 1,
                    parity: 'none'
                });
                
                serialWriter = serialPort.writable.getWriter();
                isPrinterConnected = true;
                
                updatePrinterStatus('Принтер подключен', 'connected');
                return true;
                
            } catch (error) {
                console.error('Ошибка подключения к принтеру:', error);
                updatePrinterStatus(`Ошибка: ${error.message}`, 'disconnected');
                return false;
            }
        }

        async function disconnectFromPrinter() {
            try {
                if (serialWriter) {
                    serialWriter.releaseLock();
                    serialWriter = null;
                }
                
                if (serialPort) {
                    await serialPort.close();
                    serialPort = null;
                }
                
                isPrinterConnected = false;
                updatePrinterStatus('Принтер отключен', 'disconnected');
                return true;
                
            } catch (error) {
                console.error('Ошибка отключения:', error);
                return false;
            }
        }

        async function sendRawData(data) {
            if (!isPrinterConnected || !serialWriter) {
                throw new Error('Сначала подключите принтер');
            }
            
            try {
                await serialWriter.write(data);
                return true;
            } catch (error) {
                console.error('Ошибка отправки данных:', error);
                throw error;
            }
        }

        function createPriceTagImage(product, type = 'regular') {
            const canvas = document.createElement('canvas');
            
            if (type === 'large') {
                canvas.width = 440;
                canvas.height = 300;
            } else {
                canvas.width = 440;
                canvas.height = 284;
            }
            
            const ctx = canvas.getContext('2d');
            
            ctx.fillStyle = 'white';
            ctx.fillRect(0, 0, canvas.width, canvas.height);
            
            ctx.fillStyle = 'black';
            ctx.textAlign = 'center';
            
            const textScale = 1.5;
            
            if (type === 'large') {
                const baseFonts = {
                    company: 22 * textScale,
                    article: 18 * textScale,
                    product: 16 * textScale,
                    price: 88 * textScale,
                    date: 14 * textScale
                };
                
                ctx.font = `bold ${baseFonts.company}px "Arial"`;
                ctx.fillText('ООО "КУБАНЬСТАР"', canvas.width / 2, 30);
                
                ctx.beginPath();
                ctx.moveTo(-8, 40);
                ctx.lineTo(canvas.width - 0, 40);
                ctx.lineWidth = 3;
                ctx.stroke();
                
                ctx.font = `bold ${baseFonts.article}px "Arial"`;
                ctx.textAlign = 'left';
                ctx.fillText(product.article, 6, 70);
                ctx.textAlign = 'right';
                const boxQty = product.boxQuantity || '0';
                ctx.fillText(`${boxQty} шт. в кор.`, canvas.width - 6, 70);
                
                ctx.beginPath();
                ctx.moveTo(-8, 80);
                ctx.lineTo(canvas.width - 0, 80);
                ctx.lineWidth = 3;
                ctx.stroke();
                ctx.lineWidth = 1;
                
                let productName = product.name;
                if (productName.length > 55) {
                    productName = productName.substring(0, 55) + '...';
                }
                
                ctx.font = `bold ${baseFonts.product}px "Arial"`;
                ctx.textAlign = 'center';
                
                const words = productName.split(' ');
                let line1 = '';
                let line2 = '';
                
                for (const word of words) {
                    if ((line1 + ' ' + word).length <= 25 && !line2) {
                        if (line1) line1 += ' ';
                        line1 += word;
                    } else {
                        if (line2) line2 += ' ';
                        line2 += word;
                    }
                }
                
                ctx.fillText(line1, canvas.width / 2, 110);
                if (line2) {
                    ctx.fillText(line2, canvas.width / 2, 135);
                }
                
                ctx.beginPath();
                ctx.moveTo(-8, 145);
                ctx.lineTo(canvas.width - 0, 145);
                ctx.lineWidth = 3;
                ctx.stroke();
                ctx.lineWidth = 1;
                
                const price = product.discountPrice && product.discountPrice.trim() !== '' 
                    ? product.discountPrice 
                    : product.wholesalePrice;
                
                const priceFormatted = formatNumber(price, true);
                
                ctx.font = `bold ${baseFonts.price}px "Arial"`;
                ctx.fillText(priceFormatted, canvas.width / 2, 255);
                
                ctx.beginPath();
                ctx.moveTo(-8, 271);
                ctx.lineTo(canvas.width - 0, 271);
                ctx.lineWidth = 3;
                ctx.stroke();
                
                const today = new Date();
                const dateStr = `${today.getDate().toString().padStart(2, '0')}.${(today.getMonth()+1).toString().padStart(2, '0')}.${today.getFullYear()}`;
                ctx.font = `${baseFonts.date}px "Arial"`;
                ctx.fillText(dateStr, canvas.width / 2, 291);
                
            } else {
                const baseFonts = {
                    company: 22 * textScale,
                    article: 18 * textScale,
                    product: 16 * textScale,
                    price: 44 * textScale,
                    date: 14 * textScale
                };
                
                ctx.font = `bold ${baseFonts.company}px "Arial"`;
                ctx.fillText('ООО "КУБАНЬСТАР"', canvas.width / 2, 30);
                
                ctx.beginPath();
                ctx.moveTo(-8, 40);
                ctx.lineTo(canvas.width - 0, 40);
                ctx.lineWidth = 3;
                ctx.stroke();
                
                ctx.font = `bold ${baseFonts.article}px "Arial"`;
                ctx.textAlign = 'left';
                ctx.fillText(product.article, 6, 70);
                ctx.textAlign = 'right';
                const boxQty = product.boxQuantity || '0';
                ctx.fillText(`${boxQty} шт. в кор.`, canvas.width - 6, 70);
                
                ctx.beginPath();
                ctx.moveTo(-8, 80);
                ctx.lineTo(canvas.width - 0, 80);
                ctx.lineWidth = 3;
                ctx.stroke();
                ctx.lineWidth = 1;
                
                let productName = product.name;
                if (productName.length > 55) {
                    productName = productName.substring(0, 55) + '...';
                }
                
                ctx.font = `bold ${baseFonts.product}px "Arial"`;
                ctx.textAlign = 'center';
                
                const words = productName.split(' ');
                let line1 = '';
                let line2 = '';
                
                for (const word of words) {
                    if ((line1 + ' ' + word).length <= 25 && !line2) {
                        if (line1) line1 += ' ';
                        line1 += word;
                    } else {
                        if (line2) line2 += ' ';
                        line2 += word;
                    }
                }
                
                ctx.fillText(line1, canvas.width / 2, 110);
                if (line2) {
                    ctx.fillText(line2, canvas.width / 2, 135);
                }
                
                ctx.beginPath();
                ctx.moveTo(-8, 155);
                ctx.lineTo(canvas.width - 0, 155);
                ctx.lineWidth = 3;
                ctx.stroke();
                ctx.lineWidth = 1;
                
                const price = product.discountPrice && product.discountPrice.trim() !== '' 
                    ? product.discountPrice 
                    : product.wholesalePrice;
                
                const priceFormatted = formatNumber(price, true);
                
                ctx.font = `bold ${baseFonts.price}px "Arial"`;
                ctx.fillText(`${priceFormatted} Руб.`, canvas.width / 2, 207 + 12);
                
                ctx.beginPath();
                ctx.moveTo(-8, 225 + 12);
                ctx.lineTo(canvas.width - 0, 225 + 12);
                ctx.lineWidth = 3;
                ctx.stroke();
                
                const today = new Date();
                const dateStr = `${today.getDate().toString().padStart(2, '0')}.${(today.getMonth()+1).toString().padStart(2, '0')}.${today.getFullYear()}`;
                ctx.font = `${baseFonts.date}px "Arial"`;
                ctx.fillText(dateStr, canvas.width / 2, 245 + 20);
            }
            
            return canvas;
        }

        function canvasToEscPosBitmap(canvas) {
            const ctx = canvas.getContext('2d');
            const imageData = ctx.getImageData(0, 0, canvas.width, canvas.height);
            const data = imageData.data;
            const width = canvas.width;
            const height = canvas.height;
            
            const bytesPerLine = Math.ceil(width / 8);
            const bitmap = new Uint8Array(bytesPerLine * height);
            
            for (let y = 0; y < height; y++) {
                for (let x = 0; x < width; x++) {
                    const pixelIndex = (y * width + x) * 4;
                    const r = data[pixelIndex];
                    const g = data[pixelIndex + 1];
                    const b = data[pixelIndex + 2];
                    
                    const isBlack = (r + g + b) < 384;
                    
                    if (isBlack) {
                        const byteIndex = y * bytesPerLine + Math.floor(x / 8);
                        const bitPosition = 7 - (x % 8);
                        bitmap[byteIndex] |= (1 << bitPosition);
                    }
                }
            }
            
            return {
                data: bitmap,
                width: width,
                height: height,
                bytesPerLine: bytesPerLine
            };
        }

        function createEscPosImageCommand(bitmap) {
            const width = bitmap.width;
            const height = bitmap.height;
            const bytesPerLine = bitmap.bytesPerLine;
            
            const command = new Uint8Array(bitmap.data.length + 8);
            
            command[0] = 0x1D; // GS
            command[1] = 0x76; // v
            command[2] = 0x30; // 0
            command[3] = 0x00; // m = 0
            
            const xL = bytesPerLine & 0xFF;
            const xH = (bytesPerLine >> 8) & 0xFF;
            command[4] = xL;
            command[5] = xH;
            
            const yL = height & 0xFF;
            const yH = (height >> 8) & 0xFF;
            command[6] = yL;
            command[7] = yH;
            
            command.set(bitmap.data, 8);
            
            return command;
        }

        async function printPriceTag(product, type = 'regular') {
            try {
                if (!isPrinterConnected) {
                    const connected = await connectToPrinter();
                    if (!connected) {
                        throw new Error('Не удалось подключиться к принтеру');
                    }
                }
                
                const canvas = createPriceTagImage(product, type);
                const bitmap = canvasToEscPosBitmap(canvas);
                const imageCommand = createEscPosImageCommand(bitmap);
                
                const leftMargin = 0;
                
                const fullCommand = new Uint8Array(imageCommand.length + 10);
                
                fullCommand[0] = 0x1B; // ESC
                fullCommand[1] = 0x40; // @
                
                fullCommand[2] = 0x1B; // ESC
                fullCommand[3] = 0x6C; // l
                fullCommand[4] = leftMargin;
                
                fullCommand.set(imageCommand, 5);
                
                const imageEnd = 5 + imageCommand.length;
                fullCommand[imageEnd] = 0x0A; // LF
                fullCommand[imageEnd + 1] = 0x0A; // LF
                
                await sendRawData(fullCommand);
                return true;
                
            } catch (error) {
                console.error('Ошибка печати:', error);
                throw error;
            }
        }

        function updatePriceTagPreview(product, type = 'regular') {
            const canvas = document.getElementById('priceTagPreviewCanvas');
            const ctx = canvas.getContext('2d');
            
            ctx.fillStyle = 'white';
            ctx.fillRect(0, 0, canvas.width, canvas.height);
            
            if (type === 'large') {
                canvas.width = 440;
                canvas.height = 300;
            } else {
                canvas.width = 440;
                canvas.height = 284;
            }
            
            const scale = 0.7;
            ctx.save();
            ctx.scale(scale, scale);
            
            const previewCanvas = createPriceTagImage(product, type);
            ctx.drawImage(previewCanvas, 0, 0);
            
            ctx.restore();
        }

        function showPrintStatus(message, type = 'info') {
            const statusEl = document.getElementById('printStatus');
            statusEl.textContent = message;
            statusEl.className = 'print-status ' + type;
            statusEl.style.display = 'block';
            
            setTimeout(() => {
                statusEl.style.display = 'none';
            }, 5000);
        }

        async function openPrintModal(product) {
            currentProductForPrint = product;
            document.getElementById('printModal').style.display = 'flex';
            
            currentPriceTagType = 'regular';
            document.querySelectorAll('.price-tag-type-option').forEach(option => {
                if (option.getAttribute('data-type') === 'regular') {
                    option.classList.add('selected');
                    option.querySelector('input').checked = true;
                } else {
                    option.classList.remove('selected');
                }
            });
            
            updatePriceTagPreview(product, 'regular');
            
            const printBtn = document.getElementById('printActionBtn');
            printBtn.disabled = true;
            printBtn.textContent = 'Подключаюсь к принтеру...';
            
            try {
                const connected = await connectToPrinter();
                
                if (connected) {
                    printBtn.disabled = false;
                    printBtn.textContent = 'Распечатать';
                } else {
                    printBtn.disabled = true;
                    printBtn.textContent = 'Не удалось подключиться';
                }
            } catch (error) {
                console.error('Ошибка при подключении:', error);
                printBtn.disabled = true;
                printBtn.textContent = 'Ошибка подключения';
            }
        }

        function closePrintModal() {
            document.getElementById('printModal').style.display = 'none';
            
            setTimeout(() => {
                disconnectFromPrinter();
            }, 1000);
        }

        async function handlePrint() {
            if (!currentProductForPrint) {
                showPrintStatus('Товар не выбран', 'error');
                return;
            }
            
            const printBtn = document.getElementById('printActionBtn');
            printBtn.disabled = true;
            printBtn.textContent = 'Печатаю...';
            
            try {
                await printPriceTag(currentProductForPrint, currentPriceTagType);
                showPrintStatus('Ценник успешно отправлен на печать!', 'success');
                
                setTimeout(() => {
                    closePrintModal();
                }, 1500);
                
            } catch (error) {
                console.error('Ошибка печати:', error);
                showPrintStatus('Ошибка печати: ' + error.message, 'error');
                printBtn.disabled = false;
                printBtn.textContent = 'Распечатать';
            }
        }

        function setupPriceTagTypeSelector() {
            const options = priceTagTypeSelector.querySelectorAll('.price-tag-type-option');
            
            options.forEach(option => {
                option.addEventListener('click', function() {
                    options.forEach(opt => opt.classList.remove('selected'));
                    
                    this.classList.add('selected');
                    
                    const radio = this.querySelector('input[type="radio"]');
                    radio.checked = true;
                    
                    currentPriceTagType = this.getAttribute('data-type');
                    
                    if (currentProductForPrint) {
                        updatePriceTagPreview(currentProductForPrint, currentPriceTagType);
                    }
                });
            });
            
            const radios = priceTagTypeSelector.querySelectorAll('input[type="radio"]');
            radios.forEach(radio => {
                radio.addEventListener('change', function() {
                    if (this.checked) {
                        const option = this.closest('.price-tag-type-option');
                        currentPriceTagType = option.getAttribute('data-type');
                        
                        if (currentProductForPrint) {
                            updatePriceTagPreview(currentProductForPrint, currentPriceTagType);
                        }
                    }
                });
            });
        }

        // ===== ФУНКЦИИ ДЛЯ ДАТ =====
        function getFileDatesData() {
            let displayDate = "Дата не указана";
            if (DATA_UPDATE_DATE) {
                if (DATA_UPDATE_DATE.includes(" ")) {
                    displayDate = DATA_UPDATE_DATE.split(" ")[0];
                } else {
                    displayDate = DATA_UPDATE_DATE;
                }
            }
            
            return {
                currentDate: displayDate,
                files: [
                    {
                        location: "Уральская",
                        items: [
                            {
                                label: "Офис",
                                lastModified: URAL_OFFICE_DATE || "Дата не указана"
                            },
                            {
                                label: "Уральская",
                                lastModified: URAL_DATE || "Дата не указана"
                            }
                        ]
                    },
                    {
                        location: "Шевченко",
                        items: [
                            {
                                label: "Офис",
                                lastModified: SHEVCHENKO_OFFICE_DATE || "Дата не указана"
                            },
                            {
                                label: "Шевченко",
                                lastModified: SHEVCHENKO_DATE || "Дата не указана"
                            }
                        ]
                    }
                ]
            };
        }

        function openDatesModal() {
            const data = getFileDatesData();
            const modal = document.getElementById('datesModal');
            const content = document.getElementById('datesContent');
            const modalCurrentDate = document.getElementById('modalCurrentDate');
            const dataUpdateContainer = document.getElementById('dataUpdateContainer');
            
            modalCurrentDate.textContent = `Дата обновления: ${data.currentDate}`;
            
            let updateTime = "00:00";
            
            if (DATA_UPDATE_DATE && DATA_UPDATE_DATE.includes(" ")) {
                const timeMatch = DATA_UPDATE_DATE.match(/\s(\d{2}:\d{2})$/);
                if (timeMatch && timeMatch[1]) {
                    updateTime = timeMatch[1];
                }
            } else {
                const now = new Date();
                updateTime = now.getHours().toString().padStart(2, '0') + ':' + 
                            now.getMinutes().toString().padStart(2, '0');
            }
            
            dataUpdateContainer.textContent = `Данные на : ${updateTime}`;
            
            let html = '';
            
            if (data.files && data.files.length > 0) {
                data.files.forEach(section => {
                    html += `<div class="date-section">
                        <div class="date-section-title">${section.location}:</div>`;
                    
                    section.items.forEach(item => {
                        html += `<div class="date-item">
                            <div class="date-item-row">
                                <div class="date-item-label">${item.label}:</div>
                                <div class="date-item-time">${item.lastModified}</div>
                            </div>
                        </div>`;
                    });
                    
                    html += `</div>`;
                });
            } else {
                html += `<div class="no-dates-info">Информация о датах изменения файлов отсутствует</div>`;
            }
            
            content.innerHTML = html;
            modal.style.display = 'flex';
        }

        function closeDatesModal() {
            document.getElementById('datesModal').style.display = 'none';
        }

        // ===== ИНИЦИАЛИЗАЦИЯ =====
        const searchInput = document.getElementById('searchInput');
        const clearSearchBtn = document.getElementById('clearSearchBtn');
        const searchButton = document.getElementById('searchButton');
        const scanButton = document.getElementById('scanButton');
        const resultsContainer = document.getElementById('resultsContainer');
        const searchModeRadios = document.querySelectorAll('input[name="searchMode"]');
        const combinedSearchFields = document.getElementById('combinedSearchFields');
        const articleInput = document.getElementById('articleInput');
        const nameInput = document.getElementById('nameInput');
        const barcodeInput = document.getElementById('barcodeInput');
        
        const cameraModal = document.getElementById('cameraModal');
        const resultModal = document.getElementById('resultModal');
        const printModal = document.getElementById('printModal');
        const datesModal = document.getElementById('datesModal');
        const closeCameraModal = document.getElementById('closeCameraModal');
        const closePrintModalBtn = document.getElementById('closePrintModal');
        const closeDatesModalBtn = document.getElementById('closeDatesModal');
        const closeIosScannerBtn = document.getElementById('closeIosScanner');
        
        const cameraVideo = document.getElementById('cameraVideo');
        const stopCameraBtn = document.getElementById('stopCamera');
        
        const resultCount = document.getElementById('resultCount');
        const resultProducts = document.getElementById('resultProducts');
        const continueScanBtn = document.getElementById('continueScanBtn');
        const closeResultBtn = document.getElementById('closeResultBtn');

        const printActionBtn = document.getElementById('printActionBtn');
        const priceTagTypeSelector = document.getElementById('priceTagTypeSelector');

        function initScrollToTopButton() {
            const scrollToTopBtn = document.getElementById('scrollToTopBtn');
            
            window.addEventListener('scroll', function() {
                if (window.pageYOffset > 300) {
                    scrollToTopBtn.classList.add('show');
                } else {
                    scrollToTopBtn.classList.remove('show');
                }
            });
            
            scrollToTopBtn.addEventListener('click', function() {
                window.scrollTo({
                    top: 0,
                    behavior: 'smooth'
                });
            });
        }

        // ===== ОБРАБОТЧИКИ СОБЫТИЙ =====
        searchButton.addEventListener('click', searchProducts);
        clearSearchBtn.addEventListener('click', clearSearchFields);
        scanButton.addEventListener('click', openScanner);

        searchInput.addEventListener('input', updateClearButton);
        articleInput.addEventListener('input', updateClearButton);
        nameInput.addEventListener('input', updateClearButton);
        barcodeInput.addEventListener('input', updateClearButton);

        searchInput.addEventListener('keydown', function(e) {
            if (e.key === 'Enter') searchProducts();
        });

        articleInput.addEventListener('keydown', function(e) {
            if (e.key === 'Enter') searchProducts();
        });

        nameInput.addEventListener('keydown', function(e) {
            if (e.key === 'Enter') searchProducts();
        });

        barcodeInput.addEventListener('keydown', function(e) {
            if (e.key === 'Enter') searchProducts();
        });

        closeCameraModal.addEventListener('click', function() {
            closeScanner();
        });

        cameraModal.addEventListener('click', function(e) {
            if (e.target === cameraModal) {
                closeScanner();
            }
        });

        stopCameraBtn.addEventListener('click', function() {
            closeScanner();
        });

        closeIosScannerBtn.addEventListener('click', function() {
            closeIosScanner();
        });

        continueScanBtn.addEventListener('click', function() {
            resultModal.style.display = 'none';
            setTimeout(() => {
                openScanner();
            }, 300);
        });

        closeResultBtn.addEventListener('click', function() {
            resultModal.style.display = 'none';
        });

        resultModal.addEventListener('click', function(e) {
            if (e.target === resultModal) resultModal.style.display = 'none';
        });

        closePrintModalBtn.addEventListener('click', closePrintModal);
        printModal.addEventListener('click', function(e) {
            if (e.target === printModal) closePrintModal();
        });

        closeDatesModalBtn.addEventListener('click', closeDatesModal);
        datesModal.addEventListener('click', function(e) {
            if (e.target === datesModal) closeDatesModal();
        });

        printActionBtn.addEventListener('click', handlePrint);

        document.getElementById('current-date').addEventListener('click', openDatesModal);

        searchModeRadios.forEach(radio => {
            radio.addEventListener('change', function() {
                updateSearchUI();
                if (searchInput.value.trim() || 
                    (getCurrentSearchMode() === 'combined' && 
                     (articleInput.value.trim() || nameInput.value.trim() || barcodeInput.value.trim()))) {
                    searchProducts();
                }
            });
        });

        window.addEventListener('load', function() {
            document.getElementById('modeArticle').checked = true;
            updateSearchUI();
            searchInput.focus();
            setupPlatformUI();
            initScrollToTopButton();
            setupPriceTagTypeSelector();

            if (DATA_UPDATE_DATE && DATA_UPDATE_DATE.trim() !== "") {
                let displayDate = DATA_UPDATE_DATE;
                if (DATA_UPDATE_DATE.includes(" ")) {
                    displayDate = DATA_UPDATE_DATE.split(" ")[0];
                }
                document.getElementById('current-date').textContent = displayDate;
            }
        });

        searchInput.addEventListener('keydown', function(e) {
            if (e.key === 'Escape') clearSearchFields();
        });

        document.addEventListener('keydown', function(e) {
            if ((e.ctrlKey || e.metaKey) && e.key === 'f') {
                e.preventDefault();
                const mode = getCurrentSearchMode();
                if (mode === 'combined') {
                    articleInput.focus();
                } else {
                    searchInput.focus();
                    searchInput.select();
                }
            }
        });
    </script>
</body>
</html>