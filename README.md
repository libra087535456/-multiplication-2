<html lang="zh-TW">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>【最終定稿】動態被乘數乘法比較學習工具</title>
    <style>
        /* CSS 樣式 (維持不變) */
        body {
            font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
            margin: 20px;
            text-align: center;
            background-color: #f4f7f6;
            color: #333;
        }

        h1 {
            color: #008000;
        }

        .container {
            max-width: 1100px;
            margin: 0 auto;
            padding: 20px;
            background-color: #ffffff;
            border-radius: 12px;
            box-shadow: 0 4px 12px rgba(0, 0, 0, 0.1);
        }

        /* 頂部控制區 */
        .top-controls {
            display: flex;
            justify-content: space-between;
            align-items: center;
            margin-bottom: 20px;
            flex-wrap: wrap;
        }
        
        .mode-selector-group {
            display: flex;
            align-items: center;
            gap: 15px; 
            flex-wrap: wrap;
            justify-content: flex-start;
        }

        .mode-button, .multiplicand-button {
            padding: 10px 15px;
            background-color: #f7e1b5;
            border: 2px solid #e0b445;
            border-radius: 8px;
            cursor: pointer;
            font-weight: bold;
            color: #333;
            transition: background-color 0.3s, border-color 0.3s;
            white-space: nowrap;
        }
        .mode-button:hover, .multiplicand-button:hover {
            background-color: #f5d693;
        }
        .multiplicand-button.active {
            background-color: #e0b445;
            border-color: #c99c2d;
            color: #fff;
            box-shadow: 0 2px 5px rgba(0,0,0,0.2);
        }

        /* 被乘數概念提示 */
        .concept {
            font-size: 1.1em;
            font-weight: bold;
            color: #555;
            margin-right: 20px;
            flex-shrink: 0;
        }

        /* 滑桿控制區 */
        .controls {
            display: flex;
            justify-content: space-around;
            margin-bottom: 30px;
            padding: 15px;
            border: 1px solid #e0e0e0;
            border-radius: 8px;
            background-color: #fcfcfc;
        }

        .slider-group {
            flex-basis: 45%;
            text-align: left;
            padding: 0 15px;
        }

        /* 乘法視覺化區 */
        .row {
            display: flex;
            align-items: flex-start;
            margin-bottom: 30px;
        }
        
        .label {
            width: 150px;
            font-size: 1.2em;
            font-weight: bold;
            color: #333;
            text-align: right;
            padding-right: 25px;
            padding-top: 10px;
            flex-shrink: 0;
        }

        .clover-container {
            display: flex;
            flex-wrap: wrap;
            align-items: center;
            padding: 5px 0;
            min-height: 80px; 
            flex-grow: 1;
        }
        .clover-plant { 
            display: inline-block;
            margin: 0 10px;
            text-align: center;
            line-height: 1.2;
            flex-shrink: 0;
        }
        .clover-icon {
            font-size: 3em; 
            display: block;
        }
        
        /* --- 抽象模式數字標籤樣式 --- */
        .leaf-cumulative-count {
            font-size: 1.1em;
            font-weight: bold;
            color: #008000;
            margin-top: -5px;
            display: block;
            border: 2px solid #a8e0a8;
            border-radius: 50%;
            width: 30px;
            height: 30px;
            line-height: 26px;
            margin: -5px auto 0;
            background-color: #f0fff0;
            cursor: pointer; 
        }
        
        /* 抽象模式：紅色部分樣式 (局部累積式/總累積數) */
        .extra-plant .leaf-cumulative-count {
            width: auto;
            min-width: 60px; 
            height: auto;
            line-height: 1.2;
            padding: 5px 2px;
            border-radius: 10px; 
            border-color: #ff0000;
            color: #ff0000;
            background-color: #fffafa;
            animation: pulse 1.5s infinite;
            font-size: 0.9em;
            margin: -5px auto 0;
        }
        
        /* --- 細節模式樣式 --- */
        .detail-plant-group { 
            display: flex;
            flex-direction: column;
            margin: 0 10px;
            align-items: center;
            flex-shrink: 0;
            position: relative;
        }
        .clover-group-icon {
            font-size: 3em;
            line-height: 1;
            margin-bottom: 5px;
        }
        .leaf-unit-row { 
            display: flex;
            justify-content: center;
            flex-wrap: wrap;
        }
        
        .leaf-unit-container { 
            display: flex;
            flex-direction: column;
            align-items: center;
            margin: 0 2px;
            min-height: 50px;
            position: relative;
        }
        
        .leaf-unit-circle {
            display: block;
            width: 25px;
            min-width: 25px;
            height: 25px;
            line-height: 25px;
            border-radius: 50%;
            border: 1px solid #005500;
            background-color: #ccffcc;
            color: #005500;
            padding: 0;
            font-size: 0.8em;
            cursor: pointer; 
            margin-bottom: 2px; 
        }

        /* 差異計數數字樣式 */
        .difference-count-label {
            font-size: 0.8em;
            font-weight: bold;
            color: #ff0000;
            opacity: 0;
            transition: opacity 0.3s;
            margin-top: 2px;
            white-space: nowrap;
        }
        
        /* 細節模式下：多出來的部分樣式 (紅色邊框/文字，預設的粉紅底) */
        .extra-plant .leaf-unit-circle {
            border-color: #ff0000; 
            color: #ff0000; 
            background-color: #ffe0e0;
        }

        /* 細節模式：點擊後多出來的部分 (紅點) 變成淺紅底色和黑字 */
        .extra-plant.active-diff .leaf-unit-circle {
            background-color: #ffcccc; 
            color: #000000; 
            border-color: #ff0000;
        }

        /* 細節模式：點擊後顯示差異計數 */
        .extra-plant.active-diff .difference-count-label {
            opacity: 1; 
        }

        /* --- 結果區塊樣式 (最終定稿) --- */
        .result-box {
            background-color: #e0e0e0;
            padding: 20px;
            border-radius: 8px;
            margin-top: 30px;
            border: 2px solid #ccc;
            font-size: 1.8em;
            font-weight: bold;
            color: #000000;
            line-height: 1.5;
        }

        /* 乘數顏色：nA (藍色), nB (紅色) */
        .highlight-a { color: #007bff; font-weight: bolder; background-color: #ffffff; padding: 0 5px; border-radius: 4px; }
        .highlight-b { color: #ff0000; font-weight: bolder; background-color: #ffffff; padding: 0 5px; border-radius: 4px; }
        
        /* 差異數字 (紅字) */
        .highlight-diff { color: #ff0000; font-weight: bolder; background-color: transparent; }

        /* 差異個數邊框 (框住 "X 個 4") */
        .highlight-framed {
            border: 2px solid #ff0000;
            padding: 0 5px;
            border-radius: 8px;
            margin: 0 5px;
        }

        /* 動畫 */
        @keyframes pulse {
            0% { transform: scale(1); opacity: 0.8; }
            50% { transform: scale(1.05); opacity: 1; }
            100% { transform: scale(1); opacity: 0.8; }
        }
    </style>
</head>
<body>

    <div class="container">
        <h1>🔢 動態被乘數乘法比較學習工具</h1>
        
        <div class="top-controls">
            <div class="mode-selector-group">
                <button class="multiplicand-button" id="multiplicand-2" onclick="setMultiplicand(2)">
                    🍒 櫻桃 (2)
                </button>
                <button class="multiplicand-button active" id="multiplicand-4" onclick="setMultiplicand(4)">
                    🍀 幸運草 (4)
                </button>
                <button class="multiplicand-button" id="multiplicand-5" onclick="setMultiplicand(5)">
                    🌸 花朵 (5)
                </button>
                <button class="multiplicand-button" id="multiplicand-8" onclick="setMultiplicand(8)">
                    🐙 章魚 (8)
                </button>
            </div>
            
            <button class="mode-button" id="toggle-mode-button" onclick="toggleMode()">
                切換至「細節模式」
            </button>
        </div>

        <div class="concept">
            概念：每 <span class="highlight-diff" id="current-multiplicand-text">一株</span> <span class="highlight-diff" id="current-item-name">幸運草</span> 有 <span class="highlight-diff" id="current-leaves-count">4</span> <span id="concept-total-unit">片葉子</span>。
        </div>

        <div class="controls">
            <div class="slider-group">
                <h2>要比較的數量 A：</h2>
                <input type="range" id="multiplier-a" min="2" max="10" value="7" oninput="updateVisualization()">
                <span id="value-a" style="font-weight: bold; font-size: 1.2em;">7 株</span>
            </div>
            <div class="slider-group">
                <h2>要比較的數量 B：</h2>
                <input type="range" id="multiplier-b" min="1" max="9" value="4" oninput="updateVisualization()">
                <span id="value-b" style="font-weight: bold; font-size: 1.2em;">4 株</span>
            </div>
        </div>

        <div class="visualization">
            <div class="row" id="row-a">
                <div class="label" id="label-a"></div>
                <div class="clover-container" id="clover-a"></div>
            </div>

            <div class="row" id="row-b">
                <div class="label" id="label-b"></div>
                <div class="clover-container" id="clover-b"></div>
            </div>
        </div>
        
        <div class="result-box">
            <span id="explanation">請調整上方的滑桿，看看多幾株會多幾片葉子。</span>
        </div>
    </div>

    <script>
        // JavaScript 程式碼
        const sliderA = document.getElementById('multiplier-a');
        const sliderB = document.getElementById('multiplier-b');
        const valueASpan = document.getElementById('value-a');
        const valueBSpan = document.getElementById('value-b');
        const labelA = document.getElementById('label-a');
        const cloverA = document.getElementById('clover-a');
        const labelB = document.getElementById('label-b');
        const cloverB = document.getElementById('clover-b');
        const explanation = document.getElementById('explanation');
        const toggleButton = document.getElementById('toggle-mode-button');
        const multiplicandButtons = document.querySelectorAll('.multiplicand-button');
        const currentMultiplicandText = document.getElementById('current-multiplicand-text');
        const currentItemName = document.getElementById('current-item-name');
        const currentLeavesCount = document.getElementById('current-leaves-count');
        const conceptTotalUnit = document.getElementById('concept-total-unit'); // 新增的概念單位 DOM


        let CURRENT_MULTIPLIER = 4;
        let CURRENT_EMOJI = '🍀';
        let CURRENT_ITEM_NAME = '幸運草';
        let CURRENT_ITEM_UNIT = '株'; 
        let CURRENT_TOTAL_UNIT = '片葉子'; 
        
        let detailMode = false;
        let showLocalAccumulation = false;
        let showDifferenceCount = false; 

        // 被乘數定義
        const multiplicands = {
            2: { emoji: '🍒', name: '櫻桃', unit: '串', totalUnit: '顆櫻桃' },
            4: { emoji: '🍀', name: '幸運草', unit: '株', totalUnit: '片葉子' },
            5: { emoji: '🌸', name: '花朵', unit: '朵', totalUnit: '片花瓣' },
            8: { emoji: '🐙', name: '章魚', unit: '隻', totalUnit: '條腳' }
        };

        /**
         * 根據項目名稱獲取群組量詞
         */
        function getItemUnit(name) {
            switch(name) {
                case '花朵': return '朵';
                case '章魚': return '隻'; 
                case '櫻桃': return '串';
                default: return '株'; // 幸運草
            }
        }

        /**
         * 設置被乘數
         */
        window.setMultiplicand = function(value) {
            const data = multiplicands[value];
            
            CURRENT_MULTIPLIER = value;
            CURRENT_EMOJI = data.emoji;
            CURRENT_ITEM_NAME = data.name;
            CURRENT_ITEM_UNIT = getItemUnit(data.name);
            CURRENT_TOTAL_UNIT = data.totalUnit; // 總數單位 (顆櫻桃, 片葉子, 片花瓣, 條腳)

            // 更新按鈕的 active 狀態
            multiplicandButtons.forEach(btn => {
                if (parseInt(btn.id.split('-')[1]) === value) {
                    btn.classList.add('active');
                } else {
                    btn.classList.remove('active');
                }
            });

            // --- 概念提示區塊修正 ---
            currentMultiplicandText.textContent = `一${CURRENT_ITEM_UNIT}`;
            currentItemName.textContent = CURRENT_ITEM_NAME;
            currentLeavesCount.textContent = CURRENT_MULTIPLIER;
            conceptTotalUnit.textContent = CURRENT_TOTAL_UNIT; // 關鍵修正：替換 "個單位"
            
            updateVisualization();
        }

        /**
         * 切換顯示模式 (抽象/細節)
         */
        function toggleMode() {
            detailMode = !detailMode;
            if (detailMode) {
                toggleButton.textContent = "切換回「抽象模式」";
                showDifferenceCount = false; 
            } else {
                toggleButton.textContent = "切換至「細節模式」";
                showLocalAccumulation = false; 
            }
            updateVisualization();
        }

        /**
         * 抽象模式下，點擊切換紅色的數字顯示狀態
         */
        window.toggleAccumulation = function() {
            if (detailMode) return;
            showLocalAccumulation = !showLocalAccumulation;
            updateVisualization();
        }
        
        /**
         * 細節模式下，點擊切換差異計數顯示狀態
         */
        window.toggleDifferenceDisplay = function(event) {
            if (!detailMode || !event.currentTarget.closest('.extra-plant')) return;
            
            showDifferenceCount = !document.querySelector('.detail-plant-group.extra-plant.active-diff');

            const extraPlants = document.querySelectorAll('.detail-plant-group.extra-plant');
            extraPlants.forEach(plant => {
                if (showDifferenceCount) {
                    plant.classList.add('active-diff');
                } else {
                    plant.classList.remove('active-diff');
                }
            });
        }
        
        /**
         * 根據滑桿值更新視覺化內容
         */
        function updateVisualization() {
            let nA = parseInt(sliderA.value);
            let nB = parseInt(sliderB.value);

            if (nA <= nB) {
                if (nB < 10) { 
                    nA = nB + 1; 
                } else {
                    nA = 10;
                    nB = 9;
                }
                sliderA.value = nA;
                sliderB.value = nB;
            }

            valueASpan.textContent = `${nA} ${CURRENT_ITEM_UNIT}`;
            valueBSpan.textContent = `${nB} ${CURRENT_ITEM_UNIT}`;
            
            const totalUnitsA = CURRENT_MULTIPLIER * nA;
            const totalUnitsB = CURRENT_MULTIPLIER * nB;

            const coloredNA = `<span class="highlight-a">${nA}</span>`;
            const coloredNB = `<span class="highlight-b">${nB}</span>`;

            // 乘法標籤使用 CURRENT_TOTAL_UNIT
            labelA.innerHTML = `${CURRENT_MULTIPLIER} × ${coloredNA} = <br>${totalUnitsA} ${CURRENT_TOTAL_UNIT}`;
            const generator = detailMode ? generateDetailedClovers : generateAbstractClovers;
            cloverA.innerHTML = generator(nA, nB);

            labelB.innerHTML = `${CURRENT_MULTIPLIER} × ${coloredNB} = <br>${totalUnitsB} ${CURRENT_TOTAL_UNIT}`;
            cloverB.innerHTML = generator(nB, nB); 

            const placeholderHtml = getPlaceholderClovers(nA - nB, detailMode);
            cloverB.innerHTML += placeholderHtml;

            updateExplanation(nA, nB);
            
            if (detailMode) {
                showDifferenceCount = false; 
                setTimeout(() => {
                    const extraElement = document.querySelector('.extra-plant .leaf-unit-circle');
                    if (extraElement) {
                        window.toggleDifferenceDisplay({currentTarget: extraElement});
                    }
                }, 0);
            }
        }

        /**
         * 抽象模式繪圖 (單一圖案 + 累計數字 / 局部累積式)
         */
        function generateAbstractClovers(count, startHighlight) {
            let html = '';
            let cumulativeTotal = 0; 
            let localPlantIndex = 0; 

            for (let i = 0; i < count; i++) {
                const plantIndex = i + 1;
                cumulativeTotal = plantIndex * CURRENT_MULTIPLIER; 
                
                const isExtraPlant = i >= startHighlight; 
                const plantClass = isExtraPlant ? 'clover-plant extra-plant' : 'clover-plant';
                
                let labelContent;
                let onClickEvent = '';

                if (isExtraPlant) {
                    localPlantIndex++;
                    const localTotal = localPlantIndex * CURRENT_MULTIPLIER;
                    onClickEvent = 'onclick="toggleAccumulation()"'; 
                    
                    if (showLocalAccumulation) {
                        labelContent = `${CURRENT_MULTIPLIER} × ${localPlantIndex} = ${localTotal}`;
                    } else {
                        labelContent = `${cumulativeTotal}`;
                    }
                } else {
                    labelContent = `${cumulativeTotal}`;
                    onClickEvent = 'onclick="" style="cursor: default;"'; 
                }

                html += `<div class="${plantClass}" title="累積 ${cumulativeTotal} ${CURRENT_TOTAL_UNIT}">`;
                html += `<span class="clover-icon">${CURRENT_EMOJI}</span>`;
                html += `<span class="leaf-cumulative-count" ${onClickEvent}>${labelContent}</span>`;
                html += `</div>`;
            }
            return html;
        }

        /**
         * 細節模式繪圖 (圖案 + N 個小圓點 + 連續數字)
         */
        function generateDetailedClovers(count, startHighlight) {
            let html = '';
            let cumulativeTotal = 0;
            let differenceCount = 0; 

            for (let i = 0; i < count; i++) {
                const isExtraPlant = i >= startHighlight;
                const plantClass = isExtraPlant ? 'detail-plant-group extra-plant' : 'detail-plant-group';
                
                const onClickEvent = isExtraPlant ? 'onclick="toggleDifferenceDisplay(event)"' : 'onclick="" style="cursor: default;"';
                
                html += `<div class="${plantClass}">`;
                
                html += `<span class="clover-group-icon">${CURRENT_EMOJI}</span>`;
                
                html += `<div class="leaf-unit-row">`;
                for (let j = 0; j < CURRENT_MULTIPLIER; j++) {
                    cumulativeTotal++;
                    
                    if (isExtraPlant) {
                        differenceCount++;
                    }

                    html += `<div class="leaf-unit-container">`;
                    html += `<span class="leaf-unit-circle" ${onClickEvent}>${cumulativeTotal}</span>`;
                    
                    if (isExtraPlant) {
                        html += `<span class="difference-count-label">(${differenceCount})</span>`;
                    }
                    html += `</div>`;
                }
                html += `</div>`;
                
                html += `</div>`;
            }
            return html;
        }


        /**
         * 產生空白佔位符，用於對齊
         */
        function getPlaceholderClovers(count, isDetailMode) {
            let html = '';
            const placeholderStyle = "opacity: 0; visibility: hidden;";
            
            for (let i = 0; i < count; i++) {
                if (isDetailMode) {
                    const placeholderClass = 'detail-plant-group';
                    html += `<div class="${placeholderClass}" style="${placeholderStyle}">`;
                    html += `<span class="clover-group-icon">${CURRENT_EMOJI}</span>`;
                    html += `<div class="leaf-unit-row">`;
                    for(let j=0; j<CURRENT_MULTIPLIER; j++) {
                        html += `<div class="leaf-unit-container"><span class="leaf-unit-circle">0</span><span class="difference-count-label">(0)</span></div>`;
                    }
                    html += `</div>`;
                    html += `</div>`;
                } else {
                    const placeholderClass = 'clover-plant';
                    html += `<div class="${placeholderClass}" style="${placeholderStyle}">`;
                    html += `<span class="clover-icon">${CURRENT_EMOJI}</span>`;
                    html += `<span class="leaf-cumulative-count">0</span>`;
                    html += `</div>`;
                }
            }
            return html;
        }
        
        /**
         * 產生結果解釋文字
         */
        function updateExplanation(nA, nB) {
            const diffPlants = nA - nB;
            const diffUnits = diffPlants * CURRENT_MULTIPLIER;

            const coloredNA = `<span class="highlight-a">${nA}</span>`;
            const coloredNB = `<span class="highlight-b">${nB}</span>`;
            
            const coloredDiffPlants = `<span class="highlight-diff">${diffPlants}</span>`;
            const coloredMultiplicand = `<span class="highlight-diff">${CURRENT_MULTIPLIER}</span>`;
            const coloredDiffUnits = `<span class="highlight-diff">${diffUnits}</span>`;

            const framedDifference = `<span class="highlight-framed">${coloredDiffPlants} 個 ${coloredMultiplicand}</span>`;

            const text = 
                `比較：${CURRENT_MULTIPLIER} × ${coloredNA} 比 ${CURRENT_MULTIPLIER} × ${coloredNB} 多了 ${framedDifference}，` +
                `也就是多了 ${coloredDiffUnits} ${CURRENT_TOTAL_UNIT}！`; 
            explanation.innerHTML = text;
            
            if (nA === 7 && nB === 4 && CURRENT_ITEM_NAME === '幸運草') {
                // 恢復初始狀態的提示文字
                explanation.innerHTML = '請調整上方的滑桿，看看多幾株會多幾片葉子。';
            }
        }

        // 初始載入時，更新一次
        document.addEventListener('DOMContentLoaded', () => {
            // 預設選中幸運草 (4)
            setMultiplicand(4); 
            updateVisualization();
        });

        // 監聽兩個滑桿的變化
        sliderA.addEventListener('input', updateVisualization);
        sliderB.addEventListener('input', updateVisualization);
    </script>
</body>
</html>
