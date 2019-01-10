---
title: Azure Backup – zálohování Offline nebo prvotní zálohy pomocí služby Azure Import/Export
description: Zjistěte, jak vám to poslat data připojen k síti pomocí služby Azure Import/Export umožňuje Azure Backup. Tento článek vysvětluje offline předvyplnění počáteční zálohovací data pomocí služby Azure Import Export.
services: backup
author: saurabhsensharma
manager: shivamg
ms.service: backup
ms.topic: conceptual
ms.date: 05/17/2018
ms.author: saurse
ms.openlocfilehash: 9d91ccd04ed06fb6c256a2d9911202d7df6d08a5
ms.sourcegitcommit: 63b996e9dc7cade181e83e13046a5006b275638d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/10/2019
ms.locfileid: "54188296"
---
# <a name="offline-backup-workflow-in-azure-backup"></a>Pracovní postup offline zálohování ve službě Azure Backup
Azure Backup obsahuje několik předdefinovaných úspor, které ukládají náklady na síť a úložiště během počáteční úplné zálohování dat do Azure. Počáteční úplné zálohování obvykle přenos velkých objemů dat a vyžadují větší šířku pásma sítě, ve srovnání s následné zálohy, které přenášejí pouze rozdíly/přírůstková. Procesem offline předvyplnění Azure Backup můžete používat disky k odesílání dat offline zálohování do Azure.

Azure Backup offline předvyplnění procesu je úzce integrovaná s [služba Azure Import/Export](../storage/common/storage-import-export-service.md) , která umožňuje přesuňte počáteční zálohovací data do Azure pomocí disků. Pokud máte terabajtů (TB) počáteční zálohovací data, které je potřeba přenést přes síť vysokou latencí a malou šířkou pásma, můžete použít pracovní postup offline předvyplnění dodávat kopie prvotní zálohy na jeden nebo více pevných disků do datacentra Azure. Na následujícím obrázku obsahuje přehled kroků v pracovním postupu.

  ![Přehled offline importovat pracovní postup](./media/backup-azure-backup-import-export/offlinebackupworkflowoverview.png)

Proces offline zálohování zahrnuje následující kroky:

1. Místo odesílání zálohovaných dat přes síť, zapisovat data záloh do pracovního umístění.
2. Pomocí nástroje AzureOfflineBackupDiskPrep zapisovat data v pracovní umístění, do jednoho nebo několika disků SATA.
3. Jako součást přípravných prací vytvoří nástroj AzureOfflineBackupDiskPrep úlohu služby Azure Import. Odešlete disky SATA na nejbližší datové centrum Azure a odkazovat na úloze importu do aktivity připojit.
4. V datacentru Azure data na discích zkopírují do účtu služby Azure storage.
5. Azure Backup kopíruje zálohovaná data z účtu úložiště do trezoru služby Recovery Services a přírůstkové zálohování.

## <a name="supported-configurations"></a>Podporované konfigurace 
Následující funkce Azure Backup nebo úloh podporují použití Offline zálohování.

> [!div class="checklist"]
> * Zálohování souborů a složek pomocí agenta Microsoft Azure Recovery Services (MARS), označují také jako agenta Azure Backup. 
> * Zálohování všech úloh a soubory s System Center Data Protection Manager (SC DPM) 
> * Zálohování všech úloh a souborů s využitím Microsoft Azure Backup serveru <br/>

   > [!NOTE]
   > Zálohování offline není podporováno pro zálohování stavu systému se provádí pomocí agenta Azure Backup. 

[!INCLUDE [backup-upgrade-mars-agent.md](../../includes/backup-upgrade-mars-agent.md)]

