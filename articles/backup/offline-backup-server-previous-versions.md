---
title: Offline zálohování pro Data Protection Manager (DPM) a server pro Microsoft Azure Backup (MABS) – předchozí verze
description: Pomocí Azure Backup můžete odesílat data mimo síť pomocí služby Azure import/export. Tento článek vysvětluje pracovní postup offline zálohování pro předchozí verze aplikace DPM a Azure Backup Server.
ms.topic: conceptual
ms.date: 06/08/2020
ms.openlocfilehash: 0405ab66b7714f00349419e94bb064267ca711a6
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "98702181"
---
# <a name="offline-backup-workflow-for-dpm-and-azure-backup-server-previous-versions"></a>Pracovní postup offline zálohování pro DPM a Azure Backup Server (předchozí verze)

>[!IMPORTANT]
>Tyto kroky platí pro DPM 2019 RTM a starší verze a MABS V3 RTM a starší verze.

Azure Backup má několik předdefinovaných efektivit, které při počátečním úplném zálohování dat do Azure šetří náklady na síť a úložiště. Počáteční úplné zálohování obvykle přenáší velké objemy dat a vyžaduje více šířky pásma sítě ve srovnání s dalšími zálohami, které přenášejí pouze rozdíly a přírůstkové. Azure Backup zkomprimuje počáteční zálohy. Díky procesu offline osazení může Azure Backup použít disky k nahrání komprimovaných počátečních zálohovaných dat offline do Azure.

Proces offline nasazení Azure Backup je úzce integrovaný do [služby Azure import/export](../import-export/storage-import-export-service.md). Tuto službu můžete použít k přenosu dat do Azure pomocí disků. Pokud máte terabajty (TBs) počátečních zálohovaných dat, která je potřeba přenést po síti s vysokou latencí a malou šířkou pásma, můžete k odeslání prvotní záložní kopie na jednom nebo více pevných discích do datacentra Azure použít pracovní postup offline. Tento článek poskytuje přehled a další kroky, které dokončí tento pracovní postup pro System Center Data Protection Manager (DPM) a server Microsoft Azure Backup (MABS).

> [!NOTE]
> Proces offline zálohování agenta Microsoft Azure Recovery Services (MARS) se liší od DPM a MABS. Informace o použití zálohování offline s agentem MARS najdete v tématu [pracovní postup offline zálohování v Azure Backup](backup-azure-backup-import-export.md). Zálohování v režimu offline není podporováno pro zálohy stavu systému pomocí agenta Azure Backup.
>

## <a name="overview"></a>Přehled

Díky funkcím pro online osazení Azure Backup a službě Azure pro import/export je jednoduché nahrávat data do Azure v režimu offline pomocí disků. Proces zálohování offline zahrnuje následující kroky:

> [!div class="checklist"]
>
> * Zálohovaná data se zapisují do pracovního umístění, místo aby se odesílala přes síť.
> * Data v pracovním umístění se pak zapisují na jeden nebo více disků SATA pomocí nástroje *AzureOfflineBackupDiskPrep* .
> * Pomocí nástroje se automaticky vytvoří úloha importu Azure.
> * Jednotky SATA se pak odesílají do nejbližšího datacentra Azure.
> * Po dokončení nahrávání zálohovaných dat do Azure Azure Backup zkopíruje zálohovaná data do trezoru záloh a naplánují se přírůstkové zálohy.

## <a name="supported-configurations"></a>Podporované konfigurace

Offline zálohování je podporované pro všechny modely nasazení Azure Backup, které zálohují data z místního prostředí do cloudu Microsoftu. Mezi tyto modely patří:

> [!div class="checklist"]
>
> * Zálohování souborů a složek pomocí agenta MARS nebo agenta Azure Backup.
> * Zálohování všech úloh a souborů pomocí aplikace DPM.
> * Zálohování všech úloh a souborů pomocí MABS.

>[!NOTE]
>Předplatné Azure CSP se nepodporuje pro použití s offline osazením pro DPM 2019 RTM a staršími verzemi a MABS V3 RTM a staršími verzemi. Online zálohování prostřednictvím sítě se pořád podporuje.

## <a name="prerequisites"></a>Předpoklady

Než spustíte pracovní postup offline zálohování, ujistěte se, že jsou splněné následující předpoklady:

* Byl vytvořen [Recovery Services trezor](backup-azure-recovery-services-vault-overview.md) . Pokud ho chcete vytvořit, postupujte podle kroků v části [Vytvoření trezoru Recovery Services](tutorial-backup-windows-server-to-azure.md#create-a-recovery-services-vault) .
* Agent Azure Backup nebo MABS nebo DPM byl nainstalován buď na Windows Server, nebo na klienta Windows, a počítač je zaregistrovaný v trezoru Recovery Services. Zajistěte, aby se používala jenom [nejnovější verze Azure Backup](https://go.microsoft.com/fwlink/?linkid=229525) .
* [Stáhněte si soubor nastavení publikování v Azure](https://portal.azure.com/#blade/Microsoft_Azure_ClassicResources/PublishingProfileBlade) na počítači, ze kterého plánujete zálohovat vaše data. Předplatné, ze kterého stahujete soubor nastavení publikování, se může lišit od předplatného, které obsahuje Recovery Services trezor. Pokud je vaše předplatné ve službě Azure cloudy, pak pomocí následujících odkazů stáhněte soubor s nastavením publikování v Azure.

    | Oblast cloudu svrchovan | Odkaz na soubor nastavení publikování v Azure |
    | --- | --- |
    | USA | [Odkaz](https://portal.azure.us#blade/Microsoft_Azure_ClassicResources/PublishingProfileBlade) |
    | Čína | [Odkaz](https://portal.azure.cn/#blade/Microsoft_Azure_ClassicResources/PublishingProfileBlade) |

* V předplatném, ze kterého jste stáhli soubor nastavení publikování, byl vytvořen účet služby Azure Storage s modelem nasazení Správce prostředků. V účtu úložiště vytvořte nový kontejner objektů blob, který se použije jako cíl.

  ![Vytvoření účtu úložiště pomocí Správce prostředkůho vývoje](./media/offline-backup-dpm-mabs-previous-versions/storage-account-resource-manager.png)

* Vytvoří se pracovní umístění, které může být sdílená síťová složka nebo jakákoli další jednotka na počítači, interní nebo externí, s dostatkem místa na disku pro uložení počáteční kopie. Pokud třeba chcete zálohovat souborový server 500-GB, ujistěte se, že pracovní oblast má aspoň 500 GB. (V důsledku komprese se používá menší množství.)
* U disků odeslaných do Azure zajistěte, aby se používaly interní pevné disky SATA nebo 2,5, 2,5 nebo 3,5. Můžete použít pevné disky až do 10 TB. Nejnovější sadu jednotek, které služba podporuje, najdete v [dokumentaci ke službě Import/export v Azure](../import-export/storage-import-export-requirements.md#supported-hardware) .
* Jednotky SATA musí být připojené k počítači (označovanému jako *počítač pro kopírování*), ze kterého se provádí Kopírování zálohovaných dat z pracovního umístění na jednotky SATA. Ujistěte se, že je na počítači pro kopírování povolený nástroj BitLocker.

## <a name="prepare-the-server-for-the-offline-backup-process"></a>Příprava serveru pro proces offline zálohování

>[!NOTE]
> Pokud v instalaci agenta MARS nemůžete najít uvedené nástroje, například *AzureOfflineBackupCertGen.exe*, zapište do, abyste k AskAzureBackupTeam@microsoft.com nim měli přístup.

* Na serveru otevřete příkazový řádek se zvýšenými oprávněními a spusťte následující příkaz:

    ```cmd
    AzureOfflineBackupCertGen.exe CreateNewApplication SubscriptionId:<Subs ID>
    ```

    Nástroj vytvoří aplikaci Active Directory offline zálohování Azure, pokud neexistuje.

    Pokud aplikace již existuje, zobrazí se výzva k ručnímu nahrání certifikátu do aplikace v tenantovi. Podle pokynů v [této části](#manually-upload-an-offline-backup-certificate) Nahrajte certifikát do aplikace ručně.

* Nástroj *AzureOfflineBackupCertGen.exe* generuje *OfflineApplicationParams.xml* soubor. Zkopírujte tento soubor na server pomocí MABS nebo DPM.
* Nainstalujte [nejnovějšího agenta Mars](https://aka.ms/azurebackup_agent) na instanci aplikace DPM nebo na server Azure Backup.
* Zaregistrujte server do Azure.
* Spusťte následující příkaz:

    ```cmd
    AzureOfflineBackupCertGen.exe AddRegistryEntries SubscriptionId:<subscriptionid> xmlfilepath:<path of the OfflineApplicationParams.xml file>  storageaccountname:<storageaccountname to be used for offline backup>
    ```

* Předchozí příkaz vytvoří soubor `C:\Program Files\Microsoft Azure Recovery Services Agent\Scratch\MicrosoftBackupProvider\OfflineApplicationParams_<Storageaccountname>.xml` .

## <a name="manually-upload-an-offline-backup-certificate"></a>Ruční nahrání certifikátu offline zálohování

Pomocí těchto kroků ručně Nahrajte certifikát offline zálohování do dříve vytvořené Azure Active Directory aplikace určené pro offline zálohování.

1. Přihlaste se k webu Azure Portal.
1. Přejít na **Azure Active Directory**  >  **Registrace aplikací**.
1. Na kartě **vlastněné aplikace** vyhledejte aplikaci ve formátu zobrazovaného názvu `AzureOfflineBackup _<Azure User Id` .

    ![Najít aplikaci na kartě vlastní aplikace](./media/offline-backup-dpm-mabs-previous-versions/owned-applications.png)

1. Vyberte aplikaci. V části **Spravovat** v levém podokně přejdete na **certifikáty & tajných** kódů.
1. Vyhledejte již existující certifikáty nebo veřejné klíče. Pokud žádné nemáte, můžete aplikaci bezpečně odstranit tak, že na stránce **Přehled** aplikace vyberete tlačítko **Odstranit** . Pak můžete opakovat postup pro [přípravu serveru pro offline proces zálohování](#prepare-the-server-for-the-offline-backup-process) a přeskočit následující kroky. V opačném případě pokračujte dále podle těchto kroků z instance aplikace DPM nebo serveru Azure Backup, kde chcete nakonfigurovat offline zálohování.
1. V **nabídce Start** – **Spusťte** zadejte *Certlm. msc*. V okně **certifikáty – místní počítač** vyberte kartu **certifikáty – místní počítač**  >  **osobní** . vyhledejte certifikát s názvem `CB_AzureADCertforOfflineSeeding_<ResourceId>` .
1. Vyberte certifikát, klikněte pravým tlačítkem na **všechny úlohy** a pak vyberte **exportovat** bez privátního klíče ve formátu. cer.
1. V Azure Portal přejdete do aplikace Azure offline Backup.
1. Vyberte **Spravovat**  >  **certifikáty & tajných klíčů**  >  **nahrát certifikát**. Nahrajte certifikát exportovaný v předchozím kroku.

    ![Odeslání certifikátu](./media/offline-backup-dpm-mabs-previous-versions/upload-certificate.png)

1. Na serveru otevřete registr zadáním příkazu **Regedit** v okně Spustit.
1. Přejít na položku registru *Computer\HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Config\CloudBackupProvider*.
1. Klikněte pravým tlačítkem na **CloudBackupProvider** a přidejte novou řetězcovou hodnotu s názvem `AzureADAppCertThumbprint_<Azure User Id>` .

    >[!NOTE]
    > Pokud chcete najít ID uživatele Azure, proveďte jeden z následujících kroků:
    >
    >* Z PowerShellu připojeného k Azure spusťte `Get-AzureRmADUser -UserPrincipalName "Account Holder's email as appears in the portal"` příkaz.
    >* Přejít na cestu k registru `Computer\HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\DbgSettings\OnlineBackup; Name: CurrentUserId;` .

1. Klikněte pravým tlačítkem na řetězec přidaný v předchozím kroku a vyberte **změnit**. V poli hodnota zadejte kryptografický otisk certifikátu, který jste exportovali v kroku 7. Pak vyberte **OK**.
1. Pokud chcete získat hodnotu kryptografického otisku, poklikejte na certifikát. Vyberte kartu **Podrobnosti** a posuňte se dolů, dokud se nezobrazí pole kryptografický otisk. Vyberte **kryptografický otisk** a zkopírujte hodnotu.

    ![Kopírovat hodnotu z pole kryptografický otisk](./media/offline-backup-dpm-mabs-previous-versions/thumbprint-field.png)

1. Pokračujte v části [pracovní postup](#workflow) a pokračujte v procesu offline zálohování.

## <a name="workflow"></a>Pracovní postup

Informace v této části vám pomůžou dokončit pracovní postup offline zálohování, aby se vaše data mohla doručovat do datacentra Azure a nahrály na Azure Storage. Pokud máte dotazy týkající se služby pro import nebo jakéhokoli aspektu procesu, přečtěte si [dokumentaci přehled služby Import služby](../import-export/storage-import-export-service.md) , na kterou se odkazuje dříve.

### <a name="initiate-offline-backup"></a>Zahájit zálohování offline

1. Při plánování zálohování se zobrazí následující stránka v systému Windows Server, klient Windows nebo DPM.

    ![Importovat stránku](./media/offline-backup-dpm-mabs-previous-versions/offline-backup-screen-inputs.png)

    Zde je odpovídající stránka v aplikaci DPM. <br/>

    ![Stránka Import aplikace DPM a Azure Backup Server](./media/offline-backup-dpm-mabs-previous-versions/dpm-offline.png)

    Pole, která vyplníte, jsou:

   * **Pracovní umístění**: dočasné umístění úložiště, do kterého se vytvořila prvotní záložní kopie. Pracovní umístění může být ve sdílené síťové složce nebo na místním počítači. Pokud je počítač kopírování a zdrojový počítač jiný, zadejte úplnou síťovou cestu k pracovnímu umístění.
   * **Nastavení publikování v Azure**: místní cesta k souboru nastavení publikování.
   * **Název úlohy pro import do Azure**: jedinečný název, pomocí kterého služba Azure Import/Export a Azure Backup sleduje přenos dat odeslaných na disky do Azure.
   * **ID předplatného Azure**: ID předplatného Azure pro předplatné, ze kterého jste stáhli soubor nastavení publikování v Azure.
   * **Účet Azure Storage**: název účtu úložiště v předplatném Azure přidruženého k souboru nastavení publikování v Azure.
   * **Azure Storage kontejner**: název cílového objektu BLOB úložiště v účtu služby Azure Storage, do kterého se importují zálohovaná data.

   Uložte **pracovní umístění** a informace o **názvu úlohy importu Azure** , které jste zadali. Je nutné připravit disky.

1. Dokončete pracovní postup. Chcete-li spustit kopii offline zálohování, vyberte možnost **Zálohovat nyní** v konzole pro správu agenta Azure Backup. Počáteční záloha je zapsána do pracovní oblasti v rámci tohoto kroku.

    ![Zálohovat hned](./media/offline-backup-dpm-mabs-previous-versions/backup-now.png)

    Chcete-li dokončit odpovídající pracovní postup v aplikaci DPM nebo Azure Backup Server, klikněte pravým tlačítkem myši na **skupinu ochrany**. Vyberte možnost **vytvořit bod obnovení** . Pak vyberte možnost **Online ochrany** .

    ![Zálohování DPM a MABS nyní](./media/offline-backup-dpm-mabs-previous-versions/dpm-backup-now.png)

    Po dokončení operace je pracovní umístění připravené k použití pro přípravu disku.

    ![Průběh zálohování](./media/offline-backup-dpm-mabs-previous-versions/op-backup-now.png)

### <a name="prepare-sata-drives-and-ship-to-azure"></a>Příprava jednotek SATA a odeslání do Azure

Nástroj *AzureOfflineBackupDiskPrep* se používá k přípravě jednotek SATA, které se odesílají do nejbližšího datacentra Azure. Tento nástroj je k dispozici v instalačním adresáři agenta Recovery Services v následující cestě:

`*\\Microsoft Azure Recovery Services Agent\Utils\*`

1. Do adresáře přejdete a zkopírujte adresář *AzureOfflineBackupDiskPrep* do kopírovacího počítače, do kterého se mají připojit jednotky SATA, které mají být připraveny. Zajistěte, aby:

   * Počítač pro kopírování může získat přístup k pracovnímu umístění pracovního postupu offline-osazení pomocí stejné síťové cesty, kterou jste zadali v pracovním postupu v části "zahájení zálohování offline".
   * V počítači pro kopírování je povolený nástroj BitLocker.
   * Počítač pro kopírování má přístup k Azure Portal. V případě potřeby může být počítač kopírování stejný jako zdrojový počítač.

     > [!IMPORTANT]
     > Pokud je zdrojový počítač virtuální počítač, je nutné použít jiný fyzický server nebo klientský počítač jako počítač pro kopírování.

1. Otevřete příkazový řádek se zvýšenými oprávněními na počítači pro kopírování s adresářem nástroje *AzureOfflineBackupDiskPrep* jako s aktuálním adresářem. Spusťte následující příkaz:

    `*.\AzureOfflineBackupDiskPrep.exe*   s:<*Staging Location Path*>   [p:<*Path to AzurePublishSettingsFile*>]`

    | Parametr | Popis |
    | --- | --- |
    | s: &lt; *cesta k pracovnímu umístění*&gt; |Tento povinný vstup slouží k zadání cesty k pracovnímu umístění, které jste zadali v pracovním postupu v části "zahájení offline zálohování". |
    | p: &lt; *cesta k PublishSettingsFile*&gt; |Tento nepovinný vstup slouží k zadání cesty k souboru nastavení publikování v Azure, který jste zadali v pracovním postupu v části "zahájení offline zálohování". |

    > [!NOTE]
    > &lt;Hodnota cesta k AzurePublishSettingFile &gt; je povinná, pokud se počítač kopírování a zdrojový počítač liší.
    >
    >

    Když příkaz spustíte, nástroj vyžádá výběr úlohy importu Azure, která odpovídá diskům, které je potřeba připravit. Pokud je k dispozici pouze jedna úloha importu s dodaným pracovním umístěním, zobrazí se stránka podobná této.

    ![Vstup nástroje pro přípravu disku Azure](./media/offline-backup-dpm-mabs-previous-versions/azure-disk-preparation-tool-drive-input.png) <br/>

1. Zadejte písmeno jednotky bez koncové dvojtečky pro připojený disk, který chcete připravit na přenos do Azure. Po zobrazení výzvy zadejte potvrzení formátování jednotky.

    Nástroj pak začne připravovat disk a kopírovat zálohovaná data. Pokud na poskytnutém disku není dostatek místa pro zálohovaná data, může být nutné připojit další disky po zobrazení výzvy nástrojem. <br/>

    Po úspěšném dokončení nástroje se k odeslání do Azure připraví jeden nebo víc disků, které jste zadali. V Azure se také vytvoří úloha importu s názvem, který jste zadali během pracovního postupu v části "iniciování offline zálohování". Nástroj nakonec zobrazí dodací adresu v datovém centru Azure, kde je potřeba dodat disky.

    ![Příprava disku Azure dokončena](./media/offline-backup-dpm-mabs-previous-versions/azure-disk-preparation-tool-success.png)<br/>

1. Na konci příkazu se zobrazí také možnost aktualizovat informace o dopravě.

    ![Možnost aktualizace informací o expedici](./media/offline-backup-dpm-mabs-previous-versions/update-shipping-utility.png)<br/>

1. Podrobnosti můžete zadat hned. Tento nástroj vás provede procesem, který zahrnuje řadu vstupů. Pokud nemáte informace, jako je sledovací číslo nebo jiné podrobnosti související s dodávkou, můžete relaci ukončit. V tomto článku jsou uvedené pokyny k aktualizaci podrobností o dopravě později.

1. Dodejte disky na adresu, kterou poskytuje nástroj. Ponechte sledovací číslo pro budoucí referenci.

   > [!IMPORTANT]
   > Žádné dvě úlohy importu Azure nemohou mít stejné sledovací číslo. Zajistěte, aby jednotky připravené nástrojem v rámci jedné úlohy importu Azure byly dodávány společně v jednom balíčku a aby existovalo jedno jedinečné sledovací číslo pro daný balíček. Nekombinujte jednotky připravené jako součást různých úloh importu Azure v jednom balíčku.

1. Po zadání informací o čísle sledování přejdete na zdrojový počítač, který čeká na dokončení úlohy importu. Spusťte následující příkaz v příkazovém řádku se zvýšenými oprávněními s adresářem nástroje *AzureOfflineBackupDiskPrep* jako s aktuálním adresářem.

   `*.\AzureOfflineBackupDiskPrep.exe*  u:`

   Volitelně můžete spustit následující příkaz z jiného počítače, jako je například kopírovat počítač, pomocí adresáře nástrojů *AzureOfflineBackupDiskPrep* jako aktuálního adresáře.

   `*.\AzureOfflineBackupDiskPrep.exe*  u:  s:<*Staging Location Path*>   p:<*Path to AzurePublishSettingsFile*>`

    | Parametr | Popis |
    | --- | --- |
    | h | Tento povinný vstup se používá k aktualizaci informací o dopravě pro úlohu importu Azure. |
    | s: &lt; *cesta k pracovnímu umístění*&gt; | Tento povinný vstup se používá v případě, že se příkaz na zdrojovém počítači nespustí. Slouží k zadání cesty k pracovnímu umístění, které jste zadali v pracovním postupu v části "zahájení offline zálohování". |
    | p: &lt; *cesta k PublishSettingsFile*&gt; | Tento povinný vstup se používá v případě, že se příkaz na zdrojovém počítači nespustí. Slouží k zadání cesty k souboru nastavení publikování v Azure, který jste zadali v pracovním postupu v části "zahájení offline zálohování". |

    Nástroj automaticky detekuje úlohu importu, na které zdrojový počítač čeká, nebo úlohy importu, které jsou přidruženy k pracovnímu umístění při spuštění příkazu v jiném počítači. Pak poskytuje možnost aktualizovat informace o dopravě prostřednictvím řady vstupů.

    ![Zadat informace o expedici](./media/offline-backup-dpm-mabs-previous-versions/shipping-inputs.png)<br/>

1. Po zadání všech vstupů si pečlivě Projděte podrobnosti a potvrďte informace o expedici, které jste zadali, zadáním **Ano**.

    ![Zkontrolovat informace o expedici](./media/offline-backup-dpm-mabs-previous-versions/review-shipping-information.png)<br/>

1. Po úspěšné aktualizaci informací o expedici poskytuje nástroj místní umístění, kde jsou uloženy podrobnosti o expedici, které jste zadali.

    ![Uložit informace o expedici](./media/offline-backup-dpm-mabs-previous-versions/storing-shipping-information.png)<br/>

   > [!IMPORTANT]
   > Zajistěte, aby jednotky byly dostupné v datacentru Azure během dvou týdnů od poskytování informací o expedici pomocí nástroje *AzureOfflineBackupDiskPrep* . V takovém případě může dojít k tomu, že se jednotky nezpracovávají.

Po dokončení předchozích kroků je datové centrum Azure připravené přijímat jednotky a dále je zpracovávat k přenosu zálohovaných dat z jednotek na účet služby Azure Storage s klasickým typem, který jste vytvořili.

### <a name="time-to-process-the-drives"></a>Čas ke zpracování jednotek

Doba potřebná ke zpracování úlohy importu do Azure se liší. Doba zpracování závisí na faktorech, jako je čas expedice, typ úlohy, typ a velikost kopírovaných dat, a velikost poskytnutých disků. Služba Azure import/export nemá smlouvu SLA. Po přijetí disků se služba snaží dokončit Kopírování zálohovaných dat do účtu služby Azure Storage během 7 až 10 dnů. V další části se dozvíte, jak můžete monitorovat stav úlohy importu Azure.

### <a name="monitor-azure-import-job-status"></a>Monitorovat stav úlohy Azure import

Zatímco vaše jednotky jsou v cestě nebo v datacentru Azure, které se mají zkopírovat do účtu úložiště, Azure Backup Agent nebo DPM nebo konzola MABS na zdrojovém počítači zobrazuje následující stav úlohy pro naplánované zálohy:

  `Waiting for Azure Import Job to complete. Please check on Azure Management portal for more information on job status`

Postup kontroly stavu úlohy importu:

1. Na zdrojovém počítači otevřete příkazový řádek se zvýšenými oprávněními a spusťte následující příkaz:

     `AzureOfflineBackupDiskPrep.exe u:`

1. Výstup zobrazuje aktuální stav úlohy importu.

    ![Zkontroluje stav úlohy importu.](./media/offline-backup-dpm-mabs-previous-versions/import-job-status-reporting.png)<br/>

Další informace o různých stavech úlohy importu Azure najdete v tématu [zobrazení stavu úloh Azure import/export](../import-export/storage-import-export-view-drive-status.md).

### <a name="finish-the-workflow"></a>Dokončení pracovního postupu

Po dokončení úlohy importu budou data počáteční zálohy k dispozici ve vašem účtu úložiště. V době příštího naplánovaného zálohování Azure Backup zkopíruje obsah dat z účtu úložiště do trezoru Recovery Services.

   ![Kopírovat data do trezoru Recovery Services](./media/offline-backup-dpm-mabs-previous-versions/copying-from-storage-account-to-azure-backup.png)<br/>

V době příštího plánovaného zálohování Azure Backup provede přírůstkové zálohování při prvotním záložním kopírování.

## <a name="next-steps"></a>Další kroky

* Jakékoli dotazy týkající se pracovního postupu služby Import/export v Azure najdete v tématu [použití služby Microsoft Azure import/export k přenosu dat do úložiště objektů BLOB](../import-export/storage-import-export-service.md).
