<!-- -->// ============================================
// METEOR DODGE - 15 SKINS + ALL POWERS (FIXED)
// ============================================

const canvas = document.getElementById('gameCanvas');
const ctx = canvas.getContext('2d');

// UI Elements
const scoreSpan = document.getElementById('scoreDisplay');
const speedSpan = document.getElementById('speedDisplay');
const coinDisplay = document.getElementById('coinDisplay');
const leaderboardList = document.getElementById('leaderboardList');
const reviveButton = document.getElementById('reviveButton');
const restartDeathButton = document.getElementById('restartDeathButton');
const powerButton = document.getElementById('powerButton');
const shopModal = document.getElementById('shopModal');
const nameModal = document.getElementById('nameModal');
const shopCoinDisplay = document.getElementById('shopCoinDisplay');
const powerIndicator = document.getElementById('powerIndicator');
const powerIcon = document.getElementById('powerIcon');
const powerTimer = document.getElementById('powerTimer');
const finalScoreDisplay = document.getElementById('finalScoreDisplay');
const playerNameInput = document.getElementById('playerNameInput');

const W = canvas.width = 400;
const H = canvas.height = 600;

// ========== PLAYER ==========
let player = { x: W / 2, y: H - 70, radius: 16 };

// ========== ENTITIES ==========
let meteors = [];
let particles = [];
let coins = [];

// ========== BACKGROUND STARS ==========
let stars = [];
for (let i = 0; i < 100; i++) {
  stars.push({
    x: Math.random() * W,
    y: Math.random() * H,
    size: Math.random() * 2 + 0.5,
    speed: Math.random() * 0.5 + 0.1,
    twinkle: Math.random() * Math.PI * 2,
    twinkleSpeed: Math.random() * 0.02 + 0.01
  });
}

// ========== 15 SKINS DATABASE ==========
const SKINS = [
  { id: 'default', name: '🔵 Default', power: 'None', cost: 0, colors: { primary: '#ddf0ff', glow: '#44ccff', engine: '#ffbb55' }, powers: [] },
  { id: 'purple', name: '💜 Neon Purple', power: '2x Coins', cost: 25, colors: { primary: '#ddccff', glow: '#cc44ff', engine: '#ff44ff' }, powers: ['doubleCoins'] },
  { id: 'green', name: '💚 Toxic Green', power: 'Slow Motion', cost: 40, colors: { primary: '#ccffcc', glow: '#44ff44', engine: '#88ff44' }, powers: ['slowMotion'] },
  { id: 'thunder', name: '⚡ Thunder', power: 'Speed Boost', cost: 50, colors: { primary: '#ffffcc', glow: '#ffff00', engine: '#ffdd00' }, powers: ['speedBoost'] },
  { id: 'nature', name: '🌿 Nature', power: 'Heal Over Time', cost: 55, colors: { primary: '#ccffcc', glow: '#66ff66', engine: '#44cc44' }, powers: ['healOverTime'] },
  { id: 'fire', name: '🔥 Fire Red', power: 'Shield', cost: 60, colors: { primary: '#ffddcc', glow: '#ff4444', engine: '#ff8800' }, powers: ['shield'] },
  { id: 'shadow', name: '🌑 Shadow', power: 'Invisibility', cost: 70, colors: { primary: '#333355', glow: '#6666aa', engine: '#444477' }, powers: ['invisibility'] },
  { id: 'ghost', name: '👻 Ghost', power: 'Phase Through', cost: 75, colors: { primary: '#ffffff', glow: '#ccccff', engine: '#eeeeff' }, powers: ['phaseThrough'] },
  { id: 'gold', name: '🥇 Golden', power: '3x Coins', cost: 80, colors: { primary: '#ffd700', glow: '#ffaa00', engine: '#ffcc00' }, powers: ['tripleCoins'] },
  { id: 'vampire', name: '🧛 Vampire', power: 'Life Steal', cost: 85, colors: { primary: '#ffcccc', glow: '#ff6666', engine: '#cc0000' }, powers: ['lifeSteal'] },
  { id: 'ice', name: '❄️ Ice Crystal', power: 'Freeze Meteors', cost: 90, colors: { primary: '#e0f0ff', glow: '#88ccff', engine: '#aaddff' }, powers: ['freezeNearby'] },
  { id: 'robot', name: '🤖 Robot', power: 'Auto Dodge', cost: 95, colors: { primary: '#cccccc', glow: '#888888', engine: '#00ff88' }, powers: ['autoDodge'] },
  { id: 'rainbow', name: '🌈 Rainbow', power: 'All Powers', cost: 100, colors: { primary: '#fff', glow: 'rainbow', engine: 'rainbow' }, powers: ['doubleCoins', 'slowMotion', 'shield'] },
  { id: 'cosmic', name: '🌌 Cosmic', power: 'Black Hole', cost: 110, colors: { primary: '#ffccff', glow: '#ff66ff', engine: '#cc00cc' }, powers: ['blackHole'] },
  { id: 'phoenix', name: '🦅 Phoenix', power: 'Rebirth', cost: 120, colors: { primary: '#ffaa00', glow: '#ff4400', engine: '#ff8800' }, powers: ['rebirth'] }
];

