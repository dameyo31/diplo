# Diplomacia Otomatik Bot — Bulut (GitHub Actions) Sürümü

Bu, Tampermonkey script'i ile aynı işi yapan, ama bilgisayarın kapalıyken de GitHub'ın
ücretsiz sunucularında otomatik çalışan sürümdür. Aktif olduğu saatlerde her ~15 dakikada bir:
İş sayfasında **Çalış** butonuna basar (can doluysa), ödül penceresini kapatır, Profil
sayfasında **Bilim İnsanı**'nı **PARA** ile yükseltir (yükseltme zaten sürüyorsa dokunmaz).
Ayrıca **3 saat aktif / 1 saat pasif** döngüsüyle çalışır (Tampermonkey sürümüyle aynı program).

Google/Facebook ile giriş yaptığın için bulut ortamı otomatik giriş yapamaz. Ayrıca bu site
oturumu **cookie ile değil, tarayıcının localStorage'ı** ile tutuyor (Cookie-Editor kontrol
edilince "çerez yok" çıktı). Bu yüzden tarayıcındaki **localStorage** verisini bir kere alıp
GitHub'a güvenli şekilde (Secret olarak) kaydetmen gerekiyor.

## Kurulum Adımları

### 1) GitHub hesabı oluştur
https://github.com/signup adresinden ücretsiz bir hesap aç.

### 2) Yeni bir repo (depo) oluştur
- Sağ üstten **+ → New repository**
- İsim: `diplomacia-bot` (istediğin bir isim olabilir)
- **Public** seç (ücretsiz sınırsız Actions dakikası için önemli — kodun görünür olur ama
  Secret'ların içeriği GÖRÜNMEZ, ayrı ve şifreli tutulur)
- **Create repository**

### 3) Dosyaları yükle
Oluşturduğun repo sayfasında **"Add file" → "Upload files"** butonuna tıkla ve bu klasördeki
tüm dosyaları (klasör yapısını koruyarak) sürükle-bırak:
- `bot.js`
- `package.json`
- `.gitignore`
- `.github/workflows/bot.yml`  (bu iç içe klasörle birlikte yüklenmeli — dosyaları
  sürükleyip bıraktığında GitHub klasör yapısını otomatik algılar)

Alt kısımda **Commit changes** butonuna basarak kaydet.

### 4) Oturum bilgisini (localStorage) al
1. Chrome'da `diplomacia.com.tr` sitesine git (giriş yapmış halde).
2. **F12** ile geliştirici araçlarını aç, **Konsol (Console)** sekmesine geç.
3. Şunu yazıp Enter'a bas:
   ```js
   copy(JSON.stringify(localStorage))
   ```
4. Konsolda `undefined` yazması normaldir — veri sessizce panona kopyalanmıştır.

### 5) Panodakini GitHub Secret olarak ekle
1. Repo sayfasında **Settings → Secrets and variables → Actions**
2. **New repository secret**
3. İsim: `DIPLOMACIA_STORAGE`
4. Değer: panonu yapıştır (Ctrl+V)
5. **Add secret**

### 6) Test et
1. Repo sayfasında **Actions** sekmesine git
2. Soldan **"Diplomacia Otomatik Bot"** iş akışını seç
3. Sağdan **"Run workflow"** ile elle bir kere çalıştır
4. Birkaç dakika sonra çalışan işin üzerine tıklayıp **"calistir"** adımının loglarını aç —
   `[DiploBot]` ile başlayan satırları görmelisin. "UYARI: Oturum bilgisi geçersiz..." yazıyorsa
   4-5. adımları tekrarlayıp Secret'ı güncellemen gerekir. Ayrıca o an **pasif saatteysek**
   (3s/1s programı) bot "PASİF saatteyiz" diyip hiçbir şey yapmadan çıkar — bu normaldir,
   test için `.github/workflows/bot.yml`'daki programı geçici kapatabilirsin (aşağıya bak).

Sorun yoksa bot artık kendi kendine, aktif olduğu saatlerde her ~15 dakikada bir otomatik
çalışacak — bilgisayarın kapalı olsa bile.

## Notlar / Sınırlamalar

- **Oturum bilgisi süresi dolabilir** (siteye göre günler/haftalar sürebilir). Bot "giriş ekranı
  görünüyor" uyarısı verirse 4-5. adımları tekrarlayıp Secret'ı güncelle.
- **Zamanlama tam dakikasında olmayabilir** — GitHub Actions'ın ücretsiz zamanlanmış
  görevleri birkaç dakika gecikebilir, bu normaldir.
- Bot; hap/DOLDUR, Premium/satın alma, İPTAL ET gibi hiçbir şeye kesinlikle dokunmaz
  (Tampermonkey sürümüyle aynı güvenlik kuralları).
- Çalışma saatleri programını değiştirmek/kapatmak istersen `bot.js` içindeki
  `AKTIF_SURE_MS` / `PASIF_SURE_MS` satırlarını düzenle (ya da `botAktifMi()` fonksiyonunu
  test için geçici olarak `return true;` yap).
- Sıklığı değiştirmek istersen `.github/workflows/bot.yml` içindeki
  `cron: '*/15 * * * *'` satırındaki `15` sayısını değiştirebilirsin (örn. `*/10` = 10 dk).