## <a name="prerequisites"></a>Požadavky

  > [!NOTE]
  > Následující požadavky a pracovní postup platí jenom pro Offline zálohování souborů a složek pomocí [nejnovější verzi agenta MARS](https://aka.ms/azurebackup_agent). Chcete-li provést Offline zálohování pro úlohy s využitím System Center DPM nebo Azure Backup Server, přečtěte si [v tomto článku](backup-azure-backup-server-import-export-.md). 

Před zahájením pracovní postup Offline zálohování, zajistěte splnění následujících požadavků: 
* Vytvoření [trezor služby Recovery Services](backup-azure-recovery-services-vault-overview.md). Pro vytvoření trezoru, použijte postup v [v tomto článku](tutorial-backup-windows-server-to-azure.md#create-a-recovery-services-vault)
* Ujistěte se, že to pouze [nejnovější verzi agenta Azure Backup](https://aka.ms/azurebackup_agent) byla nainstalována na klientovi Windows serveru nebo Windows, podle potřeby a počítač zaregistruje do trezoru služby Recovery Services.
* V počítači se službou Azure Backup agent se vyžaduje prostředí Azure PowerShell 3.7.0. Doporučuje se, že si stáhnout a [verze 3.7.0 instalace prostředí Azure PowerShell](https://github.com/Azure/azure-powershell/releases/tag/v3.7.0-March2017).
* Na počítači se systémem agenta Azure Backup Ujistěte se, že je nainstalovaný Microsoft Edge nebo Internet Explorer 11 a jazyk JavaScript je povolený. 
* Vytvoření účtu služby Azure Storage ve stejném předplatném jako trezor služby Recovery Services. 
* Ujistěte se, že máte [potřebná oprávnění](../active-directory/develop/howto-create-service-principal-portal.md) k vytvoření aplikace Azure Active Directory. Pracovní postup Offline zálohování vytvoří aplikaci Azure Active Directory v rámci předplatného přidružené k účtu Azure Storage. Cílem aplikace je Azure Backup poskytnout bezpečné a s vymezeným oborem přístup do služby Azure Import, vyžaduje se pro pracovní postup Offline zálohování. 
* Zaregistrujte poskytovatele prostředků Microsoft.ImportExport předplatné obsahující účet Azure Storage. Zaregistrovat poskytovatele prostředků:
    1. V hlavní nabídce klikněte na tlačítko **předplatná**.
    2. Pokud jste přihlášeni s více předplatnými, vyberte předplatné, které používáte pro offline zálohování. Můžete použít jenom jedno předplatné, zobrazí se vaše předplatné.
    3. V nabídce předplatné, klikněte na tlačítko **poskytovatelů prostředků** zobrazíte seznam zprostředkovatelů.
    4. V seznamu poskytovatelů přejděte dolů Microsoft.ImportExport. Pokud je stav NotRegistered, klikněte na tlačítko **zaregistrovat**.
    ![registrace poskytovatele prostředků](./media/backup-azure-backup-import-export/registerimportexport.png)
* Pracovní umístění, což může být sdílen v síti nebo libovolné další jednotky v počítači, interní nebo externí, dostatek místa na disku pro uložení počáteční kopie, je vytvořen. Například pokud se pokoušíte získat zpátky až 500 GB souborový server, ujistěte se, že pracovní oblasti je aspoň 500 GB. (Menší velikost se používá v důsledku komprese.)
* Při odesílání disků do Azure, použijte pouze 2,5 SSD nebo 2,5 nebo 3, 5palcová SATA II/III interních pevných disků. Můžete použít pevné disky až 10 TB. Zkontrolujte [dokumentace ke službě Azure Import/Export](../storage/common/storage-import-export-requirements.md#supported-hardware) nejnovější sady jednotek, které služba podporuje.
* Jednotky SATA musí být připojený k určitému počítači (uvedené jako *kopírování počítače*) odkud kopie dat záloh z *pracovní umístění* k SATA se provádí jednotky. Ujistěte se, že má zapnutí Bitlockeru *kopie počítače*.

## <a name="workflow"></a>Pracovní postup
Tato část popisuje pracovní postup offline zálohování tak, aby vaše data mohou doručit do datacentra Azure a nahrát do služby Azure Storage. Pokud máte dotazy týkající se importu služby či jiného aspektu procesu, přečtěte si [importovat přehled dokumentace ke službě](../storage/common/storage-import-export-service.md).

## <a name="initiate-offline-backup"></a>Zahájení offline zálohování
1. Při plánování zálohování na agenta MARS se zobrazí následující obrazovka.

    ![Obrazovka importu](./media/backup-azure-backup-import-export/offlinebackup_inputs.png)

  Popis vstupy vypadá takto:

    * **Pracovní umístění**: Umístění dočasného úložiště, ke kterému je zapsán kopie prvotní zálohy. Pracovní umístění může být ve sdílené síťové složce nebo v místním počítači. Pokud počítač kopírování a zdrojový počítač se liší, doporučujeme, že zadáte úplná síťová cesta pracovního umístění.
    * **Účet úložiště Azure Resource Manageru**: Název Typ účtu úložiště Resource Manageru v jakéhokoli předplatného Azure.
    * **Kontejner Azure Storage**: Název cílové objektů blob úložiště v účtu úložiště Azure, kterého se naimportuje data záloh, před zkopírováním do trezoru služby Recovery Services.
    * **ID předplatného Azure**: ID předplatného Azure, ve kterém se vytvoří účet služby Azure Storage.
    * **Název úlohy importu do Azure**: Jedinečný název, který Import úložiště Azure service a Azure Backup a sledovat přenosu dat odesílaných na discích do Azure. 
  
  Zadejte vstupy na obrazovce a klikněte na **Další**. Uložit zadaných *pracovní umístění* a *název úlohy importu Azure*, jak tyto informace jsou nezbytné k přípravě disky.

2. Po zobrazení výzvy se přihlaste do vašeho předplatného Azure. Musíte se přihlásit, aby Azure Backup můžete vytvořit aplikaci Azure Active Directory a zadejte požadovaná oprávnění pro přístup ke službě Azure Import.

    ![Zálohovat nyní](./media/backup-azure-backup-import-export/azurelogin.png)

3. Dokončení pracovního postupu a klikněte v konzole agenta služby Azure Backup **zálohovat nyní**.

    ![Zálohovat nyní](./media/backup-azure-backup-import-export/backupnow.png)

4. Na potvrzovací stránce průvodce, klikněte na tlačítko **zálohování**. Prvotní zálohy se zapisují do pracovní oblasti jako součást instalace.

   ![Potvrďte, že jste nyní připravení zálohování](./media/backup-azure-backup-import-export/backupnow-confirmation.png)

    Po dokončení operace pracovní umístění je připravená k použití pro přípravu disku.

   ![Zálohovat nyní](./media/backup-azure-backup-import-export/opbackupnow.png)

## <a name="prepare-sata-drives-and-ship-to-azure"></a>Jednotky SATA Připravíme a odešleme do Azure
*AzureOfflineBackupDiskPrep* nástroj připraví disky SATA, které se odesílají do nejbližší datové centrum Azure. Tento nástroj je k dispozici v instalačním adresáři agenta Azure Backup (v následující cestě):

   *Agent\Utils \Microsoft azure Recovery Services\\*

1. Přejděte do adresáře a zkopírujte **AzureOfflineBackupDiskPrep** adresáře do jiného počítače, ve kterém jsou připojené disky SATA. Na počítači s připojených disků SATA zkontrolujte:

    * Kopírování počítač získá přístup k pracovní umístění pro pracovní postup offline předvyplnění pomocí stejné cesty sítě, která byla součástí **zahájení offline zálohování** pracovního postupu.
    * Povolení nástroje BitLocker v počítači kopírování.
    * Je nainstalovaný Azure PowerShell 3.7.0.
    * Jsou nainstalované nejnovější kompatibilní prohlížeče (Microsoft Edge nebo Internet Explorer 11) a je povolen jazyk JavaScript. 
    * Počítač kopírování získá přístup k webu Azure portal. V případě potřeby kopírování počítač může být stejný jako zdrojový počítač.
    
    > [!IMPORTANT] 
    > Pokud zdrojový počítač je virtuální počítač, počítač kopie musí být jiný fyzický server nebo klientský počítač ze zdrojového počítače.

2. Otevřete příkazový řádek se zvýšenými oprávněními na počítači kopírování s *AzureOfflineBackupDiskPrep* nástroj adresář jako aktuálního adresáře a spusťte následující příkaz:

    ```.\AzureOfflineBackupDiskPrep.exe s:<Staging Location Path>```

    | Parametr | Popis |
    | --- | --- |
    | s:&lt;*pracovní umístění cesty*&gt; |Povinné vstupu používanému zadat cestu do pracovního umístění, které jste zadali v **zahájení offline zálohování** pracovního postupu. |
    | p:&lt;*cestu k PublishSettingsFile*&gt; |Volitelný vstup, který slouží k zadání cesty k **nastavením publikování v Azure** soubor, který jste zadali **zahájení offline zálohování** pracovního postupu. |

    Když příkaz spustíte, nástroj požaduje Výběr úlohy Import úložiště Azure, která odpovídá jednotky, které je potřeba se připravit. Pokud pouze jeden import úlohy souvisí s zadané pracovní umístění, objeví se obrazovka podobně jako ten, který následuje.

    ![Azure vstup nástroj Příprava disku](./media/backup-azure-backup-import-export/diskprepconsole0_1.png) <br/>

3. Zadejte písmeno jednotky bez koncové dvojtečka připojovaný disk, který chcete připravit pro přenos do Azure. 
4. Proveďte potvrzení pro formátování disku po zobrazení výzvy.
5. Zobrazí se výzva k přihlášení do vašeho předplatného Azure. Zadejte svoje přihlašovací údaje.

    ![Azure vstup nástroj Příprava disku](./media/backup-azure-backup-import-export/signindiskprep.png) <br/>

    Příprava na disku a zkopírování dat zálohování poté zahájí nástroj. Budete muset připojit další disky po zobrazení výzvy nástroj v případě, že zadaný disk nemá dostatek místa pro zálohovaná data. <br/>

    Na konci úspěšné spuštění nástroje příkazového řádku poskytuje tři údaje:
    1. Jeden nebo víc disků, které jste zadali, jsou připravené k odeslání do Azure. 
    2. Se zobrazí potvrzení, že se vytvořil úlohu importu. Úlohy importu použije název, který jste zadali.
    3. Nástroj zobrazí dodací adresu pro datové centrum Azure.

    ![Příprava Azure disku dokončení](./media/backup-azure-backup-import-export/console2.png)<br/>

6. Na konci provedení příkazu můžete aktualizovat dodací informace.

7. Pošlete disky na adresu, kterou poskytuje nástroje a Udržovat sledovací číslo pro budoucí použití.

   > [!IMPORTANT] 
   > Žádné dvě úlohy Azure Import může mít stejný počet sledování. Ujistěte se, že jednotky připravené nástrojem v rámci jedné úlohy Import úložiště Azure se dodávají společně v jednom balíčku a že je jeden jedinečný sledovací číslo pro balíček. Není možné sloučit jednotky připravené součástí samostatné úlohy Import úložiště Azure v jediném balíčku.



## <a name="update-shipping-details-on-the-azure-import-job"></a>Aktualizovat podrobnosti o dopravě na úlohy Import úložiště Azure

Následující postup aktualizuje přenosů podrobnosti úlohy Import úložiště Azure. Tyto informace zahrnují informace o:
* Název dopravce, který poskytuje disků do Azure
* Vrátí podrobnosti expedice pro disky

1. Přihlaste se ke svému předplatnému Azure.
2. V hlavní nabídce klikněte na tlačítko **všechny služby** a zadejte do dialogového okna všechny služby, Import. Když se zobrazí **úlohy Import/Export**, klikněte na něj.
    ![Zadat informace o dopravě](./media/backup-azure-backup-import-export/search-import-job.png)<br/>

    Seznam **úlohy Import/export** se otevře nabídka a zobrazí se seznam všech úloh importu a exportu ve vybraném předplatném. 

3. Pokud máte více předplatných, je potřeba vybrat předplatné použité k importu dat záloh. Vyberte nově vytvořený úlohy importu a otevřete její podrobnosti.

    ![Projděte si informace o dopravě](./media/backup-azure-backup-import-export/import-job-found.png)<br/>

4. V nabídce nastavení pro úlohy importu, klikněte na tlačítko **spravovat podrobnosti o** a zadejte zpětný expediční podrobnosti.

    ![Ukládat informace o dopravě](./media/backup-azure-backup-import-export/shipping-info.png)<br/>

5. Až budete mít sledovací číslo od přenosů operátora, klikněte na banner na stránce Přehled úlohy Import úložiště Azure a zadejte následující údaje:

   > [!IMPORTANT] 
   > Ujistěte se, že jsou aktualizované informace dopravce a sledování Číslo během dvou týdnů od vytvoření úlohy importu do Azure. Selhání ověření tyto informace do dvou týdnů může způsobit odstranění úlohy a jednotky nejsou zpracována.

   ![Ukládat informace o dopravě](./media/backup-azure-backup-import-export/joboverview.png)<br/>

   ![Ukládat informace o dopravě](./media/backup-azure-backup-import-export/tracking-info.png)

### <a name="time-to-process-the-drives"></a>Čas ke zpracování jednotky 
Množství čas potřebný k procesu úlohy import úložiště Azure se liší v závislosti na různých faktorech, jako je například dobu dodání úlohy typ, typ a velikost dat, které jsou kopírovány a požadovanou velikost disků k dispozici. Služba Azure Import/Export nemá žádnou smlouvu SLA, ale po přijetí disků se služby snaží dokončete kopírování zálohovaných dat do účtu úložiště Azure ve dnech 7 až 10. 

### <a name="monitoring-azure-import-job-status"></a>Monitorování stavu úlohy importování Azure
Můžete monitorovat stav vaší úlohy importu z portálu Azure portal tak, že přejdete na **úlohy Import/Export** stránku a výběr vašeho projektu. Další informace o stavu úlohy importu, najdete v článku [Export Import úložiště služby](../storage/common/storage-import-export-service.md) článku.

### <a name="complete-the-workflow"></a>Dokončení pracovního postupu
Po úspěšném dokončení úlohy importu počáteční data záloh jsou k dispozici ve vašem účtu úložiště. Při příští plánované zálohování Azure Backup zkopíruje obsah dat z účtu úložiště do trezoru služby Recovery Services, jak je znázorněno níže: 

   ![Kopírování dat do trezoru služby Recovery Services](./media/backup-azure-backup-import-export/copyingfromstorageaccounttoazurebackup.png)<br/>

Při příští plánované zálohování Azure Backup provádí přírůstkové zálohování.

### <a name="cleaning-up-resources"></a>Vymazání prostředků
Po dokončení prvotní zálohy se můžete bezpečně odstranit data importovat do kontejneru úložiště Azure a data záloh v pracovní umístění.

## <a name="next-steps"></a>Další postup
* Máte nějaké otázky k Azure Import/Export pracovního postupu, najdete v tématu [přenášet data do úložiště objektů Blob pomocí služby Microsoft Azure Import/Export](../storage/common/storage-import-export-service.md).
* Zálohování offline oddílu z Azure Backup [nejčastější dotazy k](backup-azure-backup-faq.md) jakékoli dotazy týkající se pracovní postup.
