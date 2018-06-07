---
title: Zálohování Azure – zálohování Offline nebo prvotní synchronizaci pomocí služby Azure Import/Export
description: Zjistěte, jak Azure Backup umožňuje odesílat data mimo síť využívající službu Azure Import/Export. Tento článek vysvětluje, offline synchronizace replik indexů data prvotní zálohy pomocí služby Azure Import exportovat.
services: backup
author: saurabhsensharma
manager: shivamg
ms.service: backup
ms.topic: conceptual
ms.date: 05/17/2018
ms.author: saurse
ms.openlocfilehash: cb37c94d3ec819823083041708cfc28ead0ed5cf
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34606133"
---
# <a name="offline-backup-workflow-in-azure-backup"></a>Pracovní postup offline zálohování ve službě Azure Backup
Zálohování Azure má několik předdefinovaných efektivitu, která ukládají náklady na síť a úložiště během počáteční úplné zálohy dat do Azure. Počáteční úplné zálohování obvykle přenos velkých objemů dat a vyžaduje větší šířku pásma sítě ve srovnání s následné zálohy, které přenášejí pouze rozdílů/přírůstková. Proces offline synchronizace replik indexů Azure Backup můžete použít disky k nahrání dat offline zálohování do Azure.

Služby Azure Backup offline synchronizace replik indexů proces je úzce integrovaná s [služba Azure Import/Export](../storage/common/storage-import-export-service.md) , umožňuje přenos dat prvotní zálohy do Azure pomocí disků. Pokud máte terabajtů (TBs) počáteční zálohovací data, která je potřeba přenést přes síť s vysokou latencí a malou šířkou pásma, můžete k dodání kopie prvotní zálohy na jeden nebo více pevných disků, na datovém centru Azure pracovní postup offline synchronizace replik indexů. Následující obrázek poskytuje přehled kroků v pracovním postupu.

  ![Přehled procesu offline importu pracovního postupu](./media/backup-azure-backup-import-export/offlinebackupworkflowoverview.png)

Proces zálohování offline zahrnuje následující kroky:

1. Místo odesílání zálohovaných dat přes síť, zapisovat data záloh do pracovní umístění.
2. Pomocí nástroje AzureOfflineBackupDiskPrep zapsat data v pracovní umístění na jeden nebo více disků SATA.
3. Jako součást přípravné práce vytvoří nástroj AzureOfflineBackupDiskPrep úlohu Import úložiště Azure. Jednotky SATA poslat nejbližší datové centrum Azure a odkazovat na připojení aktivity úlohy importu.
4. V datovém centru Azure se zkopíruje data na discích k účtu úložiště Azure.
5. Zálohování Azure zkopíruje zálohovaná data z účtu úložiště do trezoru služeb zotavení a přírůstkové zálohování.

## <a name="supported-configurations"></a>Podporované konfigurace 
Následující funkce Azure Backup nebo úlohy podporovat použití Offline zálohování.

> [!div class="checklist"]
> * Zálohování souborů a složek s agenta nástroje Microsoft Azure Recovery Services (MARS), také označuje jako agenta Azure Backup. 
> * Zálohování všech úloh a souborů pomocí System Center Data Protection Manager (SC DPM) 
> * Zálohování všech úloh a souborů pomocí serveru služby Microsoft Azure Backup <br/>

   > [!NOTE]
   > Zálohování offline není podporováno pro zálohování stavu systému se provádí pomocí agenta Azure Backup. 

[!INCLUDE [backup-upgrade-mars-agent.md](../../includes/backup-upgrade-mars-agent.md)]

