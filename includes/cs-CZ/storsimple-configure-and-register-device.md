<!--author=alkohli last changed: 12/01/15-->


#### <a name="to-configure-and-register-the-device"></a>Konfigurace a registrace zařízení
1. V konzole sériového portu zařízení StorSimple spusťte rozhraní Windows PowerShell. Další informace najdete v článku [Použití klienta PuTTY k připojení ke konzole sériového portu zařízení](#use-putty-to-connect-to-the-device-serial-console). **Postup proveďte přesně, jinak ke konzole nezískáte přístup.**
2. Ve spuštěné relaci jedním stisknutím klávesy Enter zobrazte příkazový řádek. 
3. Budete vyzváni k volbě jazyka, který chcete pro zařízení nastavit. Zadejte jazyk a stiskněte Enter. 
   
    ![Konfigurace a registrace zařízení StorSimple 1](./media/storsimple-configure-and-register-device/HCS_RegisterYourDevice1-include.png)
4. V zobrazené nabídce konzoly sériového portu se výběrem možnosti 1 přihlaste s oprávněním k úplnému přístupu. 
   
    ![Registrace zařízení StorSimple 2](./media/storsimple-configure-and-register-device/HCS_RegisterYourDevice2-include.png)
   
     Provedením kroků 5–12 konfigurujte minimální požadovaná síťová nastavení zařízení. **Tyto kroky konfigurace je nutné provést v aktivním řadiči zařízení.** Nabídka konzoly sériového portu zobrazuje stav řadiče ve zprávě v záhlaví. Pokud nejste připojeni k aktivnímu řadiči, odpojte se a potom se připojte k aktivnímu řadiči.
5. Na příkazovém řádku zadejte heslo. Výchozí heslo zařízení je **Password1**.
6. Zadejte následující příkaz:
   
     `Invoke-HcsSetupWizard` 
7. Zobrazí se průvodce instalací, který vám pomůže konfigurovat nastavení sítě pro zařízení. Zadejte následující informace: 
   
   * IP adresa síťového rozhraní DATA 0
   * maska podsítě
   * brána
   * IP adresa primárního serveru DNS
   * IP adresa primárního serveru NTP
     
     > [!NOTE]
     > Na použití nastavení masky podsítě a služby DNS může být nutné několik minut počkat. Pokud se zobrazí chybová zpráva „Zařízení není připraveno.“, zkontrolujte fyzické připojení síťového rozhraní DATA 0 aktivního řadiče k síti.
     > 
     > 
8. Volitelně konfigurujte proxy server. Konfigurace proxy serveru je volitelná, ale **můžete ji provést jenom v tomto kroku**. Další informace najdete v článku [Konfigurace webového proxy serveru pro zařízení](../articles/storsimple/storsimple-configure-web-proxy.md). Pokud se v tomto kroku setkáte s problémy, prostudujte si pokyny k řešení [chyb během konfigurace webového proxy serveru](../articles/storsimple/storsimple-troubleshoot-deployment.md#errors-during-the-optional-web-proxy-settings).

     > [!NOTE]
     > Průvodce lze kdykoliv ukončit stisknutím kombinace kláves Ctrl+C. Veškerá nastavení provedená před použitím tohoto příkazu budou zachována.

1. Platnost hesla správce zařízení z bezpečnostních důvodů vyprší po první relaci a pro následující relace je nutné heslo změnit. Až k tomu budete vyzváni, zadejte heslo správce zařízení. Platné heslo správce zařízení musí být tvořeno 8 až 15 znaky. Heslo musí obsahovat kombinaci malých písmen, velkých písmen, číslic a speciálních znaků.
2. V tomto kroku se provádí také konfigurace hesla služby StorSimple Snapshot Manager. Toto heslo se používá při ověřování zařízení u hostitele se systémem Windows, ve kterém je spuštěná služba StorSimple Snapshot Manager. Až k tomu budete vyzváni, zadejte heslo tvořené 14 až 15 znaky. Heslo musí obsahovat kombinaci tří z následujících čtyř typů znaků: malá písmena, velká písmena, číslice a speciální znaky. 
   
   ![Registrace zařízení StorSimple 4](./media/storsimple-configure-and-register-device/HCS_RegisterYourDevice4-include.png)
   
   Heslo služby StorSimple Snapshot Manager můžete resetovat pomocí rozhraní služby StorSimple Manager. Podrobné kroky najdete v článku [Změna hesel zařízení StorSimple pomocí služby StorSimple Manager](../articles/storsimple/storsimple-change-passwords.md).
   
   Pokud se v tomto kroku setkáte s problémy, prostudujte si pokyny k řešení [chyb souvisejících s hesly](../articles/storsimple/storsimple-troubleshoot-deployment.md#errors-related-to-device-administrator-and-storsimple-snapshot-manager-passwords).
3. V posledním kroku instalace zařízení průvodce registruje zařízení ve službě StorSimple Manager. K provedení tohoto kroku budete potřebovat registrační klíč služby, který jste získali v kroku 2. Po zadání registračního klíče může být nutné 2–3 minuty počkat, než se zařízení zaregistruje.
   
   Pokud potřebujete vyřešit jakékoliv chyby registrace zařízení, prostudujte si pokyny k řešení [chyb během registrace](../articles/storsimple/storsimple-troubleshoot-deployment.md#errors-during-device-registration). Podrobné pokyny k řešení potíží najdete také v [příkladu podrobného postupu řešení potíží](../articles/storsimple/storsimple-troubleshoot-deployment.md#step-by-step-storsimple-troubleshooting-example).
4. Po provedení registrace zařízení se zobrazí šifrovací klíč dat služby. Klíč zkopírujte a uložte na bezpečném místě.
   
   > [!WARNING]
   > Tento klíč bude požadován spolu s registračním klíčem služby k registraci dalších zařízení ve službě StorSimple Manager. Další informace o tomto klíči najdete v článku [Zabezpečení zařízení StorSimple](../articles/storsimple/storsimple-security.md).
   > 
   > 
   
    ![Registrace zařízení StorSimple 6](./media/storsimple-configure-and-register-device/HCS_RegisterYourDevice6-include.png)
   
    Pokud chcete zkopírovat text z okna konzoly sériového portu, jednoduše ho vyberte. Potom by mělo být možné text vložit do schránky nebo libovolného textového editoru. Ke zkopírování šifrovacího klíče dat služby NEPOUŽÍVEJTE kombinaci kláves Ctrl+C. Použití kombinace kláves Ctrl+C způsobí ukončení průvodce instalací. Hesla správce zařízení a služby StorSimple Snapshot Manager nebudou změněna a zařízení bude používat výchozí hesla.
5. Ukončete konzolu sériového portu.
6. Přejděte zpět na portál Azure Classic a proveďte následující kroky:
   
   1. Dvojitým kliknutím na službu StorSimple Manager zobrazte stránku **Rychlý start**.
   2. Klikněte na **Zobrazit připojená zařízení**.
   3. Na stránce **Zařízení** kontrolou stavu ověřte, že se zařízení úspěšně připojilo ke službě. Zařízení musí být ve stavu **Online**. Pokud je zařízení ve stavu **Offline**, počkejte několik minut, než zařízení přejde do stavu Online.
   
   ![Stránka zařízení StorSimple](./media/storsimple-configure-and-register-device/HCS_DevicesPageM-include.png) 
   
   > [!IMPORTANT]
   > Jakmile zařízení přejde do stavu Online, připojte síťové kabely, které jste odpojili na začátku tohoto kroku.
   > 
   > 

Pokud zařízení po úspěšné registraci nepřejde do stavu Online, můžete pomocí rutiny `Test-HcsmConnection -Verbose` ověřit, jestli je v pořádku síťové připojení. Podrobné informace o použití této rutiny najdete v článku [Rutina Test-HcsmConnection – reference](https://technet.microsoft.com/library/dn715782.aspx).

![Dostupné video](./media/storsimple-configure-and-register-device/Video_icon.png)**Dostupné video**

Pokud si chcete přehrát video, které ukazuje postup konfigurace a registrace zařízení pomocí rozhraní Windows PowerShell pro zařízení StorSimple, klikněte [sem](https://azure.microsoft.com/documentation/videos/initialize-the-storsimple-appliance/).

