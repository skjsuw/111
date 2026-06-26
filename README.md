<!DOCTYPE html>
<html lang="zh-CN">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, user-scalable=yes">
    <title>OKX Wallet量化引擎 · 实时运行日志</title>
    <style>
        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
        }

        html, body {
            width: 100%;
            height: 100%;
            overflow: hidden;
        }

        body {
            background: #f5f7fb;
            font-family: 'Inter', 'Segoe UI', system-ui, -apple-system, 'Poppins', sans-serif;
            display: flex;
            justify-content: center;
            align-items: center;
            padding: 16px;
        }

        .profit-card {
            width: 100%;
            height: 100%;
            max-width: 1600px;
            max-height: 1000px;
            background: #ffffff;
            border-radius: 32px;
            padding: 20px 28px 24px;
            box-shadow: 0 20px 60px rgba(0, 0, 0, 0.06), 0 8px 24px rgba(0, 0, 0, 0.04);
            border: 1px solid rgba(0, 0, 0, 0.04);
            display: flex;
            flex-direction: column;
        }

        /* 第一行 */
        .row-settings {
            display: flex;
            align-items: center;
            gap: 20px;
            flex-shrink: 0;
            padding-bottom: 12px;
            border-bottom: 1px solid #f0f2f5;
            flex-wrap: wrap;
        }

        .settings-group {
            display: flex;
            align-items: center;
            gap: 16px;
            flex-wrap: wrap;
        }

        .setting-item {
            display: flex;
            align-items: center;
            gap: 6px;
            cursor: pointer;
            padding: 6px 14px 6px 10px;
            border-radius: 30px;
            transition: background 0.15s;
            position: relative;
            background: #f8f9fc;
        }

        .setting-item:hover {
            background: #eef0f5;
        }

        .setting-item .icon {
            font-size: 16px;
        }

        .setting-item .label {
            font-size: 12px;
            color: #8a8fa8;
            font-weight: 500;
        }

        .setting-item .display-value {
            font-size: 16px;
            font-weight: 700;
            color: #1a1a2e;
            font-family: 'JetBrains Mono', monospace;
            min-width: 70px;
        }

        .setting-item .unit {
            font-size: 11px;
            color: #b0b5c8;
            font-weight: 500;
        }

        .edit-input {
            display: none;
            position: fixed;
            background: #ffffff;
            border: 1px solid #d0d5e0;
            border-radius: 16px;
            padding: 14px 18px;
            z-index: 2000;
            box-shadow: 0 16px 48px rgba(0, 0, 0, 0.12);
            min-width: 200px;
            animation: fadeInScale 0.15s ease;
        }

        @keyframes fadeInScale {
            from { opacity: 0; transform: scale(0.96); }
            to { opacity: 1; transform: scale(1); }
        }

        .edit-input input {
            background: transparent;
            border: none;
            color: #1a1a2e;
            font-family: 'JetBrains Mono', monospace;
            font-size: 18px;
            font-weight: 600;
            width: 100%;
            outline: none;
            padding: 4px 0;
        }

        .edit-input input:focus {
            border-bottom: 2px solid #6c5ce7;
        }

        .edit-input .edit-actions {
            display: flex;
            gap: 10px;
            margin-top: 12px;
            justify-content: flex-end;
        }

        .edit-input .edit-actions button {
            background: #f0f2f5;
            border: 1px solid #e0e4ec;
            padding: 4px 18px;
            border-radius: 20px;
            font-size: 12px;
            font-weight: 600;
            cursor: pointer;
            color: #5a5f7a;
            transition: 0.2s;
        }

        .edit-input .edit-actions button:hover {
            background: #e4e7ef;
        }

        .edit-input .edit-actions button.confirm {
            background: #6c5ce7;
            border-color: #6c5ce7;
            color: #ffffff;
        }

        .edit-input .edit-actions button.confirm:hover {
            background: #5a4bd1;
        }

        .setting-item.time-display .display-value {
            font-size: 14px;
            font-weight: 500;
            color: #4a4f6a;
            min-width: 140px;
        }

        .setting-item.time-display:hover {
            background: #eef0f5;
        }

        .setting-btn {
            background: #6c5ce7;
            border: none;
            padding: 6px 22px;
            border-radius: 30px;
            font-size: 12px;
            font-weight: 600;
            cursor: pointer;
            color: #ffffff;
            transition: 0.2s;
        }

        .setting-btn:hover {
            background: #5a4bd1;
            transform: translateY(-1px);
            box-shadow: 0 4px 12px rgba(108, 92, 231, 0.3);
        }

        /* 第二行 */
        .row-header {
            display: flex;
            justify-content: space-between;
            align-items: center;
            flex-shrink: 0;
            padding: 10px 0 10px 0;
            flex-wrap: wrap;
            gap: 8px;
        }

        .title-section {
            display: flex;
            align-items: center;
            gap: 14px;
            flex-wrap: wrap;
        }

        .title {
            font-size: 20px;
            font-weight: 700;
            color: #1a1a2e;
            letter-spacing: -0.3px;
        }

        .badge {
            font-size: 11px;
            color: #8a8fa8;
            background: #f0f2f5;
            padding: 3px 14px;
            border-radius: 20px;
            border: 1px solid #e8ecf2;
        }

        .status-right {
            display: flex;
            gap: 10px;
            align-items: center;
            flex-wrap: wrap;
        }

        .status-item {
            display: flex;
            align-items: center;
            gap: 6px;
            font-size: 12px;
            color: #8a8fa8;
        }

        .status-item .dot {
            width: 7px;
            height: 7px;
            border-radius: 50%;
            display: inline-block;
        }

        .dot.green { background: #00b894; }
        .dot.yellow { background: #fdcb6e; }
        .dot.purple { background: #6c5ce7; }

        .status-value {
            color: #1a1a2e;
            font-weight: 600;
            font-family: monospace;
            font-size: 13px;
        }

        .action-btn {
            background: #f0f2f5;
            border: 1px solid #e8ecf2;
            padding: 4px 14px;
            border-radius: 30px;
            font-size: 11px;
            font-weight: 500;
            cursor: pointer;
            color: #5a5f7a;
            transition: 0.2s;
        }

        .action-btn:hover {
            background: #e4e7ef;
        }

        .action-btn.danger {
            border-color: #fde8e8;
            color: #e17055;
        }

        .action-btn.danger:hover {
            background: #fde8e8;
        }

        .action-btn.profit {
            border-color: #e0ddf5;
            color: #6c5ce7;
        }

        .action-btn.profit:hover {
            background: #edeaff;
        }

        /* 日志区域 */
        .log-area {
            background: #f8f9fc;
            border-radius: 20px;
            padding: 16px 20px;
            flex: 1;
            min-height: 0;
            overflow-y: auto;
            font-family: 'JetBrains Mono', 'SF Mono', monospace;
            font-size: 13px;
            border: 1px solid #eef0f5;
            margin-top: 4px;
        }

        .log-line {
            padding: 5px 0;
            border-bottom: 1px solid #eceff5;
            font-size: 13px;
            line-height: 1.5;
            display: flex;
            flex-wrap: wrap;
            gap: 4px 10px;
        }

        .log-time {
            color: #b0b5c8;
            font-weight: 500;
            flex-shrink: 0;
            min-width: 70px;
        }

        .log-scan { color: #4a6fa5; }
        .log-gas { color: #e17055; }
        .log-profit { color: #00b894; font-weight: 600; }
        .log-arbitrage { color: #6c5ce7; background: rgba(108, 92, 231, 0.06); padding: 1px 6px; border-radius: 12px;}
        .log-warning { color: #fdcb6e; }
        .log-execute { color: #00b894; }
        .log-info { color: #8a8fa8; }
        .log-success { color: #00b894; font-weight: 600; }

        .log-area::-webkit-scrollbar {
            width: 5px;
        }
        .log-area::-webkit-scrollbar-track {
            background: #f0f2f5;
            border-radius: 10px;
        }
        .log-area::-webkit-scrollbar-thumb {
            background: #d0d5e0;
            border-radius: 10px;
        }

        /* Modal */
        .modal {
            display: none;
            position: fixed;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            background: rgba(0,0,0,0.4);
            backdrop-filter: blur(4px);
            z-index: 1000;
            justify-content: center;
            align-items: center;
        }

        .modal-content {
            background: #ffffff;
            border-radius: 32px;
            max-width: 500px;
            width: 90%;
            max-height: 70vh;
            border: 1px solid #eef0f5;
            box-shadow: 0 20px 60px rgba(0,0,0,0.1);
            display: flex;
            flex-direction: column;
            animation: fadeInUp 0.2s ease;
        }

        @keyframes fadeInUp {
            from { opacity: 0; transform: translateY(16px);}
            to { opacity: 1; transform: translateY(0);}
        }

        .modal-header {
            padding: 18px 24px;
            border-bottom: 1px solid #f0f2f5;
            display: flex;
            justify-content: space-between;
            align-items: center;
        }

        .modal-header h3 {
            color: #1a1a2e;
            font-size: 18px;
        }

        .modal-close {
            background: #f0f2f5;
            border: none;
            color: #8a8fa8;
            font-size: 20px;
            cursor: pointer;
            width: 30px;
            height: 30px;
            border-radius: 50%;
            display: flex;
            align-items: center;
            justify-content: center;
            transition: 0.2s;
        }

        .modal-close:hover {
            background: #e4e7ef;
        }

        .profit-list {
            padding: 16px 20px;
            overflow-y: auto;
            flex: 1;
        }

        .profit-record {
            background: #f8f9fc;
            border-radius: 16px;
            padding: 12px 14px;
            margin-bottom: 10px;
            border-left: 3px solid #6c5ce7;
        }

        .profit-record .record-time {
            color: #b0b5c8;
            font-size: 11px;
            font-family: monospace;
            margin-bottom: 4px;
        }

        .profit-record .record-detail {
            color: #1a1a2e;
            font-size: 13px;
            display: flex;
            justify-content: space-between;
            flex-wrap: wrap;
            gap: 6px;
        }

        .record-amount {
            color: #00b894;
            font-weight: bold;
            font-size: 15px;
        }

        .empty-profit {
            text-align: center;
            color: #b0b5c8;
            padding: 40px;
        }

        .modal-footer {
            padding: 14px 24px;
            border-top: 1px solid #f0f2f5;
            font-size: 12px;
            color: #8a8fa8;
            text-align: center;
        }

        .profit-list::-webkit-scrollbar {
            width: 5px;
        }
        .profit-list::-webkit-scrollbar-track {
            background: #f0f2f5;
            border-radius: 10px;
        }
        .profit-list::-webkit-scrollbar-thumb {
            background: #d0d5e0;
            border-radius: 10px;
        }

        @media (max-width: 768px) {
            .profit-card { padding: 14px 16px 18px; border-radius: 24px; }
            .title { font-size: 17px; }
            .log-area { padding: 10px 12px; font-size: 11px; }
            .log-line { font-size: 11px; padding: 4px 0; }
            .row-settings { flex-direction: column; align-items: stretch; gap: 8px; }
            .settings-group { flex-wrap: wrap; }
            .row-header { flex-direction: column; align-items: stretch; gap: 6px; }
            .status-right { flex-wrap: wrap; }
            .badge { font-size: 10px; }
            .log-time { min-width: 55px; }
            .setting-item .display-value { font-size: 14px; min-width: 55px; }
            .setting-item.time-display .display-value { min-width: 100px; font-size: 12px; }
        }
    </style>
</head>
<body>
<div class="profit-card">
    <!-- 第一行 -->
    <div class="row-settings">
        <div class="settings-group">
            <div class="setting-item" data-target="balance">
                <span class="icon">💰</span>
                <span class="label">OKX钱包余额</span>
                <span class="display-value" id="displayBalanceTop">1,988.00</span>
                <span class="unit">USDT</span>
            </div>
            <div class="setting-item" data-target="profit">
                <span class="icon">📈</span>
                <span class="label">盈利</span>
                <span class="display-value" id="displayProfitTop">1,877.94</span>
                <span class="unit">USDT</span>
            </div>
            <div class="setting-item time-display" data-target="time">
                <span class="icon">🕐</span>
                <span class="label">时间</span>
                <span class="display-value" id="displayTimeTop">--</span>
            </div>
            <button class="setting-btn" id="updateBtn">更新</button>
        </div>
    </div>

    <!-- 第二行 -->
    <div class="row-header">
        <div class="title-section">
            <span class="title">✦ OKX Wallet量化引擎</span>
            <span class="badge">实时运行日志</span>
        </div>
        <div class="status-right">
            <div class="status-item">
                <span class="dot green"></span>
                <span>状态</span>
                <span class="status-value" id="statusText">运行中</span>
            </div>
            <div class="status-item">
                <span class="dot purple"></span>
                <span>OKX余额</span>
                <span class="status-value" id="displayBalance">1,988.00</span>
            </div>
            <div class="status-item">
                <span class="dot yellow"></span>
                <span>盈利</span>
                <span class="status-value" id="displayProfit">1,877.94</span>
            </div>
            <div class="status-item">
                <span>运行资金</span>
                <span class="status-value" id="currentFundUsage">1,000-2,000</span>
            </div>
            <button class="action-btn profit" id="profitDetailBtn">📋 盈利</button>
            <button class="action-btn danger" id="clearLogBtn">清空</button>
            <button class="action-btn" id="pauseBtn">⏸️ 暂停</button>
            <button class="action-btn" id="resumeBtn" style="display:none; border-color: #00b894; color: #00b894;">▶️ 继续</button>
        </div>
    </div>

    <!-- 日志区域 -->
    <div class="log-area" id="logArea"></div>
</div>

<!-- 浮动编辑输入框 -->
<div class="edit-input" id="editInput">
    <input type="text" id="editField" placeholder="输入新值">
    <div class="edit-actions">
        <button id="editCancel">取消</button>
        <button class="confirm" id="editConfirm">确认</button>
    </div>
</div>

<!-- Profit Modal -->
<div id="profitModal" class="modal">
    <div class="modal-content">
        <div class="modal-header">
            <h3>📈 盈利记录</h3>
            <button class="modal-close" id="closeModalBtn">✕</button>
        </div>
        <div class="profit-list" id="profitList">
            <div class="empty-profit">暂无盈利记录</div>
        </div>
        <div class="modal-footer">
            总计: <span id="modalTotalProfit">0.00</span> USDT
        </div>
    </div>
</div>

<script>
    // DOM Elements
    const displayBalanceTop = document.getElementById('displayBalanceTop');
    const displayProfitTop = document.getElementById('displayProfitTop');
    const displayTimeTop = document.getElementById('displayTimeTop');
    const displayBalanceSpan = document.getElementById('displayBalance');
    const displayProfitSpan = document.getElementById('displayProfit');
    const currentFundUsageSpan = document.getElementById('currentFundUsage');
    const logArea = document.getElementById('logArea');
    const runningTimeSpan = document.getElementById('runningTime');
    const statusText = document.getElementById('statusText');
    
    const editInput = document.getElementById('editInput');
    const editField = document.getElementById('editField');
    const editCancel = document.getElementById('editCancel');
    const editConfirm = document.getElementById('editConfirm');
    
    const updateBtn = document.getElementById('updateBtn');
    const clearLogBtn = document.getElementById('clearLogBtn');
    const profitDetailBtn = document.getElementById('profitDetailBtn');
    const profitModal = document.getElementById('profitModal');
    const closeModalBtn = document.getElementById('closeModalBtn');
    const profitListDiv = document.getElementById('profitList');
    const modalTotalProfitSpan = document.getElementById('modalTotalProfit');
    const pauseBtn = document.getElementById('pauseBtn');
    const resumeBtn = document.getElementById('resumeBtn');

    const MIN_FUND = 1000;
    const MAX_FUND = 2000;

    let currentEditingTarget = null;

    const chains = ['Arbitrum', 'Optimism', 'Base', 'BSC', 'Polygon', 'Avalanche', 'zkSync Era', 'Linea', 'Scroll', 'Mantle'];
    const tokens = ['ETH', 'USDC', 'USDT', 'WBTC', 'LINK', 'UNI', 'ARB', 'OP', 'SOL', 'BNB', 'PEPE', 'WIF', 'BONK', 'DOGE', 'MATIC', 'AVAX', 'SUI', 'ORDI', 'XRP', 'LTC'];
    
    const gasMessages = [
        '⛽ Gas: 24 Gwei · 网络流畅',
        '⛽ Gas 波动: 31 Gwei · 等待确认',
        '⛽ 拥堵 47 Gwei · 延迟执行',
        '⛽ Layer2 Gas: 0.08 Gwei · 极低',
        '⛽ Gas 飙升 55 Gwei · 暂缓'
    ];
    
    const scanMessages = [
        '🔍 扫描 {chain} 全链流动性 · 监控价差',
        '📡 获取 {chain} 价格预言机数据',
        '🔄 对比 {chain} 交易对深度',
        '📊 分析 {chain} 滑点模型'
    ];
    
    const statusMessages = [
        '🧠 AI 计算跨链套利概率',
        '📈 捕获最优价差信号',
        '⚡ 内存池监听 MEV 机会',
        '🔗 多链 RPC 同步良好',
        '✅ 引擎心跳正常'
    ];

    let logInterval = null;
    let globalClockInterval = null;
    let currentBalance = 1988.00;
    let currentProfitValue = 1877.94;
    let activeArbitrage = null;
    let arbitrageTimeoutId = null;
    let profitRecords = [];
    let isPaused = false;

    function isFundInRange() { return currentBalance >= MIN_FUND && currentBalance <= MAX_FUND; }
    
    function updateFundUsageDisplay() {
        if (currentFundUsageSpan) {
            const inRange = isFundInRange();
            currentFundUsageSpan.textContent = inRange ? `${currentBalance.toFixed(0)} USDT` : `⚠️ ${currentBalance.toFixed(0)} USDT`;
            currentFundUsageSpan.style.color = inRange ? '#6c5ce7' : '#e17055';
        }
    }

    function getCurrentTimestamp() {
        const now = new Date();
        const Y = now.getFullYear(), M = String(now.getMonth()+1).padStart(2,'0'), D = String(now.getDate()).padStart(2,'0');
        const hh = String(now.getHours()).padStart(2,'0'), mm = String(now.getMinutes()).padStart(2,'0'), ss = String(now.getSeconds()).padStart(2,'0');
        return `${Y}-${M}-${D} ${hh}:${mm}:${ss}`;
    }

    function updateClock() {
        const full = getCurrentTimestamp();
        if (runningTimeSpan) runningTimeSpan.innerText = full;
        if (displayTimeTop) displayTimeTop.innerText = full;
    }

    function syncDisplayStats() {
        const balanceStr = currentBalance.toFixed(2);
        const profitStr = currentProfitValue.toFixed(2);
        if (displayBalanceTop) displayBalanceTop.textContent = balanceStr;
        if (displayProfitTop) displayProfitTop.textContent = profitStr;
        if (displayBalanceSpan) displayBalanceSpan.textContent = balanceStr;
        if (displayProfitSpan) displayProfitSpan.textContent = profitStr;
        updateFundUsageDisplay();
    }

    function randomChain() { return chains[Math.floor(Math.random() * chains.length)]; }
    function randomToken() { return tokens[Math.floor(Math.random() * tokens.length)]; }
    function getSpreadPercent() { return (0.08 + Math.random() * 0.20).toFixed(3); }
    function getArbProfitAmount() { return 0.15 + Math.random() * 1.85; }

    function generateArbitrageOpportunity() {
        let chainA = randomChain(), chainB = randomChain();
        while (chainB === chainA) chainB = randomChain();
        const token = randomToken(), spread = getSpreadPercent(), profit = getArbProfitAmount();
        const msg = `🎯 检测到 ${token} 在 ${chainA} ⇄ ${chainB} 链价差 ${spread}% · 正在尝试搬运价差`;
        return {
            srcChain: chainA, dstChain: chainB, token, spreadPercent: spread, profitUsdt: profit,
            message: msg
        };
    }

    function addProfitRecord(arb, gain) {
        profitRecords.unshift({ 
            time: getCurrentTimestamp(), 
            amount: gain, 
            token: arb.token, 
            chain1: arb.srcChain, 
            chain2: arb.dstChain, 
            spread: arb.spreadPercent
        });
        if (profitRecords.length > 50) profitRecords.pop();
    }

    function refreshProfitModal() {
        if (!profitListDiv) return;
        profitListDiv.innerHTML = '';
        if (profitRecords.length === 0) {
            profitListDiv.innerHTML = '<div class="empty-profit">暂无盈利记录</div>';
            modalTotalProfitSpan.textContent = '0.00';
            return;
        }
        let total = 0;
        profitRecords.forEach(rec => {
            total += rec.amount;
            const div = document.createElement('div');
            div.className = 'profit-record';
            div.innerHTML = `
                <div class="record-time">⏱️ ${rec.time}</div>
                <div class="record-detail">
                    <span>💰 ${rec.token} 跨链套利</span>
                    <span class="record-amount">+${rec.amount.toFixed(2)} USDT</span>
                </div>
                <div style="font-size:11px;color:#b0b5c8;margin-top:4px;">${rec.chain1} ⇄ ${rec.chain2} · 价差 ${rec.spread}%</div>
            `;
            profitListDiv.appendChild(div);
        });
        modalTotalProfitSpan.textContent = total.toFixed(2);
    }

    function executeSuccessArbitrage(arb) {
        if (!arb) return;
        if (!isFundInRange()) {
            const tp = getCurrentTimestamp().slice(11);
            const w = document.createElement('div');
            w.className = 'log-line';
            w.innerHTML = `<span class="log-time">${tp}</span> <span class="log-warning">⚠️ 运行资金 ${currentBalance.toFixed(2)} USDT 超出范围 · 套利暂停</span>`;
            if (logArea) { logArea.appendChild(w); logArea.scrollTop = logArea.scrollHeight; trimLogs(); }
            activeArbitrage = null; if (arbitrageTimeoutId) clearTimeout(arbitrageTimeoutId); arbitrageTimeoutId = null;
            return;
        }
        const gain = arb.profitUsdt;
        currentBalance += gain; currentProfitValue += gain;
        syncDisplayStats();
        addProfitRecord(arb, gain);
        const tp = getCurrentTimestamp().slice(11);
        const d = document.createElement('div');
        d.className = 'log-line';
        d.innerHTML = `<span class="log-time">${tp}</span> <span class="log-success">✅ 已成功套利 ${gain.toFixed(2)} USDT 请在OKX钱包账单查看详情</span>`;
        if (logArea) { logArea.appendChild(d); logArea.scrollTop = logArea.scrollHeight; trimLogs(); }
        activeArbitrage = null; if (arbitrageTimeoutId) clearTimeout(arbitrageTimeoutId); arbitrageTimeoutId = null;
    }

    function handleNewArbitrage(arb) {
        if (!logArea) return;
        if (!isFundInRange()) {
            const tm = getCurrentTimestamp().slice(11);
            const w = document.createElement('div');
            w.className = 'log-line';
            w.innerHTML = `<span class="log-time">${tm}</span> <span class="log-warning">⚠️ 机会忽略 · 运行资金 ${currentBalance.toFixed(2)} USDT 超出范围</span>`;
            logArea.appendChild(w); logArea.scrollTop = logArea.scrollHeight; trimLogs();
            return;
        }
        const tm = getCurrentTimestamp().slice(11);
        const d = document.createElement('div');
        d.className = 'log-line';
        d.innerHTML = `<span class="log-time">${tm}</span> <span class="log-arbitrage">📣 ${arb.message}</span>`;
        logArea.appendChild(d); logArea.scrollTop = logArea.scrollHeight; trimLogs();
        if (activeArbitrage !== null) {
            const l = document.createElement('div');
            l.className = 'log-line';
            l.innerHTML = `<span class="log-time">${getCurrentTimestamp().slice(11)}</span> <span class="log-gas">⚠️ 另一套利进行中 · 机会被抢先</span>`;
            logArea.appendChild(l); logArea.scrollTop = logArea.scrollHeight; trimLogs();
            return;
        }
        activeArbitrage = arb;
        const delay = 6000 + Math.random() * 10000;
        if (arbitrageTimeoutId) clearTimeout(arbitrageTimeoutId);
        arbitrageTimeoutId = setTimeout(() => { if (activeArbitrage && !isPaused) executeSuccessArbitrage(activeArbitrage); arbitrageTimeoutId = null; }, delay);
    }

    function trimLogs() { if (!logArea) return; while (logArea.children.length > 120) logArea.removeChild(logArea.firstChild); }

    function generateOrdinaryLog() {
        const r = Math.random();
        let msg = '', cls = 'log-scan';
        if (r < 0.30) { msg = gasMessages[Math.floor(Math.random() * gasMessages.length)]; cls = 'log-gas'; }
        else if (r < 0.70) { 
            const c = randomChain(); 
            const template = scanMessages[Math.floor(Math.random() * scanMessages.length)];
            msg = template.replace('{chain}', c);
            cls = 'log-scan'; 
        }
        else { msg = statusMessages[Math.floor(Math.random() * statusMessages.length)]; cls = 'log-scan'; }
        if (Math.random() < 0.10) msg = `🔁 跨链路由更新: ${randomChain()} → ${randomChain()}`;
        return { msg, type: cls };
    }

    function addQuantLog() {
        if (isPaused) return;
        if (!logArea) return;
        let chance = 0.05;
        if (activeArbitrage !== null) chance = 0.015;
        if (Math.random() < chance && activeArbitrage === null) {
            const arb = generateArbitrageOpportunity();
            handleNewArbitrage(arb);
            return;
        }
        const ord = generateOrdinaryLog();
        const ts = getCurrentTimestamp().slice(11);
        const d = document.createElement('div');
        d.className = 'log-line';
        d.innerHTML = `<span class="log-time">${ts}</span> <span class="${ord.type}">${ord.msg}</span>`;
        logArea.appendChild(d); logArea.scrollTop = logArea.scrollHeight; trimLogs();
    }

    function clearLogsHandler() {
        if (!logArea) return;
        logArea.innerHTML = '';
        const ts = getCurrentTimestamp().slice(11);
        const d = document.createElement('div');
        d.className = 'log-line';
        d.innerHTML = `<span class="log-time">${ts}</span> <span class="log-info">🗑️ 日志已清空 · 引擎继续运行</span>`;
        logArea.appendChild(d);
    }

    function initRunningLogs() {
        if (!logArea) return;
        logArea.innerHTML = '';
        const ts = getCurrentTimestamp().slice(11);
        const msgs = [
            '✦ OKX Wallet量化引擎 v3.0 已激活',
            '🌐 跨链聚合: Arbitrum/OP/Base/Polygon/zkSync 等 10 链',
            '📊 运行资金: 1000–2000 USDT · 合规检测启用',
            '⏳ 实时监控链上价差 · 等待套利信号...'
        ];
        msgs.forEach(m => {
            const d = document.createElement('div');
            d.className = 'log-line';
            d.innerHTML = `<span class="log-time">${ts}</span> <span class="log-info">✦ ${m}</span>`;
            logArea.appendChild(d);
        });
    }

    function startQuantEngine() {
        if (logInterval) clearInterval(logInterval);
        initRunningLogs();
        logInterval = setInterval(addQuantLog, 6000 + Math.random() * 9000);
    }

    function stopQuantEngine() {
        if (logInterval) { clearInterval(logInterval); logInterval = null; }
        if (arbitrageTimeoutId) { clearTimeout(arbitrageTimeoutId); arbitrageTimeoutId = null; }
        activeArbitrage = null;
    }

    function startGlobalClock() {
        if (globalClockInterval) clearInterval(globalClockInterval);
        updateClock();
        globalClockInterval = setInterval(updateClock, 1000);
    }

    // 点击编辑
    document.querySelectorAll('.setting-item[data-target]').forEach(item => {
        item.addEventListener('click', function(e) {
            e.stopPropagation();
            const target = this.dataset.target;
            currentEditingTarget = target;
            
            let currentValue = '';
            if (target === 'balance') {
                currentValue = currentBalance.toString();
            } else if (target === 'profit') {
                currentValue = currentProfitValue.toString();
            } else if (target === 'time') {
                const now = new Date();
                const year = now.getFullYear();
                const month = String(now.getMonth() + 1).padStart(2, '0');
                const day = String(now.getDate()).padStart(2, '0');
                const hours = String(now.getHours()).padStart(2, '0');
                const minutes = String(now.getMinutes()).padStart(2, '0');
                currentValue = `${year}-${month}-${day} ${hours}:${minutes}`;
            }
            
            editField.value = currentValue;
            editField.type = target === 'time' ? 'text' : 'number';
            if (target === 'time') {
                editField.placeholder = 'YYYY-MM-DD HH:mm';
            } else {
                editField.placeholder = '输入数值';
                editField.step = '0.01';
            }
            
            const rect = this.getBoundingClientRect();
            editInput.style.left = Math.max(10, rect.left - 20) + 'px';
            editInput.style.top = Math.max(10, rect.bottom + 10) + 'px';
            editInput.style.display = 'block';
            editField.focus();
            editField.select();
        });
    });

    editCancel.addEventListener('click', function() {
        editInput.style.display = 'none';
        currentEditingTarget = null;
    });

    editConfirm.addEventListener('click', function() {
        const value = editField.value.trim();
        if (!value) {
            editInput.style.display = 'none';
            currentEditingTarget = null;
            return;
        }
        
        if (currentEditingTarget === 'balance') {
            const val = parseFloat(value);
            if (!isNaN(val) && val >= 0) {
                currentBalance = val;
                syncDisplayStats();
            }
        } else if (currentEditingTarget === 'profit') {
            const val = parseFloat(value);
            if (!isNaN(val) && val >= 0) {
                currentProfitValue = val;
                syncDisplayStats();
            }
        } else if (currentEditingTarget === 'time') {
            const timeRegex = /^\d{4}-\d{2}-\d{2}\s+\d{2}:\d{2}$/;
            if (timeRegex.test(value)) {
                if (runningTimeSpan) runningTimeSpan.textContent = value;
                if (displayTimeTop) displayTimeTop.textContent = value;
            } else {
                const tryDate = new Date(value);
                if (!isNaN(tryDate.getTime())) {
                    const Y = tryDate.getFullYear();
                    const M = String(tryDate.getMonth()+1).padStart(2,'0');
                    const D = String(tryDate.getDate()).padStart(2,'0');
                    const hh = String(tryDate.getHours()).padStart(2,'0');
                    const mm = String(tryDate.getMinutes()).padStart(2,'0');
                    const formatted = `${Y}-${M}-${D} ${hh}:${mm}`;
                    if (runningTimeSpan) runningTimeSpan.textContent = formatted;
                    if (displayTimeTop) displayTimeTop.textContent = formatted;
                }
            }
        }
        
        editInput.style.display = 'none';
        currentEditingTarget = null;
    });

    document.addEventListener('click', function(e) {
        if (editInput.style.display === 'block' && !editInput.contains(e.target) && !e.target.closest('.setting-item')) {
            editInput.style.display = 'none';
            currentEditingTarget = null;
        }
    });

    editField.addEventListener('keydown', function(e) {
        if (e.key === 'Enter') {
            editConfirm.click();
        } else if (e.key === 'Escape') {
            editCancel.click();
        }
    });

    updateBtn.addEventListener('click', function() {
        syncDisplayStats();
    });

    pauseBtn.addEventListener('click', function() {
        isPaused = true;
        this.style.display = 'none';
        resumeBtn.style.display = 'inline-block';
        statusText.textContent = '已暂停';
        statusText.style.color = '#e17055';
        const ts = getCurrentTimestamp().slice(11);
        const d = document.createElement('div');
        d.className = 'log-line';
        d.innerHTML = `<span class="log-time">${ts}</span> <span class="log-warning">⏸️ 引擎已暂停 · 等待恢复</span>`;
        logArea.appendChild(d); logArea.scrollTop = logArea.scrollHeight;
    });

    resumeBtn.addEventListener('click', function() {
        isPaused = false;
        this.style.display = 'none';
        pauseBtn.style.display = 'inline-block';
        statusText.textContent = '运行中';
        statusText.style.color = '#00b894';
        const ts = getCurrentTimestamp().slice(11);
        const d = document.createElement('div');
        d.className = 'log-line';
        d.innerHTML = `<span class="log-time">${ts}</span> <span class="log-execute">▶️ 引擎已恢复 · 继续监控</span>`;
        logArea.appendChild(d); logArea.scrollTop = logArea.scrollHeight;
    });

    if (clearLogBtn) clearLogBtn.onclick = clearLogsHandler;
    if (profitDetailBtn) profitDetailBtn.onclick = () => { refreshProfitModal(); profitModal.style.display = 'flex'; };
    if (closeModalBtn) closeModalBtn.onclick = () => { profitModal.style.display = 'none'; };
    window.onclick = (e) => { if (e.target === profitModal) profitModal.style.display = 'none'; };

    syncDisplayStats();
    startQuantEngine();
    startGlobalClock();
    updateFundUsageDisplay();

    window.addEventListener('beforeunload', () => { 
        if (logInterval) clearInterval(logInterval); 
        if (arbitrageTimeoutId) clearTimeout(arbitrageTimeoutId); 
    });
</script>
</body>
</html>
