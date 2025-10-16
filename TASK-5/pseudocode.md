Öğrenci Numarası: 250541033
Adı-Soyadı: Esmanur Yılmaz
Bu ödevi ChatGPT ile yaptım ve görüntülü zil ve kapı interkom sistemleri üzerine ödevimi yapmaya karar verdim.

BAŞLA

KAMERA, MİKROFON, HOPARLÖR, ZİL_DÜĞMESİ, UYGULAMA, AKILLI_KİLİT, VERİTABANI bağlantılarını başlat
GECE_MODU_SENSÖRÜ, HAREKET_SENSÖRÜ, IŞIKLAR etkinleştir

SÜREKLİ OLARAK:
    // 1. Hareket Algılama
    EĞER HAREKET_SENSÖRÜ hareket algılarsa
        KAMERA kayda başla
        GÖRÜNTÜYÜ analiz et
        TANINAN_YÜZ = YÜZ_TANIMA(GÖRÜNTÜ)
        
        EĞER TANINAN_YÜZ veritabanında kayıtlıysa
            UYGULAMA'ya bildir: "Tanıdık kişi kapıda: [İsim]"
        DEĞİLSE
            UYGULAMA'ya bildir: "Bilinmeyen kişi kapıda!"
        SON
    SON

    // 2. Zil Basıldığında
    EĞER ZİL_DÜĞMESİ basıldıysa
        KAMERA ile anlık görüntü al
        MİKROFON ve HOPARLÖR üzerinden iletişimi başlat
        UYGULAMA'ya canlı görüntü gönder
        
        EĞER GECE_MODU_SENSÖRÜ “karanlık” algılarsa
            IŞIKLAR’ı aç
        SON

        EĞER kullanıcı UYGULAMA üzerinden "kapıyı aç" komutu gönderirse
            AKILLI_KİLİT’i aç
            UYGULAMA’ya bildir: "Kapı açıldı."
        DEĞİLSE
            AKILLI_KİLİT kapalı kalsın
        SON

        KAMERA kaydını buluta kaydet
    SON

    // 3. Güvenlik Bildirimleri
    EĞER art arda 3 kez bilinmeyen yüz algılanırsa
        UYGULAMA’ya acil güvenlik uyarısı gönder
        KAMERA kaydını “yüksek öncelikli” olarak işaretle
    SON

SON_SÜREKLİ

BİTİR