// ========== GAME STATE ==========
let score = 0;
let gameOver = false;
let gameActive = false;
let gameStarted = false;
let frameCounter = 0;
let currentSpeedMultiplier = 1.0;
const BASE_SPEED = 2.7;
const MAX_MULTIPLIER = 3.8;
let spawnDelay = 38;
let shielded = false;

// ========== MOUSE ==========
let mouseX = player.x;
let mouseY = player.y;

// ========== PERSISTENT DATA ==========
let totalCoins = parseInt(localStorage.getItem('playerCoins')) || 0;
let currentSkin = localStorage.getItem('playerSkin') || 'default';
let ownedSkins = JSON.parse(localStorage.getItem('ownedSkins')) || ['default'];
let playerName = localStorage.getItem('playerName') || '';

// ========== POWER-UP SYSTEM ==========
let slowMotionActive = false;
let slowMotionTimer = 0;
let slowMotionCooldown = 0;
const SLOW_DURATION = 240;
const SLOW_COOLDOWN = 1200;
const SLOW_FACTOR = 0.3;

// ========== SPECIAL POWER STATES ==========
let freezeActive = false;
let freezeTimer = 0;
let autoDodgeReady = true;
let phaseReady = true;
let phoenixUsed = false;

// ========== LEADERBOARD ==========
let leaderboard = [];
let personalBest = parseInt(localStorage.getItem('personalBest')) || 0;
let pendingScore = 0;

// ========== POWER HELPERS ==========
function hasPower(power) {
  const skin = SKINS.find(s => s.id === currentSkin);
  if (!skin) return false;
  if (currentSkin === 'rainbow' && ['doubleCoins', 'slowMotion', 'shield'].includes(power)) return true;
  return skin.powers.includes(power);
}

function getCoinMultiplier() {
  if (hasPower('tripleCoins')) return 3;
  if (hasPower('doubleCoins')) return 2;
  return 1;
}

function getSkinColors() {
  const skin = SKINS.find(s => s.id === currentSkin);
  if (!skin) return SKINS[0].colors;
  if (skin.id === 'rainbow') {
    return {
      primary: '#fff',
      glow: `hsl(${Date.now()/10 % 360}, 100%, 50%)`,
      engine: `hsl(${(Date.now()/10 + 60) % 360}, 100%, 50%)`
    };
  }
  return skin.colors;
}

// ========== COIN FUNCTIONS ==========
function addCoins(amount) {
  const multiplier = getCoinMultiplier();
  totalCoins += amount * multiplier;
  localStorage.setItem('playerCoins', totalCoins);
  updateCoinDisplay();
}

function spendCoins(amount) {
  if (totalCoins >= amount) {
    totalCoins -= amount;
    localStorage.setItem('playerCoins', totalCoins);
    updateCoinDisplay();
    return true;
  }
  return false;
}

function updateCoinDisplay() {
  coinDisplay.textContent = totalCoins;
  if (shopCoinDisplay) shopCoinDisplay.textContent = totalCoins;
  updateDeathButtons();
  if (shopModal.classList.contains('active')) renderShop();
}

// ========== SKIN SYSTEM ==========
function buySkin(skinName, cost) {
  if (ownedSkins.includes(skinName)) {
    equipSkin(skinName);
    return;
  }
  if (spendCoins(cost)) {
    ownedSkins.push(skinName);
    localStorage.setItem('ownedSkins', JSON.stringify(ownedSkins));
    equipSkin(skinName);
    renderShop();
    showNotification('✅ Skin equipped!');
  } else {
    showNotification('❌ Not enough coins!');
  }
}

function equipSkin(skinName) {
  currentSkin = skinName;
  localStorage.setItem('playerSkin', skinName);
  shielded = hasPower('shield');
  applySkinPowers();
  updatePowerUI();
  renderShop();
}

function applySkinPowers() {
  freezeActive = false;
  freezeTimer = 0;
  autoDodgeReady = true;
  phaseReady = true;
  phoenixUsed = false;
}

