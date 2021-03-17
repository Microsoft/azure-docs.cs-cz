---
title: Offline zálohování pro DPM a Azure Backup Server
description: Pomocí Azure Backup můžete odesílat data mimo síť pomocí služby Azure import/export. Tento článek vysvětluje pracovní postup offline zálohování pro DPM a Azure Backup Server.
ms.topic: conceptual
ms.date: 05/24/2020
ms.openlocfilehash: 006c0fa4d67c9a85426d7a007912df65876313da
ms.sourcegitcommit: 75041f1bce98b1d20cd93945a7b3bd875e6999d0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/22/2021
ms.locfileid: "98701809"
---
# <a name="offline-backup-workflow-for-dpm-and-azure-backup-server-mabs"></a>Pracovní postup offline zálohování pro DPM a Azure Backup Server (MABS)

>[!IMPORTANT]
> Tyto kroky platí pro DPM 2019 UR1 (nebo vyšší) a MABS V3 UR1 (nebo vyšší).

System Center Data Protection Manager a Azure Backup Server (MABS) se integrují s Azure Backup a používají několik předdefinovaných efektivit, které šetří náklady na síť a úložiště během počátečních úplných záloh dat do Azure. Počáteční úplné zálohování obvykle přenáší velké objemy dat a vyžaduje více šířky pásma sítě ve srovnání s dalšími zálohami, které přenášejí pouze rozdíly a přírůstkové. Azure Backup zkomprimuje počáteční zálohy. Díky procesu offline osazení může Azure Backup použít disky k nahrání komprimovaných počátečních zálohovaných dat offline do Azure.

Proces offline nasazení Azure Backup je úzce integrovaný do [služby Azure import/export](../import-export/storage-import-export-service.md). Tuto službu můžete použít k přenosu dat do Azure pomocí disků. Pokud máte terabajty (TBs) počátečních zálohovaných dat, která je potřeba přenést po síti s vysokou latencí a malou šířkou pásma, můžete k odeslání prvotní záložní kopie na jednom nebo více pevných discích do datacentra Azure použít pracovní postup offline. Tento článek poskytuje přehled a další kroky, které dokončí tento pracovní postup pro System Center Data Protection Manager (DPM) a server Microsoft Azure Backup (MABS).

> [!NOTE]
> Proces offline zálohování agenta Microsoft Azure Recovery Services (MARS) se liší od DPM a MABS. Informace o použití zálohování offline s agentem MARS najdete v tématu [pracovní postup offline zálohování v Azure Backup](backup-azure-backup-import-export.md). Zálohování v režimu offline není podporováno pro zálohy stavu systému pomocí agenta Azure Backup.
>
> Aktualizace MABS UR1 také přináší verzi Preview pro offline zálohování pomocí Azure Data Box v MABS. [SystemCenterFeedback@microsoft.com](mailto:SystemCenterFeedback@microsoft.com)Pro další informace se obraťte na kontakt.

## <a name="overview"></a>Přehled

Díky funkcím pro online osazení Azure Backup a službě Azure pro import/export je jednoduché nahrávat data do Azure v režimu offline pomocí disků. Proces zálohování offline zahrnuje následující kroky:

> [!div class="checklist"]
>
> * Zálohovaná data se zapisují do pracovního umístění, místo aby se odesílala přes síť.
> * Data v pracovním umístění se pak zapisují na jeden nebo více disků SATA pomocí nástroje *AzureOfflineBackupDiskPrep* .
> * Pomocí nástroje se automaticky vytvoří úloha importu Azure.
> * Jednotky SATA se pak odesílají do nejbližšího datacentra Azure.
> * Po dokončení nahrávání zálohovaných dat do Azure Azure Backup zkopíruje zálohovaná data do trezoru záloh a naplánují se přírůstkové zálohy.

## <a name="prerequisites"></a>Požadavky

Než spustíte pracovní postup offline zálohování, ujistěte se, že jsou splněné následující předpoklady:

