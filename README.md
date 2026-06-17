<!DOCTYPE html>
<html lang="vi">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Sic Bo Premium Casino - Hệ Thống Vận Hành Tự Động Toàn Diện</title>
    <style>
        :root {
            --dice-size: 54px;
            /* Màu đỏ Acrylic Casino trong suốt/sẫm phân tầng ánh sáng */
            --dice-color: #cb1010; 
            /* Màu chấm phẳng đục chuẩn sòng bài */
            --dot-color: #ffffff; 
            --gold-grad: linear-gradient(135deg, #ffe066 0%, #d4af37 50%, #8a6f27 100%);
        }

        body {
            display: flex;
            flex-direction: column;
            justify-content: center;
            align-items: center;
            min-height: 100vh;
            margin: 0;
            background-color: #0b140c;
            font-family: 'Segoe UI', Roboto, Helvetica, Arial, sans-serif;
            overflow: hidden;
            user-select: none;
        }

        /* --- THANH SỐ DƯ VÀ LOGO NẠP TIỀN MỚI --- */
        .top-navigation {
            position: absolute;
            top: 15px;
            right: 20px;
            display: flex;
            gap: 12px;
            align-items: center;
            z-index: 10;
        }
        .header-bar {
            display: flex;
            align-items: center;
            background: rgba(0, 0, 0, 0.7);
            padding: 8px 16px;
            border-radius: 20px;
            border: 1px solid #d4af37;
            box-shadow: 0 4px 10px rgba(0,0,0,0.5);
        }
        .balance-label {
            color: #a3c2a6;
            font-size: 12px;
            font-weight: bold;
            margin-right: 8px;
            text-transform: uppercase;
        }
        .balance-amount {
            color: #00ff88;
            font-size: 16px;
            font-weight: bold;
            text-shadow: 0 0 6px rgba(0,255,136,0.4);
        }
        .btn-deposit {
            background: linear-gradient(135deg, #00b359 0%, #006633 100%);
            border: 1px solid #00ff88;
            padding: 8px 14px;
            color: #ffffff;
            font-size: 11px;
            font-weight: bold;
            border-radius: 20px;
            cursor: pointer;
            text-transform: uppercase;
            box-shadow: 0 4px 10px rgba(0,0,0,0.4);
            transition: all 0.2s ease;
        }
        .btn-deposit:active {
            transform: scale(0.95);
            box-shadow: 0 2px 4px rgba(0,0,0,0.4);
        }

        /* --- LOGO BÀN CƯỢC HOÀNG GIA --- */
        .casino-logo {
            font-size: 24px;
            font-weight: 900;
            text-transform: uppercase;
            letter-spacing: 4px;
            background: var(--gold-grad);
            -webkit-background-clip: text;
            -webkit-text-fill-color: transparent;
            text-shadow: 0px 4px 8px rgba(0, 0, 0, 0.5);
            margin-bottom: -10px;
            margin-top: 40px;
            z-index: 2;
            border-bottom: 2px solid #d4af37;
            padding-bottom: 5px;
            text-align: center;
        }
        .casino-logo span {
            display: block;
            font-size: 10px;
            letter-spacing: 6px;
            color: #a3c2a6;
            margin-top: 2px;
        }

        /* Khu vực sân khấu chính */
        .main-stage {
            position: relative;
            width: 400px;
            height: 340px;
            display: flex;
            justify-content: center;
            align-items: center;
        }

        /* --- Vòng tròn thời gian --- */
        .progress-ring {
            position: absolute;
            transform: rotate(-90deg);
            z-index: 1;
        }
        .progress-ring__circle {
            stroke: #16291a;
            stroke-width: 10;
            fill: rgba(0,0,0,0.4);
        }
        .progress-ring__bar {
            stroke: #00ff88;
            stroke-width: 10;
            fill: transparent;
            stroke-dasharray: 754;
            stroke-dashoffset: 0;
        }

        .status-text {
            position: absolute;
            top: 5px;
            color: #ffffff;
            font-size: 18px;
            text-transform: uppercase;
            letter-spacing: 2px;
            width: 100vw;
            text-align: center;
            font-weight: bold;
            text-shadow: 0 2px 4px rgba(0,0,0,0.8);
        }

        .countdown-number {
            position: absolute;
            font-size: 60px;
            color: white;
            font-weight: bold;
            z-index: 10;
            text-shadow: 0 4px 10px rgba(0,0,0,0.5);
        }

        /* --- Khu vực Xúc Sắc 3D Nâng Cấp Vuông Góc Khối --- */
        .dice-area {
            position: absolute;
            display: flex;
            flex-direction: column;
            align-items: center;
            gap: 18px;
            z-index: 5;
            perspective: 1000px; 
        }
        .dice-row { display: flex; gap: 25px; }

        .dice {
            width: var(--dice-size);
            height: var(--dice-size);
            position: relative;
            transform-style: preserve-3d; 
            transition: transform 0.8s cubic-bezier(0.2, 0.8, 0.3, 1); 
        }

        .dice-face {
            position: absolute;
            width: var(--dice-size);
            height: var(--dice-size);
            /* Thiết kế Acrylic phẳng không bo góc */
            background: linear-gradient(135deg, #e61919 0%, var(--dice-color) 60%, #8a0606 100%);
            border-radius: 0px; 
            border: 1px solid #500202;
            outline: 1px solid rgba(255,255,255,0.15);
            outline-offset: -1px;
            display: flex;
            box-sizing: border-box;
            padding: 5px;
            box-shadow: inset 0 0 10px rgba(0,0,0,0.45), 0 3px 6px rgba(0,0,0,0.6);
        }

        /* Khoảng cách TranslateZ = 1/2 Dice Size (54px / 2 = 27px) */
        .front  { transform: rotateY(0deg) translateZ(27px); }
        .back   { transform: rotateY(180deg) translateZ(27px); }
        .right  { transform: rotateY(90deg) translateZ(27px); }
        .left   { transform: rotateY(-90deg) translateZ(27px); }
        .top    { transform: rotateX(90deg) translateZ(27px); }
        .bottom { transform: rotateX(-90deg) translateZ(27px); }

        /* Các dấu chấm của xúc xắc */
        .dot {
            width: 10px;
            height: 10px;
            background: var(--dot-color) !important; /* Ép luôn luôn màu trắng */
            border-radius: 50%;
            box-shadow: inset 1px 1px 2px rgba(0,0,0,0.6);
            display: inline-block;
        }

        /* Chấm Nhất (mặt 1) cũng giữ màu trắng đồng bộ theo yêu cầu */
        .face-1 .dot {
            width: 18px;
            height: 18px;
            background: var(--dot-color) !important; /* Đổi mặt 1 thành màu trắng đồng bộ */
            box-shadow: inset 1px 1px 3px rgba(0,0,0,0.7);
        }

        .face-1 { justify-content: center; align-items: center; }
        .face-2 { justify-content: space-between; }
        .face-2 .dot:nth-child(2) { align-self: flex-end; }
        .face-3 { justify-content: space-between; }
        .face-3 .dot:nth-child(2) { align-self: center; }
        .face-3 .dot:nth-child(3) { align-self: flex-end; }

        .face-4, .face-5, .face-6 {
            display: grid;
            grid-template-columns: 1fr 1fr;
            grid-template-rows: repeat(3, 1fr);
            justify-items: center;
            align-items: center;
        }
        .face-4 .dot:nth-child(1) { grid-area: 1 / 1; }
        .face-4 .dot:nth-child(2) { grid-area: 1 / 2; }
        .face-4 .dot:nth-child(3) { grid-area: 3 / 1; }
        .face-4 .dot:nth-child(4) { grid-area: 3 / 2; }

        .face-5 .dot:nth-child(1) { grid-area: 1 / 1; }
        .face-5 .dot:nth-child(2) { grid-area: 1 / 2; }
        .face-5 .dot:nth-child(3) { grid-area: 2 / 1 / 3 / 3; } 
        .face-5 .dot:nth-child(4) { grid-area: 3 / 1; }
        .face-5 .dot:nth-child(5) { grid-area: 3 / 2; }

        .face-6 .dot:nth-child(1) { grid-area: 1 / 1; }
        .face-6 .dot:nth-child(2) { grid-area: 2 / 1; }
        .face-6 .dot:nth-child(3) { grid-area: 3 / 1; }
        .face-6 .dot:nth-child(4) { grid-area: 1 / 2; }
        .face-6 .dot:nth-child(5) { grid-area: 2 / 2; }
        .face-6 .dot:nth-child(6) { grid-area: 3 / 2; }

        .rolling { animation: rollContinuous 4s linear infinite; }

        @keyframes rollContinuous {
            0% { transform: scale(1) rotateX(0deg) rotateY(0deg) rotateZ(0deg); }
            20% { transform: scale(1.4) rotateX(720deg) rotateY(360deg) rotateZ(180deg); }
            50% { transform: scale(1.4) rotateX(1440deg) rotateY(1080deg) rotateZ(360deg); }
            80% { transform: scale(1.4) rotateX(2160deg) rotateY(1800deg) rotateZ(540deg); }
            100% { transform: scale(1.1) rotateX(2880deg) rotateY(2160deg) rotateZ(720deg); }
        }

        /* --- NẮP ĐẬY --- */
        #lid {
            position: absolute; 
            width: 240px;
            height: 240px;
            background: radial-gradient(circle at 30% 30%, #555, #1a1a1a);
            border-radius: 50%;
            z-index: 20;
            display: none; /* Khởi tạo ẩn, chỉ xuất hiện lúc nặn kết quả */
            cursor: grab;
            box-shadow: 0 15px 35px rgba(0,0,0,0.9), inset 0 2px 6px rgba(255,255,255,0.3);
            border: 5px solid #3a3a3a;
            top: calc(50% - 120px);
            left: calc(50% - 120px);
            transform: translate(0px, 0px);
            touch-action: none; 
        }
        #lid:active { cursor: grabbing; }

        .lid-drop {
            display: block !important;
            animation: dropIn 0.6s cubic-bezier(0.175, 0.885, 0.32, 1.275) forwards;
        }

        .lid-flyaway {
            animation: flyAway 0.8s cubic-bezier(0.25, 0.46, 0.45, 0.94) forwards !important;
            pointer-events: none; 
        }

        @keyframes dropIn {
            0% { transform: translateY(-500px) scale(1.5); opacity: 0; }
            100% { transform: translateY(0px) scale(1); opacity: 1; }
        }

        @keyframes flyAway {
            0% { transform: translate(var(--curr-x, 0px), var(--curr-y, 0px)) rotate(0deg) scale(1); opacity: 1; }
            100% { transform: translate(600px, -700px) rotate(160deg) scale(0.5); opacity: 0; }
        }

        /* --- BÀN CƯỢC CASINO --- */
        .betting-table {
            display: flex;
            gap: 20px;
            width: 440px;
            margin-top: 10px;
            z-index: 2;
        }

        .bet-box {
            flex: 1;
            height: 140px;
            background: rgba(0, 0, 0, 0.4);
            border-radius: 16px; 
            position: relative;
            overflow: hidden;
            cursor: pointer;
            display: flex;
            flex-direction: column;
            justify-content: center;
            align-items: center;
            transition: all 0.2s ease;
            box-shadow: inset 0 0 15px rgba(0,0,0,0.6);
        }

        .bet-box.xiu { border: 3px solid #1c75bc; }
        .bet-box.xiu:hover { background: rgba(28, 117, 188, 0.15); }
        .bet-box.tai { border: 3px solid #b31217; }
        .bet-box.tai:hover { background: rgba(179, 18, 23, 0.15); }

        .bet-title {
            font-size: 32px;
            font-weight: 900;
            margin: 0;
            letter-spacing: 2px;
            text-shadow: 0 2px 4px rgba(0,0,0,0.8);
        }
        .bet-box.xiu .bet-title { color: #4dadff; }
        .bet-box.tai .bet-title { color: #ff4d4d; }

        .bet-desc {
            font-size: 11px;
            color: #cccccc;
            margin-top: 2px;
        }

        .success-badge {
            position: absolute;
            top: 8px;
            background: #00ff88;
            color: #000000;
            font-size: 9px;
            font-weight: bold;
            padding: 2px 6px;
            border-radius: 10px;
            text-transform: uppercase;
            box-shadow: 0 0 8px #00ff88;
            opacity: 0;
            pointer-events: none;
            z-index: 4;
        }
        .success-badge.show-fade {
            animation: flashFade 1s forwards ease-out;
        }
        @keyframes flashFade {
            0% { opacity: 0; transform: scale(0.8); }
            15% { opacity: 1; transform: scale(1.05); }
            30% { opacity: 1; transform: scale(1); }
            80% { opacity: 1; }
            100% { opacity: 0; transform: translateY(-5px); }
        }

        .bet-amount-status {
            font-size: 15px;
            font-weight: bold;
            color: #ffe066;
            margin-top: 6px;
            text-shadow: 0 1px 3px rgba(0,0,0,0.9);
            display: block;
        }
        .bump-animation {
            animation: numberBump 0.2s ease-out;
        }
        @keyframes numberBump {
            0% { transform: scale(1); color: #ffe066; }
            50% { transform: scale(1.35); color: #ffffff; text-shadow: 0 0 10px #ffe066; }
            100% { transform: scale(1); color: #ffe066; }
        }

        .ripple {
            position: absolute;
            background: rgba(255, 255, 255, 0.2);
            border-radius: 50%;
            transform: scale(0);
            animation: rippleEffect 0.4s linear;
            pointer-events: none;
        }
        @keyframes rippleEffect {
            to { transform: scale(4); opacity: 0; }
        }

        .chip-container {
            display: flex;
            justify-content: center;
            align-items: center;
            gap: 12px;
            margin-top: 20px;
            background: rgba(0, 0, 0, 0.5);
            padding: 10px 20px;
            border-radius: 35px;
            border: 1px solid #222;
            z-index: 2;
        }

        .chip {
            width: 46px;
            height: 46px;
            border-radius: 50%;
            display: flex;
            justify-content: center;
            align-items: center;
            font-size: 11px;
            font-weight: bold;
            color: white;
            cursor: pointer;
            position: relative;
            border: 3px dashed rgba(255, 255, 255, 0.6);
            box-shadow: 0 4px 6px rgba(0,0,0,0.4), inset 0 0 8px rgba(0,0,0,0.5);
            transition: transform 0.2s cubic-bezier(0.175, 0.885, 0.32, 1.275);
        }

        .chip[data-value="1K"]   { background: #777777; transform: scale(0.85); }
        .chip[data-value="10K"]  { background: #aa66cc; transform: scale(0.9); }
        .chip[data-value="50K"]  { background: #1b75bc; transform: scale(0.95); }
        .chip[data-value="100K"] { background: #39b54a; transform: scale(1.0); }
        .chip[data-value="500K"] { background: #f7931e; transform: scale(1.05); }
        .chip[data-value="1M"]   { background: #b31217; transform: scale(1.1); border-color: #ffe066; }

        .chip::after {
            content: attr(data-value);
            position: absolute;
            width: 26px;
            height: 26px;
            background: rgba(255,255,255,0.15);
            border-radius: 50%;
            display: flex;
            justify-content: center;
            align-items: center;
            font-size: 9px;
        }

        .chip.selected {
            transform: translateY(-8px) scale(1.2) !important;
            box-shadow: 0 8px 15px rgba(0,0,0,0.5), 0 0 10px #00ff88;
            z-index: 3;
        }

        .control-btns {
            display: flex;
            gap: 15px;
            margin-top: 15px;
            z-index: 2;
        }
        .btn-action {
            padding: 8px 18px;
            font-size: 12px;
            font-weight: bold;
            color: white;
            border-radius: 8px;
            cursor: pointer;
            text-transform: uppercase;
            letter-spacing: 1px;
            box-shadow: 0 4px 6px rgba(0,0,0,0.3);
            transition: transform 0.1s, filter 0.2s;
        }
        .btn-action:active { transform: scale(0.95); }
        .btn-cancel { background: linear-gradient(to bottom, #555, #333); border: 1px solid #666; }
        .btn-allin { background: linear-gradient(to bottom, #ff9900, #cc6600); border: 1px solid #ffcc00; }

        .overlay-screen {
            position: fixed;
            top: 0; left: 0; width: 100vw; height: 100vh;
            display: flex;
            flex-direction: column;
            justify-content: center;
            align-items: center;
            z-index: 100;
            opacity: 0;
            pointer-events: none;
            transition: opacity 0.4s ease;
        }
        .overlay-screen.active { opacity: 1; pointer-events: auto; }

        .screen-win { background: rgba(11, 20, 12, 0.85); }
        .win-box {
            background: linear-gradient(135deg, #222 0%, #111 100%);
            border: 3px solid #d4af37;
            padding: 30px 50px;
            border-radius: 20px;
            text-align: center;
            box-shadow: 0 0 50px rgba(212,175,55,0.6);
            transform: scale(0.7);
            transition: transform 0.4s cubic-bezier(0.175, 0.885, 0.32, 1.275);
        }
        .overlay-screen.active .win-box { transform: scale(1); }
        
        .win-title {
            font-size: 40px;
            font-weight: 900;
            background: var(--gold-grad);
            -webkit-background-clip: text;
            -webkit-text-fill-color: transparent;
            margin: 0 0 10px 0;
            letter-spacing: 4px;
        }
        .win-msg { color: #ffffff; font-size: 18px; margin: 0 0 15px 0; }
        .plus-animation {
            font-size: 32px;
            color: #00ff88;
            font-weight: bold;
            animation: bounceAmount 0.5s infinite alternate;
        }
        @keyframes bounceAmount {
            0% { transform: scale(0.95); text-shadow: 0 0 5px #00ff88; }
            100% { transform: scale(1.05); text-shadow: 0 0 20px #00ff88; }
        }

        .screen-lose { background: rgba(20, 10, 10, 0.85); }
        .lose-box {
            background: #1a1111;
            border: 3px solid #553333;
            padding: 30px 50px;
            border-radius: 20px;
            text-align: center;
            box-shadow: 0 0 4px rgba(0,0,0,0.8);
        }
        .lose-title { font-size: 36px; font-weight: 800; color: #cc3333; margin: 0 0 10px 0; letter-spacing: 2px; }
        .lose-msg { color: #999999; font-size: 16px; margin: 0; }
    </style>
</head>
<body>

    <div class="top-navigation">
        <div class="header-bar">
            <span class="balance-label">Số Dư Khả Dụng:</span>
            <span id="accountBalance" class="balance-amount">2.000.000đ</span>
        </div>
        <button class="btn-deposit" onclick="virtualDeposit()">+ Nạp 2.000.000đ</button>
    </div>

    <div class="casino-logo">
        Sic Bo Premium
        <span>LIVE MACAU CLUB</span>
    </div>

    <div class="main-stage">
        <div id="status" class="status-text">Đang chờ...</div>

        <svg class="progress-ring" width="260" height="260">
            <circle class="progress-ring__circle" cx="130" cy="130" r="120"></circle>
            <circle id="bar" class="progress-ring__bar" cx="130" cy="130" r="120"></circle>
        </svg>

        <div id="countdown" class="countdown-number">26</div>

        <div class="dice-area" id="diceArea">
            <div class="dice-row">
                <div class="dice" id="d1">
                    <div class="dice-face front face-1"><div class="dot"></div></div>
                    <div class="dice-face back face-6"><div class="dot"></div><div class="dot"></div><div class="dot"></div><div class="dot"></div><div class="dot"></div><div class="dot"></div></div>
                    <div class="dice-face right face-2"><div class="dot"></div><div class="dot"></div></div>
                    <div class="dice-face left face-5"><div class="dot"></div><div class="dot"></div><div class="dot"></div><div class="dot"></div><div class="dot"></div></div>
                    <div class="dice-face top face-3"><div class="dot"></div><div class="dot"></div><div class="dot"></div></div>
                    <div class="dice-face bottom face-4"><div class="dot"></div><div class="dot"></div><div class="dot"></div><div class="dot"></div></div>
                </div>
                <div class="dice" id="d2">
                    <div class="dice-face front face-1"><div class="dot"></div></div>
                    <div class="dice-face back face-6"><div class="dot"></div><div class="dot"></div><div class="dot"></div><div class="dot"></div><div class="dot"></div><div class="dot"></div></div>
                    <div class="dice-face right face-2"><div class="dot"></div><div class="dot"></div></div>
                    <div class="dice-face left face-5"><div class="dot"></div><div class="dot"></div><div class="dot"></div><div class="dot"></div><div class="dot"></div></div>
                    <div class="dice-face top face-3"><div class="dot"></div><div class="dot"></div><div class="dot"></div></div>
                    <div class="dice-face bottom face-4"><div class="dot"></div><div class="dot"></div><div class="dot"></div><div class="dot"></div></div>
                </div>
            </div>
            <div class="dice" id="d3">
                <div class="dice-face front face-1"><div class="dot"></div></div>
                <div class="dice-face back face-6"><div class="dot"></div><div class="dot"></div><div class="dot"></div><div class="dot"></div><div class="dot"></div><div class="dot"></div></div>
                <div class="dice-face right face-2"><div class="dot"></div><div class="dot"></div></div>
                <div class="dice-face left face-5"><div class="dot"></div><div class="dot"></div><div class="dot"></div><div class="dot"></div><div class="dot"></div></div>
                <div class="dice-face top face-3"><div class="dot"></div><div class="dot"></div><div class="dot"></div></div>
                <div class="dice-face bottom face-4"><div class="dot"></div><div class="dot"></div><div class="dot"></div><div class="dot"></div></div>
            </div>
        </div>

        <div id="lid"></div>
    </div>

    <div class="betting-table">
        <div class="bet-box xiu" data-target="XỈU" onclick="handleBetPlacement(event, 'XỈU')">
            <span class="success-badge">Đã cược thành công</span>
            <span class="bet-title">XỈU</span>
            <span class="bet-desc">4 - 10 (1 ăn 1)</span>
            <span id="totalBetXiu" class="bet-amount-status">0đ</span>
        </div>
        <div class="bet-box tai" data-target="TÀI" onclick="handleBetPlacement(event, 'TÀI')">
            <span class="success-badge">Đã cược thành công</span>
            <span class="bet-title">TÀI</span>
            <span class="bet-desc">11 - 17 (1 ăn 1)</span>
            <span id="totalBetTai" class="bet-amount-status">0đ</span>
        </div>
    </div>

    <div class="control-btns">
        <div class="btn-action btn-cancel" onclick="cancelAllBets()">Hủy Cược</div>
        <div class="btn-action btn-allin" onclick="triggerAllIn()">All-In Tất Tay</div>
    </div>

    <div class="chip-container">
        <div class="chip selected" data-value="1K" onclick="selectChip(this, 1000)"></div>
        <div class="chip" data-value="10K" onclick="selectChip(this, 10000)"></div>
        <div class="chip" data-value="50K" onclick="selectChip(this, 50000)"></div>
        <div class="chip" data-value="100K" onclick="selectChip(this, 100000)"></div>
        <div class="chip" data-value="500K" onclick="selectChip(this, 500000)"></div>
        <div class="chip" data-value="1M" onclick="selectChip(this, 1000000)"></div>
    </div>

    <div id="winScreen" class="overlay-screen screen-win">
        <div class="win-box">
            <div class="win-title">YOU WIN!</div>
            <div class="win-msg">Chúc mừng bạn đã đoán chính xác</div>
            <div id="winAmountText" class="plus-animation">+2.000.000đ</div>
        </div>
    </div>

    <div id="loseScreen" class="overlay-screen screen-lose">
        <div class="lose-box">
            <div class="lose-title">YOU LOSE</div>
            <div class="lose-msg">Rất tiếc, may mắn sẽ đến vào lần sau!</div>
        </div>
    </div>

    <script>
        const bar = document.getElementById('bar');
        const countdownEl = document.getElementById('countdown');
        const statusEl = document.getElementById('status');
        const lid = document.getElementById('lid');
        const diceElements = [document.getElementById('d1'), document.getElementById('d2'), document.getElementById('d3')];
        const balanceEl = document.getElementById('accountBalance');
        const statusXiuEl = document.getElementById('totalBetXiu');
        const statusTaiEl = document.getElementById('totalBetTai');

        let timeWait = 26;
        let timeNan = 10;
        const circumference = 2 * Math.PI * 120;
        bar.style.strokeDasharray = circumference;

        const faceTransforms = {
            1: { x: 0,   y: 0 }, 2: { x: 0,   y: -90 }, 3: { x: -90, y: 0 },
            4: { x: 90,  y: 0 }, 5: { x: 0,   y: 90 },  6: { x: 180, y: 0 }
        };

        let userBalance = 2000000;
        let currentSelectedValue = 1000; 
        
        let totalXiuBet = 0;
        let totalTaiBet = 0;
        let myXiuBet = 0;
        let myTaiBet = 0;

        let isBettingAllowed = true;
        let finalResults = [1, 1, 1];
        let currentX = 0, currentY = 0;
        let loopTimer = null;
        let botBetInterval = null;

        let isDragging = false;
        let startX = 0, startY = 0;

        setupDraggingEvents();
        startNewGameSession();

        function updateBalanceDisplay() {
            balanceEl.innerText = userBalance.toLocaleString('vi-VN') + 'đ';
        }

        function virtualDeposit() {
            userBalance += 2000000;
            updateBalanceDisplay();
            
            balanceEl.parentElement.style.transform = "scale(1.1)";
            balanceEl.parentElement.style.transition = "transform 0.1s ease";
            setTimeout(() => {
                balanceEl.parentElement.style.transform = "scale(1)";
            }, 200);
        }

        function startNewGameSession() {
            timeWait = 26; timeNan = 10;
            totalXiuBet = 0; totalTaiBet = 0;
            myXiuBet = 0; myTaiBet = 0;
            isBettingAllowed = true;

            statusXiuEl.innerText = '0đ';
            statusTaiEl.innerText = '0đ';
            document.querySelectorAll('.success-badge').forEach(badge => badge.classList.remove('show-fade'));
            document.getElementById('winScreen').classList.remove('active');
            document.getElementById('loseScreen').classList.remove('active');

            countdownEl.style.display = 'block';
            countdownEl.innerText = timeWait;
            statusEl.innerText = "Đang nhận cược...";
            bar.style.transition = 'none';
            bar.style.strokeDashoffset = 0;
            
            /* SỬA ĐỔI: Giữ nắp ẩn hoàn toàn khi đang đợi nhận cược và chuẩn bị lắc */
            lid.style.display = 'none';
            lid.style.transform = `translate(0px, 0px)`;
            lid.className = '';
            currentX = 0; currentY = 0;

            updateBalanceDisplay();

            clearInterval(loopTimer);
            loopTimer = setInterval(() => {
                timeWait--;
                if (timeWait >= 0) {
                    countdownEl.innerText = timeWait;
                    let offset = circumference - (timeWait / 26) * circumference;
                    bar.style.transition = 'stroke-dashoffset 1s linear';
                    bar.style.strokeDashoffset = offset;
                }
                if (timeWait <= 0) {
                    clearInterval(loopTimer);
                    clearInterval(botBetInterval); 
                    startRollingAnimation();
                }
            }, 1000);

            startBotBettingSimulation();
        }

        function startBotBettingSimulation() {
            clearInterval(botBetInterval);
            botBetInterval = setInterval(() => {
                if (!isBettingAllowed || timeWait <= 2) return;

                const isXiu = Math.random() > 0.5;
                const chipValues = [1000, 10000, 50000, 100000];
                const randomAmount = chipValues[Math.floor(Math.random() * chipValues.length)];

                if (isXiu) {
                    totalXiuBet += randomAmount;
                    updateBetDisplay(statusXiuEl, totalXiuBet + myXiuBet);
                } else {
                    totalTaiBet += randomAmount;
                    updateBetDisplay(statusTaiEl, totalTaiBet + myTaiBet);
                }
            }, 250);
        }

        function updateBetDisplay(element, totalValue) {
            element.innerText = totalValue.toLocaleString('vi-VN') + 'đ';
            element.classList.remove('bump-animation');
            void element.offsetWidth; 
            element.classList.add('bump-animation');
        }

        function selectChip(element, value) {
            document.querySelectorAll('.chip').forEach(c => c.classList.remove('selected'));
            element.classList.add('selected');
            currentSelectedValue = value;
        }

        function handleBetPlacement(event, side) {
            if (!isBettingAllowed) {
                statusEl.innerText = "Hết thời gian đặt cược!";
                return;
            }
            if (userBalance < currentSelectedValue) {
                alert("Số dư không đủ để thực hiện lượt cược này!");
                return;
            }

            userBalance -= currentSelectedValue;
            updateBalanceDisplay();

            const targetBox = event.currentTarget;
            const badge = targetBox.querySelector('.success-badge');
            badge.classList.remove('show-fade');
            void badge.offsetWidth;
            badge.classList.add('show-fade');

            if (side === 'XỈU') {
                myXiuBet += currentSelectedValue;
                updateBetDisplay(statusXiuEl, totalXiuBet + myXiuBet);
            } else {
                myTaiBet += currentSelectedValue;
                updateBetDisplay(statusTaiEl, totalTaiBet + myTaiBet);
            }

            const rect = targetBox.getBoundingClientRect();
            const ripple = document.createElement('span');
            ripple.className = 'ripple';
            ripple.style.left = `${event.clientX - rect.left}px`;
            ripple.style.top = `${event.clientY - rect.top}px`;
            targetBox.appendChild(ripple);
            setTimeout(() => ripple.remove(), 400);
        }

        function cancelAllBets() {
            if (!isBettingAllowed) return;
            userBalance += (myXiuBet + myTaiBet);
            myXiuBet = 0;
            myTaiBet = 0;
            updateBalanceDisplay();
            updateBetDisplay(statusXiuEl, totalXiuBet);
            updateBetDisplay(statusTaiEl, totalTaiBet);
            statusEl.innerText = "Đã hủy toàn bộ cược!";
        }

        function triggerAllIn() {
            if (!isBettingAllowed || userBalance <= 0) return;
            const chosenSide = myXiuBet >= myTaiBet ? 'XỈU' : 'TÀI';
            const allInVal = userBalance;
            userBalance = 0;
            updateBalanceDisplay();

            if (chosenSide === 'XỈU' || (myXiuBet === 0 && myTaiBet === 0)) {
                myXiuBet += allInVal;
                updateBetDisplay(statusXiuEl, totalXiuBet + myXiuBet);
            } else {
                myTaiBet += allInVal;
                updateBetDisplay(statusTaiEl, totalTaiBet + myTaiBet);
            }
        }

        function startRollingAnimation() {
            isBettingAllowed = false;
            statusEl.innerText = "Hệ thống đang lắc xúc xắc...";
            countdownEl.style.display = 'none';

            /* SỬA ĐỔI: Không cho nắp đậy xuất hiện khi đang lắc. Nắp vẫn ẩn đi. */
            lid.style.display = 'none';

            // Kích hoạt class xoay liên tục
            diceElements.forEach(dice => dice.classList.add('rolling'));

            setTimeout(() => {
                stopRollingAndGenerateResult();
            }, 4000); // Lắc trong 4 giây
        }

        function stopRollingAndGenerateResult() {
            // Tạo kết quả ngẫu nhiên
            finalResults = [
                Math.floor(Math.random() * 6) + 1,
                Math.floor(Math.random() * 6) + 1,
                Math.floor(Math.random() * 6) + 1
            ];

            // Tắt hiệu ứng rolling
            diceElements.forEach(dice => dice.classList.remove('rolling'));

            // Áp định dạng góc quay để hiển thị đúng mặt kết quả
            diceElements.forEach((dice, index) => {
                let res = finalResults[index];
                let trans = faceTransforms[res];
                // Thêm một vài vòng quay bổ sung để dừng lại tự nhiên
                dice.style.transform = `rotateX(${trans.x + 360}deg) rotateY(${trans.y + 360}deg)`;
            });

            /* SỬA ĐỔI: Sau khi lắc xong và hiển thị xúc xắc, nắp mới xuất hiện (dropIn) để người chơi nặn */
            statusEl.innerText = "Hãy kéo nắp để nặn kết quả!";
            lid.className = 'lid-drop';

            // Đếm ngược thời gian nặn kết quả (10s)
            timeNan = 10;
            countdownEl.style.display = 'block';
            countdownEl.innerText = timeNan;

            clearInterval(loopTimer);
            loopTimer = setInterval(() => {
                timeNan--;
                countdownEl.innerText = timeNan;
                if (timeNan <= 0) {
                    clearInterval(loopTimer);
                    autoRevealLid(); // Hết giờ tự động mở nắp
                }
            }, 1000);
        }

        function autoRevealLid() {
            if (lid.classList.contains('lid-flyaway')) return;
            lid.style.setProperty('--curr-x', `${currentX}px`);
            lid.style.setProperty('--curr-y', `${currentY}px`);
            lid.classList.add('lid-flyaway');
            setTimeout(() => {
                processGamePayout();
            }, 6000);
        }

        function processGamePayout() {
            countdownEl.style.display = 'none';
            const sum = finalResults[0] + finalResults[1] + finalResults[2];
            const gameResultText = (sum >= 11) ? 'TÀI' : 'XỈU';

            statusEl.innerText = `Kết quả: ${finalResults.join(' - ')} = ${sum} (${gameResultText})`;

            let winAmount = 0;
            if (gameResultText === 'XỈU' && myXiuBet > 0) winAmount = myXiuBet * 2;
            if (gameResultText === 'TÀI' && myTaiBet > 0) winAmount = myTaiBet * 2;

            if (winAmount > 0) {
                userBalance += winAmount;
                document.getElementById('winAmountText').innerText = `+${winAmount.toLocaleString('vi-VN')}đ`;
                document.getElementById('winScreen').classList.add('active');
            } else if (myXiuBet > 0 || myTaiBet > 0) {
                document.getElementById('loseScreen').classList.add('active');
            }

            updateBalanceDisplay();

            // Khởi động phiên mới sau 4 giây hiển thị thông báo
            setTimeout(() => {
                startNewGameSession();
            }, 4000);
        }

        // --- HỆ THỐNG KÉO NẶN NẮP (DRAG & DROP) ---
        function setupDraggingEvents() {
            lid.addEventListener('mousedown', startDrag);
            lid.addEventListener('touchstart', startDrag, { passive: true });

            window.addEventListener('mousemove', dragMove);
            window.addEventListener('touchmove', dragMove, { passive: false });

            window.addEventListener('mouseup', endDrag);
            window.addEventListener('touchend', endDrag);
        }

        function startDrag(e) {
            if (!isBettingAllowed && timeNan > 0) {
                isDragging = true;
                let clientX = e.clientX || e.touches[0].clientX;
                let clientY = e.clientY || e.touches[0].clientY;
                startX = clientX - currentX;
                startY = clientY - currentY;
            }
        }

        function dragMove(e) {
            if (!isDragging) return;
            if (e.cancelable) e.preventDefault();

            let clientX = e.clientX || (e.touches && e.touches[0].clientX);
            let clientY = e.clientY || (e.touches && e.touches[0].clientY);

            currentX = clientX - startX;
            currentY = clientY - startY;

            lid.style.transform = `translate(${currentX}px, ${currentY}px)`;

            // Nếu kéo nắp lệch đi xa hơn 120px, kích hoạt hiệu ứng bay nắp ra ngoài
            if (Math.abs(currentX) > 120 || Math.abs(currentY) > 120) {
                isDragging = false;
                clearInterval(loopTimer);
                autoRevealLid();
            }
        }

        function endDrag() {
            isDragging = false;
        }
    </script>
</body>
</html>