// ========== SHOP UI ==========
function renderShop() {
  const grid = document.getElementById('skinGrid');
  if (!grid) return;
  
  grid.innerHTML = '';
  
  SKINS.forEach(skin => {
    const card = document.createElement('div');
    card.className = 'skin-card';
    if (currentSkin === skin.id) card.classList.add('equipped');
    else if (ownedSkins.includes(skin.id)) card.classList.add('owned');
    
    const previewBg = skin.id === 'rainbow' ? 'linear-gradient(90deg, red, orange, yellow, green, blue, purple)' : skin.colors.primary;
    const previewGlow = skin.id === 'rainbow' ? 'gold' : skin.colors.glow;
    
    card.innerHTML = `
      <div class="skin-preview" style="background:${previewBg}; box-shadow:0 0 15px ${previewGlow};"></div>
      <div class="skin-name">${skin.name}</div>
      <div class="skin-power">${skin.power}</div>
      <div class="skin-cost">${skin.cost === 0 ? 'FREE' : '🪙 ' + skin.cost}</div>
      <button class="skin-btn ${currentSkin === skin.id ? 'equipped' : ''}" 
              ${(!ownedSkins.includes(skin.id) && totalCoins < skin.cost) ? 'disabled' : ''}>
        ${currentSkin === skin.id ? 'EQUIPPED' : ownedSkins.includes(skin.id) ? 'EQUIP' : 'BUY'}
      </button>
    `;
    
    card.addEventListener('click', (e) => {
      if (e.target.tagName === 'BUTTON') return;
      buySkin(skin.id, skin.cost);
    });
    grid.appendChild(card);
  });
}

// ========== DEATH BUTTONS ==========
function updateDeathButtons() {
  if (gameOver) {
    reviveButton.classList.toggle('show', totalCoins >= 30);
    restartDeathButton.classList.add('show');
  } else {
    reviveButton.classList.remove('show');
    restartDeathButton.classList.remove('show');
  }
}

// ========== POWER UI ==========
function updatePowerUI() {
  if (!gameActive || gameOver || !hasPower('slowMotion')) {
    if (powerButton) powerButton.classList.remove('show');
    if (powerIndicator) powerIndicator.style.display = 'none';
    return;
  }
  
  if (powerButton) powerButton.classList.add('show');
  if (powerIndicator) powerIndicator.style.display = 'block';
  
  if (slowMotionActive) {
    powerButton.textContent = '❄️ ' + Math.ceil(slowMotionTimer / 60) + 's';
    powerButton.classList.add('cooldown'); powerButton.disabled = true;
    powerIcon.textContent = '❄️'; powerTimer.textContent = Math.ceil(slowMotionTimer / 60) + 's';
    powerIndicator.className = 'power-indicator'; powerIndicator.style.borderColor = '#44aaff';
  } else if (slowMotionCooldown > 0) {
    powerButton.textContent = '⏳ ' + Math.ceil(slowMotionCooldown / 60) + 's';
    powerButton.classList.add('cooldown'); powerButton.disabled = true;
    powerIcon.textContent = '⏳'; powerTimer.textContent = Math.ceil(slowMotionCooldown / 60) + 's';
    powerIndicator.className = 'power-indicator cooldown'; powerIndicator.style.borderColor = '#ff4444';
  } else {
    powerButton.textContent = '❄️ SLOW';
    powerButton.classList.remove('cooldown'); powerButton.disabled = false;
    powerIcon.textContent = '⚡'; powerTimer.textContent = 'OK';
    powerIndicator.className = 'power-indicator ready'; powerIndicator.style.borderColor = '#ffd700';
  }
}

function activateSlowMotion() {
  if (!gameActive || gameOver || !hasPower('slowMotion') || slowMotionCooldown > 0 || slowMotionActive) return;
  
  slowMotionActive = true;
  slowMotionTimer = SLOW_DURATION;
  slowMotionCooldown = SLOW_COOLDOWN;
  
  meteors.forEach(m => {
    m.speedY *= SLOW_FACTOR;
    m.speedX *= SLOW_FACTOR;
    m.originalSpeedY = m.speedY;
    m.originalSpeedX = m.speedX;
  });
  
  showNotification('❄️ SLOW MOTION!');
  updatePowerUI();
}

function deactivateSlowMotion() {
  meteors.forEach(m => {
    if (m.originalSpeedY) {
      m.speedY = m.originalSpeedY / SLOW_FACTOR;
      m.speedX = m.originalSpeedX / SLOW_FACTOR;
      delete m.originalSpeedY;
      delete m.originalSpeedX;
    }
  });
  slowMotionActive = false;
}

// ========== NOTIFICATION ==========
function showNotification(message) {
  const existing = document.querySelector('.game-notification');
  if (existing) existing.remove();
  const notif = document.createElement('div');
  notif.className = 'game-notification';
  notif.textContent = message;
  notif.style.animation = 'fadeOut 2s forwards';
  document.body.appendChild(notif);
  setTimeout(() => notif.remove(), 2000);
}

// ========== LEADERBOARD ==========
function autoUpdateLeaderboard(finalScore) {
  if (finalScore <= 10) return;
  if (finalScore <= personalBest) return;
  
  personalBest = finalScore;
  localStorage.setItem('personalBest', personalBest);
  
  if (playerName && playerName.trim() !== '') {
    leaderboard = leaderboard.filter(entry => entry.name !== playerName);
    leaderboard.push({ name: playerName, score: finalScore, date: new Date().toLocaleDateString() });
    saveLeaderboard();
    renderLeaderboard();
    showNotification('🏆 Record auto-saved, ' + playerName + '!');
  } else {
    setTimeout(() => showNameEntry(finalScore), 800);
  }
}

