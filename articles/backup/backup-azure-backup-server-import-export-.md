---
title: Azure Backup – offline zálohování pro DPM a Azure Backup Server
description: Azure Backup umožňuje odesílat data mimo síť pomocí služby Azure import/export. Tento článek vysvětluje pracovní postup offline zálohování pro DPM a Azure Backup Server (MABS).
ms.reviewer: saurse
author: dcurwin
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 5/8/2018
ms.author: dacurwin
ms.openlocfilehash: 0f60fbf22541e5fdd003d0ab663b5905cd31adcd
ms.sourcegitcommit: c662440cf854139b72c998f854a0b9adcd7158bb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/02/2019
ms.locfileid: "68737237"
---
# <a name="offline-backup-workflow-for-dpm-and-azure-backup-server"></a>Pracovní postup offline zálohování pro DPM a Azure Backup Server
Azure Backup má několik předdefinovaných efektivit, které při počátečním úplném zálohování dat do Azure šetří náklady na síť a úložiště. Počáteční úplné zálohování obvykle přenáší velké objemy dat a vyžaduje více šířky pásma sítě ve srovnání s dalšími zálohami, které přenášejí pouze rozdíly a přírůstkové. Azure Backup zkomprimuje počáteční zálohy. Díky procesu offline osazení může Azure Backup použít disky k nahrání komprimovaných počátečních zálohovaných dat offline do Azure.

Proces offline nasazení Azure Backup je úzce integrovaný do [služby Azure import/export](../storage/common/storage-import-export-service.md) , která umožňuje přenášet data do Azure pomocí disků. Pokud máte terabajty (TBs) počátečních zálohovaných dat, která je nutné přenést v síti s vysokou latencí a malou šířkou pásma, můžete k odeslání prvotní záložní kopie na jednom nebo více pevných discích do datacentra Azure použít pracovní postup offline. Tento článek poskytuje přehled a další podrobné kroky pro dokončení tohoto pracovního postupu pro System Center DPM a Azure Backup Server.

> [!NOTE]
> Proces offline zálohování agenta Microsoft Azure Recovery Services (MARS) se liší od nástroje System Center DPM a Azure Backup Server. Informace o použití zálohování offline s agentem MARS najdete v [tomto článku](backup-azure-backup-import-export.md). Zálohování v režimu offline není podporováno pro zálohování stavu systému pomocí agenta Azure Backup.
>

## <a name="overview"></a>Přehled
Díky funkcím pro online osazení Azure Backup a Azure import/export je jednoduché nahrávat data do Azure pomocí disků v režimu offline. Proces zálohování offline zahrnuje následující kroky:

> [!div class="checklist"]
> * Data záloh se místo jejich odeslání přes síť zapisují do *pracovního umístění* .
> * Data v *pracovním umístění* se pak zapisují na jeden nebo víc disků SATA pomocí nástroje *AzureOfflineBackupDiskPrep* .
> * Pomocí nástroje se automaticky vytvoří úloha importu Azure.
> * Jednotky SATA se pak odesílají do nejbližšího datacentra Azure.
> * Po dokončení nahrávání zálohovaných dat do Azure Azure Backup zkopíruje zálohovaná data do trezoru záloh a naplánují se přírůstkové zálohy.

## <a name="supported-configurations"></a>Podporované konfigurace
Offline zálohování je podporované pro všechny modely nasazení Azure Backup, které data zálohují z místního prostředí do Microsoft Cloud. To zahrnuje

> [!div class="checklist"]
> * Zálohování souborů a složek s agentem Microsoft Azure Recovery Services (MARS) nebo agentem Azure Backup.
> * Zálohování všech úloh a souborů pomocí nástroje System Center Data Protection Manager (SC DPM)
> * Zálohování všech úloh a souborů pomocí serveru Microsoft Azure Backup <br/>

