1. Na počítači Mac přejděte na web [Azure Portal]. Klikněte na **Všechny služby** > **App Services** a pak na back-end, který jste právě vytvořili. V nastavení mobilní aplikace zvolte preferovaný jazyk:

    - Objective-C &ndash; **Rychlý start** > **iOS (Objective-C)**
    - Swift &ndash; **Rychlý start** > **iOS (Swift)**

    V části **3. Konfigurace klientské aplikace** klikněte na **Stáhnout**. Tím stáhnete dokončený projekt Xcode přednastavený k připojení k vašemu back-endu. Otevřete projekt pomocí Xcode.

1. Stisknutím tlačítka **Spustit** sestavíte projekt a spustíte aplikaci v simulátoru iOS.

1. V aplikaci zadejte smysluplný text, například *Dokončit kurz* a pak klikněte na ikonu plus (**+**). Tím odešlete do prostředí back-end v Azure, které jste předtím vytvořili, požadavek POST. Prostředí back-end vloží data z požadavku do tabulky SQL TodoItem a vrátí informace o nově uložených položkách do mobilní aplikace. Mobilní aplikace zobrazí tato data v seznamu.

   ![Aplikace Rychlý start pro iOS](./media/app-service-mobile-ios-quickstart/mobile-quickstart-startup-ios.png)

[Azure Portal]: https://portal.azure.com/
