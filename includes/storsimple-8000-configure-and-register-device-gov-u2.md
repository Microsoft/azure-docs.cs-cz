<!--author=SharS last changed: 06/22/2016-->

### <a name="to-configure-and-register-the-device"></a>Konfigurace a registrace zařízení
1. V konzole sériového portu zařízení StorSimple spusťte rozhraní Windows PowerShell. Další informace najdete v článku [Použití klienta PuTTY k připojení ke konzole sériového portu zařízení](../articles/storsimple/storsimple-8000-deployment-walkthrough-gov-u2.md#use-putty-to-connect-to-the-device-serial-console). **Postup proveďte přesně, jinak ke konzole nezískáte přístup.**
2. Ve spuštěné relaci jedním stisknutím klávesy **Enter** zobrazte příkazový řádek.
3. Budete vyzváni k volbě jazyka, který chcete pro zařízení nastavit. Zadejte jazyk a stiskněte **Enter**.
   
    ![Konfigurace a registrace zařízení StorSimple 1](./media/storsimple-configure-and-register-device-gov-u2/HCS_RegisterYourDevice1-gov-include.png)
4. V nabídce konzoly sériového portu, který se zobrazí, zvolte možnost 1, **přihlásit úplný přístup**.
   
    ![Registrace zařízení StorSimple 2](./media/storsimple-configure-and-register-device-gov-u2/HCS_RegisterYourDevice2-gov-include.png)
5. Proveďte následující postup pro konfiguraci nastavení sítě minimální požadované pro vaše zařízení.
   
   > [!IMPORTANT]
   > Tyto kroky konfigurace je nutné provést v aktivním řadiči zařízení. Nabídka konzoly sériového portu zobrazuje stav řadiče ve zprávě v záhlaví. Pokud se připojíte k aktivnímu řadiči, odpojte a připojte se k aktivnímu řadiči.
   
   1. Na příkazovém řádku zadejte heslo. Výchozí heslo zařízení je **Password1**.
   2. Zadejte následující příkaz:
      
        `Invoke-HcsSetupWizard`
   3. Zobrazí se průvodce instalací, který vám pomůže konfigurovat nastavení sítě pro zařízení. Zadejte následující informace:
      
      * IP adresu síťového rozhraní DATA 0
      * maska podsítě
      * brána
      * IP adresa primárního serveru DNS
      * IP adresa primárního serveru NTP
      
      > [!NOTE]
      > Možná budete muset Počkejte několik minut pro masku podsítě a nastavení DNS, které se má použít.
    
   4. Volitelně nakonfigurujte váš webový proxy server.
      
      > [!IMPORTANT]
      > Přestože konfigurace webového proxy serveru je volitelný, mějte na paměti, že pokud používáte webový proxy server, můžete pouze nakonfigurovat ji sem. Další informace najdete v článku [Konfigurace webového proxy serveru pro zařízení](../articles/storsimple/storsimple-configure-web-proxy.md).
     
6. Stiskněte kombinaci kláves Ctrl + C ukončíte Průvodce instalací.
8. Spusťte následující rutinu tak, aby odkazoval zařízení na portálu Microsoft Azure Government (protože je ve výchozím nastavení odkazuje na veřejnou portál Azure classic). To se restartuje oba řadiče. Doporučujeme vám, že používáte dvě relace PuTTY lze najednou připojit pro oba řadiče, aby mohli zobrazit, když je restartování každého řadiče.
   
    `Set-CloudPlatform -AzureGovt_US`
   
   Zobrazí se potvrzovací zpráva. Přijměte výchozí nastavení (**Y**).
9. Spusťte následující rutiny můžete pokračovat v instalaci:
   
    `Invoke-HcsSetupWizard`
   
    ![Obnovení Průvodce instalací](./media/storsimple-configure-and-register-device-gov-u2/HCS_ResumeSetup-gov-include.png)
   
10. Přijměte nastavení sítě. Zobrazí se zpráva ověření a po přijetí jednotlivých nastavení.
11. Platnost hesla správce zařízení z bezpečnostních důvodů vyprší po první relaci a je nutné heslo nyní změnit. Až k tomu budete vyzváni, zadejte heslo správce zařízení. Platné heslo správce zařízení musí být tvořeno 8 až 15 znaky. Heslo musí obsahovat kombinaci tří z následujících čtyř typů znaků: malá písmena, velká písmena, číslice a speciální znaky.
    
    <br/>![Registrace zařízení StorSimple 5](./media/storsimple-configure-and-register-device-gov-u2/HCS_RegisterYourDevice5_gov-include.png)
12. V posledním kroku průvodce instalací zařízení zaregistruje zařízení ve službě Správce zařízení StorSimple. V takovém případě budete potřebovat registrační klíč služby, který jste získali v [krok 2: získání registračního klíče služby](../articles/storsimple/storsimple-8000-deployment-walkthrough-gov-u2.md#step-2-get-the-service-registration-key). Po zadání registračního klíče může být nutné 2–3 minuty počkat, než se zařízení zaregistruje.
    
    > [!NOTE]
    > Průvodce lze kdykoliv ukončit stisknutím kombinace kláves Ctrl+C. Pokud jste zadali všechna nastavení sítě (IP adresu pro rozhraní DATA 0, masku podsítě a bránu), vaše záznamy zůstanou zachovány.
    
    ![Probíhá registrace zařízení StorSimple](./media/storsimple-configure-and-register-device-gov-u2/HCS_RegistrationProgress-gov-include.png)
13. Po provedení registrace zařízení se zobrazí šifrovací klíč dat služby. Klíč zkopírujte a uložte na bezpečném místě. **Tento klíč je požadován s registrační klíč služby k registraci dalších zařízení se službou Správce zařízení StorSimple.** Další informace o tomto klíči najdete v článku [Zabezpečení zařízení StorSimple](../articles/storsimple/storsimple-8000-security.md).
    
    ![Registrace zařízení StorSimple 7](./media/storsimple-configure-and-register-device-gov-u2/HCS_RegisterYourDevice7_gov-include.png)
    > [!IMPORTANT]
    > Pokud chcete zkopírovat text z okna konzoly sériového portu, jednoduše ho vyberte. Potom by mělo být možné text vložit do schránky nebo libovolného textového editoru.
    > 
    > Nepoužívejte **kombinaci kláves Ctrl + C** ke zkopírování šifrovacího klíče dat služby. Pomocí **kombinaci kláves Ctrl + C** způsobí ukončení Průvodce instalací. Heslo správce zařízení nebude změněno a zařízení bude používat výchozí heslo.
    
14. Ukončete konzolu sériového portu.
15. Vraťte se k portálu Azure Government a proveďte následující kroky:
    
    1. Přejděte do služby Správce zařízení StorSimple.
    2. Klikněte na **Zařízení**. V seznamu zařízení Identifikujte zařízení, že jste ddeploying. Ověřte, že zařízení má úspěšně připojen ke službě vyhledáním stav. Zařízení musí být ve stavu **Online**.
            
        Pokud je zařízení ve stavu **Offline**, počkejte několik minut, než zařízení přejde do stavu Online.
       
        Pokud je zařízení i po uplynutí několika minut stále v režimu Offline, je nutné zkontrolovat, jestli je brána firewall nastavená způsobem popsaným v článku o [požadavcích zařízení StorSimple na síťe](../articles/storsimple/storsimple-8000-system-requirements.md).
       
        Zkontrolujte, jestli je port 9354 otevřený pro odchozí komunikaci, protože ho používá sběrnice služby pro komunikaci služby Správce zařízení StorSimple se zařízením.

