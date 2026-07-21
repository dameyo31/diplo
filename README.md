# Diplomacia Otomatik Bot — Bulut (GitHub Actions) Sürümü

Bu, Tampermonkey script'i ile aynı işi yapan, ama bilgisayarın kapalıyken de GitHub'ın
ücretsiz sunucularında otomatik çalışan sürümdür. Her ~15 dakikada bir:
İş sayfasında **Çalış** butonuna basar (can doluysa), ödül penceresini kapatır, Profil
sayfasında **Bilim İnsanı**'nı **PARA** ile yükseltir (yükseltme zaten sürüyorsa dokunmaz).

Google/Facebook ile giriş yaptığın için bulut ortamı otomatik giriş yapamaz — bunun yerine
tarayıcındaki **oturum çerezlerini (cookies)** kullanır. Bu yüzden önce çerezlerini bir kere
alıp GitHub'a güvenli şekilde (Secret olarak) kaydetmen gerekiyor.

## Kurulum Adımları

### 1) GitHub hesabı oluştur
https://github.com/signup adresinden ücretsiz bir hesap aç.

### 2) Yeni bir repo (depo) oluştur
- Sağ üstten **+ → New repository**
- İsim: `diplomacia-bot` (istediğin bir isim olabilir)
- **Public** seç (ücretsiz sınırsız Actions dakikası için önemli — kodun görünür olur ama
  çerezlerin/şifrelerin GÖRÜNMEZ, onlar ayrı ve şifreli tutulur)
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

### 4) Çerezlerini al
Bilgisayarında Chrome'da:
1. **Cookie-Editor** eklentisini kur: https://chromewebstore.google.com/detail/cookie-editor/hlkenndednhfkekhgcdicdfddnkalmdm
2. `diplomacia.com.tr` sitesine git (giriş yapmış halde).
3. Cookie-Editor ikonuna tıkla → **Export** → **Export as JSON** (panoya kopyalanır).

### 5) Çerezleri GitHub Secret olarak ekle
1. Repo sayfasında **Settings → Secrets and variables → Actions**
2. **New repository secret**
3. İsim: `DIPLOMACIA_COOKIES`
4. Değer: az önce kopyaladığın JSON'u yapıştır
5. **Add secret**

### 6) Test et
1. Repo sayfasında **Actions** sekmesine git
2. Soldan **"Diplomacia Otomatik Bot"** iş akışını seç
3. Sağdan **"Run workflow"** ile elle bir kere çalıştır
4. Birkaç dakika sonra çalışan işin üzerine tıklayıp **"calistir"** adımının loglarını aç —
   `[DiploBot]` ile başlayan satırları görmelisin. "UYARI: Çerezler geçersiz..." yazıyorsa
   4. adımı tekrarlayıp Secret'ı güncellemen gerekir.

Sorun yoksa bot artık kendi kendine her 15 dakikada bir otomatik çalışacak — bilgisayarın
kapalı olsa bile.

## Notlar / Sınırlamalar

- **Çerezler süresi dolabilir** (siteye göre günler/haftalar sürebilir). Bot "giriş ekranı
  görünüyor" uyarısı verirse 4-5. adımları tekrarlayıp Secret'ı güncelle.
- **Zamanlama tam dakikasında olmayabilir** — GitHub Actions'ın ücretsiz zamanlanmış
  görevleri birkaç dakika gecikebilir, bu normaldir.
- Bot; hap/DOLDUR, Premium/satın alma, İPTAL ET gibi hiçbir şeye kesinlikle dokunmaz
  (Tampermonkey sürümüyle aynı güvenlik kuralları).
- Sıklığı değiştirmek istersen `.github/workflows/bot.yml` içindeki
  `cron: '*/15 * * * *'` satırındaki `15` sayısını değiştirebilirsin (örn. `*/10` = 10 dk).