function showNameEntry(finalScore) {
  pendingScore = finalScore;
  finalScoreDisplay.textContent = finalScore;
  playerNameInput.value = '';
  nameModal.classList.add('active');
  setTimeout(() => playerNameInput.focus(), 300);
}

function submitScore() {
  const name = playerNameInput.value.trim();
  if (!name) { showNotification('Enter a name!'); return; }
  playerName = name;
  localStorage.setItem('playerName', playerName);
  leaderboard = leaderboard.filter(entry => entry.name !== playerName);
  leaderboard.push({ name: playerName, score: pendingScore, date: new Date().toLocaleDateString() });
  saveLeaderboard();
  renderLeaderboard();
  nameModal.classList.remove('active');
  showNotification('🏆 Name saved forever!');
}

function skipLeaderboard() { nameModal.classList.remove('active'); }

// ========== REVIVE ==========
function revivePlayer() {
  if (totalCoins < 30 || !gameOver) return;
  spendCoins(30);
  gameOver = false;
  gameActive = true;
  meteors = meteors.slice(0, Math.floor(meteors.length / 2));
  player.x = W / 2; player.y = H - 70;
  mouseX = player.x; mouseY = player.y;
  shielded = hasPower('shield');
  slowMotionActive = false; slowMotionTimer = 0;
  applySkinPowers();
  updateDeathButtons();
  updateUI();
  updatePowerUI();
  showNotification('💫 REVIVED!');
}

// ========== SHOP MODAL ==========
function openShop() {
  shopModal.classList.add('active');
  shopCoinDisplay.textContent = totalCoins;
  renderShop();
}
function closeShop() { shopModal.classList.remove('active'); }

// ========== LEADERBOARD STORAGE ==========
function loadLeaderboard() {
  try { leaderboard = JSON.parse(localStorage.getItem('meteorDodgeLeaderboard')) || []; }
  catch(e) { leaderboard = []; }
  leaderboard.sort((a, b) => b.score - a.score);
  if (leaderboard.length > 5) leaderboard = leaderboard.slice(0, 5);
}

function saveLeaderboard() {
  leaderboard.sort((a, b) => b.score - a.score);
  if (leaderboard.length > 5) leaderboard = leaderboard.slice(0, 5);
  localStorage.setItem('meteorDodgeLeaderboard', JSON.stringify(leaderboard));
}

function renderLeaderboard() {
  if (!leaderboardList) return;
  leaderboardList.innerHTML = leaderboard.length ? '' : '<li style="justify-content:center;">No scores yet</li>';
  leaderboard.forEach((entry, idx) => {
    const li = document.createElement('li');
    li.innerHTML = `<span>#${idx+1} ${entry.name || 'Player'}</span><span>${entry.score} pts</span>`;
    leaderboardList.appendChild(li);
  });
}

// ========== GAME FUNCTIONS ==========
function startGame() { gameActive = true; gameStarted = true; updatePowerUI(); }

function restartGame() {
  player.x = W / 2; player.y = H - 70;
  meteors = []; particles = []; coins = [];
  score = 0; gameOver = false; gameActive = false; gameStarted = false;
  frameCounter = 0; currentSpeedMultiplier = 1.0; spawnDelay = 38;
  mouseX = player.x; mouseY = player.y;
  shielded = hasPower('shield');
  slowMotionActive = false; slowMotionTimer = 0; slowMotionCooldown = 0;
  phoenixUsed = false;
  applySkinPowers();
  updateDeathButtons();
  if (powerButton) powerButton.classList.remove('show');
  if (powerIndicator) powerIndicator.style.display = 'none';
  updateUI();
  setTimeout(() => { if (!gameOver) startGame(); }, 500);
}

function updateUI() {
  scoreSpan.textContent = score;
  speedSpan.textContent = currentSpeedMultiplier.toFixed(1) + 'x';
  coinDisplay.textContent = totalCoins;
}

function spawnMeteor() {
  let r = 14 + Math.random() * 24;
  let x = Math.random() * (W - r*2) + r;
  if (Math.random() < 0.25) x = Math.random() < 0.5 ? r+2 : W-r-2;
  let sy = (BASE_SPEED + Math.random()*2) * currentSpeedMultiplier;
  let sx = (Math.random()-0.5) * 1.6 * currentSpeedMultiplier;
  
  if (slowMotionActive) { sy *= SLOW_FACTOR; sx *= SLOW_FACTOR; }
  
  meteors.push({
    x, y: -r - Math.random()*35, radius: r, speedY: sy, speedX: sx,
    color: `hsl(${15+Math.random()*25}, ${70+Math.random()*30}%, ${45+Math.random()*25}%)`,
    rotation: Math.random()*Math.PI*2, rotationSpeed: (Math.random()-0.5)*0.07
  });
}

