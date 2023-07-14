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
    var currentDate = new Date();
    var currentDay = currentDate.getDate();
    var currentMonth = currentDate.getMonth() + 1;
    var currentYear = currentDate.getFullYear();

    // DOM 요소 가져오기
    var assetElement = document.getElementById('asset');
    var dateElement = document.getElementById('date');
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
    dateElement.textContent = '날짜: ' + currentYear + '년 ' + currentMonth + '월 ' + currentDay + '일';

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
                checkMarketFluctuation();
                checkSpecialEvents(product);
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
            checkMarketFluctuation();
            checkSpecialEvents(product);
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
        simulateMarketFluctuation();
        checkSpecialEvents();
    });

    // 시장 변동 시뮬레이션 함수
    function simulateMarketFluctuation() {
        for (var product in products) {
            var priceChange = getRandomInt(-500, 500);
            var newPrice = products[product] + priceChange;
            products[product] = Math.max(newPrice, 1000); // 가격이 1,000 셜링 미만으로 내려가지 않도록 보장
        }

        updateProductPrices();
    }

    // 특별 이벤트 확인 함수
    function checkSpecialEvents(product) {
        if (product === '소') {
            var diseaseEventChance = getRandomInt(1, 10);
            if (diseaseEventChance <= 5) {
                inventory[product] = Math.floor(inventory[product] / 2);
                resultElement.textContent = '전염병으로 소가 절반 이상 사망하였습니다.';
                updateInventory();
            }
        } else if (product === '콩') {
            var droughtEventChance = getRandomInt(1, 10);
            if (droughtEventChance <= 3) {
                delete inventory[product];
                resultElement.textContent = '가뭄으로 콩이 죽었습니다.';
                updateInventory();
            }
        } else if (product === '유정란') {
            var spoilageEventChance = getRandomInt(1, 10);
            if (spoilageEventChance <= 2) {
                delete inventory[product];
                resultElement.textContent = '유정란이 상하여 사용할 수 없게 되었습니다.';
                updateInventory();
            }
        }
    }

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

    // 랜덤 정수 반환
    function getRandomInt(min, max) {
        min = Math.ceil(min);
        max = Math.floor(max);
        return Math.floor(Math.random() * (max - min + 1)) + min;
    }

    // 거래 횟수에 따라 시장 변동을 확인하는 함수
    function checkMarketFluctuation() {
        transactionCount++;

        if (transactionCount >= 3 && transactionCount % getRandomInt(3, 7) === 0) {
            simulateMarketFluctuation();
        }

        if (transactionCount % 2 === 0) {
            // 다음 날짜로 변경
            currentDate.setDate(currentDate.getDate() + 1);
            currentDay = currentDate.getDate();
            currentMonth = currentDate.getMonth() + 1;
            currentYear = currentDate.getFullYear();
            dateElement.textContent = '날짜: ' + currentYear + '년 ' + currentMonth + '월 ' + currentDay + '일';
        }
    }

    // 초기 날짜 설정
    dateElement.textContent = '날짜: ' + currentYear + '년 ' + currentMonth + '월 ' + currentDay + '일';

    // 초기 거래 확인
    if (transactionCount >= 2) {
        simulateMarketFluctuation();
        checkMarketFluctuation();
    }
</script>