* Byl vytvořen [Recovery Services trezor](backup-azure-recovery-services-vault-overview.md) . Pokud ho chcete vytvořit, postupujte podle kroků v části [Vytvoření služby Recovery Services trezor](tutorial-backup-windows-server-to-azure.md#create-a-recovery-services-vault)– Backup-Windows-Server-to-Azure # Create-a-Recovery-Services-trezor).
* Zajistěte, aby v SC DPM nebo MABS byla nainstalovaná jenom [nejnovější verze agenta Microsoft Azure Recovery Services](https://aka.ms/azurebackup_agent) a zaregistrovala se v Recovery Services trezoru.
* Kumulativní aktualizace 1 je nainstalovaná v SC DPM 2019 nebo MABS v3.

  > [!NOTE]
  > Pomocí aplikace DPM 2019 UR1 a MABS V3 UR1 ověřování offline pomocí Azure Active Directory.

* Na serveru DPM nebo MABS se ujistěte, že je nainstalovaný Microsoft Edge nebo Internet Explorer 11 a že je povolený JavaScript.
* Vytvořte účet Azure Storage ve stejném předplatném jako Recovery Services trezor.
* Ujistěte se, že máte [potřebná oprávnění](../active-directory/develop/howto-create-service-principal-portal.md) k vytvoření Azure Active Directory aplikace. Pracovní postup offline zálohování vytvoří aplikaci Azure Active Directory v rámci předplatného přidruženého k účtu Azure Storage. Cílem aplikace je poskytnout Azure Backup se zabezpečeným a vymezeným přístupem ke službě Azure import, kterou vyžaduje pracovní postup offline zálohování.
* Zaregistrujte poskytovatele prostředků Microsoft. ImportExport k předplatnému, které obsahuje účet Azure Storage. Registrace poskytovatele prostředků:
    1. V hlavní nabídce vyberte **předplatná**.
    2. Pokud jste přihlášeni k odběru více předplatných, vyberte předplatné, které používáte pro offline zálohování. Pokud použijete jenom jedno předplatné, zobrazí se vaše předplatné.
    3. V nabídce předplatné vyberte **poskytovatelé prostředků** , abyste zobrazili seznam zprostředkovatelů.
    4. V seznamu zprostředkovatelů přejděte dolů na Microsoft. ImportExport. Pokud je stav NotRegistered, vyberte **zaregistrovat**.

       ![Registrace poskytovatele prostředků](./media/backup-azure-backup-server-import-export/register-import-export.png)

* Vytvoří se pracovní umístění, které může být sdílená síťová složka nebo jakákoli další jednotka na počítači, interní nebo externí, s dostatkem místa na disku pro uložení počáteční kopie. Pokud třeba chcete zálohovat souborový server 500-GB, ujistěte se, že pracovní oblast má aspoň 500 GB. (V důsledku komprese se používá menší množství.)
* U disků odeslaných do Azure zajistěte, aby se používaly interní pevné disky SATA nebo 2,5, 2,5 nebo 3,5. Můžete použít pevné disky až do 10 TB. Nejnovější sadu jednotek, které služba podporuje, najdete v [dokumentaci ke službě Import/export v Azure](../import-export/storage-import-export-requirements.md#supported-hardware) .
* Jednotky SATA musí být připojené k počítači (označovanému jako *počítač pro kopírování*), ze kterého se provádí Kopírování zálohovaných dat z pracovního umístění na jednotky SATA. Ujistěte se, že je na počítači pro kopírování povolený nástroj BitLocker.

## <a name="workflow"></a>Pracovní postup

Informace v této části vám pomůžou dokončit pracovní postup offline zálohování, aby se vaše data mohla doručovat do datacentra Azure a nahrály na Azure Storage. Pokud máte dotazy týkající se služby pro import nebo jakéhokoli aspektu procesu, přečtěte si dokumentaci [Přehled služby Import služby](../import-export/storage-import-export-service.md) , na kterou se odkazuje dříve.

## <a name="initiate-offline-backup"></a>Zahájit zálohování offline

1. Když vytvoříte novou skupinu ochrany pomocí online ochrany nebo přidáte online ochranu do existující skupiny ochrany, zobrazí se následující obrazovka. Pokud chcete vybrat úvodní online metodu replikace, vyberte možnost **přenést pomocí vlastního disku** a vyberte **Další**.

    ![Import obrazovky](./media/backup-azure-backup-server-import-export/create-new-protection-group.png)

2. Otevře se přihlašovací stránka Azure. Přihlaste se pomocí uživatelského účtu Azure, který má oprávnění role *vlastníka* pro předplatné Azure.

    ![Přihlašovací stránka Azure](./media/backup-azure-backup-server-import-export/choose-initial-online-replication.png)

3. Zadejte vstupy na stránce **použít vlastní disk** .

   ![Vstupy pro použití vlastního disku](./media/backup-azure-backup-server-import-export/use-your-own-disk.png)

   Popis vstupů je následující:

   * **Pracovní umístění**: dočasné umístění úložiště, do kterého se vytvořila prvotní záložní kopie. Pracovní umístění může být ve sdílené síťové složce nebo na místním počítači. Pokud je počítač kopírování a zdrojový počítač jiný, zadejte úplnou síťovou cestu k pracovnímu umístění.
   * **Azure Resource Manager účet úložiště**: název účtu úložiště Správce prostředků typu (pro obecné účely v1 nebo pro obecné účely v2) v jakémkoli předplatném Azure.
   * **Azure Storage kontejner**: název cílového kontejneru úložiště objektů BLOB v účtu služby Azure Storage, do kterého se importují zálohovaná data.
   * **ID předplatného Azure**: ID předplatného Azure, kde se vytváří účet Azure Storage.
   * **Název úlohy pro import do Azure**: jedinečný název, podle kterého služba Azure import service a Azure Backup sleduje přenos dat odeslaných na disky do Azure.

    Uložte **pracovní umístění** a informace o **názvu úlohy importu Azure** , které jste zadali. Je nutné připravit disky.

4. Dokončete pracovní postup, aby se tato ochrana vytvořila nebo aktualizovala. Chcete-li spustit kopii v režimu offline zálohování, klikněte pravým tlačítkem myši na **skupinu ochrany** a poté vyberte možnost **vytvořit bod obnovení** . Pak zvolíte možnost **Online ochrany** .

   ![Vytvořit bod obnovení](./media/backup-azure-backup-server-import-export/create-recovery-point.png)

5. Monitorujte úlohu vytvoření repliky online v podokně monitorování. Úloha by měla být úspěšně dokončena s upozorněním na *dokončení úlohy importu do Azure*.

   ![Dokončit bod obnovení](./media/backup-azure-backup-server-import-export/complete-recovery-point.png)

6. Po dokončení operace je pracovní umístění připravené k použití pro přípravu disku.

## <a name="prepare-sata-drives-and-ship-to-azure"></a>Příprava jednotek SATA a odeslání do Azure

Nástroj *AzureOfflineBackupDiskPrep* připraví jednotky SATA, které se odesílají do nejbližšího datacentra Azure. Tento nástroj je k dispozici v instalačním adresáři agenta Azure Backup (v následující cestě): `*\Microsoft Azure Recovery Services Agent\Utils\\*`

1. Přejít do adresáře a zkopírovat adresář **AzureOfflineBackupDiskPrep** do jiného počítače, kde jsou připojeny jednotky SATA. V počítači s připojenými jednotkami SATA zajistěte:

   * Počítač pro kopírování může získat přístup k pracovnímu umístění pracovního postupu offline-osazení pomocí stejné síťové cesty, kterou jste zadali v pracovním postupu v části "zahájení zálohování offline".
   * V počítači pro kopírování je povolený nástroj BitLocker.
   * Azure PowerShell 3.7.0 je nainstalován na počítači kopírování (není vyžadováno, pokud používáte nástroj AzureOfflineBackupDiskPrep na serveru DPM nebo MABS).
   * Jsou nainstalované nejnovější kompatibilní prohlížeče (Microsoft Edge nebo Internet Explorer 11) a je povolený JavaScript.
   * Počítač pro kopírování má přístup k Azure Portal. V případě potřeby může být počítač kopírování stejný jako zdrojový počítač.

     > [!IMPORTANT]
     > Pokud je zdrojový počítač virtuální počítač, je nutné použít jiný fyzický server nebo klientský počítač jako počítač pro kopírování.

1. Otevřete příkazový řádek se zvýšenými oprávněními na počítači pro kopírování s adresářem nástroje *AzureOfflineBackupDiskPrep* jako s aktuálním adresářem. Spusťte následující příkaz:

    ```console
    .\AzureOfflineBackupDiskPrep.exe s:<Staging Location Path>
    ```

    | Parametr | Popis |
    | --- | --- |
    | s: &lt; *cesta k pracovnímu umístění*&gt; |Tento povinný vstup slouží k zadání cesty k pracovnímu umístění, které jste zadali v pracovním postupu v části "zahájení offline zálohování". |
    | p: &lt; *cesta k PublishSettingsFile*&gt; |Tento nepovinný vstup slouží k zadání cesty k souboru nastavení publikování v Azure. |

    Když příkaz spustíte, nástroj vyžádá výběr úlohy importu Azure, která odpovídá diskům, které je potřeba připravit. Pokud je k zadanému pracovnímu umístění přidružená jenom jedna úloha importu, zobrazí se vám stejná obrazovka jako ta, která následuje.

      ![Konzola přípravy disku](./media/backup-azure-backup-server-import-export/disk-prep-console.png)

1. Zadejte písmeno jednotky bez koncové dvojtečky pro připojený disk, který chcete připravit na přenos do Azure.
1. Po zobrazení výzvy zadejte potvrzení formátování jednotky.
1. Budete vyzváni k přihlášení k předplatnému Azure. Zadejte svoje přihlašovací údaje.

    ![Přihlašovací obrazovka Azure](./media/backup-azure-backup-server-import-export/signin-disk-prep.png)

    Nástroj pak začne připravovat disk a kopírovat zálohovaná data. Pokud na poskytnutém disku není dostatek místa pro zálohovaná data, může být nutné připojit další disky po zobrazení výzvy nástrojem. <br/>

    Na konci úspěšného provedení tohoto nástroje příkazový řádek poskytuje tři informace:
    * K odeslání do Azure se připraví jeden nebo víc disků, které jste zadali.
    * Zobrazí se potvrzení, že se vytvořila vaše úloha importu. Úloha importu používá zadaný název.
    * Nástroj zobrazí dodací adresu pro datacentrum Azure.

     ![Příprava disku Azure dokončena](./media/backup-azure-backup-server-import-export/console.png)

1. Na konci příkazu se zobrazí také možnost aktualizovat informace o dopravě.

1. Dodejte disky na adresu, kterou poskytuje nástroj, a udržujte sledovací číslo pro budoucí referenci.

   > [!IMPORTANT]
   > Žádné dvě úlohy importu Azure nemohou mít stejné sledovací číslo. Zajistěte, aby jednotky připravené nástrojem v rámci jedné úlohy importu Azure byly dodávány společně v jednom balíčku a aby existovalo jedno jedinečné sledovací číslo pro daný balíček. Nekombinujte jednotky připravené jako součást různých úloh importu Azure v jednom balíčku.

## <a name="update-shipping-details-on-the-azure-import-job"></a>Aktualizovat podrobnosti o dopravě v úloze importu Azure

Následující postup aktualizuje podrobnosti o expedici úlohy Azure import. Tyto informace obsahují podrobnosti o:

* název přepravce, který disky doručuje do Azure
* vrátit informace o expedici pro vaše disky

   1. Přihlaste se ke svému předplatnému Azure.
   2. V hlavní nabídce vyberte **všechny služby** a v dialogovém okně všechny služby zadejte import. Až uvidíte **úlohy import/export**, vyberte je.
       ![Zadávání informací o expedici](./media/backup-azure-backup-server-import-export/search-import-job.png)

       Otevře se seznam **úloh import/export** a zobrazí se seznam všech úloh importu/exportu ve vybraném předplatném.

   3. Pokud máte více předplatných, nezapomeňte vybrat předplatné používané pro import zálohovaných dat. Pak vyberte nově vytvořenou úlohu importu a otevřete její podrobnosti.

       ![Zkontrolovat informace o expedici](./media/backup-azure-backup-server-import-export/import-job-found.png)

   4. V nabídce nastavení úlohy import vyberte **Spravovat informace o expedici** a zadejte podrobnosti o zpětném odeslání.

       ![Ukládání informací o expedici](./media/backup-azure-backup-server-import-export/shipping-info.png)

   5. Pokud máte sledovací číslo od přepravce, vyberte banner na stránce Přehled úlohy importu Azure a zadejte následující podrobnosti:

      > [!IMPORTANT]
      > Do dvou týdnů od vytvoření úlohy importu do Azure nezapomeňte aktualizovat informace o dopravci a sledovací číslo. Při ověřování těchto informací během dvou týdnů může dojít k odstranění úlohy a nezpracovávání jednotek.

      ![Přehled úlohy](./media/backup-azure-backup-server-import-export/job-overview.png)

      ![Informace o sledování](./media/backup-azure-backup-server-import-export/tracking-info.png)

### <a name="time-to-process-the-drives"></a>Čas ke zpracování jednotek

Doba potřebná ke zpracování úlohy importu do Azure se liší. Doba zpracování závisí na faktorech, jako je čas expedice, typ úlohy, typ a velikost kopírovaných dat, a velikost poskytnutých disků. Služba Azure import/export nemá smlouvu SLA. Po přijetí disků se služba snaží dokončit Kopírování zálohovaných dat do účtu služby Azure Storage během 7 až 10 dnů. V další části se dozvíte, jak můžete monitorovat stav úlohy importu Azure.

### <a name="monitor-azure-import-job-status"></a>Monitorovat stav úlohy Azure import

Stav úlohy importu můžete monitorovat z Azure Portal tak, že přejdete na stránku **úlohy import/export** a vyberete svou úlohu. Další informace o stavu úloh importu najdete v článku o [importu služby Storage pro export](../import-export/storage-import-export-service.md) .

### <a name="complete-the-workflow"></a>Dokončení pracovního postupu

Po dokončení úlohy importu budou data počáteční zálohy k dispozici ve vašem účtu úložiště. V době příštího naplánovaného zálohování Azure Backup zkopíruje obsah dat z účtu úložiště do trezoru Recovery Services.

V okamžiku příští naplánované úlohy vytváření online repliky Data Protection Manager provede přírůstkové zálohování při prvotním kopírování záložní kopie.

## <a name="next-steps"></a>Další kroky

* Jakékoli dotazy týkající se pracovního postupu služby Import/export v Azure najdete v tématu [použití služby Microsoft Azure import/export k přenosu dat do úložiště objektů BLOB](../import-export/storage-import-export-service.md).
