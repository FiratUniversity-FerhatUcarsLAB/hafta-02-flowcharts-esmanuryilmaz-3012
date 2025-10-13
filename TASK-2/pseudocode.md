Öğrenci Numarası: 250541033
Adı-Soyadı: Esmanur Yılmaz

Bu ödev için Google Gemini kullandım.


// --------------------------------------------------------
// VERİ YAPILARI
// --------------------------------------------------------

// Her bir ürünün temel yapısı (Veritabanından gelir)
YAPI Urun {
    ID: TAM SAYI
    Ad: METİN
    Fiyat: ONDALIK SAYI
}

// Sepetteki her bir kalemin yapısı
YAPI SepetKalemi {
    Urun: Urun
    Miktar: TAM SAYI
    BirimIndirim: ONDALIK SAYI // Ürüne uygulanan birim başına indirim miktarı (örneğin: 5 TL)
    AraToplam: ONDALIK SAYI    // Miktar * (Urun.Fiyat - BirimIndirim)
}

// Alışveriş Sepetinin Genel Yapısı
YAPI AlisverisSepeti {
    Kalemler: LİSTE SepetKalemi
    KuponKodu: METİN            // Uygulanan kupon kodu
    GenelIndirim: ONDALIK SAYI  // Sepet Ara Toplamından düşülecek genel indirim miktarı
}

// Global Sepet Nesnesi
SEPET: AlisverisSepeti
SEPET.Kalemler = YENİ LİSTE SepetKalemi
SEPET.KuponKodu = ""
SEPET.GenelIndirim = 0.0

// --------------------------------------------------------
// YARDIMCI İŞLEVLER
// --------------------------------------------------------

// Belirli bir ürünü sepette bulur
İŞLEV SepetteBul(urunID):
    HER BİR KALEM İÇİN SEPET.Kalemler İÇİNDE:
        EĞER KALEM.Urun.ID EŞİTTİR urunID İSE
            GERİ DÖN KALEM
    GERİ DÖN BOŞ

// Sepet Kaleminin Ara Toplamını Hesaplar ve Günceller
İŞLEV GUNCELLE SepetKalemiAraToplam(kalem):
    // İndirimli Birim Fiyatı Hesapla: Fiyat - Birim Indirim
    IndirimliFiyat = kalem.Urun.Fiyat - kalem.BirimIndirim
    EĞER IndirimliFiyat < 0 İSE IndirimliFiyat = 0.0 // Fiyatın sıfırın altına düşmesini engelle
    
    // Ara Toplamı Hesapla: İndirimli Fiyat * Miktar
    kalem.AraToplam = IndirimliFiyat * kalem.Miktar
    GERİ DÖN

// SepetKalemini yeni miktar ve varsayılan (veya mevcut) indirim ile günceller
İŞLEV GUNCELLE SepetKalemi(kalem, yeniMiktar):
    kalem.Miktar = yeniMiktar
    GUNCELLE SepetKalemiAraToplam(kalem) // Ara toplamı yeniden hesapla
    GERİ DÖN

// --------------------------------------------------------
// TEMEL SEPET İŞLEVLERİ
// --------------------------------------------------------

// 1. ÜRÜNÜ SEPTE EKLEME VEYA MİKTARINI ARTIRMA
İŞLEV SepeteEkle(urunID, miktar):
    EĞER miktar <= 0 İSE GERİ DÖN "Hata: Miktar pozitif olmalıdır."
    
    KALEM = SepetteBul(urunID)
    
    EĞER KALEM BOŞ DEĞİL İSE
        // Ürün zaten sepette varsa, miktarını güncelle
        YeniMiktar = KALEM.Miktar + miktar
        GUNCELLE SepetKalemi(KALEM, YeniMiktar)
    DEĞİLSE
        // Yeni kalem oluştur
        URUN = VeritabanındanUrunGetir(urunID) // Varsayımsal veritabanı sorgusu
        EĞER URUN BOŞ DEĞİL İSE
            YENİ_KALEM = YENİ SepetKalemi(URUN, miktar)
            YENİ_KALEM.BirimIndirim = UrunIndiriminiGetir(urunID) // Ürüne özel indirimi al
            SEPET.Kalemler'E Ekle(YENİ_KALEM)
            GUNCELLE SepetKalemiAraToplam(YENİ_KALEM)
        DEĞİLSE
            GERİ DÖN "Hata: Ürün veritabanında bulunamadı."
        
    GERİ DÖN "Ürün sepete başarıyla eklendi."

