---
title: Řešení potíží s agentem Azure Backup
description: Řešení potíží instalace a registrace agenta Azure Backup
services: backup
author: saurabhsensharma
manager: shreeshd
ms.service: backup
ms.topic: conceptual
ms.date: 7/25/2018
ms.author: saurse
ms.openlocfilehash: 2c8978cfba8fc56d4dbc565cb3a91c75d9d54679
ms.sourcegitcommit: cb61439cf0ae2a3f4b07a98da4df258bfb479845
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/05/2018
ms.locfileid: "43700191"
---
# <a name="troubleshoot-microsoft-azure-recovery-services-mars-agent-issues"></a>Řešení potíží s agentem Microsoft Azure Recovery Services (MARS)
## <a name="recommended-steps"></a>Doporučené kroky
Přečtěte si tento článek pro vyřešení chyb během konfigurace, registraci, zálohování a obnovení pomocí agenta MARS.

## <a name="invalid-vault-credentials-provided-the-file-is-either-corrupted-or-does-not-have-the-latest-credentials-associated-with-recovery-service"></a>Neplatné zadané přihlašovací údaje trezoru. Soubor je poškozený nebo nemá mít nejnovější přihlašovací údaje přidružené ke službě obnovení.
| Podrobnosti o chybě | Možné příčiny | Doporučené akce |
| ---     | ---     | ---    |
| **Chyba** </br> *Zadané neplatné přihlašovací údaje trezoru. Soubor je poškozený nebo nemá mít nejnovější přihlašovací údaje přidružené ke službě obnovení. (ID: 34513)* | <ul><li> Přihlašovací údaje trezoru jsou neplatné (to znamená, že byly staženy víc než 48 hodin, než čas registrace).<li>MARS Agent se nemůže stahovat soubory do svého adresáře Windows Temp. <li>Přihlašovací údaje trezoru jsou v umístění v síti. <li>Protokol TLS 1.0 je zakázaný.<li> Nakonfigurovaný proxy server blokuje připojení. <br> |  <ul><li>Stáhněte si nové přihlašovací údaje trezoru.<li>Přejděte na **Možnosti Internetu** > **zabezpečení** > **Internet**. V dalším kroku vyberte **vlastní úroveň**a posuňte, dokud se nezobrazí stahování části souboru. Potom vyberte **povolit**.<li>Budete také muset přidejte web do [Důvěryhodné servery](https://docs.microsoft.com/azure/backup/backup-try-azure-backup-in-10-mins#network-and-connectivity-requirements).<li>Změňte nastavení pro použití proxy serveru. Zadejte proxy server podrobnosti. <li> Datum a čas dodržovat váš počítač.<li>Přejděte na C:/Windows/Temp a zkontrolujte, zda jsou více než 60 000 nebo než 65 000 vývojáři soubory s příponou TMP. Pokud existuje, odstraňte tyto soubory.<li>Můžete ověřit balíček SDP běží na serveru. Pokud obdržíte chybu s informacemi o tom, že stahování souborů nejsou povoleny, je pravděpodobné, že existuje velký počet souborů v adresáři C:/Windows/Temp.<li>Ujistěte se, že máte nainstalované rozhraní .NET framework 4.6.2. <li>Pokud jste zakázali protokol TLS 1.0 z důvodu dodržování PCI, podívejte se na to [stránka o řešení problémů](https://support.microsoft.com/help/4022913). <li>Pokud máte antivirový software nainstalovaný na serveru, vylučte z antivirová kontrola následující soubory: <ul><li>CBengine.exe<li>CSC.exe, který má vztah k rozhraní .NET Framework. Existuje CSC.exe pro každou verzi rozhraní .NET, který je nainstalován na serveru. Vylučte soubory CSC.exe, které jsou vázané na všechny verze rozhraní .NET framework na příslušném serveru. <li>Dočasné umístění složky nebo mezipaměť. <br>*Výchozí umístění pro odkládací složce nebo cestu k umístění mezipaměti je C:\Program Files\Microsoft Azure Recovery Services Agent\Scratch*.

## <a name="the-microsoft-azure-recovery-service-agent-was-unable-to-connect-to-microsoft-azure-backup"></a>Agent služby Microsoft Azure Recovery se nepodařilo připojit ke službě Microsoft Azure Backup

| Podrobnosti o chybě | Možné příčiny | Doporučené akce |
| ---     | ---     | ---    |
| **Chyba** </br><ol><li>*Agent služby Microsoft Azure Recovery se nepodařilo připojit ke službě Microsoft Azure Backup. (ID: 100050) Zkontrolujte nastavení sítě a ujistěte se, že se můžete připojit k Internetu*<li>*Vyžadováno ověřování proxy serveru (407)* |Blokuje připojení proxy server. |  <ul><li>Přejděte do **Možnosti Internetu** > **zabezpečení** > **Internet**. Potom vyberte **vlastní úroveň** a posuňte, dokud se nezobrazí stahování části souboru. Vyberte **Povolit**.<li>Budete také muset přidejte web do [Důvěryhodné servery](https://docs.microsoft.com/azure/backup/backup-try-azure-backup-in-10-mins#network-and-connectivity-requirements).<li>Změňte nastavení pro použití proxy serveru. Zadejte proxy server podrobnosti. <li>Pokud máte antivirový software nainstalovaný na serveru, vylučte následující soubory z antivirová kontrola. <ul><li>CBEngine.exe (namísto dpmra.exe).<li>CSC.exe (související s rozhraní .NET Framework). Existuje CSC.exe pro každou verzi rozhraní .NET, který je nainstalován na serveru. Vylučte soubory CSC.exe, které jsou vázané na všechny verze rozhraní .NET framework na příslušném serveru. <li>Dočasné umístění složky nebo mezipaměť. <br>*Výchozí umístění pro odkládací složce nebo cestu k umístění mezipaměti je C:\Program Files\Microsoft Azure Recovery Services Agent\Scratch*.

## <a name="failed-to-set-the-encryption-key-for-secure-backups"></a>Nepovedlo se nastavit šifrovací klíč pro bezpečné zálohy

| Podrobnosti o chybě | Možné příčiny | Doporučené akce |
| ---     | ---     | ---    |      
| **Chyba** </br>*Nepovedlo se nastavit šifrovací klíč pro bezpečné zálohy aktivace nebylo zcela úspěšné, ale šifrovací heslo se uložila do následujícího souboru*. |<li>Server je již zaregistrován jiný trezor.<li>Během konfigurace byla poškozena heslo| Zrušit registraci serveru v trezoru a znovu zaregistrujte novou heslu.

## <a name="the-activation-did-not-complete-successfully-the-current-operation-failed-due-to-an-internal-service-error-0x1fc07"></a>Aktivace nebyla úspěšně dokončena. Aktuální operace selhala kvůli vnitřní chybě služby [0x1FC07]

| Podrobnosti o chybě | Možné příčiny | Doporučené akce |
|---------|---------|---------|
|**Chyba** </br><ol>*Aktivace nebyla úspěšně dokončena. Aktuální operace selhala kvůli vnitřní chybě služby [0x1FC07]. Po nějaké době zkuste operaci zopakovat. Pokud se problém nevyřeší, kontaktujte prosím podporu Microsoftu*     | <li> Odkládací složka se nachází na svazku, který nemá dostatek místa. <li> Pomocnou složku byl přesunut nesprávně do jiného umístění. <li> Chybí soubor OnlineBackup.KEK.         | <li>Upgrade na [nejnovější verzi](http://aka.ms/azurebackup_agent) agenta MARS.<li>Přesuňte pomocné umístění složky nebo mezipaměti na svazek s volné místo odpovídající 5 až 10 % celkové velikosti dat zálohy. Správně přesuňte umístění mezipaměti, použijte postup v [dotazy týkající se Azure Backup Agent](https://docs.microsoft.com/azure/backup/backup-azure-file-folder-backup-faq#backup).<li> Ujistěte se, že soubor OnlineBackup.KEK je k dispozici. <br>*Výchozí umístění pro odkládací složce nebo cestu k umístění mezipaměti je C:\Program Files\Microsoft Azure Recovery Services Agent\Scratch*.        |

## <a name="error-34506-the-encryption-passphrase-stored-on-this-computer-is-not-correctly-configured"></a>Došlo k chybě 34506. Šifrovací heslo uložené na tomto počítači není správně nakonfigurovaný.

| Podrobnosti o chybě | Možné příčiny | Doporučené akce |
|---------|---------|---------|
|**Chyba** </br><ol>*Došlo k chybě 34506. Šifrovací heslo uložené na tomto počítači není správně nakonfigurovaný*.    | <li> Odkládací složka se nachází na svazku, který nemá dostatek místa. <li> Pomocnou složku byl přesunut nesprávně do jiného umístění. <li> Chybí soubor OnlineBackup.KEK.        | <li>Upgrade na [nejnovější verzi](http://aka.ms/azurebackup_agent) agenta MARS.<li>Přesuňte pomocnou složku nebo umístění mezipaměti na svazek s volné místo odpovídající 5 – 10 % celkové velikosti dat zálohy. Správně přesuňte umístění mezipaměti, použijte postup v [dotazy týkající se Azure Backup Agent](https://docs.microsoft.com/azure/backup/backup-azure-file-folder-backup-faq#backup).<li> Ujistěte se, že soubor OnlineBackup.KEK je k dispozici. <br>*Výchozí umístění pro odkládací složce nebo cestu k umístění mezipaměti je C:\Program Files\Microsoft Azure Recovery Services Agent\Scratch*.         |

## <a name="backups-do-not-run-as-per-schedule"></a>Zálohování nelze spustit podle plánu
Proveďte následující kroky při plánovaných záloh není se aktivují automaticky, zatímco ruční zálohy budou fungovat bez problémů. 
 
<li>Zkontrolujte, že prostředí PowerShell 3.0 nebo vyšší je nainstalována na serveru. Pokud chcete zkontrolovat verzi prostředí PowerShell, spusťte následující příkaz a ověřte, zda *hlavní* číslo verze je roven nebo větší než 3.

`$PSVersionTable.PSVersion`
<li>Ujistěte se, že následující cesta je součástí *PSMODULEPATH* proměnné prostředí.

`<MARS agent installation path>\Microsoft Azure Recovery Services Agent\bin\Modules\MSOnlineBackup`
<li>Pokud zásady spouštění prostředí powershell pro *LocalMachine* je nastavena na s omezeným přístupem, rutina prostředí powershell, který se aktivuje úloha zálohování může selhat. Spuštěním následujících příkazů v režimu se zvýšenými oprávněními ke kontrole a nastavte zásady spouštění buď *Unrestricted* nebo *RemoteSigned*

`PS C:\WINDOWS\system32> Get-ExecutionPolicy -List`

`PS C:\WINDOWS\system32> Set-ExecutionPolicy Unrestricted`
<li>Přejděte do ovládacích panelů -> Nástroje pro správu -> Plánovač úloh -> rozbalte Microsoft -> vyberte Online zálohování
<li>Dvakrát klikněte na panel úkolů: Microsoft-OnlineBackup"a přejděte na kartu"Triggery".
<li>Zkontrolujte, že "Status" úlohu je nastavená na "Povoleno". V opačném případě klikněte na možnost "Upravit" a vyberte zaškrtávací políčko "Povoleno"
<li>Přejděte *možnosti zabezpečení* část *Obecné* kartu
<li>Ujistěte se, že uživatelský účet vybrané ke spuštění úkolu je buď *systému* nebo skupiny místní správci na serveru

> [!TIP]
> Doporučuje se server restartuje po provedení výše uvedené kroky a ujistěte se, že změny jsou konzistentní


## <a name="troubleshooting-restore-issues"></a>Odstraňování problémů obnovení

### <a name="failure-to-mount-the-recovery-volume-during-recovery-of-files-and-folders"></a>Nepodařilo se připojit svazek pro obnovení během obnovení souborů a složek
Pokud nelze Azure Backup úspěšně připojit svazek pro obnovení ani po několika minutách kliknutím **připojit** nebo nebude moci připojit svazek pro obnovení pomocí jedné nebo více chybám, postupujte podle následujících kroků, abyste obnovovat normálně.

1.  Zrušte proces probíhající připojení v případě, že je spuštěné pro několik minut.

2.  Ujistěte se, že jste na nejnovější verzi agenta Azure Backup. Přečtěte si informace o verzi agenta Azure Backup, klikněte na **o Microsoft Azure Recovery Services Agent** na **akce** podokně služby Microsoft Azure Backup konzoly a ujistěte se, **Verze** počet se rovná nebo je vyšší než verze uvedená v [v tomto článku](https://go.microsoft.com/fwlink/?linkid=229525). Můžete stáhnout nejnovější verzi z [zde](https://go.microsoft.com/fwLink/?LinkID=288905)

3.  Přejděte na **Správce zařízení** -> **řadiče úložiště** a ujistěte se, že máte přístup k **iniciátoru iSCSI od Microsoftu**. Pokud ji mohli najít, přejdete přímo ke kroku 7 níže. 

4.  Pokud služba iniciátoru iSCSI společnosti Microsoft nelze najít, jak je uvedeno v kroku 3, zkontrolujte, jestli nenajdete položku **Správce zařízení** -> **řadiče úložiště** volá  **Neznámé zařízení** s ID hardwaru **ROOT\ISCSIPRT**.

5.  Klikněte pravým tlačítkem na **neznámé zařízení** a vyberte **aktualizace ovladače**.

6.  Aktualizovat ovladač tak, že vyberete možnost **vyhledat automaticky aktualizovaný ovladač**. Dokončení aktualizace by se měla změnit **neznámé zařízení** k **iniciátoru iSCSI od Microsoftu** jak je znázorněno níže. 

    ![Šifrování](./media/backup-azure-restore-windows-server/UnknowniSCSIDevice.png)

7.  Přejděte na **Správce úloh** -> **služby (místní počítač)** -> **služba iniciátoru iSCSI společnosti Microsoft**. 

    ![Šifrování](./media/backup-azure-restore-windows-server/MicrosoftInitiatorServiceRunning.png)
    
8.  Restartujte službu iniciátoru iSCSI společnosti Microsoft, pravým tlačítkem na službu, klikněte na Stop a dále znovu kliknete pravým tlačítkem a kliknutím na **Start**.

9.  Zkuste obnovení pomocí rychlé obnovení. 

Pokud obnovení pořád selže, restartujte server nebo klienta. Pokud není žádoucí, restartování nebo obnovení selže i i po restartování serveru, zkuste obnovení z jiný počítač, který pomocí kroků uvedených v [v tomto článku](backup-azure-restore-windows-server.md#use-instant-restore-to-restore-data-to-an-alternate-machine)

## <a name="need-help-contact-support"></a>Potřebujete pomoct? Kontaktování podpory
Pokud stále potřebujete pomoc, [obraťte se na podporu](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) pro rychlé vyřešení problému.

## <a name="next-steps"></a>Další postup
* Přečtěte si víc o [zálohování Windows serveru pomocí agenta Azure Backup](tutorial-backup-windows-server-to-azure.md).
* Potřebujete-li obnovit zálohu, použijte tento článek k [obnovení souborů na počítač se systémem Windows](backup-azure-restore-windows-server.md).
