# JaskaSoska
// review-integration.js — barcha API'larni birlashtiruvchi mini-loyiha namunasi
// "Offlayn eslatmalar + status indikatori" ilovasi

// 1. IndexedDB — eslatmalarni saqlash
async function saveNoteLocally(note) {
  const db = await openNotesDB();
  const tx = db.transaction('notes', 'readwrite');
  tx.objectStore('notes').add(note);
  return new Promise((resolve) => (tx.oncomplete = resolve));
}

function openNotesDB() {
  return new Promise((resolve) => {
    const request = indexedDB.open('ReviewNotesDB', 1);
    request.onupgradeneeded = (e) => {
      e.target.result.createObjectStore('notes', {
        keyPath: 'id',
        autoIncrement: true,
      });
    };
    request.onsuccess = (e) => resolve(e.target.result);
  });
}

// 2. Canvas — status indikatorini chizish (online/offline)
function drawStatusIndicator(canvas, isOnline) {
  const ctx = canvas.getContext('2d');
  ctx.clearRect(0, 0, canvas.width, canvas.height);
  ctx.beginPath();
  ctx.arc(15, 15, 10, 0, Math.PI * 2);
  ctx.fillStyle = isOnline ? '#10b981' : '#ef4444';
  ctx.fill();
}

// 3. WebSocket — online holatda real vaqt sinxronizatsiya
let socket = null;

function connectSync() {
  socket = new WebSocket('wss://sync.example.com/notes');
  socket.onopen = () => drawStatusIndicator(statusCanvas, true);
  socket.onclose = () => drawStatusIndicator(statusCanvas, false);
  socket.onmessage = (event) => {
    const remoteNote = JSON.parse(event.data);
    saveNoteLocally(remoteNote);
  };
}

// 4. File API — eslatmaga rasm biriktirish
function attachImageToNote(file, noteId) {
  const reader = new FileReader();
  reader.onload = async (event) => {
    const db = await openNotesDB();
    const tx = db.transaction('notes', 'readwrite');
    const store = tx.objectStore('notes');
    const getRequest = store.get(noteId);
    getRequest.onsuccess = () => {
      const note = getRequest.result;
      note.image = event.target.result;
      store.put(note);
    };
  };
  reader.readAsDataURL(file);
}

// 5. Service Worker — offlayn ishlashni ta'minlash
if ('serviceWorker' in navigator) {
  navigator.serviceWorker.register('/sw.js');
}

// 6. Umumiy oqim: online bo'lsa WebSocket, bo'lmasa faqat IndexedDB
window.addEventListener('online', connectSync);
window.addEventListener('offline', () => {
  if (socket) socket.close();
  drawStatusIndicator(statusCanvas, false);
});

// Boshlang'ich holatni tekshirish
const statusCanvas = document.getElementById('statusCanvas');
if (navigator.onLine) {
  connectSync();
} else {
  drawStatusIndicator(statusCanvas, false);
}
