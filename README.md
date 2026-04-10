<!DOCTYPE html>
<html lang="ko">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>오늘의 사표: 퇴사 시뮬레이터 🏢</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <link href="https://fonts.googleapis.com/css2?family=Jua&display=swap" rel="stylesheet">
    <style>
        body {
            font-family: 'Jua', sans-serif;
            background-color: #f0fdf4;
            touch-action: manipulation;
        }
        .paper-fly {
            position: absolute;
            animation: fly 0.6s ease-out forwards;
            pointer-events: none;
            z-index: 50;
        }
        @keyframes fly {
            0% { transform: translate(-50%, 0) rotate(0deg); opacity: 1; bottom: 100px; }
            100% { transform: translate(-50%, -40vh) rotate(720deg); opacity: 0; bottom: 100px; }
        }
        .boss-shake {
            animation: boss-hit 0.2s ease-in-out;
        }
        @keyframes boss-hit {
            0% { transform: scale(1); }
            50% { transform: scale(0.9) rotate(5deg); }
            100% { transform: scale(1); }
        }
        .bg-gradient-soft {
            background: linear-gradient(135deg, #dcfce7 0%, #f0fdf4 100%);
        }
    </style>
</head>
<body class="flex flex-col items-center justify-between min-h-screen p-6 overflow-hidden bg-gradient-soft">

    <!-- Header Area -->
    <div class="text-center mt-4">
        <h1 class="text-3xl text-green-600 mb-1">오늘의 사표 던지기 📄</h1>
        <p class="text-gray-500 text-sm">부장님, 저 이제 자유예요! (상상만 함)</p>
    </div>

    <!-- Boss Area -->
    <div id="boss-container" class="relative flex flex-col items-center justify-center transition-all duration-300">
        <div id="boss-emoji" class="text-9xl mb-4 transition-transform">👨‍💼</div>
        <div id="boss-bubble" class="bg-white p-3 rounded-2xl shadow-md border-2 border-green-200 relative hidden">
            <p id="boss-text" class="text-sm text-gray-700"></p>
            <div class="absolute -bottom-2 left-1/2 -translate-x-1/2 w-4 h-4 bg-white border-b-2 border-r-2 border-green-200 rotate-45"></div>
        </div>
        <div id="hp-bar-container" class="w-48 h-4 bg-gray-200 rounded-full mt-6 border-2 border-white shadow-inner overflow-hidden">
            <div id="hp-bar" class="w-full h-full bg-green-500 transition-all duration-200"></div>
        </div>
        <p class="text-xs text-gray-400 mt-2 font-sans">퇴사까지 남은 멘탈: <span id="hp-text">100</span>%</p>
    </div>

    <!-- Message Display -->
    <div id="msg-box" class="h-12 text-center text-lg text-green-700 font-bold px-4"></div>

    <!-- Controls -->
    <div class="w-full max-w-xs mb-8">
        <button id="fire-btn" class="w-full bg-green-500 hover:bg-green-600 text-white text-2xl py-5 rounded-3xl shadow-xl transform active:scale-90 transition-all border-b-8 border-green-700">
            사표 투척하기! 📄
        </button>
    </div>

    <!-- Result Modal -->
    <div id="win-modal" class="fixed inset-0 bg-black/70 flex items-center justify-center hidden z-50 p-6">
        <div class="bg-white rounded-3xl p-8 w-full max-w-xs text-center border-4 border-yellow-400">
            <div class="text-7xl mb-4">🕊️</div>
            <h2 class="text-2xl font-bold mb-2 text-gray-800">퇴사 성공!</h2>
            <p class="text-gray-600 mb-6 leading-relaxed">축하합니다!<br>이제 당신은 자유로운 집요정이에요!<br>💰 퇴직금이 입금되었습니다.</p>
            <button onclick="location.reload()" class="w-full bg-yellow-400 text-white py-4 rounded-2xl font-bold text-xl shadow-lg border-b-4 border-yellow-600">한 번 더 던지기</button>
        </div>
    </div>

    <script>
        const fireBtn = document.getElementById('fire-btn');
        const bossEmoji = document.getElementById('boss-emoji');
        const bossBubble = document.getElementById('boss-bubble');
        const bossText = document.getElementById('boss-text');
        const hpBar = document.getElementById('hp-bar');
        const hpText = document.getElementById('hp-text');
        const msgBox = document.getElementById('msg-box');
        const winModal = document.getElementById('win-modal');

        let hp = 100;
        const hitDamage = 5;

        const bossReactions = [
            "뭐.. 뭐야?!",
            "지금 뭐 하는 건가?",
            "이봐, 다시 생각하게!",
            "자네 미쳤나?!",
            "인사고과에 반영하겠네!",
            "내 밑으로 다 집합시켜!",
            "자네 없으면 우리 팀 망해!"
        ];

        const myMessages = [
            "저 퇴사합니다! 안녕히 계세요! 👋",
            "부장님, 직접 타이핑한 사표입니다! ✨",
            "가슴 속에 품고 있던 옥석 같은 사표! 💎",
            "이제 월요일 아침이 두렵지 않아요! ☀️",
            "내일부터 12시까지 잘 겁니다! 😴",
            "자유를 찾아서 떠납니다! 🌊"
        ];

        fireBtn.addEventListener('click', () => {
            if (hp <= 0) return;

            // Damage and Update UI
            hp = Math.max(0, hp - hitDamage);
            hpBar.style.width = hp + '%';
            hpText.innerText = hp;

            // Visual Effects
            createPaperEffect();
            bossEmoji.classList.add('boss-shake');
            setTimeout(() => bossEmoji.classList.remove('boss-shake'), 200);

            // Change Boss Face
            if (hp <= 0) {
                bossEmoji.innerText = "😵‍💫";
                showWin();
            } else if (hp < 30) {
                bossEmoji.innerText = "😱";
            } else if (hp < 60) {
                bossEmoji.innerText = "💢";
            } else if (hp < 90) {
                bossEmoji.innerText = "😳";
            }

            // Show Random Messages
            if (Math.random() > 0.6) {
                bossBubble.classList.remove('hidden');
                bossText.innerText = bossReactions[Math.floor(Math.random() * bossReactions.length)];
                setTimeout(() => bossBubble.classList.add('hidden'), 1500);
            }

            msgBox.innerText = myMessages[Math.floor(Math.random() * myMessages.length)];
            
            // HP Bar Color change
            if (hp < 30) hpBar.classList.replace('bg-green-500', 'bg-red-500');
            else if (hp < 60) hpBar.classList.replace('bg-green-500', 'bg-yellow-500');
        });

        function createPaperEffect() {
            const paper = document.createElement('div');
            paper.innerText = '📄';
            paper.className = 'paper-fly text-3xl';
            paper.style.left = '50%';
            document.body.appendChild(paper);
            setTimeout(() => paper.remove(), 600);
        }

        function showWin() {
            fireBtn.disabled = true;
            fireBtn.classList.add('opacity-50');
            
            // Celebration Effect
            const duration = 2000;
            const end = Date.now() + duration;

            const interval = setInterval(() => {
                if (Date.now() > end) return clearInterval(interval);
                
                const emoji = document.createElement('div');
                emoji.innerText = ['💸', '✨', '🎈', '🎉'][Math.floor(Math.random() * 4)];
                emoji.style.position = 'fixed';
                emoji.style.left = Math.random() * 100 + 'vw';
                emoji.style.top = '-50px';
                emoji.style.fontSize = '2rem';
                emoji.style.zIndex = '100';
                emoji.animate([
                    { transform: 'translateY(0) rotate(0deg)', opacity: 1 },
                    { transform: `translateY(110vh) rotate(${Math.random() * 360}deg)`, opacity: 0 }
                ], { duration: 2000, easing: 'linear' });
                
                document.body.appendChild(emoji);
                setTimeout(() => emoji.remove(), 2000);
            }, 100);

            setTimeout(() => {
                winModal.classList.remove('hidden');
            }, 1500);
        }
    </script>
</body>
</html>