function spawnCoin() {
  coins.push({
    x: Math.random()*(W-20)+10, y: -10-Math.random()*20, radius: 10,
    speed: 2.2+Math.random()*1.5, rotation: 0, rotationSpeed: 0.05, value: 1
  });
}

function createExplosion(px, py, colors = ['#ffaa33', '#ff6611']) {
  for (let i=0; i<16; i++) {
    let a = Math.random()*Math.PI*2, s = 2.2+Math.random()*5.5;
    particles.push({ x:px, y:py, vx:Math.cos(a)*s, vy:Math.sin(a)*s, life:1, decay:0.02, size:2.2+Math.random()*6, color:colors[i%colors.length] });
  }
}

function checkCollision(a, b) {
  return Math.hypot(a.x-b.x, a.y-b.y) < (a.radius + b.radius);
}

// ========== HANDLE HIT (ALL 15 POWER CHECKS) ==========
function handleHit(meteor) {
  if (!gameActive || gameOver) return;
  
  // ========== PHOENIX REBIRTH ==========
  if (hasPower('rebirth') && !phoenixUsed) {
    phoenixUsed = true;
    
    createExplosion(player.x, player.y, ['#ff4400', '#ffaa00', '#ff6600']);
    for (let i = 0; i < 30; i++) {
      let a = Math.random() * Math.PI * 2;
      particles.push({ 
        x: player.x, y: player.y, 
        vx: Math.cos(a) * (4 + Math.random() * 8), 
        vy: Math.sin(a) * (4 + Math.random() * 8), 
        life: 1.5, decay: 0.015, 
        size: 5 + Math.random() * 10, 
        color: ['#ff4400', '#ffaa00', '#ff6600', '#ffff00'][Math.floor(Math.random() * 4)]
      });
    }
    
    const idx = meteors.indexOf(meteor);
    if (idx > -1) meteors.splice(idx, 1);
    
    showNotification('🦅 Phoenix reborn!');
    meteors = [];
    player.x = W / 2;
    player.y = H - 70;
    mouseX = player.x;
    mouseY = player.y;
    shielded = hasPower('shield');
    return;
  }
  
  // ========== GHOST - Phase Through ==========
  if (hasPower('phaseThrough') && phaseReady && Math.random() < 0.5) {
    phaseReady = false;
    setTimeout(() => { phaseReady = true; }, 5000);
    createExplosion(meteor.x, meteor.y, ['#ccccff', '#ffffff']);
    showNotification('👻 Phased through!');
    return;
  }
  
  // ========== ROBOT - Auto Dodge ==========
  if (hasPower('autoDodge') && autoDodgeReady) {
    autoDodgeReady = false;
    setTimeout(() => { autoDodgeReady = true; }, 3000);
    player.x = Math.random() * (W - 40) + 20;
    player.y = Math.random() * (H - 200) + 100;
    createExplosion(meteor.x, meteor.y, ['#888888', '#cccccc']);
    showNotification('🤖 Auto-dodged!');
    return;
  }
  
  // ========== SHIELD ==========
  if (shielded && hasPower('shield')) {
    shielded = false;
    createExplosion(meteor.x, meteor.y, ['#00ff88', '#44ffcc']);
    meteors.splice(meteors.indexOf(meteor), 1);
    showNotification('🛡️ Shield!');
    return;
  }
  
  // ========== ICE CRYSTAL - Freeze ==========
  if (hasPower('freezeNearby') && !freezeActive) {
    freezeActive = true;
    freezeTimer = 180;
    meteors.forEach(m => {
      m.frozenSpeedY = m.speedY;
      m.frozenSpeedX = m.speedX;
      m.speedY *= 0.2;
      m.speedX *= 0.2;
    });
    createExplosion(meteor.x, meteor.y, ['#88ccff', '#aaddff']);
    showNotification('❄️ Meteors frozen!');
    return;
  }
  
  // ========== NORMAL DEATH ==========
  createExplosion(meteor.x, meteor.y);
  meteors.splice(meteors.indexOf(meteor), 1);
  gameActive = false;
  gameOver = true;
  
  if (slowMotionActive) deactivateSlowMotion();
  
  createExplosion(player.x, player.y, ['#ffaa00', '#ff8844']);
  for (let i = 0; i < 18; i++) {
    let a = Math.random() * Math.PI * 2;
    particles.push({ 
      x: player.x, y: player.y, 
      vx: Math.cos(a) * (3 + Math.random() * 7), 
      vy: Math.sin(a) * (3 + Math.random() * 7), 
      life: 1, decay: 0.02, 
      size: 4 + Math.random() * 8, 
      color: '#ff8844' 
    });
  }
  
  if (powerButton) powerButton.classList.remove('show');
  if (powerIndicator) powerIndicator.style.display = 'none';
  
  updateDeathButtons();
  updateUI();
  autoUpdateLeaderboard(score);
}

