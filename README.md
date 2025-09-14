<!DOCTYPE html>
<html lang="ko">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, user-scalable=no">
    <title>NEKBLE AI Dual Service App (Final Polished)</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <link rel="stylesheet" as="style" crossorigin href="https://cdn.jsdelivr.net/gh/orioncactus/pretendard@v1.3.9/dist/web/static/pretendard.min.css" />
    <style>
        :root {
            --font-size-base: clamp(14px, 1.2vw + 0.25rem, 16px);
            --font-size-title: clamp(3rem, 8vw, 4.5rem);
            --spacing-unit: clamp(1rem, 2vw, 1.5rem);
            --icon-size: clamp(4rem, 10vw, 5rem);
            
            /* Light Theme */
            --bg-color: transparent;
            --text-primary: #111827;
            --text-secondary: #4b5563;
            --icon-svg-color: #4b5563;
            --icon-bg-gradient: linear-gradient(135deg, #fdfbfb 0%, #ebedee 100%);
            --card-bg: rgba(255, 255, 255, 0.6);
            --card-border: rgba(255, 255, 255, 0.25);
            --card-shadow: rgba(0, 0, 0, 0.1);
            --close-btn-bg: rgba(0,0,0,0.1);
            --close-btn-text: rgba(0,0,0,0.5);
            --link-icon-bg: rgba(0,0,0,0.05);
            --theme: 'light';
        }

        @media (prefers-color-scheme: dark) {
            :root {
                /* Dark Theme */
                --bg-color: #111827;
                --text-primary: #f9fafb;
                --text-secondary: #d1d5db;
                --icon-svg-color: #f9fafb;
                --icon-bg-gradient: linear-gradient(135deg, #4a5568 0%, #2d3748 100%);
                --card-bg: rgba(20, 20, 30, 0.65);
                --card-border: rgba(255, 255, 255, 0.1);
                --card-shadow: rgba(0, 0, 0, 0.3);
                --close-btn-bg: rgba(255,255,255,0.1);
                --close-btn-text: rgba(255,255,255,0.5);
                --link-icon-bg: rgba(255,255,255,0.1);
                --theme: 'dark';
            }
        }

        html {
            font-size: var(--font-size-base);
        }
        body {
            min-height: 100vh;
            margin: 0;
            padding: var(--spacing-unit);
            overflow: hidden;
            display: flex;
            align-items: center;
            justify-content: center;
            background-color: var(--bg-color);
            color: var(--text-primary);
            font-family: 'Pretendard', -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, "Helvetica Neue", Arial, "Noto Sans", sans-serif, "Apple Color Emoji", "Segoe UI Emoji", "Segoe UI Symbol", "Noto Color Emoji";
            transition: background-color 0.3s ease, color 0.3s ease;
        }

        #launcher-wrapper {
            position: relative;
            display: flex;
            flex-direction: column;
            align-items: center;
            justify-content: center;
            transition: transform 0.4s cubic-bezier(0.4, 0, 0.2, 1), opacity 0.4s ease;
            will-change: transform, opacity;
            width: 100%;
            max-width: 900px;
        }
        
        #main-title {
            font-size: var(--font-size-title);
            font-weight: 800;
            margin-bottom: calc(var(--spacing-unit) * 1.5);
            text-align: center;
            background-size: 400% 100%;
            animation: progress-bar-animation 4s ease infinite;
        }
        
        #icon-container {
            display: flex;
            flex-wrap: wrap;
            justify-content: center;
            gap: calc(var(--spacing-unit) * 1.5);
            margin-top: calc(var(--spacing-unit) * 2);
        }
        
        #search-form {
            width: 100%;
            max-width: calc( (var(--icon-size) * 1.5 * 2) + var(--spacing-unit) * 1.5);
        }
        .search-input {
            background: var(--card-bg);
            border: 1px solid var(--card-border);
            backdrop-filter: blur(10px);
            -webkit-backdrop-filter: blur(10px);
            color: var(--text-primary);
            box-shadow: 0 4px 12px rgba(0,0,0,0.08);
            transition: all 0.2s ease;
        }

        .search-input:focus {
            outline: none;
            box-shadow: 0 6px 16px rgba(0,0,0,0.12);
            border-color: rgba(79, 70, 229, 0.5);
        }

        .search-input::placeholder {
            color: var(--text-secondary);
            opacity: 0.7;
        }
        
        @media (prefers-color-scheme: dark) {
            .search-input:focus {
                border-color: rgba(129, 140, 248, 0.5);
            }
        }

        .app-launcher-item {
            display: flex;
            flex-direction: column;
            align-items: center;
            gap: calc(var(--spacing-unit) * 0.5);
        }

        .icon-bg {
            width: calc(var(--icon-size) * 1.5);
            height: calc(var(--icon-size) * 1.5);
            border-radius: calc(var(--spacing-unit) * 1.5);
            background: var(--icon-bg-gradient);
            box-shadow: 0 0.85rem 2rem rgba(0, 0, 0, 0.12), 0 0.5rem 0.7rem rgba(0, 0, 0, 0.08);
            display: flex;
            align-items: center;
            justify-content: center;
            transition: all 0.2s ease-in-out;
            cursor: pointer;
        }
        
        .icon-bg svg {
            color: var(--icon-svg-color);
        }

        .app-launcher:hover .icon-bg {
            transform: scale(1.05);
            box-shadow: 0 1rem 2.5rem rgba(0, 0, 0, 0.15), 0 0.6rem 1rem rgba(0, 0, 0, 0.1);
        }
         .app-launcher:active .icon-bg {
            transform: scale(0.98);
        }
        
        .icon-label { display: none; }
       
        .app-view-container {
            position: absolute;
            top: 50%;
            left: 50%;
            opacity: 0;
            pointer-events: none;
            transform: translate(-50%, -50%) scale(0.95);
            transition: transform 0.4s cubic-bezier(0.4, 0, 0.2, 1), opacity 0.4s ease;
            will-change: transform, opacity;
            width: clamp(320px, 90%, 1100px);
            max-height: 90vh;
            overflow-y: auto;
            padding: var(--spacing-unit);
            background: var(--card-bg);
            -webkit-backdrop-filter: blur(30px);
            backdrop-filter: blur(30px);
            border-radius: calc(var(--spacing-unit) * 1.8);
            border: 1px solid var(--card-border);
            box-shadow: 0 8px 32px 0 var(--card-shadow);
        }
        
        body.app-active #launcher-wrapper {
            opacity: 0;
            transform: scale(0.8);
            pointer-events: none;
        }
        body.service-app-active #service-app-container,
        body.trend-app-active #trend-app-container {
            opacity: 1;
            pointer-events: auto;
            transform: translate(-50%, -50%) scale(1);
        }
        
        .close-app-btn {
            position: absolute;
            top: 1.5rem;
            right: 1.5rem;
            width: 2.5rem;
            height: 2.5rem;
            background-color: var(--close-btn-bg);
            color: var(--close-btn-text);
            border: none;
            border-radius: 50%;
            cursor: pointer;
            font-size: 1.4rem;
            line-height: 2.5rem;
            text-align: center;
            transition: background-color 0.2s, color 0.2s, transform 0.2s;
            z-index: 1001;
        }
        .close-app-btn:hover {
            background-color: rgba(0,0,0,0.2);
            color: rgba(0,0,0,0.8);
            transform: scale(1.1);
        }
        @media (prefers-color-scheme: dark) {
            .close-app-btn:hover {
                 background-color: rgba(255,255,255,0.2);
                color: rgba(255,255,255,0.8);
            }
        }


        .app-content-grid {
            display: grid;
            gap: var(--spacing-unit);
            grid-template-columns: 1fr;
        }

        .glass-card {
            background: var(--card-bg);
            border-radius: 1.4rem;
            border: 1px solid var(--card-border);
            box-shadow: 0 2px 10px var(--card-shadow);
            position: relative;
            padding: var(--spacing-unit);
        }
         .glass-card::before {
            content: '';
            position: absolute; top: 0; left: 0; right: 0; bottom: 0;
            border-radius: 1.4rem;
            box-shadow: inset 0 2px 2px 0 rgba(255,255,255,0.4), inset 0 -2px 2px 0 rgba(0,0,0,0.05);
            pointer-events: none;
        }
        @media (prefers-color-scheme: dark) {
            .glass-card::before {
                 box-shadow: inset 0 2px 2px 0 rgba(255,255,255,0.08), inset 0 -2px 2px 0 rgba(0,0,0,0.2);
            }
        }
        
        @keyframes progress-bar-animation { 0%{background-position:0% 50%} 50%{background-position:100% 50%} 100%{background-position:0% 50%} }
        #progress-bar { background-size: 400% 100%; animation: progress-bar-animation 2s ease infinite; }
        
        #trend-list li {
            background-color: rgba(255,255,255,0.5);
            padding: 0.75rem var(--spacing-unit);
            border-radius: 0.75rem;
            margin-bottom: 0.5rem;
            display: flex;
            flex-direction: column;
            font-weight: 500;
            transition: background-color 0.2s, transform 0.2s;
        }
        @media (prefers-color-scheme: dark) {
            #trend-list li {
                 background-color: rgba(255,255,255,0.05);
            }
        }
        #trend-list li:hover {
            background-color: rgba(255,255,255,0.8);
        }
        @media (prefers-color-scheme: dark) {
            #trend-list li:hover {
                 background-color: rgba(255,255,255,0.15);
            }
        }
        .trend-item-main {
             display: flex;
            align-items: center;
            justify-content: space-between;
        }
        #trend-list .rank-keyword {
            display: flex;
            align-items: center;
            flex-grow: 1;
            min-width: 0;
        }
        #trend-list .rank {
            font-weight: 700;
            font-size: 1.1em;
            width: 2.5rem;
            flex-shrink: 0;
            color: #4f46e5;
        }
        @media (prefers-color-scheme: dark) {
             #trend-list .rank { color: #818cf8; }
        }
        #trend-list .keyword {
            white-space: nowrap;
            overflow: hidden;
            text-overflow: ellipsis;
            color: var(--text-primary);
        }
        .link-icons-container {
            display: flex;
            align-items: center;
            gap: 0.5rem;
            flex-shrink: 0;
        }
        .link-icon {
            display: flex;
            align-items: center;
            justify-content: center;
            width: 1.75rem;
            height: 1.75rem;
            border-radius: 50%;
            background-color: var(--link-icon-bg);
            transition: background-color 0.2s;
        }
        .link-icon:hover {
            background-color: rgba(0,0,0,0.15);
        }
         @media (prefers-color-scheme: dark) {
            .link-icon:hover {
                background-color: rgba(255,255,255,0.2);
            }
        }
        .gemini-summary-btn {
            background: none; border: none; cursor: pointer; padding: 0;
            display: flex; align-items: center; justify-content: center;
            width: 1.75rem; height: 1.75rem; border-radius: 50%;
            background-color: var(--link-icon-bg);
            transition: background-color 0.2s;
        }
        .gemini-summary-btn:hover {
             background-color: rgba(0,0,0,0.15);
        }
        @media (prefers-color-scheme: dark) {
            .gemini-summary-btn:hover {
                 background-color: rgba(255,255,255,0.2);
            }
        }
        .gemini-summary-content {
            display: none;
            margin-top: 0.75rem;
            padding-top: 0.75rem;
            border-top: 1px solid var(--card-border);
            font-size: 0.9em;
            color: var(--text-secondary);
            line-height: 1.6;
            white-space: pre-wrap;
        }
        .gemini-summary-content ul, .gemini-result-area ul { list-style-type: disc; padding-left: 1.5rem; margin-top: 0.5rem; margin-bottom: 0.5rem; }
        .gemini-summary-content strong, .gemini-result-area strong { font-weight: 600; color: var(--text-primary); }

        #trend-header {
            display: flex;
            align-items: center;
            justify-content: center;
            position: relative;
            padding-left: 3rem; 
            padding-right: 3rem; 
        }
        #trend-header-title {
            text-align: center;
        }
        #refresh-trends-btn {
            position: absolute;
            left: 1.25rem;
            top: 50%;
            transform: translateY(-50%);
            width: 2.5rem;
            height: 2.5rem;
            background: none;
            border: none;
            cursor: pointer;
            padding: 0.4rem;
            border-radius: 50%;
            color: var(--text-secondary);
            transition: background-color 0.2s, transform 0.3s;
        }
        #refresh-trends-btn:hover {
            background-color: rgba(0,0,0,0.08);
        }
        @media (prefers-color-scheme: dark) {
            #refresh-trends-btn:hover {
                background-color: rgba(255,255,255,0.1);
            }
        }

        #refresh-trends-btn.loading {
            animation: spin 1s linear infinite;
        }
        #trend-list-container {
            position: relative;
            min-height: 100px;
        }
        #trend-loader {
            position: absolute;
            top: 0; left: 0; right: 0; bottom: 0;
            display: flex;
            align-items: center;
            justify-content: center;
            background-color: var(--card-bg);
            border-radius: 1rem;
            opacity: 0;
            pointer-events: none;
            transition: opacity 0.3s;
        }
        #trend-loader.visible {
            opacity: 1;
        }
        
        .loading-state {
            display: flex;
            align-items: center;
            justify-content: center;
            min-height: 4rem;
            text-align: center;
        }
        .spinner {
            width: 1.2rem; height: 1.2rem;
            border: 2px solid #f3f3f3;
            border-top: 2px solid #4f46e5;
            border-radius: 50%;
            animation: spin 1s linear infinite;
            display: inline-block;
        }

        @keyframes spin { from { transform: rotate(0deg); } to { transform: rotate(360deg); } }

        @media (min-width: 768px) {
            #trend-app-container {
                 width: clamp(420px, 60%, 700px);
            }
            #service-app-container .app-content-grid {
                grid-template-columns: minmax(0, 1.2fr) minmax(0, 1fr);
            }
            #header-card { grid-column: 1 / -1; }
            #progress-card { grid-column: 1; grid-row: 2; }
            #finance-card { grid-column: 2; grid-row: 2; }
            #metaphor-card { grid-column: 1; grid-row: 3; align-self: start; }
            #details-card { grid-column: 2; grid-row: 3; }
        }
    </style>
