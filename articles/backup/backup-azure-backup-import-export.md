---
title: Zálohování offline pomocí služby Azure Import/Export
description: Zjistěte, jak můžete pomocí služby Azure Backup odesílat data ze sítě pomocí služby Azure Import/Export. Tento článek vysvětluje offline osazení počáteční záložní data pomocí služby Azure Import/Export.
ms.reviewer: saurse
ms.topic: conceptual
ms.date: 05/17/2018
ms.openlocfilehash: 1359616e074f36a1324a418d5b2c889076ced52d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78206754"
---
# <a name="offline-backup-workflow-in-azure-backup"></a>Pracovní postup zálohování offline v Azure Backup

Azure Backup má několik integrovaných efektivit, které šetří náklady na síť a úložiště během počátečního úplného zálohování dat do Azure. Počáteční úplné zálohy obvykle přenášejí velké množství dat a vyžadují větší šířku pásma sítě ve srovnání s následnými zálohami, které přenášejí pouze rozdíly/přírůstky. Prostřednictvím procesu offline osevací, Azure Backup můžete použít disky k nahrání dat zálohování offline do Azure.

Proces offline seedingu azure backup je úzce integrovaný se [službou Azure Import/Export](../storage/common/storage-import-export-service.md). Pomocí této služby můžete přenést počáteční záložní data do Azure pomocí disků. Pokud máte terabajty (RB) počátečních záložních dat, které je potřeba přenést přes síť s vysokou latencí a nízkou šířkou pásma, můžete použít pracovní postup offline seedingu k odeslání počáteční záložní kopie na jeden nebo více pevných disků do datového centra Azure. Následující obrázek poskytuje přehled kroků v pracovním postupu.

  ![Přehled procesu pracovního postupu offline importu](./media/backup-azure-backup-import-export/offlinebackupworkflowoverview.png)

Proces zálohování offline zahrnuje tyto kroky:

1. Namísto odesílání záložních dat v síti zapište záložní data do pracovního umístění.
1. Pomocí nástroje *AzureOfflineBackupDiskPrep* zapište data v pracovním umístění na jeden nebo více disků SATA.
1. Jako součást přípravné práce nástroj *AzureOfflineBackupDiskPrep* vytvoří úlohu importu Azure. Odešlete jednotky SATA do nejbližšího datového centra Azure a odkazujte na úlohu importu pro připojení aktivit.
1. V datovém centru Azure se data na discích zkopírují do účtu úložiště Azure.
1. Služba Azure Backup zkopíruje data zálohování z účtu úložiště do trezoru služby Recovery Services a jsou naplánovány přírůstkové zálohy.

## <a name="supported-configurations"></a>Podporované konfigurace

Použití offline zálohování podporují následující funkce nebo úlohy Azure Backup pro:

> [!div class="checklist"]
>
> * Zálohování souborů a složek pomocí agenta Microsoft Azure Recovery Services (MARS), označovaného také jako agent azure backup.
> * Zálohování všech úloh a souborů pomocí správce ochrany dat system center (DPM).
> * Zálohování všech úloh a souborů pomocí Microsoft Azure Backup Server.
 
   > [!NOTE]
   > Offline zálohování není podporováno pro zálohování stavu systému provedené pomocí agenta zálohování Azure.

[!INCLUDE [backup-upgrade-mars-agent.md](../../includes/backup-upgrade-mars-agent.md)]