// ========== UPDATE ==========
function update() {
  stars.forEach(star => {
    star.y += star.speed;
    star.twinkle += star.twinkleSpeed;
    if (star.y > H) { star.y = 0; star.x = Math.random() * W; }
  });
  
  updateParticles();
  
  if (slowMotionActive) {
    slowMotionTimer--;
    if (slowMotionTimer <= 0) {
      deactivateSlowMotion();
      updatePowerUI();
    }
  }
  if (slowMotionCooldown > 0) {
    slowMotionCooldown--;
    if (slowMotionCooldown === 0) updatePowerUI();
  }
  if (frameCounter % 10 === 0) updatePowerUI();
  
  if (freezeActive) {
    freezeTimer--;
    if (freezeTimer <= 0) {
      freezeActive = false;
      meteors.forEach(m => {
        if (m.frozenSpeedY) {
          m.speedY = m.frozenSpeedY;
          m.speedX = m.frozenSpeedX;
          delete m.frozenSpeedY;
          delete m.frozenSpeedX;
        }
      });
    }
  }
  
  if (!gameActive || gameOver) { updateDeathButtons(); return; }
  
  const moveSpeed = hasPower('speedBoost') ? 0.35 : 0.2;
  player.x += (Math.min(Math.max(mouseX, player.radius), W-player.radius) - player.x) * moveSpeed;
  player.y += (Math.min(Math.max(mouseY, player.radius), H-player.radius) - player.y) * moveSpeed;
  
  if (hasPower('healOverTime') && frameCounter % 120 === 0) {
    addCoins(1);
  }
  
  if (hasPower('blackHole')) {
    coins.forEach(c => {
      const dx = player.x - c.x;
      const dy = player.y - c.y;
      const dist = Math.hypot(dx, dy);
      if (dist < 150 && dist > 0) {
        c.x += dx / dist * 1.5;
        c.y += dy / dist * 1.5;
      }
    });
  }
  
  currentSpeedMultiplier = Math.min(MAX_MULTIPLIER, 1.0 + Math.floor(score/5)*0.18);
  spawnDelay = Math.max(16, 38 - Math.floor(score/4)*2);
  
  if (++frameCounter % spawnDelay === 0) {
    spawnMeteor();
    if (score>10 && Math.random()<0.45) spawnMeteor();
    if (score>25 && Math.random()<0.3) spawnMeteor();
  }
  if (frameCounter % 55 === 0 && Math.random()<0.7) spawnCoin();
  
  for (let i=meteors.length-1; i>=0; i--) {
    let m = meteors[i];
    m.y += m.speedY; m.x += m.speedX; m.rotation += m.rotationSpeed;
    if (m.y-m.radius>H+45 || m.x+m.radius<-35 || m.x-m.radius>W+35) {
      meteors.splice(i,1);
      if (gameActive && !gameOver) { score++; updateUI(); }
    } else if (gameActive && !gameOver && checkCollision(player, m)) handleHit(m);
  }
  
  for (let i=coins.length-1; i>=0; i--) {
    let c = coins[i];
    c.y += c.speed; c.rotation += c.rotationSpeed;
    if (c.y-c.radius > H+30) { coins.splice(i,1); continue; }
    if (gameActive && !gameOver && checkCollision(player, c)) {
      addCoins(c.value);
      createExplosion(c.x, c.y, ['#ffd700', '#ffed4a']);
      coins.splice(i,1);
    }
  }
  updateUI();
}

function updateParticles() {
  for (let i=particles.length-1; i>=0; i--) {
    let p = particles[i];
    p.x += p.vx; p.y += p.vy;
    if ((p.life -= p.decay) <= 0) particles.splice(i,1);
  }
}

// ========== DRAWING ==========
function drawBackground() {
  const gradient = ctx.createLinearGradient(0, 0, 0, H);
  gradient.addColorStop(0, '#0a0a2e');
  gradient.addColorStop(0.5, '#1a1a3e');
  gradient.addColorStop(1, '#0d0d1a');
  ctx.fillStyle = gradient;
  ctx.fillRect(0, 0, W, H);
  
  ctx.fillStyle = 'rgba(138, 43, 226, 0.03)';
  ctx.beginPath(); ctx.arc(W*0.3, H*0.4, 120, 0, Math.PI*2); ctx.fill();
  ctx.fillStyle = 'rgba(0, 100, 255, 0.03)';
  ctx.beginPath(); ctx.arc(W*0.7, H*0.6, 100, 0, Math.PI*2); ctx.fill();
  ctx.fillStyle = 'rgba(255, 50, 100, 0.02)';
  ctx.beginPath(); ctx.arc(W*0.5, H*0.3, 80, 0, Math.PI*2); ctx.fill();
  
  stars.forEach(star => {
    const brightness = 0.3 + Math.sin(star.twinkle) * 0.4;
    ctx.fillStyle = `rgba(255, 240, 200, ${brightness})`;
    ctx.beginPath(); ctx.arc(star.x, star.y, star.size, 0, Math.PI*2); ctx.fill();
    if (star.size > 1.5) {
      ctx.fillStyle = `rgba(200, 220, 255, ${brightness * 0.3})`;
      ctx.beginPath(); ctx.arc(star.x, star.y, star.size * 2.5, 0, Math.PI*2); ctx.fill();
    }
  });
  
  if (slowMotionActive) {
    ctx.fillStyle = 'rgba(0, 100, 255, 0.15)';
    ctx.fillRect(0, 0, W, H);
    const vignette = ctx.createRadialGradient(W/2, H/2, W*0.4, W/2, H/2, W*0.9);
    vignette.addColorStop(0, 'rgba(0,0,0,0)');
    vignette.addColorStop(1, 'rgba(0,50,150,0.3)');
    ctx.fillStyle = vignette;
    ctx.fillRect(0, 0, W, H);
  }
}

