<!DOCTYPE html>
<html lang="zh-CN">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>工作性价比计算器 - 你的打工值吗？</title>
    <style>
        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
        }
        body {
            font-family: "Microsoft YaHei", sans-serif;
            background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
            color: #333;
            min-height: 100vh;
            padding: 20px;
        }
        .container {
            max-width: 600px;
            margin: 0 auto;
            background: #fff;
            border-radius: 15px;
            box-shadow: 0 10px 30px rgba(0,0,0,0.3);
            overflow: hidden;
        }
        header {
            background: #333;
            color: #fff;
            text-align: center;
            padding: 30px 20px;
        }
        header h1 {
            font-size: 28px;
            margin-bottom: 10px;
        }
        header p {
            font-size: 14px;
            color: #ccc;
        }
        .main {
            padding: 30px;
        }
        .form-group {
            margin-bottom: 20px;
        }
        label {
            display: block;
            margin-bottom: 8px;
            font-weight: bold;
            color: #555;
        }
        input[type="number"], select {
            width: 100%;
            padding: 12px;
            border: 2px solid #ddd;
            border-radius: 8px;
            font-size: 16px;
            transition: border 0.3s;
        }
        input[type="number"]:focus, select:focus {
            border-color: #667eea;
            outline: none;
        }
        button {
            width: 100%;
            padding: 15px;
            background: #667eea;
            color: white;
            border: none;
            border-radius: 8px;
            font-size: 18px;
            cursor: pointer;
            transition: background 0.3s;
            font-weight: bold;
        }
        button:hover {
            background: #5a67d8;
        }
        .result {
            margin-top: 30px;
            padding: 20px;
            border-radius: 10px;
            text-align: center;
            display: none;
        }
        .score {
            font-size: 48px;
            font-weight: bold;
            margin: 10px 0;
        }
        .verdict-0 { background: #fee2e2; border-left: 5px solid #ef4444; }
        .verdict-1 { background: #fef3c7; border-left: 5px solid #d97706; }
        .verdict-2 { background: #d1fae5; border-left: 5px solid #10b981; }
        .verdict-3 { background: #ddd6fe; border-left: 5px solid #7c3aed; }
        .tip {
            font-size: 14px;
            color: #666;
            margin-top: 10px;
        }
        .footer {
            text-align: center;
            padding: 20px;
            color: #aaa;
            font-size: 12px;
        }
        .note {
            font-size: 12px;
            color: #888;
            margin-top: 5px;
        }
    </style>
</head>
<body>
    <div class="container">
        <header>
            <h1>💣 打工性价比计算器</h1>
            <p>基于神秘算法A，测测你现在的状态是“跑路”还是“爽炸”</p>
        </header>

        <div class="main">
            <div class="form-group">
                <label>💰 年薪 (税前/万)</label>
                <input type="number" id="salary" placeholder="例如：20" min="0" step="0.1">
            </div>

            <div class="form-group">
                <label>🎓 学历系数</label>
                <select id="edu">
                    <option value="0.8">专科 (0.8x)</option>
                    <option value="1.0" selected>普通本科 (1.0x)</option>
                    <option value="1.2">211/985本科 (1.2x)</option>
                    <option value="1.4">普通硕士 (1.4x)</option>
                    <option value="1.6">211/985硕士 (1.6x)</option>
                    <option value="2.0">博士 (2.0x)</option>
                </select>
            </div>

            <div class="form-group">
                <label>🏢 环境系数</label>
                <select id="env">
                    <option value="0.8">艰苦/工厂/偏远 (0.8x)</option>
                    <option value="0.9">普通办公/一般 (0.9x)</option>
                    <option value="1.0" selected>标准CBD/写字楼 (1.0x)</option>
                    <option value="1.1">高大上/环境优美 (1.1x)</option>
                </select>
            </div>

            <div class="form-group">
                <label>⏱️ 每日工作时长 (小时)</label>
                <input type="number" id="workHours" placeholder="例如：9" min="0" max="24" step="0.5">
            </div>

            <div class="form-group">
                <label>🚌 单程通勤时长 (小时)</label>
                <input type="number" id="commute" placeholder="例如：1" min="0" max="12" step="0.5">
            </div>

            <div class="form-group">
                <label>😴 每日摸鱼/休息时长 (小时)</label>
                <input type="number" id="slacking" placeholder="例如：1" min="0" max="24" step="0.5">
                <div class="note">* 吃饭、午休、发呆时间，适当减去压力</div>
            </div>

            <button onclick="calculate()">🚀 计算我的性价比</button>

            <div id="result" class="result">
                <div id="resultText"></div>
                <div class="score" id="score"></div>
                <div class="tip" id="tip"></div>
            </div>
        </div>

        <div class="footer">
            <p>💡 提示：此算法参考网络流行公式，仅供娱乐与自我审视。祝你工作愉快！</p>
        </div>
    </div>

    <script>
        function calculate() {
            // 获取输入值
            const salary = parseFloat(document.getElementById('salary').value) * 10000; // 转换为元
            const edu = parseFloat(document.getElementById('edu').value);
            const env = parseFloat(document.getElementById('env').value);
            const workHours = parseFloat(document.getElementById('workHours').value);
            const commute = parseFloat(document.getElementById('commute').value) * 2; // 往返
            const slacking = parseFloat(document.getElementById('slacking').value);

            // 验证输入
            if (!salary || !workHours || isNaN(commute)) {
                alert("请填写完整的薪资和时长信息！");
                return;
            }

            // 算法A核心逻辑
            // 日薪 = 年薪 / 365 (简化版，或者用260)
            const dailySalary = salary / 365;
            
            // 分母：总投入时间 = 工作 + 通勤 - 摸鱼
            // 公式中的35是一个经验常数（基准线）
            const totalTime = workHours + commute - (0.5 * slacking);
            
            // 防止分母为0或负数
            if (totalTime <= 0) {
                document.getElementById('result').style.display = 'block';
                document.getElementById('resultText').innerHTML = `<div class="verdict-1">状态异常</div>`;
                document.getElementById('score').textContent = '数据不合理';
                document.getElementById('tip').textContent = '总投入时间不能为0或负数，请检查输入。';
                return;
            }

            const value = (dailySalary * edu * env) / (35 * totalTime);

            // 显示结果
            const resultEl = document.getElementById('result');
            const resultTextEl = document.getElementById('resultText');
            const scoreEl = document.getElementById('score');
            const tipEl = document.getElementById('tip');

            resultEl.style.display = 'block';

            if (value < 0.8) {
                resultTextEl.innerHTML = '<div class="verdict-0">💀 建议跑路</div>';
                scoreEl.textContent = value.toFixed(2);
                tipEl.textContent = '性价比极低，你的生命正在被透支，考虑换个活法吧。';
            } else if (value < 1.2) {
                resultTextEl.innerHTML = '<div class="verdict-1">😐 普通打工人</div>';
                scoreEl.textContent = value.toFixed(2);
                tipEl.textContent = '平平淡淡才是真，虽然累点，但大家都在这么过。';
            } else if (value < 1.5) {
                resultTextEl.innerHTML = '<div class="verdict-2">👍 不错的工作</div>';
                scoreEl.textContent = value.toFixed(2);
                tipEl.textContent = '这工作还算体面，性价比尚可，值得继续干下去。';
            } else if (value < 2.0) {
                resultTextEl.innerHTML = '<div class="verdict-2">🎉 爽歪歪</div>';
                scoreEl.textContent = value.toFixed(2);
                tipEl.textContent = '恭喜！这属于“神仙工作”，记得请同事喝奶茶。';
            } else {
                resultTextEl.innerHTML = '<div class="verdict-3">✨ 爽炸了</div>';
                scoreEl.textContent = value.toFixed(2);
                tipEl.textContent = '人上人！请务必珍惜，这是多少人梦寐以求的状态。';
            }
        }
    </script>
</body>
</html>
