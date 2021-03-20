---
title: Počáteční zálohování offline pomocí služby Azure import/export
description: Naučte se, jak můžete pomocí Azure Backup posílat data mimo síť pomocí služby Azure import/export. V tomto článku se dozvíte, jak používat službu import/export v režimu offline při počátečním zálohování dat.
ms.reviewer: saurse
ms.topic: conceptual
ms.date: 05/17/2018
ms.openlocfilehash: 3ea470c2e732b7e0ef46e9e5fa78c744aa30c955
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "98704359"
---
# <a name="offline-backup-workflow-in-azure-backup"></a>Pracovní postup offline zálohování v Azure Backup

Azure Backup má několik předdefinovaných efektivit, které při počátečním úplném zálohování dat do Azure šetří náklady na síť a úložiště. Počáteční úplné zálohování obvykle přenáší velké objemy dat a vyžaduje více šířky pásma sítě ve srovnání s dalšími zálohami, které přenášejí pouze rozdíly a přírůstkové. V průběhu režimu offline osazení Azure Backup můžou použít disky k nahrání offline zálohovaných dat do Azure.

Azure Backup procesu offline nasazení je těsně integrovaná se [službou Azure import/export](../import-export/storage-import-export-service.md). Tuto službu můžete použít k přenosu počátečních zálohovaných dat do Azure pomocí disků. Pokud máte terabajty (TBs) počátečních zálohovaných dat, která je potřeba přenést přes síť s vysokou latencí a malou šířkou pásma, můžete k odeslání prvotní záložní kopie na jednom nebo více pevných discích do datacentra Azure použít pracovní postup offline. Následující obrázek poskytuje přehled kroků v pracovním postupu.

  ![Přehled procesu pracovního postupu offline importu](./media/backup-azure-backup-import-export/offlinebackupworkflowoverview.png)

Proces zálohování offline zahrnuje tyto kroky:

1. Místo odesílání zálohovaných dat přes síť zapište záložní data do pracovního umístění.
1. Pomocí nástroje *AzureOfflineBackupDiskPrep* Zapište data do pracovního umístění na jeden nebo více disků SATA.
1. V rámci přípravné práce nástroj *AzureOfflineBackupDiskPrep* vytvoří úlohu importu Azure. Odešlete jednotky SATA do nejbližšího datacentra Azure a odkázat na úlohu importu, aby se tyto aktivity připojily.
1. V datovém centru Azure se data na discích zkopírují do účtu služby Azure Storage.
1. Azure Backup zkopíruje zálohovaná data z účtu úložiště do trezoru Recovery Services a naplánují se přírůstkové zálohy.

## <a name="supported-configurations"></a>Podporované konfigurace

Následující Azure Backup funkce nebo úlohy podporují použití offline zálohování pro:

> [!div class="checklist"]
>
> * Zálohování souborů a složek pomocí agenta Microsoft Azure Recovery Services (MARS), označovaného také jako agent Azure Backup.
> * Zálohování všech úloh a souborů pomocí nástroje System Center Data Protection Manager (DPM).
> * Zálohování všech zatížení a souborů pomocí serveru Microsoft Azure Backup.

   > [!NOTE]
   > Zálohování v režimu offline není podporováno pro zálohy stavu systému pomocí agenta Azure Backup.

[!INCLUDE [backup-upgrade-mars-agent.md](../../includes/backup-upgrade-mars-agent.md)]

