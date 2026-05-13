# Cara penggunaan
Lakukan embedded ke website anda seperti di bawah ini dan tambahkan di dalam body.
```bash
<script src="https://cdn.jsdelivr.net/gh/dzikrymh/cdn-softphone-widget@1.0.0/softphone-widget.js"></script>
  <script>
    // Ganti nilai di bawah untuk testing
    TradaSoftphone.init({
      extension: 'YOUR_USER_EXTENSION',
      password: 'YOUR_SECRET',
      server: 'YOUR_SERVER_WSS',
      theme: 'light'
    });
</script>
```
