---
title: Řešení potíží s agentem Azure Backup
description: Řešení potíží instalace a registrace agenta Azure Backup
services: backup
author: saurabhsensharma
manager: shivamg
ms.service: backup
ms.topic: conceptual
ms.date: 05/21/2019
ms.author: saurse
ms.openlocfilehash: 2c2ed46ed6e4a5d6663387777d3425d18b50500e
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "67060219"
---
# <a name="troubleshoot-microsoft-azure-recovery-services-mars-agent"></a>Řešení potíží s agentem Microsoft Azure Recovery Services (MARS)

Tady je postup pro řešení chyb můžete setkat během konfigurace, registraci, zálohování a obnovení.

## <a name="basic-troubleshooting"></a>Základní řešení potíží

Doporučujeme provést ověření, než začnete řešení potíží s agentem Microsoft Azure Recovery Services (MARS):

- [Ujistěte se, že Agent Microsoft Azure Recovery Services (MARS) je aktuální.](https://go.microsoft.com/fwlink/?linkid=229525&clcid=0x409)
- [Ujistěte se, že existuje síťové propojení mezi agentem MARS a Azure.](https://aka.ms/AB-A4dp50)
- Ujistěte se, že je spuštěná služba Microsoft Azure Recovery Services (v konzole služby). V případě potřeby ji restartujte a opakujte operaci.
- [Ujistěte se, že je v umístění pomocné složky k dispozici 5 až 10 % volného místa.](https://aka.ms/AB-AA4dwtt)
- [Zkontrolujte, jestli službě Azure Backup nepřekáží jiný proces nebo antivirový software.](https://aka.ms/AB-AA4dwtk)
- [Plánované zálohování se nedaří, ale ruční zálohování funguje](https://aka.ms/ScheduledBackupFailManualWorks)
- Ujistěte se, že má váš operační systém nainstalované nejnovější aktualizace.
- [Zajištění nepodporované disky a soubory s nepodporované atributy jsou vyloučeny ze zálohy](backup-support-matrix-mars-agent.md#supported-drives-or-volumes-for-backup)
- Ujistěte se, že **systémové hodiny** v chráněném systému jsou nakonfigurované na správné časové pásmo. <br>
- [Ujistěte se, že má server minimálně rozhraní .Net Framework verze 4.5.2 nebo novější](https://www.microsoft.com/download/details.aspx?id=30653)<br>
- Pokud se pokoušíte **znovu zaregistrovat server** k trezoru, postupujte takto: <br>
  - Ujistěte se, že je agent odinstalovaný ze serveru a odstraněný z portálu. <br>
  - Použijte stejné heslo, jaké jste původně použili k registraci serveru. <br>
- V případě offline zálohování zkontrolujte, zda prostředí Azure PowerShell verze 3.7.0 je nainstalován na počítači zdroje a zkopírujte před zahájením práce offline zálohování
- [Poslední informací Backup agent běží na virtuálním počítači Azure](https://aka.ms/AB-AA4dwtr)

## <a name="invalid-vault-credentials-provided"></a>Neplatné přihlašovací údaje úložiště k dispozici

**Chybová zpráva**: Zadané neplatné přihlašovací údaje trezoru. Soubor je poškozený nebo nemá mít nejnovější přihlašovací údaje přidružené ke službě obnovení. (ID: 34513)

| Příčina | Doporučená akce |
| ---     | ---    |
| **Přihlašovací údaje trezoru jsou neplatné** <br/> <br/> Soubory přihlašovacích údajů trezoru je pravděpodobně být poškozen nebo jeho platnost vypršela (to znamená stažené víc než 48 hodin, než čas registrace)| Stažení nových přihlašovacích údajů z trezoru služby Recovery Services na webu Azure Portal (naleznete v tématu *kroku 6* pod [ **stáhnout agenta MARS** ](https://docs.microsoft.com/azure/backup/backup-configure-vault#download-the-mars-agent) oddílu) a provádět níže: <ul><li> Pokud již máte nainstalována a registrována Microsoft Azure Backup Agent, otevřete konzolu pro Microsoft Azure Backup Agent konzoly MMC a zvolte **zaregistrovat Server** z podokna akcí k dokončení registrace s nově staženým přihlašovací údaje <br/> <li> Pokud nové instalace se nezdařila pokuste se znovu nainstalovat pomocí nových přihlašovacích údajů</ul> **Poznámka:** Pokud více souborů přihlašovacích údajů trezoru se stáhnou předtím, jenom nejnovější stažený soubor je platný do 48 hodin. Proto doporučujeme stáhnout novou nový soubor s přihlašovacími údaji trezoru.
| **Proxy Server nebo brána firewall blokuje <br/>nebo <br/>připojení k Internetu bez** <br/><br/> Pokud váš počítač nebo Proxy serveru má omezený přístup k Internetu potom bez výpisu potřebné adresy URL registrace se nezdaří.| Pokud chcete tento problém vyřešit, proveďte níže:<br/> <ul><li> Váš tým IT zajistit, že systém má připojení k Internetu<li> Pokud Proxy server, zajistěte možnost proxy serveru byl zrušen výběr cesty při registraci agenta, zkontrolujte uvedené kroky nastavení proxy serveru [zde](#verifying-proxy-settings-for-windows)<li> Pokud máte brány firewall a proxy server, pak práce se síťovým týmem zajistit, že následující adresy URL a IP adres mají přístup<br/> <br> **Adresy URL**<br> - *www.msftncsi.com* <br>-  *.Microsoft.com* <br> -  *.WindowsAzure.com* <br>-  *.microsoftonline.com* <br>-  *.windows.net* <br>**IP adresa**<br> - *20.190.128.0/18* <br> - *40.126.0.0/18* <br/></ul></ul>Zkuste znovu zaregistrovat po dokončení výše uvedené kroky pro řešení potíží
| **Antivirový software blokuje** | Pokud máte antivirový software nainstalovaný na serveru, přidejte pravidla vyloučení nezbytné pro následující soubory z antivirová kontrola: <br/><ui> <li> *CBengine.exe* <li> *CSC.exe*<li> Výchozí umístění je pomocnou složku *C:\Program Files\Microsoft Azure Recovery Services Agent\Scratch* <li> Složky Bin na *C:\Program Files\Microsoft Azure Recovery Services Agent\Bin*

### <a name="additional-recommendations"></a>Další doporučení
- Přejděte na *C:/Windows/Temp* a zkontrolujte, zda jsou více než 60 000 nebo než 65 000 vývojáři soubory s příponou TMP. Pokud existuje, odstraňte tyto soubory
- Zkontrolujte data a času počítači je spárování s místním časovém pásmu
- Zkontrolujte [následující](backup-configure-vault.md#verify-internet-access) lokalit jsou přidány do IE důvěryhodných serverů

### <a name="verifying-proxy-settings-for-windows"></a>Ověřuje se nastavení proxy serveru pro Windows

- Stáhněte si **psexec** z [zde](https://docs.microsoft.com/sysinternals/downloads/psexec)
- To `psexec -i -s "c:\Program Files\Internet Explorer\iexplore.exe"` z řádku se zvýšenými oprávněními:
- Tím se spustí *aplikace Internet Explorer* okna
- Přejděte na *nástroje* -> *Možnosti Internetu* -> *připojení* -> *nastavení místní sítě*
- Ověřte nastavení proxy serveru pro *systému* účtu
- Pokud je nakonfigurovaný žádný proxy server a podrobnosti o proxy serveru jsou k dispozici, pak odeberte podrobnosti
-   Pokud proxy server je nakonfigurován a podrobnosti o proxy nejsou správné, zajistěte *Proxy IP* a *port* správnost podrobností
- Zavřít *aplikace Internet Explorer*

## <a name="unable-to-download-vault-credential-file"></a>Nepovedlo se stáhnout soubor s přihlašovacími údaji trezoru

| Podrobnosti o chybě | Doporučené akce |
| ---     | ---    |
|Nepovedlo se stáhnout soubor s přihlašovacími údaji trezoru. (ID: 403) | <ul><li> Zkusit stáhnout přihlašovací údaje trezoru pomocí jiný prohlížeč nebo provádět následujících kroků: <ul><li> Spusťte Internet Exploreru, stiskněte klávesu F12. </li><li> Přejděte na **sítě** kartu k vymazání mezipaměti aplikace Internet Explorer a soubory cookie </li> <li> Aktualizujte stránku<br>(NEBO)</li></ul> <li> Zkontrolujte, jestli předplatné je zakázané nebo vypršela platnost<br>(NEBO)</li> <li> Zaškrtněte, pokud jakékoli pravidlo brány firewall blokuje stahování souborů přihlašovacích údajů trezoru <br>(NEBO)</li> <li> Ujistěte se, že nebyly vyčerpali limit pro trezor (50 počítačů pro každý trezor)<br>(NEBO)</li>  <li> Zajištění uživatel má požadované oprávnění Azure Backup pro stažení přihlašovacích údajů trezoru a server zaregistrujete pomocí úložiště, přečtěte si [článku](backup-rbac-rs-vault.md)</li></ul> |

## <a name="the-microsoft-azure-recovery-service-agent-was-unable-to-connect-to-microsoft-azure-backup"></a>Agent Microsoft Azure Recovery Services se nemohl připojit ke službě Microsoft Azure Backup

| Podrobnosti o chybě | Možné příčiny | Doporučené akce |
| ---     | ---     | ---    |
| **Chyba** <br /><ol><li>*Agent služby Microsoft Azure Recovery se nepodařilo připojit ke službě Microsoft Azure Backup. (ID: 100050) zkontrolujte nastavení sítě a ujistěte se, že se můžete připojit k Internetu*<li>*(407) Vyžaduje se ověření proxy serverem* |Blokuje připojení proxy server. |  <ul><li>Spuštění **IE** > **nastavení** > **Možnosti Internetu** > **zabezpečení**  >  **Internet**. Potom vyberte **vlastní úroveň** a posuňte, dokud se nezobrazí stahování části souboru. Vyberte **povolit**.<li>Budete také muset tyto weby přidat do aplikace IE [Důvěryhodné servery](https://docs.microsoft.com/azure/backup/backup-try-azure-backup-in-10-mins).<li>Změňte nastavení pro použití proxy serveru. Zadejte proxy server podrobnosti.<li> Pokud váš počítač má omezený přístup k Internetu, ujistěte se, že nastavení brány firewall na počítači nebo proxy umožňují tyto [adresy URL](backup-configure-vault.md#verify-internet-access) a [IP adresu](backup-configure-vault.md#verify-internet-access). <li>Pokud máte antivirový software nainstalovaný na serveru, vylučte následující soubory z antivirová kontrola. <ul><li>CBEngine.exe (namísto dpmra.exe).<li>CSC.exe (související s rozhraní .NET Framework). Existuje CSC.exe pro každou verzi rozhraní .NET, který je nainstalován na serveru. Vylučte soubory CSC.exe, které jsou vázané na všechny verze rozhraní .NET framework na příslušném serveru. <li>Dočasné umístění složky nebo mezipaměť. <br>*Výchozí umístění pro odkládací složce nebo cestu k umístění mezipaměti je C:\Program Files\Microsoft Azure Recovery Services Agent\Scratch*.<li>Složky bin C:\Program Files\Microsoft Azure Recovery Services Agent\Bin



## <a name="failed-to-set-the-encryption-key-for-secure-backups"></a>Nepovedlo se nastavit šifrovací klíč pro bezpečné zálohy

| Podrobnosti o chybě | Možné příčiny | Doporučené akce |
| ---     | ---     | ---    |
| **Chyba** <br />*Nepovedlo se nastavit šifrovací klíč pro bezpečné zálohy. Aktivace nebylo zcela úspěšné, ale šifrovací heslo se uložila do následujícího souboru*. |<li>Server je už zaregistrovaný s jiný trezor.<li>Během konfigurace byla poškozena přístupové heslo.| Zrušit registraci serveru v trezoru a znovu zaregistrujte novou heslu.

## <a name="the-activation-did-not-complete-successfully"></a>Aktivace nebyla úspěšně dokončena.

| Podrobnosti o chybě | Možné příčiny | Doporučené akce |
|---------|---------|---------|
|**Chyba** <br />*Aktivace nebyla úspěšně dokončena. Aktuální operace selhala kvůli vnitřní chybě služby [0x1FC07]. Po nějaké době operaci opakujte. Pokud se problém nevyřeší, kontaktujte prosím podporu Microsoftu*     | <li> Odkládací složka se nachází na svazku, který nemá dostatek místa. <li> Pomocnou složku byl přesunut nesprávně do jiného umístění. <li> Chybí soubor OnlineBackup.KEK.         | <li>Upgrade na [nejnovější verzi](https://aka.ms/azurebackup_agent) agenta MARS.<li>Přesuňte pomocné umístění složky nebo mezipaměti na svazek s volné místo odpovídající 5 až 10 % celkové velikosti dat zálohy. Správně přesuňte umístění mezipaměti, použijte postup v [dotazy týkající se Azure Backup Agent](https://docs.microsoft.com/azure/backup/backup-azure-file-folder-backup-faq#backup).<li> Ujistěte se, že soubor OnlineBackup.KEK je k dispozici. <br>*Výchozí umístění pro odkládací složce nebo cestu k umístění mezipaměti je C:\Program Files\Microsoft Azure Recovery Services Agent\Scratch*.        |

## <a name="encryption-passphrase-not-correctly-configured"></a>Šifrovací heslo není správně nakonfigurované

| Podrobnosti o chybě | Možné příčiny | Doporučené akce |
|---------|---------|---------|
|**Chyba** <br />*Došlo k chybě 34506. Šifrovací heslo uložené na tomto počítači není správně nakonfigurovaný*.    | <li> Odkládací složka se nachází na svazku, který nemá dostatek místa. <li> Pomocnou složku byl přesunut nesprávně do jiného umístění. <li> Chybí soubor OnlineBackup.KEK.        | <li>Upgrade na [nejnovější verzi](https://aka.ms/azurebackup_agent) agenta MARS.<li>Přesuňte pomocnou složku nebo umístění mezipaměti na svazek s volné místo odpovídající 5 – 10 % celkové velikosti dat zálohy. Správně přesuňte umístění mezipaměti, použijte postup v [dotazy týkající se Azure Backup Agent](https://docs.microsoft.com/azure/backup/backup-azure-file-folder-backup-faq#backup).<li> Ujistěte se, že soubor OnlineBackup.KEK je k dispozici. <br>*Výchozí umístění pro odkládací složce nebo cestu k umístění mezipaměti je C:\Program Files\Microsoft Azure Recovery Services Agent\Scratch*.         |


## <a name="backups-dont-run-according-to-the-schedule"></a>Zálohování nelze spustit podle plánu
Pokud naplánovaných záloh není aktivují automaticky, zatímco ruční zálohy budou fungovat bez problémů, zkuste následující akce:

- Ujistěte se, že plán zálohování Windows serveru nejsou v konfliktu se soubory a složky, plán zálohování Azure.

- Zkontrolujte stav Online zálohování je nastavená na **povolit**. Chcete-li ověřit stav provádění níže:

  - Otevřít **Plánovač úloh** a rozbalte **Microsoft**a vyberte **Online zálohování**.
  - Dvakrát klikněte na panel **Microsoft OnlineBackup**a přejděte na **triggery** kartu.
  - Ověření, pokud je stav nastaven **povoleno**. Pokud tomu tak není, vyberte **upravit** > **povoleno** zaškrtávací políčko a klikněte na tlačítko **OK**.

- Zkontrolujte uživatelský účet vybrané ke spuštění úkolu je buď **systému** nebo **skupiny Local Administrators** na serveru. Chcete-li ověřit, že uživatelský účet, přejděte **Obecné** kartě a zaškrtněte **zabezpečení** možnosti.

- Ujistěte se, že je nainstalovaný PowerShell 3.0 nebo vyšší na serveru. Pokud chcete zkontrolovat verzi prostředí PowerShell, spusťte následující příkaz a ověřte, zda *hlavní* číslo verze je roven nebo větší než 3.

  `$PSVersionTable.PSVersion`

- Zkontrolujte následující cesta je součástí *PSMODULEPATH* proměnné prostředí

  `<MARS agent installation path>\Microsoft Azure Recovery Services Agent\bin\Modules\MSOnlineBackup`

- Pokud zásady spouštění prostředí PowerShell pro *LocalMachine* je nastavena na s omezeným přístupem, rutina prostředí PowerShell, který se aktivuje úloha zálohování může selhat. Spuštěním následujících příkazů v režimu se zvýšenými oprávněními ke kontrole a nastavte zásady spouštění buď *Unrestricted* nebo *RemoteSigned*

  `PS C:\WINDOWS\system32> Get-ExecutionPolicy -List`

  `PS C:\WINDOWS\system32> Set-ExecutionPolicy Unrestricted`

- Zajistili zde nejsou žádné chybějící nebo poškozený **PowerShell** modulu **MSonlineBackup**. V případě, že existuje všechny chybějící nebo poškozené soubory, k vyřešení tohoto problému proveďte níže:

  - Z libovolného počítače s agenta MARS, který pracuje správně, zkopírujte složku MSOnlineBackup z *(C:\Program Files\Microsoft Azure Recovery Services Agent\bin\Modules)* cestu.
  - To vložte problematických počítače ve stejné cestě *(C:\Program Files\Microsoft Azure Recovery Services Agent\bin\Modules)* .
  - Pokud **MSOnlineBackup** složka je již existoval v počítači, vložte nebo nahraďte soubory obsahu uvnitř.


> [!TIP]
> Aby bylo zajištěno, že změny jsou konzistentní, restartujte server po provedení výše uvedených kroků.


## <a name="troubleshoot-restore-issues"></a>Řešení potíží s obnovení

Azure Backup nemusí úspěšně připojit svazek pro obnovení, dokonce i po několika minutách. Během procesu může také zobrazit chybové zprávy. Pokud chcete začít, obvykle obnovení, postupujte takto:

1.  Zrušte proces probíhající připojení v případě, že je spuštěné pro několik minut.

2.  Podívejte se, pokud jste na nejnovější verzi agenta zálohování. Chcete-li zjistit, na verzi, **akce** podokně konzoly MARS, vyberte **o Microsoft Azure Recovery Services Agent**. Ujistěte se, že **verze** počet se rovná nebo je vyšší než verze uvedená v [v tomto článku](https://go.microsoft.com/fwlink/?linkid=229525). Nejnovější verzi můžete stáhnout [tady](https://go.microsoft.com/fwLink/?LinkID=288905).

3.  Přejděte na **Správce zařízení** > **řadiče úložiště**a vyhledejte **iniciátoru iSCSI od Microsoftu**. Pokud ji mohli najít, přejděte přímo ke kroku 7.

4.  Pokud nemůžete najít služba iniciátoru iSCSI společnosti Microsoft, pokuste se najít položku **Správce zařízení** > **řadiče úložiště** volá **neznámé zařízení**, s ID hardwaru **ROOT\ISCSIPRT**.

5.  Klikněte pravým tlačítkem na **neznámé zařízení**a vyberte **aktualizace ovladače**.

6.  Aktualizovat ovladač tak, že vyberete možnost **vyhledat automaticky aktualizovaný ovladač**. Dokončení aktualizace by se měla změnit **neznámé zařízení** k **iniciátoru iSCSI od Microsoftu**, jak je znázorněno níže.

    ![Snímek obrazovky s Azure Backup Správce zařízení, se zvýrazněným řadiče úložiště](./media/backup-azure-restore-windows-server/UnknowniSCSIDevice.png)

7.  Přejděte na **Správce úloh** > **služby (místní počítač)**  > **služba iniciátoru iSCSI společnosti Microsoft**.

    ![Snímek obrazovky z Azure Backup Správce úloh, se zvýrazněným služby (místní)](./media/backup-azure-restore-windows-server/MicrosoftInitiatorServiceRunning.png)

8.  Restartujte službu iniciátoru iSCSI společnosti Microsoft. Chcete-li to provést, klikněte pravým tlačítkem na službu, vyberte **Zastavit**znovu kliknete pravým tlačítkem a vyberte **Start**.

9.  Opakujte obnovení pomocí [ **rychlé obnovení**](backup-instant-restore-capability.md).

Pokud obnovení pořád selže, restartujte server nebo klienta. Pokud nechcete, aby k restartování nebo obnovení selže i i po restartování serveru, zkuste obnovení v jiném počítači. Postupujte podle kroků v [v tomto článku](backup-azure-restore-windows-server.md#use-instant-restore-to-restore-data-to-an-alternate-machine).

## <a name="need-help-contact-support"></a>Potřebujete pomoc? Kontaktujte podporu
Pokud stále potřebujete pomoc, [obraťte se na podporu](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) pro rychlé vyřešení problému.

## <a name="next-steps"></a>Další postup
* Přečtěte si víc o [zálohování Windows serveru pomocí agenta Azure Backup](tutorial-backup-windows-server-to-azure.md).
* Potřebujete-li obnovit zálohu, použijte tento článek k [obnovení souborů na počítač se systémem Windows](backup-azure-restore-windows-server.md).