function drawMeteors() {
  meteors.forEach(m => {
    ctx.save(); ctx.translate(m.x, m.y); ctx.rotate(m.rotation);
    ctx.shadowColor = slowMotionActive ? '#4488ff' : '#ff5500';
    ctx.shadowBlur = slowMotionActive ? 25 : 18;
    ctx.fillStyle = m.color;
    ctx.beginPath();
    for (let i = 0; i < 10; i++) {
      let a = (i / 10) * Math.PI * 2;
      let vr = 0.82 + Math.sin(i * 5) * 0.15 + Math.cos(i * 3) * 0.1;
      let x = Math.cos(a) * m.radius * vr;
      let y = Math.sin(a) * m.radius * vr;
      i === 0 ? ctx.moveTo(x, y) : ctx.lineTo(x, y);
    }
    ctx.closePath(); ctx.fill();
    ctx.fillStyle = '#ff8800'; ctx.shadowColor = '#ff4400'; ctx.shadowBlur = 10;
    ctx.beginPath(); ctx.arc(-m.radius*0.2, -m.radius*0.2, m.radius*0.35, 0, Math.PI*2); ctx.fill();
    ctx.fillStyle = '#331100'; ctx.shadowBlur = 0;
    ctx.beginPath(); ctx.arc(m.radius*0.35, -m.radius*0.25, m.radius*0.22, 0, Math.PI*2); ctx.fill();
    ctx.restore();
  });
}

function drawCoins() {
  coins.forEach(c => {
    ctx.save(); ctx.translate(c.x, c.y); ctx.rotate(c.rotation);
    ctx.shadowColor = '#ffd700'; ctx.shadowBlur = 15;
    ctx.fillStyle = '#ffcc00'; ctx.beginPath(); ctx.arc(0, 0, c.radius, 0, Math.PI*2); ctx.fill();
    ctx.fillStyle = '#ffed4a'; ctx.beginPath(); ctx.arc(0, 0, c.radius*0.6, 0, Math.PI*2); ctx.fill();
    ctx.font = 'bold 11px Arial'; ctx.textAlign = 'center'; ctx.fillStyle = '#b8860b'; ctx.fillText('🪙', 0, 1);
    ctx.restore();
  });
}

function drawParticles() {
  particles.forEach(p => {
    ctx.save(); ctx.globalAlpha = p.life * 0.85;
    ctx.fillStyle = p.color; ctx.shadowColor = p.color; ctx.shadowBlur = 8;
    ctx.beginPath(); ctx.arc(p.x, p.y, p.size*p.life, 0, Math.PI*2); ctx.fill();
    ctx.restore();
  });
}

function drawPlayer() {
  ctx.save(); ctx.translate(player.x, player.y);
  const skin = getSkinColors();
  
  if (shielded && hasPower('shield')) {
    ctx.beginPath(); ctx.arc(0, 0, player.radius+10, 0, Math.PI*2);
    ctx.strokeStyle = 'rgba(0, 255, 136, 0.8)'; ctx.lineWidth = 3;
    ctx.shadowColor = '#00ff88'; ctx.shadowBlur = 20; ctx.stroke();
  }
  
  if (hasPower('invisibility')) ctx.globalAlpha = 0.5;
  
  ctx.shadowColor = skin.glow; ctx.shadowBlur = 18;
  ctx.fillStyle = skin.primary; ctx.strokeStyle = 'white'; ctx.lineWidth = 2.5;
  ctx.beginPath();
  let r = player.radius;
  ctx.moveTo(0, -r*1.1); ctx.lineTo(-r*0.8, r*0.7); ctx.lineTo(-r*0.3, r*0.35);
  ctx.lineTo(-r*0.3, r*0.9); ctx.lineTo(r*0.3, r*0.9); ctx.lineTo(r*0.3, r*0.35);
  ctx.lineTo(r*0.8, r*0.7); ctx.closePath();
  ctx.fill(); ctx.stroke();
  
  ctx.fillStyle = 'white'; ctx.beginPath(); ctx.arc(0, -2, r*0.35, 0, 2*Math.PI); ctx.fill();
  ctx.shadowColor = skin.engine; ctx.shadowBlur = 15; ctx.fillStyle = skin.engine;
  ctx.beginPath(); ctx.moveTo(-5, r*0.7); ctx.lineTo(0, r*0.9+6); ctx.lineTo(5, r*0.7); ctx.fill();
  ctx.restore();
}

