Öğrenci Numarası: 250541033
Adı-Soyadı: Esmanur Yılmaz
Bu ödevi ChatGPT ile yaptım ve görüntülü zil ve kapı interkom sistemleri üzerine ödevimi yapmaya karar verdim.

// Başlangıç: sistem beklemede
WHILE system_is_on:
    
    IF doorbell_button_pressed THEN
        ActivateCamera()
        image = CaptureImage()
        SendNotificationToUser(image)
        
        response = WaitForUserResponse(timeout=30 seconds)
        
        IF response == "open_door" THEN
            UnlockDoor()
            ShowMessage("Kapı açıldı.")
        
        ELSE IF response == "deny_access" THEN
            KeepDoorLocked()
            ShowMessage("Giriş reddedildi.")
        
        ELSE IF response == "no_response" THEN
            SaveImageToLog(image)
            ShowMessage("Yanıt alınamadı, görüntü kaydedildi.")
        
        END IF

        DeactivateCamera()
    
    END IF

END WHILE
