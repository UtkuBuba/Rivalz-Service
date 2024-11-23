# Rivalz Node Systemd Servis Dosyası ve Hata Çözümleri

Bu rehber, **Rivalz node**'un systemd servis dosyası olarak nasıl çalıştırılacağını ve oluşabilecek sorunların nasıl çözüleceğini adım adım açıklar.

---

## **1. Rivalz Komutunun Yolunu Doğrulama**

Servis dosyasını doğru yapılandırabilmek için Rivalz komutunun tam yolunu bulun:

```bash
which rivalz
```

Bu komut, Rivalz binary dosyasının tam yolunu verecektir. Çıktıyı not edin ve `ExecStart` satırında kullanın. Örneğin, eğer Rivalz komutunun tam yolu şu şekilde ise:
```plaintext
/root/.nvm/versions/node/v20.5.0/bin/rivalz
```
Bu yolu aşağıdaki `ExecStart` satırında kullanabilirsiniz.

---

## **2. Servis Dosyasını Oluşturma**

Servis dosyasını `/etc/systemd/system/rivalz.service` olarak oluşturun:
```bash
sudo nano /etc/systemd/system/rivalz.service
```

---

## **3. Servis Dosyasını Düzenleme**

Aşağıdaki içeriği servis dosyasına yapıştırın:

```ini
[Unit]
Description=Rivalz Node
After=network.target

[Service]
Type=simple
User=root
WorkingDirectory=/root
ExecStart=/root/.nvm/versions/node/v20.5.0/bin/rivalz run
Restart=always
RestartSec=5
Environment=PATH=/root/.nvm/versions/node/v20.5.0/bin:/usr/bin:/bin
Environment=NODE_ENV=production

[Install]
WantedBy=multi-user.target
```

### Açıklamalar:
- **`ExecStart`**: Rivalz komutunun tam yolunu ve çalıştırılacak komutu içerir (`run`).
- **`Environment=PATH`**: NVM'nin kullandığı `PATH` değişkenini ayarlar, böylece `node` veya `npm` gibi komutlar çalışabilir.
- **`NODE_ENV=production`**: Çalışma ortamını `production` olarak ayarlar.

---

## **4. Servisi Etkinleştirme ve Başlatma**

Servisi etkinleştirmek ve başlatmak için şu komutları kullanın:
```bash
sudo systemctl daemon-reload
sudo systemctl enable rivalz.service
sudo systemctl start rivalz.service
```

---

## **5. Servis Durumunu Kontrol Etme**

Servisin düzgün çalışıp çalışmadığını kontrol etmek için:
```bash
sudo systemctl status rivalz.service
```

Eğer çıktı şu şekildeyse, servis düzgün çalışıyordur:
```plaintext
● rivalz.service - Rivalz Node
   Loaded: loaded (/etc/systemd/system/rivalz.service; enabled)
   Active: active (running) since ...
```

---

## **6. Logları Görüntüleme**

Servisin loglarını görmek için:
```bash
journalctl -u rivalz.service
```

---

## **7. Servisi Durdurma veya Yeniden Başlatma**

Servisi durdurmak için:
```bash
sudo systemctl stop rivalz.service
```

Servisi yeniden başlatmak için:
```bash
sudo systemctl restart rivalz.service
```

---

## **Notlar**

1. **NVM Kullanımı:** PATH değişkeninin doğru ayarlandığından emin olun. Yanlış bir `PATH` ayarı, Rivalz'ın çalışmasını engelleyebilir.
2. **Hata Durumunda:** Eğer servis başlamazsa logları kontrol edin:
   ```bash
   journalctl -u rivalz.service
   ```

3. **Otomatik Başlatma:** Bu ayarlama sayesinde, Rivalz node sunucu yeniden başlatıldığında otomatik olarak çalışır.

---

Herhangi bir sorunla karşılaşırsanız log çıktısını paylaşarak yardım alabilirsiniz.
