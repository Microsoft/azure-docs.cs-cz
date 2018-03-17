---
title: "Zálohování Azure – zálohování Offline nebo prvotní synchronizaci pomocí služby Azure Import/Export | Microsoft Docs"
description: "Zjistěte, jak Azure Backup umožňuje odesílat data mimo síť využívající službu Azure Import/Export. Tento článek vysvětluje, offline synchronizace replik indexů data prvotní zálohy pomocí služby Azure Import exportovat."
services: backup
documentationcenter: 
author: saurabhsensharma
manager: shivamg
editor: 
ms.assetid: ada19c12-3e60-457b-8a6e-cf21b9553b97
ms.service: backup
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 2/6/2018
ms.author: saurse;nkolli;trinadhk
ms.openlocfilehash: 7af2623a25f73f6d9062d476309ecd53da542f70
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/17/2018
---
# <a name="offline-backup-workflow-in-azure-backup"></a>Pracovní postup offline zálohování ve službě Azure Backup
Zálohování Azure má několik předdefinovaných efektivitu, která ukládají náklady na síť a úložiště během počáteční úplné zálohy dat do Azure. Počáteční úplné zálohování obvykle přenos velkých objemů dat a vyžaduje větší šířku pásma sítě ve srovnání s následné zálohy, které přenášejí pouze rozdílů/přírůstková. Zálohování Azure komprimaci prvotní zálohy. Proces offline synchronizace replik indexů Azure Backup můžete použít disky k nahrání komprimovaná data prvotní zálohy do offline režimu Azure.  

Proces offline synchronizace replik indexů Azure Backup je úzce integrovaná s [služba Azure Import/Export](../storage/common/storage-import-export-service.md) , umožňuje přenos dat do Azure pomocí disků. Pokud máte terabajtů (TBs) počáteční zálohovací data, která je potřeba přenést přes síť s vysokou latencí a malou šířkou pásma, můžete k dodání kopie prvotní zálohy na jeden nebo více pevných disků na datovém centru Azure pracovní postup offline synchronizace replik indexů. Tento článek obsahuje přehled a další podrobnosti o kroky, které dokončit tento pracovní postup.


## <a name="overview"></a>Přehled
Pomocí funkce offline synchronizace replik indexů Azure Backup a Azure Import/Export je jednoduše nahrát data do offline režimu do Azure pomocí disků. Proces Offline zálohování zahrnuje následující kroky:

> [!div class="checklist"]
> * Zálohování data, místo odeslání přes síť, se zapisují do *pracovního umístění*
> * Data na *pracovního umístění* je pak zapsán do jednoho nebo více disků SATA pomocí *AzureOfflineBackupDiskPrep* nástroj
> * Nástroj automaticky vytvoří úlohu Import úložiště Azure
> * Jednotky SATA jsou pak se odešle na nejbližší datové centrum Azure
> * Po dokončení nahrávání dat zálohování do Azure, Azure Backup zkopíruje data záloh do trezoru záloh a přírůstkové zálohování.

## <a name="supported-configurations"></a>Podporované konfigurace 
Zálohování offline je podporována pro všechny modely nasazení služby Azure Backup, mimo pracoviště zálohovaná data z místně ke cloudu Microsoftu. To zahrnuje

> [!div class="checklist"]
> * Zálohování souborů a složek s agenta nástroje Microsoft Azure Recovery Services (MARS) nebo agenta Azure Backup. 
> * Zálohování všech úloh a souborů pomocí System Center Data Protection Manager (SC DPM) 
> * Zálohování všech úloh a souborů pomocí serveru služby Microsoft Azure Backup <br/>

   > [!NOTE]
   > Zálohování offline není podporováno pro zálohování stavu systému se provádí pomocí agenta Azure Backup. 

[!INCLUDE [backup-upgrade-mars-agent.md](../../includes/backup-upgrade-mars-agent.md)]

