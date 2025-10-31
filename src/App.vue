<template>
  <main class="scene">
    <!-- 하늘 배경, 별 -->
    <div class="sky">
      <span
          v-for="s in stars"
          :key="s.id"
          class="star"
          :style="{ left: s.x + '%', top: s.y + '%', animationDelay: s.delay + 's' }"
      />
      <!-- 구름 -->
      <div class="cloud cloud-1" />
      <div class="cloud cloud-2" />
      <div class="cloud cloud-3" />
    </div>

    <!-- 달 & 토끼 그림자 -->
    <div class="moon-wrap">
      <div class="moon">
        <div class="crater c1"></div>
        <div class="crater c2"></div>
        <div class="crater c3"></div>
      </div>
      <svg class="rabbit" viewBox="0 0 120 120" aria-hidden="true">
        <g fill="rgba(0,0,0,0.15)">
          <ellipse cx="50" cy="85" rx="25" ry="15"/>
          <circle cx="55" cy="60" r="18"/>
          <rect x="48" y="28" width="8" height="22" rx="4"/>
          <rect x="60" y="30" width="8" height="20" rx="4" transform="rotate(10 64 40)"/>
        </g>
      </svg>
    </div>

    <!-- 등롱 (왼/오른쪽 흔들림) -->
    <div class="lantern left">
      <div class="string"></div>
      <div class="body">
        <div class="lines"></div>
        <div class="knot"></div>
        <div class="tassel"></div>
      </div>
    </div>
    <div class="lantern right">
      <div class="string"></div>
      <div class="body">
        <div class="lines"></div>
        <div class="knot"></div>
        <div class="tassel"></div>
      </div>
    </div>

    <!-- 산 -->
    <div class="mountains">
      <div class="m m1"></div>
      <div class="m m2"></div>
      <div class="m m3"></div>
    </div>

    <!-- 헤더/카피 -->
    <section class="hero">
      <h1>풍성한 한가위 되세요</h1>
      <p class="subtitle">보름달처럼 마음도 가득 찬 추석 보내세요 · Happy Chuseok!</p>

      <div class="actions">
        <button class="btn primary" @click="openPouch">복주머니 열기</button>
        <button class="btn ghost" @click="share">공유하기</button>
      </div>

      <!-- 송편들 -->
      <div class="songpyeon">
        <span class="rice rice1" />
        <span class="rice rice2" />
        <span class="rice rice3" />
        <span class="rice rice4" />
      </div>
    </section>

    <!-- 낙엽 -->
    <div
        v-for="leaf in leaves"
        :key="leaf.id"
        class="leaf"
        :style="{
        left: leaf.x + '%',
        animationDuration: leaf.dur + 's',
        animationDelay: leaf.delay + 's',
        transform: 'scale(' + leaf.scale + ')'
      }"
    />

    <!-- 복주머니 모달 -->
    <dialog ref="dialogRef" class="pouch" @click.self="closePouch">
      <div class="pouch-card">
        <div class="pouch-head">
          <span class="knot"></span>
          <h2>올해의 한가위 덕담</h2>
        </div>
        <p class="wish">{{ wish }}</p>
        <div class="pouch-actions">
          <button class="btn primary" @click="reroll">다시 뽑기</button>
          <button class="btn" @click="closePouch">닫기</button>
        </div>
      </div>
    </dialog>

    <!-- 푸터 -->
    <footer class="footer">
      <small>&copy; {{ year }} Chuseok · Vue + Vite + TS</small>
    </footer>
  </main>
</template>

<script setup lang="ts">
import { onMounted, ref } from 'vue'

type Star = { id: number; x: number; y: number; delay: number }
type Leaf = { id: number; x: number; dur: number; delay: number; scale: number }

const year = new Date().getFullYear()

const wishes = [
  '보름달처럼 풍성한 행복이 가득하길!',
  '가족과 따뜻한 시간 보내세요 🌕',
  '건강과 평안이 늘 함께하길 기원합니다.',
  '마음 먹은 일들, 환하게 이루어지길 ✨',
  '웃음 가득한 한가위 보내세요 😊',
  '새 달처럼 새 기운, 복이 가득하길!',
]