</head>
<body>

    <div id="launcher-wrapper">
        <h1 id="main-title" class="bg-gradient-to-r from-sky-400 via-blue-500 via-purple-600 to-pink-500 bg-clip-text text-transparent">NEKBLE</h1>
        <form id="search-form" action="https://www.google.com/search" method="GET" target="_blank" class="w-full max-w-md mx-auto">
            <div class="relative">
                <input type="text" name="q" placeholder="" class="search-input w-full pl-6 pr-12 py-3 rounded-full border-none">
                <button type="submit" class="absolute right-2 top-1/2 -translate-y-1/2 p-2">
                    <svg class="w-6 h-6 text-gray-400" fill="none" stroke="currentColor" viewBox="0 0 24 24" xmlns="http://www.w3.org/2000/svg"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M21 21l-6-6m2-5a7 7 0 11-14 0 7 7 0 0114 0z"></path></svg>
                </button>
            </div>
        </form>
        <div id="icon-container">
            <div class="app-launcher-item">
                <div id="service-app-launcher" class="app-launcher">
                    <div class="icon-bg">
                        <svg xmlns="http://www.w3.org/2000/svg" class="w-2/3 h-2/3" viewBox="0 0 20 20" fill="currentColor">
                            <path d="M10.894 2.553a1 1 0 00-1.788 0l-7 14a1 1 0 001.169 1.409l5-1.429A1 1 0 009 15.571V11a1 1 0 112 0v4.571a1 1 0 00.725.962l5 1.428a1 1 0 001.17-1.408l-7-14z" />
                        </svg>
                    </div>
                </div>
                <span class="icon-label">복무율 계산기</span>
            </div>
            <div class="app-launcher-item">
                <div id="trend-app-launcher" class="app-launcher">
                    <div class="icon-bg">
                        <svg xmlns="http://www.w3.org/2000/svg" class="w-2/3 h-2/3" fill="none" viewBox="0 0 24 24" stroke-width="1.5" stroke="currentColor">
                            <path stroke-linecap="round" stroke-linejoin="round" d="M2.25 18 9 11.25l4.306 4.306a11.95 11.95 0 0 1 5.814-5.518l2.74-1.22m0 0-5.94-2.281m5.94 2.28-2.28 5.941" />
                        </svg>
                    </div>
                </div>
                <span class="icon-label">실시간 검색어</span>
            </div>
        </div>
    </div>

    <!-- Service Tracker App -->
    <div id="service-app-container" class="app-view-container">
        <button class="close-app-btn">&times;</button>
        <div class="app-content-grid">
            <header id="header-card" class="text-center">
                <h1 class="text-2xl font-bold" style="font-family: 'Pretendard', sans-serif;">공군 861기 전역 계산기</h1>
            </header>
            <div id="progress-card" class="glass-card text-center">
                <p class="text-sm font-medium text-blue-600">현재 복무율</p>
                <p id="progress-percent" class="text-4xl lg:text-5xl font-bold my-1 tabular-nums bg-gradient-to-r from-sky-400 via-blue-500 via-purple-600 to-pink-500 bg-clip-text text-transparent">0.0%</p>
                <div class="w-full bg-gray-200 rounded-full h-2.5 mt-2 overflow-hidden">
                    <div id="progress-bar" class="bg-gradient-to-r from-sky-400 via-blue-500 via-purple-600 to-pink-500 h-full rounded-full"></div>
                </div>
            </div>
            <div id="finance-card" class="grid grid-cols-2 gap-3">
                <div class="glass-card !p-3 flex flex-col items-center justify-center h-full">
                    <svg class="w-6 h-6 mb-2 text-gray-500" xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24" stroke-width="1.5" stroke="currentColor"><path stroke-linecap="round" stroke-linejoin="round" d="M2.25 8.25h19.5M2.25 9h19.5m-16.5 5.25h6m-6 2.25h3m-3.75 3h15a2.25 2.25 0 0 0 2.25-2.25V6.75A2.25 2.25 0 0 0 19.5 4.5h-15a2.25 2.25 0 0 0-2.25 2.25v10.5A2.25 2.25 0 0 0 4.5 19.5Z" /></svg>
                    <p class="text-xs text-gray-500 leading-tight">이달의 월급</p>
                    <p class="text-xl font-bold mt-1"><span id="current-salary">0</span>원</p>
                </div>
                <div class="glass-card !p-3 flex flex-col items-center justify-center h-full">
                    <svg class="w-6 h-6 mb-2 text-gray-500" xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24" stroke-width="1.5" stroke="currentColor"><path stroke-linecap="round" stroke-linejoin="round" d="M12 6.042A8.967 8.967 0 0 0 6 3.75c-1.052 0-2.062.18-3 .512v14.25A8.987 8.987 0 0 1 6 18c2.305 0 4.408.867 6 2.292m0-14.25a8.966 8.966 0 0 1 6-2.292c1.052 0 2.062.18 3 .512v14.25A8.987 8.987 0 0 0 18 18a8.967 8.967 0 0 0-6 2.292m0-14.25v14.25" /></svg>
                    <p class="text-xs text-gray-500 leading-tight">군적금 수령액</p>
                    <p class="text-xl font-bold mt-1"><span id="total-payout">0</span>원</p>
                </div>
            </div>
            <div id="metaphor-card" class="glass-card text-center">
                <p class="text-4xl mb-2" id="metaphor-icon">🛫</p>
                <p id="metaphor-title" class="font-semibold text-base">출격 준비 완료</p>
                <p class="text-xs" id="metaphor-text">전역을 향한 긴 비행이 곧 시작됩니다.</p>
            </div>
            <div id="details-card" class="grid grid-cols-3 gap-2 text-center">
                <div class="glass-card !p-2 flex flex-col items-center justify-center h-full">
                    <svg class="w-5 h-5 mb-1 text-gray-500" xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24" stroke-width="1.5" stroke="currentColor"><path stroke-linecap="round" stroke-linejoin="round" d="M19.5 13.5 12 21m0 0-7.5-7.5M12 21V3" /></svg>
                    <p class="text-[10px] text-gray-500 leading-tight">입대일</p><p class="text-sm font-semibold mt-1" id="enlist-date"></p>
                </div>
                <div class="glass-card !p-2 flex flex-col items-center justify-center h-full">
                    <svg class="w-5 h-5 mb-1 text-gray-500" xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24" stroke-width="1.5" stroke="currentColor"><path stroke-linecap="round" stroke-linejoin="round" d="M4.5 10.5 12 3m0 0 7.5 7.5M12 3v18" /></svg>
                    <p class="text-[10px] text-gray-500 leading-tight">전역일</p><p class="text-sm font-semibold mt-1" id="discharge-date-display"></p>
                </div>
                <div class="glass-card !p-2 flex flex-col items-center justify-center h-full">
                    <svg class="w-5 h-5 mb-1 text-gray-500" xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24" stroke-width="1.5" stroke="currentColor"><path stroke-linecap="round" stroke-linejoin="round" d="M6.75 3v2.25M17.25 3v2.25M3 18.75V7.5a2.25 2.25 0 0 1 2.25-2.25h13.5A2.25 2.25 0 0 1 21 7.5v11.25m-18 0A2.25 2.25 0 0 0 5.25 21h13.5A2.25 2.25 0 0 0 21 18.75m-18 0h18" /></svg>
                    <p class="text-[10px] text-gray-500 leading-tight">총 복무일</p><p class="text-sm font-semibold mt-1"><span id="total-days"></span></p>
                </div>
                <div class="glass-card !p-2 flex flex-col items-center justify-center h-full">
                    <svg class="w-5 h-5 mb-1 text-gray-500" xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24" stroke-width="1.5" stroke="currentColor"><path stroke-linecap="round" stroke-linejoin="round" d="M9 12.75 11.25 15 15 9.75M21 12a9 9 0 1 1-18 0 9 9 0 0 1 18 0Z" /></svg>
                    <p class="text-[10px] text-gray-500 leading-tight">복무한 날</p><p class="text-sm font-semibold mt-1"><span id="served-days"></span></p>
                </div>
                <div class="glass-card !p-2 flex flex-col items-center justify-center h-full">
                    <svg class="w-5 h-5 mb-1 text-gray-500" xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24" stroke-width="1.5" stroke="currentColor"><path stroke-linecap="round" stroke-linejoin="round" d="M12 6v6h4.5m4.5 0a9 9 0 1 1-18 0 9 9 0 0 1 18 0Z" /></svg>
                    <p class="text-[10px] text-gray-500 leading-tight">남은 날</p><p class="text-sm font-semibold mt-1"><span id="remaining-days"></span></p>
                </div>
                <div class="glass-card !p-2 flex flex-col items-center justify-center h-full">
                     <svg class="w-5 h-5 mb-1 text-gray-500" xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24" stroke-width="1.5" stroke="currentColor"><path stroke-linecap="round" stroke-linejoin="round" d="M12 6v6h4.5m4.5 0a9 9 0 1 1-18 0 9 9 0 0 1 18 0Z" /></svg>
                    <p class="text-[10px] text-gray-500 leading-tight">현재 시각</p><p class="text-sm font-semibold tabular-nums mt-1" id="current-time"></p>
                </div>
            </div>
        </div>
    </div>
    
    <!-- Trend App -->
    <div id="trend-app-container" class="app-view-container">
        <button class="close-app-btn">&times;</button>
        <div class="app-content-grid app-content-grid-single">
            <header id="trend-header">
                 <div id="trend-header-title">
                    <h1 class="text-2xl font-bold" style="font-family: 'Pretendard', sans-serif;">실시간 트렌드</h1>
                    <p class="text-xs mt-1">나무위키 인기 검색어</p>
                </div>
                <button id="refresh-trends-btn" title="새로고침">
                    <svg class="w-full h-full" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"><polyline points="23 4 23 10 17 10"></polyline><path d="M20.49 15a9 9 0 1 1-2.12-9.36L23 10"></path></svg>
                </button>
            </header>
            <div id="trend-list-container">
                 <ol id="trend-list" class="mt-2"></ol>
                 <div id="trend-loader">
                    <svg class="animate-spin h-8 w-8 text-indigo-500" xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24"><circle class="opacity-25" cx="12" cy="12" r="10" stroke="currentColor" stroke-width="4"></circle><path class="opacity-75" fill="currentColor" d="M4 12a8 8 0 018-8V0C5.373 0 0 5.373 0 12h4zm2 5.291A7.962 7.962 0 014 12H0c0 3.042 1.135 5.824 3 7.938l3-2.647z"></path></svg>
                 </div>
            </div>
        </div>
    </div>

    <script>
        const body = document.body;
        // Launchers
        const serviceAppLauncher = document.getElementById('service-app-launcher');
        const trendAppLauncher = document.getElementById('trend-app-launcher');
        
        // App Containers
        const serviceAppContainer = document.getElementById('service-app-container');
        const trendAppContainer = document.getElementById('trend-app-container');

        serviceAppLauncher.addEventListener('click', () => {
            body.classList.add('app-active', 'service-app-active');
            body.classList.remove('trend-app-active');
        });
        trendAppLauncher.addEventListener('click', () => {
            body.classList.add('app-active', 'trend-app-active');
            body.classList.remove('service-app-active');
        });

        // Close Buttons
        document.querySelectorAll('.close-app-btn').forEach(btn => {
            btn.addEventListener('click', () => {
                body.classList.remove('app-active', 'service-app-active', 'trend-app-active');
            });
        });
        
        // --- Gemini API Logic ---
        const GEMINI_API_KEY = ""; // Provided by environment
        const GEMINI_API_URL = `https://generativelanguage.googleapis.com/v1beta/models/gemini-2.5-flash-preview-05-20:generateContent?key=${GEMINI_API_KEY}`;
        
        function formatGeminiResponse(text) {
            const safeText = text.replace(/</g, "&lt;").replace(/>/g, "&gt;");
            
            let html = safeText
                .replace(/\*\*(.*?)\*\*/g, '<strong>$1</strong>')
                .replace(/^- (.*$)/gm, '<li>$1</li>')
                .replace(/^\* (.*$)/gm, '<li>$1</li>')
                .replace(/^\• (.*$)/gm, '<li>$1</li>');

            html = html.replace(/(<li>.*<\/li>)/gs, '<ul>$1</ul>').replace(/<\/ul>\s*<ul>/gs, '');

            return html.replace(/\n/g, '<br>').replace(/<br>\s*<ul>/g, '<ul>').replace(/<\/ul>\s*<br>/g, '</ul>');
        }

        async function callGeminiAPI(prompt, useGrounding = false, retries = 3, delay = 1000) {
            const payload = {
                contents: [{ parts: [{ text: prompt }] }],
            };
            if (useGrounding) {
                payload.tools = [{ "google_search": {} }];
            }

            for (let i = 0; i < retries; i++) {
                try {
                    const response = await fetch(GEMINI_API_URL, {
                        method: 'POST',
                        headers: { 'Content-Type': 'application/json' },
                        body: JSON.stringify(payload)
                    });

                    if (!response.ok) {
                        throw new Error(`HTTP error! status: ${response.status}`);
                    }
                    
                    const result = await response.json();
                    const candidate = result.candidates?.[0];
                    if (candidate && candidate.content?.parts?.[0]?.text) {
                        return candidate.content.parts[0].text;
                    } else {
                        if (candidate && candidate.finishReason !== "STOP") {
                            return `AI 응답 생성에 실패했습니다. (사유: ${candidate.finishReason})`;
                        }
                        throw new Error("Invalid response structure from Gemini API");
                    }
                } catch (error) {
                    console.error(`Gemini API call failed (attempt ${i + 1}):`, error);
                    if (i === retries - 1) { // Last retry
                        return "AI 응답을 가져오는 데 실패했습니다. 잠시 후 다시 시도해 주세요.";
                    }
                    await new Promise(res => setTimeout(res, delay * Math.pow(2, i))); // Exponential backoff
                }
            }
        }


        // --- Service Tracker Logic ---
        const enlistmentDate = new Date('2024-09-09T00:00:00');
        const dischargeDate = new Date('2026-06-08T00:00:00'); 
        
        const pfcDate = new Date(enlistmentDate); pfcDate.setMonth(pfcDate.getMonth() + 2);
        const cplDate = new Date(pfcDate); cplDate.setMonth(cplDate.getMonth() + 6);
        const sgtDate = new Date(cplDate); sgtDate.setMonth(sgtDate.getMonth() + 6);

        const payScales = { 2024: { '이병': 640000, '일병': 800000, '상병': 1000000, '병장': 1250000 }, 2025: { '이병': 860000, '일병': 1000000, '상병': 1200000, '병장': 1500000 }, 2026: { '이병': 860000, '일병': 1000000, '상병': 1200000, '병장': 1500000 } };
        const savingsInfo = { 2024: { amount: 400000 }, 2025: { amount: 550000 }, 2026: { amount: 550000 } };
        const serviceElements = { progressPercent: document.getElementById('progress-percent'), progressBar: document.getElementById('progress-bar'), totalDays: document.getElementById('total-days'), servedDays: document.getElementById('served-days'), remainingDays: document.getElementById('remaining-days'), enlistDate: document.getElementById('enlist-date'), dischargeDateDisplay: document.getElementById('discharge-date-display'), currentTime: document.getElementById('current-time'), metaphorIcon: document.getElementById('metaphor-icon'), metaphorTitle: document.getElementById('metaphor-title'), metaphorText: document.getElementById('metaphor-text'), currentSalary: document.getElementById('current-salary'), totalPayout: document.getElementById('total-payout') };
        const metaphors = [ { limit: 10, icon: '🛫', title: '이륙 허가!', text: '이제 막 활주로에 들어섰습니다.'}, { limit: 25, icon: '✈️', title: '상승 중', text: '순항 고도를 향해 날아오르고 있습니다.'}, { limit: 50, icon: '☁️', title: '순항 고도 진입', text: '비행의 절반을 향해 안정적으로 비행 중입니다.'}, { limit: 75, icon: '🌍', title: '지상이 보인다!', text: '긴 비행의 끝이 보이기 시작합니다.'}, { limit: 90, icon: '🛬', title: '착륙 준비', text: '목표 지점이 코앞입니다.'}, { limit: 99.9, icon: '🎉', title: '최종 접근', text: '활주로에 거의 닿았습니다!'}, { limit: 101, icon: '🥳', title: '전역!', text: '축하합니다! 무사히 귀환했습니다.'} ];
        const msToDays = (ms) => ms / (1000 * 60 * 60 * 24);
        const formatDate = (date) => `${date.getFullYear()} ${String(date.getMonth() + 1).padStart(2, '0')} ${String(date.getDate()).padStart(2, '0')}`;
        const formatTime = (date) => date.toLocaleTimeString('ko-KR', { hour: '2-digit', minute: '2-digit', hour12: false });
        
        function getCurrentRank(now) { if (now >= sgtDate) return '병장'; if (now >= cplDate) return '상병'; if (now >= pfcDate) return '일병'; return '이병'; }
        function calculateTotalPayout() {
            let totalPrincipal = 0, totalSupport = 0; const lastPaymentMonth = new Date(dischargeDate); lastPaymentMonth.setMonth(lastPaymentMonth.getMonth() - 1); const govSupportRate = 1.0;
            for (let y = enlistmentDate.getFullYear(); y <= lastPaymentMonth.getFullYear(); y++) {
                const startMonth = (y === enlistmentDate.getFullYear()) ? enlistmentDate.getMonth() : 0; const endMonth = (y === lastPaymentMonth.getFullYear()) ? lastPaymentMonth.getMonth() : 11;
                for (let m = startMonth; m <= endMonth; m++) { const savingAmount = savingsInfo[y]?.amount || 0; totalPrincipal += savingAmount; totalSupport += savingAmount * govSupportRate; }
            }
            return totalPrincipal + totalSupport;
        }
        function updateServiceStatus() {
            const now = new Date(); const totalServiceMillis = dischargeDate - enlistmentDate; let servedMillis = now - enlistmentDate;
            if (servedMillis < 0) servedMillis = 0; if (now > dischargeDate) servedMillis = totalServiceMillis;
            const percentage = (servedMillis / totalServiceMillis) * 100;
            const remainingDays = Math.ceil(msToDays(dischargeDate - now));
            const currentRank = getCurrentRank(now);
            serviceElements.progressPercent.textContent = `${percentage.toFixed(7)}%`; serviceElements.progressBar.style.width = `${percentage}%`;
            serviceElements.totalDays.textContent = `${Math.floor(msToDays(totalServiceMillis)).toLocaleString()}일`; serviceElements.servedDays.textContent = `${Math.floor(msToDays(servedMillis)).toLocaleString()}일`; serviceElements.remainingDays.textContent = `${remainingDays.toLocaleString()}일`; serviceElements.currentTime.textContent = formatTime(now);
            const currentYear = now.getFullYear();
            const baseSalary = payScales[currentYear]?.[currentRank] || 0; const monthlySaving = savingsInfo[currentYear]?.amount || 0;
            serviceElements.currentSalary.textContent = (baseSalary - monthlySaving).toLocaleString();
            const currentMetaphor = metaphors.find(m => percentage < m.limit) || metaphors[metaphors.length - 1];
            serviceElements.metaphorIcon.textContent = currentMetaphor.icon; serviceElements.metaphorText.textContent = currentMetaphor.text; serviceElements.metaphorTitle.textContent = currentMetaphor.title;
        }
        
        function initializeServiceTracker() {
            serviceElements.enlistDate.textContent = formatDate(enlistmentDate); serviceElements.dischargeDateDisplay.textContent = formatDate(dischargeDate);
            serviceElements.totalPayout.textContent = calculateTotalPayout().toLocaleString();
            updateServiceStatus(); setInterval(updateServiceStatus, 50);
        }
        
        // --- Trend App Logic ---
        const trendList = document.getElementById('trend-list');
        const trendLoader = document.getElementById('trend-loader');
        const refreshBtn = document.getElementById('refresh-trends-btn');

        async function fetchTrends() {
            trendLoader.classList.add('visible');
            refreshBtn.classList.add('loading');
            trendList.innerHTML = '';
            
            try {
                const apiUrl = 'https://search.namu.wiki/api/ranking';
                const proxyUrl = 'https://corsproxy.io/?';
                const response = await fetch(`${proxyUrl}${encodeURIComponent(apiUrl)}`);

                if (!response.ok) {
                    const errorText = await response.text();
                    throw new Error(`HTTP error! status: ${response.status}, message: ${errorText}`);
                }
                const keywords = await response.json();
                
                if (keywords && keywords.length > 0) {
                    keywords.forEach((keyword, index) => {
                        const li = document.createElement('li');
                        li.dataset.keyword = keyword;
                        const encodedKeyword = encodeURIComponent(keyword);
                        const namuUrl = `https://namu.wiki/w/${encodedKeyword}`;
                        const arcaUrl = `https://arca.live/b/namuhotnow?target=all&keyword=${encodedKeyword}`;

                        li.innerHTML = `
                            <div class="trend-item-main">
                                <div class="rank-keyword">
                                    <span class="rank">${index + 1}</span>
                                    <span class="keyword">${keyword}</span>
                                </div>
                                <div class="link-icons-container">
                                    <button class="gemini-summary-btn" title="AI 요약 보기">
                                        <svg class="w-4 h-4 text-gray-600" fill="none" stroke="currentColor" viewBox="0 0 24 24" xmlns="http://www.w3.org/2000/svg"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M9.663 17h4.673M12 3v1m6.364 1.636l-.707.707M21 12h-1M4 12H3m3.343-5.657l-.707-.707m2.828 9.9a5 5 0 117.072 0l-.548.547A3.374 3.374 0 0014 18.469V19a2 2 0 11-4 0v-.531c0-.895-.356-1.754-.988-2.386l-.548-.547z"></path></svg>
                                    </button>
                                    <a href="${namuUrl}" target="_blank" class="link-icon" title="나무위키로 보기">
                                        <svg class="w-4 h-4 text-gray-600" fill="none" stroke="currentColor" viewBox="0 0 24 24" xmlns="http://www.w3.org/2000/svg"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M12 6.253v13m0-13C10.832 5.477 9.246 5 7.5 5S4.168 5.477 3 6.253v13C4.168 18.477 5.754 18 7.5 18s3.332.477 4.5 1.253m0-13C13.168 5.477 14.754 5 16.5 5c1.747 0 3.332.477 4.5 1.253v13C19.832 18.477 18.246 18 16.5 18c-1.746 0-3.332.477-4.5 1.253"></path></svg>
                                    </a>
                                    <a href="${arcaUrl}" target="_blank" class="link-icon" title="아카라이브에서 검색">
                                        <svg class="w-4 h-4 text-gray-600" fill="none" stroke="currentColor" viewBox="0 0 24 24" xmlns="http://www.w3.org/2000/svg"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M21 21l-6-6m2-5a7 7 0 11-14 0 7 7 0 0114 0z"></path></svg>
                                    </a>
                                </div>
                            </div>
                            <div class="gemini-summary-content"></div>
                        `;
                        trendList.appendChild(li);
                    });
                } else {
                     throw new Error('No keywords returned from API.');
                }

            } catch (error) {
                console.error('트렌드 데이터를 가져오는 데 실패했습니다:', error);
                trendList.innerHTML = `<li class="text-center !bg-transparent"><span class="text-secondary">데이터를 불러오는 데 실패했습니다.<br>잠시 후 다시 시도해 주세요.</span></li>`;
            } finally {
                trendLoader.classList.remove('visible');
                refreshBtn.classList.remove('loading');
            }
        }
        
        trendList.addEventListener('click', async (e) => {
            const summaryBtn = e.target.closest('.gemini-summary-btn');
            if (!summaryBtn) return;
            
            const listItem = summaryBtn.closest('li');
            const keyword = listItem.dataset.keyword;
            const summaryContent = listItem.querySelector('.gemini-summary-content');

            if (summaryContent.style.display === 'block') {
                summaryContent.style.display = 'none';
                return;
            }
            
            summaryContent.style.display = 'block';

            if (summaryContent.innerHTML === '') {
                summaryContent.classList.add('loading-state');
                summaryContent.innerHTML = '<div class="spinner"></div><span class="ml-2 text-sm text-gray-500">AI가 트렌드 이유를 분석 중입니다...</span>';
                const prompt = `You are a helpful trend analyst. The search term '${keyword}' is currently trending in South Korea. Using Google Search, find out why this term is trending right now and provide a concise, two-sentence summary in Korean. Focus on the most recent events or reasons for its popularity.`;
                const result = await callGeminiAPI(prompt, true);
                summaryContent.classList.remove('loading-state');
                summaryContent.innerHTML = formatGeminiResponse(result);
            }
        });

        refreshBtn.addEventListener('click', fetchTrends);
        
        // --- Theme Watcher ---
        const colorSchemeQuery = window.matchMedia('(prefers-color-scheme: dark)');
        const applySystemTheme = (e) => {
            const newTheme = e.matches ? 'dark' : 'light';
            document.documentElement.className = newTheme;
        }
        colorSchemeQuery.addEventListener('change', applySystemTheme);
        
        // --- Initialize Everything ---
        applySystemTheme(colorSchemeQuery); // Apply on initial load
        initializeServiceTracker();
        fetchTrends();
    </script>
</body>
</html>



