<!--author=alkohli last changed: 09/01/16-->

#### <a name="to-download-hotfixes"></a>Stažení oprav hotfix
Provedením následujících kroků si stáhněte aktualizace softwaru z Katalogu služby Microsoft Update.

1. Spusťte Internet Explorer a přejděte na [http://catalog.update.microsoft.com](http://catalog.update.microsoft.com).
2. Pokud na tomto počítači používáte Katalog služby Microsoft Update poprvé, po zobrazení výzvy k instalaci doplňku Katalog služby Microsoft Update klikněte na **Nainstalovat**.
    ![Nainstalujte katalogu](./media/storsimple-install-update2-hotfix/HCS_InstallCatalog-include.png)
3. Do vyhledávacího pole katalogu služby Microsoft Update, zadejte číslo znalostní báze Knowledge Base (KB) opravy hotfix, které chcete stáhnout, například **3186843**a potom klikněte na **vyhledávání**.
   
    Zobrazí se seznam oprav hotfix, například **kumulativní softwaru sady aktualizace 3.0 pro zařízení StorSimple řady 8000**.
   
    ![Prohledávání katalogu](./media/storsimple-install-update2-hotfix/HCS_SearchCatalog1-include.png)
4. Klikněte na tlačítko **Přidat**. Aktualizace se přidá do košíku.
5. Vyhledejte všechny další opravy hotfix uvedené v předchozí tabulce (**3186859**) a přidejte všechny do košíku.
6. Klikněte na **Zobrazit košík**.
7. Klikněte na **Stáhnout**. Zadejte místní umístění, do kterého chcete aktualizace stáhnout, nebo do něj přejděte pomocí tlačítka **Procházet**. Aktualizace se stáhnou do zadaného umístění a umístit do podsložky se stejným názvem jako aktualizace. Složku je také možné zkopírovat do sdílené síťové složky dostupné ze zařízení.

> [!NOTE]
> Opravy hotfix musí být dostupný z obou řadičích ke zjištění potenciálních chybové zprávy z druhé strany řadiče.
> 
> 

#### <a name="to-install-and-verify-regular-mode-hotfixes"></a>Instalace a ověření oprav hotfix běžného režimu
Provedením následujících kroků nainstalujte a ověřte opravy hotfix běžného režimu. Pokud jste již nainstalovali pomocí portálu Azure, přeskočit na [instalaci a ověření opravy hotfix režimu údržby](#to-install-and-verify-maintenance-mode-hotfixes).

1. Pokud chcete nainstalovat opravy hotfix, v konzole sériového portu zařízení StorSimple spusťte rozhraní Windows PowerShell. Postupujte podle podrobných pokynů v článku [Připojení ke konzole sériového portu pomocí klienta PuTTy](../articles/storsimple/storsimple-deployment-walkthrough.md#use-putty-to-connect-to-the-device-serial-console). Na příkazovém řádku stiskněte **Enter**.
2. Vyberte **Možnost 1** a přihlaste se k zařízení s úplným přístupem. Doporučujeme opravu hotfix nejprve nainstalovat na pasivním kontroleru.
3. Pokud chcete nainstalovat opravu hotfix, na příkazovém řádku zadejte:
   
    `Start-HcsHotfix -Path <path to update file> -Credential <credentials in domain\username format>`
   
    V cestě ke sdílené složce v předchozím příkazu používejte IP adresu místo DNS. Parametr Credential se používá pouze pro přístup ke sdílené složce s nutností ověření.
   
    Parametr Credential doporučujeme používat pro přístup ke sdíleným složkám. I sdílené složky otevřené všem uživatelům obvykle nejsou otevřené pro neověřené uživatele.
   
    Po zobrazení výzvy zadejte heslo.
   
    Ukázkový výstup najdete níž.
   
        ````
        Controller0>Start-HcsHotfix -Path \\10.100.100.100\share
        \hcsmdssoftwareupdate.exe -Credential contoso\John
   
        Confirm
   
        This operation starts the hotfix installation and could reboot one or
        both of the controllers. If the device is serving I/Os, these will not
        be disrupted. Are you sure you want to continue?
        [Y] Yes [N] No [?] Help (default is "Y"): Y
   
        ````
4. Po zobrazení výzvy k potvrzení instalace opravy hotfix zadejte **Y**.
5. Průběh aktualizace můžete sledovat pomocí rutiny `Get-HcsUpdateStatus`. Aktualizace se nejdříve dokončí na pasivním kontroleru. Jakmile je pasivní kontroler aktualizovaný, proběhne převzetí služeb při selhání a potom se aktualizace nainstaluje i na druhý kontroler. Aktualizace je dokončena, když jsou aktualizované oba kontrolery.
   
    Následující ukázkový výstup ukazuje probíhající aktualizaci. Když aktualizace probíhá, hodnota `RunInprogress` bude `True`.

    ```
    Controller0>Get-HcsUpdateStatus
    RunInprogress       : True
    LastHotfixTimestamp :
    LastUpdateTimestamp : 8/29/2016 2:04:02 AM
    Controller0Events   :
    Controller1Events   :
    ```
   
     Následující ukázkový výstup ukazuje dokončení aktualizace. Když se aktualizace dokončí, hodnota `RunInProgress` bude `False`.
   
    ```
    Controller0>Get-HcsUpdateStatus
    RunInprogress       : False
    LastHotfixTimestamp : 8/30/2016 9:15:55 AM
    LastUpdateTimestamp : 8/30/2016 9:06:07 AM
    Controller0Events   :
    Controller1Events   :
    ```

    > [!NOTE] 
    > Rutina občas hlásí `False`, i když aktualizace stále probíhá. Pokud chcete zkontrolovat, že se oprava hotfix dokončila, počkejte několik minut, znovu spusťte tento příkaz a ověřte, že hodnota `RunInProgress` je `False`. Pokud ano, oprava hotfix byla dokončena.

1. Po dokončení aktualizace softwaru zkontrolujte verze systémového softwaru. Zadejte:
   
    `Get-HcsSystem`
   
    Měly by se zobrazit následující verze:
   
   * `HcsSoftwareVersion: 6.3.9600.17759`
   * `CisAgentVersion:  1.0.9343.0`
   * `MdsAgentVersion: 30.0.4698.16`
     
     Pokud číslo verze se nezmění po instalaci aktualizace, znamená to, že se nepodařilo použít opravu hotfix. Pokud je to váš případ a potřebujete další pomoc, kontaktujte [podporu Microsoftu](../articles/storsimple/storsimple-contact-microsoft-support.md).
     
     > [!IMPORTANT]
     > Před instalací zbývajících aktualizací je nutné restartovat aktivní kontroler pomocí rutiny `Restart-HcsController`. 
     > 
     > 
2. Opakujte kroky 3 až 5 nainstalujte opravu hotfix pro ovladače a firmware LSI **KB3186859**. Po dokončení instalace opravy hotfix, pomocí `Get-HcsSystem` rutiny. Verze LSI musí být:
   
   * `Lsisas2Version: 2.0.78.00`
3. Opakujte kroky 3 až 5 pro instalaci aktualizace Storport a Spaceport **KB3121261**.
4. Při aktualizaci Update 2 nebo dřívější verzi, bude také muset stáhnout:
   
   * Oprava iSCSI pomocí KB3146621
   * Oprava rozhraní WMI pomocí KB3103616

#### <a name="to-install-and-verify-maintenance-mode-hotfixes"></a>Instalace a ověření oprav hotfix režimu údržby
Použití KB3121899 k instalaci aktualizace firmwaru disku. Jedná se o narušující aktualizace a jejich dokončení trvá přibližně 30 minut. Můžete se rozhodnout je nainstalovat během naplánovaného časového období údržby pomocí připojení ke konzole sériového portu zařízení.

Poznámka: Pokud je váš firmware disku aktuální, není třeba instalovat tyto aktualizace. Z konzoly sériového portu zařízení spusťte rutinu `Get-HcsUpdateAvailability`, která zkontroluje dostupnost aktualizací a jestli se jedná o narušující (režim údržby) nebo nenarušující (běžný režim) aktualizace.

Pokud chcete nainstalovat aktualizace firmwaru disku, postupujte podle následujících pokynů.

1. Umístíte zařízení do režimu údržby. Všimněte si, že byste neměli používat vzdálenou komunikaci prostředí Windows PowerShell, při připojení k zařízení v režimu údržby. Místo toho spusťte tuto rutinu v řadiče zařízení při připojení prostřednictvím konzole sériového portu zařízení. Zadejte:
   
    `Enter-HcsMaintenanceMode`
   
    Ukázkový výstup najdete níž.
   
        Controller0>Enter-HcsMaintenanceMode
        Checking device state...
   
        In maintenance mode, your device will not service IOs and will be disconnected from the Microsoft Azure StorSimple Manager service. Entering maintenance mode will end the current session and reboot both controllers, which takes a few minutes to complete. Are you sure you want to enter maintenance mode?
        [Y] Yes [N] No (Default is "Y"): Y
   
        -----------------------MAINTENANCE MODE------------------------
        Microsoft Azure StorSimple Appliance Model 8100
        Name: Update2-8100-SHG0997879L76673
        Copyright (C) 2014 Microsoft Corporation. All rights reserved.
        You are connected to Controller0 - Passive
        ---------------------------------------------------------------
   
        Serial Console Menu
        [1] Log in with full access
        [2] Log into peer controller with full access
        [3] Connect with limited access
        [4] Change language
        Please enter your choice>
   
    Oběma řadičům potom restartujte do režimu údržby.
2. Pokud chcete nainstalovat aktualizaci firmwaru disku, zadejte:
   
    `Start-HcsHotfix -Path <path to update file> -Credential <credentials in domain\username format>`
   
    Ukázkový výstup najdete níž.
   
        Controller1>Start-HcsHotfix -Path \\10.100.100.100\share\DiskFirmwarePackage.exe -Credential contoso\john
        Enter Password:
        WARNING: In maintenance mode, hotfixes should be installed on each controller sequentially. After the hotfix is installed on this controller, install it on the peer controller.
        Confirm
        This operation starts a hotfix installation and could reboot one or both of the controllers. By installing new updates you agree to, and accept any additional terms associated with, the new functionality listed in the release notes (https://go.microsoft.com/fwLink/?LinkID=613790). Are you sure you want to continue?
        [Y] Yes [N] No (Default is "Y"): Y
        WARNING: Installation is currently in progress. This operation can take several minutes to complete.
3. Průběh instalace můžete sledovat pomocí příkazu `Get-HcsUpdateStatus`. Když se `RunInProgress` změní na `False`, aktualizace je dokončena.
4. Po dokončení instalace se kontroler, na který se instalovala oprava hotfix režimu údržby, restartuje. Přihlaste se jako Možnost 1 s úplným přístup a zkontrolujte verzi firmwaru disku. Zadejte:
   
   `Get-HcsFirmwareVersion`
   
   Očekávané verze firmwaru disku jsou:
   
   `XMGG, XGEG, KZ50, F6C2, VR08`
   
   Ukázkový výstup najdete níž.
   
       -----------------------MAINTENANCE MODE------------------------
       Microsoft Azure StorSimple Appliance Model 8100
       Name: Update2-8100-SHG0997879L76YD
       Software Version: 6.3.9600.17705
       Copyright (C) 2014 Microsoft Corporation. All rights reserved.
       You are connected to Controller1
       ---------------------------------------------------------------
   
       Controller1>Get-HcsFirmwareVersion
   
       Controller0 : TalladegaFirmware
         ActiveBIOS:0.45.0006
         BackupBIOS:0.45.0008
         MainCPLD:17.0.0005
         ActiveBMCRoot:2.0.000E
         BackupBMCRoot:2.0.000E
         BMCBoot:2.0.0001
         LsiFirmware:19.00.00.00
         LsiBios:07.37.00.00
         Battery1Firmware:06.29
         Battery2Firmware:06.29
         DomFirmware:X231600
         CanisterFirmware:3.5.0.32
         CanisterBootloader:5.03
         CanisterConfigCRC:0xD1B030A4
         CanisterVPDStructure:0x06
         CanisterGEMCPLD:0x17
         CanisterVPDCRC:0xEE3504B4
         MidplaneVPDStructure:0x0C
         MidplaneVPDCRC:0xA6BD4F64
         MidplaneCPLD:0x10
         PCM1Firmware:1.00|1.05
         PCM1VPDStructure:0x05
         PCM1VPDCRC:0x41BEF99C
         PCM2Firmware:1.00|1.05
         PCM2VPDStructure:0x05
         PCM2VPDCRC:0x41BEF99C
   
         DisksFirmware
         SEAGATE:ST400FM0073:XGEG
         SEAGATE:ST400FM0073:XGEG
         SEAGATE:ST400FM0073:XGEG
         SEAGATE:ST400FM0073:XGEG
         SEAGATE:ST4000NM0023:XMGG
         SEAGATE:ST4000NM0023:XMGG
         SEAGATE:ST4000NM0023:XMGG
         SEAGATE:ST4000NM0023:XMGG
         SEAGATE:ST4000NM0023:XMGG
         SEAGATE:ST4000NM0023:XMGG
         SEAGATE:ST4000NM0023:XMGG
         SEAGATE:ST4000NM0023:XMGG
   
    Na druhém kontroleru spusťte příkaz `Get-HcsFirmwareVersion` a ověřte, že došlo k aktualizaci verze softwaru. Potom můžete ukončit režim údržby. Uděláte to tak, že na obou kontrolerech zařízení zadáte následující příkaz:
   
   `Exit-HcsMaintenanceMode`
5. V řadičích restartovat po ukončení režimu údržby. Po úspěšné instalaci aktualizací firmwaru disku a ukončení režimu údržby na zařízení se vraťte na portál Azure Classic. Všimněte si, že nemusí zobrazit na portálu, že jste nainstalovali aktualizace režimu údržby po dobu 24 hodin.

