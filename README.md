# linzhongtech.github.io
<html lang="zh-CN"><head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>初二8班随机点名器</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <link href="https://cdn.jsdelivr.net/npm/font-awesome@4.7.0/css/font-awesome.min.css" rel="stylesheet">
    <script>
        tailwind.config = {
            theme: {
                extend: {
                    colors: {
                        primary: '#3B82F6',
                        secondary: '#10B981',
                        accent: '#F59E0B',
                        dark: '#1F2937',
                        light: '#F3F4F6'
                    },
                    fontFamily: {
                        sans: ['Inter', 'system-ui', 'sans-serif'],
                    },
                }
            }
        }
    </script>
    <style type="text/tailwindcss">
        @layer utilities {
            .content-auto {
                content-visibility: auto;
            }
            .text-shadow {
                text-shadow: 0 2px 4px rgba(0,0,0,0.1);
            }
            .animate-roll {
                animation: roll 0.05s infinite;
            }
            @keyframes roll {
                0% { transform: translateY(0); }
                100% { transform: translateY(-5px); }
            }
            .card-shadow {
                box-shadow: 0 10px 15px -3px rgba(0, 0, 0, 0.1), 0 4px 6px -2px rgba(0, 0, 0, 0.05);
            }
        }
    </style>
</head>
<body class="bg-gradient-to-br from-blue-50 to-indigo-100 min-h-screen font-sans">
    <div class="container mx-auto px-4 py-8 max-w-5xl">
        <!-- 标题区域 -->
        <header class="text-center mb-10">
            <h1 class="text-[clamp(2rem,5vw,3.5rem)] font-bold text-dark mb-2 text-shadow">
                初二8班
            </h1>
            <p class="text-[clamp(1.2rem,3vw,1.8rem)] text-primary font-semibold">
                随机点名器
            </p>
        </header>

        <!-- 主要显示区域 -->
        <main class="flex flex-col lg:flex-row gap-8">
            <!-- 点名显示区 -->
            <div class="lg:w-2/3">
                <div class="bg-white rounded-2xl p-6 card-shadow transition-all duration-300 hover:shadow-lg">
                    <!-- 选择抽取人数 -->
                    <div class="mb-6 flex justify-center items-center gap-2">
                        <label for="count-select" class="text-dark font-medium">抽取人数:</label>
                        <select id="count-select" class="border border-gray-300 rounded-lg px-3 py-2 focus:outline-none focus:ring-2 focus:ring-primary">
                            <option value="1">1人</option>
                            <option value="2">2人</option>
                            <option value="3">3人</option>
                            <option value="4">4人</option>
                            <option value="5">5人</option>
                        </select>
                    </div>
                    
                    <div id="display-area" class="h-64 flex items-center justify-center border-4 border-primary/20 rounded-xl mb-6 overflow-hidden">
                        <p id="name-display" class="text-[clamp(2rem,6vw,4rem)] font-bold text-center text-dark transition-all duration-300">
                            准备开始
                        </p>
                    </div>
                    
                    <!-- 控制按钮 -->
                    <div class="flex flex-wrap justify-center gap-4">
                        <button id="start-btn" class="px-8 py-4 bg-primary text-white rounded-full text-lg font-semibold shadow-lg hover:bg-primary/90 active:scale-95 transition-all duration-300 flex items-center gap-2">
                            <i class="fa fa-play"></i> 开始
                        </button>
                        <button id="stop-btn" class="px-8 py-4 bg-accent text-white rounded-full text-lg font-semibold shadow-lg hover:bg-accent/90 active:scale-95 transition-all duration-300 flex items-center gap-2" disabled="">
                            <i class="fa fa-stop"></i> 停止
                        </button>
                        <button id="reset-btn" class="px-6 py-4 bg-gray-600 text-white rounded-full text-lg font-semibold shadow-lg hover:bg-gray-700 active:scale-95 transition-all duration-300 flex items-center gap-2">
                            <i class="fa fa-refresh"></i> 重置
                        </button>
                    </div>
                </div>
            </div>

            <!-- 记录区域 -->
            <div class="lg:w-1/3">
                <div class="bg-white rounded-2xl p-6 card-shadow h-full transition-all duration-300 hover:shadow-lg">
                    <h2 class="text-xl font-bold text-dark mb-4 flex items-center gap-2">
                        <i class="fa fa-history text-primary"></i> 抽取记录
                    </h2>
                    <div id="history-container" class="border-2 border-gray-200 rounded-lg h-64 overflow-y-auto p-3 space-y-2">
                        <p class="text-gray-500 text-center italic">暂无记录</p>
                    </div>
                    
                    <div class="mt-4">
                        <h3 class="text-lg font-semibold text-dark mb-2">剩余人数: <span id="remaining-count" class="text-primary">51</span></h3>
                        <div class="w-full bg-gray-200 rounded-full h-2.5">
                            <div id="remaining-progress" class="bg-secondary h-2.5 rounded-full" style="width: 100%"></div>
                        </div>
                    </div>
                </div>
            </div>
        </main>

        <!-- 名单列表（可折叠） -->
        <div class="mt-8 bg-white rounded-2xl p-6 card-shadow">
            <button id="toggle-list" class="flex items-center gap-2 text-primary font-semibold mb-4">
                <i class="fa fa-list"></i> 学生名单
                <i id="toggle-icon" class="fa fa-chevron-down transition-transform duration-300"></i>
            </button>
            <div id="name-list" class="grid grid-cols-2 sm:grid-cols-3 md:grid-cols-4 lg:grid-cols-5 gap-2 hidden">
                <!-- 名单将通过JavaScript动态生成 -->
            </div>
        </div>

        <!-- 页脚 -->
        <footer class="mt-10 text-center text-gray-500 text-sm">
            <p>©️ Copyright 李林骏 2025 保留所有权利.</p>
        </footer>
    </div>

    <script>
        // 学生名单
        const students = [
            "曾祥然", "陈芷萱", "戴颂萁", "戴熙雯", "戴梓倩", 
            "邓可馨", "邓梓涵", "方跃宁", "高润文", "郭腾翔", 
            "何乐桐", "何依灵", "胡珏", "黄宝裕", "黄建润", 
            "黄景苗", "黄徐莉", "柯泽凯", "赖柏霖", "赖煜铭", 
            "雷博钧", "李林骏", "李倩颖", "梁珈郡", "刘恩彤", 
            "刘宋承", "龙昱程", "卢一帆", "罗瑞洋", "罗文昊", 
            "骆宇坤", "邱睿哲", "邱振炫", "苏穆穆", "王诗琪", 
            "巫亮宏", "吴律", "吴莜", "吴泽瑄", "谢润鹏", 
            "谢思彤", "熊海航", "张海祺", "张佳琦", "张静雅", 
            "张宁恩", "张深皓", "张圳诺", "郑腾骏", "周永平", "蓝中骏"
        ];

        // 可用学生名单（初始为全部学生）
        let availableStudents = [...students];
        // 已抽取记录
        let history = [];
        // 滚动状态
        let isRolling = false;
        // 滚动计时器
        let rollInterval;
        // 滚动速度（毫秒），数值越小速度越快
        const ROLL_SPEED = 30;
        // 当前显示的随机学生索引
        let currentDisplayIndex = 0;

        // DOM元素
        const nameDisplay = document.getElementById('name-display');
        const startBtn = document.getElementById('start-btn');
        const stopBtn = document.getElementById('stop-btn');
        const resetBtn = document.getElementById('reset-btn');
        const historyContainer = document.getElementById('history-container');
        const remainingCount = document.getElementById('remaining-count');
        const remainingProgress = document.getElementById('remaining-progress');
        const toggleListBtn = document.getElementById('toggle-list');
        const nameList = document.getElementById('name-list');
        const toggleIcon = document.getElementById('toggle-icon');
        const displayArea = document.getElementById('display-area');
        const countSelect = document.getElementById('count-select');

        // 获取当前选择的抽取人数
        function getSelectedCount() {
            const count = parseInt(countSelect.value);
            // 确保抽取人数不超过剩余人数
            return Math.min(count, availableStudents.length);
        }

        // 更新抽取人数选择器的可用选项
        function updateCountOptions() {
            const options = countSelect.options;
            for (let i = 0; i < options.length; i++) {
                const value = parseInt(options[i].value);
                options[i].disabled = value > availableStudents.length;
            }
            
            // 如果当前选中的数量不可用，自动选择最大可用数量
            if (getSelectedCount() > availableStudents.length) {
                countSelect.value = Math.min(availableStudents.length, 5).toString();
            }
        }

        // 初始化学生名单显示
        function initNameList() {
            nameList.innerHTML = '';
            students.forEach(student => {
                const nameEl = document.createElement('div');
                nameEl.className = 'px-3 py-2 rounded-md border border-gray-200 text-center student-item';
                nameEl.textContent = student;
                nameEl.dataset.name = student;
                nameList.appendChild(nameEl);
            });
        }

        // 更新剩余人数显示
        function updateRemaining() {
            const remaining = availableStudents.length;
            const total = students.length;
            const percentage = (remaining / total) * 100;
            
            remainingCount.textContent = remaining;
            remainingProgress.style.width = `${percentage}%`;
            
            // 当剩余人数较少时改变进度条颜色
            if (percentage < 20) {
                remainingProgress.classList.remove('bg-secondary');
                remainingProgress.classList.add('bg-accent');
            } else {
                remainingProgress.classList.remove('bg-accent');
                remainingProgress.classList.add('bg-secondary');
            }
            
            // 更新可选人数
            updateCountOptions();
        }

        // 更新历史记录显示
        function updateHistory() {
            historyContainer.innerHTML = '';
            
            if (history.length === 0) {
                const emptyMsg = document.createElement('p');
                emptyMsg.className = 'text-gray-500 text-center italic';
                emptyMsg.textContent = '暂无记录';
                historyContainer.appendChild(emptyMsg);
                return;
            }
            
            history.forEach((record, index) => {
                const recordEl = document.createElement('div');
                recordEl.className = 'flex items-center gap-2 p-2 bg-gray-50 rounded-md';
                
                // 如果是多名学生，用换行分隔
                const names = Array.isArray(record) ? record.join('<br>') : record;
                
                recordEl.innerHTML = `
                    <span class="w-6 h-6 rounded-full bg-primary/20 text-primary flex items-center justify-center text-sm font-bold">${index + 1}</span>
                    <span class="font-medium">${names}</span>
                `;
                historyContainer.appendChild(recordEl);
            });
            
            // 滚动到最新记录
            historyContainer.scrollTop = historyContainer.scrollHeight;
        }

        // 更新已抽取学生的样式
        function updateSelectedStudents() {
            const items = document.querySelectorAll('.student-item');
            items.forEach(item => {
                const name = item.dataset.name;
                if (!availableStudents.includes(name)) {
                    item.classList.add('bg-gray-100', 'text-gray-500', 'line-through');
                } else {
                    item.classList.remove('bg-gray-100', 'text-gray-500', 'line-through');
                }
            });
        }

        // 随机滚动名字
        function rollNames() {
            if (availableStudents.length === 0) {
                stopRolling();
                nameDisplay.textContent = '已全部抽取';
                return;
            }
            
            // 循环显示不同的随机学生，创造快速滚动效果
            currentDisplayIndex = (currentDisplayIndex + 1) % availableStudents.length;
            nameDisplay.textContent = availableStudents[currentDisplayIndex];
        }

        // 开始滚动
        function startRolling() {
            if (isRolling || availableStudents.length === 0) return;
            
            isRolling = true;
            startBtn.disabled = true;
            stopBtn.disabled = false;
            countSelect.disabled = true; // 滚动时禁用人数选择
            nameDisplay.classList.add('animate-roll');
            displayArea.classList.add('border-primary');
            displayArea.classList.remove('border-primary/20');
            
            // 快速滚动名字
            rollInterval = setInterval(rollNames, ROLL_SPEED);
        }

        // 停止滚动并选择结果
        function stopRolling() {
            if (!isRolling) return;
            
            clearInterval(rollInterval);
            isRolling = false;
            startBtn.disabled = false;
            stopBtn.disabled = true;
            countSelect.disabled = false; // 停止后启用人数选择
            nameDisplay.classList.remove('animate-roll');
            
            // 突出显示选中的名字
            displayArea.classList.add('border-accent');
            displayArea.classList.remove('border-primary');
            
            const count = getSelectedCount();
            let selectedNames = [];
            
            // 随机选择指定数量的学生
            for (let i = 0; i < count; i++) {
                const randomIndex = Math.floor(Math.random() * availableStudents.length);
                selectedNames.push(availableStudents[randomIndex]);
                // 从可用名单中移除已选中的学生
                availableStudents.splice(randomIndex, 1);
            }
            
            // 显示选中的学生
            nameDisplay.textContent = selectedNames.join('\n');
            
            // 添加到历史记录
            history.push(selectedNames.length > 1 ? selectedNames : selectedNames[0]);
            
            // 更新显示
            updateHistory();
            updateRemaining();
            updateSelectedStudents();
            
            // 添加选中动画
            nameDisplay.classList.add('scale-110');
            setTimeout(() => {
                nameDisplay.classList.remove('scale-110');
            }, 300);
        }

        // 重置点名器
        function resetPicker() {
            clearInterval(rollInterval);
            isRolling = false;
            availableStudents = [...students];
            history = [];
            currentDisplayIndex = 0;
            
            nameDisplay.textContent = '准备开始';
            nameDisplay.classList.remove('animate-roll', 'scale-110');
            displayArea.classList.remove('border-primary', 'border-accent');
            displayArea.classList.add('border-primary/20');
            
            startBtn.disabled = false;
            stopBtn.disabled = true;
            countSelect.disabled = false;
            
            updateHistory();
            updateRemaining();
            updateSelectedStudents();
        }

        // 切换名单显示
        function toggleNameList() {
            nameList.classList.toggle('hidden');
            toggleIcon.classList.toggle('rotate-180');
        }

        // 事件监听
        startBtn.addEventListener('click', startRolling);
        stopBtn.addEventListener('click', stopRolling);
        resetBtn.addEventListener('click', resetPicker);
        toggleListBtn.addEventListener('click', toggleNameList);
        countSelect.addEventListener('change', () => {
            // 确保选择的数量不超过剩余人数
            updateCountOptions();
        });

        // 键盘快捷键
        document.addEventListener('keydown', (e) => {
            // 空格键控制开始/停止
            if (e.code === 'Space') {
                e.preventDefault();
                if (isRolling) {
                    stopRolling();
                } else {
                    startRolling();
                }
            }
            // R键重置
            if (e.code === 'KeyR' && e.ctrlKey) {
                e.preventDefault();
                resetPicker();
            }
        });

        // 初始化
        window.addEventListener('DOMContentLoaded', () => {
            initNameList();
            updateHistory();
            updateRemaining();
        });
    </script>


</body></html>