// 2. ÜRÜN MİKTARINI GÜNCELLEME
İŞLEV SepetMiktarGuncelle(urunID, yeniMiktar):
    KALEM = SepetteBul(urunID)
    
    EĞER KALEM BOŞ DEĞİL İSE
        EĞER yeniMiktar <= 0 İSE
            SepettenCikar(urunID)
            GERİ DÖN "Ürün miktarı sıfırlandığı için sepetten çıkarıldı."
        DEĞİLSE
            GUNCELLE SepetKalemi(KALEM, yeniMiktar)
            GERİ DÖN "Ürün miktarı güncellendi."
    DEĞİLSE
        GERİ DÖN "Hata: Ürün sepette bulunamadı."

// 3. ÜRÜNÜ SEPETTEN ÇIKARMA
İŞLEV SepettenCikar(urunID):
    KALEM = SepetteBul(urunID)
    
    EĞER KALEM BOŞ DEĞİL İSE
        SEPET.Kalemler'DEN Kaldır(KALEM)
        GERİ DÖN "Ürün sepetten çıkarıldı."
    DEĞİLSE
        GERİ DÖN "Hata: Ürün sepette bulunamadı."

// --------------------------------------------------------
// İNDİRİM YÖNETİMİ İŞLEVLERİ
// --------------------------------------------------------

// YARDIMCI İŞLEV: Sepet Kalemlerinin Toplamını Hesaplar (Genel İndirim Düşülmeden Önce)
İŞLEV SepetKalemleriToplaminiHesapla():
    ARA_TOPLAM = 0.0
    HER BİR KALEM İÇİN SEPET.Kalemler İÇİNDE:
        ARA_TOPLAM = ARA_TOPLAM + KALEM.AraToplam // AraToplam zaten birim indirimi içerir.
    GERİ DÖN ARA_TOPLAM

// Kupon/Promosyon Uygulama
İŞLEV KuponUygula(kod):
    KUPO_BILGISI = KuponVerileriniGetir(kod) // Varsayımsal DB sorgusu
    
    EĞER KUPO_BILGISI BOŞ DEĞİL İSE
        SepetAraToplami = SepetKalemleriToplaminiHesapla()
        
        // Kuponun tipine göre indirim miktarını hesapla
        EĞER KUPO_BILGISI.Tip EŞİTTİR "Yüzde" İSE
            IndirimMiktari = SepetAraToplami * (KUPO_BILGISI.Deger / 100)
        DEĞİLSE EĞER KUPO_BILGISI.Tip EŞİTTİR "Sabit" İSE
            IndirimMiktari = KUPO_BILGISI.Deger
        DEĞİLSE
            IndirimMiktari = 0.0
        
        // Sepet nesnesini güncelle
        SEPET.KuponKodu = kod
        SEPET.GenelIndirim = IndirimMiktari
        GERİ DÖN "Kupon başarıyla uygulandı."
    DEĞİLSE
        SEPET.KuponKodu = ""
        SEPET.GenelIndirim = 0.0
        GERİ DÖN "Hata: Geçersiz Kupon Kodu."

// --------------------------------------------------------
// SONUÇ HESAPLAMA
// --------------------------------------------------------

// SEPETİN FİNAL TOPLAMINI HESAPLAMA
İŞLEV SepetToplaminiHesapla():
    
    // 1. Ürün bazlı indirimler düşülmüş Ara Toplamı al
    SepetAraToplami = SepetKalemleriToplaminiHesapla()
    
    // 2. Genel Kupon İndirimini düş
    NihaiToplam = SepetAraToplami - SEPET.GenelIndirim
    
    // 3. Negatif toplamı engelle
    EĞER NihaiToplam < 0 İSE
        NihaiToplam = 0.0
    
    // (Opsiyonel: Kargo ve KDV'yi buraya ekle)
    
    // Detaylı sonuç yapısını dön
    SONUC = YENİ YAPI {
        ToplamUrunFiyati: SepetAraToplami
        UygulananKuponIndirimi: SEPET.GenelIndirim
        OdenecekToplam: NihaiToplam
    }
    
    GERİ DÖN SONUC
