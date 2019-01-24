---
title: Azure Backup - Offline zálohování aplikace DPM a Azure Backup Server
description: Zjistěte, jak vám to poslat data připojen k síti pomocí služby Azure Import/Export umožňuje Azure Backup. Tento článek vysvětluje offline předvyplnění počáteční zálohovací data pomocí služby Azure Import Export.
services: backup
author: saurabhsensharma
manager: shivamg
ms.service: backup
ms.topic: conceptual
ms.date: 5/8/2018
ms.author: saurse
ms.openlocfilehash: 01b90d6bb18addd6a0235101f86b9d51953cc096
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/23/2019
ms.locfileid: "54818553"
---
# <a name="offline-backup-workflow-for-dpm-and-azure-backup-server"></a>Pracovní postup offline zálohování DPM a Azure Backup Server
Azure Backup obsahuje několik předdefinovaných úspor, které ukládají náklady na síť a úložiště během počáteční úplné zálohování dat do Azure. Počáteční úplné zálohování obvykle přenos velkých objemů dat a vyžadují větší šířku pásma sítě, ve srovnání s následné zálohy, které přenášejí pouze rozdíly/přírůstková. Azure Backup komprimuje prvotní zálohy. Procesem offline předvyplnění Azure Backup můžete použít disky k nahrání komprimované počáteční zálohovací data do offline režimu do Azure.

Proces offline předvyplnění služby Azure Backup je úzce integrovaná s [služba Azure Import/Export](../storage/common/storage-import-export-service.md) , která umožňuje přenášet data do Azure s použitím disků. Pokud máte terabajtů (TB) počáteční zálohovací data, které je potřeba přenést přes síť vysokou latencí a malou šířkou pásma, můžete použít pracovní postup offline předvyplnění dodávat kopie prvotní zálohy na jeden nebo více pevných disků do datacentra Azure. Tento článek obsahuje přehled a další podrobnosti o kroky, které tento pracovní postup pro nástroj System Center DPM a Azure Backup serveru.

> [!NOTE]
> Proces zálohování Offline pro agenta Microsoft Azure Recovery Services (MARS) se liší od System Center DPM a Azure Backup Server. Informace o používání Offline zálohování pomocí agenta MARS najdete v tématu [v tomto článku](backup-azure-backup-import-export.md). Zálohování offline není podporováno pro zálohování stavu systému se provádí pomocí agenta Azure Backup. 
>

## <a name="overview"></a>Přehled
S funkcí offline předvyplnění Azure Backup a Azure Import/Export je jednoduchý k odesílání dat do offline režimu do Azure s použitím disků. Proces Offline zálohování zahrnuje následující kroky:

> [!div class="checklist"]
> * Data záloh, místo odeslání přes síť, je zapsána do *pracovní umístění*
> * Data na *pracovní umístění* se poté zapíšou do jednoho nebo několika disků SATA pomocí *AzureOfflineBackupDiskPrep* nástroje
> * Nástroj automaticky vytvoří úlohu služby Azure Import
> * Jednotky SATA se pak odešlou do nejbližší datové centrum Azure
> * Po dokončení nahrávání zálohování dat do Azure, Azure Backup zkopíruje data záloh do trezoru záloh a přírůstkové zálohování.

## <a name="supported-configurations"></a>Podporované konfigurace 
Offline zálohování je podporováno pro všechny modely nasazení z Azure Backup, které mimo pracoviště zálohovaná data z místní ke cloudu Microsoftu. Jedná se o

> [!div class="checklist"]
> * Zálohování souborů a složek pomocí agenta Microsoft Azure Recovery Services (MARS) nebo agenta Azure Backup. 
> * Zálohování všech úloh a soubory s System Center Data Protection Manager (SC DPM) 
> * Zálohování všech úloh a souborů s využitím Microsoft Azure Backup serveru <br/>

