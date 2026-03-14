<!DOCTYPE html>
<html lang="ko">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>TRTD-4444: The Road To Death</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <link href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.0.0/css/all.min.css" rel="stylesheet">
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Nosifer&family=Noto+Sans+KR:wght@300;400;700;900&display=swap');

        :root {
            --blood-red: #8b0000;
            --admin-gold: #b2a15f;
            --scout-blue: #3b82f6;
            --tooth-crimson: #ef4444;
            --neutral-zinc: #71717a;
        }

        body {
            background-color: #050505;
            color: #e4e4e7;
            font-family: 'Noto Sans KR', sans-serif;
            margin: 0;
            height: 100vh;
            display: flex;
            flex-direction: column;
            overflow: hidden;
        }

        .blood-text {
            font-family: 'Nosifer', cursive;
            color: var(--blood-red);
            text-shadow: 2px 2px 4px #000;
        }

        .glass-panel {
            background: rgba(15, 15, 15, 0.95);
            border: 1px solid rgba(255, 255, 255, 0.05);
            border-radius: 12px;
            backdrop-filter: blur(10px);
        }

        .nav-btn {
            padding: 14px 18px;
            text-align: center;
            border-bottom: 2px solid transparent;
            cursor: pointer;
            font-weight: 500;
            color: #71717a;
            transition: all 0.3s ease;
            white-space: nowrap;
        }
        .nav-btn.active {
            border-bottom-color: var(--blood-red);
            color: #ffffff;
            background: linear-gradient(to bottom, transparent, rgba(139, 0, 0, 0.1));
        }

        .char-card {
            background: #0f0f0f;
            border: 1px solid #1f1f1f;
            border-radius: 12px;
            overflow: hidden;
            transition: all 0.25s ease;
        }

        /* 이중인격 카드 애니메이션 */
        .dual-card { perspective: 1000px; position: relative; }
        .dual-card-inner { transition: transform 0.6s cubic-bezier(0.4, 0, 0.2, 1); transform-style: preserve-3d; position: relative; }
        .dual-card.swapped .dual-card-inner { transform: rotateY(180deg); }
        .personality-front, .personality-back { backface-visibility: hidden; width: 100%; }
        .personality-back { position: absolute; top: 0; left: 0; transform: rotateY(180deg); }

        .group-tag { font-size: 10px; padding: 2px 8px; border-radius: 99px; font-weight: 900; text-transform: uppercase; }
        .tag-admin { background: rgba(178, 161, 95, 0.15); color: var(--admin-gold); }
        .tag-scout { background: rgba(59, 130, 246, 0.15); color: var(--scout-blue); }
        .tag-tooth { background: rgba(239, 68, 68, 0.15); color: var(--tooth-crimson); }
        .tag-none { background: rgba(113, 113, 122, 0.15); color: var(--neutral-zinc); }

        .scroll-hide::-webkit-scrollbar { width: 4px; }
        .scroll-hide::-webkit-scrollbar-thumb { background: #27272a; border-radius: 10px; }
        main { flex: 1; overflow-y: auto; padding-bottom: 40px; }

        .mission-card { background: linear-gradient(135deg, #1a1a1a 0%, #0a0a0a 100%); border: 1px solid #333; }
        .notice-card { border-left: 4px solid var(--blood-red); background: #111; }
        
        .setting-section { margin-bottom: 24px; }
        .setting-section h3 { color: var(--blood-red); font-weight: 900; font-size: 1.1rem; margin-bottom: 8px; display: flex; align-items: center; gap: 8px; }
        .setting-section p { font-size: 0.85rem; line-height: 1.6; color: #a1a1aa; }
    </style>
</head>
<body>

<!-- 진입 화면 -->
<div id="entry-screen" class="fixed inset-0 z-50 flex flex-col items-center justify-center p-6 bg-black">
    <div class="mb-12 text-center">
        <h1 class="blood-text text-6xl mb-3 tracking-tighter">TRTD-4444</h1>
        <p class="text-zinc-600 tracking-[0.5em] text-xs uppercase">The Road To Death</p>
    </div>
    <div class="glass-panel p-8 w-full max-w-sm border border-zinc-800">
        <input id="user-nickname" type="text" placeholder="탑승자 코드명 입력" class="w-full bg-zinc-900 border border-zinc-800 p-4 rounded-lg mb-4 text-center text-white focus:outline-none focus:border-red-900 transition-colors">
        <div class="space-y-3">
            <button onclick="enterAs('USER')" class="w-full bg-red-800 text-white py-4 rounded-lg font-bold shadow-lg">열차 탑승</button>
            <button onclick="openMCPopup()" class="w-full bg-zinc-900 hover:bg-zinc-800 text-zinc-500 py-3 rounded-lg text-sm transition-colors">MC 인증 접속</button>
        </div>
    </div>
</div>

<!-- MC 인증 팝업 -->
<div id="mc-auth-popup" class="fixed inset-0 z-[60] hidden flex items-center justify-center bg-black/95 p-4">
    <div class="glass-panel p-6 w-full max-w-xs text-center border-red-900/30">
        <h3 class="text-lg font-bold mb-4 text-red-600 tracking-widest">ADMIN AUTH</h3>
        <input id="mc-pass" type="password" placeholder="PASSWORD" class="w-full bg-zinc-900 border border-zinc-800 p-3 rounded mb-4 text-center text-white">
        <div class="flex gap-2">
            <button onclick="closeMCPopup()" class="flex-1 bg-zinc-800 p-3 rounded text-sm text-zinc-400">닫기</button>
            <button onclick="enterAs('MC')" class="flex-1 bg-red-800 p-3 rounded text-sm font-bold text-white">인증</button>
        </div>
    </div>
</div>

<!-- 메인 앱 UI -->
<div id="app-ui" class="hidden flex flex-col h-screen">
    <header class="p-4 flex justify-between items-center bg-black border-b border-zinc-900">
        <div class="flex items-center gap-3">
            <span class="blood-text text-2xl tracking-tighter">TRTD</span>
            <span id="badge-mc" class="hidden bg-red-600 text-[10px] px-2 py-0.5 rounded font-black text-white">ADMIN</span>
        </div>
        <div class="flex items-center gap-4">
            <button onclick="toggleBgm()" id="bgm-toggle" class="text-zinc-600"><i class="fas fa-volume-mute text-lg"></i></button>
            <div class="flex flex-col items-end">
                <span id="my-name" class="font-bold text-red-500 text-sm"></span>
                <span class="text-[9px] text-zinc-600 tracking-widest">STATION-CONNECTED</span>
            </div>
        </div>
    </header>

    <nav class="flex overflow-x-auto scroll-hide bg-[#080808] border-b border-zinc-900">
        <div onclick="setTab('setting')" class="nav-btn active" data-tab="setting">설정</div>
        <div onclick="setTab('notice')" class="nav-btn" data-tab="notice">공지</div>
        <div onclick="setTab('chars')" class="nav-btn" data-tab="chars">인물</div>
        <div onclick="setTab('mission')" class="nav-btn" data-tab="mission">미션</div>
        <div onclick="setTab('chat')" class="nav-btn" data-tab="chat">채팅</div>
        <div id="mc-nav" onclick="setTab('admin')" class="nav-btn hidden text-red-600 font-bold" data-tab="admin">MC 권한</div>
    </nav>

    <main class="p-4 scroll-hide">
        <!-- 설정 탭 -->
        <section id="tab-setting" class="tab-content space-y-6">
            <div class="setting-section">
                <h3><i class="fas fa-skull"></i> 메인 설정</h3>
                <p>세상에 좀비가 창궐했다 이런 문장을 들으면 보통 아 혹시 좀비가 나오는 작품에 첫 문장인가요? 할거다 뭐 좀비물 보면 대충 대기업 연구소에서 실험 잘못 하다가 좀비 바이러스가 터지는 이야기 근데..그작품이 우리현실이고 그 주인공이 우리다 하...이 이야기는 하필 전세계랑 이어진 특수한 열차 trtd-4444 에 탑승한 승객인 여러분은 이제 열차를 타고다니며 생존해야됩니다</p>
            </div>
            <div class="setting-section">
                <h3><i class="fas fa-dna"></i> 면역자들</h3>
                <p>좀비에 면역되어(그치만 물리면 좀비화) 강한 신체능력과 특수한 초능력을 가진 자들이 생겨났다 그리고 이들을 초능력과 등급을 나눠 D부터 SS까지 존재한다</p>
            </div>
            <div class="setting-section">
                <h3><i class="fas fa-crosshairs"></i> 탐색대</h3>
                <p>면역자들을 모아 여러 물품및 식자제등을 모아오는 자들이자 열차의 무력이라 할수있으며 치안관리등 여러 일을 하며 인원이 많다 거주구역을 주로 맡는다</p>
            </div>
            <div class="setting-section">
                <h3><i class="fas fa-building"></i> 다이아 바이오</h3>
                <p>이 모든 일을 시작한 원인이자 생명 변이 프로젝트로 이 도시를 망친 주범이며 좀비와 면역자들을 탄생시킨것들도 이들이다</p>
            </div>
            <div class="setting-section">
                <h3><i class="fas fa-vial"></i> 생명 변이 프로젝트</h3>
                <p>정부와 다이아 바이오의 비밀 프로젝트 생명 변이및 장애 치료를 위한 실험이었으나 인간성을 삭제하고 식욕과 공격성만 남기는 변이가 발생해버려 세상은 아포칼립스가 되었다</p>
            </div>
            <div class="setting-section">
                <h3><i class="fas fa-biohazard"></i> 좀비</h3>
                <p>생명 변이 프로젝트로 변이된 자들중 면역자가 안되고 이성을 잃어버린 자들로 식욕과 공격성이 매우 강하며 개채도 많고 여러 형태가 존재하며 일반인은 이들 근처에 가도 감염이다 청각과 시각이 존재하니 조심하자</p>
            </div>
            <div class="setting-section">
                <h3><i class="fas fa-teeth"></i> 어금니</h3>
                <p>거주구역 31부터 50까지 거주구역을 점령한 조직이며 탐사대와 매번 부딪히는 이 열차 안에서 생긴 사회에 악의 축이라 할수있으며</p>
            </div>
        </section>

        <!-- 공지 탭 -->
        <section id="tab-notice" class="tab-content hidden space-y-3">
            <div id="notice-list" class="space-y-3">
                <!-- MC 공지사항 렌더링 -->
            </div>
        </section>

        <!-- 인물칸 탭 -->
        <section id="tab-chars" class="tab-content hidden grid grid-cols-1 gap-4 pb-10">
            <!-- JS 캐릭터 렌더링 -->
        </section>

        <!-- 미션 탭 -->
        <section id="tab-mission" class="tab-content hidden space-y-4">
            <div id="mission-list" class="space-y-4">
                <p class="text-center text-zinc-600 py-10 text-xs">현재 등록된 미션이 없습니다.</p>
            </div>
            <div id="event-display" class="hidden p-4 bg-orange-950/20 border border-orange-700/50 rounded-lg shadow-2xl animate-pulse">
                <div class="flex justify-between items-center mb-2">
                    <h3 class="text-orange-500 font-black text-sm"><i class="fas fa-bolt mr-2"></i>돌발 이벤트 발생!</h3>
                </div>
                <div id="event-content"></div>
            </div>
        </section>

        <!-- 채팅 탭 -->
        <section id="tab-chat" class="tab-content hidden flex flex-col h-full gap-2">
            <div id="chat-display" class="flex-1 glass-panel p-4 overflow-y-auto text-sm space-y-3 min-h-[420px] scroll-hide"></div>
            <div class="flex gap-2">
                <input id="chat-input" type="text" placeholder="메시지 입력..." class="flex-1 bg-zinc-900 border border-zinc-800 p-4 rounded-xl text-sm text-white focus:outline-none">
                <button onclick="sendMsg()" class="bg-red-800 px-6 rounded-xl text-white"><i class="fas fa-paper-plane"></i></button>
            </div>
        </section>

        <!-- MC 권한 칸 (MC 전용) -->
        <section id="tab-admin" class="tab-content hidden space-y-8 pb-10">
            <!-- 공지 작성 -->
            <div class="glass-panel p-5 border-red-900/30">
                <h3 class="font-bold text-red-500 text-xs mb-4 uppercase tracking-widest"><i class="fas fa-bullhorn mr-2"></i>공지 사항 게시</h3>
                <div class="space-y-3">
                    <input id="admin-notice-title" type="text" placeholder="공지 제목" class="w-full bg-zinc-950 border border-zinc-800 p-3 rounded text-sm text-white">
                    <textarea id="admin-notice-content" placeholder="공지 내용을 입력하세요." class="w-full bg-zinc-950 border border-zinc-800 p-3 rounded h-20 text-sm text-white"></textarea>
                    <button onclick="adminPostNotice()" class="w-full bg-red-900 p-3 rounded text-xs font-bold text-white uppercase">공지 배포</button>
                </div>
            </div>

            <!-- 미션 생성 -->
            <div class="glass-panel p-5 border-blue-900/30">
                <h3 class="font-bold text-blue-500 text-xs mb-4 uppercase tracking-widest"><i class="fas fa-tasks mr-2"></i>미션 생성 (선착순)</h3>
                <div class="space-y-3">
                    <input id="admin-mission-title" type="text" placeholder="미션 제목" class="w-full bg-zinc-950 border border-zinc-800 p-3 rounded text-sm text-white">
                    <textarea id="admin-mission-content" placeholder="미션 내용" class="w-full bg-zinc-950 border border-zinc-800 p-3 rounded h-20 text-sm text-white"></textarea>
                    <input id="admin-mission-reward" type="text" placeholder="클리어 보상" class="w-full bg-zinc-950 border border-zinc-800 p-3 rounded text-sm text-yellow-500">
                    <button onclick="adminCreateMission()" class="w-full bg-blue-900 p-3 rounded text-xs font-bold text-white uppercase">미션 등록</button>
                    <hr class="border-zinc-800 my-4">
                    <h4 class="text-[10px] text-zinc-500 font-bold mb-2 uppercase">미션 클리어 정산</h4>
                    <div class="flex gap-2">
                        <input id="clear-user-nick" type="text" placeholder="유저 닉네임" class="flex-1 bg-zinc-950 border border-zinc-800 p-3 rounded text-sm text-white">
                        <button onclick="adminClearMission()" class="bg-yellow-600 px-4 rounded text-xs font-bold text-black">지급</button>
                    </div>
                </div>
            </div>

            <!-- 돌발 이벤트 -->
            <div class="glass-panel p-5 border-orange-900/30">
                <h3 class="font-bold text-orange-500 text-xs mb-4 uppercase tracking-widest"><i class="fas fa-bolt mr-2"></i>돌발 이벤트 컨트롤</h3>
                <div class="space-y-3">
                    <input id="event-title" type="text" placeholder="이벤트 명칭" class="w-full bg-zinc-950 border border-zinc-800 p-3 rounded text-sm text-white">
                    <textarea id="event-desc" placeholder="이벤트 설명 및 발생 상황" class="w-full bg-zinc-950 border border-zinc-800 p-3 rounded h-20 text-sm text-white"></textarea>
                    <input id="event-players" type="text" placeholder="참가자 목록 (쉼표 구분)" class="w-full bg-zinc-950 border border-zinc-800 p-3 rounded text-sm text-white">
                    <div class="grid grid-cols-2 gap-2">
                        <input id="event-reward-1" type="text" placeholder="1등 보상" class="bg-zinc-950 border border-zinc-800 p-3 rounded text-sm text-yellow-500">
                        <input id="event-reward-all" type="text" placeholder="참가 보상" class="bg-zinc-950 border border-zinc-800 p-3 rounded text-sm text-zinc-400">
                    </div>
                    <button onclick="adminTriggerEvent()" class="w-full bg-orange-700 p-3 rounded text-xs font-bold text-white uppercase">이벤트 개시</button>
                    <hr class="border-zinc-800 my-4">
                    <h4 class="text-[10px] text-zinc-500 font-bold uppercase mb-2">기여도 정산 및 종료</h4>
                    <textarea id="event-result" placeholder="유저: 기여도 - 보상내용" class="w-full bg-zinc-950 border border-zinc-800 p-3 rounded h-24 text-sm text-white"></textarea>
                    <button onclick="adminFinalizeEvent()" class="w-full bg-zinc-800 p-3 rounded text-xs font-bold text-zinc-400">최종 결과 발표</button>
                </div>
            </div>
        </section>
    </main>
</div>

<audio id="audio-bgm" loop>
    <source src="https://www.soundhelix.com/examples/mp3/SoundHelix-Song-8.mp3" type="audio/mpeg">
</audio>

<script>
    const appState = { 
        role: 'USER', 
        name: '', 
        messages: [],
        notices: [],
        missions: [],
        currentEvent: null
    };

    const charData = [
        { group: "운영진", name: "김진형", gender: "남", age: 37, pow: "C", trait: "기관사", weapon: "설득 및 중재", item: "확성기", tendency: "선", desc: "이 열차의 총책임자로써 여러 권리 및 이 열차에 강한 권능을 가지고 있습니다. 하지만..그가 말하는 책임자 그저 평범한 시민들까지 챙길지는.." },
        { group: "탐사대", name: "유진성", gender: "남", age: 19, pow: "A(S~SS)", trait: "검술달인 (바람의 검)", weapon: "검술", item: "검", tendency: "선", desc: "과거 검도 선수로 이름을 날렸으나 누명을 쓰고 자격박탈당한 순간 세상이 멸망했다. 그의 검은 이제 좀비를 베며 면역자이다." },
        { group: "운영진", name: "이예현", gender: "남", age: 29, pow: "B", trait: "요리사", weapon: "없음", item: "요리 물품", tendency: "선", desc: "유명 셰프 출신 주방칸 총책임자. 식자제를 가져다줄 시 특별한 일이 생기거나 체력이 채워집니다." },
        { group: "탐사대", name: "마석형", gender: "남", age: 32, pow: "SS", trait: "예비 아빠 (신체강화)", weapon: "주먹", item: "라이터, 담배, 글러브", tendency: "선", desc: "전직 조폭 출신 예비 아빠. 세계관 최강 근력만으로 좀비를 죽이는게 가능한 탐색대이자 면역자이다." },
        { group: "무소속", name: "하루여", gender: "여", age: 22, pow: "D", trait: "인플러언서", weapon: "없음", item: "핸드폰, 화장품", tendency: "혼돈", desc: "전직 유명 아이돌이자 버튜버. 머리가 꽃밭이고 너무 착해서 가끔식 말썽을 부린다." },
        { group: "운영진", name: "이로미", gender: "여", age: 26, pow: "C", trait: "연구자 (제조)", weapon: "화학 약품 제조", item: "과학 물품", tendency: "선", desc: "다이아 바이오 전 연구자. 열차의 브레인으로 화학 약품을 가져다줄 시 아이템 제조 가능." },
        { group: "운영진", name: "아연우", gender: "여", age: 24, pow: "D", trait: "의사 (새크리파이스)", weapon: "주사기(투척)", item: "의료약품", tendency: "선", desc: "엘리트 의사. 부상당한 열차사람들을 치료하는 중요한 역할을 맡은 면역자이다." },
        { group: "어금니", name: "창준섭", gender: "남", age: 25, pow: "S(SS)", trait: "사신 (흑안개)", weapon: "나이프 파이팅", item: "나이프, 기름통", tendency: "악", desc: "사람을 죽이는데 희열을 느끼는 싸이코패스. 어금니 5간부 서열 4위 '사신'." },
        { group: "탐사대", name: "김민우", gender: "남", age: 29, pow: "SS", trait: "특수부대 (ERROR)", weapon: "생존무술/총술", item: "M16", tendency: "중립", desc: "특수부대 출신 탐색대 대장. 강한 전투력을 가진 면역자이다." },
        { group: "탐사대", name: "이한준", gender: "남", age: 17, pow: "S", trait: "불사조", weapon: "주먹과 불", item: "라이터", tendency: "선", desc: "방화범 출신 분위기 메이커. 탐색대의 선봉이자 면역자이다." },
        { group: "어금니", name: "우현진", gender: "남", age: 34, pow: "SS", trait: "비스트 (중력 제어)", weapon: "장검술", item: "비밀약품", tendency: "악", desc: "어금니의 보스. 생존을 위해 악이 된 자. 치밀하고 압도적인 무력을 가졌다." },
        { group: "어금니", name: "호반연", gender: "남", age: 24, pow: "S", trait: "로키 (환각)", weapon: "선동", item: "강철실", tendency: "악,혼돈", desc: "실눈의 수상한 남자. 어금니 서열 3위 로키." },
        { group: "어금니", name: "광예선", gender: "여", age: 19, pow: "S", trait: "광녀 (혈계강화)", weapon: "대낫", item: "대낫", tendency: "악", desc: "전투를 미치도록 좋아하는 전투광. 어금니 서열 5위." },
        { group: "어금니", name: "금강혁", gender: "남", age: 22, pow: "SS", trait: "보이드 (공허)", weapon: "주먹", item: "안경", tendency: "혼돈", desc: "도파민 노예 낭만주의자. 어금니 서열 2위." },
        { group: "무소속", name: "마담 카탈리아", gender: "여", age: 35, pow: "D", trait: "상점", weapon: "언론전", item: "부채", tendency: "이익", desc: "열차 내 경제 시스템을 만든 상점가 주인. 어떤 물건이든 유통해올 수 있다." },
        { group: "탐사대", name: "이하윤", gender: "여", age: 17, pow: "B", trait: "예지안", weapon: "격투술", item: "없음", tendency: "혼돈", desc: "오만한 천재 메스가키. 5초 이내의 미래를 시각화한다. 열차 수장이 목표." },
        { group: "무소속", name: "정선진", gender: "남", age: "??", pow: "A", trait: "심판", weapon: "대검술", item: "치우쳐진 천칭", tendency: "선", desc: "질서와 정의에 집착하는 전 판사. 치우쳐진 천칭으로 심판을 내린다." },
        { 
            isDual: true, 
            group: "무소속", 
            p1: { name: "이현 (천사)", age: 17, pow: "S", trait: "천사 소환", weapon: "소환술", item: "사진", tendency: "선/기피", desc: "과거의 상흔으로 인격이 나뉜 소년. 대인기피증이 있으며 마음이 무너져 있다." },
            p2: { name: "이현 (악마)", age: 17, pow: "S", trait: "악마 소환", weapon: "소환술", item: "사진", tendency: "악/소외", desc: "소외감 속에서 착한 마음이 악해진 인격. 서로를 미워하고 증오한다." }
        }
    ];

    function enterAs(role) {
        const nick = document.getElementById('user-nickname').value.trim();
        if(!nick) return alert('코드명을 입력하세요.');
        if(role === 'MC' && document.getElementById('mc-pass').value !== 'zutefcdwt435!') return alert('인증 실패.');
        
        appState.role = role;
        appState.name = nick;
        if(role === 'MC') {
            document.getElementById('badge-mc').classList.remove('hidden');
            document.getElementById('mc-nav').classList.remove('hidden');
        }
        document.getElementById('my-name').innerText = nick;
        document.getElementById('entry-screen').classList.add('hidden');
        document.getElementById('app-ui').classList.remove('hidden');
        renderChars();
        sysLog(`${nick} 탑승자가 로그인하였습니다.`);
    }

    function setTab(id) {
        document.querySelectorAll('.tab-content').forEach(c => c.classList.add('hidden'));
        document.getElementById(`tab-${id}`).classList.remove('hidden');
        document.querySelectorAll('.nav-btn').forEach(b => {
            b.classList.remove('active');
            if(b.dataset.tab === id) b.classList.add('active');
        });
    }

    /* MC 관리 기능 */
    function adminPostNotice() {
        const title = document.getElementById('admin-notice-title').value;
        const content = document.getElementById('admin-notice-content').value;
        if(!title || !content) return alert('내용을 입력하세요.');
        const n = { title, content, time: new Date().toLocaleTimeString() };
        appState.notices.unshift(n);
        renderNotices();
        sysLog(`[공지] ${title}`);
        alert('공지가 게시되었습니다.');
    }

    function renderNotices() {
        const wrap = document.getElementById('notice-list');
        wrap.innerHTML = appState.notices.map(n => `
            <div class="notice-card p-4 rounded shadow-lg border border-red-900/10">
                <div class="flex justify-between items-center mb-1">
                    <h4 class="text-sm font-bold text-white">${n.title}</h4>
                    <span class="text-[9px] text-zinc-600">${n.time}</span>
                </div>
                <p class="text-xs text-zinc-400 leading-relaxed">${n.content}</p>
            </div>
        `).join('');
    }

    function adminCreateMission() {
        const title = document.getElementById('admin-mission-title').value;
        const content = document.getElementById('admin-mission-content').value;
        const reward = document.getElementById('admin-mission-reward').value;
        if(!title || !content) return alert('정보를 모두 입력하세요.');
        const m = { id: Date.now(), title, content, reward, taker: null };
        appState.missions.unshift(m);
        renderMissions();
        sysLog(`[신규 미션] ${title}`);
        alert('미션이 배포되었습니다.');
    }

    function renderMissions() {
        const wrap = document.getElementById('mission-list');
        if(appState.missions.length === 0) { wrap.innerHTML = '<p class="text-center text-zinc-600 py-10 text-xs">진행 가능한 미션이 없습니다.</p>'; return; }
        wrap.innerHTML = appState.missions.map(m => `
            <div class="mission-card p-5 rounded-xl border border-zinc-800">
                <div class="flex justify-between items-start mb-3">
                    <h4 class="text-blue-400 font-black text-sm">${m.title}</h4>
                    <span class="text-[10px] ${m.taker ? 'text-red-500' : 'text-green-500'} font-bold">${m.taker ? '진행중' : '수락가능'}</span>
                </div>
                <p class="text-xs text-zinc-400 mb-4">${m.content}</p>
                <div class="flex justify-between items-center">
                    <span class="text-[10px] text-yellow-500 font-bold"><i class="fas fa-gift mr-1"></i>${m.reward}</span>
                    ${m.taker ? `<span class="text-[10px] text-zinc-600 font-bold">${m.taker}</span>` : `<button onclick="takeMission(${m.id})" class="bg-blue-900 px-4 py-2 rounded text-[10px] font-bold text-white">수락하기</button>`}
                </div>
            </div>
        `).join('');
    }

    function takeMission(id) {
        const m = appState.missions.find(x => x.id === id);
        if(m.taker) return;
        m.taker = appState.name;
        renderMissions();
        sysLog(`${appState.name}님이 [${m.title}] 미션을 수락했습니다.`);
        alert('미션을 수락했습니다!');
    }

    function adminClearMission() {
        const nick = document.getElementById('clear-user-nick').value;
        if(!nick) return alert('닉네임을 입력하세요.');
        sysLog(`MISSION CLEAR: ${nick}님이 미션을 완수했습니다. 보상이 지급됩니다!`);
        alert('클리어 메시지 발송 완료.');
    }

    function adminTriggerEvent() {
        const title = document.getElementById('event-title').value;
        const desc = document.getElementById('event-desc').value;
        const players = document.getElementById('event-players').value;
        const r1 = document.getElementById('event-reward-1').value;
        const ra = document.getElementById('event-reward-all').value;
        appState.currentEvent = { title, desc, players };
        document.getElementById('event-display').classList.remove('hidden');
        document.getElementById('event-content').innerHTML = `
            <h4 class="text-white font-bold text-xs mb-1">${title}</h4>
            <p class="text-[11px] text-zinc-400 mb-2">${desc}</p>
            <div class="text-[9px] text-zinc-500 font-bold">참가: ${players}</div>
            <div class="mt-2 text-[9px] text-yellow-500">1등 보상: ${r1} / 전원 보상: ${ra}</div>
        `;
        sysLog(`!! 돌발 이벤트 발생: ${title} !!`);
        alert('이벤트가 개시되었습니다.');
    }

    function adminFinalizeEvent() {
        const result = document.getElementById('event-result').value;
        if(!result) return alert('정산 내용을 입력하세요.');
        sysLog(`EVENT FINISHED: ${appState.currentEvent.title} 정산 결과\n${result}`);
        document.getElementById('event-display').classList.add('hidden');
        alert('이벤트 정산 완료.');
    }

    /* 캐릭터 렌더링 */
    function renderChars() {
        const wrap = document.getElementById('tab-chars');
        wrap.innerHTML = charData.map(c => {
            if (c.isDual) {
                return `
                <div class="dual-card w-full" id="dual-card-lee">
                    <div class="dual-card-inner">
                        <div class="char-card p-4 personality-front bg-[#0a0a15] border-blue-900/30">
                            <div class="flex justify-between items-start mb-2">
                                <div><span class="group-tag tag-none">무소속</span><h4 class="text-lg font-black text-blue-400 mt-1">${c.p1.name}</h4></div>
                                <button onclick="togglePersonality()" class="text-[8px] bg-zinc-800 p-1 px-2 rounded">인격 전환 <i class="fas fa-sync"></i></button>
                            </div>
                            <div class="text-[10px] text-zinc-500 mb-1">성향: ${c.p1.tendency} | 무기: ${c.p1.weapon}</div>
                            <p class="text-xs text-zinc-400 mb-3">${c.p1.desc}</p>
                            <div class="flex gap-2"><span class="text-[9px] bg-zinc-900 text-blue-400 p-1 px-2 rounded font-bold">능력: ${c.p1.trait}</span></div>
                        </div>
                        <div class="char-card p-4 personality-back bg-[#150a0a] border-red-900/30">
                            <div class="flex justify-between items-start mb-2">
                                <div><span class="group-tag tag-none">무소속</span><h4 class="text-lg font-black text-red-500 mt-1">${c.p2.name}</h4></div>
                                <button onclick="togglePersonality()" class="text-[8px] bg-zinc-800 p-1 px-2 rounded">인격 전환 <i class="fas fa-sync"></i></button>
                            </div>
                            <div class="text-[10px] text-zinc-500 mb-1">성향: ${c.p2.tendency} | 무기: ${c.p2.weapon}</div>
                            <p class="text-xs text-zinc-400 mb-3">${c.p2.desc}</p>
                            <div class="flex gap-2"><span class="text-[9px] bg-zinc-900 text-red-500 p-1 px-2 rounded font-bold">능력: ${c.p2.trait}</span></div>
                        </div>
                    </div>
                </div>`;
            }
            const tagClass = c.group === '운영진' ? 'tag-admin' : c.group === '탐사대' ? 'tag-scout' : c.group === '어금니' ? 'tag-tooth' : 'tag-none';
            return `
                <div class="char-card p-4">
                    <div class="flex justify-between items-start mb-2">
                        <div><span class="group-tag ${tagClass}">${c.group}</span><h4 class="text-lg font-black text-white mt-1">${c.name}</h4></div>
                        <span class="text-[10px] font-black text-red-600 bg-red-950/20 px-2 py-0.5 rounded">POW: ${c.pow}</span>
                    </div>
                    <div class="text-[10px] text-zinc-500 mb-1">성향: ${c.tendency} | 무기: ${c.weapon} | 나이: ${c.age}</div>
                    <p class="text-xs text-zinc-400 mb-3">${c.desc}</p>
                    <div class="flex flex-wrap gap-2">
                        <span class="text-[9px] bg-zinc-900 text-red-400 p-1 px-2 rounded font-bold">능력: ${c.trait}</span>
                        <span class="text-[9px] bg-zinc-900 text-zinc-500 p-1 px-2 rounded">소지품: ${c.item}</span>
                    </div>
                </div>
            `;
        }).join('');
    }

    function togglePersonality() { document.getElementById('dual-card-lee').classList.toggle('swapped'); }

    function sendMsg() {
        const input = document.getElementById('chat-input');
        if(!input.value.trim()) return;
        appState.messages.push({ sender: appState.name, role: appState.role, text: input.value, time: new Date().toLocaleTimeString([], { hour: '2-digit', minute: '2-digit' }) });
        renderChat(); input.value = '';
    }

    function sysLog(txt) { appState.messages.push({ role: 'SYSTEM', text: txt }); renderChat(); }

    function renderChat() {
        const box = document.getElementById('chat-display');
        box.innerHTML = appState.messages.map(m => {
            if(m.role === 'SYSTEM') return `<div class="text-center text-zinc-700 text-[10px] py-2 uppercase tracking-widest">[ ${m.text} ]</div>`;
            return `
                <div class="mb-3 animate-fade-in">
                    <div class="flex items-center gap-2 mb-1">
                        <span class="font-black text-[11px] ${m.role === 'MC' ? 'text-red-500' : 'text-zinc-400'}">${m.sender}</span>
                        <span class="text-[9px] text-zinc-700">${m.time}</span>
                    </div>
                    <div class="bg-[#111] border ${m.role === 'MC' ? 'border-red-900/50' : 'border-zinc-800'} p-3 rounded-lg text-zinc-400 text-xs">${m.text}</div>
                </div>
            `;
        }).join('');
        box.scrollTop = box.scrollHeight;
    }

    function toggleBgm() {
        const a = document.getElementById('audio-bgm');
        const btn = document.getElementById('bgm-toggle');
        if(a.paused) { a.play(); btn.innerHTML = '<i class="fas fa-volume-up"></i>'; btn.classList.add('text-red-500'); }
        else { a.pause(); btn.innerHTML = '<i class="fas fa-volume-mute"></i>'; btn.classList.remove('text-red-500'); }
    }

    function openMCPopup() { document.getElementById('mc-auth-popup').classList.remove('hidden'); }
    function closeMCPopup() { document.getElementById('mc-auth-popup').classList.add('hidden'); }
    document.getElementById('chat-input').addEventListener('keypress', (e) => { if(e.key === 'Enter') sendMsg(); });
</script>

</body>
</html>

