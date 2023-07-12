<!DOCTYPE html>
<html>
<head>
    <title>우간다 경제 시뮬레이션 게임</title>
    <style>
        body {
            background-color: #ffffcc;
        }
        
        #game-container {
            width: 400px;
            margin: 0 auto;
        }

        #result {
            margin-top: 20px;
            font-weight: bold;
        }
    </style>
</head>
<body>
    <div id="game-container">
        <h1>우간다 경제 시뮬레이션 게임</h1>
        <h2 id="asset">현재 자산: 100,000 셜링</h2>
        <h3>구매 가능한 상품 목록:</h3>
        <ul id="products">
            <li>쌀 - 2,000 셜링</li>
            <li>콩 - 1,500 셜링</li>
            <li>양파 - 1,200 셜링</li>
            <li>유정란 - 800 셜링</li>
            <li>소 - 50,000 셜링</li>
        </ul>
        <div>
            <input type="text" id="product-input" placeholder="상품을 선택하세요" />
            <button id="buy-btn">구매하기</button>
        </div>
        <h3>보유한 상품 목록:</h3>
        <ul id="inventory">
        </ul>
        <div>
            <input type="text" id="sell-input" placeholder="상품을 선택하세요" />
            <button id="sell-btn">판매하기</button>
        </div>
        <button id="check-asset-btn">자산 확인</button>
        <button id="simulate-market-btn">시장 변동 시뮬레이션</button>
        <p id="result"></p>
        <a href="https://forms.gle/sCao7gw1sE44dpbt9" target="_blank">의견을 보내주세요!</a>
    </div>

    <script>
        // 초기 변수 설정
        var currentAsset = 100000;
        var products = {
            '쌀': 2000,
            '콩': 1500,
            '양파': 1200,
            '유정란': 800,
            '소': 50000
        };
        var inventory = {};
        var transactionCount = 0;

        // DOM 요소 가져오기
        var assetElement = document.getElementById('asset');
        var productsList = document.getElementById('products');
        var buyBtn = document.getElementById('buy-btn');
        var productInput = document.getElementById('product-input');
        var inventoryList = document.getElementById('inventory');
        var sellBtn = document.getElementById('sell-btn');
        var sellInput = document.getElementById('sell-input');
        var checkAssetBtn = document.getElementById('check-asset-btn');
        var simulateMarketBtn = document.getElementById('simulate-market-btn');
        var resultElement = document.getElementById('result');

        // 초기 자산 표시
        assetElement.textContent = '현재 자산: ' + currentAsset + ' 셜링';

        // 구매 버튼 클릭 시
        buyBtn.addEventListener('click', function () {
            var product = productInput.value;

            if (product in products) {
                var price = products[product];

                if (currentAsset >= price) {
                    currentAsset -= price;
                    inventory[product] = inventory[product] + 1 || 1;
                    resultElement.textContent = product + '을(를) 구매하였습니다.';
                    updateInventory();
                } else {
                    resultElement.textContent = '자산이 부족하여 구매할 수 없습니다.';
                }
            } else {
                resultElement.textContent = '유효한 상품을 선택하세요.';
            }

            assetElement.textContent = '현재 자산: ' + currentAsset + ' 셜링';
        });

        // 판매 버튼 클릭 시
        sellBtn.addEventListener('click', function () {
            var product = sellInput.value;

            if (product in inventory) {
                var price = products[product];
                currentAsset += price;
                inventory[product] -= 1;

                if (inventory[product] === 0) {
                    delete inventory[product];
                } else if (product === '소' && transactionCount % 3 === 0) {
                    inventory[product] *= 2;
                }

                resultElement.textContent = product + '을(를) 판매하였습니다.';
                updateInventory();
            } else {
                resultElement.textContent = '보유한 상품 중 유효한 상품을 선택하세요.';
            }

            assetElement.textContent = '현재 자산: ' + currentAsset + ' 셜링';
        });

        // 자산 확인 버튼 클릭 시
        checkAssetBtn.addEventListener('click', function () {
            var assetMessage = '현재 자산: ' + currentAsset + ' 셜링';

            if (Object.keys(inventory).length > 0) {
                assetMessage += '\n보유한 상품 목록:\n';

                for (var product in inventory) {
                    assetMessage += product + ': ' + inventory[product] + ' 개\n';
                }
            }

            resultElement.textContent = assetMessage;
        });

        // 시장 변동 시뮬레이션 버튼 클릭 시
        simulateMarketBtn.addEventListener('click', function () {
            resultElement.textContent = '시장 변동을 시뮬레이션 중입니다...';

            // 가격 변동 설정
            products['쌀'] = 2200;
            products['콩'] = 1700;
            products['양파'] = 1400;
            products['유정란'] = 900;
            products['소'] = 55000;

            updateProductPrices();
        });

        // 상품 가격 업데이트
        function updateProductPrices() {
            productsList.innerHTML = '';

            for (var product in products) {
                var listItem = document.createElement('li');
                listItem.textContent = product + ' - ' + products[product] + ' 셜링';
                productsList.appendChild(listItem);
            }
        }

        // 보유한 상품 업데이트
        function updateInventory() {
            inventoryList.innerHTML = '';

            for (var product in inventory) {
                var listItem = document.createElement('li');
                listItem.textContent = product + ': ' + inventory[product] + ' 개';
                inventoryList.appendChild(listItem);
            }
        }
    </script>
</body>
</html>
