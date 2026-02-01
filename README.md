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
            margin-bottom: 15px;
        }
        button:hover {
            background: #5a67d8;
        }
        button.download-btn {
            background: #10b981;
        }
        button.download-btn:hover {
            background: #059669;
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
        .analysis {
            margin-top: 25px;
            padding: 20px;
            background: #f8fafc;
            border-radius: 10px;
            display: none;
        }
        .analysis h3 {
            color: #333;
            font-size: 18px;
            margin-bottom: 15px;
            padding-bottom: 8px;
            border-bottom: 1px solid #eee;
        }
        .analysis-section {
            margin-bottom: 12px;
        }
        .analysis-section h4 {
            color: #555;
            font-size: 16px;
            margin-bottom: 6px;
            display: flex;
            align-items: center;
        }
        .analysis-section h4::before {
            content: "•";
            color: #667eea;
            font-size: 20px;
            margin-right: 8px;
        }
        .analysis-section p {
            color: #666;
            font-size: 14px;
            line-height: 1.6;
            margin-left: 18px;
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
        .loading {
            font-size: 14px;
            color: #667eea;
            text-align: center;
            margin-top: 10px;
            display: none;
        }
    </style>
    <!-- 引入PDF生成依赖库 -->
    <script src="https://cdn.jsdelivr.net/npm/jspdf@2.5.1/dist/jspdf.umd.min.js"></script>
    <script src="https://cdn.jsdelivr.net/npm/html2canvas@1.4.1/dist/html2canvas.min.js"></script>
</head>
<body>
    <div class="container">
        <header>
            <h1>💣 打工性价比计算器</h1>
            <p>专业版｜含维度拆解分析+PDF报告下载，测测你的工作值不值</p>
        </header>

        <div class="main">
            <div class="form-group">
                <label>💰 年薪 (税前/万)</label>
                <input type="number" id="salary" placeholder="例如：20" min="0" step="0.1" required>
            </div>

            <div class="form-group">
                <label>🎓 学历系数</label>
                <select id="edu" required>
                    <option value="0.8">专科 (0.8x)</option>
                    <option value="1.0" selected>普通本科 (1.0x)</option>
                    <option value="1.2">211/985本科 (1.2x)</option>
                    <option value="1.4">普通硕士 (1.4x)</option>
                    <option value="1.6">211/985硕士 (1.6x)</option>
                    <option value="2.0">博士 (2.0x)</option>
                </select>
            </div>

            <div class="form-group">
                <label>🏢 办公环境系数</label>
                <select id="env" required>
                    <option value="0.8">艰苦/工厂/偏远 (0.8x)</option>
                    <option value="0.9">普通办公/一般 (0.9x)</option>
                    <option value="1.0" selected>标准CBD/写字楼 (1.0x)</option>
                    <option value="1.1">高大上/环境优美 (1.1x)</option>
                </select>
            </div>

            <div class="form-group">
                <label>⏱️ 每日正常工作时长 (小时)</label>
                <input type="number" id="workHours" placeholder="例如：8" min="0" max="24" step="0.5" required>
            </div>

            <div class="form-group">
                <label>🚌 单程通勤时长 (小时)</label>
                <input type="number" id="commute" placeholder="例如：0.5" min="0" max="12" step="0.5" required>
            </div>

            <div class="form-group">
                <label>😴 每日摸鱼/休息时长 (小时)</label>
                <input type="number" id="slacking" placeholder="例如：1" min="0" max="24" step="0.5" value="0" required>
                <div class="note">* 吃饭、午休、发呆时间，适当抵消工作压力</div>
            </div>

            <div class="form-group">
                <label>📆 周末休息类型</label>
                <select id="restType" required>
                    <option value="0.7">单休/月休2天 (0.7x)</option>
                    <option value="0.85">大小周 (0.85x)</option>
                    <option value="1.0" selected>标准双休 (1.0x)</option>
                    <option value="1.1">双休+带薪假期多 (1.1x)</option>
                </select>
            </div>

            <div class="form-group">
                <label>🔥 每月加班天数</label>
                <input type="number" id="overtime" placeholder="例如：2" min="0" max="31" step="1" value="0" required>
                <div class="note">* 按平均每次加班4小时计算，计入总投入时间</div>
            </div>

            <div class="form-group">
                <label>🏦 五险一金缴纳系数</label>
                <select id="insurance" required>
                    <option value="0.8">按最低标准缴 (0.8x)</option>
                    <option value="0.9">按实际薪资低比例缴 (0.9x)</option>
                    <option value="1.0" selected>按实际薪资标准缴 (1.0x)</option>
                    <option value="1.15">按实际薪资高比例缴/补充公积金 (1.15x)</option>
                </select>
            </div>

            <div class="form-group">
                <label>😫 工作压力系数</label>
                <select id="pressure" required>
                    <option value="0.7">高压/996/随时待命 (0.7x)</option>
                    <option value="0.85">中压/偶尔加班/考核严 (0.85x)</option>
                    <option value="1.0" selected>低压/准时下班/无强制考核 (1.0x)</option>
                    <option value="1.1">超轻松/佛系管理/无KPI (1.1x)</option>
                </select>
            </div>

            <button onclick="calculate()">🚀 计算我的打工性价比</button>
            <button class="download-btn" id="downloadBtn" onclick="downloadPDF()" style="display: none;">📥 下载PDF分析报告</button>
            <div class="loading" id="loading">PDF报告生成中... 请稍候</div>

            <div id="result" class="result">
                <div id="resultText"></div>
                <div class="score" id="score"></div>
                <div class="tip" id="tip"></div>
            </div>

            <!-- 新增：详细分析结论区域 -->
            <div id="analysis" class="analysis">
                <h3>📊 维度拆解分析结论</h3>
                <div class="analysis-section" id="salaryAnalysis"></div>
                <div class="analysis-section" id="timeAnalysis"></div>
                <div class="analysis-section" id="welfareAnalysis"></div>
                <div class="analysis-section" id="experienceAnalysis"></div>
                <div class="analysis-section" id="suggestionAnalysis">
                    <h4>优化建议</h4>
                    <p id="suggestionText"></p>
                </div>
            </div>
        </div>

        <div class="footer">
            <p>💡 提示：算法基于职场经验优化，仅供娱乐与自我审视；PDF报告生成需联网加载依赖库</p>
        </div>
    </div>

    <script>
        // 全局存储计算结果，用于PDF生成
        let calcResult = null;

        function calculate() {
            // 获取所有输入值
            const salary = parseFloat(document.getElementById('salary').value) * 10000;
            const edu = parseFloat(document.getElementById('edu').value);
            const env = parseFloat(document.getElementById('env').value);
            const workHours = parseFloat(document.getElementById('workHours').value);
            const commute = parseFloat(document.getElementById('commute').value) * 2;
            const slacking = parseFloat(document.getElementById('slacking').value);
            const restType = parseFloat(document.getElementById('restType').value);
            const overtime = parseFloat(document.getElementById('overtime').value);
            const insurance = parseFloat(document.getElementById('insurance').value);
            const pressure = parseFloat(document.getElementById('pressure').value);

            // 输入验证
            const allVals = [salary, workHours, commute, slacking, restType, overtime, insurance, pressure];
            if (allVals.some(val => isNaN(val) || val < 0)) {
                alert("请填写所有有效数值，不可留空/为负数！");
                return;
            }
            if (salary === 0) {
                alert("年薪不能为0，请填写实际薪资！");
                return;
            }

            // 核心计算逻辑
            const dailySalary = salary / 365;
            const dailyOvertime = (overtime * 4) / 30;
            const totalTime = workHours + commute - (0.5 * slacking) + dailyOvertime;

            if (totalTime <= 0) {
                showResult('verdict-1', '状态异常', '数据不合理', '总投入时间不能为0/负数，检查加班/摸鱼/工作时长输入！');
                return;
            }

            const totalCoeff = edu * env * restType * insurance * pressure;
            const value = (dailySalary * totalCoeff) / (35 * totalTime);

            // 存储计算结果
            calcResult = {
                salary: salary / 10000, // 还原为万
                edu: getOptionText('edu', edu),
                env: getOptionText('env', env),
                workHours,
                commute: commute / 2, // 还原为单程
                slacking,
                restType: getOptionText('restType', restType),
                overtime,
                insurance: getOptionText('insurance', insurance),
                pressure: getOptionText('pressure', pressure),
                score: value.toFixed(2),
                verdict: getVerdict(value),
                tip: getTip(value)
            };

            // 展示结果和分析
            showResult(getVerdictClass(value), calcResult.verdict, calcResult.score, calcResult.tip);
            generateAnalysis(value, calcResult);

            // 显示下载按钮
            document.getElementById('downloadBtn').style.display = 'block';
        }

        // 结果展示函数
        function showResult(verdictClass, verdictText, score, tip) {
            const resultEl = document.getElementById('result');
            const resultTextEl = document.getElementById('resultText');
            const scoreEl = document.getElementById('score');
            const tipEl = document.getElementById('tip');

            resultEl.className = 'result ' + verdictClass;
            resultEl.style.display = 'block';
            resultTextEl.innerHTML = `<div>${verdictText}</div>`;
            scoreEl.textContent = score;
            tipEl.textContent = tip;
        }

        // 生成详细分析结论
        function generateAnalysis(value, data) {
            const analysisEl = document.getElementById('analysis');
            analysisEl.style.display = 'block';

            // 薪资维度分析
            const salaryAnalysis = document.getElementById('salaryAnalysis');
            let salaryEval = '';
            if (data.salary < 15) {
                salaryEval = '你的年薪处于较低水平，结合学历系数来看，薪资竞争力较弱，是影响性价比的主要因素之一。';
            } else if (data.salary < 30) {
                salaryEval = '你的年薪处于中等水平，与学历系数匹配度一般，若想提升性价比，可考虑薪资涨幅或减少时间投入。';
            } else if (data.salary < 50) {
                salaryEval = '你的年薪处于中高水平，薪资竞争力较强，是工作性价比的核心优势，建议重点维护其他维度的平衡。';
            } else {
                salaryEval = '你的年薪处于高水平，薪资优势显著，即使其他维度存在小幅短板，也能支撑较高的整体性价比。';
            }
            salaryAnalysis.innerHTML = `<h4>薪资与学历匹配度</h4><p>年薪${data.salary}万 + ${data.edu}，${salaryEval}</p>`;

            // 时间投入分析
            const timeAnalysis = document.getElementById('timeAnalysis');
            const totalDailyTime = data.workHours + (data.commute * 2) + (data.overtime * 4 / 30);
            let timeEval = '';
            if (totalDailyTime < 10) {
                timeEval = '每日总投入时间（工作+通勤+加班）较少，时间成本较低，是提升性价比的重要优势，建议保持现有节奏。';
            } else if (totalDailyTime < 12) {
                timeEval = '每日总投入时间处于中等水平，通勤和加班时长相对合理，若想优化可适当减少无效加班或缩短通勤距离。';
            } else if (totalDailyTime < 14) {
                timeEval = '每日总投入时间偏多，主要受通勤或加班影响，时间成本较高，建议优先考虑优化通勤方式或与公司沟通减少加班。';
            } else {
                timeEval = '每日总投入时间过多，远超合理阈值，时间成本极高，已严重影响工作生活平衡，是性价比偏低的核心原因。';
            }
            timeAnalysis.innerHTML = `<h4>时间投入合理性</h4><p>每日工作${data.workHours}小时 + 通勤${data.commute*2}小时 + 月加班${data.overtime}天，${timeEval}</p>`;

            // 福利与环境分析
            const welfareAnalysis = document.getElementById('welfareAnalysis');
            let welfareEval = '';
            if (data.insurance.includes('最低') || data.restType.includes('单休')) {
                welfareEval = '五险一金缴纳标准较低或休息时间不足，福利保障维度存在明显短板，建议关注后续福利调整或考虑更换福利更完善的工作。';
            } else if (data.insurance.includes('标准') && data.restType.includes('双休')) {
                welfareEval = '五险一金按标准缴纳，休息类型为双休，福利保障处于行业平均水平，与工作性价比匹配度良好。';
            } else {
                welfareEval = '五险一金缴纳比例较高或休息时间充足（含带薪假期），福利保障维度表现优秀，是工作的核心优势之一，建议重点珍惜。';
            }
            welfareAnalysis.innerHTML = `<h4>福利与休息保障</h4><p>${data.insurance} + ${data.restType}，${welfareEval}</p>`;

            // 体验与压力分析
            const experienceAnalysis = document.getElementById('experienceAnalysis');
            let expEval = '';
            if (data.pressure.includes('高压') || data.env.includes('艰苦')) {
                expEval = '工作压力较大或办公环境较一般，主观体验维度短板明显，长期可能影响身心健康，建议优先调整工作节奏或寻求环境更友好的岗位。';
            } else if (data.pressure.includes('中压') || data.env.includes('普通')) {
                expEval = '工作压力中等或办公环境一般，主观体验处于行业平均水平，可通过增加摸鱼休息时间、优化工作方法来提升体验感。';
            } else {
                expEval = '工作压力较小且办公环境良好，主观体验维度表现优秀，能有效提升工作幸福感，是维持高性价比的重要因素。';
            }
            experienceAnalysis.innerHTML = `<h4>工作体验与压力</h4><p>${data.pressure} + ${data.env}，${data.slacking > 0 ? '每日摸鱼' + data.slacking + '小时，' : ''}${expEval}</p>`;

            // 优化建议
            const suggestionText = document.getElementById('suggestionText');
            let suggestion = '';
            if (value < 0.8) {
                suggestion = '1. 优先考虑换工作：重点关注薪资涨幅≥30%或工作时间减少20%的机会；2. 短期优化：与领导沟通减少加班，或更换通勤方式（如搬家、换地铁）缩短通勤；3. 福利争取：申请提高五险一金缴纳标准，或协商调整休息制度。';
            } else if (value < 1.2) {
                suggestion = '1. 薪资提升：通过跳槽、内部晋升或技能提升争取15%-20%的薪资涨幅；2. 时间优化：减少无效加班（如优化工作效率），或通过拼车、远程办公等方式缩短通勤；3. 体验改善：合理分配摸鱼休息时间，缓解工作压力，提升主观幸福感。';
            } else if (value < 1.5) {
                suggestion = '1. 维持优势：保持现有工作节奏，重点提升专业技能，为后续晋升或薪资涨幅铺垫；2. 小幅优化：若存在通勤偏长或偶尔加班问题，可适当调整通勤方式或与团队沟通工作分配；3. 福利升级：关注公司福利政策调整，争取更高比例的五险一金缴纳或更多带薪假期。';
            } else {
                suggestion = '1. 珍惜现有工作：你的工作性价比处于优秀水平，建议重点维护工作生活平衡；2. 长期规划：利用充足的休息时间提升自我（如学习新技能、发展副业），或争取内部晋升进一步提升薪资；3. 风险对冲：关注行业动态，保持职场竞争力，避免因外部环境变化影响现有优势。';
            }
            suggestionText.textContent = suggestion;
        }

        // PDF下载功能
        function downloadPDF() {
            if (!calcResult) return;

            const loadingEl = document.getElementById('loading');
            loadingEl.style.display = 'block';

            // 延迟执行，避免UI阻塞
            setTimeout(async () => {
                try {
                    const { jsPDF } = window.jspdf;
                    const pdf = new jsPDF('p', 'mm', 'a4');

                    // 设置字体（支持中文）
                    pdf.setFont('simhei', 'normal');

                    // 页面标题
                    pdf.setFontSize(18);
                    pdf.text('工作性价比分析报告', 105, 20, { align: 'center' });

                    // 基本信息
                    pdf.setFontSize(14);
                    pdf.text('一、基本信息', 20, 40);
                    pdf.setFontSize(12);
                    const infoY = 50;
                    const infoList = [
                        `年薪：${calcResult.salary}万元（税前）`,
                        `学历系数：${calcResult.edu}`,
                        `办公环境：${calcResult.env}`,
                        `每日工作时长：${calcResult.workHours}小时`,
                        `单程通勤时长：${calcResult.commute}小时`,
                        `每日摸鱼时长：${calcResult.slacking}小时`,
                        `周末休息类型：${calcResult.restType}`,
                        `每月加班天数：${calcResult.overtime}天`,
                        `五险一金缴纳：${calcResult.insurance}`,
                        `工作压力系数：${calcResult.pressure}`
                    ];
                    infoList.forEach((info, index) => {
                        pdf.text(info, 25, infoY + index * 8);
                    });

                    // 核心结果
                    pdf.setFontSize(14);
                    pdf.text('二、核心性价比结果', 20, infoY + infoList.length * 8 + 15);
                    pdf.setFontSize(16);
                    pdf.text(`评级：${calcResult.verdict}`, 25, infoY + infoList.length * 8 + 25);
                    pdf.text(`性价比得分：${calcResult.score}`, 25, infoY + infoList.length * 8 + 35);
                    pdf.setFontSize(12);
                    pdf.text(`评价：${calcResult.tip}`, 25, infoY + infoList.length * 8 + 45);

                    // 详细分析（截取页面分析内容）
                    pdf.setFontSize(14);
                    const analysisY = infoY + infoList.length * 8 + 60;
                    pdf.text('三、维度拆解分析', 20, analysisY);

                    // 逐个获取分析段落并写入PDF
                    const analysisSections = document.querySelectorAll('.analysis-section');
                    let currentY = analysisY + 15;
                    analysisSections.forEach(section => {
                        const h4 = section.querySelector('h4').textContent;
                        const p = section.querySelector('p').textContent;

                        pdf.setFontSize(13);
                        pdf.text(h4, 25, currentY);
                        currentY += 8;

                        // 处理长文本换行
                        const splitText = pdf.splitTextToSize(p, 160);
                        pdf.setFontSize(11);
                        splitText.forEach(line => {
                            pdf.text(line, 25, currentY);
                            currentY += 7;
                        });
                        currentY += 5;
                    });

                    // 底部说明
                    pdf.setFontSize(10);
                    pdf.text('注：本报告基于职场经验优化算法生成，仅供娱乐与自我审视，不构成职业决策依据', 20, currentY + 10, { align: 'left' });
                    pdf.text(`报告生成时间：${new Date().toLocaleString()}`, 20, currentY + 18);

                    // 下载PDF
                    pdf.save(`工作性价比分析报告_${new Date().getTime()}.pdf`);
                } catch (error) {
                    alert('PDF生成失败，请检查网络连接或刷新页面重试！');
                    console.error('PDF生成错误：', error);
                } finally {
                    loadingEl.style.display = 'none';
                }
            }, 300);
        }

        // 辅助函数：获取下拉框选项文本
        function getOptionText(id, value) {
            const select = document.getElementById(id);
            for (let i = 0; i < select.options.length; i++) {
                if (select.options[i].value == value) {
                    return select.options[i].text;
                }
            }
            return '';
        }

        // 辅助函数：获取评级文本
        function getVerdict(value) {
            if (value < 0.8) return '💀 建议跑路';
            if (value < 1.2) return '😐 普通打工人';
            if (value < 1.5) return '👍 不错的工作';
            if (value < 2.0) return '🎉 爽歪歪';
            return '✨ 爽炸了';
        }

        // 辅助函数：获取评级提示语
        function getTip(value) {
            if (value < 0.8) return '性价比极低，生命被透支，赶紧骑驴找马换工作！';
            if (value < 1.2) return '平平淡淡凑活过，大家都是这样，佛系就好～';
            if (value < 1.5) return '性价比尚可，体面又稳定，值得继续干下去！';
            if (value < 2.0) return '神仙工作预定，少劳多获，记得珍惜这份幸运！';
            return '人上人本仁！这工作天花板，躺着赚钱都不夸张～';
        }

        // 辅助函数：获取评级样式类
        function getVerdictClass(value) {
            if (value < 0.8) return 'verdict-0';
            if (value < 1.2) return 'verdict-1';
            if (value < 2.0) return 'verdict-2';
            return 'verdict-3';
        }
    </script>
</body>
</html>
