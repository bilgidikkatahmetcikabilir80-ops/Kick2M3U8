# Kick2M3U8
Wispbyte ile çalışan Node.js tabanlı Kick yayınını m3u8'e çevirmeye yarayan araç.

Kendi Kick yayınınızı (veya izinli bir HLS kaynağını) M3U / M3U8 bağlantısı olarak sunan, en fazla **5 kanallı**, hafif bir Node.js proxy ve yönetim paneli. Wispbyte ücretsiz planı gibi düşük kaynaklı sunucular için tasarlandı.

## Özellikler
- 5 kanala kadar M3U8 proxy (`/channel/1.m3u8` … `/channel/5.m3u8`) ve `/playlist.m3u` listesi
- Koyu temalı, mobil uyumlu Türkçe yönetim paneli (bağlantı kopyalama, durum kontrolü, ayarlar)
- İsteğe bağlı FFmpeg HLS paketleme (`-c copy`, yeniden kodlama yok); FFmpeg yoksa proxy modu çalışır
- Kendi Kick kanalınız için isteğe bağlı otomatik oynatma adresi çözümleme
- İsteğe bağlı parola ve izleme token'ı
- Sıfır bağımlılık, Node.js 20+ (19'da da çalışır)

## Kurulum
1. Dosyaları sunucunun ana dizinine yükleyin (`server.js` ana dizinde olmalı).
2. Ortam değişkenlerini girin veya `.env.example` dosyasını `.env` olarak kopyalayıp doldurun.
3. Başlatın: `npm start`

## Ortam değişkenleri
| Değişken | Açıklama |
|---|---|
| `PORT` | Dinlenecek port (Wispbyte'ta panelde yazan port) |
| `PUBLIC_BASE_URL` | Sitenizin tam adresi, sonunda `/` olmadan |
| `ADMIN_PASSWORD` | Panel parolası. **Boşsa panel parolasız açılır** |
| `STREAM_TOKEN` | Doluysa izleme bağlantılarına `?token=` zorunlu olur |
| `CHANNEL_n_NAME` | Kanal adı (n = 1–5) |
| `CHANNEL_n_USER` | Kick kullanıcı adı |
| `CHANNEL_n_SOURCE_URL` | Gerçek HLS (.m3u8) kaynak adresi |
| `CHANNEL_n_MODE` | `proxy` (varsayılan) veya `ffmpeg` |
| `CHANNEL_n_RESOLVE` | `kick` ise ve kaynak boşsa kendi kanalınızın adresi çözülür |

Gelişmiş: `FFMPEG_PATH`, `FFMPEG_MAX_PROCS`, `HLS_SEGMENT_SECONDS`, `HLS_LIST_SIZE`, `MAX_RSS_MB`, `MAX_UPSTREAM_CONNECTIONS`, `SEGMENT_CACHE_MB`, `CHECK_INTERVAL_SEC`, `TRUST_PROXY`.

## Uç noktalar
`/` panel · `/login` · `/playlist.m3u` · `/channel/1..5.m3u8` · `/api/health` · `/api/status` · `/api/channels`

## Güvenlik
- Açık proxy yok: yalnızca yapılandırılmış kaynaklar ve onların playlist'lerinde geçen adresler getirilir.
- SSRF koruması: özel ağ adreslerine istek gönderilmez.
- Kaynak URL'leri API yanıtlarında gösterilmez. Oturum çerezi HttpOnly + SameSite, CSRF ve giriş deneme sınırı vardır.
- `.env` dosyasını asla Git'e eklemeyin.

## Sınırlar
- Kick erişim kontrolleri veya DRM aşılmaz. Yalnızca **sahibi olduğunuz veya izin aldığınız** yayınlarda kullanın.
- `RESOLVE=kick` Kick'in herkese açık kanal bilgisine dayanır; Kick reddederse veya değiştirirse çalışmayabilir.
- 5–20 eşzamanlı izleyici garanti değildir. Çıkış ≈ izleyici × bitrate (ör. 4 Mbps × 10 izleyici ≈ 40 Mbps).
- Ücretsiz planda 5 FFmpeg sürecinin aynı anda çalışması garanti edilmez.

## Test
```
npm test
```

## Lisans
MIT