## <a name="prerequisites"></a>Požadavky
Ujistěte se, že jsou splněny následující požadavky před inicializací pracovní postup Offline zálohování
* A [trezor služby Recovery Services](backup-azure-recovery-services-vault-overview.md) se vytvořil. Chcete-li jeden vytvořit, použijte postup v [v tomto článku](tutorial-backup-windows-server-to-azure.md#create-a-recovery-services-vault)
* Azure Backup agent nebo Azure Backup serveru nebo SC DPM nainstalovala buď klienta systému Windows Server nebo Windows, podle potřeby a počítač zaregistruje do trezoru služby Recovery Services. Zkontrolujte, že pouze [nejnovější verzi služby Azure Backup](https://go.microsoft.com/fwlink/?linkid=229525) se používá. 
* [Stáhněte si soubor nastavení publikování v Azure](https://portal.azure.com/#blade/Microsoft_Azure_ClassicResources/PublishingProfileBlade) na počítači, ze kterého plánujete zpět vaše data. Předplatné, ze kterého si stáhnout soubor nastavení publikování se může lišit od předplatné obsahující trezor služby Recovery Services. Pokud je vaše předplatné v suverénních Cloudech Azure, pomocí následujících odkazů podle potřeby a stáhněte soubor nastavení publikování v Azure.

    | Oblast suverénních cloudů | Odkaz na soubor nastavení publikování Azure |
    | --- | --- |
    | Spojené státy | [Odkaz](https://portal.azure.us#blade/Microsoft_Azure_ClassicResources/PublishingProfileBlade) |
    | Čína | [Odkaz](https://portal.azure.us#blade/Microsoft_Azure_ClassicResources/PublishingProfileBlade) |

* Účet služby Azure Storage s *classic* modelu nasazení se vytvořil v rámci předplatného, ze kterého jste stáhli soubor nastavení publikování jak je znázorněno níže: 

 ![Vytvoření účtu klasického úložiště.](./media/backup-azure-backup-import-export/storageaccountclassiccreate.png)

* Pracovní umístění, což může být sdílen v síti nebo libovolné další jednotky v počítači, interní nebo externí, dostatek místa na disku pro uložení počáteční kopie, je vytvořen. Například pokud se pokoušíte získat zpátky až 500 GB souborový server, ujistěte se, že pracovní oblasti je aspoň 500 GB. (Menší velikost se používá v důsledku komprese.)
* S ohledem na disky, které se odešlou do Azure Ujistěte se, pouze 2,5 SSD nebo 2,5 nebo 3, 5palcová SATA II/III interní pevné disky se používají. Můžete použít pevné disky až 10 TB. Zkontrolujte [dokumentace ke službě Azure Import/Export](../storage/common/storage-import-export-requirements.md#supported-hardware) nejnovější sady jednotek, které služba podporuje.
* Jednotky SATA musí být připojený k určitému počítači (uvedené jako *kopírování počítače*) odkud kopie dat záloh z *pracovního umístění* k SATA jednotky probíhá. Ujistěte se, že má zapnutí Bitlockeru *kopie počítače* 

## <a name="workflow"></a>Pracovní postup
Informace v této části vám pomůže dokončit pracovní postup offline zálohování tak, aby vaše data mohou doručit do datacentra Azure a nahrát do služby Azure Storage. Pokud máte dotazy týkající se importu služby či jiného aspektu procesu, přečtěte si [Přehled služby importu](../storage/common/storage-import-export-service.md) dokumentace odkazuje dříve.

### <a name="initiate-offline-backup"></a>Zahájení offline zálohování
1. Když naplánujete zálohování, zobrazí se následující obrazovka (ve Windows serveru, klienta Windows nebo System Center Data Protection Manager).

    ![Obrazovka importu](./media/backup-azure-backup-import-export/offlineBackupscreenInputs.png)

    Tady je odpovídající obrazovky v nástroji System Center Data Protection Manager: <br/>
    ![Obrazovka importu serveru SC DPM a Azure Backup](./media/backup-azure-backup-import-export/dpmoffline.png)

    Popis vstupy vypadá takto:

    * **Pracovní umístění**: Umístění dočasného úložiště, ke kterému je zapsán kopie prvotní zálohy. Pracovní umístění může být ve sdílené síťové složce nebo v místním počítači. Pokud počítač kopírování a zdrojový počítač se liší, doporučujeme, že zadáte úplná síťová cesta pracovního umístění.
    * **Název úlohy importu do Azure**: Jedinečný název, který Import úložiště Azure service a Azure Backup a sledovat přenosu dat odesílaných na discích do Azure.
    * **Nastavení publikování v Azure**: Zadejte místní cestu k souboru nastavení publikování.
    * **ID předplatného Azure**: ID předplatného Azure pro předplatné, ze které jste stáhli soubor nastavení publikování v Azure. 
    * **Azure Storage Account**: Název účtu úložiště v rámci předplatného Azure přidružené k souboru nastavení publikování v Azure.
    * **Azure Storage Container**: Název cílové objektů blob úložiště v účtu úložiště Azure, kterého se naimportuje data záloh.

     Uložit *pracovní umístění* a *název úlohy importu Azure* jste zadali, protože je nutné připravit disky.  
     
2. Dokončení pracovního postupu a k zahájení kopírování offline zálohování, klikněte na tlačítko **zálohovat nyní** v konzole pro správu Azure Backup agent. Prvotní zálohy se zapisují do pracovní oblasti v rámci tohoto kroku.

    ![Zálohovat nyní](./media/backup-azure-backup-import-export/backupnow.png)

    Dokončení odpovídající pracovní postup v System Center Data Protection Manager nebo Azure Backup server, klikněte pravým tlačítkem myši **skupiny ochrany**a klikněte na tlačítko **vytvořit bod obnovení** možnost. Potom můžete vybrat **Online ochrany** možnost.

    ![SC DPM a server Azure Backup zálohovat nyní](./media/backup-azure-backup-import-export/dpmbackupnow.png)

    Po dokončení operace pracovní umístění je připravená k použití pro přípravu disku.

    ![Postup zálohování](./media/backup-azure-backup-import-export/opbackupnow.png)

### <a name="prepare-sata-drives-and-ship-to-azure"></a>Jednotky SATA Připravíme a odešleme do Azure
*AzureOfflineBackupDiskPrep* nástroj slouží k přípravě disky SATA, které se odesílají do nejbližší datové centrum Azure. Tento nástroj je k dispozici v instalačním adresáři agenta služeb zotavení v následující cestě:

   *\Microsoft* *azure* *obnovení* *služby* * Agent\Utils\*

1. Přejděte do adresáře a zkopírujte **AzureOfflineBackupDiskPrep** adresáře pro kopírování počítače, na kterém jsou připojené disky SATA, abyste byli připraveni. Zajistěte následující s ohledem na počítači kopírování:

    * Kopírování počítač získá přístup k pracovní umístění pro pracovní postup offline předvyplnění pomocí stejné cesty sítě, která byla součástí **zahájení offline zálohování** pracovního postupu.
    * Povolení nástroje BitLocker v počítači kopírování.
    * Počítač kopírování získá přístup k webu Azure portal.

    V případě potřeby kopírování počítač může být stejný jako zdrojový počítač. 
    
    > [!IMPORTANT] 
    > Pokud zdrojový počítač je virtuální počítač, je nutné použít jiný fyzický server nebo klientský počítač jako počítač kopírování.
    
    
2. Otevřete příkazový řádek se zvýšenými oprávněními na počítači kopírování s *AzureOfflineBackupDiskPrep* nástroj adresář jako aktuálního adresáře a spusťte následující příkaz:

    `*.\AzureOfflineBackupDiskPrep.exe*   s:<*Staging Location Path*>   [p:<*Path to AzurePublishSettingsFile*>]`

    | Parametr | Popis |
    | --- | --- |
    | s:&lt;*pracovní umístění cesty*&gt; |Povinný vstup, který slouží k zadání cesty do pracovního umístění, které jste zadali v **zahájení offline zálohování** pracovního postupu. |
    | p:&lt;*cestu k PublishSettingsFile*&gt; |Volitelný vstup, který slouží k zadání cesty k **nastavením publikování v Azure** soubor, který jste zadali **zahájení offline zálohování** pracovního postupu. |

    > [!NOTE]
    > &lt;Cestu k AzurePublishSettingFile&gt; hodnota je povinná, pokud kopie počítače a zdrojový počítač se liší.
    >
    >

    Když příkaz spustíte, nástroj požaduje Výběr úlohy Import úložiště Azure, která odpovídá jednotky, které je potřeba se připravit. Pokud pouze jeden import úlohy souvisí s zadané pracovní umístění, objeví se obrazovka podobně jako ten, který následuje.

    ![Azure vstup nástroj Příprava disku](./media/backup-azure-backup-import-export/azureDiskPreparationToolDriveInput.png) <br/>
    
3. Zadejte písmeno jednotky bez koncové dvojtečka připojovaný disk, který chcete připravit pro přenos do Azure. Proveďte potvrzení pro formátování disku po zobrazení výzvy.

    Příprava na disku a zkopírování dat zálohování poté zahájí nástroj. Budete muset připojit další disky po zobrazení výzvy nástroj v případě, že zadaný disk nemá dostatek místa pro zálohovaná data. <br/>

    Na konci úspěšné spuštění nástroje pro jeden nebo víc disků, které jste zadali jsou připravené k odeslání do Azure. Kromě toho se název úlohy importu jste zadali během **zahájení offline zálohování** pracovního postupu je vytvořena v Azure. A konečně nástroj zobrazí dodací adresu do datacentra Azure, kde je třeba dodat disky.

    ![Příprava Azure disku dokončení](./media/backup-azure-backup-import-export/azureDiskPreparationToolSuccess.png)<br/>
    
4. Na konci spuštění příkazu zobrazí také možnost aktualizovat dodací informace, jak je znázorněno níže:

    ![Aktualizovat dodací informace o možnost](./media/backup-azure-backup-import-export/updateshippingutility.png)<br/>

5. Můžete zadat podrobné okamžitě. Nástroj provede vás procesem zahrnující řadu vstupů. Ale pokud nemáte informace, například sledování Číslo nebo dalších podrobností týkajících se přesouvání, můžete ukončit relaci. Kroky a aktualizujete podrobnosti o dopravě dále jsou uvedené v tomto článku. 

6. Pošlete disky na adresu, kterou poskytuje nástroje a Udržovat sledovací číslo pro budoucí použití.

   > [!IMPORTANT] 
   > Žádné dvě úlohy Azure Import může mít stejný počet sledování. Ujistěte se, že jednotky připravené nástrojem v rámci jedné úlohy Azure Import se dodávají společně v jednom balíčku a že je jeden jedinečný sledovací číslo pro balíček. Není možné sloučit jednotky připravené jako součást **různých** úlohy Azure Import v jediném balíčku. 

5. Pokud máte pro sledování Číslo informace, přejděte na zdrojový počítač, který čeká na dokončení úlohy Import úložiště a spuštěním následujícího příkazu v příkazovém řádku se zvýšenými oprávněními s *AzureOfflineBackupDiskPrep* adresáře nástroje, jako aktuální adresář: 

   `*.\AzureOfflineBackupDiskPrep.exe*  u:`

   Volitelně spustíte následující příkaz z jiného počítače, jako *kopie počítače*, s *AzureOfflineBackupDiskPrep* nástroj adresáři jako aktuální adresář:
   
   `*.\AzureOfflineBackupDiskPrep.exe*  u:  s:<*Staging Location Path*>   p:<*Path to AzurePublishSettingsFile*>`

    | Parametr | Popis |
    | --- | --- |
    | u: | Povinný vstup používá k aktualizaci přenosů podrobnosti pro úlohy importu Azure |
    | s:&lt;*pracovní umístění cesty*&gt; | Povinný vstup na zdrojovém počítači není spuštění příkazu. Umožňuje zadat cestu do pracovního umístění, které jste zadali v **zahájení offline zálohování** pracovního postupu. |
    | p:&lt;*cestu k PublishSettingsFile*&gt; | Povinný vstup na zdrojovém počítači není spuštění příkazu. Lze zadat cestu k **nastavením publikování v Azure** soubor, který jste zadali **zahájení offline zálohování** pracovního postupu. |
    
    Nástroj automaticky rozpozná úlohu importu, který zdrojový počítač čeká na nebo úlohy importu přidružené pracovní umístění při spuštění příkazu v jiném počítači. Pak poskytuje možnost aktualizovat dodací informace prostřednictvím řady vstupů, jak je znázorněno níže: 
    
    ![Zadat informace o dopravě](./media/backup-azure-backup-import-export/shippinginputs.png)<br/>

6. Jakmile se všechny vstupy jsou k dispozici, pečlivě zkontrolujte podrobnosti a potvrdit dodací informace, které jste zadali zadáním *Ano*. 

    ![Projděte si informace o dopravě](./media/backup-azure-backup-import-export/reviewshippinginformation.png)<br/>

7. Informace o expedici aktualizace úspěšně, poskytuje nástroj místního umístění, kde jsou uloženy podrobnosti o dopravě zadali sami, jak je znázorněno níže 

    ![Ukládat informace o dopravě](./media/backup-azure-backup-import-export/storingshippinginformation.png)<br/>

   > [!IMPORTANT] 
   > Zajistíte, že jednotky kontaktovat datového centra Azure během dvou týdnů od poskytuje dodací informace pomocí *AzureOfflineBackupDiskPrep* nástroj. Pokud tak neučiníte může způsobit jednotky nejsou zpracována.  

Po dokončení výše uvedených kroků, datové centrum Azure je připravena přijímat jednotky a pokračovat ve zpracování je k přenosu zálohovaných dat z jednotky pro účet úložiště Azure classic – typ, který jste vytvořili. 

### <a name="time-to-process-the-drives"></a>Čas ke zpracování jednotky 
Množství čas potřebný k procesu úlohy import úložiště Azure se liší v závislosti na různých faktorech, jako je například dobu dodání úlohy typ, typ a velikost dat, které jsou kopírovány a požadovanou velikost disků k dispozici. Služba Azure Import/Export nemá žádnou smlouvu SLA, ale po přijetí disků se služby snaží dokončete kopírování zálohovaných dat do účtu úložiště Azure ve dnech 7 až 10. Následující části podrobně popisuje, jak můžete monitorovat stav úlohy importu do Azure. 

### <a name="monitoring-azure-import-job-status"></a>Monitorování stavu úlohy importování Azure
Jednotky jsou na cestě nebo v datacentru Azure, které se mají zkopírovat do účtu úložiště, agenta Azure Backup nebo SC DPM nebo konzole Azure Backup serveru na zdrojovém počítači zobrazuje následující stav úlohy pro naplánované zálohování. 

  `Waiting for Azure Import Job to complete. Please check on Azure Management portal for more information on job status`

Postupujte podle kroků níže, a zkontrolujte stav úlohy Import úložiště. 
1. Otevřete příkazový řádek se zvýšenými oprávněními na zdrojovém počítači a spusťte následující příkaz:
    
     `AzureOfflineBackupDiskPrep.exe u:`
    
2.  Výstup ukazuje aktuální stav úlohy importu, jak je znázorněno níže: 

    ![Kontroluje se stav úlohy importu](./media/backup-azure-backup-import-export/importjobstatusreporting.png)<br/>

Další informace o různých státech úlohy importu do Azure najdete v tématu [v tomto článku](../storage/common/storage-import-export-view-drive-status.md)

### <a name="complete-the-workflow"></a>Dokončení pracovního postupu
Po dokončení úlohy import počáteční data záloh jsou k dispozici ve vašem účtu úložiště. Při příští plánované zálohování Azure backup zkopíruje obsah dat z účtu úložiště do trezoru služby Recovery Services jak je znázorněno níže: 

   ![Kopírování dat do trezoru služby Recovery Services](./media/backup-azure-backup-import-export/copyingfromstorageaccounttoazurebackup.png)<br/>

Při příští plánované zálohování Azure Backup provádí přírůstkové zálohy kopie prvotní zálohy.

## <a name="next-steps"></a>Další postup
* Máte nějaké otázky k Azure Import/Export pracovního postupu, najdete v tématu [přenášet data do úložiště objektů Blob pomocí služby Microsoft Azure Import/Export](../storage/common/storage-import-export-service.md).
* Zálohování offline oddílu z Azure Backup [nejčastější dotazy k](backup-azure-backup-faq.md) jakékoli dotazy týkající se pracovní postup.