## <a name="prerequisites"></a>Požadavky

  > [!NOTE]
  > Následující požadavky a pracovní postup se vztahují pouze na zálohování Offline souborů a složek pomocí [nejnovější verze agenta MARS](https://aka.ms/azurebackup_agent). Chcete-li provést zálohování Offline, pro zatížení pomocí System Center DPM nebo serveru Azure Backup, přečtěte [v tomto článku](backup-azure-backup-server-import-export-.md). 

Před zahájením pracovní postup Offline zálohování, splnit následující požadavky: 
* Vytvoření [trezor služeb zotavení](backup-azure-recovery-services-vault-overview.md). Pro vytvoření trezoru, naleznete postup v [v tomto článku](tutorial-backup-windows-server-to-azure.md#create-a-recovery-services-vault)
* Ujistěte se, že pouze [nejnovější verzi agenta Azure Backup](https://aka.ms/azurebackup_agent) byla nainstalována do klienta systému Windows Server a Windows, podle vhodnosti a počítač je zaregistrován k trezoru služeb zotavení.
* Prostředí Azure PowerShell 3.7.0 nebo vyšší se vyžaduje na počítači se systémem agenta Azure Backup. Doporučujeme vám [nainstalujte nejnovější verzi prostředí Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-azurerm-ps?view=azurermps-5.7.0).
* Na počítače se systémem agenta Azure Backup Přesvědčte se, zda je nainstalovaný Microsoft Edge nebo Internet Explorer 11 a je povolen jazyk JavaScript. 
* Vytvoření účtu úložiště Azure ve stejném předplatném jako trezor služeb zotavení. 
* Zajistěte, aby byla [potřebná oprávnění](../azure-resource-manager/resource-group-create-service-principal-portal.md) k vytvoření aplikace Azure Active Directory. Pracovní postup Offline zálohování vytvoří aplikaci služby Azure Active Directory v rámci předplatného přidružené k účtu úložiště Azure. Cílem aplikace je poskytnout Azure Backup zabezpečení a oboru přístupu ke službě Azure Import, vyžaduje se pro pracovní postup Offline zálohování. 
* Zaregistrujte zprostředkovatele prostředků Microsoft.ImportExport s předplatným obsahující účtu úložiště Azure. Registrace poskytovatele prostředků:
    1. V hlavní nabídce klikněte na tlačítko **odběry**.
    2. Pokud odebíráte více předplatných, vyberte odběr, který používáte pro zálohování offline. Můžete použít jenom jedno předplatné, zobrazí se vaše předplatné.
    3. V nabídce předplatné, klikněte na tlačítko **zprostředkovatelé prostředků** zobrazíte seznam zprostředkovatelů.
    4. V seznamu zprostředkovatelů přejděte dolů Microsoft.ImportExport. Pokud je ve stavu NotRegistered, klikněte na tlačítko **zaregistrovat**.
    ![registrace poskytovatele prostředků](./media/backup-azure-backup-import-export/registerimportexport.png)
* Pracovní umístění, což může být sdílené síťové složky nebo všechny další jednotku na počítači, interní nebo externí, s dostatek místa na disku pro uložení vaší počáteční kopie se vytvoří. Například pokud chcete zálohovat 500 GB souborový server, zajistěte, aby byl v pracovní oblasti alespoň 500 GB. (Menší velikost se používá v důsledku komprese.)
* Při odesílání disky do Azure, použijte pouze 2,5 SSD nebo 2,5 nebo 3,5 SATA II/III interní pevné disky. Pevné disky můžete použít až do 10 TB. Zkontrolujte [dokumentace ke službě Azure Import/Export](../storage/common/storage-import-export-requirements.md#supported-hardware) pro nejnovější sadu disků, které služba podporuje.
* Jednotky SATA musí být připojen k počítači (říká *kopie počítače*) odkud kopii zálohovaná data ze *pracovního umístění* k SATA jednotek se provádí. Ujistěte se, že nástroj Bitlocker je povolena v *kopie počítače*.

## <a name="workflow"></a>Pracovní postup
Tato část popisuje pracovní postup offline zálohování tak, aby vaše data mohou doručit do datového centra Azure a nahrané do úložiště Azure. Pokud máte dotazy týkající se importu služby či jiného aspektu procesu, najdete v článku [importovat přehled dokumentace služby](../storage/common/storage-import-export-service.md).

## <a name="initiate-offline-backup"></a>Spustit zálohování offline
1. Při plánování zálohování na agenta MARS uvidíte následující obrazovku.

    ![Import obrazovky](./media/backup-azure-backup-import-export/offlinebackup_inputs.png)

  Popis vstupních hodnot je následující:

    * **Pracovní umístění**: dočasné úložiště umístění, do které se zapíše kopie prvotní zálohy. Pracovní umístění může být na sdílené síťové složky nebo místního počítače. Pokud počítač kopie a zdrojového počítače liší, doporučujeme, že zadáváte cestu plné síti pracovní umístění.
    * **Účet úložiště Azure Resource Manager**: název účtu úložiště typu Resource Manager v jakéhokoli předplatného Azure.
    * **Kontejner úložiště Azure**: název cílový objekt blob úložiště v účtu Azure Storage, kde je zálohovaná data importována před kopírovány do trezoru služeb zotavení.
    * **ID předplatného Azure**: ID předplatného Azure, kde se má vytvořit účet úložiště Azure.
    * **Azure název úlohy importu**: jedinečný název, který Import úložiště Azure service a Azure Backup sledovat přenos dat odesílaných na discích do Azure. 
  
  Zadejte vstupy na obrazovce a klepněte na tlačítko **Další**. Uložit poskytnutého *pracovní umístění* a *název úlohy importu Azure*, jak tyto informace jsou nezbytné pro přípravu disky.

2. Po zobrazení výzvy se přihlaste do vašeho předplatného Azure. Musíte se přihlásit, aby zálohování Azure můžete vytvořit aplikaci Azure Active Directory a poskytují nezbytné oprávnění k přístupu ke službě Azure Import.

    ![Zálohovat nyní](./media/backup-azure-backup-import-export/azurelogin.png)

3. Dokončení pracovního postupu a v konzole Azure Backup agent klikněte na **zálohovat nyní**.

    ![Zálohovat nyní](./media/backup-azure-backup-import-export/backupnow.png)

4. Na potvrzovací stránce průvodce, klikněte na tlačítko **zálohování**. Prvotní zálohování je zapsána do pracovní oblasti jako součást instalace.

   ![Potvrďte, že jste nyní připraveni zálohování](./media/backup-azure-backup-import-export/backupnow-confirmation.png)

    Po dokončení operace je připravená k použití pro přípravu disku pracovní umístění.

   ![Zálohovat nyní](./media/backup-azure-backup-import-export/opbackupnow.png)

## <a name="prepare-sata-drives-and-ship-to-azure"></a>Příprava jednotky SATA a dodávat do Azure
*AzureOfflineBackupDiskPrep* nástroj připraví disky SATA, které byly odeslány na nejbližší datové centrum Azure. Tento nástroj je k dispozici v instalační adresář agenta Azure Backup (v následující cestě):

   *\Microsoft azure Recovery Services Agent\Utils\\*

1. Přejděte do adresáře a kopírování **AzureOfflineBackupDiskPrep** adresáře do jiného počítače, kde jsou připojené jednotky SATA. Na počítači s připojené jednotky SATA zkontrolujte:

    * Kopírování počítače mají přístup ke pracovní umístění pro pracovní postup offline synchronizace replik indexů pomocí stejné síťové cestě, která byla součástí **zahájit zálohování offline** pracovního postupu.
    * Nástroj BitLocker je povolen v počítači kopie.
    * Prostředí Azure PowerShell 3.7.0, nebo novější, je nainstalována.
    * Jsou nainstalované s nejnovějšími prohlížeči kompatibilní (hraniční nebo Internet Explorer 11) a je povolen jazyk JavaScript. 
    * Kopírování počítače získat přístup k portálu Azure. V případě potřeby kopie počítač může být stejný jako zdrojový počítač.
    
    > [!IMPORTANT] 
    > Pokud zdrojový počítač je virtuální počítač, počítač kopie musí být na jiný fyzický server nebo klientský počítač ze zdrojového počítače.

2. Otevřete příkazový řádek se zvýšenými oprávněními v počítači kopie s *AzureOfflineBackupDiskPrep* nástroj adresáři jako aktuálního adresáře a spusťte následující příkaz:

    ```.\AzureOfflineBackupDiskPrep.exe s:<Staging Location Path>```

    | Parametr | Popis |
    | --- | --- |
    | s:&lt;*cesta pracovní umístění*&gt; |Umožňuje zadat cestu k pracovní umístění, které jste zadali v povinný vstupní **zahájit zálohování offline** pracovního postupu. |
    | p:&lt;*cestu k PublishSettingsFile*&gt; |Volitelné vstup, který slouží k zadat cestu k **nastavením publikování v Azure** soubor, který jste zadali v **zahájit zálohování offline** pracovního postupu. |

    Při spuštění příkazu požadavky nástroj Výběr úlohy importu v Azure, která odpovídá jednotky, které je nutné připravit. Pokud jenom jeden import úlohy je přidružen zadané pracovní umístění, zobrazí obrazovky jako ten, který následuje.

    ![Azure vstup nástroj Příprava disku](./media/backup-azure-backup-import-export/diskprepconsole0_1.png) <br/>

3. Zadejte písmeno jednotky bez koncové dvojtečkou připojeného disku, který chcete připravit pro přenos do Azure. 
4. Potvrzení pro formátování disku po zobrazení výzvy zadejte.
5. Zobrazí se výzva k přihlášení do vašeho předplatného Azure. Zadejte svoje přihlašovací údaje.

    ![Azure vstup nástroj Příprava disku](./media/backup-azure-backup-import-export/signindiskprep.png) <br/>

    Nástroj pro přípravu na disku a zkopírování dat zálohování poté zahájí. Budete muset připojit další disky, a po zobrazení výzvy nástroj v případě, že zadaný disk nemá dostatek místa pro zálohovaná data. <br/>

    Na konci úspěšné provedení nástroj příkazového řádku poskytuje tři údaje:
    1. Jeden nebo více disků, které jste zadali připravené pro přesouvání do Azure. 
    2. Zobrazí potvrzení vytvořený vaše úlohy importu. Úloha importu používá název, který jste zadali.
    3. Nástroj zobrazí adresy příjemce pro datové centrum Azure.

    ![Příprava Azure disku dokončení](./media/backup-azure-backup-import-export/console2.png)<br/>

6. Na konci provedení příkazu můžete aktualizovat informace o přesouvání.

7. Dodávat disky na adresu, které poskytuje nástroje a zachovejte sledování pro budoucí použití.

   > [!IMPORTANT] 
   > Žádné dvě úlohy importu v Azure můžete mít stejný počet sledování. Ujistěte se, že jednotky připravený pomocí nástroje v rámci jedné úlohy importu v Azure jsou sice společně v jednom balíčku a zda je číslo jeden jedinečný sledování pro balíček. Nebudete kombinovat jednotky připravený v rámci samostatné úlohy Import úložiště Azure v jeden balíček.



## <a name="update-shipping-details-on-the-azure-import-job"></a>Aktualizovat podrobnosti přenosů na úlohy importu v Azure

Následující postup aktualizuje přenosů podrobnosti úlohy Import úložiště Azure. Tyto informace obsahuje podrobnosti o:
* Název operátora, který poskytuje disky do Azure
* Vrátí přenosů podrobnosti pro disky

1. Přihlaste se k předplatnému Azure.
2. V hlavní nabídce klikněte na tlačítko **všechny služby** a v dialogovém okně všechny služby, typ importu. Až se zobrazí **úlohy importu a exportu**, klikněte na něj.
    ![Zadat informace o přesouvání](./media/backup-azure-backup-import-export/search-import-job.png)<br/>

    Seznam **úlohy importu a exportu** otevře se nabídka a zobrazí se seznam všech úloh importu a exportu ve vybraném předplatném. 

3. Pokud máte více předplatných, je nutné vybrat předplatné používá pro import zálohovaná data. Pak vyberte nově vytvořenou úlohy importu otevřete její podrobnosti.

    ![Přečtěte si informace o přesouvání](./media/backup-azure-backup-import-export/import-job-found.png)<br/>

4. V nabídce nastavení úlohy importu, klikněte na tlačítko **spravovat přenosů informace** a zadejte podrobnosti návratový přesouvání.

    ![Ukládání přesouvání informace](./media/backup-azure-backup-import-export/shipping-info.png)<br/>

5. Když máte toto číslo od přenosů operátora, klikněte na informační zpráva na stránce Přehled úlohy Import úložiště Azure a zadejte následující podrobnosti:

   > [!IMPORTANT] 
   > Zajistěte, aby se během dvou týdnů od vytvoření úlohy importu do Azure aktualizovala informace poskytovatel a sledování Číslo. Selhání ověření tyto informace do dvou týdnů může způsobit úloha odstraňuje a disky nejsou zpracována.

   ![Ukládání přesouvání informace](./media/backup-azure-backup-import-export/joboverview.png)<br/>

   ![Ukládání přesouvání informace](./media/backup-azure-backup-import-export/tracking-info.png)

### <a name="time-to-process-the-drives"></a>Doba zpracování jednotky 
Množství času úlohu importu do Azure se liší v závislosti na různých faktorech, například přesouvání čas zpracování úlohy typu, typ a velikost dat kopírovány a velikosti disků zadat. Služba Azure Import/Export nemá SLA, ale po disky jsou přijaty službu snaží dokončení kopírování zálohování dat do účtu úložiště Azure za 7 až 10 dní. 

### <a name="monitoring-azure-import-job-status"></a>Stav monitorování úloze importu v Azure
Můžete sledovat stav úlohy importu z portálu Azure přechodem na **úlohy importu a exportu** stránku a vyberte úlohu. Další informace o stavu úlohy importu, najdete v článku [exportovat Import úložiště služby](../storage/common/storage-import-export-service.md) článku.

### <a name="complete-the-workflow"></a>Dokončení pracovního postupu
Po úspěšném dokončení úlohy importu, je k dispozici ve vašem účtu úložiště dat prvotní zálohy. Při příští naplánované zálohování zálohování Azure zkopíruje obsah dat z účtu úložiště do trezoru služeb zotavení, jak je uvedeno níže: 

   ![Kopírování dat do trezoru služeb zotavení](./media/backup-azure-backup-import-export/copyingfromstorageaccounttoazurebackup.png)<br/>

Při příští naplánované zálohování zálohování Azure provádí přírůstkové zálohování.

### <a name="cleaning-up-resources"></a>Vymazání prostředků
Po dokončení prvotní zálohování můžete bezpečně odstranit data importovat do kontejneru úložiště Azure a zálohování dat v pracovní umístění.

## <a name="next-steps"></a>Další postup
* Všechny dotazy týkající se Azure Import/Export pracovního postupu najdete v části [používat službu Microsoft Azure Import/Export k přenosu dat do úložiště objektů Blob](../storage/common/storage-import-export-service.md).
* Informace naleznete v sekci zálohování offline ze služby Azure Backup [– nejčastější dotazy](backup-azure-backup-faq.md) pro všechny dotazy týkající se pracovní postup.