## <a name="prerequisites"></a>Předpoklady

  > [!NOTE]
  > Následující předpoklady a pracovní postup platí jenom pro offline zálohování souborů a složek pomocí [nejnovějšího agenta Azure Recovery Services](https://aka.ms/azurebackup_agent). Pokud chcete provádět offline zálohování pro úlohy pomocí nástroje System Center DPM nebo Azure Backup Server, přečtěte si téma [pracovní postup offline zálohování pro DPM a Azure Backup Server](backup-azure-backup-server-import-export.md).

Než spustíte pracovní postup offline zálohování, proveďte následující požadavky:

* Vytvořte [trezor Recovery Services](backup-azure-recovery-services-vault-overview.md). Pokud chcete vytvořit trezor, postupujte podle kroků v části [Vytvoření trezoru Recovery Services](tutorial-backup-windows-server-to-azure.md#create-a-recovery-services-vault).
* Zajistěte, aby byla na Windows serveru nebo na klientovi Windows nainstalovaná jenom [nejnovější verze agenta Azure Backup](https://aka.ms/azurebackup_agent) , a počítač je zaregistrovaný v trezoru Recovery Services.
* V počítači, na kterém je spuštěný agent Azure Backup, se vyžaduje Azure PowerShell 3.7.0. Stáhněte a [nainstalujte 3.7.0 verze Azure PowerShell](https://github.com/Azure/azure-powershell/releases/tag/v3.7.0-March2017).
* V počítači, na kterém je spuštěný agent Azure Backup, se ujistěte, že je nainstalovaný Microsoft Edge nebo Internet Explorer 11 a že je povolený JavaScript.
* Vytvořte účet úložiště Azure ve stejném předplatném jako trezor Recovery Services.
* Ujistěte se, že máte [potřebná oprávnění](../active-directory/develop/howto-create-service-principal-portal.md) k vytvoření Azure Active Directory aplikace. Pracovní postup offline zálohování vytvoří aplikaci Azure Active Directory v rámci předplatného přidruženého k účtu úložiště Azure. Cílem aplikace je poskytnout Azure Backup se zabezpečeným a vymezeným přístupem ke službě Azure import/export, která je vyžadována pro pracovní postup offline zálohování.
* Zaregistrujte poskytovatele prostředků *Microsoft. ImportExport* k předplatnému, které obsahuje účet úložiště Azure. Registrace poskytovatele prostředků:
    1. V hlavní nabídce vyberte **odběry**.
    1. Pokud jste přihlášeni k odběru více předplatných, vyberte předplatné, které chcete použít pro zálohování offline. Pokud použijete jenom jedno předplatné, zobrazí se vaše předplatné.
    1. V nabídce předplatné vyberte **poskytovatelé prostředků** , abyste zobrazili seznam zprostředkovatelů.
    1. V seznamu zprostředkovatelů přejděte dolů na *Microsoft. ImportExport*. Pokud je **stav** **NotRegistered**, vyberte **zaregistrovat**.

        ![Registrace poskytovatele prostředků](./media/backup-azure-backup-import-export/registerimportexport.png)

* Vytvoří se pracovní umístění, které může být sdílená síťová složka nebo jakákoli další jednotka na počítači, interní nebo externí, s dostatkem místa na disku pro uložení počáteční kopie. Pokud třeba chcete zálohovat souborový server 500-GB, ujistěte se, že pracovní oblast má aspoň 500 GB. (V důsledku komprese se používá menší množství.)
* Když odesíláte disky do Azure, používejte jenom 2,5 jednotek SSD nebo 2,5-palce nebo 3,5 – na 3, 5palcové interní pevné disky SATA II/III. Můžete použít pevné disky až do 10 TB. Nejnovější sadu jednotek, které služba podporuje, najdete v [dokumentaci ke službě Import/export v Azure](../import-export/storage-import-export-requirements.md#supported-hardware) .
* Jednotky SATA musí být připojené k počítači (označovanému jako *počítač pro kopírování*), ze kterého se provádí Kopírování zálohovaných dat z pracovního umístění na jednotky SATA. Ujistěte se, že je na počítači pro kopírování povolený nástroj BitLocker.

## <a name="workflow"></a>Pracovní postup

Tato část popisuje pracovní postup offline zálohování, aby bylo možné doručovat data do datacentra Azure a odeslat je do Azure Storage. Pokud máte dotazy týkající se služby pro import nebo jakéhokoli aspektu procesu, přečtěte si [dokumentaci přehled služby Import/export Azure](../import-export/storage-import-export-service.md).

## <a name="initiate-offline-backup"></a>Zahájit zálohování offline

1. Po naplánování zálohy v agentovi Recovery Services se zobrazí tato stránka.

    ![Importovat stránku](./media/backup-azure-backup-import-export/offlinebackup_inputs.png)

1. Vyberte možnost **přenos pomocí vlastních disků**.

    > [!NOTE]
    > Použijte možnost Azure Data Box k přenosu počátečních zálohovaných dat do režimu offline. Tato možnost uloží úsilí potřebné k zajištění vlastního disku kompatibilního s Azure. Zajišťuje společnosti Microsoft zařízení, která jsou zabezpečená, zabezpečená a Azure Data Box tamperproof, na která je možné přímo zapisovat data záloh pomocí agenta Recovery Services.

1. Vyberte **Další** a pečlivě zadejte pole.

    ![Zadejte podrobnosti o disku.](./media/backup-azure-backup-import-export/your-disk-details.png)

   Pole, která vyplníte, jsou:

    * **Pracovní umístění**: dočasné umístění úložiště, do kterého se vytvořila prvotní záložní kopie. Pracovní umístění může být ve sdílené síťové složce nebo na místním počítači. Pokud je počítač kopírování a zdrojový počítač jiný, zadejte úplnou síťovou cestu k pracovnímu umístění.
    * **Azure Resource Manager účet úložiště**: název účtu úložiště Správce prostředků typu (pro obecné účely v1 nebo pro obecné účely v2) v jakémkoli předplatném Azure.
    * **Azure Storage kontejner**: název cílového kontejneru úložiště objektů BLOB v účtu služby Azure Storage, do kterého se data záloh importují, než se zkopírují do trezoru Recovery Services.
    * **ID předplatného Azure**: ID předplatného Azure, kde se vytváří účet Azure Storage.
    * **Název úlohy pro import do Azure**: jedinečný název, pomocí kterého služba Azure Import/Export a Azure Backup sleduje přenos dat odeslaných na disky do Azure.
  
   Po vyplnění polí vyberte **Další**. Uložte **pracovní umístění** a informace o **názvu úlohy importu Azure** . Je nutné připravit disky.

1. Po zobrazení výzvy se přihlaste ke svému předplatnému Azure. Musíte se přihlásit, aby Azure Backup mohl vytvořit aplikaci Azure Active Directory. Zadejte požadovaná oprávnění pro přístup ke službě Azure import/export.

    ![Přihlašovací stránka předplatného Azure](./media/backup-azure-backup-import-export/azure-login.png)

1. Dokončete pracovní postup. V konzole agenta Azure Backup vyberte **Zálohovat nyní**.

    ![Zálohovat hned](./media/backup-azure-backup-import-export/backupnow.png)

1. Na stránce **potvrzení** v průvodci vyberte **zálohovat**. Počáteční záloha je zapsána do pracovní oblasti jako součást instalace.

   ![Potvrďte, že jste připravení zálohovat nyní](./media/backup-azure-backup-import-export/backupnow-confirmation.png)

    Po dokončení operace je pracovní umístění připravené k použití pro přípravu disku.

   ![Stránka Průvodce zálohováním nyní](./media/backup-azure-backup-import-export/opbackupnow.png)

## <a name="prepare-sata-drives-and-ship-to-azure"></a>Příprava jednotek SATA a odeslání do Azure

Nástroj *AzureOfflineBackupDiskPrep* připraví jednotky SATA, které se odesílají do nejbližšího datacentra Azure. Tento nástroj je k dispozici v instalačním adresáři Azure Backup agenta v následující cestě:

```*\Microsoft Azure Recovery Services Agent\Utils\\*```

1. Do adresáře přejdete a zkopírujte adresář *AzureOfflineBackupDiskPrep* do jiného počítače, kde jsou připojené jednotky SATA. V počítači s připojenými jednotkami SATA zajistěte, aby:

   * Počítač pro kopírování může získat přístup k pracovnímu umístění pracovního postupu offline-osazení pomocí stejné síťové cesty, kterou jste zadali v pracovním postupu v části "zahájení zálohování offline".
   * V počítači pro kopírování je povolený nástroj BitLocker.
   * Je nainstalovaná Azure PowerShell 3.7.0.
   * Jsou nainstalované nejnovější kompatibilní prohlížeče (Microsoft Edge nebo Internet Explorer 11) a je povolený JavaScript.
   * Počítač pro kopírování má přístup k Azure Portal. V případě potřeby může být počítač kopírování stejný jako zdrojový počítač.

     > [!IMPORTANT]
     > Pokud se jedná o virtuální počítač, musí být počítač pro kopírování na jiném fyzickém serveru nebo klientském počítači ze zdrojového počítače.

1. Otevřete příkazový řádek se zvýšenými oprávněními na počítači pro kopírování s adresářem nástroje *AzureOfflineBackupDiskPrep* jako s aktuálním adresářem. Spusťte následující příkaz:

    ```.\AzureOfflineBackupDiskPrep.exe s:<Staging Location Path>```

    | Parametr | Popis |
    | --- | --- |
    | s: &lt; *cesta k pracovnímu umístění*&gt; |Tento povinný vstup slouží k zadání cesty k pracovnímu umístění, které jste zadali v pracovním postupu v části "zahájení offline zálohování". |
    | p: &lt; *cesta k PublishSettingsFile*&gt; |Tento nepovinný vstup slouží k zadání cesty k souboru nastavení publikování v Azure.  |

    Když příkaz spustíte, nástroj vyžádá výběr úlohy importu Azure, která odpovídá diskům, které je potřeba připravit. Pokud je k dispozici pouze jedna úloha importu s dodaným pracovním umístěním, zobrazí se stránka podobná této.

    ![Vstup nástroje pro přípravu disku Azure](./media/backup-azure-backup-import-export/diskprepconsole0_1.png) <br/>

1. Zadejte písmeno jednotky bez koncové dvojtečky pro připojený disk, který chcete připravit na přenos do Azure.
1. Po zobrazení výzvy zadejte potvrzení formátování jednotky.
1. Budete vyzváni k přihlášení k předplatnému Azure. Zadejte svoje přihlašovací údaje.

    ![Přihlášení k předplatnému Azure](./media/backup-azure-backup-import-export/signindiskprep.png) <br/>

    Nástroj pak začne připravovat disk a kopírovat zálohovaná data. Pokud na poskytnutém disku není dostatek místa pro zálohovaná data, může být nutné připojit další disky po zobrazení výzvy nástrojem. <br/>

    Na konci úspěšného provedení tohoto nástroje příkazový řádek poskytuje tři informace:

   1. K odeslání do Azure se připraví jeden nebo víc disků, které jste zadali.
   1. Zobrazí se potvrzení, že se vytvořila vaše úloha importu. Úloha importu používá zadaný název.
   1. Nástroj zobrazí dodací adresu pro datacentrum Azure.

      ![Příprava disku Azure dokončena](./media/backup-azure-backup-import-export/console2.png)<br/>

1. Na konci provádění příkazu můžete aktualizovat informace o expedici.

1. Dodejte disky na adresu, kterou poskytuje nástroj. Ponechte sledovací číslo pro budoucí referenci.

   > [!IMPORTANT]
   > Žádné dvě úlohy importu Azure nemohou mít stejné sledovací číslo. Zajistěte, aby jednotky připravené nástrojem v rámci jedné úlohy importu Azure byly dodávány společně v jednom balíčku a aby existovalo jedno jedinečné sledovací číslo pro daný balíček. Nekombinujte jednotky připravené jako součást samostatných úloh importu Azure v jednom balíčku.

## <a name="update-shipping-details-on-the-azure-import-job"></a>Aktualizovat podrobnosti o dopravě v úloze importu Azure

Následující postup aktualizuje podrobnosti o expedici úlohy Azure import. Tyto informace obsahují podrobnosti o:

* Název dopravce, který doručí disky do Azure.
* Vrátí informace o expedici pro vaše disky.

1. Přihlaste se ke svému předplatnému Azure.
1. V hlavní nabídce vyberte **všechny služby**. V dialogovém okně **všechny služby** zadejte **Import**. Až uvidíte **úlohy import/export**, vyberte je.

    ![Zadat informace o expedici](./media/backup-azure-backup-import-export/search-import-job.png)<br/>

    Otevře se nabídka **úlohy import/export** a zobrazí se seznam všech úloh importu/exportu ve vybraném předplatném.

1. Pokud máte více předplatných, vyberte předplatné, které se používá k importu zálohovaných dat. Pak vyberte nově vytvořenou úlohu importu a otevřete její podrobnosti.

    ![Zkontrolovat informace o expedici](./media/backup-azure-backup-import-export/import-job-found.png)<br/>

1. V nabídce **Nastavení** úlohy import vyberte **Spravovat informace o expedici**. Zadejte podrobnosti o zpětném odeslání.

    ![Uložit informace o expedici](./media/backup-azure-backup-import-export/shipping-info.png)<br/>

1. Pokud máte sledovací číslo od přepravce, vyberte banner na stránce Přehled úlohy importu Azure a zadejte následující podrobnosti.

   > [!IMPORTANT]
   > Do dvou týdnů od vytvoření úlohy importu do Azure nezapomeňte aktualizovat informace o dopravci a sledovací číslo. Nepovedlo se ověřit tyto informace během dvou týdnů, což může způsobit odstranění úlohy a nezpracovávání jednotek.

   ![Výstraha aktualizace informací o sledování](./media/backup-azure-backup-import-export/joboverview.png)<br/>

   ![Informace o nosiči a sledovací číslo](./media/backup-azure-backup-import-export/tracking-info.png)

### <a name="time-to-process-the-drives"></a>Čas ke zpracování jednotek

Doba potřebná ke zpracování úlohy importu do Azure se liší. Doba zpracování je založena na faktorech, jako je čas expedice, typ úlohy, typ a velikost kopírovaných dat, a velikost poskytnutých disků. Služba Azure import/export nemá smlouvu SLA. Po přijetí disků se služba snaží dokončit Kopírování zálohovaných dat do účtu služby Azure Storage 7 až 10 dní.

### <a name="monitor-azure-import-job-status"></a>Monitorovat stav úlohy Azure import

Stav úlohy importu můžete monitorovat z Azure Portal. Přejít na stránku **úlohy import/export** a vyberte svou úlohu. Další informace o stavu úloh importu najdete v tématu [co je služba Azure import/export?](../import-export/storage-import-export-service.md).

### <a name="finish-the-workflow"></a>Dokončení pracovního postupu

Po úspěšném dokončení úlohy importu budou v účtu úložiště k dispozici počáteční data zálohy. V době příštího naplánovaného zálohování Azure Backup zkopíruje obsah dat z účtu úložiště do trezoru Recovery Services.

   ![Kopírovat data do trezoru Recovery Services](./media/backup-azure-backup-import-export/copyingfromstorageaccounttoazurebackup.png)<br/>

V době příštího naplánovaného zálohování Azure Backup provede přírůstkové zálohování.

### <a name="clean-up-resources"></a>Vyčištění prostředků

Po dokončení prvotního zálohování můžete bezpečně odstranit data importovaná do kontejneru Azure Storage a záložní data do pracovního umístění.

## <a name="next-steps"></a>Další kroky

* Jakékoli dotazy týkající se pracovního postupu služby Import/export v Azure najdete v tématu [použití služby Microsoft Azure import/export k přenosu dat do úložiště objektů BLOB](../import-export/storage-import-export-service.md).
