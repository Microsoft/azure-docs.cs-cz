<!--author=SharS last changed: 02/22/16-->

### <a name="to-configure-and-register-the-device"></a>Konfigurace a registrace zařízení
1. V konzole sériového portu zařízení StorSimple spusťte rozhraní Windows PowerShell. Další informace najdete v článku [Použití klienta PuTTY k připojení ke konzole sériového portu zařízení](#use-putty-to-connect-to-the-device-serial-console). **Postup proveďte přesně, jinak ke konzole nezískáte přístup.**
2. Ve spuštěné relaci jedním stisknutím klávesy Enter zobrazte příkazový řádek. 
3. Budete vyzváni k volbě jazyka, který chcete pro zařízení nastavit. Zadejte jazyk a stiskněte Enter. 
   
    ![Konfigurace a registrace zařízení StorSimple 1](./media/storsimple-configure-and-register-device-gov/HCS_RegisterYourDevice1-gov-include.png)
4. V zobrazené nabídce konzoly sériového portu se výběrem možnosti 1 přihlaste s oprávněním k úplnému přístupu. 
   
    ![Registrace zařízení StorSimple 2](./media/storsimple-configure-and-register-device-gov/HCS_RegisterYourDevice2-gov-include.png)
5. Proveďte následující postup pro konfiguraci nastavení sítě minimální požadované pro vaše zařízení.
   
   > [!IMPORTANT]
   > Tyto kroky konfigurace je nutné provést v aktivním řadiči zařízení. Nabídka konzoly sériového portu zobrazuje stav řadiče ve zprávě v záhlaví. Pokud se připojíte k aktivnímu řadiči, odpojte a připojte se k aktivnímu řadiči.
   > 
   > 
   
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
      > 
      > 
   4. Volitelně nakonfigurujte váš webový proxy server.
      
      > [!IMPORTANT]
      > Přestože konfigurace webového proxy serveru je volitelný, mějte na paměti, že pokud používáte webový proxy server, můžete pouze nakonfigurovat ji sem. Další informace najdete v článku [Konfigurace webového proxy serveru pro zařízení](../articles/storsimple/storsimple-configure-web-proxy.md). 
      > 
      > 
6. Stiskněte kombinaci kláves Ctrl + C ukončíte Průvodce instalací.
7. Nainstalujte aktualizace následujícím způsobem:
   
   1. Chcete-li nastavit IP adresy na obou řadičích použijte následující rutinu:
      
      `Set-HcsNetInterface -InterfaceAlias Data0 -Controller0IPv4Address <Controller0 IP> -Controller1IPv4Address <Controller1 IP>`
   2. Na příkazovém řádku spusťte `Get-HcsUpdateAvailability`. Mají být upozorněni, že jsou k dispozici aktualizace.
   3. Spusťte `Start-HcsUpdate`. Tento příkaz můžete spustit v každém uzlu. Aktualizace se použijí na prvním řadiči, řadičem bude převzetí služeb při selhání a pak aktualizace se použijí na jiný řadič.
      
      Průběh aktualizace můžete sledovat spuštěním `Get-HcsUpdateStatus`.    
      
      Následující ukázkový výstup ukazuje probíhající aktualizaci.
      
      ````
      Controller0>Get-HcsUpdateStatus
      RunInprogress       : True
      LastHotfixTimestamp : 4/13/2015 10:56:13 PM
      LastUpdateTimestamp : 4/13/2015 10:35:25 PM
      Controller0Events   :
      Controller1Events   : 
      ````
      
      Následující ukázkový výstup ukazuje dokončení aktualizace.
      
      ````
      Controller1>Get-HcsUpdateStatus
      
      RunInprogress       : False
      LastHotfixTimestamp : 4/13/2015 10:56:13 PM
      LastUpdateTimestamp : 4/13/2015 10:35:25 PM
      Controller0Events   :
      Controller1Events   :
      
      ````
      
      To může trvat až 11 hodin použít všechny aktualizace, včetně aktualizací Windows.

8. Po všechny aktualizace jsou úspěšně nainstalováni, spusťte následující rutiny a potvrďte, zda byly správně nasazení aktualizací softwaru:
   
     `Get-HcsSystem`
   
    Měly by se zobrazit následující verze:
   
   * HcsSoftwareVersion: 6.3.9600.17491
   * CisAgentVersion: 1.0.9037.0
   * MdsAgentVersion: 26.0.4696.1433
9. Spusťte následující rutiny a potvrďte, že aktualizace firmwaru použil správně:
   
    `Start-HcsFirmwareCheck`.
   
     Musí být ve stavu firmware **UpToDate**.
10. Spusťte následující rutinu tak, aby odkazoval zařízení na portálu Microsoft Azure Government (protože je ve výchozím nastavení odkazuje na veřejnou portál Azure classic). To se restartuje oba řadiče. Doporučujeme vám, že používáte dvě relace PuTTY lze najednou připojit pro oba řadiče, aby mohli zobrazit, když je restartování každého řadiče.
    
     `Set-CloudPlatform -AzureGovt_US`
    
    Zobrazí se potvrzovací zpráva. Přijměte výchozí nastavení (**Y**).
11. Spusťte následující rutiny můžete pokračovat v instalaci:
    
     `Invoke-HcsSetupWizard`
    
     ![Obnovení Průvodce instalací](./media/storsimple-configure-and-register-device-gov/HCS_ResumeSetup-gov-include.png)
    
    Pokud budete pokračovat v instalaci, bude průvodce verzí Update 1 (který odpovídá verzi 17469). 
12. Přijměte nastavení sítě. Zobrazí se zpráva ověření a po přijetí jednotlivých nastavení.
13. Platnost hesla správce zařízení z bezpečnostních důvodů vyprší po první relaci a je nutné heslo nyní změnit. Až k tomu budete vyzváni, zadejte heslo správce zařízení. Platné heslo správce zařízení musí být tvořeno 8 až 15 znaky. Heslo musí obsahovat kombinaci tří z následujících čtyř typů znaků: malá písmena, velká písmena, číslice a speciální znaky.
    
    <br/>![Registrace zařízení StorSimple 5](./media/storsimple-configure-and-register-device-gov/HCS_RegisterYourDevice5_gov-include.png)
14. V posledním kroku instalace zařízení průvodce registruje zařízení ve službě StorSimple Manager. V takovém případě budete potřebovat registrační klíč služby, který jste získali v [krok 2: získání registračního klíče služby](#step-2-get-the-service-registration-key). Po zadání registračního klíče může být nutné 2–3 minuty počkat, než se zařízení zaregistruje.
    
    > [!NOTE]
    > Průvodce lze kdykoliv ukončit stisknutím kombinace kláves Ctrl+C. Pokud jste zadali všechna nastavení sítě (IP adresu pro rozhraní DATA 0, masku podsítě a bránu), vaše záznamy zůstanou zachovány.
    > 
    > 
    
    ![Probíhá registrace zařízení StorSimple](./media/storsimple-configure-and-register-device-gov/HCS_RegistrationProgress-gov-include.png)
15. Po provedení registrace zařízení se zobrazí šifrovací klíč dat služby. Klíč zkopírujte a uložte na bezpečném místě. **Tento klíč bude požadován spolu s registračním klíčem služby k registraci dalších zařízení ve službě StorSimple Manager.** Další informace o tomto klíči najdete v článku [Zabezpečení zařízení StorSimple](../articles/storsimple/storsimple-security.md).
    
    ![Registrace zařízení StorSimple 7](./media/storsimple-configure-and-register-device-gov/HCS_RegisterYourDevice7_gov-include.png)    
    
    > [!IMPORTANT]
    > Pokud chcete zkopírovat text z okna konzoly sériového portu, jednoduše ho vyberte. Potom by mělo být možné text vložit do schránky nebo libovolného textového editoru. 
    > 
    > Ke zkopírování šifrovacího klíče dat služby NEPOUŽÍVEJTE kombinaci kláves Ctrl+C. Použití kombinace kláves Ctrl+C způsobí ukončení průvodce instalací. Heslo správce zařízení nebude změněno a zařízení bude používat výchozí heslo.
    > 
    > 
16. Ukončete konzolu sériového portu.
17. Vraťte se k portálu Azure Government a proveďte následující kroky:
    
    1. Dvojitým kliknutím na službu StorSimple Manager zobrazte stránku **Rychlý start**.
    2. Klikněte na **Zobrazit připojená zařízení**.
    3. Na stránce **Zařízení** kontrolou stavu ověřte, že se zařízení úspěšně připojilo ke službě. Zařízení musí být ve stavu **Online**.
       
        ![Stránka zařízení StorSimple](./media/storsimple-configure-and-register-device-gov/HCS_DeviceOnline-gov-include.png) 
       
        Pokud je zařízení ve stavu **Offline**, počkejte několik minut, než zařízení přejde do stavu Online. 
       
        Pokud je zařízení i po uplynutí několika minut stále v režimu Offline, je nutné zkontrolovat, jestli je brána firewall nastavená způsobem popsaným v článku o [požadavcích zařízení StorSimple na síťe](../articles/storsimple/storsimple-system-requirements.md). 
       
        Ověřte, zda je port 9354 otevřený pro odchozí komunikaci, protože ho používá sběrnice služby pro komunikaci zařízení služby StorSimple Manager.