## <a name="prerequisites"></a>Požadavky

  > [!NOTE]
  > Následující požadavky a pracovní postup platí jenom pro offline zálohování souborů a složek pomocí [nejnovějšího agenta služby Azure Recovery Services](https://aka.ms/azurebackup_agent). Pokud chcete provádět offline zálohování úloh pomocí System Center DPM nebo Azure Backup Server, přečtěte si [informace o pracovním postupu zálohování offline pro DPM a Azure Backup Server](backup-azure-backup-server-import-export-.md).

Před zahájením pracovního postupu zálohování offline proveďte následující požadavky:

* Vytvořte [trezor služby Recovery Services](backup-azure-recovery-services-vault-overview.md). Chcete-li vytvořit trezor, postupujte podle pokynů v [části Vytvoření trezoru služby Recovery Services](tutorial-backup-windows-server-to-azure.md#create-a-recovery-services-vault).
* Ujistěte se, že je v klientovi Windows Server nebo Windows nainstalovaná jenom [nejnovější verze agenta zálohování Azure](https://aka.ms/azurebackup_agent) a že je počítač zaregistrován v trezoru služby Recovery Services.
* Azure PowerShell 3.7.0 je povinný v počítači se spuštěnou agentem zálohování Azure. Stáhněte a [nainstalujte verzi 3.7.0 Azure PowerShellu](https://github.com/Azure/azure-powershell/releases/tag/v3.7.0-March2017).
* V počítači se spuštěným agentem zálohování Azure zkontrolujte, jestli je nainstalovaná Microsoft Edge nebo Internet Explorer 11 a že je povolený JavaScript.
* Vytvořte účet úložiště Azure ve stejném předplatném jako trezor služby Recovery Services.
* Ujistěte se, že máte [potřebná oprávnění](../active-directory/develop/howto-create-service-principal-portal.md) k vytvoření aplikace Azure Active Directory. Pracovní postup zálohování offline vytvoří aplikaci Azure Active Directory v předplatném přidruženém k účtu úložiště Azure. Cílem aplikace je poskytnout Azure Backup zabezpečený a s vymezeným oborem přístup ke službě Azure Import/Export, která je vyžadována pro pracovní postup zálohování offline.
* Zaregistrujte poskytovatele prostředků *Microsoft.ImportExport* s předplatným, které obsahuje účet úložiště Azure. Registrace poskytovatele prostředků:
    1. V hlavní nabídce vyberte **Předplatná**.
    1. Pokud jste přihlášeni k odběru více předplatných, vyberte předplatné, které chcete použít pro offline zálohování. Pokud používáte pouze jedno předplatné, zobrazí se vaše předplatné.
    1. V nabídce odběr vyberte **zprostředkovatele prostředků,** chcete-li zobrazit seznam zprostředkovatelů.
    1. V seznamu zprostředkovatelů přejděte dolů na *Microsoft.ImportExport*. Pokud je **stav** **NotRegistered**, vyberte **Registrovat**.

        ![Registrace poskytovatele prostředků](./media/backup-azure-backup-import-export/registerimportexport.png)

* Vytvoří se pracovní umístění, které může být sdílenou sítí nebo jakákoli další jednotka v počítači, interní nebo externí, s dostatkem místa na disku pro uložení počáteční kopie. Chcete-li například zálohovat souborový server o velikosti 500 GB, ujistěte se, že pracovní oblast je alespoň 500 GB. (Menší množství se používá z důvodu komprese.)
* Když odesíláte disky do Azure, používejte jenom 2,5palcový ssd disk nebo 2,5palcový nebo 3,5palcový interní pevné disky SATA II/III. Můžete použít pevné disky až 10 TB. Podívejte se na [dokumentaci služby Azure Import/Export](../storage/common/storage-import-export-requirements.md#supported-hardware) pro nejnovější sadu jednotek, které služba podporuje.
* Jednotky SATA musí být připojeny k počítači (označované jako *kopírovací počítač),* odkud se provádí kopie záložních dat z pracovního umístění do jednotek SATA. Zkontrolujte, zda je v kopírovacím počítači povolen nástroj BitLocker.

## <a name="workflow"></a>Pracovní postup

Tato část popisuje pracovní postup zálohování offline, aby se vaše data mohla doručit do datového centra Azure a nahrát do Azure Storage. Pokud máte dotazy týkající se služby importu nebo jakéhokoli aspektu procesu, přečtěte si [dokumentaci k přehledu služby Azure Import/Export](../storage/common/storage-import-export-service.md).

## <a name="initiate-offline-backup"></a>Zahájení zálohování offline

1. Když naplánujete zálohování na agenta služby Recovery Services, zobrazí se tato stránka.

    ![Stránka importu](./media/backup-azure-backup-import-export/offlinebackup_inputs.png)

1. Vyberte možnost **Přenos pomocí vlastních disků**.

    > [!NOTE]
    > K přenosu počátečních záložních dat offline použijte možnost Azure Data Box. Tato možnost šetří úsilí potřebné k získání vlastních disků kompatibilních s Azure. Poskytuje zařízení Azure Data Box proprietární, zabezpečená a nefalšovaná pro Microsoft, do kterých může agent služby Recovery Services přímo zapisovat data.

1. Vyberte **další**a pečlivě vyplňte pole.

    ![Zadejte podrobnosti o disku.](./media/backup-azure-backup-import-export/your-disk-details.png)

   Vysuňte pole, která vyplníte, jsou:

    * **Pracovní umístění**: Dočasné umístění úložiště, do kterého je zapsána počáteční záložní kopie. Pracovní umístění může být ve sdílené síťové složce nebo v místním počítači. Pokud se kopírovací počítač a zdrojový počítač liší, zadejte úplnou síťovou cestu pracovního umístění.
    * **Účet úložiště Azure Resource Manager**: Název účtu typu úložiště Správce prostředků (obecné účely v1 nebo pro obecné účely v2) v libovolném předplatném Azure.
    * **Kontejner úložiště Azure:** Název cílového objektu blob úložiště v účtu úložiště Azure, kde se importují data zálohy před kopírováním do trezoru služby Recovery Services.
    * **ID předplatného Azure:** ID předplatného Azure, kde se vytvoří účet úložiště Azure.
    * **Název úlohy importu Azure:** Jedinečný název, podle kterého služba Azure Import/Export a Azure Backup sledují přenos dat odeslaných na disky do Azure.
  
   Po vyplnění polí vyberte **Další**. Uložte **pracovní umístění** a informace o názvu **úlohy importu Azure.** Je nutné připravit disky.

1. Po zobrazení výzvy se přihlaste k předplatnému Azure. Musíte se přihlásit, aby služba Azure Backup mohla vytvořit aplikaci Azure Active Directory. Zadejte požadovaná oprávnění pro přístup ke službě Import a export Azure.

    ![Přihlašovací stránka předplatného Azure](./media/backup-azure-backup-import-export/azure-login.png)

1. Dokončete pracovní postup. Na konzole Agent pro zálohování Azure vyberte **Zálohovat .**

    ![Zálohovat nyní](./media/backup-azure-backup-import-export/backupnow.png)

1. Na stránce **Potvrzení** průvodce vyberte **Zálohovat**. Počáteční záloha je zapsána do pracovní oblasti jako součást instalace.

   ![Potvrďte, že jste připraveni zálohovat](./media/backup-azure-backup-import-export/backupnow-confirmation.png)

    Po dokončení operace je pracovní umístění připraveno k použití pro přípravu disku.

   ![Stránka Průvodce zálohováním](./media/backup-azure-backup-import-export/opbackupnow.png)

## <a name="prepare-sata-drives-and-ship-to-azure"></a>Příprava disků SATA a odeslání do Azure

Nástroj *AzureOfflineBackupDiskPrep* připravuje jednotky SATA, které jsou odesílány do nejbližšího datového centra Azure. Tento nástroj je k dispozici v instalačním adresáři Agent a zálohování Azure v následující cestě:

```*\Microsoft Azure Recovery Services Agent\Utils\\*```

1. Přejděte do adresáře a zkopírujte adresář *AzureOfflineBackupDiskPrep* do jiného počítače, ve kterém jsou připojeny jednotky SATA. V počítači s připojenými jednotkami SATA zajistěte, aby:

   * Kopírovací počítač může přistupovat k pracovnímu umístění pracovního postupu offline seeding pomocí stejné síťové cesty, která byla poskytnuta v pracovním postupu v části Zahájit zálohování offline.
   * Nástroj BitLocker je povolen v kopírovacím počítači.
   * Azure PowerShell 3.7.0 je nainstalovaný.
   * Jsou nainstalovány nejnovější kompatibilní prohlížeče (Microsoft Edge nebo Internet Explorer 11) a je povolen JavaScript.
   * Kopírovací počítač má přístup k portálu Azure. V případě potřeby může být kopírovací počítač stejný jako zdrojový počítač.

     > [!IMPORTANT]
     > Pokud je zdrojový počítač virtuální počítač, musí být kopírovací počítač jiný fyzický server nebo klientský počítač od zdrojového počítače.

1. Otevřete příkazový řádek se zvýšenými oprávněními v kopírovacím počítači s adresářem nástrojů *AzureOfflineBackupDiskPrep* jako aktuálním adresářem. Spusťte následující příkaz:

    ```.\AzureOfflineBackupDiskPrep.exe s:<Staging Location Path>```

    | Parametr | Popis |
    | --- | --- |
    | s:&lt;*Cesta pracovního umístění*&gt; |Tento povinný vstup slouží k poskytnutí cesty k pracovnímu umístění, které jste zadali do pracovního postupu v části Zahájit zálohování offline. |
    | p:&lt;*Cesta k PublishSettingsFile*&gt; |Tento volitelný vstup se používá k poskytnutí cesty k souboru nastavení publikování Azure, který jste zadali do pracovního postupu v části Zahájit zálohování offline. |

    Při spuštění příkazu nástroj požaduje výběr úlohy importu Azure, která odpovídá jednotky, které je třeba připravit. Pokud je k zadanému pracovnímu umístění přidružena pouze jedna úloha importu, zobrazí se stránka, jako je tato.

    ![Vstup nástroje pro přípravu disku Azure](./media/backup-azure-backup-import-export/diskprepconsole0_1.png) <br/>

1. Zadejte písmeno jednotky bez koncovédvojtečky pro připojený disk, který chcete připravit k přenosu do Azure.
1. Po zobrazení výzvy poskytněte potvrzení formátování jednotky.
1. Budete vyzváni k přihlášení k předplatnému Azure. Zadejte svoje přihlašovací údaje.

    ![Přihlášení k předplatnému Azure](./media/backup-azure-backup-import-export/signindiskprep.png) <br/>

    Nástroj pak začne připravit disk a zkopírovat záložní data. Pokud daný disk nemá dostatek místa pro záložní data, může být nutné připojit další disky. <br/>

    Na konci úspěšného spuštění nástroje, příkazový řádek poskytuje tři informace:

   1. Jeden nebo více disků, které jste zadali, jsou připraveny k odeslání do Azure.
   1. Obdržíte potvrzení, že byla vytvořena úloha importu. Úloha importu používá zadaný název.
   1. Nástroj zobrazí dodací adresu datového centra Azure.

      ![Příprava disku Azure byla dokončena](./media/backup-azure-backup-import-export/console2.png)<br/>

1. Na konci spuštění příkazu můžete aktualizovat informace o dopravě.

1. Disky předevčit na adresu, kterou nástroj zadaný. Ponechte sledovací číslo pro budoucí použití.

   > [!IMPORTANT]
   > Žádné dvě úlohy importu Azure nemůže mít stejné číslo sledování. Ujistěte se, že jednotky připravené nástrojem v rámci jedné úlohy importu Azure jsou dodávány společně v jednom balíčku a že pro balíček existuje jediné jedinečné sledovací číslo. Nekombinujte jednotky připravené jako součást samostatných úloh importu Azure v jednom balíčku.

## <a name="update-shipping-details-on-the-azure-import-job"></a>Aktualizace podrobností o dopravě u úlohy importu Azure

Následující postup aktualizuje podrobnosti o odesílání úloh importu Azure. Tyto informace zahrnují podrobnosti o:

* Název operátora, který doručuje disky do Azure.
* Vrácení podrobností o přepravě disků.

1. Přihlaste se ke svému předplatnému Azure.
1. V hlavní nabídce vyberte **Všechny služby**. V dialogovém okně **Všechny služby** zadejte **importovat**. Až se zobrazí **import nebo export úloh**, vyberte je.

    ![Zadejte informace o zásilce.](./media/backup-azure-backup-import-export/search-import-job.png)<br/>

    Otevře se nabídka **Úlohy importu a exportu** a zobrazí se seznam všech úloh importu a exportu ve vybraném předplatném.

1. Pokud máte více předplatných, vyberte předplatné použité k importu záložních dat. Pak vyberte nově vytvořenou úlohu importu a otevřete její podrobnosti.

    ![Kontrola informací o přepravě](./media/backup-azure-backup-import-export/import-job-found.png)<br/>

1. V nabídce **Nastavení** úlohy importu vyberte **Spravovat informace o dopravě**. Zadejte podrobnosti o vrácení zásilky.

    ![Uložit informace o přepravě](./media/backup-azure-backup-import-export/shipping-info.png)<br/>

1. Když máte sledovací číslo od svého dopravce, vyberte banner na stránce přehled úloh importu Azure a zadejte následující podrobnosti.

   > [!IMPORTANT]
   > Do dvou týdnů od vytvoření úlohy importu do Azure nezapomeňte aktualizovat informace o dopravci a sledovací číslo. Pokud tyto informace neověříte do dvou týdnů, může dojít k odstranění úlohy a nezpracování jednotek.

   ![Upozornění na aktualizaci informací o sledování](./media/backup-azure-backup-import-export/joboverview.png)<br/>

   ![Informace o operátorovi a sledovací číslo](./media/backup-azure-backup-import-export/tracking-info.png)

### <a name="time-to-process-the-drives"></a>Čas na zpracování jednotek

Doba, kterou zpracování úlohy importu Azure trvá, se liší. Doba zpracování je založena na faktorech, jako je doba expedice, typ úlohy, typ a velikost kopírovaných dat a velikost poskytnutých disků. Služba Azure Import/Export nemá sla. Po přijetí disků se služba snaží dokončit záložní kopii dat do vašeho účtu úložiště Azure za 7 až 10 dní.

### <a name="monitor-azure-import-job-status"></a>Sledování stavu úlohy importu Azure

Stav úlohy importu můžete sledovat z webu Azure Portal. Přejděte na stránku **Importovat nebo exportovat úlohy** a vyberte úlohu. Další informace o stavu úloh importu najdete v [tématu Co je služba Import/Export Azure?](../storage/common/storage-import-export-service.md).

### <a name="finish-the-workflow"></a>Dokončení pracovního postupu

Po úspěšném dokončení úlohy importu jsou v účtu úložiště k dispozici počáteční data zálohování. V době další naplánované zálohy Azure Backup zkopíruje obsah dat z účtu úložiště do trezoru služby Recovery Services.

   ![Kopírování dat do trezoru služby Recovery Services](./media/backup-azure-backup-import-export/copyingfromstorageaccounttoazurebackup.png)<br/>

V době dalšího naplánovaného zálohování azure backup provádí přírůstkové zálohování.

### <a name="clean-up-resources"></a>Vyčištění prostředků

Po dokončení počáteční zálohy můžete bezpečně odstranit data importovaná do kontejneru úložiště Azure a záložní data v pracovním umístění.

## <a name="next-steps"></a>Další kroky

* Jakékoli dotazy týkající se pracovního postupu služby Import a export Azure najdete v [tématu Přenos dat do úložiště objektů Blob pomocí služby Import a Export Microsoft Azure](../storage/common/storage-import-export-service.md).
