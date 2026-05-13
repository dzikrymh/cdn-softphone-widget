# Softphone Widget

Widget embeddable untuk menampilkan panel softphone di website (muncul di kanan bawah), lengkap dengan:
- status SIP (REGISTER)
- outgoing call dari host page
- incoming call popup (Answer / Decline)

## Dokumentasi untuk Client (Website Host)
Bagian ini menjelaskan **cara pemakaian** dan **fungsi yang tersedia** saat widget sudah di-embed.

### File yang dibutuhkan
Client hanya perlu **1 file** hasil build:
- `softphone-widget.js`

File ini sudah berisi CSS widget (di-inject ke Shadow DOM), jadi **tidak perlu** menyertakan file CSS terpisah.

### Cara embed
1) Host `softphone-widget.js` di server/hosting client (atau CDN)
2) Sertakan script di HTML
3) Panggil `SoftphoneWidget.init(config)`

Ada 2 opsi url yang disediakan dan bisa di embed
- https://cdn.jsdelivr.net/gh/dzikrymh/cdn-softphone-widget@2.0.0/softphone-widget.js
- https://cdn.jsdelivr.net/gh/dzikrymh/cdn-softphone-widget@2.0.0/softphone-widget.min.js

Contoh:

```html
<script src="https://cdn.jsdelivr.net/gh/dzikrymh/cdn-softphone-widget@2.0.0/softphone-widget.min.js"></script>
<script>
  SoftphoneWidget.init({
    extension: 'YOUR_USER',
    password: 'YOUR_SECRET',
    server: 'YOUR_SERVER_WSS',
    theme: 'light'
  });
</script>
```

### Konfigurasi (`init(config)`)
Wajib:
- `extension` (string)
- `password` (string)
- `server` (string) — endpoint SIP over WebSocket (umumnya **WSS**)

Opsional:
- `theme`: `'dark' | 'light'`

Catatan validasi:
- Jika field wajib kosong, `init` akan throw error.
- Jika `theme` diisi selain `dark/light`, `init` akan throw error.

### Fungsi yang tersedia (Global API)
Widget build diexpose sebagai global `SoftphoneWidget` (format IIFE), dengan fungsi:

- `SoftphoneWidget.init(config)`
  - Fungsi untuk mount widget (pojok kanan bawah).
  - Aman dipanggil sebelum DOM siap: akan menunggu `DOMContentLoaded`.
  - Jika dipanggil lagi, widget akan di-render ulang dengan config terbaru.

- `SoftphoneWidget.call(target: string): Promise<void>`
  - Trigger outgoing call dari host page.
  - Akan throw error jika:
    - `target` kosong
    - widget belum siap
    - belum terkoneksi/REGISTER (status online)

- `SoftphoneWidget.hangup(): Promise<void>`
  - Mengakhiri panggilan aktif.
  - Akan throw error jika widget belum siap.

### Contoh pemakaian (dengan error handling)
Karena `call()` / `hangup()` berbasis Promise dan bisa melempar error, sebaiknya dipanggil dengan `await` + `try/catch`.

```html
<button id="btn-call">Call 1002</button>
<button id="btn-hangup">Hangup</button>

<script src="https://cdn.jsdelivr.net/gh/dzikrymh/cdn-softphone-widget@2.0.0/softphone-widget.min.js"></script>
<script>
  SoftphoneWidget.init({
    extension: 'YOUR_USER',
    password: 'YOUR_SECRET',
    server: 'YOUR_SERVER_WSS',
    theme: 'light'
  });

  document.getElementById('btn-call').addEventListener('click', async () => {
    try {
      await SoftphoneWidget.call('1002');
    } catch (e) {
      console.error(e);
      alert(String(e && e.message ? e.message : e));
    }
  });

  document.getElementById('btn-hangup').addEventListener('click', async () => {
    try {
      await SoftphoneWidget.hangup();
    } catch (e) {
      console.error(e);
      alert(String(e && e.message ? e.message : e));
    }
  });
</script>
```

### Catatan integrasi
- Widget akan membuat container `<div id="softphone-widget">` di `document.body` dan memasang UI di Shadow DOM.
- Outgoing call hanya bisa dilakukan saat status SIP sudah online/REGISTER (ditampilkan di UI widget). Saat belum online, `SoftphoneWidget.call()` akan error.
- Audio ringtone incoming bisa tidak bunyi jika browser memblokir autoplay. Biasanya perlu minimal 1 interaksi user (klik) di halaman.
- Untuk akses mikrofon dan WebRTC yang stabil, umumnya disarankan menjalankan website host via HTTPS (terutama di environment produksi).
