---
title: Počáteční zálohování offline pomocí služby Import/export
description: Přečtěte si, jak Azure Backup umožňuje odesílat data mimo síť pomocí služby Azure import/export. V tomto článku se dozvíte, jak v režimu offline importu počátečních dat zálohování použít službu Azure import export.
ms.reviewer: saurse
ms.topic: conceptual
ms.date: 05/17/2018
ms.openlocfilehash: 162d129eaea83ef6623daaa063e8a088c021e25d
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/05/2020
ms.locfileid: "77022609"
---
# <a name="offline-backup-workflow-in-azure-backup"></a>Pracovní postup offline zálohování ve službě Azure Backup

Azure Backup má několik předdefinovaných efektivit, které při počátečním úplném zálohování dat do Azure šetří náklady na síť a úložiště. Počáteční úplné zálohování obvykle přenáší velké objemy dat a vyžaduje více šířky pásma sítě ve srovnání s dalšími zálohami, které přenášejí pouze rozdíly a přírůstkové. V průběhu režimu offline osazení Azure Backup můžou použít disky k nahrání offline zálohovaných dat do Azure.

Azure Backup proces offline nasazení úzce integruje se [službou Azure import/export](../storage/common/storage-import-export-service.md) , která umožňuje přenos počátečních zálohovaných dat do Azure pomocí disků. Pokud máte terabajty (TBs) počátečních zálohovaných dat, která je potřeba přenést přes síť s vysokou latencí a malou šířkou pásma, můžete k odeslání prvotní záložní kopie na jednom nebo více pevných discích do datacentra Azure použít pracovní postup offline. Následující obrázek poskytuje přehled kroků v pracovním postupu.

  ![Přehled procesu pracovního postupu offline importu](./media/backup-azure-backup-import-export/offlinebackupworkflowoverview.png)

Proces zálohování offline zahrnuje následující kroky:

1. Místo odesílání zálohovaných dat přes síť zapište záložní data do pracovního umístění.
2. Pomocí nástroje AzureOfflineBackupDiskPrep Zapište data do pracovního umístění na jeden nebo více disků SATA.
3. V rámci přípravné práce nástroj AzureOfflineBackupDiskPrep vytvoří úlohu importu Azure. Odešlete jednotky SATA do nejbližšího datacentra Azure a odkázat na úlohu importu, aby se tyto aktivity připojily.
4. V datovém centru Azure se data na discích zkopírují do účtu služby Azure Storage.
5. Azure Backup zkopíruje zálohovaná data z účtu úložiště do trezoru Recovery Services a naplánují se přírůstkové zálohy.

## <a name="supported-configurations"></a>Podporované konfigurace

Následující Azure Backup funkce nebo úlohy podporují použití offline zálohování.

> [!div class="checklist"]
>
> * Zálohování souborů a složek pomocí agenta Microsoft Azure Recovery Services (MARS), označovaného také jako agent Azure Backup.
> * Zálohování všech úloh a souborů pomocí nástroje System Center Data Protection Manager (SC DPM)
> * Zálohování všech úloh a souborů pomocí serveru Microsoft Azure Backup
 
   > [!NOTE]
   > Zálohování v režimu offline není podporováno pro zálohování stavu systému pomocí agenta Azure Backup.

[!INCLUDE [backup-upgrade-mars-agent.md](../../includes/backup-upgrade-mars-agent.md)]

