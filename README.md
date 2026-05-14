<!DOCTYPE html>
<html lang="ko">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>시간표</title>
<link href="https://fonts.googleapis.com/css2?family=Noto+Sans+KR:wght@300;400;500;700;900&family=Space+Mono:wght@400;700&display=swap" rel="stylesheet">
<style>
  :root {
    --bg: #0d0f14;
    --surface: #161a23;
    --surface2: #1e2433;
    --border: #2a3045;
    --accent: #4f8ef7;
    --accent2: #f7c94f;
    --active-bg: #1a2a4a;
    --active-border: #4f8ef7;
    --text: #e8ecf5;
    --text-dim: #7a8aa8;
    --text-muted: #3d4a62;
    --now-glow: 0 0 20px rgba(79,142,247,0.35), 0 0 40px rgba(79,142,247,0.15);
    --radius: 10px;
  }

  * { margin: 0; padding: 0; box-sizing: border-box; }

  body {
    background: var(--bg);
    color: var(--text);
    font-family: 'Noto Sans KR', sans-serif;
    min-height: 100vh;
    overflow-x: hidden;
  }

  /* ── 배경 그리드 패턴 ── */
  body::before {
    content: '';
    position: fixed;
    inset: 0;
    background-image:
      linear-gradient(rgba(79,142,247,0.03) 1px, transparent 1px),
      linear-gradient(90deg, rgba(79,142,247,0.03) 1px, transparent 1px);
    background-size: 40px 40px;
    pointer-events: none;
    z-index: 0;
  }

  .container {
    position: relative;
    z-index: 1;
    max-width: 1100px;
    margin: 0 auto;
    padding: 28px 20px 60px;
  }

  /* ── 헤더 ── */
  header {
    display: flex;
    align-items: flex-end;
    justify-content: space-between;
    margin-bottom: 28px;
    flex-wrap: wrap;
    gap: 12px;
  }

  .header-left h1 {
    font-size: 2rem;
    font-weight: 900;
    letter-spacing: -0.03em;
    line-height: 1;
  }
  .header-left h1 span { color: var(--accent); }

  .header-left .subtitle {
    font-size: 0.75rem;
    color: var(--text-dim);
    margin-top: 6px;
    letter-spacing: 0.05em;
    text-transform: uppercase;
  }

  .clock-box {
    background: var(--surface);
    border: 1px solid var(--border);
    border-radius: var(--radius);
    padding: 10px 20px;
    text-align: right;
  }
  .clock-time {
    font-family: 'Space Mono', monospace;
    font-size: 1.6rem;
    font-weight: 700;
    color: var(--accent);
    letter-spacing: 0.05em;
  }
  .clock-date {
    font-size: 0.72rem;
    color: var(--text-dim);
    margin-top: 2px;
    letter-spacing: 0.04em;
  }

  /* ── 현재 수업 배너 ── */
  #now-banner {
    background: var(--active-bg);
    border: 1px solid var(--active-border);
    border-radius: var(--radius);
    padding: 14px 20px;
    margin-bottom: 24px;
    display: flex;
    align-items: center;
    gap: 14px;
    box-shadow: var(--now-glow);
    transition: all 0.4s;
  }
  #now-banner.empty {
    border-color: var(--border);
    background: var(--surface);
    box-shadow: none;
  }
  .now-dot {
    width: 10px; height: 10px;
    border-radius: 50%;
    background: var(--accent);
    flex-shrink: 0;
    animation: pulse 1.6s infinite;
  }
  #now-banner.empty .now-dot { background: var(--text-muted); animation: none; }
  @keyframes pulse {
    0%,100% { box-shadow: 0 0 0 0 rgba(79,142,247,0.6); }
    50% { box-shadow: 0 0 0 6px rgba(79,142,247,0); }
  }
  .now-label {
    font-size: 0.68rem;
    color: var(--accent);
    text-transform: uppercase;
    letter-spacing: 0.1em;
    font-weight: 700;
  }
  #now-banner.empty .now-label { color: var(--text-muted); }
  .now-subject { font-size: 1.05rem; font-weight: 700; }
  .now-meta { font-size: 0.78rem; color: var(--text-dim); margin-top: 1px; }
  .now-countdown {
    margin-left: auto;
    font-family: 'Space Mono', monospace;
    font-size: 0.85rem;
    color: var(--accent2);
    white-space: nowrap;
  }

  /* ── 요일 탭 ── */
  .day-tabs {
    display: flex;
    gap: 6px;
    margin-bottom: 20px;
    flex-wrap: wrap;
  }
  .day-tab {
    padding: 7px 18px;
    border-radius: 6px;
    border: 1px solid var(--border);
    background: var(--surface);
    color: var(--text-dim);
    font-size: 0.82rem;
    font-weight: 500;
    cursor: pointer;
    transition: all 0.2s;
    font-family: 'Noto Sans KR', sans-serif;
  }
  .day-tab:hover { border-color: var(--accent); color: var(--text); }
  .day-tab.active {
    background: var(--accent);
    border-color: var(--accent);
    color: #fff;
    font-weight: 700;
  }
  .day-tab.today-tab {
    border-color: var(--accent2);
    color: var(--accent2);
  }
  .day-tab.today-tab.active {
    background: var(--accent2);
    border-color: var(--accent2);
    color: #0d0f14;
  }

  /* ── 시간표 목록 ── */
  .schedule-list { display: flex; flex-direction: column; gap: 4px; }

  .period-row {
    display: grid;
    grid-template-columns: 90px 110px 1fr 110px 100px;
    align-items: center;
    gap: 0;
    background: var(--surface);
    border: 1px solid var(--border);
    border-radius: var(--radius);
    padding: 12px 16px;
    transition: all 0.25s;
    position: relative;
    overflow: hidden;
  }
  .period-row::before {
    content: '';
    position: absolute;
    left: 0; top: 0; bottom: 0;
    width: 3px;
    background: transparent;
    border-radius: 10px 0 0 10px;
    transition: background 0.3s;
  }
  .period-row:hover { border-color: #3a4560; background: var(--surface2); }

  /* 특수 행 (점심, 청소, 저녁) */
  .period-row.special {
    background: transparent;
    border-color: transparent;
    padding: 5px 16px;
  }
  .period-row.special:hover { background: transparent; border-color: transparent; }

  /* 현재 수업 강조 */
  .period-row.is-now {
    background: var(--active-bg);
    border-color: var(--active-border);
    box-shadow: var(--now-glow);
  }
  .period-row.is-now::before { background: var(--accent); }

  /* 지난 수업 */
  .period-row.is-past { opacity: 0.4; }
  .period-row.is-past .period-num,
  .period-row.is-past .period-subject { color: var(--text-muted); }

  .period-num {
    font-family: 'Space Mono', monospace;
    font-size: 0.72rem;
    color: var(--text-muted);
    font-weight: 700;
  }
  .period-row.is-now .period-num { color: var(--accent); }

  .period-time {
    font-family: 'Space Mono', monospace;
    font-size: 0.7rem;
    color: var(--text-muted);
  }
  .period-row.is-now .period-time { color: var(--text-dim); }

  .period-subject {
    font-size: 0.95rem;
    font-weight: 700;
    padding: 0 12px;
  }
  .period-row.special .period-subject {
    font-size: 0.78rem;
    font-weight: 400;
    color: var(--text-muted);
  }

  .period-room {
    font-size: 0.75rem;
    color: var(--text-dim);
  }

  .period-teacher {
    font-size: 0.75rem;
    color: var(--text-dim);
    text-align: right;
  }

  /* 빈 교시 */
  .period-row.empty-period .period-subject { color: var(--text-muted); font-weight: 400; font-size: 0.8rem; }

  /* ── 반응형 ── */
  @media (max-width: 640px) {
    .period-row {
      grid-template-columns: 70px 95px 1fr;
    }
    .period-room, .period-teacher { display: none; }
    .period-subject { padding: 0 8px; }
    .clock-time { font-size: 1.25rem; }
    header h1 { font-size: 1.5rem; }
  }
</style>
</head>
<body>
<div class="container">

  <header>
    <div class="header-left">
      <h1>내 <span>시간표</span></h1>
      <div class="subtitle">실시간 수업 안내 · 2025학년도</div>
    </div>
    <div class="clock-box">
      <div class="clock-time" id="clock">--:--:--</div>
      <div class="clock-date" id="date-label">---</div>
    </div>
  </header>

  <!-- 현재 수업 배너 -->
  <div id="now-banner" class="empty">
    <div class="now-dot"></div>
    <div>
      <div class="now-label" id="now-label">현재 수업 없음</div>
      <div class="now-subject" id="now-subject">—</div>
      <div class="now-meta" id="now-meta"></div>
    </div>
    <div class="now-countdown" id="now-countdown"></div>
  </div>

  <!-- 요일 탭 -->
  <div class="day-tabs">
    <button class="day-tab" data-day="0" onclick="selectDay(0)">월요일</button>
    <button class="day-tab" data-day="1" onclick="selectDay(1)">화요일</button>
    <button class="day-tab" data-day="2" onclick="selectDay(2)">수요일</button>
    <button class="day-tab" data-day="3" onclick="selectDay(3)">목요일</button>
    <button class="day-tab" data-day="4" onclick="selectDay(4)">금요일</button>
  </div>

  <div class="schedule-list" id="schedule-list"></div>

</div>

<script>
// ── 시간표 데이터 ──
const timetable = [
  // 월요일 (0)
  [
    { type:'class', period:'1교시', start:'08:40', end:'09:30', subject:'자율', room:'3-6반', teacher:'고익준' },
    { type:'class', period:'2교시', start:'09:40', end:'10:30', subject:'사회 과제 연구', room:'1학년 전용 교실', teacher:'김현욱' },
    { type:'class', period:'3교시', start:'10:40', end:'11:30', subject:'영어 독해와 작문', room:'위국실', teacher:'' },
    { type:'class', period:'4교시', start:'11:40', end:'12:30', subject:'고전윤리', room:'3-2반', teacher:'조현응' },
    { type:'special', period:'점심', start:'12:30', end:'13:30', subject:'🍚 점심시간', room:'급식실', teacher:'' },
    { type:'class', period:'5교시', start:'13:30', end:'14:20', subject:'예배', room:'3-6반', teacher:'고익준' },
    { type:'class', period:'6교시', start:'14:30', end:'15:20', subject:'사회문화', room:'3학년 전용교실', teacher:'은종서' },
    { type:'special', period:'청소', start:'15:20', end:'15:40', subject:'🧹 청소시간', room:'3-6반', teacher:'고익준' },
    { type:'class', period:'7교시', start:'15:40', end:'16:30', subject:'논술', room:'3-8반', teacher:'서버들' },
    { type:'class', period:'8교시', start:'16:40', end:'17:30', subject:'확률과 통계 방과후', room:'3-2반', teacher:'신소운' },
    { type:'special', period:'저녁', start:'17:30', end:'18:30', subject:'🌙 저녁시간', room:'급식실', teacher:'' },
    { type:'class', period:'9교시', start:'18:30', end:'20:10', subject:'야간 자율 학습', room:'3학년 전용 교실', teacher:'' },
    { type:'class', period:'10교시', start:'20:20', end:'21:30', subject:'야간 자율 학습', room:'3학년 전용 교실', teacher:'' },
  ],
  // 화요일 (1)
  [
    { type:'class', period:'1교시', start:'08:40', end:'09:30', subject:'고전윤리', room:'3-2반', teacher:'조현응' },
    { type:'class', period:'2교시', start:'09:40', end:'10:30', subject:'영어 독해와 작문', room:'위국실', teacher:'서버들' },
    { type:'class', period:'3교시', start:'10:40', end:'11:30', subject:'확률과 통계', room:'3학년 전용 교실', teacher:'신소운' },
    { type:'class', period:'4교시', start:'11:40', end:'12:30', subject:'사회문화', room:'3학년 전용 교실', teacher:'은종서' },
    { type:'special', period:'점심', start:'12:30', end:'13:30', subject:'🍚 점심시간', room:'급식실', teacher:'' },
    { type:'class', period:'5교시', start:'13:30', end:'14:20', subject:'생명과학 2', room:'3-6반', teacher:'최효주' },
    { type:'class', period:'6교시', start:'14:30', end:'15:20', subject:'일본어 2', room:'위국실', teacher:'정주리' },
    { type:'special', period:'청소', start:'15:20', end:'15:40', subject:'🧹 청소시간', room:'3-6반', teacher:'고익준' },
    { type:'class', period:'7교시', start:'15:40', end:'16:30', subject:'사회 문화 방과후', room:'3-8반', teacher:'은종서' },
    { type:'class', period:'8교시', start:'16:40', end:'17:30', subject:'사회 문화 방과후', room:'3-8반', teacher:'은종서' },
    { type:'special', period:'저녁', start:'17:30', end:'18:30', subject:'🌙 저녁시간', room:'급식실', teacher:'' },
    { type:'class', period:'9교시', start:'18:30', end:'20:10', subject:'사회 문제 탐구 캠공', room:'수학전용교실', teacher:'은종서' },
    { type:'class', period:'10교시', start:'20:20', end:'21:30', subject:'사회 문제 탐구 캠공', room:'수학전용교실', teacher:'은종서' },
  ],
  // 수요일 (2)
  [
    { type:'class', period:'1교시', start:'08:40', end:'09:30', subject:'고전윤리', room:'3-2반', teacher:'조현응' },
    { type:'class', period:'2교시', start:'09:40', end:'10:30', subject:'사회 과제 연구', room:'1학년 전용 교실', teacher:'김현욱' },
    { type:'class', period:'3교시', start:'10:40', end:'11:30', subject:'심화 국어', room:'3-6반', teacher:'김재린' },
    { type:'class', period:'4교시', start:'11:40', end:'12:30', subject:'화법과 작문', room:'3-8반', teacher:'주영진' },
    { type:'special', period:'점심', start:'12:30', end:'13:30', subject:'🍚 점심시간', room:'급식실', teacher:'' },
    { type:'class', period:'5교시', start:'13:30', end:'14:20', subject:'생명과학 2', room:'3-6반', teacher:'최효주' },
    { type:'class', period:'6교시', start:'14:30', end:'15:20', subject:'심화 영어 독해', room:'3-7반', teacher:'서버들' },
    { type:'special', period:'청소', start:'15:20', end:'15:40', subject:'🧹 청소시간', room:'3-6반', teacher:'고익준' },
    { type:'empty', period:'7교시', start:'15:40', end:'16:30', subject:'(없음)', room:'', teacher:'' },
    { type:'class', period:'8교시', start:'16:40', end:'17:30', subject:'야간 자율 학습', room:'3학년 전용 교실', teacher:'' },
    { type:'special', period:'저녁', start:'17:30', end:'18:30', subject:'🌙 저녁시간', room:'급식실', teacher:'' },
    { type:'class', period:'9교시', start:'18:30', end:'20:10', subject:'야간 자율 학습', room:'3학년 전용 교실', teacher:'' },
    { type:'class', period:'10교시', start:'20:20', end:'21:30', subject:'야간 자율 학습', room:'3학년 전용 교실', teacher:'' },
  ],
  // 목요일 (3)
  [
    { type:'class', period:'1교시', start:'08:40', end:'09:30', subject:'심화 국어', room:'3-6반', teacher:'김재린' },
    { type:'class', period:'2교시', start:'09:40', end:'10:30', subject:'사회 문화', room:'3학년 전용교실', teacher:'은종서' },
    { type:'class', period:'3교시', start:'10:40', end:'11:30', subject:'심화 영어 독해', room:'3-7반', teacher:'서버들' },
    { type:'class', period:'4교시', start:'11:40', end:'12:30', subject:'화법과 작문', room:'3-8반', teacher:'주영진' },
    { type:'special', period:'점심', start:'12:30', end:'13:30', subject:'🍚 점심시간', room:'급식실', teacher:'' },
    { type:'class', period:'5교시', start:'13:30', end:'14:20', subject:'미술 창작', room:'미술실', teacher:'전아영' },
    { type:'class', period:'6교시', start:'14:30', end:'15:20', subject:'확률과 통계', room:'3학년 전용 교실', teacher:'신소운' },
    { type:'special', period:'청소', start:'15:20', end:'15:40', subject:'🧹 청소시간', room:'3-6반', teacher:'고익준' },
    { type:'class', period:'7교시', start:'15:40', end:'16:30', subject:'일본어 2', room:'위국실', teacher:'정주리' },
    { type:'class', period:'8교시', start:'16:40', end:'17:30', subject:'확률과 통계 방과후', room:'3-2반', teacher:'신소운' },
    { type:'special', period:'저녁', start:'17:30', end:'18:30', subject:'🌙 저녁시간', room:'급식실', teacher:'' },
    { type:'class', period:'9교시', start:'18:30', end:'20:10', subject:'사회 문제 탐구 캠공', room:'수학 전용교실', teacher:'은종서' },
    { type:'class', period:'10교시', start:'20:20', end:'21:30', subject:'사회 문제 탐구 캠공', room:'수학 전용교실', teacher:'은종서' },
  ],
  // 금요일 (4)
  [
    { type:'class', period:'1교시', start:'08:40', end:'09:30', subject:'확률과 통계', room:'3학년 전용교실', teacher:'신소운' },
    { type:'class', period:'2교시', start:'09:40', end:'10:30', subject:'스포츠 생활', room:'체육관', teacher:'이정우' },
    { type:'class', period:'3교시', start:'10:40', end:'11:30', subject:'진로', room:'애인실', teacher:'주영진' },
    { type:'class', period:'4교시', start:'11:40', end:'12:30', subject:'동아리', room:'애인실', teacher:'주영진' },
    { type:'special', period:'점심', start:'12:30', end:'13:30', subject:'🍚 점심시간', room:'급식실', teacher:'' },
    { type:'class', period:'5교시', start:'13:30', end:'14:20', subject:'화법과 작문', room:'3-8반', teacher:'주영진' },
    { type:'class', period:'6교시', start:'14:30', end:'15:20', subject:'생명과학 2', room:'3-6반', teacher:'최효주' },
    { type:'special', period:'청소', start:'15:20', end:'15:40', subject:'🧹 청소시간', room:'3-6반', teacher:'고익준' },
    { type:'class', period:'7교시', start:'15:40', end:'16:30', subject:'영어 독해와 작문', room:'위국실', teacher:'서버들' },
    { type:'class', period:'8교시', start:'16:40', end:'17:30', subject:'야간 자율 학습', room:'3학년 전용교실', teacher:'' },
    { type:'special', period:'저녁', start:'17:30', end:'18:30', subject:'🌙 저녁시간', room:'급식실', teacher:'' },
    { type:'class', period:'9교시', start:'18:30', end:'20:10', subject:'야간 자율 학습', room:'3학년 전용교실', teacher:'' },
    { type:'class', period:'10교시', start:'20:20', end:'21:30', subject:'야간 자율 학습', room:'3학년 전용교실', teacher:'' },
  ],
];

const DAY_NAMES = ['월요일','화요일','수요일','목요일','금요일'];

let selectedDay = null;

function toMinutes(timeStr) {
  const [h, m] = timeStr.split(':').map(Number);
  return h * 60 + m;
}

function getTodayIdx() {
  const d = new Date().getDay(); // 0=일,1=월,...,6=토
  return d - 1; // 월=0,...,금=4, 그외 음수/5+
}

function getNowMinutes() {
  const n = new Date();
  return n.getHours() * 60 + n.getMinutes();
}

function getCurrentPeriod(dayIdx) {
  if (dayIdx < 0 || dayIdx > 4) return null;
  const now = getNowMinutes();
  for (const item of timetable[dayIdx]) {
    if (toMinutes(item.start) <= now && now < toMinutes(item.end)) return item;
  }
  return null;
}

function renderSchedule(dayIdx) {
  const list = document.getElementById('schedule-list');
  const now = getNowMinutes();
  const todayIdx = getTodayIdx();
  const isToday = (dayIdx === todayIdx);
  list.innerHTML = '';

  timetable[dayIdx].forEach(item => {
    const row = document.createElement('div');
    const startMin = toMinutes(item.start);
    const endMin = toMinutes(item.end);
    const isNow = isToday && startMin <= now && now < endMin;
    const isPast = isToday && now >= endMin;

    let classes = 'period-row';
    if (item.type === 'special') classes += ' special';
    if (item.type === 'empty') classes += ' empty-period';
    if (isNow) classes += ' is-now';
    else if (isPast) classes += ' is-past';

    row.className = classes;
    row.id = `row-${item.start.replace(':','')}`;

    row.innerHTML = `
      <div class="period-num">${item.type === 'special' ? '' : item.period}</div>
      <div class="period-time">${item.start}–${item.end}</div>
      <div class="period-subject">${item.subject}</div>
      <div class="period-room">${item.room}</div>
      <div class="period-teacher">${item.teacher}</div>
    `;
    list.appendChild(row);
  });
}

function updateBanner() {
  const todayIdx = getTodayIdx();
  const banner = document.getElementById('now-banner');
  const labelEl = document.getElementById('now-label');
  const subjectEl = document.getElementById('now-subject');
  const metaEl = document.getElementById('now-meta');
  const countdownEl = document.getElementById('now-countdown');

  if (todayIdx < 0 || todayIdx > 4) {
    banner.className = 'empty';
    labelEl.textContent = '오늘은 수업이 없는 날이에요';
    subjectEl.textContent = '주말 / 공휴일';
    metaEl.textContent = '';
    countdownEl.textContent = '';
    return;
  }

  const current = getCurrentPeriod(todayIdx);
  const now = getNowMinutes();

  if (current) {
    banner.className = '';
    labelEl.textContent = '지금 수업 중';
    subjectEl.textContent = current.subject;
    const parts = [];
    if (current.room) parts.push(current.room);
    if (current.teacher) parts.push(current.teacher + ' 선생님');
    metaEl.textContent = parts.join(' · ');
    const remaining = toMinutes(current.end) - now;
    countdownEl.textContent = `${remaining}분 남음`;
  } else {
    // 다음 수업 찾기
    let next = null;
    for (const item of timetable[todayIdx]) {
      if (toMinutes(item.start) > now) { next = item; break; }
    }
    if (next) {
      banner.className = 'empty';
      labelEl.textContent = '쉬는 시간 / 대기 중';
      subjectEl.textContent = `다음: ${next.subject}`;
      const parts = [];
      if (next.room) parts.push(next.room);
      if (next.teacher) parts.push(next.teacher + ' 선생님');
      metaEl.textContent = parts.join(' · ');
      const wait = toMinutes(next.start) - now;
      countdownEl.textContent = `${wait}분 후 시작`;
    } else {
      banner.className = 'empty';
      labelEl.textContent = '오늘 수업 끝';
      subjectEl.textContent = '모든 일정 완료';
      metaEl.textContent = '';
      countdownEl.textContent = '';
    }
  }
}

function updateClock() {
  const now = new Date();
  const h = String(now.getHours()).padStart(2,'0');
  const m = String(now.getMinutes()).padStart(2,'0');
  const s = String(now.getSeconds()).padStart(2,'0');
  document.getElementById('clock').textContent = `${h}:${m}:${s}`;

  const days = ['일','월','화','수','목','금','토'];
  const dateStr = `${now.getFullYear()}년 ${now.getMonth()+1}월 ${now.getDate()}일 (${days[now.getDay()]})`;
  document.getElementById('date-label').textContent = dateStr;
}

function selectDay(dayIdx) {
  selectedDay = dayIdx;
  document.querySelectorAll('.day-tab').forEach((btn, i) => {
    btn.classList.toggle('active', i === dayIdx);
  });
  renderSchedule(dayIdx);
  updateBanner();
}

function init() {
  // 요일 탭에 오늘 표시
  const todayIdx = getTodayIdx();
  document.querySelectorAll('.day-tab').forEach((btn, i) => {
    if (i === todayIdx) btn.classList.add('today-tab');
  });

  // 오늘 또는 월요일로 시작
  const startDay = (todayIdx >= 0 && todayIdx <= 4) ? todayIdx : 0;
  selectDay(startDay);

  updateClock();
  setInterval(() => {
    updateClock();
    updateBanner();
    // 현재 보고 있는 날이 오늘이면 강조 갱신
    if (selectedDay === getTodayIdx()) renderSchedule(selectedDay);
  }, 10000); // 10초마다 갱신
}

init();
</script>
</body>
</html>