const wish = ref(wishes[Math.floor(Math.random() * wishes.length)])

const stars = ref<Star[]>([])
const leaves = ref<Leaf[]>([])
const dialogRef = ref<HTMLDialogElement | null>(null)

const makeStars = (count = 80) => {
  stars.value = Array.from({ length: count }, (_, i) => ({
    id: i,
    x: Math.random() * 100,
    y: Math.random() * 60, // 위쪽 60% 영역만
    delay: Math.random() * 3,
  }))
}

const makeLeaves = (count = 14) => {
  leaves.value = Array.from({ length: count }, (_, i) => ({
    id: i,
    x: Math.random() * 100,
    dur: 8 + Math.random() * 6,
    delay: Math.random() * 5,
    scale: 0.6 + Math.random() * 0.8,
  }))
}

const openPouch = () => {
  if (!dialogRef.value?.open) dialogRef.value?.showModal()
}
const closePouch = () => dialogRef.value?.close()
const reroll = () => (wish.value = wishes[Math.floor(Math.random() * wishes.length)])

const share = async () => {
  const text = '풍성한 한가위 되세요 🎑 Vue로 만든 추석 웹 카드'
  try {
    if (navigator.share) {
      await navigator.share({ title: 'Chuseok Greeting', text })
    } else {
      await navigator.clipboard.writeText(text)
      alert('공유를 지원하지 않는 브라우저예요. 문구를 클립보드에 복사했어요!')
    }
  } catch {
    // 사용자가 취소한 경우 등
  }
}

onMounted(() => {
  makeStars()
  makeLeaves()
})
</script>

<style scoped>
:root {
  --bg-top: #0b1533;
  --bg-bottom: #0f1d44;
  --accent: #ffd166;
  --moon: #ffe9a8;
  --lantern: #f6a6a6;
  --lantern-deep: #e66d6d;
  --ink: #d1d9ff;
  --ink-dim: #9aa4d1;
  --btn: #3a59ff;
  --btn-ghost: rgba(255,255,255,0.12);
}

* { box-sizing: border-box; }

.scene {
  min-height: 100svh;
  background: linear-gradient(180deg, var(--bg-top), var(--bg-bottom));
  color: var(--ink);
  position: relative;
  overflow: hidden;
  font-family: ui-sans-serif, system-ui, -apple-system, Segoe UI, Roboto, "Noto Sans KR", "Apple SD Gothic Neo", "Malgun Gothic", Arial, "Helvetica Neue", sans-serif;
}

/* 하늘/별 */
.sky { position: absolute; inset: 0; overflow: hidden; }
.star {
  position: absolute; width: 2px; height: 2px;
  background: white; border-radius: 50%;
  opacity: 0.8; animation: twinkle 2.5s infinite ease-in-out;
}
@keyframes twinkle { 50% { opacity: 0.2; transform: scale(0.6); } }

/* 구름 */
.cloud {
  position: absolute; top: 10%; width: 220px; height: 70px;
  background: rgba(255,255,255,0.07);
  filter: blur(1px); border-radius: 60px;
  animation: drift 60s linear infinite;
}
.cloud::before, .cloud::after {
  content: ""; position: absolute; background: inherit;
  width: 140px; height: 50px; border-radius: 60px;
  top: -20px;
}
.cloud::before { left: 30px; }
.cloud::after  { left: 100px; top: -10px; width: 110px; height: 40px; }
.cloud-1 { left: -20%; animation-duration: 65s; }
.cloud-2 { top: 18%; left: -30%; animation-duration: 80s; }
.cloud-3 { top: 25%; left: -25%; animation-duration: 95s; }
@keyframes drift { to { transform: translateX(140vw); } }