## <a name="prerequisites"></a>Požadavky
Ujistěte se, že jsou splněny následující požadavky před zahájením pracovní postup Offline zálohování
* A [trezor služeb zotavení](backup-azure-recovery-services-vault-overview.md) byla vytvořena. Vytvořit, naleznete postup v [v tomto článku](tutorial-backup-windows-server-to-azure.md#create-a-recovery-services-vault)
* Azure Backup agent nebo serveru Azure Backup nebo SC DPM byla nainstalována na buď klienta systému Windows Server a Windows, podle vhodnosti a počítač je zaregistrován k trezoru služeb zotavení. Ujistěte se, že pouze [nejnovější verzi služby Azure Backup](https://go.microsoft.com/fwlink/?linkid=229525) se používá. 
* [Stáhněte si soubor nastavení publikování Azure](https://portal.azure.com/#blade/Microsoft_Azure_ClassicResources/PublishingProfileBlade) na počítači, ze kterého plánujete zálohování dat. Předplatné, ze kterého si stáhnout soubor nastavení publikování se může lišit od odběr, který obsahuje trezoru služeb zotavení. Pokud vaše předplatné je na suverénní Azure Cloudy, pomocí následujících odkazů v závislosti na stažení souboru nastavení publikování Azure.

    | Oblast svrchovaných cloudu | Azure odkaz na soubor nastavení publikování |
    | --- | --- |
    | Spojené státy | [Link](https://portal.azure.us#blade/Microsoft_Azure_ClassicResources/PublishingProfileBlade) |
    | Čína | [Link](https://portal.azure.us#blade/Microsoft_Azure_ClassicResources/PublishingProfileBlade) |

* Účet úložiště Azure s *classic* modelu nasazení byla vytvořena v rámci předplatného, ze kterého jste stáhli soubor nastavení publikování jak je uvedeno níže: 

 ![Vytvoření účtu úložiště classic](./media/backup-azure-backup-import-export/storageaccountclassiccreate.png)

* Pracovní umístění, což může být sdílené síťové složky nebo všechny další jednotku na počítači, interní nebo externí, s dostatek místa na disku pro uložení vaší počáteční kopie se vytvoří. Například pokud chcete zálohovat 500 GB souborový server, zajistěte, aby byl v pracovní oblasti alespoň 500 GB. (Menší velikost se používá v důsledku komprese.)
* S ohledem na disky, které se budou odesílat do Azure Ujistěte se, které pouze 2,5 palec SSD nebo 2,5 nebo 3,5 SATA II/III interní pevné disky se používají. Pevné disky můžete použít až do 10 TB. Zkontrolujte [dokumentace ke službě Azure Import/Export](../storage/common/storage-import-export-service.md#hard-disk-drives) pro nejnovější sadu disků, které služba podporuje.
* Jednotky SATA mají být připojené k počítači (říká *kopie počítače*) odkud kopii zálohovaná data ze *pracovního umístění* k SATA jednotek se provádí. Ujistěte se, že nástroj Bitlocker je povolena v *kopie počítače* 

## <a name="workflow"></a>Pracovní postup
Informace v této části vám pomůže dokončit pracovní postup offline zálohování tak, aby vaše data mohou doručit do datového centra Azure a nahrané do úložiště Azure. Pokud máte dotazy týkající se importu služby či jiného aspektu procesu, najdete v článku [Přehled služby importu](../storage/common/storage-import-export-service.md) dokumentace odkazuje dříve.

### <a name="initiate-offline-backup"></a>Spustit zálohování offline
1. Pokud naplánujete zálohování, uvidíte následující obrazovku (v systému Windows Server, klient systému Windows nebo System Center Data Protection Manager).

    ![Import obrazovky](./media/backup-azure-backup-import-export/offlineBackupscreenInputs.png)

    Tady je odpovídající obrazovky v System Center Data Protection Manager: <br/>
    ![Aplikace DPM SC a server Azure Backup importovat obrazovky](./media/backup-azure-backup-import-export/dpmoffline.png)

    Popis vstupních hodnot je následující:

    * **Pracovní umístění**: dočasné úložiště umístění, do které se zapíše kopie prvotní zálohy. Pracovní umístění může být na sdílené síťové složky nebo místního počítače. Pokud počítač kopie a zdrojového počítače liší, doporučujeme, že zadáváte cestu plné síti pracovní umístění.
    * **Azure název úlohy importu**: jedinečný název, který Import úložiště Azure service a Azure Backup sledovat přenos dat odesílaných na discích do Azure.
    * **Nastavení Azure publikování**: Zadejte místní cestu k souboru nastavení publikování.
    * **ID předplatného Azure**: ID předplatného Azure pro předplatné, ze které jste stáhli soubor nastavení publikování Azure. 
    * **Účet služby Azure Storage**: název účtu úložiště v rámci předplatného Azure přidružené k souboru nastavení publikování Azure.
    * **Kontejner úložiště Azure**: název cílový objekt blob úložiště v účtu úložiště Azure, kde se záložní data importovat.

     Uložit *pracovního umístění* a *název úlohy importu Azure* jste zadali, jako je třeba, aby připravit disky.  
     
2. Dokončení pracovního postupu a pokud chcete zahájit kopírování zálohování offline, klikněte na tlačítko **zálohovat nyní** v konzole pro správu Azure Backup agent. Prvotní zálohování je zapsána do pracovní oblasti v rámci tohoto kroku.

    ![Zálohovat nyní](./media/backup-azure-backup-import-export/backupnow.png)

    K dokončení odpovídající pracovní postup v System Center Data Protection Manager nebo Azure Backup server, klikněte pravým tlačítkem myši **skupiny ochrany**a potom zvolte **vytvořit bod obnovení** možnost. Potom vyberte **Online ochrany** možnost.

    ![Aplikace DPM SC a server Azure Backup zálohovat nyní](./media/backup-azure-backup-import-export/dpmbackupnow.png)

    Po dokončení operace je připravená k použití pro přípravu disku pracovní umístění.

    ![Postup zálohování](./media/backup-azure-backup-import-export/opbackupnow.png)

### <a name="prepare-sata-drives-and-ship-to-azure"></a>Příprava jednotky SATA a dodávat do Azure
*AzureOfflineBackupDiskPrep* nástroj slouží k přípravě jednotky SATA, které se odesílají do nejbližší datového centra Azure. Tento nástroj je k dispozici v instalační adresář agenta služeb zotavení v následující cestě:

   *\Microsoft* *Azure* *Recovery* *Services* *Agent\Utils\*

1. Přejděte do adresáře a kopírování **AzureOfflineBackupDiskPrep** adresář pro kopírování počítače, na kterém jsou připojené jednotky SATA, abyste byli připraveni. Zajistěte následující s ohledem na počítači kopie:

    * Kopírování počítače mají přístup ke pracovní umístění pro pracovní postup offline synchronizace replik indexů pomocí stejné síťové cestě, která byla součástí **zahájit zálohování offline** pracovního postupu.
    * Nástroj BitLocker je povolen v počítači kopie.
    * Kopírování počítače získat přístup k portálu Azure.

    V případě potřeby kopie počítač může být stejný jako zdrojový počítač. 
    
    > [!IMPORTANT] 
    > Pokud zdrojový počítač je virtuální počítač, je nutné použít na jiný fyzický server nebo klientský počítač jako počítač kopírování.
    
    
2. Otevřete příkazový řádek se zvýšenými oprávněními v počítači kopie s *AzureOfflineBackupDiskPrep* nástroj adresáři jako aktuálního adresáře a spusťte následující příkaz:

    `*.\AzureOfflineBackupDiskPrep.exe*   s:<*Staging Location Path*>   [p:<*Path to AzurePublishSettingsFile*>]`

    | Parametr | Popis |
    | --- | --- |
    | s:&lt;*cesta pracovní umístění*&gt; |Povinné vstup, který slouží k zadání cesty na pracovní umístění, které jste zadali v **zahájit zálohování offline** pracovního postupu. |
    | p:&lt;*cestu k PublishSettingsFile*&gt; |Volitelné vstup, který slouží k zadat cestu k **nastavením publikování v Azure** soubor, který jste zadali v **zahájit zálohování offline** pracovního postupu. |

    > [!NOTE]
    > &lt;Cestu k AzurePublishSettingFile&gt; hodnota je povinný, když počítač kopie a zdrojový počítač se liší.
    >
    >

    Při spuštění příkazu požadavky nástroj Výběr úlohy importu v Azure, která odpovídá jednotky, které je nutné připravit. Pokud jenom jeden import úlohy je přidružen zadané pracovní umístění, zobrazí obrazovky jako ten, který následuje.

    ![Azure vstup nástroj Příprava disku](./media/backup-azure-backup-import-export/azureDiskPreparationToolDriveInput.png) <br/>
    
3. Zadejte písmeno jednotky bez koncové dvojtečkou připojeného disku, který chcete připravit pro přenos do Azure. Potvrzení pro formátování disku po zobrazení výzvy zadejte.

    Nástroj pro přípravu na disku a zkopírování dat zálohování poté zahájí. Budete muset připojit další disky, a po zobrazení výzvy nástroj v případě, že zadaný disk nemá dostatek místa pro zálohovaná data. <br/>

    Na konci úspěšné provedení nástroj jeden nebo více disků, které jste zadali připravené pro přesouvání do Azure. Kromě toho úlohy importu s názvem jste zadali při **zahájit zálohování offline** pracovní postup je vytvořen v Azure. Nakonec nástroj zobrazí adresy příjemce na datovém centru Azure, kde je třeba dodat disky.

    ![Příprava Azure disku dokončení](./media/backup-azure-backup-import-export/azureDiskPreparationToolSuccess.png)<br/>
    
4. Na konci provedení příkazu zobrazí také možnost aktualizovat informace o přesouvání, jak je uvedeno níže:

    ![Aktualizovat informace o přesouvání – možnost](./media/backup-azure-backup-import-export/updateshippingutility.png)<br/>

5. Můžete zadat podrobnosti hned. Nástroj vás provede procesem zahrnující řady vstupy. Ale pokud nemáte informace, jako jsou sledování Číslo nebo další podrobnosti týkající se přesouvání, můžete ukončit relaci. Postup přesouvání podrobné informace o aktualizaci později jsou uvedeny v tomto článku. 

6. Dodávat disky na adresu, které poskytuje nástroje a zachovejte sledování pro budoucí použití.

   > [!IMPORTANT] 
   > Žádné dvě úlohy importu v Azure můžete mít stejný počet sledování. Ujistěte se, že jednotky připravený pomocí nástroje v rámci jedné úloze importu v Azure jsou sice společně v jednom balíčku a zda je číslo jeden jedinečný sledování pro balíček. Nebudete kombinovat jednotky připravené jako součást **různých** Azure úlohy importu v jeden balíček. 

5. Pokud máte sledování Číslo informace, přejděte na zdrojový počítač, který se čeká na dokončení úlohy importu a spusťte následující příkaz v příkazovém řádku se zvýšenými s *AzureOfflineBackupDiskPrep* nástroj adresáři jako aktuální adresář: 

   `*.\AzureOfflineBackupDiskPrep.exe*  u:`

   Můžete případně spustit následující příkaz z jiného počítače, jako *kopie počítače*, s *AzureOfflineBackupDiskPrep* nástroj adresáři jako aktuální adresář:
   
   `*.\AzureOfflineBackupDiskPrep.exe*  u:  s:<*Staging Location Path*>   p:<*Path to AzurePublishSettingsFile*>`

    | Parametr | Popis |
    | --- | --- |
    | U: | Povinné vstupní použít k aktualizaci přenosů podrobnosti úlohy importu v Azure |
    | s:&lt;*cesta pracovní umístění*&gt; | Povinné vstupní při příkaz není spuštěna na zdrojovém počítači. Umožňuje zadat cestu k pracovní umístění, které jste zadali v **zahájit zálohování offline** pracovního postupu. |
    | p:&lt;*cestu k PublishSettingsFile*&gt; | Povinné vstupní při příkaz není spuštěna na zdrojovém počítači. Umožňuje zadat cestu k **nastavením publikování v Azure** soubor, který jste zadali v **zahájit zálohování offline** pracovního postupu. |
    
    Nástroj automaticky rozpozná Import úloha, která zdrojový počítač čeká na nebo úlohy importu přidružené pracovní umístění při spuštění příkazu v jiném počítači. Pak poskytuje možnost aktualizace přenosů informace prostřednictvím řady vstupy, jak je uvedeno níže: 
    
    ![Zadat informace o přesouvání](./media/backup-azure-backup-import-export/shippinginputs.png)<br/>

6. Jakmile jsou uvedeny všechny vstupy, pečlivě zkontrolujte podrobnosti a potvrdit přesouvání informace, které jste zadali zadáním *Ano*. 

    ![Přečtěte si informace o přesouvání](./media/backup-azure-backup-import-export/reviewshippinginformation.png)<br/>

7. Informace o přesouvání aktualizace úspěšně, poskytuje nástroj místního umístění, kde jsou uloženy podrobnosti o přesouvání, které jste zadali, jak je uvedeno níže 

    ![Ukládání přesouvání informace](./media/backup-azure-backup-import-export/storingshippinginformation.png)<br/>

   > [!IMPORTANT] 
   > Zajistit, aby jednotky přístup datového centra Azure během dvou týdnů od poskytování informací přesouvání pomocí *AzureOfflineBackupDiskPrep* nástroj. Uděláte to tak může dojít jednotky nejsou zpracována.  

Po dokončení kroků výše je připraven přijmout jednotky a pokračovat ve zpracování je k přenosu dat záloh z jednotky k účtu úložiště Azure classic-type, kterou jste vytvořili datového centra Azure. 

### <a name="time-to-process-the-drives"></a>Doba zpracování jednotky 
Množství času úlohu importu do Azure se liší v závislosti na různých faktorech, například přesouvání čas zpracování úlohy typu, typ a velikost dat kopírovány a velikosti disků zadat. Služba Azure Import/Export nemá SLA, ale po disky jsou přijaty službu snaží dokončení kopírování zálohování dat do účtu úložiště Azure za 7 až 10 dní. V další části Podrobné informace o tom, jak můžete monitorovat stav úlohy importu do Azure. 

### <a name="monitoring-azure-import-job-status"></a>Stav monitorování úloze importu v Azure
Jednotky jsou při přenosu nebo v datovém centru Azure, který se má zkopírovat na účet úložiště, agent Azure Backup nebo SC DPM nebo konzole Azure Backup server na zdrojovém počítači znázorňuje následující stav úlohy pro naplánované zálohování. 

  `Waiting for Azure Import Job to complete. Please check on Azure Management portal for more information on job status`

Postupujte podle kroků dole, a zkontrolujte stav úlohy importu. 
1. Otevřete příkazový řádek se zvýšenými oprávněními na zdrojovém počítači a spusťte následující příkaz:
    
     `AzureOfflineBackupDiskPrep.exe u:`
    
2.  Výstup ukazuje aktuální stav úlohy importu, jak je uvedeno níže: 

    ![Kontrola stavu úlohy importu](./media/backup-azure-backup-import-export/importjobstatusreporting.png)<br/>

Další informace o různé stavy úlohy importu do Azure najdete v tématu [v tomto článku](../storage/common/storage-import-export-service.md#how-does-the-azure-importexport-service-work)

### <a name="complete-the-workflow"></a>Dokončení pracovního postupu
Po dokončení úlohy importu dat prvotní zálohy je k dispozici ve vašem účtu úložiště. Při příští naplánované zálohování zálohování Azure zkopíruje obsah dat z účtu úložiště do trezoru služeb zotavení jak je uvedeno níže: 

   ![Kopírování dat do trezoru služeb zotavení](./media/backup-azure-backup-import-export/copyingfromstorageaccounttoazurebackup.png)<br/>

Při příští naplánované zálohování zálohování Azure provádí přírůstkové zálohování přes kopie prvotní zálohy.

## <a name="next-steps"></a>Další postup
* Všechny dotazy týkající se Azure Import/Export pracovního postupu najdete v části [používat službu Microsoft Azure Import/Export k přenosu dat do úložiště objektů Blob](../storage/common/storage-import-export-service.md).
* Informace naleznete v sekci zálohování offline ze služby Azure Backup [– nejčastější dotazy](backup-azure-backup-faq.md) pro všechny dotazy týkající se pracovní postup.
