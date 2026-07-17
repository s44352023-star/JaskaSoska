# JaskaSoska
Service Worker nima?
Service Worker — veb sahifadan mustaqil ravishda brauzer fonida ishlaydigan JavaScript skripti. U sahifa va tarmoq o'rtasida "proksi" vazifasini bajaradi — tarmoq so'rovlarini ushlab qolishi, keshlashi va hatto internet aloqasi yo'q bo'lganda ham javob berishi mumkin.

Service Worker hayot aylanishi
Register — navigator.serviceWorker.register('/sw.js') orqali ro'yxatdan o'tkaziladi
Install — birinchi marta o'rnatilganda, odatda kerakli fayllar keshlanadi
Activate — eski keshlar tozalanadi, yangi Service Worker faollashadi
Fetch — har bir tarmoq so'rovini ushlab, keshdan yoki tarmoqdan javob beradi
PWA (Progressive Web App) nima?
PWA — Service Worker, Web App Manifest va HTTPS asosida qurilgan veb ilova bo'lib, u o'rnatiladigan (installable), offlayn ishlaydigan va native ilovaga o'xshash tajriba beradi.

PWA uchun kerakli komponentlar
Komponent	Vazifasi
manifest.json	Ilova nomi, ikonkasi, rangi haqida ma'lumot
Service Worker	Offlayn ishlash va keshlash
HTTPS	Xavfsizlik talabi (localhost bundan mustasno)
Keshlash strategiyalari
Eng mashhur strategiyalar: Cache First (avval keshdan, bo'lmasa tarmoqdan), Network First (avval tarmoqdan, bo'lmasa keshdan) va Stale-While-Revalidate (keshdan darhol javob, fonda yangilash).

Service Worker va PWA oqimi
Ha

Yo'q

sw.js register qilinadi

install: fayllar keshlanadi

activate: eski kesh tozalanadi

fetch: har bir so'rov ushlab qolinadi

Internet bormi?

Tarmoqdan javob, keshga saqlanadi

Keshdan javob beriladi - offlayn ishlaydi

PWA'lar foydalanuvchiga tezkor yuklanish, bosh ekranga o'rnatish va internet bo'lmaganda ham ishlash imkonini beradi — bu native ilova va veb sahifa o'rtasidagi ko'prikdir.

💻
Код
Kod namunasi
#2
code
 Копировать
// sw.js — Service Worker fayli (root papkada joylashadi)
const CACHE_NAME = 'my-pwa-cache-v1';
const URLS_TO_CACHE = [
  '/',
  '/index.html',
  '/styles.css',
  '/app.js',
  '/offline.html',
];

// 1. INSTALL — kerakli fayllarni keshlash
self.addEventListener('install', (event) => {
  console.log('Service Worker: install');
  event.waitUntil(
    caches.open(CACHE_NAME).then((cache) => {
      return cache.addAll(URLS_TO_CACHE);
    })
  );
  self.skipWaiting();
});

// 2. ACTIVATE — eski keshlarni tozalash
self.addEventListener('activate', (event) => {
  console.log('Service Worker: activate');
  event.waitUntil(
    caches.keys().then((cacheNames) => {
      return Promise.all(
        cacheNames
          .filter((name) => name !== CACHE_NAME)
          .map((name) => caches.delete(name))
      );
    })
  );
  self.clients.claim();
});

// 3. FETCH — Cache First strategiyasi
self.addEventListener('fetch', (event) => {
  event.respondWith(
    caches.match(event.request).then((cachedResponse) => {
      if (cachedResponse) {
        return cachedResponse;
      }

      return fetch(event.request)
        .then((networkResponse) => {
          return caches.open(CACHE_NAME).then((cache) => {
            cache.put(event.request, networkResponse.clone());
            return networkResponse;
          });
        })
        .catch(() => {
          // Internet yo'q va keshda ham topilmasa — offlayn sahifa
          return caches.match('/offline.html');
        });
    })
  );
});

// app.js — Service Worker'ni ro'yxatdan o'tkazish (asosiy sahifada)
if ('serviceWorker' in navigator) {
  window.addEventListener('load', () => {
    navigator.serviceWorker
      .register('/sw.js')
      .then((registration) => {
        console.log('SW ro\'yxatdan o\'tdi:', registration.scope);
      })
      .catch((error) => {
        console.error('SW ro\'yxatdan o\'tishda xato:', error);
      });
  });
}

// manifest.json — PWA uchun manifest fayli namunasi
// {
//   "name": "Mening PWA Ilovam",
//   "short_name": "MyPWA",
//   "start_url": "/",
//   "display": "standalone",
//   "background_color": "#ffffff",
//   "theme_color": "#8b5cf6",
//   "icons": [
//     { "src": "/icon-192.png", "sizes": "192x192", "type": "image/png" },
//     { "src": "/icon-512.png", "sizes": "512x512", "type": "image/png" }
//   ]
// }
