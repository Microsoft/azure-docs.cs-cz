---
title: Offline zálohování pro DPM a Azure Backup Server
description: Pomocí služby Azure Backup můžete odesílat data ze sítě pomocí služby Azure Import/Export. Tento článek vysvětluje pracovní postup zálohování offline pro DPM a Azure Backup Server.
ms.reviewer: saurse
ms.topic: conceptual
ms.date: 1/28/2020
ms.openlocfilehash: 080b0bc53b2058bd186e90f354b8f5bcda510414
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78197040"
---
# <a name="offline-backup-workflow-for-dpm-and-azure-backup-server"></a>Pracovní postup zálohování offline pro DPM a Azure Backup Server

Azure Backup má několik integrovaných efektivit, které šetří náklady na síť a úložiště během počátečního úplného zálohování dat do Azure. Počáteční úplné zálohy obvykle přenášejí velké množství dat a vyžadují větší šířku pásma sítě ve srovnání s následnými zálohami, které přenášejí pouze rozdíly/přírůstky. Azure Backup komprimuje počáteční zálohy. Prostřednictvím procesu offline osevací azure backup můžete použít disky k nahrání komprimované počáteční záložní data offline do Azure.

Offline proces seeding u Azure Backup je úzce integrovaný se [službou Azure Import/Export](../storage/common/storage-import-export-service.md). Tuto službu můžete použít k přenosu dat do Azure pomocí disků. Pokud máte terabajty (RB) počátečních záložních dat, které je potřeba přenést přes síť s vysokou latencí a nízkou šířkou pásma, můžete použít pracovní postup offline seedingu k odeslání počáteční záložní kopie na jeden nebo více pevných disků do datového centra Azure. Tento článek obsahuje přehled a další kroky, které dokončí tento pracovní postup pro Správce ochrany dat system center (DPM) a Microsoft Azure Backup Server (MABS).

> [!NOTE]
> Proces zálohování offline pro agenta Microsoft Azure Recovery Services (MARS) se liší od dpm a MABS. Informace o použití offline zálohování s agentem MARS najdete [v tématu Offline pracovní postup zálohování v azure backup](backup-azure-backup-import-export.md). Offline zálohování není podporováno pro zálohování stavu systému provedené pomocí agenta zálohování Azure.
>

## <a name="overview"></a>Přehled

Díky offline secí službě Azure Backup a službě Azure Import/Export je snadné nahrávat data do Azure offline pomocí disků. Proces zálohování offline zahrnuje následující kroky:

> [!div class="checklist"]
>
> * Záložní data se zapisují do pracovního umístění namísto odesílání po síti.
> * Data v pracovním umístění se pak zapisují na jeden nebo více disků SATA pomocí nástroje *AzureOfflineBackupDiskPrep.*
> * Úloha importu Azure se automaticky vytvoří pomocí nástroje.
> * Jednotky SATA se pak odešlou do nejbližšího datového centra Azure.
> * Po dokončení nahrávání dat zálohování do Azure Azure Zálohování zkopíruje záložní data do úložiště záloh a přírůstkové zálohy jsou naplánovány.

## <a name="supported-configurations"></a>Podporované konfigurace

Offline zálohování je podporované pro všechny modely nasazení Služby Azure Backup, které zálohují data z místního do cloudu Microsoftu. Mezi tyto modely patří:

> [!div class="checklist"]
>
> * Zálohování souborů a složek pomocí agenta MARS nebo agenta zálohování Azure.
> * Zálohování všech úloh a souborů pomocí aplikace DPM.
> * Zálohování všech úloh a souborů pomocí MABS.

## <a name="prerequisites"></a>Požadavky

Před zahájením pracovního postupu zálohování offline se ujistěte, že jsou splněny následující požadavky:

