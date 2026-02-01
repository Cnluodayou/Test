<!DOCTYPE html>
<html lang="zh-CN">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, user-scalable=no">
    <title>打工性价比计算器 - 精准核算版</title>
    <!-- 引入ECharts稳定版CDN，增加跨域兼容 -->
    <script src="https://cdn.bootcdn.net/ajax/libs/echarts/5.4.3/echarts.min.js"></script>
    <style>
        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
            font-family: "Microsoft YaHei", "微软雅黑", sans-serif;
        }
        body {
            background-color: #f5f7fa;
            padding: 15px;
            line-height: 1.6;
            min-width: 320px;
        }
        .container {
            max-width: 1200px;
            margin: 0 auto;
            background: #fff;
            border-radius: 12px;
            box-shadow: 0 2px 16px rgba(0,0,0,0.08);
            padding: 20px;
        }
        h1 {
            text-align: center;
            color: #2E86AB;
            margin-bottom: 25px;
            font-size: 22px;
            font-weight: 600;
        }
        .card {
            background: #f9fafb;
            border-radius: 8px;
            padding: 18px;
            margin-bottom: 25px;
            border: 1px solid #eee;
        }
        .card h2 {
            color: #333;
            font-size: 17px;
            margin-bottom: 18px;
            padding-bottom: 10px;
            border-bottom: 1px solid #eee;
        }
        .form-group {
            display: flex;
            flex-wrap: wrap;
            gap: 12px;
            margin-bottom: 12px;
        }
        .form-item {
            flex: 1;
            min-width: 180px;
        }
        .form-item label {
            display: block;
            color: #666;
            font-size: 14px;
            margin-bottom: 6px;
            font-weight: 500;
        }
        .form-item input {
            width: 100%;
            padding: 9px 12px;
            border: 1px solid #ddd;
            border-radius: 6px;
            font-size: 14px;
            outline: none;
            transition: border 0.2s;
        }
        .form-item input:focus {
            border-color: #2E86AB;
            box-shadow: 0 0 0 2px rgba(46,134,171,0.1);
        }
        /* 解决number输入框箭头样式问题 */
        .form-item input[type="number"] {
            -moz-appearance: textfield;
        }
        .form-item input[type="number"]::-webkit-outer-spin-button,
        .form-item input[type="number"]::-webkit-inner-spin-button {
            -webkit-appearance: none;
            margin: 0;
        }
        .btn {
            display: block;
            width: 100%;
            padding: 12px;
            background: #2E86AB;
            color: #fff;
            border: none;
            border-radius: 6px;
            font-size: 16px;
            cursor: pointer;
            transition: background 0.3s;
            margin-top: 8px;
        }
        .btn:hover {
            background: #256b8f;
        }
        .btn:active {
            background: #1e5875;
        }
        .result-area {
            display: none;
            margin-top: 25px;
        }
        .chart-box {
            display: flex;
            flex-wrap: wrap;
            gap: 15px;
            margin-bottom: 25px;
            width: 100%;
        }
        /* 修复图表容器渲染问题，设置明确宽高 */
        .chart-item {
            flex: 1;
            min-width: 280px;
            height: 280px;
            border-radius: 8px;
            box-shadow: 0 1px 8px rgba(0,0,0,0.05);
            padding: 10px;
            background: #fff;
            border: 1px solid #eee;
            position: relative;
        }
        /* 表格适配，解决小屏横向滚动 */
        .table-wrap {
            width: 100%;
            overflow-x: auto;
            -webkit-overflow-scrolling: touch;
            margin-top: 20px;
        }
        .result-table {
            width: 100%;
            min-width: 600px;
            border-collapse: collapse;
        }
        .result-table th, .result-table td {
            padding: 10px 12px;
            text-align: left;
            border-bottom: 1px solid #eee;
            font-size: 14px;
        }
        .result-table th {
            background-color: #f5f7fa;
            color: #333;
            font-weight: 600;
            white-space: nowrap;
        }
        .result-table tr:hover {
            background-color: #f9fafb;
        }
        .highlight {
            color: #C73E1D;
            font-weight: 600;
        }
        /* 适配手机端 */
        @media (max-width: 768px) {
            .container {
                padding: 15px;
            }
            .chart-item {
                height: 250px;
                min-width: 100%;
            }
            h1 {
                font-size: 20px;
            }
            .card h2 {
                font-size: 16px;
            }
            .form-group {
                gap: 10px;
            }
            .form-item {
                min-width: 150px;
            }
        }
        @media (max-width: 480px) {
            .form-item {
                min-width: 100%;
            }
            .chart-item {
                height: 220px;
            }
        }
    </style>