## <a name="prerequisites"></a>Požadavky

  > [!NOTE]
  > Následující předpoklady a pracovní postup platí jenom pro offline zálohování souborů a složek pomocí [nejnovějšího agenta Mars](https://aka.ms/azurebackup_agent). Pokud chcete provádět offline zálohování pro úlohy pomocí nástroje System Center DPM nebo Azure Backup Server, přečtěte si [Tento článek](backup-azure-backup-server-import-export-.md).

Než začnete pracovní postup offline zálohování iniciovat, proveďte následující požadavky:

* Vytvořte [trezor Recovery Services](backup-azure-recovery-services-vault-overview.md). Pokud chcete vytvořit trezor, přečtěte si postup v [tomto článku](tutorial-backup-windows-server-to-azure.md#create-a-recovery-services-vault) .
* Zajistěte, aby byla do klienta Windows Server/Windows nainstalována pouze [nejnovější verze agenta Azure Backup](https://aka.ms/azurebackup_agent) a počítač byl zaregistrován v trezoru Recovery Services.
* V počítači se spuštěným agentem Azure Backup se vyžaduje Azure PowerShell 3.7.0. Doporučujeme, abyste si stáhli a [nainstalovali verzi 3.7.0 Azure PowerShell](https://github.com/Azure/azure-powershell/releases/tag/v3.7.0-March2017).
* V počítači, na kterém je spuštěný agent Azure Backup, se ujistěte, že je nainstalovaný Microsoft Edge nebo Internet Explorer 11 a že je povolený JavaScript.
* Vytvořte účet Azure Storage ve stejném předplatném jako Recovery Services trezor.
* Ujistěte se, že máte [potřebná oprávnění](../active-directory/develop/howto-create-service-principal-portal.md) k vytvoření Azure Active Directory aplikace. Pracovní postup offline zálohování vytvoří aplikaci Azure Active Directory v rámci předplatného přidruženého k účtu Azure Storage. Cílem aplikace je poskytnout Azure Backup se zabezpečeným a vymezeným přístupem ke službě Azure import, kterou vyžaduje pracovní postup offline zálohování.
* Zaregistrujte poskytovatele prostředků Microsoft. ImportExport k předplatnému, které obsahuje účet Azure Storage. Registrace poskytovatele prostředků:
    1. V hlavní nabídce klikněte na **odběry**.
    2. Pokud jste přihlášeni k odběru více předplatných, vyberte předplatné, které používáte pro offline zálohování. Pokud použijete jenom jedno předplatné, zobrazí se vaše předplatné.
    3. V nabídce Předplatné klikněte na **poskytovatelé prostředků** . zobrazí se seznam zprostředkovatelů.
    4. V seznamu zprostředkovatelů přejděte dolů na Microsoft. ImportExport. Pokud je stav NotRegistered, klikněte na **zaregistrovat**.
    ![registrace poskytovatele prostředků](./media/backup-azure-backup-import-export/registerimportexport.png)
* Vytvoří se pracovní umístění, které může být sdílená síťová složka nebo jakákoli další jednotka na počítači, interní nebo externí, s dostatkem místa na disku pro uložení počáteční kopie. Pokud se třeba pokoušíte zálohovat souborový server 500-GB, ujistěte se, že pracovní oblast má aspoň 500 GB. (V důsledku komprese se používá menší množství.)
* Při posílání disků do Azure používejte jenom 2,5 palce SSD, nebo 2,5-palce nebo 3,5 – na interních pevných discích SATA II/III. Můžete použít pevné disky až do 10 TB. Nejnovější sadu jednotek, které služba podporuje, najdete v [dokumentaci ke službě Import/export v Azure](../storage/common/storage-import-export-requirements.md#supported-hardware) .
* Jednotky SATA musí být připojené k počítači (označovanému jako *počítač pro kopírování*), ze kterého se provádí Kopírování zálohovaných dat z *pracovního umístění* na jednotky SATA. Ujistěte se, že je na *počítači pro kopírování*povolený nástroj BitLocker.

## <a name="workflow"></a>Pracovní postupy

Tato část popisuje pracovní postup offline zálohování, aby bylo možné doručovat data do datacentra Azure a odeslat je do Azure Storage. Pokud máte dotazy týkající se služby pro import nebo jakéhokoli aspektu procesu, přečtěte si [dokumentaci Přehled importu služby](../storage/common/storage-import-export-service.md).

## <a name="initiate-offline-backup"></a>Zahájit zálohování offline

1. Po naplánování zálohy agenta MARS se zobrazí následující obrazovka.

    ![Import obrazovky](./media/backup-azure-backup-import-export/offlinebackup_inputs.png)

2. Vyberte možnost **přenos pomocí vlastních disků**.

    >[!NOTE]
    >Pro přenos počátečních zálohovaných dat do režimu offline doporučujeme použít možnost Azure Data Box. Tato možnost šetří úsilí potřebné k zajištění vlastních disků kompatibilních s Azure tím, že poskytuje Microsoftem zabezpečená, bezpečná a neoprávněná zařízení Azure Data box, na která můžou data záloh zapisovat přímo agent MARS.

3. Klikněte na **Další** a pečlivě vyplňte vstupy:

    ![Zadejte podrobnosti o disku.](./media/backup-azure-backup-import-export/your-disk-details.png)

   Popis vstupů je následující:

    * **Pracovní umístění**: dočasné umístění úložiště, do kterého se vytvořila prvotní záložní kopie. Pracovní umístění může být ve sdílené síťové složce nebo na místním počítači. Pokud je počítač kopírování a zdrojový počítač jiný, doporučujeme zadat úplnou cestu k pracovnímu umístění v síti.
    * **Azure Resource Manager účet úložiště**: název účtu úložiště Správce prostředků typu (pro obecné účely v1 nebo pro obecné účely v2) v jakémkoli předplatném Azure.
    * **Azure Storage kontejner**: název cílového objektu BLOB úložiště v účtu Azure Storage, do kterého se data záloh importují, než se zkopírují do trezoru Recovery Services.
    * **ID předplatného Azure**: ID předplatného Azure, kde se vytvoří účet Azure Storage.
    * **Název úlohy pro import do Azure**: jedinečný název, podle kterého služba Azure import service a Azure Backup sleduje přenos dat odeslaných na disky do Azure.
  
   Zadejte vstupy na obrazovce a klikněte na **Další**. Uložte zadané *pracovní umístění* a *název úlohy importu Azure*, protože tyto informace jsou potřebné k přípravě disků.

4. Po zobrazení výzvy se přihlaste k předplatnému Azure. Musíte se přihlásit, aby Azure Backup mohl vytvořit aplikaci Azure Active Directory a poskytovala požadovaná oprávnění pro přístup ke službě Azure import.

    ![Zálohovat hned](./media/backup-azure-backup-import-export/azure-login.png)

5. Dokončete pracovní postup a v konzole agenta Azure Backup klikněte na **Zálohovat nyní**.

    ![Zálohovat hned](./media/backup-azure-backup-import-export/backupnow.png)

6. Na stránce potvrzení v průvodci klikněte na **zálohovat**. Počáteční záloha je zapsána do pracovní oblasti jako součást instalace.

   ![Potvrďte, že jste připravení zálohovat nyní](./media/backup-azure-backup-import-export/backupnow-confirmation.png)

    Po dokončení operace je pracovní umístění připravené k použití pro přípravu disku.

   ![Zálohovat hned](./media/backup-azure-backup-import-export/opbackupnow.png)

## <a name="prepare-sata-drives-and-ship-to-azure"></a>Příprava jednotek SATA a odeslání do Azure

Nástroj *AzureOfflineBackupDiskPrep* připraví jednotky SATA, které se odesílají do nejbližšího datacentra Azure. Tento nástroj je k dispozici v instalačním adresáři agenta Azure Backup (v následující cestě):

    *\Microsoft Azure Recovery Services Agent\Utils\\*

1. Přejít do adresáře a zkopírovat adresář **AzureOfflineBackupDiskPrep** do jiného počítače, kde jsou připojeny jednotky SATA. V počítači s připojenými jednotkami SATA zajistěte:

   * Počítač pro kopírování může získat přístup k pracovnímu umístění pracovního postupu offline-osazení pomocí stejné síťové cesty, kterou jste zadali v pracovním postupu **zahájení offline zálohování** .
   * V počítači pro kopírování je povolený nástroj BitLocker.
   * Je nainstalovaná Azure PowerShell 3.7.0.
   * Jsou nainstalované nejnovější kompatibilní prohlížeče (Microsoft Edge nebo Internet Explorer 11) a je povolený JavaScript.
   * Počítač pro kopírování má přístup k Azure Portal. V případě potřeby může být počítač kopírování stejný jako zdrojový počítač.

     > [!IMPORTANT]
     > Pokud se jedná o virtuální počítač, musí být počítač pro kopírování na jiném fyzickém serveru nebo klientském počítači ze zdrojového počítače.

2. Otevřete příkazový řádek se zvýšenými oprávněními na počítači pro kopírování pomocí adresáře nástrojů *AzureOfflineBackupDiskPrep* jako aktuální adresář a spusťte následující příkaz:

    ```.\AzureOfflineBackupDiskPrep.exe s:<Staging Location Path>```

    | Parametr | Popis |
    | --- | --- |
    | s:&lt;*cestu k pracovnímu umístění*&gt; |Povinný vstup, který slouží k zadání cesty k pracovnímu umístění, které jste zadali v pracovním postupu **zahájení offline zálohování** . |
    | p:&lt;*cesta k PublishSettingsFile*&gt; |Volitelný vstup, který slouží k zadání cesty k souboru **Nastavení publikování v Azure** , který jste zadali v pracovním postupu **zahájení offline zálohování** . |

    Když příkaz spustíte, nástroj vyžádá výběr úlohy importu Azure, která odpovídá diskům, které je potřeba připravit. Pokud je k zadanému pracovnímu umístění přidružená jenom jedna úloha importu, zobrazí se vám stejná obrazovka jako ta, která následuje.

    ![Vstup nástroje pro přípravu disku Azure](./media/backup-azure-backup-import-export/diskprepconsole0_1.png) <br/>

3. Zadejte písmeno jednotky bez koncové dvojtečky pro připojený disk, který chcete připravit na přenos do Azure.
4. Po zobrazení výzvy zadejte potvrzení formátování jednotky.
5. Zobrazí se výzva, abyste se přihlásili k předplatnému Azure. Zadejte své přihlašovací údaje.

    ![Vstup nástroje pro přípravu disku Azure](./media/backup-azure-backup-import-export/signindiskprep.png) <br/>

    Nástroj pak začne připravovat disk a kopírovat data zálohy. Po zobrazení výzvy nástrojem může být nutné připojit další disky pro případ, že na poskytnutém disku není dostatek místa pro zálohovaná data. <br/>

    Na konci úspěšného provedení tohoto nástroje příkazový řádek poskytuje tři informace:
   1. K odeslání do Azure se připraví jeden nebo víc disků, které jste zadali.
   2. Zobrazí se potvrzení, že se vytvořila vaše úloha importu. Úloha importu používá zadaný název.
   3. Nástroj zobrazí dodací adresu pro datacentrum Azure.

      ![Příprava disku Azure dokončena](./media/backup-azure-backup-import-export/console2.png)<br/>

6. Na konci provádění příkazu můžete aktualizovat informace o expedici.

7. Dodejte disky na adresu, kterou poskytuje nástroj, a udržujte sledovací číslo pro budoucí referenci.

   > [!IMPORTANT]
   > Žádné dvě úlohy importu Azure nemohou mít stejné sledovací číslo. Zajistěte, aby jednotky připravené nástrojem v rámci jedné úlohy importu Azure byly dodávány společně v jednom balíčku a aby existovalo jedno jedinečné sledovací číslo pro daný balíček. Nekombinujte jednotky připravené jako součást samostatných úloh importu Azure v jednom balíčku.

## <a name="update-shipping-details-on-the-azure-import-job"></a>Aktualizovat podrobnosti o dopravě v úloze importu Azure

Následující postup aktualizuje podrobnosti o expedici úlohy Azure import. Tyto informace obsahují podrobnosti o:

* název přepravce, který disky doručuje do Azure
* vrátit informace o expedici pro vaše disky

1. Přihlaste se ke svému předplatnému Azure.
2. V hlavní nabídce klikněte na **všechny služby** a v dialogovém okně všechny služby zadejte import. Až uvidíte **úlohy import/export**, klikněte na ni.
    ![zadávání informací o dopravě](./media/backup-azure-backup-import-export/search-import-job.png)<br/>

    Otevře se seznam **úloh import/export** a zobrazí se seznam všech úloh importu/exportu ve vybraném předplatném.

3. Pokud máte více předplatných, nezapomeňte vybrat předplatné používané pro import zálohovaných dat. Pak vyberte nově vytvořenou úlohu importu a otevřete její podrobnosti.

    ![Zkontrolovat informace o expedici](./media/backup-azure-backup-import-export/import-job-found.png)<br/>

4. V nabídce nastavení úlohy Import klikněte na **Spravovat informace o expedici** a zadejte podrobnosti o zpětném odeslání.

    ![Ukládání informací o expedici](./media/backup-azure-backup-import-export/shipping-info.png)<br/>

5. Pokud máte sledovací číslo od přepravce, klikněte na banner na stránce Přehled úlohy importu Azure a zadejte následující podrobnosti:

   > [!IMPORTANT]
   > Do dvou týdnů od vytvoření úlohy importu do Azure nezapomeňte aktualizovat informace o dopravci a sledovací číslo. Při ověřování těchto informací během dvou týdnů může dojít k odstranění úlohy a nezpracovávání jednotek.

   ![Ukládání informací o expedici](./media/backup-azure-backup-import-export/joboverview.png)<br/>

   ![Ukládání informací o expedici](./media/backup-azure-backup-import-export/tracking-info.png)

### <a name="time-to-process-the-drives"></a>Čas ke zpracování jednotek

Doba potřebná ke zpracování úlohy importu do Azure se liší v závislosti na různých faktorech, jako je například doba expedice, typ úlohy, typ a velikost kopírovaných dat a velikost poskytnutých disků. Služba Azure import/export nemá smlouvu SLA, ale po přijetí těchto disků se služba snaží dokončit Kopírování zálohovaných dat do účtu služby Azure Storage 7 až 10 dní.

### <a name="monitoring-azure-import-job-status"></a>Monitorování stavu úlohy pro import do Azure

Stav úlohy importu můžete monitorovat z Azure Portal tak, že přejdete na stránku **úlohy import/export** a vyberete svou úlohu. Další informace o stavu úloh importu najdete v článku o [importu služby Storage pro export](../storage/common/storage-import-export-service.md) .

### <a name="complete-the-workflow"></a>Dokončení pracovního postupu

Po úspěšném dokončení úlohy importu budou v účtu úložiště k dispozici počáteční data zálohy. V době příštího naplánovaného zálohování Azure Backup zkopíruje obsah dat z účtu úložiště do trezoru Recovery Services, jak je znázorněno níže:

   ![Kopírování dat do trezoru Recovery Services](./media/backup-azure-backup-import-export/copyingfromstorageaccounttoazurebackup.png)<br/>

V době příštího naplánovaného zálohování Azure Backup provede přírůstkové zálohování.

### <a name="cleaning-up-resources"></a>Čištění prostředků

Po dokončení prvotního zálohování můžete bezpečně odstranit data importovaná do kontejneru Azure Storage a záložní data do pracovního umístění.

## <a name="next-steps"></a>Další kroky

* Všechny dotazy k pracovnímu postupu importu/exportu v Azure najdete v tématu [použití služby Microsoft Azure import/export k přenosu dat do úložiště objektů BLOB](../storage/common/storage-import-export-service.md).