* Byl vytvořen [trezor služby Recovery Services.](backup-azure-recovery-services-vault-overview.md) Chcete-li jej vytvořit, postupujte podle pokynů v [části Vytvoření trezoru služby Recovery Services.](tutorial-backup-windows-server-to-azure.md#create-a-recovery-services-vault)
* Agent zálohování Azure nebo MABS nebo DPM byla nainstalována na Windows Server nebo klient systému Windows, podle potřeby a počítač je registrován v trezoru služby Recovery Services. Ujistěte se, že se používá jenom [nejnovější verze azure backupu.](https://go.microsoft.com/fwlink/?linkid=229525)
* [Stáhněte si soubor nastavení publikování Azure](https://portal.azure.com/#blade/Microsoft_Azure_ClassicResources/PublishingProfileBlade) v počítači, ze kterého chcete zálohovat data. Předplatné, ze kterého stáhnete soubor nastavení publikování se může lišit od předplatného, který obsahuje úložiště služby Recovery Services. Pokud je vaše předplatné na suverénní cloudy Azure, pak použijte následující odkazy podle potřeby ke stažení souboru nastavení publikování Azure.

    | Suverénní cloudová oblast | Propojení souborů nastavení publikování Azure |
    | --- | --- |
    | Spojené státy | [Odkaz](https://portal.azure.us#blade/Microsoft_Azure_ClassicResources/PublishingProfileBlade) |
    | Čína | [Odkaz](https://portal.azure.cn/#blade/Microsoft_Azure_ClassicResources/PublishingProfileBlade) |

* Účet úložiště Azure s modelem nasazení Resource Manager u byl vytvořen v předplatném, ze kterého jste stáhli soubor nastavení publikování.

  ![Vytvoření účtu úložiště s vývojem Správce prostředků](./media/backup-azure-backup-import-export/storage-account-resource-manager.png)

* Vytvoří se pracovní umístění, které může být sdílenou sítí nebo jakákoli další jednotka v počítači, interní nebo externí, s dostatkem místa na disku pro uložení počáteční kopie. Chcete-li například zálohovat souborový server o velikosti 500 GB, ujistěte se, že pracovní oblast je alespoň 500 GB. (Menší množství se používá z důvodu komprese.)
* U disků odeslaných do Azure se ujistěte, že se používá pouze 2,5palcový disk SSD nebo 2,5palcový nebo 3,5palcový interní pevné disky SATA II/III. Můžete použít pevné disky až 10 TB. Podívejte se na [dokumentaci služby Azure Import/Export](../storage/common/storage-import-export-requirements.md#supported-hardware) pro nejnovější sadu jednotek, které služba podporuje.
* Jednotky SATA musí být připojeny k počítači (označované jako *kopírovací počítač),* odkud se provádí kopie záložních dat z pracovního umístění do jednotek SATA. Zkontrolujte, zda je v kopírovacím počítači povolen nástroj BitLocker.

## <a name="prepare-the-server-for-the-offline-backup-process"></a>Příprava serveru na proces zálohování offline

>[!NOTE]
> Pokud nemůžete najít uvedené nástroje, jako je například *AzureOfflineBackupCertGen.exe*, v instalaci AskAzureBackupTeam@microsoft.com agenta MARS, zapište, chcete-li k nim získat přístup.

* Otevřete příkazový řádek se zvýšenými oprávněními na serveru a spusťte následující příkaz:

    ```cmd
    AzureOfflineBackupCertGen.exe CreateNewApplication SubscriptionId:<Subs ID>
    ```

    Nástroj vytvoří aplikaci Azure Offline Backup Active Directory, pokud neexistuje.

    Pokud aplikace již existuje, tento spustitelný soubor vás požádá o ruční nahrání certifikátu do aplikace v tenantovi. Podle kroků v [této části](#manually-upload-an-offline-backup-certificate) nahrajte certifikát ručně do aplikace.

* Nástroj *AzureOfflineBackup.exe* generuje soubor *OfflineApplicationParams.xml.* Zkopírujte tento soubor na server pomocí mabs nebo dpm.
* Nainstalujte [nejnovějšího agenta MARS](https://aka.ms/azurebackup_agent) na instanci DPM nebo na server Azure Backup.
* Zaregistrujte server do Azure.
* Spusťte následující příkaz:

    ```cmd
    AzureOfflineBackupCertGen.exe AddRegistryEntries SubscriptionId:<subscriptionid> xmlfilepath:<path of the OfflineApplicationParams.xml file>  storageaccountname:<storageaccountname configured with Azure Data Box>
    ```

* Předchozí příkaz vytvoří `C:\Program Files\Microsoft Azure Recovery Services Agent\Scratch\MicrosoftBackupProvider\OfflineApplicationParams_<Storageaccountname>.xml`soubor .

## <a name="manually-upload-an-offline-backup-certificate"></a>Ruční nahrání zálohovacího certifikátu offline

Chcete-li ručně nahrát certifikát zálohování offline do dříve vytvořené aplikace Azure Active Directory určené pro zálohování offline, postupujte takto.

1. Přihlaste se k portálu Azure.
1. Přejděte na**registrace aplikací** **Azure Active Directory** > .
1. Na kartě **Vlastní aplikace** vyhledejte aplikaci `AzureOfflineBackup _<Azure User Id`s formátem zobrazovaného názvu .

    ![Vyhledání aplikace na kartě Použité aplikace](./media/backup-azure-backup-import-export/owned-applications.png)

1. Vyberte aplikaci. V části **Spravovat** v levém podokně přejděte na **certifikáty & tajných kódů**.
1. Zkontrolujte již existující certifikáty nebo veřejné klíče. Pokud žádné nejsou, můžete aplikaci bezpečně odstranit výběrem tlačítka **Odstranit** na stránce **Přehled** aplikace. Potom můžete zopakovat kroky k [přípravě serveru pro](#prepare-the-server-for-the-offline-backup-process) proces zálohování offline a přeskočit následující kroky. V opačném případě pokračujte v těchto krocích z instance DPM nebo serveru Azure Backup, kde chcete nakonfigurovat zálohování offline.
1. Vyberte kartu **Spravovat aplikaci** > certifikátu počítače**Osobní.** Vyhledejte certifikát s názvem `CB_AzureADCertforOfflineSeeding_<ResourceId>`.
1. Vyberte certifikát, klepněte pravým tlačítkem myši na **všechny úkoly**a pak vyberte **exportovat**bez soukromého klíče ve formátu CER.
1. Přejděte na offline aplikaci pro zálohování Azure na webu Azure Portal.
1. Vyberte **Spravovat** > **certifikáty & tajných kódů** > **Nahrát certifikát**. Nahrajte certifikát exportovaný v předchozím kroku.

    ![Odeslání certifikátu](./media/backup-azure-backup-import-export/upload-certificate.png)

1. Na serveru otevřete registr zadáním **regedit** v okně spustit.
1. Přejděte na položku *registru Počítač\HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Config\CloudBackupProvider*.
1. Klepněte pravým tlačítkem myši na **položku CloudBackupProvider**a přidejte novou hodnotu řetězce s názvem `AzureADAppCertThumbprint_<Azure User Id>`.

    >[!NOTE]
    > Pokud chcete Najít ID uživatele Azure, proveďte jeden z následujících kroků:
    >
    >* Z PowerShellu připojeného k `Get-AzureRmADUser -UserPrincipalName “Account Holder’s email as appears in the portal”` Azure spusťte příkaz.
    >* Přejděte na `Computer\HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\DbgSettings\OnlineBackup; Name: CurrentUserId;`cestu registru .

1. Klepněte pravým tlačítkem myši na řetězec přidaný v předchozím kroku a vyberte **příkaz Změnit**. V hodnotě zadejte kryptografický otisk certifikátu, který jste exportovali v kroku 7. Pak vyberte **OK**.
1. Chcete-li získat hodnotu kryptografického otisku, poklepejte na certifikát. Vyberte kartu **Podrobnosti** a posuňte se dolů, dokud se nezobrazí pole kryptografického otisku. Vyberte **Kryptografický otisk**a zkopírujte hodnotu.

    ![Kopírovat hodnotu z pole kryptografického potisku](./media/backup-azure-backup-import-export/thumbprint-field.png)

1. Pokračujte do části [Pracovní postup](#workflow) a pokračujte v procesu zálohování offline.

## <a name="workflow"></a>Pracovní postup

Informace v této části vám pomohou dokončit pracovní postup zálohování offline, aby se vaše data mohla doručit do datového centra Azure a nahrát do Azure Storage. Pokud máte dotazy týkající se služby importu nebo jakéhokoli aspektu procesu, podívejte se na [dokumentaci přehledu služby importu,](../storage/common/storage-import-export-service.md) na kterou odkazuje dříve.

### <a name="initiate-offline-backup"></a>Zahájení zálohování offline

1. Při plánování zálohování se v systému Windows Server, klientovi systému Windows nebo aplikaci DPM zobrazí následující stránka.

    ![Stránka importu](./media/backup-azure-backup-import-export/offlineBackupscreenInputs.png)

    Zde je odpovídající stránka v DPM. <br/>
    
    ![Stránka importu DPM a Zálohovacího serveru Azure](./media/backup-azure-backup-import-export/dpmoffline.png)

    Vysuňte pole, která vyplníte, jsou:

   * **Pracovní umístění**: Dočasné umístění úložiště, do kterého je zapsána počáteční záložní kopie. Pracovní umístění může být ve sdílené síťové složce nebo v místním počítači. Pokud se kopírovací počítač a zdrojový počítač liší, zadejte úplnou síťovou cestu pracovního umístění.
   * **Nastavení publikování Azure**: Místní cesta k souboru nastavení publikování.
   * **Název úlohy importu Azure:** Jedinečný název, podle kterého služba Azure Import/Export a Azure Backup sledují přenos dat odeslaných na disky do Azure.
   * **ID předplatného Azure**: ID předplatného Azure pro předplatné, ze kterého jste stáhli soubor nastavení publikování Azure.
   * **Účet úložiště Azure**: Název účtu úložiště v předplatném Azure přidruženéka k souboru nastavení publikování Azure.
   * **Azure Storage Container**: Název cílového objektu blob úložiště v účtu úložiště Azure, kde se importují data zálohy.

   Uložte **pracovní umístění** a informace o názvu **úlohy importu Azure,** které jste zadali. Je nutné připravit disky.

1. Dokončete pracovní postup. Pokud chcete spustit offline záložní kopii, vyberte **Zálohovat na** konzoli pro správu agenta zálohování Azure. Počáteční záloha je zapsána do pracovní oblasti jako součást tohoto kroku.

    ![Zálohování nyní](./media/backup-azure-backup-import-export/backupnow.png)

    Chcete-li dokončit odpovídající pracovní postup v aplikaci DPM nebo Azure Backup Server, klepněte pravým tlačítkem myši na **položku Skupina ochrany**. Vyberte možnost **Vytvořit bod obnovení.** Pak vyberte možnost **Online ochrana.**

    ![DPM a MABS zálohovat](./media/backup-azure-backup-import-export/dpmbackupnow.png)

    Po dokončení operace je pracovní umístění připraveno k použití pro přípravu disku.

    ![Průběh zálohování](./media/backup-azure-backup-import-export/opbackupnow.png)

### <a name="prepare-sata-drives-and-ship-to-azure"></a>Příprava disků SATA a odeslání do Azure

Nástroj *AzureOfflineBackupDiskPrep* se používá k přípravě jednotek SATA, které jsou odesílány do nejbližšího datového centra Azure. Tento nástroj je k dispozici v instalačním adresáři agenta služby Recovery Services v následující cestě:

`*\\Microsoft Azure Recovery Services Agent\Utils\*`

1. Přejděte do adresáře a zkopírujte adresář *AzureOfflineBackupDiskPrep* do kopírovacího počítače, ve kterém jsou připojeny jednotky SATA, které mají být připraveny. Zajistit:

   * Kopírovací počítač může přistupovat k pracovnímu umístění pracovního postupu offline seeding pomocí stejné síťové cesty, která byla poskytnuta v pracovním postupu v části Zahájit zálohování offline.
   * Nástroj BitLocker je povolen v kopírovacím počítači.
   * Kopírovací počítač má přístup k portálu Azure. V případě potřeby může být kopírovací počítač stejný jako zdrojový počítač.

     > [!IMPORTANT]
     > Pokud je zdrojový počítač virtuální počítač, je povinné používat jako kopírovací počítač jiný fyzický nebo klientský počítač.

1. Otevřete příkazový řádek se zvýšenými oprávněními v kopírovacím počítači s adresářem nástrojů *AzureOfflineBackupDiskPrep* jako aktuálním adresářem. Spusťte následující příkaz:

    `*.\AzureOfflineBackupDiskPrep.exe*   s:<*Staging Location Path*>   [p:<*Path to AzurePublishSettingsFile*>]`

    | Parametr | Popis |
    | --- | --- |
    | s:&lt;*Cesta pracovního umístění*&gt; |Tento povinný vstup slouží k poskytnutí cesty k pracovnímu umístění, které jste zadali do pracovního postupu v části Zahájit zálohování offline. |
    | p:&lt;*Cesta k PublishSettingsFile*&gt; |Tento volitelný vstup se používá k poskytnutí cesty k souboru nastavení publikování Azure, který jste zadali do pracovního postupu v části Zahájit zálohování offline. |

    > [!NOTE]
    > Cesta &lt;k AzurePublishSettingFile&gt; hodnota je povinná, pokud se liší kopírovací počítač a zdrojový počítač.
    >
    >

    Při spuštění příkazu nástroj požaduje výběr úlohy importu Azure, která odpovídá jednotky, které je třeba připravit. Pokud je k zadanému pracovnímu umístění přidružena pouze jedna úloha importu, zobrazí se stránka, jako je tato.

    ![Vstup nástroje pro přípravu disku Azure](./media/backup-azure-backup-import-export/azureDiskPreparationToolDriveInput.png) <br/>

1. Zadejte písmeno jednotky bez koncovédvojtečky pro připojený disk, který chcete připravit k přenosu do Azure. Po zobrazení výzvy poskytněte potvrzení formátování jednotky.

    Nástroj pak začne připravit disk a zkopírovat záložní data. Pokud daný disk nemá dostatek místa pro záložní data, může být nutné připojit další disky. <br/>

    Po úspěšném dokončení nástroje se připraví jeden nebo více disků, které jste zadali k odeslání do Azure. Úloha importu s názvem, který jste zadali během pracovního postupu v části Zahájit zálohování offline, se také vytvoří v Azure. Nakonec nástroj zobrazí dodací adresu do datového centra Azure, kde je potřeba odeslat disky.

    ![Příprava disku Azure byla dokončena](./media/backup-azure-backup-import-export/azureDiskPreparationToolSuccess.png)<br/>

1. Na konci spuštění příkazu se také zobrazí možnost aktualizovat informace o dopravě.

    ![Aktualizovat informace o přepravě](./media/backup-azure-backup-import-export/updateshippingutility.png)<br/>

1. Můžete zadat podrobnosti ihned. Nástroj vás provede procesem, který zahrnuje řadu vstupů. Pokud nemáte informace, jako je sledovací číslo nebo jiné podrobnosti týkající se dopravy, můžete relaci ukončit. Kroky k aktualizaci podrobností o dopravě později jsou uvedeny v tomto článku.

1. Disky předevčit na adresu, kterou nástroj zadaný. Ponechte sledovací číslo pro budoucí použití.

   > [!IMPORTANT]
   > Žádné dvě úlohy importu Azure nemůže mít stejné číslo sledování. Ujistěte se, že jednotky připravené nástrojem v rámci jedné úlohy importu Azure jsou dodávány společně v jednom balíčku a že pro balíček existuje jediné jedinečné sledovací číslo. Nekombinujte jednotky připravené jako součást různých úloh importu Azure v jednom balíčku.

1. Pokud máte informace o sledovacím čísle, přejděte do zdrojového počítače, který čeká na dokončení úlohy importu. Spusťte následující příkaz v příkazovém řádku se zvýšenými oprávněními s adresářem nástrojů *AzureOfflineBackupDiskPrep* jako aktuálním adresářem.

   `*.\AzureOfflineBackupDiskPrep.exe*  u:`

   Volitelně můžete spustit následující příkaz z jiného počítače, jako je například kopírovací počítač, s adresářem nástroje *AzureOfflineBackupDiskPrep* jako aktuálním adresářem.

   `*.\AzureOfflineBackupDiskPrep.exe*  u:  s:<*Staging Location Path*>   p:<*Path to AzurePublishSettingsFile*>`

    | Parametr | Popis |
    | --- | --- |
    | U: | Tento povinný vstup se používá k aktualizaci podrobností o dopravě pro úlohu importu Azure. |
    | s:&lt;*Cesta pracovního umístění*&gt; | Tento povinný vstup se používá v případě, že příkaz není spuštěn ve zdrojovém počítači. Slouží k poskytnutí cesty k pracovnímu umístění, které jste zadali do pracovního postupu v části Zahájit zálohování offline. |
    | p:&lt;*Cesta k PublishSettingsFile*&gt; | Tento povinný vstup se používá v případě, že příkaz není spuštěn ve zdrojovém počítači. Používá se k poskytnutí cesty k souboru nastavení publikování Azure, který jste zadali do pracovního postupu v části "Zahájit zálohování offline". |

    Nástroj automaticky detekuje úlohu importu, na kterou zdrojový počítač čeká, nebo úlohy importu přidružené k pracovnímu umístění při spuštění příkazu v jiném počítači. Potom poskytuje možnost aktualizovat informace o dopravě prostřednictvím řady vstupů.

    ![Zadejte informace o zásilce.](./media/backup-azure-backup-import-export/shippinginputs.png)<br/>

1. Po poskytnutí všech vstupů si pečlivě prohlédněte podrobnosti a zasuzujte informace o přepravě, které jste poskytli, zadáním **ano**.

    ![Kontrola informací o přepravě](./media/backup-azure-backup-import-export/reviewshippinginformation.png)<br/>

1. Po úspěšné aktualizaci informací o přepravě poskytuje nástroj místní umístění, kde jsou uloženy zadané podrobnosti o dopravě.

    ![Uložit informace o přepravě](./media/backup-azure-backup-import-export/storingshippinginformation.png)<br/>

   > [!IMPORTANT]
   > Ujistěte se, že jednotky dorazí do datového centra Azure do dvou týdnů od poskytnutí informací o dopravě pomocí nástroje *AzureOfflineBackupDiskPrep.* Pokud tak neučiníte, může dojít k tomu, že jednotky nebudou zpracovány. 

Po dokončení předchozích kroků je datové centrum Azure připravené přijímat jednotky a dále je zpracovat k přenosu záložních dat z jednotek do klasického účtu úložiště Azure, který jste vytvořili.

### <a name="time-to-process-the-drives"></a>Čas na zpracování jednotek

Doba, kterou zpracování úlohy importu Azure trvá, se liší. Doba zpracování závisí na faktorech, jako je doba expedice, typ úlohy, typ a velikost kopírovaných dat a velikost poskytnutých disků. Služba Azure Import/Export nemá sla. Po přijetí disků se služba snaží dokončit záložní kopii dat do vašeho účtu úložiště Azure za 7 až 10 dní. V další části je popsáno, jak můžete sledovat stav úlohy importu Azure.

### <a name="monitor-azure-import-job-status"></a>Sledování stavu úlohy importu Azure

Zatímco vaše jednotky jsou v tranzitu nebo v datovém centru Azure, které se mají zkopírovat do účtu úložiště, agent zálohování Azure nebo DPM nebo konzola MABS ve zdrojovém počítači zobrazí následující stav úlohy pro naplánované zálohy:

  `Waiting for Azure Import Job to complete. Please check on Azure Management portal for more information on job status`

Kontrola stavu úlohy importu:

1. Otevřete příkazový řádek se zvýšenými oprávněními ve zdrojovém počítači a spusťte následující příkaz:

     `AzureOfflineBackupDiskPrep.exe u:`

1. Výstup zobrazuje aktuální stav úlohy importu.

    ![Zkontrolovat stav úlohy importu](./media/backup-azure-backup-import-export/importjobstatusreporting.png)<br/>

Další informace o různých stavech úlohy importu Azure najdete [v tématu Zobrazení stavu úloh importu a exportu Azure](../storage/common/storage-import-export-view-drive-status.md).

### <a name="finish-the-workflow"></a>Dokončení pracovního postupu

Po dokončení úlohy importu jsou počáteční data zálohování k dispozici v účtu úložiště. V době další naplánované zálohy Azure Backup zkopíruje obsah dat z účtu úložiště do trezoru služby Recovery Services.

   ![Kopírování dat do trezoru služby Recovery Services](./media/backup-azure-backup-import-export/copyingfromstorageaccounttoazurebackup.png)<br/>

V době dalšího naplánovaného zálohování azure backup provádí přírůstkové zálohování přes počáteční záložní kopii.

## <a name="next-steps"></a>Další kroky

* Jakékoli dotazy týkající se pracovního postupu služby Import a export Azure najdete v [tématu Přenos dat do úložiště objektů Blob pomocí služby Import a Export Microsoft Azure](../storage/common/storage-import-export-service.md).
