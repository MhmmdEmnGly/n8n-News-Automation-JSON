# 📰 Günlük Teknoloji Haber Özeti - Kurulum Kılavuzu

## 🎯 Sistemin Özellikleri

✅ Her sabah 08:30'da otomatik çalışır
✅ 7 farklı kaynaktan haber toplar (Webrazzi, AA, ShiftDelete, TechCrunch, The Verge, Ars Technica, MIT Tech Review)
✅ Son 24 saatteki haberleri filtreler
✅ Gemini AI ile akıllı filtreleme (Enerji, IoT, yenilikçi teknolojiler odaklı)
✅ 5-15 önemli haber seçer
✅ Profesyonel HTML email formatında gönderir
✅ gulaymuhammed41@gmail.com adresine teslim eder

---

## 🚀 Kurulum Adımları

### 1. n8n'e Workflow'u Import Et

1. n8n arayüzünde sağ üst köşeden **"Import from File"** veya **"+"** butonuna tıkla
2. `tech_news_digest_workflow.json` dosyasını seç
3. Workflow import edilecek

### 2. Gmail Credentials Ayarla

**Önemli:** Gmail gönderimi için OAuth2 authentication gerekli

1. n8n'de **Credentials** menüsüne git
2. **"+ Add Credential"** tıkla
3. **"Gmail OAuth2"** seç
4. Google Cloud Console'dan OAuth2 credentials oluştur:
   - https://console.cloud.google.com/ adresine git
   - Yeni proje oluştur veya mevcut birini seç
   - "APIs & Services" > "Credentials" git
   - "Create Credentials" > "OAuth 2.0 Client ID" seç
   - Application type: "Web application"
   - Authorized redirect URIs: n8n'in OAuth callback URL'ini ekle (genellikle `https://[n8n-domain]/rest/oauth2-credential/callback`)
   - Client ID ve Client Secret'i kopyala
5. n8n'de bu bilgileri gir ve Gmail hesabını bağla

### 3. Gemini API Key Ayarla

1. https://aistudio.google.com/app/apikey adresine git
2. "Create API Key" tıkla
3. API key'i kopyala
4. n8n'de **Credentials** > **"+ Add Credential"** > **"Google Gemini API"** seç
5. API key'i yapıştır

### 4. Workflow'da Son Ayarlar

1. Workflow'daki **"Send Gmail"** node'unu aç
2. Gmail credential'ını seç (az önce oluşturduğun)
3. **"Gemini API - Filter News"** node'unu aç
4. Gemini API credential'ını seç

### 5. Test Et!

1. Workflow'u kaydet
2. Sağ üstten **"Execute Workflow"** butonuna bas
3. İlk test için hemen çalıştır (zamanlayıcıyı beklemeden)
4. Email geldiyse kurulum başarılı! 🎉

---

## 🔧 Özelleştirme Seçenekleri

### Zaman Ayarı Değiştirme
**"Schedule Trigger"** node'unda cron expression: `30 8 * * *`
- Farklı saat: `30 9 * * *` (09:30)
- Günde 2 kez: İkinci bir Schedule Trigger ekle

### RSS Kaynakları Ekleme/Çıkarma
- Yeni RSS node ekle ve **"Merge All Feeds"** node'una bağla
- Gereksiz kaynağı sil veya devre dışı bırak

### Filtreleme Kriterlerini Değiştirme
**"Gemini API - Filter News"** node'undaki prompt'u düzenle:
```
Sen bir teknoloji haber editörüsün. [KRİTERLERİNİ BURAYA YAZ]
```

### Email Tasarımını Değiştirme
**"Format Email"** node'undaki HTML/CSS'i düzenle

### Haber Sayısını Ayarlama
- **"Filter Recent News (24h)"** node'unda `.slice(0, 30)` değerini değiştir (Gemini'ye gönderilecek max haber)
- Gemini prompt'unda "5-15 haberi seç" kısmını değiştir

---

## 🐛 Sorun Giderme

### Email gelmiyor
- Gmail credentials doğru mu kontrol et
- Google hesabında "Less secure app access" kapalı olmalı (OAuth2 kullanıyoruz)
- n8n'in internet erişimi var mı?

### Gemini hata veriyor
- API key geçerli mi kontrol et
- Gemini API quota'nız dolmuş olabilir (ücretsiz plan limitleri)
- https://aistudio.google.com/app/apikey adresinden kontrol et

### RSS feed'ler çalışmıyor
- Bazı siteler RSS'i kapatmış olabilir
- Alternatif feed URL'leri dene
- HTTP Request node ile direkt scraping yap

### Haber sayısı çok az/çok fazla
- "Filter Recent News" node'unda 24 saatlik filtre var
- Daha fazla haber için 48 saate çıkar: `48 * 60 * 60 * 1000`
- Gemini'nin seçim kriterlerini gevşet

---

## 📊 Sistem Mimarisi

```
Schedule (08:30) 
    ↓
7x RSS Feeds (Paralel)
    ↓
Merge All Feeds
    ↓
Filter Last 24h → İlk 30 haber
    ↓
Prepare for Gemini → Tek prompt'a çevir
    ↓
Gemini API → 5-15 önemli haber seç
    ↓
Parse Selection → Seçilen haberleri al
    ↓
Format HTML Email → Güzel tasarım
    ↓
Send Gmail → gulaymuhammed41@gmail.com
```

---

## 💡 İleri Seviye Özellikler

### Slack/Discord'a Gönder
- Gmail node yerine Slack/Discord webhook node ekle

### Database'e Kaydet
- Her haberi PostgreSQL/MongoDB'ye kaydet
- Tekrar eden haberleri engelle

### Sentiment Analysis
- Gemini'ye "pozitif/negatif/nötr" etiketlemesi yaptır
- Renk kodlamasıyla göster

### Özet Metinler
- Her haber için Gemini'ye 2-3 cümlelik özet yazdır

### Mobil Push Notification
- Pushover/Telegram bot ekle

---

## 📞 Destek

Sorun yaşarsan:
1. n8n execution log'larına bak
2. Her node'un output'unu tek tek kontrol et
3. Bana geri dönebilirsin, yardımcı olurum!

---

**Hayırlı olsun! 🚀**
