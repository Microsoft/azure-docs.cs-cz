
1. Navštivte [Azure Portal]. Klikněte na **Procházet vše** > **Mobilní aplikace** > prostředí back-end, které jste právě vytvořili. V nastavení mobilní aplikace klikněte na **Rychlý start** > **Android**. V části **Configure your client application** (Konfigurace klientské aplikace) klikněte na **Stáhnout**. Tím stáhnete dokončený projekt Android pro aplikaci přednastavenou k připojení k vašemu prostředí back-end. 
2. Otevřete projekt v **Android Studiu** pomocí možnosti **Importovat projekt (Eclipse ADT, Gradle atd.)**. Nezapomeňte vybrat tuto možnost importu, abyste se vyhnuli případným chybám JDK.
3. Stisknutím tlačítka **Spustit aplikaci** sestavíte projekt a spustíte aplikaci v simulátoru Androidu.
4. V aplikaci zadejte smysluplný text, třeba *Dokončit kurz*, a klikněte na tlačítko Přidat. Tím odešlete do prostředí back-end v Azure, které jste předtím vytvořili, požadavek POST. Back-end vloží data z požadavku do tabulky SQL TodoItem a vrátí informace o nově uložených položkách do mobilní aplikace. Mobilní aplikace zobrazí tato data v seznamu. 
   
    ![](./media/app-service-mobile-android-quickstart/mobile-quickstart-startup-android.png)

[Azure Portal]: https://portal.azure.com/