## <a name="prerequisites"></a>Požadavky
Před zahájením pracovního postupu offline zálohování se ujistěte, že jsou splněné následující předpoklady.
* Byl vytvořen [Recovery Services trezor](backup-azure-recovery-services-vault-overview.md) . Pokud ho chcete vytvořit, přečtěte si postup v [tomto článku](tutorial-backup-windows-server-to-azure.md#create-a-recovery-services-vault) .
* Agent Azure Backup nebo Azure Backup Server nebo SC DPM byl nainstalován buď na klienta Windows Server/Windows, a počítač je zaregistrován v úložišti Recovery Services. Zajistěte, aby se používala jenom [nejnovější verze Azure Backup](https://go.microsoft.com/fwlink/?linkid=229525) .
* [Stáhněte si soubor nastavení publikování v Azure](https://portal.azure.com/#blade/Microsoft_Azure_ClassicResources/PublishingProfileBlade) na počítači, ze kterého plánujete zálohovat vaše data. Předplatné, ze kterého stahujete soubor nastavení publikování, se může lišit od předplatného, které obsahuje Recovery Services trezor. Pokud je vaše předplatné ve službě Azure cloudy, pak pomocí následujících odkazů stáhněte soubor s nastavením publikování v Azure.

    | Oblast cloudu svrchovan | Odkaz na soubor nastavení publikování v Azure |
    | --- | --- |
    | Spojené státy | [Odkaz](https://portal.azure.us#blade/Microsoft_Azure_ClassicResources/PublishingProfileBlade) |
    | Čína | [Odkaz](https://portal.azure.cn/#blade/Microsoft_Azure_ClassicResources/PublishingProfileBlade) |

* V předplatném, ze kterého jste stáhli soubor nastavení publikování, byl vytvořen Azure Storage účet s modelem nasazení *Classic* :

  ![Vytvoření účtu klasického úložiště](./media/backup-azure-backup-import-export/storageaccountclassiccreate.png)

* Vytvoří se pracovní umístění, které může být sdílená síťová složka nebo jakákoli další jednotka na počítači, interní nebo externí, s dostatkem místa na disku pro uložení počáteční kopie. Pokud se třeba pokoušíte zálohovat souborový server 500-GB, ujistěte se, že pracovní oblast má aspoň 500 GB. (V důsledku komprese se používá menší množství.)
* S ohledem na disky, které se odesílají do Azure, se ujistěte, že se používají jenom 2,5 palce SSD nebo 2,5-palce nebo 3,5. Můžete použít pevné disky až do 10 TB. Nejnovější sadu jednotek, které služba podporuje, najdete v [dokumentaci ke službě Import/export v Azure](../storage/common/storage-import-export-requirements.md#supported-hardware) .
* Jednotky SATA musí být připojené k počítači (označovanému jako *počítač pro kopírování*), ze kterého se provádí Kopírování zálohovaných dat z *pracovního umístění* na jednotky SATA. Zajistěte, aby byl na *kopírovacím počítači* povolený nástroj BitLocker.

## <a name="workflow"></a>Pracovní postup
Informace v této části vám pomůžou dokončit pracovní postup offline zálohování, aby se vaše data mohla doručovat do datacentra Azure a nahrály na Azure Storage. Pokud máte dotazy týkající se služby pro import nebo jakéhokoli aspektu procesu, přečtěte si dokumentaci [Přehled služby Import služby](../storage/common/storage-import-export-service.md) , na kterou se odkazuje dříve.

### <a name="initiate-offline-backup"></a>Zahájit zálohování offline
1. Po naplánování zálohy se zobrazí následující obrazovka (v systému Windows Server, klient Windows nebo System Center Data Protection Manager).

    ![Import obrazovky](./media/backup-azure-backup-import-export/offlineBackupscreenInputs.png)

    Tady je odpovídající obrazovka v nástroji System Center Data Protection Manager: <br/>
    ![Import aplikace SC DPM a Azure Backup Server – obrazovka](./media/backup-azure-backup-import-export/dpmoffline.png)

    Popis vstupů je následující:

   * **Pracovní umístění**: Dočasné umístění úložiště, do kterého je zapisována prvotní záložní kopie. Pracovní umístění může být ve sdílené síťové složce nebo na místním počítači. Pokud je počítač kopírování a zdrojový počítač jiný, doporučujeme zadat úplnou cestu k pracovnímu umístění v síti.
   * **Název úlohy importu do Azure**: Jedinečný název, pomocí kterého služba Azure import Service a Azure Backup sleduje přenos dat odeslaných na disky do Azure.
   * **Nastavení publikování v Azure**: Zadejte místní cestu k souboru nastavení publikování.
   * **ID předplatného Azure**: ID předplatného Azure pro předplatné, ze kterého jste stáhli soubor nastavení publikování v Azure.
   * **Účet Azure Storage**: Název účtu úložiště v předplatném Azure přidruženého k souboru nastavení publikování v Azure
   * **Azure Storage kontejner**: Název cílového objektu BLOB úložiště v účtu služby Azure Storage, do kterého se naimportují data záloh.

     Uložte *pracovní umístění* a *název úlohy importu Azure* , který jste zadali, protože je potřeba k přípravě disků.  

2. Dokončete pracovní postup, a pokud chcete spustit offline kopii zálohování, klikněte na **Zálohovat nyní** v konzole pro správu agenta Azure Backup. Počáteční záloha je zapsána do pracovní oblasti v rámci tohoto kroku.

    ![Zálohovat hned](./media/backup-azure-backup-import-export/backupnow.png)

    Chcete-li dokončit odpovídající pracovní postup v nástroji System Center Data Protection Manager nebo Azure Backup serveru, klikněte pravým tlačítkem na **skupinu ochrany**a poté zvolte možnost **vytvořit bod obnovení** . Pak zvolíte možnost **Online ochrany** .

    ![SC DPM a Azure Backup Server zálohovat nyní](./media/backup-azure-backup-import-export/dpmbackupnow.png)

    Po dokončení operace je pracovní umístění připravené k použití pro přípravu disku.

    ![Průběh zálohování](./media/backup-azure-backup-import-export/opbackupnow.png)

### <a name="prepare-sata-drives-and-ship-to-azure"></a>Příprava jednotek SATA a odeslání do Azure
Nástroj *AzureOfflineBackupDiskPrep* se používá k přípravě jednotek SATA, které se odesílají do nejbližšího datacentra Azure. Tento nástroj je k dispozici v instalačním adresáři agenta Recovery Services v následující cestě:

*\\Agenti\\Microsoft Azure Recovery Services\\*

1. Do adresáře přejdete a zkopírujte adresář **AzureOfflineBackupDiskPrep** do kopírovacího počítače, do kterého se mají připojit jednotky SATA, které mají být připraveny. V souvislosti s kopírováním počítače zajistěte následující:

   * Počítač pro kopírování může získat přístup k pracovnímu umístění pracovního postupu offline-osazení pomocí stejné síťové cesty, kterou jste zadali v pracovním postupu **zahájení offline zálohování** .
   * V počítači pro kopírování je povolený nástroj BitLocker.
   * Počítač pro kopírování má přístup k Azure Portal.

     V případě potřeby může být počítač kopírování stejný jako zdrojový počítač.

     > [!IMPORTANT]
     > Pokud je zdrojový počítač virtuální počítač, je nutné použít jiný fyzický server nebo klientský počítač jako počítač pro kopírování.


2. Otevřete příkazový řádek se zvýšenými oprávněními na počítači pro kopírování pomocí adresáře nástrojů *AzureOfflineBackupDiskPrep* jako aktuální adresář a spusťte následující příkaz:

    `*.\AzureOfflineBackupDiskPrep.exe*   s:<*Staging Location Path*>   [p:<*Path to AzurePublishSettingsFile*>]`

    | Parametr | Popis |
    | --- | --- |
    | s:&lt;*cesta k pracovnímu umístění*&gt; |Povinný vstup, který slouží k zadání cesty k pracovnímu umístění, které jste zadali v pracovním postupu **zahájení offline zálohování** . |
    | p:&lt;*cesta k PublishSettingsFile*&gt; |Volitelný vstup, který slouží k zadání cesty k souboru **Nastavení publikování v Azure** , který jste zadali v pracovním postupu **zahájení offline zálohování** . |

    > [!NOTE]
    > Hodnota cesta k AzurePublishSettingFile&gt; je povinná, pokud se počítač kopírování a zdrojový počítač liší. &lt;
    >
    >

    Když příkaz spustíte, nástroj vyžádá výběr úlohy importu Azure, která odpovídá diskům, které je potřeba připravit. Pokud je k zadanému pracovnímu umístění přidružená jenom jedna úloha importu, zobrazí se vám stejná obrazovka jako ta, která následuje.

    ![Vstup nástroje pro přípravu disku Azure](./media/backup-azure-backup-import-export/azureDiskPreparationToolDriveInput.png) <br/>

3. Zadejte písmeno jednotky bez koncové dvojtečky pro připojený disk, který chcete připravit na přenos do Azure. Po zobrazení výzvy zadejte potvrzení formátování jednotky.

    Nástroj pak začne připravovat disk a kopírovat data zálohy. Po zobrazení výzvy nástrojem může být nutné připojit další disky pro případ, že na poskytnutém disku není dostatek místa pro zálohovaná data. <br/>

    Na konci úspěšného provedení tohoto nástroje se jeden nebo víc disků, které jste zadali, připravují k odeslání do Azure. Kromě toho se v Azure vytvoří úloha importu s názvem, který jste zadali během iniciování pracovního postupu pro **offline zálohování** . Nástroj nakonec zobrazí dodací adresu v datovém centru Azure, kde je potřeba dodat disky.

    ![Příprava disku Azure dokončena](./media/backup-azure-backup-import-export/azureDiskPreparationToolSuccess.png)<br/>

4. Na konci příkazu se zobrazí také možnost aktualizovat informace o dopravě, jak je znázorněno níže:

    ![Možnost aktualizace informací o expedici](./media/backup-azure-backup-import-export/updateshippingutility.png)<br/>

5. Podrobnosti můžete zadat hned. Tento nástroj vás provede procesem zahrnujícím řadu vstupů. Pokud ale nemáte informace, jako je sledovací číslo nebo jiné podrobnosti související s dodávkou, můžete relaci ukončit. V tomto článku jsou uvedené pokyny k aktualizaci podrobností o dopravě později.

6. Dodejte disky na adresu, kterou poskytuje nástroj, a udržujte sledovací číslo pro budoucí referenci.

   > [!IMPORTANT]
   > Žádné dvě úlohy importu Azure nemohou mít stejné sledovací číslo. Zajistěte, aby jednotky připravené nástrojem v rámci jedné úlohy importu Azure byly dodávány společně v jednom balíčku a aby existovalo jedno jedinečné sledovací číslo pro daný balíček. Nekombinujte jednotky připravené jako součást **různých** úloh importu Azure v jednom balíčku.

5. Pokud máte informace o čísle sledování, přečtěte si zdrojový počítač, který čeká na dokončení úlohy importování, a spusťte následující příkaz v příkazovém řádku se zvýšenými oprávněními s adresářem nástrojů *AzureOfflineBackupDiskPrep* jako aktuální adresář:

   `*.\AzureOfflineBackupDiskPrep.exe*  u:`

   Volitelně můžete spustit následující příkaz z jiného počítače, jako je například *kopírovací počítač*, s adresářem nástroje *AzureOfflineBackupDiskPrep* jako aktuální adresář:

   `*.\AzureOfflineBackupDiskPrep.exe*  u:  s:<*Staging Location Path*>   p:<*Path to AzurePublishSettingsFile*>`

    | Parametr | Popis |
    | --- | --- |
    | u: | Povinný vstup, který se používá k aktualizaci podrobností o dopravě pro úlohu importu Azure |
    | s:&lt;*cesta k pracovnímu umístění*&gt; | Povinný vstup, pokud se příkaz na zdrojovém počítači nespustí. Slouží k zadání cesty k pracovnímu umístění, které jste zadali v pracovním postupu **zahájení offline zálohování** . |
    | p:&lt;*cesta k PublishSettingsFile*&gt; | Povinný vstup, pokud se příkaz na zdrojovém počítači nespustí. Slouží k zadání cesty k souboru **Nastavení publikování v Azure** , který jste zadali v pracovním postupu **zahájení offline zálohování** . |

    Nástroj automaticky detekuje úlohu importu, na které zdrojový počítač čeká, nebo úlohy importu, které jsou přidruženy k pracovnímu umístění při spuštění příkazu v jiném počítači. Pak poskytuje možnost aktualizovat informace o dopravě prostřednictvím řady vstupů, jak je znázorněno níže:

    ![Zadávání informací o expedici](./media/backup-azure-backup-import-export/shippinginputs.png)<br/>

6. Po zadání všech vstupů si pečlivě Projděte podrobnosti a potvrďte informace o expedici, které jste zadali, zadáním *Ano*.

    ![Zkontrolovat informace o expedici](./media/backup-azure-backup-import-export/reviewshippinginformation.png)<br/>

7. Po úspěšné aktualizaci informací o expedici poskytuje nástroj místní umístění, kde jsou uvedeny podrobnosti o expedici, jak je uvedeno níže.

    ![Ukládání informací o expedici](./media/backup-azure-backup-import-export/storingshippinginformation.png)<br/>

   > [!IMPORTANT]
   > Zajistěte, aby jednotky byly dostupné v datacentru Azure během dvou týdnů od poskytování informací o expedici pomocí nástroje *AzureOfflineBackupDiskPrep* . V takovém případě může dojít k tomu, že se jednotky nezpracovávají.  

Až provedete výše uvedené kroky, datacentrum Azure je připravené přijímat jednotky a dále je zpracovávat k přenosu zálohovaných dat z jednotek na klasický typ účtu úložiště Azure, který jste vytvořili.

### <a name="time-to-process-the-drives"></a>Čas ke zpracování jednotek
Doba potřebná ke zpracování úlohy importu do Azure se liší v závislosti na různých faktorech, jako je například doba expedice, typ úlohy, typ a velikost kopírovaných dat a velikost poskytnutých disků. Služba Azure import/export nemá smlouvu SLA, ale po přijetí těchto disků se služba snaží dokončit Kopírování zálohovaných dat do účtu služby Azure Storage 7 až 10 dní. V další části najdete podrobné informace o tom, jak můžete monitorovat stav úlohy importu Azure.

### <a name="monitoring-azure-import-job-status"></a>Monitorování stavu úlohy pro import do Azure
Zatímco vaše jednotky jsou v cestě nebo v datacentru Azure, které se mají zkopírovat do účtu úložiště, Azure Backup Agent nebo SC DPM nebo konzola Azure Backup serveru na zdrojovém počítači zobrazuje následující stav úlohy pro naplánované zálohy.

  `Waiting for Azure Import Job to complete. Please check on Azure Management portal for more information on job status`

Chcete-li zjistit stav úlohy importu, postupujte podle následujících kroků.
1. Na zdrojovém počítači otevřete příkazový řádek se zvýšenými oprávněními a spusťte následující příkaz:

     `AzureOfflineBackupDiskPrep.exe u:`

2.  Výstup zobrazuje aktuální stav úlohy importu, jak je znázorněno níže:

    ![Kontroluje se stav úlohy importu.](./media/backup-azure-backup-import-export/importjobstatusreporting.png)<br/>

Další informace o různých stavech úlohy importu Azure najdete v [tomto článku](../storage/common/storage-import-export-view-drive-status.md) .

### <a name="complete-the-workflow"></a>Dokončení pracovního postupu
Po dokončení úlohy importu budou data počáteční zálohy k dispozici ve vašem účtu úložiště. V době příštího naplánovaného zálohování kopíruje Azure data z účtu úložiště do trezoru Recovery Services, jak je znázorněno níže:

   ![Kopírování dat do trezoru Recovery Services](./media/backup-azure-backup-import-export/copyingfromstorageaccounttoazurebackup.png)<br/>

V době příštího plánovaného zálohování Azure Backup provede přírůstkové zálohování při prvotním záložním kopírování.

## <a name="next-steps"></a>Další postup
* Všechny dotazy k pracovnímu postupu importu/exportu v Azure najdete [v tématu použití služby Microsoft Azure import/export k přenosu dat do úložiště objektů BLOB](../storage/common/storage-import-export-service.md).
* V části offline zálohování Azure Backup [Nejčastější dotazy](backup-azure-backup-faq.md) týkající se všech otázek k pracovnímu postupu.
