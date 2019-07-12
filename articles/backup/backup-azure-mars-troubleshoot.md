---
title: Řešení potíží s agentem Azure Backup
description: Řešení potíží instalace a registrace agenta Azure Backup
services: backup
author: saurabhsensharma
manager: sivan
ms.service: backup
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: saurse
ms.openlocfilehash: 437b175efad081b8382d80be8427aa074920fd3e
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/09/2019
ms.locfileid: "67705057"
---
# <a name="troubleshoot-the-microsoft-azure-recovery-services-mars-agent"></a>Řešení potíží s agentem Microsoft Azure Recovery Services (MARS)

Tento článek popisuje, jak pro vyřešení chyb, může se zobrazit při registraci, zálohování, konfigurace a obnovení.

## <a name="basic-troubleshooting"></a>Základní řešení potíží

Doporučujeme, abyste před zahájením řešení potíží s Microsoft Azure Recovery Services (MARS) agent zkontrolujte následující:

- [Zkontrolujte aktuální agenta MARS](https://go.microsoft.com/fwlink/?linkid=229525&clcid=0x409).
- [Ujistěte se, máte síťové připojení mezi Azure a agenta MARS](https://aka.ms/AB-A4dp50).
- Ujistěte se, že je spuštěná MARS (v konzole služby). Pokud je potřeba restartovat a zkuste operaci zopakovat.
- [Zajištění 5 až 10 % volného volné místo je k dispozici v umístění odkládací složky](https://aka.ms/AB-AA4dwtt).
- [Zkontrolujte, zda jiný proces nebo antivirový software je zasahovala do Azure Backup](https://aka.ms/AB-AA4dwtk).
- Naplánované zálohování se nezdaří, ale ruční zálohování funguje, najdete v článku [zálohy nemusíte spouštět automaticky podle plánu](https://aka.ms/ScheduledBackupFailManualWorks).
- Ujistěte se, že váš operační systém má nejnovější aktualizace.
- [Zajištění ze zálohování vyloučena nepodporované disky a soubory s nepodporované atributy](backup-support-matrix-mars-agent.md#supported-drives-or-volumes-for-backup).
- Zkontrolujte, že čas na chráněných systémových nastaven na správném časovém pásmu.
- [Ujistěte se rozhraní .NET Framework 4.5.2 nebo novější nainstalován na serveru](https://www.microsoft.com/download/details.aspx?id=30653).
- Pokud se snažíte zaregistrovat server do trezoru:
  - Ujistěte se daný agent nebude odinstalován na serveru a že byla odstraněna z portálu.
  - Použijte stejné heslo, který byl zpočátku použije k registraci serveru.
- U zálohování offline zkontrolujte, jestli že prostředí Azure PowerShell 3.7.0 je nainstalované na zdroji i v počítači kopii, před zahájením zálohování.
- Pokud agenta zálohování běží na virtuálním počítači Azure, přečtěte si téma [v tomto článku](https://aka.ms/AB-AA4dwtr).

## <a name="invalid-vault-credentials-provided"></a>Neplatné přihlašovací údaje úložiště k dispozici

**Chybová zpráva**: Zadané neplatné přihlašovací údaje trezoru. Soubor je poškozený nebo nemá mít nejnovější přihlašovací údaje přidružené ke službě obnovení. (ID: 34513)

| Příčina | Doporučené akce |
| ---     | ---    |
| **Neplatných přihlašovacích údajů trezoru** <br/> <br/> Soubory přihlašovacích údajů trezoru je pravděpodobně být poškozen nebo vypršela. (Například jejich může být stažené víc než 48 hodin, než čas registrace.)| Stáhněte si nové přihlašovací údaje z trezoru služby Recovery Services na webu Azure portal. (Přejděte ke kroku 6 v [stáhnout agenta MARS](https://docs.microsoft.com/azure/backup/backup-configure-vault#download-the-mars-agent) části.) Pak proveďte tyto kroky, podle potřeby: <ul><li> Pokud jste již nainstalován a registrován MARS, otevřete konzolu pro Microsoft Azure Backup Agent konzoly MMC a potom vyberte **zaregistrovat Server** v **akce** k dokončení registrace se nové podokno přihlašovací údaje. <br/> <li> Pokud se nová instalace nezdaří, zkuste přeinstalovat novými přihlašovacími údaji.</ul> **Poznámka:** Pokud byly staženy soubory s více přihlašovacích údajů trezoru, pouze nejnovější soubor je platný pro další 48 hodin. Doporučujeme vám, stáhněte si nový soubor přihlašovacích údajů trezoru.
| **Proxy server nebo brána firewall neblokuje registraci** <br/>or <br/>**Bez připojení k Internetu** <br/><br/> Pokud váš počítač nebo proxy server má omezené připojení k Internetu a nemusíte zajistit přístup pro potřeby adresy URL, registrace selže.| Proveďte tyto kroky:<br/> <ul><li> Práce s pracovníky IT, ujistěte se, že systém má připojení k Internetu.<li> Pokud nemáte proxy server, ujistěte se, že není vybraná možnost proxy serveru, při registraci agenta. [Zkontrolujte vaše nastavení proxy serveru](#verifying-proxy-settings-for-windows).<li> Pokud máte brány firewall nebo proxy server, spolupracujte s týmem sítě, aby tyto adresy URL a IP adresy mají přístup:<br/> <br> **Adresy URL**<br> `www.msftncsi.com` <br> .Microsoft.com <br> .WindowsAzure.com <br> .microsoftonline.com <br> .windows.net <br>**IP adresy**<br>  20.190.128.0/18 <br>  40.126.0.0/18 <br/></ul></ul>Zkuste registraci znovu po dokončení předchozích kroků řešení potíží.
| **Antivirový software blokuje registrace** | Pokud máte antivirový software nainstalovaný na serveru, přidejte pravidla vyloučení potřeby antivirovou kontrolu pro tyto soubory a složky: <br/><ui> <li> CBengine.exe <li> CSC.exe<li> Odkládací složky. Jeho výchozí umístění je C:\Program Files\Microsoft Azure Recovery Services Agent\Scratch. <li> Složky bin na C:\Program Files\Microsoft Azure Recovery Services Agent\Bin.

### <a name="additional-recommendations"></a>Další doporučení
- Přejděte na C:/Windows/Temp a zkontrolujte, zda jsou více než 60 000 nebo než 65 000 vývojáři soubory s příponou TMP. Pokud existuje, odstraňte tyto soubory.
- Zkontrolujte místní časové pásmo počítače datum a čas shoda.
- Zajištění [tyto weby](backup-configure-vault.md#verify-internet-access) se přidají do vašeho důvěryhodných webů v Internet Exploreru.

### <a name="verifying-proxy-settings-for-windows"></a>Ověřuje se nastavení proxy serveru pro Windows

1. Stáhněte si nástroje PsExec z [Sysinternals](https://docs.microsoft.com/sysinternals/downloads/psexec) stránky.
1. Spustit `psexec -i -s "c:\Program Files\Internet Explorer\iexplore.exe"` z příkazového řádku se zvýšenými oprávněními.

   Tento příkaz se otevře aplikace Internet Explorer.
1. Přejděte na **nástroje** > **Možnosti Internetu** > **připojení** > **nastavení místní sítě**.
1. Zkontrolujte nastavení proxy serveru pro systémový účet.
1. Pokud je nakonfigurovaný žádný proxy server a podrobnosti o proxy serveru jsou k dispozici, odeberte podrobnosti.
1. Pokud je nakonfigurován proxy server a podrobnosti proxy nejsou správné, ujistěte se, **Proxy IP** a **Port** správnost podrobností.
1. Zavřete aplikaci Internet Explorer.

## <a name="unable-to-download-vault-credential-file"></a>Nepovedlo se stáhnout soubor s přihlašovacími údaji trezoru

| Chyba   | Doporučené akce |
| ---     | ---    |
|Nepovedlo se stáhnout soubor s přihlašovacími údaji trezoru. (ID: 403) | <ul><li> Zkusit stáhnout přihlašovací údaje trezoru pomocí jiný prohlížeč, nebo proveďte tyto kroky: <ul><li> Spusťte aplikaci Internet Explorer. Vyberte F12. </li><li> Přejděte **sítě** kartu a vymazat mezipaměť a soubory cookie. </li> <li> Aktualizujte stránku.<br></li></ul> <li> Zkontrolujte, jestli předplatné je zakázané nebo vypršela platnost.<br></li> <li> Zkontrolujte, zda jakékoli pravidlo brány firewall neblokuje stahování. <br></li> <li> Zajistěte, aby že nebyly vyčerpali limit pro trezor (50 počítačů pro každý trezor).<br></li>  <li> Ujistěte se, že uživatel má oprávnění Azure Backup, která jsou nutná ke stažení přihlašovacích údajů trezoru a zaregistrujte server v trezoru. Zobrazit [Use Role-Based řízení přístupu ke správě body obnovení Azure Backup](backup-rbac-rs-vault.md).</li></ul> |

## <a name="the-microsoft-azure-recovery-service-agent-was-unable-to-connect-to-microsoft-azure-backup"></a>Agent Microsoft Azure Recovery Services se nemohl připojit ke službě Microsoft Azure Backup

| Chyba  | Možná příčina | Doporučené akce |
| ---     | ---     | ---    |
| <br /><ul><li>Agent služby Microsoft Azure Recovery se nepodařilo připojit ke službě Microsoft Azure Backup. (ID: 100050) zkontrolujte nastavení sítě a ujistěte se, že se můžete připojit k Internetu.<li>Vyžadováno ověřování proxy serveru (407). |Proxy Server blokuje připojení. |  <ul><li>V Internet Exploreru přejděte na **nástroje** > **Možnosti Internetu** > **zabezpečení** > **Internet**. Vyberte **vlastní úroveň** a přejděte dolů k položce **stahování souborů** oddílu. Vyberte **povolit**.<p>Budete taky muset přidat [adresy URL a IP adresy](backup-configure-vault.md#verify-internet-access) Důvěryhodné servery v Internet Exploreru.<li>Změňte nastavení pro použití proxy serveru. Zadejte proxy server podrobnosti.<li> Pokud váš počítač má omezený přístup k Internetu, ujistěte se, že nastavení brány firewall na počítači nebo proxy umožňují tyto [adresy URL a IP adresy](backup-configure-vault.md#verify-internet-access). <li>Pokud máte antivirový software nainstalovaný na serveru, vylučte z antivirovou kontrolu tyto soubory: <ul><li>CBEngine.exe (namísto dpmra.exe).<li>CSC.exe (související s rozhraní .NET Framework). Existuje CSC.exe pro všechny verze rozhraní .NET Framework nainstalované na serveru. Vylučte soubory CSC.exe pro všechny verze rozhraní .NET Framework na příslušném serveru. <li>Pomocné umístění složky nebo mezipaměť. <br>Výchozí umístění pro odkládací složce nebo cestu k mezipaměti je C:\Program Files\Microsoft Azure Recovery Services Agent\Scratch.<li>Složky bin na C:\Program Files\Microsoft Azure Recovery Services Agent\Bin.


## <a name="failed-to-set-the-encryption-key-for-secure-backups"></a>Nepovedlo se nastavit šifrovací klíč pro bezpečné zálohy

| Chyba | Možné příčiny | Doporučené akce |
| ---     | ---     | ---    |
| <br />Nepovedlo se nastavit šifrovací klíč pro bezpečné zálohy. Aktivace nebylo zcela úspěšné, ale šifrovací heslo se uložila do následujícího souboru. |<li>Server je už zaregistrovaný s jiný trezor.<li>Během konfigurace byla poškozena přístupové heslo.| Zrušit registraci serveru trezor a zaregistrujte ho znovu pomocí nového přístupového hesla.

## <a name="the-activation-did-not-complete-successfully"></a>Aktivace nebyla úspěšně dokončena.

| Chyba  | Možné příčiny | Doporučené akce |
|---------|---------|---------|
|<br />Aktivace nebyla úspěšně dokončena. Aktuální operace selhala kvůli vnitřní chybě služby [0x1FC07]. Po nějaké době operaci opakujte. Pokud se problém opakuje, obraťte se prosím na podporu Microsoftu.     | <li> Odkládací složka se nachází na svazku, který nemá dostatek místa. <li> Nesprávně se přesunulo pomocnou složku. <li> Chybí soubor OnlineBackup.KEK.         | <li>Upgrade na [nejnovější verzi](https://aka.ms/azurebackup_agent) agenta MARS.<li>Přesuňte pomocné umístění složky nebo mezipaměti na svazek s volným místem, který je mezi 5 a 10 % celkové velikosti dat zálohy. Správně přesuňte umístění mezipaměti, použijte postup v [běžné otázky týkající se zálohování souborů a složek](https://docs.microsoft.com/azure/backup/backup-azure-file-folder-backup-faq#manage-the-backup-cache-folder).<li> Ujistěte se, že soubor OnlineBackup.KEK je k dispozici. <br>*Výchozí umístění pro odkládací složce nebo cestu k mezipaměti je C:\Program Files\Microsoft Azure Recovery Services Agent\Scratch*.        |

## <a name="encryption-passphrase-not-correctly-configured"></a>Šifrovací heslo není správně nakonfigurované

| Chyba  | Možné příčiny | Doporučené akce |
|---------|---------|---------|
| <br />Došlo k chybě 34506. Šifrovací heslo uložené na tomto počítači není správně nakonfigurovaný.    | <li> Odkládací složka se nachází na svazku, který nemá dostatek místa. <li> Nesprávně se přesunulo pomocnou složku. <li> Chybí soubor OnlineBackup.KEK.        | <li>Upgrade na [nejnovější verzi](https://aka.ms/azurebackup_agent) agenta MARS.<li>Přesuňte pomocné umístění složky nebo mezipaměti na svazek s volným místem, který je mezi 5 a 10 % celkové velikosti dat zálohy. Správně přesuňte umístění mezipaměti, použijte postup v [běžné otázky týkající se zálohování souborů a složek](https://docs.microsoft.com/azure/backup/backup-azure-file-folder-backup-faq#manage-the-backup-cache-folder).<li> Ujistěte se, že soubor OnlineBackup.KEK je k dispozici. <br>*Výchozí umístění pro odkládací složce nebo cestu k mezipaměti je C:\Program Files\Microsoft Azure Recovery Services Agent\Scratch*.         |


## <a name="backups-dont-run-according-to-schedule"></a>Zálohy nemusíte spouštět automaticky podle plánu
Pokud nechcete získat automaticky spustí naplánované zálohy, ale ruční zálohy budou fungovat správně, zkuste následující akce:

- Ujistěte se, že plán zálohování Windows serveru není v konfliktu s Azure plán zálohování souborů a složek.

- Zkontrolujte stav online zálohování je nastavená na **povolit**. Pokud chcete ověřit stav, proveďte tyto kroky:

  1. V Plánovači úloh, rozbalte **Microsoft** a vyberte **Online zálohování**.
  1. Dvakrát klikněte na **Microsoft OnlineBackup** a Přejít **aktivační události** kartu.
  1. Zaškrtněte, pokud je stav nastaven **povoleno**. Pokud tomu tak není, vyberte **upravit**vyberte **povoleno**a pak vyberte **OK**.

- Zkontrolujte uživatelský účet vybrané ke spuštění úkolu je buď **systému** nebo **skupiny Local Administrators** na serveru. Chcete-li ověřit, že uživatelský účet, přejděte **Obecné** kartě a zaškrtněte **zabezpečení** možnosti.

- Ujistěte se, že je nainstalovaný PowerShell 3.0 nebo vyšší na serveru. Pokud chcete zkontrolovat verzi prostředí PowerShell, spusťte tento příkaz a ověřte, zda `Major` je číslo verze 3 nebo novější:

  `$PSVersionTable.PSVersion`

- Zkontrolujte tato cesta je součástí `PSMODULEPATH` proměnné prostředí:

  `<MARS agent installation path>\Microsoft Azure Recovery Services Agent\bin\Modules\MSOnlineBackup`

- Pokud zásady spouštění prostředí PowerShell pro `LocalMachine` je nastavena na s omezeným přístupem, rutina prostředí PowerShell, který se aktivuje úloha zálohování může selhat. Tyto příkazy spustit v režimu se zvýšenými oprávněními ke kontrole a nastavte zásady spouštění buď `Unrestricted` nebo `RemoteSigned`:

  `PS C:\WINDOWS\system32> Get-ExecutionPolicy -List`

  `PS C:\WINDOWS\system32> Set-ExecutionPolicy Unrestricted`

- Ujistěte se, že nejsou soubory MSOnlineBackup modulu žádné chybějící nebo poškozené prostředí PowerShell. Pokud existují žádné chybějící nebo poškozené soubory, proveďte tyto kroky:

  1. Z libovolného počítače, který má agenta MARS, který pracuje správně zkopírujte z C:\Program Files\Microsoft Azure Recovery Services Agent\bin\Modules MSOnlineBackup složku.
  1. Na počítači, problematické Vložte zkopírované soubory ve stejném umístění složky (C:\Program Files\Microsoft Azure Recovery Services Agent\bin\Modules).

     Pokud je již k MSOnlineBackup složce na počítači, vložte do něj soubory nebo nahradit všechny existující soubory.


> [!TIP]
> Ujistěte se, že změny jsou konzistentní, restartujte server po provedení předchozích kroků.


## <a name="troubleshoot-restore-problems"></a>Řešení problémů obnovení

Azure Backup nemusí úspěšně připojit svazek pro obnovení, dokonce i po několika minutách. A zobrazí se chybové zprávy během procesu. Pokud chcete začít, obvykle obnovení, proveďte tyto kroky:

1.  Zrušte procesu připojení, pokud je spuštěné pro několik minut.

2.  Zaškrtněte, pokud máte nejnovější verzi agenta zálohování. Pokud chcete zkontrolovat verzi, na **akce** podokně konzoly MARS vyberte **o Microsoft Azure Recovery Services Agent**. Ujistěte se, že **verze** počet se rovná nebo je vyšší než verze uvedená v [v tomto článku](https://go.microsoft.com/fwlink/?linkid=229525). Vyberte tento odkaz [stáhnout nejnovější verzi](https://go.microsoft.com/fwLink/?LinkID=288905).

3.  Přejděte na **Správce zařízení** > **řadiče úložiště** a vyhledejte **iniciátoru iSCSI od Microsoftu**. Pokud ji najít, přejděte přímo ke kroku 7.

4.  Pokud je služba iniciátoru iSCSI společnosti Microsoft nelze najít, pokuste se najít položku **Správce zařízení** > **řadiče úložiště** s názvem **neznámé zařízení** s ID hardwaru **ROOT\ISCSIPRT**.

5.  Klikněte pravým tlačítkem na **neznámé zařízení** a vyberte **aktualizace ovladače**.

6.  Aktualizovat ovladač tak, že vyberete možnost **vyhledat automaticky aktualizovaný ovladač**. Tato aktualizace by se měla změnit **neznámé zařízení** k **iniciátoru iSCSI od Microsoftu**:

    ![Snímek obrazovky s Azure Backup Správce zařízení, se zvýrazněným řadiče úložiště](./media/backup-azure-restore-windows-server/UnknowniSCSIDevice.png)

7.  Přejděte na **Správce úloh** > **služby (místní počítač)**  > **služba iniciátoru iSCSI společnosti Microsoft**:

    ![Snímek obrazovky z Azure Backup Správce úloh, se zvýrazněným služby (místní)](./media/backup-azure-restore-windows-server/MicrosoftInitiatorServiceRunning.png)

8.  Restartujte službu iniciátoru iSCSI společnosti Microsoft. Chcete-li to provést, klikněte pravým tlačítkem na službu a vyberte **Zastavit**. Klikněte pravým tlačítkem znovu a vyberte **Start**.

9.  Opakujte obnovení pomocí [rychlé obnovení](backup-instant-restore-capability.md).

Pokud obnovení pořád selže, restartujte server nebo klienta. Pokud nechcete, aby k restartování, nebo pokud obnovení pořád selže i po restartování serveru, zkuste [obnovení z jiného počítače](backup-azure-restore-windows-server.md#use-instant-restore-to-restore-data-to-an-alternate-machine).

## <a name="need-help-contact-support"></a>Potřebujete pomoct? Kontaktujte podporu
Pokud stále potřebujete pomoc, [obraťte se na podporu](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) získat rychlé vyřešení problému.

## <a name="next-steps"></a>Další postup
* Přečtěte si víc o [zálohování Windows serveru s agentem Azure Backup](tutorial-backup-windows-server-to-azure.md).
* Pokud potřebujete obnovit zálohu, najdete v článku [obnovit soubory do počítače s Windows](backup-azure-restore-windows-server.md).