</head>
<body>
    <div class="container">
        <h1>打工性价比计算器 📊</h1>

        <!-- 输入表单区 -->
        <div class="card">
            <h2>基础参数设置（*为必填，其余可默认）</h2>
            <div class="form-group">
                <div class="form-item">
                    <label>月薪（元）*</label>
                    <input type="number" id="monthlySalary" value="10000" min="1000" step="100" required>
                </div>
                <div class="form-item">
                    <label>每月工作天数</label>
                    <input type="number" id="workDays" value="22" min="1" max="31" step="1">
                </div>
                <div class="form-item">
                    <label>每日工作小时</label>
                    <input type="number" id="dailyWorkHours" value="8" min="1" max="16" step="0.5">
                </div>
                <div class="form-item">
                    <label>每日通勤小时</label>
                    <input type="number" id="commuteTime" value="1" min="0" max="5" step="0.5">
                </div>
            </div>
            <div class="form-group">
                <div class="form-item">
                    <label>每月加班小时</label>
                    <input type="number" id="overtimeHours" value="0" min="0" max="100" step="1">
                </div>
                <div class="form-item">
                    <label>加班费倍率</label>
                    <input type="number" id="overtimeRate" value="1.5" min="1" max="3" step="0.5">
                </div>
                <div class="form-item">
                    <label>社保公积金比例</label>
                    <input type="number" id="socialInsurance" value="0.1" min="0" max="0.2" step="0.01">
                </div>
                <div class="form-item">
                    <label>税率</label>
                    <input type="number" id="taxRate" value="0.05" min="0" max="0.2" step="0.01">
                </div>
            </div>
            <div class="form-group">
                <div class="form-item">
                    <label>每日午餐费用（元）</label>
                    <input type="number" id="lunchCost" value="20" min="0" max="200" step="1">
                </div>
                <div class="form-item">
                    <label>每日通勤费用（元）</label>
                    <input type="number" id="commuteCost" value="10" min="0" max="200" step="1">
                </div>
                <div class="form-item">
                    <label>工作压力评分（1-10）</label>
                    <input type="number" id="workStress" value="5" min="1" max="10" step="1">
                </div>
                <div class="form-item">
                    <label>工作满意度评分（1-10）</label>
                    <input type="number" id="jobSatisfaction" value="5" min="1" max="10" step="1">
                </div>
            </div>
            <button class="btn" onclick="calculateWorkValue()">立即计算 🚀</button>
        </div>

        <!-- 结果展示区 -->
        <div class="result-area" id="resultArea">
            <h2>计算结果总览</h2>
            <!-- 可视化图表区 -->
            <div class="chart-box">
                <div class="chart-item" id="incomeChart"></div>
                <div class="chart-item" id="wageChart"></div>
                <div class="chart-item" id="timeChart"></div>
                <div class="chart-item" id="scoreChart"></div>
            </div>
            <!-- 详细结果表格（增加滚动容器） -->
            <div class="card">
                <h2>核心指标详细数据</h2>
                <div class="table-wrap">
                    <table class="result-table">
                        <thead>
                            <tr>
                                <th>指标分类</th>
                                <th>指标名称</th>
                                <th>数值</th>
                                <th>单位</th>
                            </tr>
                        </thead>
                        <tbody id="resultTable"></tbody>
                    </table>
                </div>
            </div>
        </div>
    </div>

    <script>
        // 全局存储计算结果和图表实例，避免重复初始化
        let workResult = {};
        let chartInstances = {};
        // 防抖函数，解决resize多次触发
        function debounce(fn, delay = 300) {
            let timer = null;
            return function() {
                clearTimeout(timer);
                timer = setTimeout(() => fn.apply(this, arguments), delay);
            }
        }

        // 核心计算逻辑（增加全量容错，避免NaN）
        function calculateWorkValue() {
            // 获取并校验输入值，增加默认值兜底
            const monthlySalary = Math.max(parseFloat(document.getElementById('monthlySalary').value) || 10000, 1000);
            const workDays = Math.max(parseFloat(document.getElementById('workDays').value) || 22, 1);
            const dailyWorkHours = Math.max(parseFloat(document.getElementById('dailyWorkHours').value) || 8, 1);
            const commuteTime = Math.max(parseFloat(document.getElementById('commuteTime').value) || 1, 0);
            const overtimeHours = Math.max(parseFloat(document.getElementById('overtimeHours').value) || 0, 0);
            const overtimeRate = Math.max(parseFloat(document.getElementById('overtimeRate').value) || 1.5, 1);
            const socialInsurance = Math.min(Math.max(parseFloat(document.getElementById('socialInsurance').value) || 0.1, 0), 0.2);
            const taxRate = Math.min(Math.max(parseFloat(document.getElementById('taxRate').value) || 0.05, 0), 0.2);
            const lunchCost = Math.max(parseFloat(document.getElementById('lunchCost').value) || 20, 0);
            const commuteCost = Math.max(parseFloat(document.getElementById('commuteCost').value) || 10, 0);
            const workStress = Math.min(Math.max(parseFloat(document.getElementById('workStress').value) || 5, 1), 10);
            const jobSatisfaction = Math.min(Math.max(parseFloat(document.getElementById('jobSatisfaction').value) || 5, 1), 10);

            // 基础时间计算，增加分母非零容错
            const monthlyWorkHours = workDays * dailyWorkHours;
            const annualWorkDays = workDays * 12;
            const annualWorkHours = annualWorkDays * dailyWorkHours + overtimeHours * 12;
            const totalMonthlyHours = Math.max(monthlyWorkHours + overtimeHours, 1); // 避免除零

            // 收入计算（含加班费，全量容错）
            const preTaxIncome = monthlySalary * (1 - socialInsurance);
            const tax = preTaxIncome * taxRate;
            const hourlyWage = monthlyWorkHours > 0 ? monthlySalary / monthlyWorkHours : 0;
            const overtimeWage = hourlyWage * overtimeRate;
            const monthlyOvertimeIncome = overtimeHours * overtimeWage;
            const actualMonthlyIncome = preTaxIncome - tax + monthlyOvertimeIncome;
            const actualAnnualIncome = actualMonthlyIncome * 12;

            // 时薪计算
            const actualHourlyWage = totalMonthlyHours > 0 ? actualMonthlyIncome / totalMonthlyHours : 0;
            const baseHourlyWage = hourlyWage;

            // 生活成本计算
            const monthlyLivingCost = (lunchCost + commuteCost) * workDays;
            const annualLivingCost = monthlyLivingCost * 12;

            // 净收入计算
            const monthlyNetIncome = actualMonthlyIncome - monthlyLivingCost;
            const annualNetIncome = monthlyNetIncome * 12;
            const hourlyNetIncome = totalMonthlyHours > 0 ? monthlyNetIncome / totalMonthlyHours : 0;

            // 时间分配计算
            const dailyCommuteWork = dailyWorkHours + commuteTime;
            const annualTotalTime = annualWorkDays * dailyCommuteWork;
            const annualSleepTime = 365 * 8;
            const annualFreeTime = Math.max(365 * 24 - annualTotalTime - annualSleepTime, 0);
            const annualCommuteTime = Math.max(annualTotalTime - annualWorkHours, 0);

            // 性价比评分（1-10分）
            const wageScore = Math.min(actualHourlyWage / 50, 4);
            const stressScore = Math.max(0, (10 - workStress) / 10 * 3);
            const satisfactionScore = jobSatisfaction / 10 * 3;
            const comprehensiveScore = Math.min((wageScore + stressScore + satisfactionScore).toFixed(1), 10);

            // 存储所有结果，保留两位小数兜底
            workResult = {
                // 收入类
                monthlySalary: monthlySalary.toFixed(2),
                actualMonthlyIncome: actualMonthlyIncome.toFixed(2),
                actualAnnualIncome: actualAnnualIncome.toFixed(2),
                monthlyNetIncome: monthlyNetIncome.toFixed(2),
                annualNetIncome: annualNetIncome.toFixed(2),
                // 时薪类
                baseHourlyWage: baseHourlyWage.toFixed(2),
                actualHourlyWage: actualHourlyWage.toFixed(2),
                hourlyNetIncome: hourlyNetIncome.toFixed(2),
                // 时间类
                monthlyWorkHours: monthlyWorkHours.toFixed(2),
                totalMonthlyHours: totalMonthlyHours.toFixed(2),
                annualWorkHours: annualWorkHours.toFixed(2),
                annualFreeTime: annualFreeTime.toFixed(2),
                annualCommuteTime: annualCommuteTime.toFixed(2),
                // 成本类
                monthlyLivingCost: monthlyLivingCost.toFixed(2),
                annualLivingCost: annualLivingCost.toFixed(2),
                // 评分类
                comprehensiveScore: comprehensiveScore,
                workStress: workStress,
                jobSatisfaction: jobSatisfaction,
                wageScore: wageScore.toFixed(1),
                stressScore: stressScore.toFixed(1),
                satisfactionScore: satisfactionScore.toFixed(1),
                // 辅助计算
                annualSleepTime: annualSleepTime.toFixed(2)
            };

            // 渲染结果
            renderResultTable();
            renderCharts();
            // 显示结果区并平滑滚动
            const resultArea = document.getElementById('resultArea');
            resultArea.style.display = 'block';
            resultArea.scrollIntoView({ behavior: 'smooth', block: 'start' });
        }

        // 渲染详细结果表格（增加容错）
        function renderResultTable() {
            const tableBody = document.getElementById('resultTable');
            if (!tableBody) return;
            tableBody.innerHTML = '';
            // 构造表格数据
            const tableData = [
                // 收入类
                {type: '收入类', name: '税前月薪', value: workResult.monthlySalary, unit: '元/月'},
                {type: '收入类', name: '税后月收入（含加班费）', value: workResult.actualMonthlyIncome, unit: '元/月', highlight: true},
                {type: '收入类', name: '税后年收入（含加班费）', value: workResult.actualAnnualIncome, unit: '元/年'},
                {type: '收入类', name: '月净收入（扣生活成本）', value: workResult.monthlyNetIncome, unit: '元/月', highlight: true},
                {type: '收入类', name: '年净收入（扣生活成本）', value: workResult.annualNetIncome, unit: '元/年', highlight: true},
                // 时薪类
                {type: '时薪类', name: '基础时薪（无加班）', value: workResult.baseHourlyWage, unit: '元/小时'},
                {type: '时薪类', name: '实际时薪（含加班）', value: workResult.actualHourlyWage, unit: '元/小时', highlight: true},
                {type: '时薪类', name: '净收入时薪（核心指标）', value: workResult.hourlyNetIncome, unit: '元/小时', highlight: true},
                // 时间类
                {type: '时间类', name: '每月基础工作小时', value: workResult.monthlyWorkHours, unit: '小时/月'},
                {type: '时间类', name: '每月总工作小时（含加班）', value: workResult.totalMonthlyHours, unit: '小时/月'},
                {type: '时间类', name: '每年工作小时', value: workResult.annualWorkHours, unit: '小时/年'},
                {type: '时间类', name: '每年通勤小时', value: workResult.annualCommuteTime, unit: '小时/年'},
                {type: '时间类', name: '每年可自由支配小时', value: workResult.annualFreeTime, unit: '小时/年', highlight: true},
                // 成本类
                {type: '成本类', name: '每月生活成本（通勤+午餐）', value: workResult.monthlyLivingCost, unit: '元/月'},
                {type: '成本类', name: '每年生活成本（通勤+午餐）', value: workResult.annualLivingCost, unit: '元/年'},
                // 评分类
                {type: '评分类', name: '综合性价比评分', value: workResult.comprehensiveScore, unit: '分（1-10）', highlight: true},
                {type: '评分类', name: '时薪得分', value: workResult.wageScore, unit: '分（0-4）'},
                {type: '评分类', name: '压力得分', value: workResult.stressScore, unit: '分（0-3）'},
                {type: '评分类', name: '满意度得分', value: workResult.satisfactionScore, unit: '分（0-3）'},
                {type: '评分类', name: '工作压力评分', value: workResult.workStress, unit: '分（1-10）'},
                {type: '评分类', name: '工作满意度评分', value: workResult.jobSatisfaction, unit: '分（1-10）'}
            ];

            // 生成表格行
            tableData.forEach(item => {
                const tr = document.createElement('tr');
                tr.innerHTML = `
                    <td>${item.type || '-'}</td>
                    <td>${item.name || '-'}</td>
                    <td class="${item.highlight ? 'highlight' : ''}">${item.value || 0}</td>
                    <td>${item.unit || '-'}</td>
                `;
                tableBody.appendChild(tr);
            });
        }

        // 渲染ECharts图表（核心修复：判断元素存在+避免重复初始化）
        function renderCharts() {
            // 销毁原有图表实例，避免重复渲染
            for (let key in chartInstances) {
                if (chartInstances[key] && chartInstances[key].dispose) {
                    chartInstances[key].dispose();
                }
            }
            chartInstances = {};

            // 1. 收入结构饼图（简化计算，避免冗余变量）
            const incomeChartEl = document.getElementById('incomeChart');
            if (incomeChartEl) {
                chartInstances.incomeChart = echarts.init(incomeChartEl);
                const monthlySalary = parseFloat(workResult.monthlySalary);
                const socialCost = monthlySalary * parseFloat(document.getElementById('socialInsurance').value) || 0;
                const tax = (monthlySalary - socialCost) * parseFloat(document.getElementById('taxRate').value) || 0;
                const overtimeIncome = parseFloat(document.getElementById('overtimeHours').value) * (monthlySalary/(workDays*dailyWorkHours) || 0) * parseFloat(document.getElementById('overtimeRate').value) || 0;
                const baseIncome = monthlySalary - socialCost - tax || 0;
                chartInstances.incomeChart.setOption({
                    title: {text: '月收入结构', left: 'center', fontSize: 14, fontWeight: 500},
                    tooltip: {trigger: 'item', formatter: '{b}: {c} 元\n{percent}%'},
                    legend: {orient: 'vertical', left: 10, top: 20, fontSize: 12, itemWidth: 10, itemHeight: 10},
                    series: [{
                        name: '收入分配',
                        type: 'pie',
                        radius: ['40%', '70%'],
                        center: ['55%', '50%'],
                        data: [
                            {value: baseIncome, name: '税后基础收入'},
                            {value: socialCost, name: '社保公积金'},
                            {value: tax, name: '税收'},
                            {value: overtimeIncome, name: '加班费'}
                        ].filter(item => item.value > 0), // 过滤0值，避免图表异常
                        color: ['#2E86AB', '#A23B72', '#F18F01', '#C73E1D'],
                        label: {fontSize: 12},
                        labelLine: {show: false}
                    }]
                });
            }

            // 2. 时薪对比柱状图
            const wageChartEl = document.getElementById('wageChart');
            if (wageChartEl) {
                chartInstances.wageChart = echarts.init(wageChartEl);
                chartInstances.wageChart.setOption({
                    title: {text: '时薪对比', left: 'center', fontSize: 14, fontWeight: 500},
                    tooltip: {trigger: 'axis', formatter: '{b}: {c} 元/小时'},
                    xAxis: {type: 'category', data: ['基础时薪', '实际时薪', '净收入时薪'], axisLabel: {fontSize: 12}},
                    yAxis: {type: 'value', axisLabel: {fontSize: 12}},
                    grid: {left: '10%', right: '10%', top: '20%', bottom: '15%'},
                    series: [{
                        data: [
                            parseFloat(workResult.baseHourlyWage) || 0,
                            parseFloat(workResult.actualHourlyWage) || 0,
                            parseFloat(workResult.hourlyNetIncome) || 0
                        ],
                        type: 'bar',
                        color: ['#87CEEB', '#2E86AB', '#C73E1D'],
                        barWidth: '40%'
                    }]
                });
            }

            // 3. 年度时间分配图（转换为天数）
            const timeChartEl = document.getElementById('timeChart');
            if (timeChartEl) {
                chartInstances.timeChart = echarts.init(timeChartEl);
                const workTimeDay = (parseFloat(workResult.annualWorkHours) / 24) || 0;
                const commuteTimeDay = (parseFloat(workResult.annualCommuteTime) / 24) || 0;
                const sleepTimeDay = (parseFloat(workResult.annualSleepTime) / 24) || 0;
                const freeTimeDay = (parseFloat(workResult.annualFreeTime) / 24) || 0;
                chartInstances.timeChart.setOption({
                    title: {text: '年度时间分配（天）', left: 'center', fontSize: 14, fontWeight: 500},
                    tooltip: {trigger: 'axis', formatter: '{b}: {c} 天'},
                    xAxis: {type: 'category', data: ['工作时间', '通勤时间', '睡眠时间', '自由支配'], axisLabel: {fontSize: 12, rotate: 0}},
                    yAxis: {type: 'value', axisLabel: {fontSize: 12}},
                    grid: {left: '10%', right: '10%', top: '20%', bottom: '20%'},
                    series: [{
                        data: [
                            Math.round(workTimeDay),
                            Math.round(commuteTimeDay),
                            Math.round(sleepTimeDay),
                            Math.round(freeTimeDay)
                        ],
                        type: 'bar',
                        color: ['#2E86AB', '#A23B72', '#F18F01', '#C73E1D'],
                        barWidth: '35%'
                    }]
                });
            }

            // 4. 性价比评分图
            const scoreChartEl = document.getElementById('scoreChart');
            if (scoreChartEl) {
                chartInstances.scoreChart = echarts.init(scoreChartEl);
                chartInstances.scoreChart.setOption({
                    title: {text: '性价比评分（1-10分）', left: 'center', fontSize: 14, fontWeight: 500},
                    tooltip: {trigger: 'axis', formatter: '{b}: {c} 分'},
                    xAxis: {type: 'category', data: ['时薪得分', '压力得分', '满意度得分', '综合得分'], axisLabel: {fontSize: 12, rotate: 0}},
                    yAxis: {type: 'value', max: 10, axisLabel: {fontSize: 12}},
                    grid: {left: '10%', right: '10%', top: '20%', bottom: '20%'},
                    series: [{
                        data: [
                            parseFloat(workResult.wageScore) || 0,
                            parseFloat(workResult.stressScore) || 0,
                            parseFloat(workResult.satisfactionScore) || 0,
                            parseFloat(workResult.comprehensiveScore) || 0
                        ],
                        type: 'bar',
                        color: ['#87CEEB', '#DDA0DD', '#FFE4B5', '#C73E1D'],
                        barWidth: '30%'
                    }]
                });
            }
        }

        // 窗口resize时重绘图表（增加防抖）
        window.addEventListener('resize', debounce(function() {
            for (let key in chartInstances) {
                if (chartInstances[key] && chartInstances[key].resize) {
                    chartInstances[key].resize();
                }
            }
        }));

        // 表单输入实时校验，限制范围
        document.querySelectorAll('input').forEach(input => {
            input.addEventListener('blur', function() {
                if (this.min && parseFloat(this.value) < parseFloat(this.min)) {
                    this.value = this.min;
                }
                if (this.max && parseFloat(this.value) > parseFloat(this.max)) {
                    this.value = this.max;
                }
                if (this.value === '' && this.hasAttribute('required')) {
                    this.value = this.defaultValue;
                }
            });
        });

        // 页面加载完成后初始化，确保DOM加载完毕
        window.addEventListener('DOMContentLoaded', function() {
            console.log('页面加载完成，计算器已就绪');
        });
    </script>
</body>
</html>
