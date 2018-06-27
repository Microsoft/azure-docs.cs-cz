### <a name="install-maintenance-mode-updates-via-windows-powershell-for-storsimple"></a>Instalovat aktualizace režimu údržby pomocí prostředí Windows PowerShell pro StorSimple

Když použijete aktualizace režimu údržby pro zařízení StorSimple, jsou pozastavena všechny vstupně-výstupní požadavky. Službám, jako je paměť s náhodným přístupem stálé (paměti NVRAM) nebo službu clusteringu se zastaví. Oba řadiče restartovat, když zadáte nebo ukončit tento režim. Při ukončení tohoto režimu se všechny služby obnovit a jsou v pořádku. (To může trvat několik minut.)

> [!IMPORTANT]
> * Před přechodem do režimu údržby, ověřte, zda jsou oba řadiče zařízení na portálu Azure v pořádku. Pokud není v pořádku, řadičem [obraťte se na podporu společnosti Microsoft](../articles/storsimple/storsimple-8000-contact-microsoft-support.md) pro další kroky.
> * Pokud jste v režimu údržby, je potřeba nejdřív aktualizovat jeden řadič a pak jiný řadič.

1. Použití klienta PuTTY k připojení ke konzole sériového portu. Postupujte podle podrobných pokynů v článku [Připojení ke konzole sériového portu pomocí klienta PuTTy](../articles/storsimple/storsimple-8000-deployment-walkthrough-u2.md#use-putty-to-connect-to-the-device-serial-console). Na příkazovém řádku stiskněte **Enter**. Vyberte možnost 1, **přihlásit úplný přístup**.

2. Chcete-li kontroler převeďte do režimu údržby, zadejte:
    
    `Enter-HcsMaintenanceMode`

    Oběma řadičům restartování do režimu údržby.

3. Nainstalujte vaše aktualizace režimu údržby. Zadejte:

    `Start-HcsUpdate`

    Zobrazí se výzva k potvrzení. Po ověření, aktualizace, budou nainstalovány na řadiči, který se právě používají. Po instalaci aktualizace, restartuje kontroleru.

4. Monitorujte stav aktualizace. Přihlaste se k řadiči sdílené jako je aktuální řadič se aktualizuje a nemůže zpracovat žádné další příkazy. Zadejte:

    `Get-HcsUpdateStatus`

    Pokud `RunInProgress` je `True`, aktualizaci stále probíhá. Pokud `RunInProgress` je `False`, znamená to, že aktualizace byla dokončena.

5. Po aktualizace firmwaru disku jsou úspěšně použity a restartování aktualizované řadiče, zkontrolujte verzi firmwaru disku. Aktualizované řadiče zadejte:

    `Get-HcsFirmwareVersion`
   
    Verze firmwaru očekávané disku jsou:  `XMGJ, XGEG, KZ50, F6C2, VR08, N003, 0107`

6. Ukončete režim údržby. Zadejte následující příkaz pro každý řadič zařízení:

    `Exit-HcsMaintenanceMode`

    Kontrolery se restartují, jakmile ukončíte režim údržby.

7. Vraťte se k portálu Azure. Na portálu se nemusí zobrazit nainstalované aktualizace režimu údržby po dobu 24 hodin.