/* 달 & 토끼 */
.moon-wrap { position: absolute; right: 8vw; top: 10vh; }
.moon {
  width: 180px; height: 180px; border-radius: 50%;
  background: radial-gradient(60% 60% at 35% 35%, var(--moon), #ffd873 70%, #f3c55a 100%);
  box-shadow: 0 0 40px rgba(255, 216, 115, 0.6);
  position: relative; animation: float 6s ease-in-out infinite;
}
@keyframes float { 50% { transform: translateY(-6px); } }
.crater { position: absolute; background: rgba(0,0,0,0.06); border-radius: 50%; }
.c1 { width: 24px; height: 24px; left: 30%; top: 28%; }
.c2 { width: 14px; height: 14px; right: 20%; top: 36%; }
.c3 { width: 18px; height: 18px; left: 45%; bottom: 22%; }
.rabbit { position: absolute; width: 180px; height: 180px; left: 0; top: 0; }

/* 등롱 */
.lantern { position: absolute; top: 5vh; width: 80px; transform-origin: top center; }
.lantern.left  { left: 4vw;  animation: swing 4s ease-in-out infinite; }
.lantern.right { right: 4vw; animation: swing 4.6s ease-in-out infinite reverse; }
@keyframes swing { 50% { transform: rotate(3deg); } }
.lantern .string { width: 2px; height: 80px; background: rgba(255,255,255,0.3); margin: 0 auto; }
.lantern .body {
  width: 80px; height: 92px; margin: 0 auto; border-radius: 50% 50% 40% 40%;
  background: radial-gradient(80% 90% at 50% 20%, var(--lantern), var(--lantern-deep));
  position: relative; box-shadow: 0 8px 16px rgba(0,0,0,0.2), 0 0 24px rgba(255, 132, 132, 0.35);
}
.lantern .lines {
  position: absolute; inset: 0; background:
    repeating-linear-gradient( to bottom,
    rgba(255,255,255,0.15) 0 6px, rgba(0,0,0,0) 6px 12px );
  border-radius: inherit;
}
.lantern .knot {
  position: absolute; bottom: -8px; left: 50%; transform: translateX(-50%);
  width: 22px; height: 8px; background: #b23b3b; border-radius: 4px;
}
.lantern .tassel {
  position: absolute; bottom: -42px; left: 50%; transform: translateX(-50%);
  width: 2px; height: 36px; background: #e35252;
  box-shadow: -4px 0 0 #d95353, 4px 0 0 #d95353;
}

/* 산 */
.mountains { position: absolute; bottom: 0; left: 0; right: 0; height: 36vh; pointer-events: none; }
.m {
  position: absolute; bottom: 0; width: 50vw; height: 38vh; background: #0a1230;
  clip-path: polygon(0% 100%, 0% 60%, 15% 35%, 35% 55%, 55% 28%, 75% 50%, 100% 30%, 100% 100%);
  filter: drop-shadow(0 -8px 40px rgba(0,0,0,0.3));
}
.m1 { left: -10vw; opacity: 0.35; }
.m2 { left: 12vw;  opacity: 0.5;  background: #09102a; }
.m3 { left: 38vw;  opacity: 0.65; background: #070e24; }

/* 히어로 텍스트 */
.hero {
  position: relative; z-index: 2;
  display: grid; place-items: center;
  text-align: center; padding: 14vh 2rem 18vh;
}
h1 {
  margin: 0; font-size: clamp(2rem, 5vw, 3.5rem);
  letter-spacing: -0.02em; line-height: 1.1;
  text-shadow: 0 2px 10px rgba(0,0,0,0.35);
}
.subtitle {
  margin: 12px 0 22px; color: var(--ink-dim);
  font-size: clamp(0.95rem, 1.6vw, 1.1rem);
}

/* 버튼 */
.actions { display: flex; gap: 10px; flex-wrap: wrap; justify-content: center; }
.btn {
  border: 1px solid transparent; background: var(--btn-ghost); color: var(--ink);
  padding: 10px 16px; border-radius: 999px; cursor: pointer;
  backdrop-filter: blur(4px);
  transition: transform .15s ease, background .2s ease, border-color .2s ease, box-shadow .2s ease;
}
.btn:hover { transform: translateY(-1px); border-color: rgba(255,255,255,0.24); }
.btn.primary {
  background: linear-gradient(135deg, #5670ff, #7a9eff);
  border: none; box-shadow: 0 8px 20px rgba(90, 117, 255, 0.35);
}
.btn.ghost { background: var(--btn-ghost); }

/* 송편 */
.songpyeon { margin-top: 26px; display: flex; gap: 10px; justify-content: center; }
.rice {
  width: 28px; height: 22px; display: inline-block;
  background: #a0e8af; border-radius: 50% 50% 45% 45% / 60% 60% 40% 40%;
  position: relative; box-shadow: inset 0 -3px 0 rgba(0,0,0,0.12);
  animation: bob 3s ease-in-out infinite;
}
.rice::after { content: ""; position: absolute; width: 16px; height: 10px; left: 6px; top: 2px; border-radius: 50%; background: rgba(255,255,255,0.25); }
.rice1 { background:#a0e8af; animation-delay: .0s; }
.rice2 { background:#ffd3a4; animation-delay: .2s; }
.rice3 { background:#f6a6a6; animation-delay: .4s; }
.rice4 { background:#c9c7ff; animation-delay: .6s; }
@keyframes bob { 50% { transform: translateY(-4px); } }

/* 낙엽 */
.leaf {
  position: absolute; top: -40px; width: 28px; height: 16px; border-radius: 60% 40% 70% 30% / 60% 50% 50% 40%;
  background: linear-gradient(135deg, #f6c36b, #f08d49);
  box-shadow: inset 0 -2px 0 rgba(0,0,0,0.16);
  animation: fall linear infinite, sway ease-in-out infinite;
  opacity: 0.9;
}
.leaf::after { content:""; position:absolute; inset: 4px; border-radius: inherit; border-left: 2px solid rgba(0,0,0,0.18); transform: rotate(-12deg); }
@keyframes fall { to { transform: translateY(120vh) rotate(160deg); } }
@keyframes sway { 50% { margin-left: 40px; } }

/* 복주머니 모달 */
.pouch {
  border: none; padding: 0; background: transparent; width: min(92vw, 540px);
}
.pouch::backdrop { background: rgba(8, 12, 28, 0.6); backdrop-filter: blur(2px); }
.pouch-card {
  background: radial-gradient(120% 120% at 50% 0%, rgba(255,255,255,0.06), rgba(255,255,255,0.02));
  border: 1px solid rgba(255,255,255,0.12);
  border-radius: 20px; padding: 20px 20px 16px; color: var(--ink);
  box-shadow: 0 20px 60px rgba(0,0,0,0.4);
}
.pouch-head { display: grid; place-items: center; margin-bottom: 10px; position: relative; }
.pouch-head .knot {
  width: 56px; height: 18px; border-radius: 12px;
  background: linear-gradient(135deg, #ff6b6b, #ff8a8a); display: inline-block;
  box-shadow: 0 6px 14px rgba(255, 107, 107, 0.35);
}
.pouch h2 { margin: 10px 0 6px; font-size: 1.3rem; }
.pouch .wish { margin: 0; text-align: center; color: var(--ink); font-size: 1.05rem; line-height: 1.5; padding: 6px 4px 2px; }
.pouch-actions { margin-top: 14px; display: flex; gap: 8px; justify-content: center; flex-wrap: wrap; }

.footer {
  position: absolute; bottom: 12px; left: 0; right: 0;
  text-align: center; color: rgba(255,255,255,0.55);
  font-size: 12px; z-index: 2;
}

/* 반응형 */
@media (max-width: 640px) {
  .moon { width: 140px; height: 140px; }
  .moon-wrap { right: 5vw; top: 8vh; }
  .lantern { display: none; }
  .hero { padding: 18vh 1.2rem 20vh; }
}
</style>
