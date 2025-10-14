Öğrenci Numarası: 250541033
Adı-Soyadı: Esmanur Yılmaz
Bu ödev için Microsoft Copilot kullandım.

BAŞLA

HastaListesi ← boş liste
RandevuListesi ← boş liste

FONKSİYON AnaMenu()
    YAZDIR "1. Yeni Hasta Kaydı"
    YAZDIR "2. Randevu Al"
    YAZDIR "3. Randevuları Gör"
    YAZDIR "4. Randevu İptal Et"
    YAZDIR "5. Çıkış"
    SEÇİM ← GİRİŞ AL

    EĞER SEÇİM = 1 İSE
        HastaKaydi()
    EĞER SEÇİM = 2 İSE
        RandevuAl()
    EĞER SEÇİM = 3 İSE
        RandevulariGoster()
    EĞER SEÇİM = 4 İSE
        RandevuIptal()
    EĞER SEÇİM = 5 İSE
        YAZDIR "Sistemden çıkılıyor..."
        ÇIKIŞ
    DEĞİLSE
        YAZDIR "Geçersiz seçim! Lütfen tekrar deneyin."
        AnaMenu()
    SON
SON

FONKSİYON HastaKaydi()
    YAZDIR "Adınızı girin:"
    AD ← GİRİŞ AL
    YAZDIR "TC Kimlik No girin:"
    TC ← GİRİŞ AL
    HastaListesi'ne (AD, TC) EKLE
    YAZDIR "Kayıt başarıyla tamamlandı."
    AnaMenu()
SON

FONKSİYON RandevuAl()
    YAZDIR "TC Kimlik No girin:"
    TC ← GİRİŞ AL
    EĞER TC HastaListesi'nde YOKSA
        YAZDIR "Bu TC ile kayıtlı hasta bulunamadı."
        AnaMenu()
    SON

    YAZDIR "Bölüm seçin (örnek: Kardiyoloji):"
    BOLUM ← GİRİŞ AL
    YAZDIR "Tarih girin (GG/AA/YYYY):"
    TARIH ← GİRİŞ AL
    YAZDIR "Saat girin (örnek: 14:00):"
    SAAT ← GİRİŞ AL

    RandevuListesi'ne (TC, BOLUM, TARIH, SAAT) EKLE
    YAZDIR "Randevunuz başarıyla oluşturuldu."
    AnaMenu()
SON

FONKSİYON RandevulariGoster()
    YAZDIR "TC Kimlik No girin:"
    TC ← GİRİŞ AL
    YAZDIR "Randevularınız:"
    RANDEVU_SAYISI ← 0
    HER RANDEVU İÇİN RandevuListesi
        EĞER RANDEVU.TC = TC İSE
            YAZDIR RANDEVU.BOLUM + " - " + RANDEVU.TARIH + " " + RANDEVU.SAAT
            RANDEVU_SAYISI ← RANDEVU_SAYISI + 1
        SON
    SON
    EĞER RANDEVU_SAYISI = 0 İSE
        YAZDIR "Kayıtlı randevunuz bulunmamaktadır."
    SON
    AnaMenu()
SON

FONKSİYON RandevuIptal()
    YAZDIR "TC Kimlik No girin:"
    TC ← GİRİŞ AL
    YAZDIR "İptal etmek istediğiniz randevunun tarihini girin (GG/AA/YYYY):"
    TARIH ← GİRİŞ AL
    YAZDIR "Saatini girin (örnek: 14:00):"
    SAAT ← GİRİŞ AL

    RandevuBulundu ← YANLIŞ
    HER RANDEVU İÇİN RandevuListesi
        EĞER RANDEVU.TC = TC VE RANDEVU.TARIH = TARIH VE RANDEVU.SAAT = SAAT İSE
            RandevuListesi'nden RANDEVU'yu SİL
            RandevuBulundu ← DOĞRU
            YAZDIR "Randevunuz başarıyla iptal edildi."
            KIR
        SON
    SON

    EĞER RandevuBulundu = YANLIŞ İSE
        YAZDIR "Belirtilen bilgilerle eşleşen bir randevu bulunamadı."
    SON

    AnaMenu()
SON

AnaMenu()

BİTİR