function drawStartMessage() {
  if (gameActive || gameOver) return;
  ctx.fillStyle = 'rgba(0,0,0,0.5)'; ctx.fillRect(0, H/2-60, W, 120);
  ctx.font = 'bold 22px "Segoe UI"'; ctx.fillStyle = 'white'; ctx.textAlign = 'center';
  ctx.fillText('🎮 MOVE TO START', W/2, H/2-10);
  ctx.font = '14px "Segoe UI"'; ctx.fillText('Best: ' + personalBest + ' | ' + (playerName || 'No name set'), W/2, H/2+30);
}

function drawGameOverOverlay() {
  if (!gameOver) return;
  ctx.fillStyle = 'rgba(0,0,0,0.7)'; ctx.fillRect(0, 0, W, H);
  ctx.font = 'bold 34px "Segoe UI"'; ctx.fillStyle = 'white'; ctx.shadowColor = '#ff4400'; ctx.shadowBlur = 22; ctx.textAlign = 'center';
  ctx.fillText('💥 GAME OVER', W/2, H/2-80);
  ctx.font = '18px "Segoe UI"'; ctx.fillText('Score: ' + score + ' | Best: ' + personalBest, W/2, H/2-40);
}

function drawCursor() {
  if (gameOver) return;
  const skin = getSkinColors();
  ctx.beginPath(); ctx.arc(mouseX, mouseY, 6, 0, Math.PI*2);
  ctx.fillStyle = skin.glow; ctx.shadowColor = skin.glow; ctx.shadowBlur = 10; ctx.fill();
  ctx.strokeStyle = 'white'; ctx.lineWidth = 2; ctx.stroke();
}

function drawAll() {
  drawBackground();
  drawMeteors();
  drawCoins();
  drawParticles();
  drawPlayer();
  drawCursor();
  drawStartMessage();
  drawGameOverOverlay();
}

// ========== EVENTS ==========
function handleMouseMove(e) {
  const rect = canvas.getBoundingClientRect();
  let cx, cy;
  if (e.touches) {
    if (!e.touches.length) return;
    cx = e.touches[0].clientX; cy = e.touches[0].clientY;
    e.preventDefault();
  } else { cx = e.clientX; cy = e.clientY; }
  mouseX = Math.min(Math.max((cx-rect.left)*(W/rect.width), player.radius), W-player.radius);
  mouseY = Math.min(Math.max((cy-rect.top)*(H/rect.height), player.radius), H-player.radius);
  if (!gameActive && !gameOver && !gameStarted) startGame();
}

function init() {
  loadLeaderboard();
  renderLeaderboard();
  
  canvas.addEventListener('mousemove', handleMouseMove);
  canvas.addEventListener('touchmove', e => { e.preventDefault(); handleMouseMove(e); }, { passive: false });
  canvas.addEventListener('touchstart', e => { e.preventDefault(); handleMouseMove(e); }, { passive: false });
  canvas.addEventListener('contextmenu', e => e.preventDefault());
  
  document.getElementById('restartButton').addEventListener('click', restartGame);
  document.getElementById('restartDeathButton').addEventListener('click', restartGame);
  document.getElementById('shopButton').addEventListener('click', openShop);
  document.getElementById('reviveButton').addEventListener('click', revivePlayer);
  
  powerButton.addEventListener('click', e => { e.preventDefault(); activateSlowMotion(); });
  powerButton.addEventListener('touchend', e => { e.preventDefault(); activateSlowMotion(); });
  
  document.addEventListener('keydown', e => {
    if (e.key === ' ' || e.code === 'Space') { e.preventDefault(); activateSlowMotion(); }
    if ((e.key === 'r' || e.key === 'R') && gameOver && totalCoins >= 30) revivePlayer();
    if (e.key === 'Enter' && nameModal.classList.contains('active')) submitScore();
  });
  
  shopModal.addEventListener('click', e => { if (e.target === shopModal) closeShop(); });
  nameModal.addEventListener('click', e => { if (e.target === nameModal) skipLeaderboard(); });
  
  window.buySkin = buySkin;
  window.closeShop = closeShop;
  window.submitScore = submitScore;
  window.skipLeaderboard = skipLeaderboard;
  
  updateCoinDisplay();
  restartGame();
  gameLoop();
}

function gameLoop() {
  update();
  drawAll();
  requestAnimationFrame(gameLoop);
}

window.addEventListener('DOMContentLoaded', init);