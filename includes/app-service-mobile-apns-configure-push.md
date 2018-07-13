

1. Na počítači Mac spusťte **přístup do řetězce klíčů**. V levém navigačním panelu v části **kategorie**, otevřete **mé certifikáty**. Vyhledejte certifikát protokolu SSL, který jste stáhli v předchozí části a potom zveřejnit svůj obsah. Vyberte jenom ten certifikát (nesmí být zvolen privátní klíč). Potom [ho exportovat](https://support.apple.com/kb/PH20122?locale=en_US).
2. V [webu Azure portal](https://portal.azure.com/)vyberte **Procházet vše** > **App Services**. Potom vyberte Mobile Apps back-endu. 
3. V části **nastavení**vyberte **App Service Push**. Vyberte název vašeho centra oznámení. 
3. Přejděte na **Apple Push Notification Services** > **nahrát certifikát**. Nahrajte soubor .p12, výběrem správné **režimu** (v závislosti na tom, zda certifikát klienta SSL z předchozí je produkční nebo izolovaného prostoru). Uložte změny.

Vaše služba je nyní nakonfigurováno pro práci s nabízenými oznámeními v systému iOS.

[1]: ./media/app-service-mobile-apns-configure-push/mobile-push-notification-hub.png
