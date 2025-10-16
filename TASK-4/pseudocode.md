Öğrenci Numarası: 250541033
Adı-Soyadı: Esmanur Yılmaz
Bu ödevi Google Gemini ile yaptım.

Harika bir fikir\! Kontenjan kontrolü, gerçek bir ders kaydı sisteminin önemli bir parçasıdır.

İstediğiniz gibi, pseudocode'u daha basitleştirip, özellikle seçmeli dersler ve hoca seçimleri kısmına bir **kontenjan kontrolü (`IF` durumu)** ekliyorum.

Bu yeni versiyonda, her hoca seçeneği için sabit bir kontenjan olacak. Eğer seçilen hocanın kontenjanı dolmuşsa, sistem kullanıcıyı uyaracak ve farklı bir hoca seçmesini isteyecektir.

```
BAŞLA

// Sabitler ve Veri Yapıları Tanımlaması
SABİTLER:
    MAX_SECIM_HAKKI_SUBE = 2
    KONTENJAN_LIMITI = 3 // Basitleştirilmiş, her seçmeli ders/hoca için maksimum öğrenci sayısı

// --------------------------------------------------------------------------------
// Veri Yapıları: Dersler ve Kontenjan Durumu
// --------------------------------------------------------------------------------

// Ana Dersler
ANA_DERSLER = [
    {Ad: "Ana Ders 1", Tipi: "Şube", Şubeler: ["A", "B"]}, // Şube dersi
    {Ad: "Ana Ders 2", Tipi: "Şube", Şubeler: ["A", "B"]}, // Şube dersi
    {Ad: "Ana Ders 3", Tipi: "Şube", Şubeler: ["A", "B"]}, // Şube dersi
    {Ad: "Ana Ders 4", Tipi: "Şube", Şubeler: ["A", "B"]}, // Şube dersi
    {Ad: "Ana Ders 5", Tipi: "Normal"},
    {Ad: "Ana Ders 6", Tipi: "Normal"}
]

// Seçmeli Dersler ve Hocaları
SECIMLI_DERSLER = [
    {Ad: "Seçmeli 1", Hocalar: ["Hoca A1", "Hoca A2", "Hoca A3", "Hoca A4"]},
    // ... (15 adet ders olduğu varsayılmıştır)
    {Ad: "Seçmeli 15", Hocalar: ["Hoca O1", "Hoca O2", "Hoca O3", "Hoca O4"]}
]

// Kontenjan Takip Mekanizması (Hoca ve Şubeye Kayıtlı Öğrenci Sayısı)
// Gerçek sistemde bir veritabanı olurdu. Burada bir Map/Sözlük kullanıyoruz.
KONTENJAN_DURUMU = YENİ_SÖZLÜK

// Başlangıçta tüm hoca/şube kontenjanlarını 0 olarak ayarla (Basitlik İçin)
Döngü her DERS için SECIMLI_DERSLER içinde:
    Döngü her HOCA için DERS.Hocalar içinde:
        KONTENJAN_DURUMU[DERS.Ad + "-" + HOCA] = 0 // Örnek: "Seçmeli 1-Hoca A1": 0
    Döngü_Sonu
Döngü_Sonu

// --------------------------------------------------------------------------------
// Kayıt Durumu
// --------------------------------------------------------------------------------
KAYIT_LİSTESİ = Boş_Dizi
Kalan_Şube_Seçim_Hakkı = MAX_SECIM_HAKKI_SUBE

EKRANA_YAZ("===== Ders Kayıt Sistemi Başladı =====")

// --------------------------------------------------------------------------------
// ADIM 1: Ana Derslerin ve Şubelerin Seçimi (Basitçe Otomatik Seçim)
// --------------------------------------------------------------------------------

EKRANA_YAZ(">> 6 Ana Ders Otomatik Olarak Kaydediliyor...")

Döngü her DERS için ANA_DERSLER içinde:
    Eğer DERS.Tipi == "Şube" İse:
        // Şube seçim hakkı kontrolü
        Eğer Kalan_Şube_Seçim_Hakkı > 0 İse:
            SEÇİLEN_ŞUBE = "A" // Örnek: İlk şubeyi seçtik
            KAYIT_LİSTESİ.Ekle({Ders: DERS.Ad, Tip: "Ana", Şube: SEÇİLEN_ŞUBE})
            Kalan_Şube_Seçim_Hakkı = Kalan_Şube_Seçim_Hakkı - 1
            EKRANA_YAZ("- " + DERS.Ad + " (Şube: " + SEÇİLEN_ŞUBE + ") seçildi. Kalan hak: " + Kalan_Şube_Seçim_Hakkı)
        Başka Durumda:
            // Şube hakkı bitti, şubesiz kaydet (ya da kaydetme, senaryoya göre değişir)
            KAYIT_LİSTESİ.Ekle({Ders: DERS.Ad, Tip: "Ana", Şube: "YOK (Hak Bitti)"})
            EKRANA_YAZ("- " + DERS.Ad + " Şube hakkı bittiği için şubesiz kaydedildi.")
    Başka Durumda:
        KAYIT_LİSTESİ.Ekle({Ders: DERS.Ad, Tip: "Ana", Şube: "Yok"})
        EKRANA_YAZ("- " + DERS.Ad + " normal ders olarak seçildi.")
Döngü_Sonu

EKRANA_YAZ(" ")

// --------------------------------------------------------------------------------
// ADIM 2: Seçmeli Derslerin ve Hocaların Seçimi (Kontenjan Kontrollü)
// --------------------------------------------------------------------------------

EKRANA_YAZ(">> Seçmeli Ders Seçimi Başladı (Kontenjan Limiti: " + KONTENJAN_LIMITI + ")")

Döngü her DERS için SECIMLI_DERSLER içinde:
    EKRANA_YAZ("--- " + DERS.Ad + " ---")
    
    Hoca_Seçimi_Başarılı = YANLIŞ
    
    // Kontenjan Kontrollü Seçim Döngüsü
    Döngü Hoca_Seçimi_Başarılı == YANLIŞ İken:
        
        EKRANA_YAZ("Mevcut Hocalar: " + DERS.Hocalar.Birleştir(", "))
        SEÇİM_ADI = KULLANICIDAN_GİRİŞ_AL("Lütfen hoca adını girin (" + DERS.Hocalar[0] + " vb.):")
        
        // 1. Kontrol: Girilen hoca listede var mı?
        Eğer SEÇİM_ADI DERS.Hocalar içinde BULUNUYOR İse:
            ANAHTAR = DERS.Ad + "-" + SEÇİM_ADI
            MEVCUT_KAYIT = KONTENJAN_DURUMU[ANAHTAR]
            
            // 2. Kontrol (IF Durumu): Kontenjan Kontrolü
            EĞER MEVCUT_KAYIT < KONTENJAN_LIMITI İSE:
                // Kontenjan müsait
                KONTENJAN_DURUMU[ANAHTAR] = KONTENJAN_DURUMU[ANAHTAR] + 1 // Öğrenciyi kaydet
                KAYIT_LİSTESİ.Ekle({Ders: DERS.Ad, Tip: "Seçmeli", Hoca: SEÇİM_ADI})
                Hoca_Seçimi_Başarılı = DOĞRU
                EKRANA_YAZ("BAŞARILI: " + SEÇİM_ADI + " hocası seçildi. Mevcut kayıt: " + KONTENJAN_DURUMU[ANAHTAR])
            
            BAŞKA DURUMDA:
                // Kontenjan Dolu
                EKRANA_YAZ("HATA: " + SEÇİM_ADI + " hocasının kontenjanı dolmuştur (" + KONTENJAN_LIMITI + "). Lütfen başka bir hoca seçiniz.")
            // IF Durumu Sonu
            
        BAŞKA DURUMDA:
            EKRANA_YAZ("HATA: Geçersiz hoca adı. Lütfen listeden bir hoca seçiniz.")

    Döngü_Sonu // Hoca_Seçimi_Başarılı Olana Kadar Döngü

Döngü_Sonu // Seçmeli Ders Döngüsü

EKRANA_YAZ(" ")

// --------------------------------------------------------------------------------
// ADIM 3: Kayıt Özeti
// --------------------------------------------------------------------------------

EKRANA_YAZ("===== Kayıt Tamamlandı =====")
EKRANA_YAZ("Toplam Kayıtlı Ders Sayısı: " + KAYIT_LİSTESİ.Uzunluk)
EKRANA_YAZ(" ")

EKRANA_YAZ(">> Detaylı Kayıt Listesi:")
Döngü her KAYIT için KAYIT_LİSTESİ içinde:
    Eğer KAYIT.Tip == "Ana" İse:
        EKRANA_YAZ("- [ANA DERS] " + KAYIT.Ders + " | Şube: " + KAYIT.Şube)
    Başka Durumda:
        EKRANA_YAZ("- [SEÇMELİ] " + KAYIT.Ders + " | Hoca: " + KAYIT.Hoca)
Döngü_Sonu

SON
```
