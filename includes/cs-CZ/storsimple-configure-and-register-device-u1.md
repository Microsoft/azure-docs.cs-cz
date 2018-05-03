<!--author=alkohli last changed: 02/22/2016-->


### <a name="to-configure-and-register-the-device"></a>Konfigurace a registrace zařízení
1. V konzole sériového portu zařízení StorSimple spusťte rozhraní Windows PowerShell. Další informace najdete v článku [Použití klienta PuTTY k připojení ke konzole sériového portu zařízení](#use-putty-to-connect-to-the-device-serial-console). **Postup proveďte přesně, jinak ke konzole nezískáte přístup.**
2. Ve spuštěné relaci jedním stisknutím klávesy Enter zobrazte příkazový řádek. 
3. Budete vyzváni k volbě jazyka, který chcete pro zařízení nastavit. Zadejte jazyk a stiskněte Enter. 
   
    ![Konfigurace a registrace zařízení StorSimple 1](./media/storsimple-configure-and-register-device-u1/HCS_RegisterYourDevice1-U1-include.png)
4. V zobrazené nabídce konzoly sériového portu se výběrem možnosti 1 přihlaste s oprávněním k úplnému přístupu. 
   
    ![Registrace zařízení StorSimple 2](./media/storsimple-configure-and-register-device-u1/HCS_RegisterYourDevice2_U1-include.png)
   
     Provedením kroků 5–12 konfigurujte minimální požadovaná síťová nastavení zařízení. **Tyto kroky konfigurace je nutné provést v aktivním řadiči zařízení.** Nabídka konzoly sériového portu zobrazuje stav řadiče ve zprávě v záhlaví. Pokud nejste připojeni k aktivnímu řadiči, odpojte se a potom se připojte k aktivnímu řadiči.
5. Na příkazovém řádku zadejte heslo. Výchozí heslo zařízení je **Password1**.
6. Zadejte následující příkaz: `Invoke-HcsSetupWizard`. 
7. Zobrazí se průvodce instalací, který vám pomůže konfigurovat nastavení sítě pro zařízení. Zadejte následující informace: 
   
   * IP adresa síťového rozhraní DATA 0
   * maska podsítě
   * brána
   * IP adresa primárního serveru DNS
     
        Upozornění: systém po provedení každého kroku postupu ověřuje nastavení sítě.
     
     > [!NOTE]
     > Na použití nastavení masky podsítě a služby DNS může být nutné několik minut počkat. Pokud se zobrazí chybová zpráva „Zkontrolujte síťové připojení rozhraní Data 0“, zkontrolujte fyzické připojení síťového rozhraní DATA 0 aktivního řadiče k síti.
     > 
     > 
8. Volitelně konfigurujte proxy server. Konfigurace proxy serveru je volitelná, ale **můžete ji provést jenom v tomto kroku**. Další informace najdete v článku [Konfigurace webového proxy serveru pro zařízení](../articles/storsimple/storsimple-configure-web-proxy.md).
9. Konfigurujte primární server NTP pro zařízení. Servery NTP jsou požadovány, protože zařízení musí synchronizovat čas, aby ho mohli ověřit poskytovatelé cloudových služeb. Ujistěte se, že vaše síť umožňuje přenos dat NTP z vašeho datového centra na internet. Pokud to není možné, zadejte interní server NTP. 
10. Platnost hesla správce zařízení z bezpečnostních důvodů vyprší po první relaci a je nutné heslo nyní změnit. Až k tomu budete vyzváni, zadejte heslo správce zařízení. Platné heslo správce zařízení musí být tvořeno 8 až 15 znaky. Heslo musí obsahovat kombinaci tří z následujících čtyř typů znaků: malá písmena, velká písmena, číslice a speciální znaky.
    
    <br/>![Registrace zařízení StorSimple 5](./media/storsimple-configure-and-register-device-u1/HCS_RegisterYourDevice5_U1-include.png)
11. V posledním kroku instalace zařízení průvodce registruje zařízení ve službě StorSimple Manager. K provedení tohoto kroku budete potřebovat registrační klíč služby, který jste získali v kroku 2. Po zadání registračního klíče může být nutné 2–3 minuty počkat, než se zařízení zaregistruje.
    
    > [!NOTE]
    > Průvodce lze kdykoliv ukončit stisknutím kombinace kláves Ctrl+C. Pokud jste zadali všechna nastavení sítě (IP adresu pro rozhraní DATA 0, masku podsítě a bránu), vaše záznamy zůstanou zachovány.
    > 
    > 
    
    ![Registrace zařízení StorSimple 6](./media/storsimple-configure-and-register-device-u1/HCS_RegisterYourDevice6_U1-include.png)
12. Po provedení registrace zařízení se zobrazí šifrovací klíč dat služby. Klíč zkopírujte a uložte na bezpečném místě. **Tento klíč bude požadován spolu s registračním klíčem služby k registraci dalších zařízení ve službě StorSimple Manager.** Další informace o tomto klíči najdete v článku [Zabezpečení zařízení StorSimple](../articles/storsimple/storsimple-security.md).
    
    ![Registrace zařízení StorSimple 7](./media/storsimple-configure-and-register-device-u1/HCS_RegisterYourDevice7_U1-include.png)    
    
    > [!NOTE]
    > Pokud chcete zkopírovat text z okna konzoly sériového portu, jednoduše ho vyberte. Potom by mělo být možné text vložit do schránky nebo libovolného textového editoru. Ke zkopírování šifrovacího klíče dat služby NEPOUŽÍVEJTE kombinaci kláves Ctrl+C. Použití kombinace kláves Ctrl+C způsobí ukončení průvodce instalací. Heslo správce zařízení nebude změněno a zařízení bude používat výchozí heslo.
    > 
    > 
13. Ukončete konzolu sériového portu.
14. Přejděte zpět na portál Azure Classic a proveďte následující kroky:
    
    1. Dvojitým kliknutím na službu StorSimple Manager zobrazte stránku **Rychlý start**.
    2. Klikněte na **Zobrazit připojená zařízení**.
    3. Na stránce **Zařízení** kontrolou stavu ověřte, že se zařízení úspěšně připojilo ke službě. Zařízení musí být ve stavu **Online**.
       
        ![Stránka zařízení StorSimple](./media/storsimple-configure-and-register-device-u1/HCS_DevicesPageM_U1-include.png) 
       
        Pokud je zařízení ve stavu **Offline**, počkejte několik minut, než zařízení přejde do stavu Online. 
       
        Pokud je zařízení i po uplynutí několika minut stále v režimu Offline, je nutné zkontrolovat, jestli je brána firewall nastavená způsobem popsaným v článku o [požadavcích zařízení StorSimple na síťe](../articles/storsimple/storsimple-system-requirements.md). 
       
        Zkontrolujte, jestli je port 9354 otevřený pro odchozí komunikaci, protože ho používá sběrnice služby pro komunikaci služby StorSimple Manager se zařízením.

