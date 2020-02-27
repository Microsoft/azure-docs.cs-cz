---
title: SAP HANA Azure Backup na úrovni souborů | Microsoft Docs
description: Existují dvě hlavní možnosti zálohování SAP HANA na virtuálních počítačích Azure. Tento článek se zabývá SAP HANA Azure Backup na úrovni souborů.
services: virtual-machines-linux
documentationcenter: ''
author: hermanndms
manager: juergent
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ums.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 07/05/2018
ms.author: hermannd
ms.openlocfilehash: d1044e4cbfd59cdf58af1a132a64e7143083905c
ms.sourcegitcommit: f15f548aaead27b76f64d73224e8f6a1a0fc2262
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/26/2020
ms.locfileid: "77616155"
---
# <a name="sap-hana-azure-backup-on-file-level"></a>SAP HANA Azure Backup na úrovni souborů

## <a name="introduction"></a>Úvod

Tato část je součástí série článků souvisejících v rámci zálohování SAP HANA. [Průvodce zálohováním pro SAP HANA v Azure Virtual Machines](./sap-hana-backup-guide.md) poskytuje přehled a informace o tom, jak začít, a [SAP HANA zálohování na základě snímků úložiště](./sap-hana-backup-storage-snapshots.md) pokrývá možnost zálohování na základě snímků úložiště.

Různé typy virtuálních počítačů v Azure umožňují, aby byl připojený jiný počet virtuálních pevných disků. Přesné podrobnosti jsou popsány v části [velikosti pro virtuální počítače se systémem Linux v Azure](../../linux/sizes.md). Pro testy uvedené v této dokumentaci jsme použili GS5 virtuální počítač Azure, který umožňuje 64 připojených datových disků. V případě větších SAP HANA systémů už může být u souborů dat a protokolů, které jsou v kombinaci s softwarovým obložením pro optimální propustnost v/v, zavedený velký počet disků. Další podrobnosti o navrhovaných konfiguracích disků pro nasazení SAP HANA na virtuálních počítačích Azure najdete v článku [SAP HANA v provozní příručce Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations). Tato doporučení zahrnují taky doporučení na místo na disku pro místní zálohování.

V tuto chvíli není k dispozici žádná SAP HANA integrace služby Backup v rámci služby Azure Backup. Standardní způsob, jak spravovat zálohování nebo obnovení na úrovni souboru, je zálohování na základě souborů prostřednictvím SAP HANA studia nebo prostřednictvím příkazů SAP HANA SQL. Další informace najdete v referenčních informacích o [SAP HANA SQL a systémových zobrazeních](https://help.sap.com/hana/SAP_HANA_SQL_and_System_Views_Reference_en.pdf) .

![Na tomto obrázku je znázorněno dialogové okno položky nabídky zálohování v SAP HANA studiu](media/sap-hana-backup-file-level/image022.png)

Na tomto obrázku je znázorněno dialogové okno položky nabídky zálohování v SAP HANA studiu. Když zvolíte typ &quot;soubor,&quot; jeden musí zadat cestu v systému souborů, kde SAP HANA zapisuje záložní soubory. Obnovení funguje stejným způsobem.

I když je tato volba zvukem jednoduchá a přímá, je k dispozici nějaký ohled. Jak už bylo zmíněno dříve, virtuální počítač Azure má omezení počtu datových disků, které se dají připojit. V závislosti na velikosti požadavků databáze a propustnosti disku nemusí být možné ukládat SAP HANA záložní soubory do systémů souborů virtuálního počítače. to může zahrnovat softwarové obložení na více datových disků. V tomto článku jsou k dispozici různé možnosti přesunutí těchto souborů zálohy a Správa omezení velikosti souborů a výkonu při zpracování terabajtů dat.

Další možností, která nabízí větší volnost v oblasti celkové kapacity, je úložiště objektů BLOB v Azure. I když je jeden objekt BLOB taky omezený na 1 TB, celková kapacita jednoho kontejneru objektů BLOB je v současnosti 500 TB. Kromě toho zákazníkům dává možnost zvolit, že se jim říká &quot;studená&quot; úložiště objektů blob, což má zvýhodněné náklady. Podrobnosti o studeném úložišti objektů BLOB najdete v tématu [Azure Blob Storage: horká a studená vrstva úložiště](../../../storage/blobs/storage-blob-storage-tiers.md) .

Pro zajištění vyšší bezpečnosti použijte geograficky replikovaný účet úložiště pro uložení záloh SAP HANA. Podrobnosti o replikaci účtu úložiště najdete v tématu [replikace Azure Storage](../../../storage/common/storage-redundancy.md) .

Jedna z nich by mohla umístit vyhrazené virtuální pevné disky pro SAP HANA zálohy ve vyhrazeném účtu úložiště záloh, který je geograficky replikovaný. Nebo jinak může zkopírovat virtuální pevné disky, které udržují SAP HANA zálohy do geograficky replikovaného účtu úložiště, nebo do účtu úložiště, který je v jiné oblasti.

## <a name="azure-backup-agent"></a>Agent Azure Backup

Azure Backup nabízí možnost nejen zálohovat pouze kompletní virtuální počítače, ale také soubory a adresáře prostřednictvím agenta zálohování, který musí být nainstalován v hostovaném operačním systému. Ale tento agent se podporuje jenom v systému Windows (viz [zálohování Windows serveru nebo klienta do Azure pomocí modelu nasazení Správce prostředků](../../../backup/backup-configure-vault.md)).

Alternativním řešením je nejprve zkopírovat SAP HANA záložní soubory do virtuálního počítače s Windows v Azure (například přes sdílenou složku SAMBA) a potom z něj použít agenta Azure Backup. I když je to technicky možné, by mohlo dojít ke složitosti a zpomalení procesu zálohování nebo obnovení poměrně trochu kvůli kopii mezi Linuxem a virtuálním počítačem s Windows. Nedoporučujeme postupovat podle tohoto přístupu.

## <a name="azure-blobxfer-utility-details"></a>Podrobnosti o nástroji Azure blobxfer

K ukládání adresářů a souborů v Azure Storage může jeden použít CLI nebo PowerShell nebo vyvíjet nástroj pomocí jedné ze [sad SDK Azure](https://azure.microsoft.com/downloads/). Pro kopírování dat do služby Azure Storage je k dispozici také nástroj AzCopy připravený k použití. (viz [přenos dat pomocí nástroje příkazového řádku AzCopy](../../../storage/common/storage-use-azcopy.md)).

Proto se blobxfer použil k kopírování souborů zálohy SAP HANA. Je to open source, který používá mnoho zákazníků v produkčních prostředích a je dostupný na [GitHubu](https://github.com/Azure/blobxfer). Tento nástroj umožňuje jedné kopírování dat přímo do úložiště objektů blob Azure nebo sdílené složky Azure. Nabízí také řadu užitečných funkcí, jako jsou hash MD5 nebo automatické paralelismuy při kopírování adresáře s více soubory.

## <a name="sap-hana-backup-performance"></a>Výkon zálohování SAP HANA

![Tento snímek obrazovky je konzola SAP HANA Backup v SAP HANA studiu](media/sap-hana-backup-file-level/image023.png)

Tento snímek obrazovky je SAP HANA konzole pro zálohování v SAP HANA studiu. Trvalo to přibližně 42 minut, než se zálohuje 230 GB na jednom disku úložiště Azure Standard připojeném k virtuálnímu počítači HANA pomocí systému souborů XFS.

![Tento snímek obrazovky je YaST na testovacím virtuálním počítači SAP HANA.](media/sap-hana-backup-file-level/image024.png)

Tento snímek obrazovky je YaST na testovacím virtuálním počítači SAP HANA. Jedna z nich může pro SAP HANA zálohu zobrazit jeden disk 1 TB, jak je uvedeno dříve. Zálohování na 230 GB trvalo přibližně 42 minut. Kromě toho 5 200 GB disků bylo připojeno a byl vytvořen softwarový md0 RAID s prokládáním na těchto pěti datových discích Azure.

![Opakování stejné zálohy u softwarového pole RAID s prokládáním v pěti připojených datových discích Azure Standard Storage](media/sap-hana-backup-file-level/image025.png)

Opakuje stejnou zálohu na softwarovém poli RAID s prokládáním přes pět připojených datových disků Azure úrovně Standard, což je doba zálohování od 42 minut až do 10 minut. K virtuálnímu počítači byly připojeny disky bez ukládání do mezipaměti. Proto je zřejmé, jak důležitá propustnost zápisu na disk je pro čas zálohování. Jedna z nich pak může přejít na Azure Premium Storage, aby lépe urychlila proces optimálního výkonu. Obecně platí, že Azure Premium Storage by se měly používat pro produkční systémy.

## <a name="copy-sap-hana-backup-files-to-azure-blob-storage"></a>Kopírování souborů SAP HANA zálohování do Azure Blob Storage

Další možností pro rychlé uložení SAP HANAch záložních souborů je úložiště objektů BLOB v Azure. Jeden kontejner objektů BLOB má limit 500 TB, který je dostatečný pro některé menší SAP HANA systémy, používá M32ts, M32ls, M64ls a GS5 typy virtuálních počítačů Azure, aby se zajistilo dostatečné SAP HANA zálohování. Zákazníci si můžou vybrat mezi &quot;horkou&quot; a &quot;studeným&quot; BLOB Storage (viz [Azure Blob Storage: Hot a studená vrstva úložiště](../../../storage/blobs/storage-blob-storage-tiers.md)).

Pomocí nástroje blobxfer je snadné zkopírovat SAP HANA záložní soubory přímo do úložiště objektů BLOB v Azure.

![Tady vidíte soubory úplné SAP HANA zálohování souborů.](media/sap-hana-backup-file-level/image026.png)

Tady vidíte soubory úplné SAP HANA zálohování souborů. Existují čtyři soubory a největší z nich má přibližně 230 GB.

![Přibližně 3000 sekund trvalo zkopírovat 230 GB do kontejneru objektů BLOB ve standardním účtu úložiště Azure](media/sap-hana-backup-file-level/image027.png)

V počátečním testu se nepoužívá hodnota hash MD5. pro zkopírování 230 GB do kontejneru objektů BLOB standardního úložiště Azure trvalo přibližně 3000 sekund.

![Na tomto snímku obrazovky se může zobrazit, jak vypadá na Azure Portal](media/sap-hana-backup-file-level/image028.png)

Na tomto snímku obrazovky se může zobrazit, jak vypadá na Azure Portal. Vytvořil se kontejner objektů BLOB s názvem &quot;SAP-HANA-backups&quot; a zahrnuje čtyři objekty blob, které reprezentují SAP HANA záložní soubory. Jedna z nich má velikost přibližně 230 GB.

Konzola zálohování HANA Studio umožňuje jednu omezit maximální velikost souborů záložních souborů HANA. Ve vzorovém prostředí se zvýšil výkon tím, že umožňuje mít několik menších záložních souborů místo jednoho velkého 230 souboru.

![Nastavení omezení velikosti záložního souboru na straně&#39;Hana nezvyšuje čas zálohování.](media/sap-hana-backup-file-level/image029.png)

Nastavení limitu velikosti záložního souboru na straně&#39;Hana nezvyšuje čas zálohování, protože soubory jsou zapsány postupně, jak je znázorněno na tomto obrázku. Omezení velikosti souboru bylo nastaveno na 60 GB, takže záloha vytvořila čtyři soubory velkých datových souborů místo jednoho souboru 230-GB. Použití několika záložních souborů je nezbytné pro zálohování databází HANA, které využívají paměť větších virtuálních počítačů Azure, jako jsou M64s, M64ms, M128s a M128ms.

![Pokud chcete otestovat paralelismus nástroje blobxfer, je maximální velikost souboru pro zálohy HANA nastavená na 15 GB.](media/sap-hana-backup-file-level/image030.png)

Pokud chcete otestovat paralelismus nástroje blobxfer, je maximální velikost souborů pro zálohy HANA nastavená na 15 GB, což vedlo k 19 záložním souborům. Tato konfigurace zaznamenala čas, kdy blobxfer kopírovat 230 GB do úložiště objektů blob Azure od 3000 sekund až po 875 sekund.

Z tohoto důvodu je výsledkem limitu 60 MB/s pro zápis objektu blob Azure. Paralelismus prostřednictvím více objektů BLOB řeší kritické body, ale existuje Nevýhodou: zvýšení výkonu nástroje blobxfer pro kopírování všech těchto záložních souborů HANA do úložiště objektů BLOB v Azure přináší zatížení pro virtuální počítač HANA i síť. Bude to mít vliv na provoz systému HANA.

## <a name="blob-copy-of-dedicated-azure-data-disks-in-backup-software-raid"></a>Kopie vyhrazených datových disků Azure v objektu BLOB v zálohovacím softwaru RAID

Na rozdíl od ručního zálohování dat virtuálního počítače se v tomto postupu nezálohují všechny datové disky na virtuálním počítači, aby se uložila celá instalace SAP, včetně dat HANA, souborů protokolu HANA a konfiguračních souborů. Místo toho je vhodné mít vyhrazené softwarové pole RAID s prokládáním na několika virtuálních pevných discích Azure pro ukládání úplné SAP HANA zálohy souborů. Jedna zkopíruje pouze tyto disky, které mají SAP HANA zálohu. Můžou se snadno uchovávat ve vyhrazeném účtu úložiště pro zálohování HANA nebo být připojené k vyhrazenému virtuálnímu počítači pro správu zálohování &quot;&quot; k dalšímu zpracování.

![Všechny potřebné virtuální pevné disky se zkopírovaly pomocí příkazu * * Start-azurestorageblobcopy * * PowerShellu.](media/sap-hana-backup-file-level/image031.png)

Po dokončení zálohování do místního softwarového pole RAID se všechny virtuální pevné disky zkopírovaly pomocí příkazu **Start-azurestorageblobcopy** prostředí PowerShell (viz [Start-azurestorageblobcopy](/powershell/module/azure.storage/start-azurestorageblobcopy)). Vzhledem k tomu, že má vliv jenom na vyhrazený systém souborů pro uchovávání záložních souborů, nemusíte mít žádné obavy o SAP HANA konzistenci souborů dat nebo souboru protokolu na disku. Výhodou tohoto příkazu je, že funguje, i když virtuální počítač zůstane online. Pokud se chcete ujistit, že žádný proces nezapisuje do zálohovacího skladu, nezapomeňte ho před kopírováním objektu BLOB odpojit a potom ho znovu připojit. Nebo může použít vhodný způsob, jak &quot;zablokovat&quot; systému souborů. Například přes XFS\_zamrznutí pro systém souborů XFS.

![Tento snímek obrazovky zobrazuje seznam objektů BLOB v kontejneru VHD na Azure Portal.](media/sap-hana-backup-file-level/image032.png)

Azure Portal na tomto snímku obrazovky se zobrazuje seznam objektů BLOB v kontejneru &quot;VHD&quot; kontejner. Snímek obrazovky ukazuje pět virtuálních pevných disků, které byly připojeny k virtuálnímu počítači SAP HANA serveru, aby sloužily jako softwarové pole RAID pro uchovávání SAP HANAch záložních souborů. Zobrazuje také pět kopií, které byly pořízeny prostřednictvím příkazu pro kopírování objektů BLOB.

![Pro účely testování se k virtuálnímu počítači s aplikačním serverem připojily kopie disků RAID softwaru SAP HANA Backup.](media/sap-hana-backup-file-level/image033.png)

Pro účely testování se k virtuálnímu počítači s aplikačním serverem připojily kopie disků RAID softwaru SAP HANA Backup.

![Virtuální počítač aplikačního serveru se vypnul, aby se připojily kopie disků.](media/sap-hana-backup-file-level/image034.png)

Virtuální počítač aplikačního serveru se vypnul, aby se připojily kopie disků. Po spuštění virtuálního počítače se disky a RAID správně zjistily (připojily se přes UUID). Nebyl nalezen pouze přípojný bod, který byl vytvořen pomocí oddílu YaST. Následně se kopie souborů SAP HANA zálohy na úrovni operačního systému stala viditelnými.

## <a name="copy-sap-hana-backup-files-to-nfs-share"></a>Kopírovat SAP HANA záložní soubory do sdílené složky NFS

Pokud chcete snížit potenciální dopad na SAP HANA systému z hlediska výkonu nebo místa na disku, může to mít za důsledek uložení SAP HANA souborů zálohy do sdílené složky systému souborů NFS. Technicky funguje, ale to znamená, že jako hostitele sdílené složky systému souborů NFS používá druhý virtuální počítač Azure. Vzhledem k šířce pásma sítě virtuálních počítačů by neměl být malá velikost virtuálního počítače. To by mělo smysl vypnout tuto &quot;záložním virtuálním počítači&quot; a aktivovat ho jenom pro provádění SAP HANA zálohování. Zápis do sdílené složky systému souborů NFS přináší zatížení sítě a ovlivňuje SAP HANA systém, ale pouze správu záložních souborů, a to na základě &quot;záložního virtuálního počítače,&quot; by neměl vliv na systém SAP HANA.

![Sdílená složka NFS z jiného virtuálního počítače Azure je připojená k virtuálnímu počítači SAP HANA serveru.](media/sap-hana-backup-file-level/image035.png)

K ověření případu použití NFS je sdílená složka NFS z jiného virtuálního počítače Azure připojená k virtuálnímu počítači s SAP HANA serverem. Nepoužilo se žádné speciální ladění NFS.

![Trvalo 1 hodinu a 46 minut, než se zálohování provede přímo](media/sap-hana-backup-file-level/image036.png)

Sdílená složka NFS byla rychlá sada, třeba ta na serveru SAP HANA. Při zápisu do místní sady prokládaných svazků ale trvalá záloha přímo na sdílené složce NFS místo 10 minut, a to za 1 hodiny a 46 minut.

![Alternativa není mnohem rychlejší, a to za 1 hodinu a 43 minut.](media/sap-hana-backup-file-level/image037.png)

Alternativou zálohování do místní sady Stripe a zkopírování do sdílené složky NFS na úrovni operačního systému (jednoduchý příkaz **CP-AVR** ) nemá mnohem rychlejší. Trvalo 1 hodinu a 43 minut.

Takže funguje, ale výkon není dobrý pro záložní test 230-GB. V případě více terabajtů by vypadala ještě horší.

## <a name="copy-sap-hana-backup-files-to-azure-files"></a>Kopírování souborů SAP HANA zálohování do souborů Azure

Sdílenou složku služby soubory Azure je možné připojit v rámci virtuálního počítače Azure Linux. Článek [použití služby Azure File Storage se systémem Linux](../../../storage/files/storage-how-to-use-files-linux.md) poskytuje podrobné informace o tom, jak to provést. Mějte na paměti, že pro jednu sdílenou složku Azure je aktuálně povolená kvóta o velikosti 5 TB a velikost souboru je omezena na 1 TB na jeden soubor. Další informace najdete v tématu [škálovatelnost a cíle výkonnosti souborů Azure](../../../storage/files/storage-files-scale-targets.md).

Tyto testy se ale ukázaly, ale SAP HANA zálohování&#39;nefunguje v současnosti přímo s tímto druhem připojení CIFS. Je také uvedený v [SAP Note 1820529](https://launchpad.support.sap.com/#/notes/1820529) , že CIFS se nedoporučuje.

![Tento obrázek ukazuje chybu v dialogovém okně zálohování v SAP HANA studiu](media/sap-hana-backup-file-level/image038.png)

Na tomto obrázku se při pokusu o zálohování přímo do sdílené složky Azure připojené ke CIFS zobrazuje chyba v dialogovém okně zálohování v SAP HANA studiu. Proto jedna musí provést standardní SAP HANA zálohu do systému souborů virtuálního počítače a pak zkopírovat záložní soubory ze souboru do služby Azure File Service.

![Tento obrázek ukazuje, že trvalo přibližně 929 sekund až po zkopírování 19 SAP HANA záložních souborů.](media/sap-hana-backup-file-level/image039.png)

Tento obrázek ukazuje, že trvalo přibližně 929 sekund zkopírovat 19 SAP HANA záložních souborů o celkovou velikost přibližně 230 GB do sdílené složky Azure.

![Zdrojová adresářová struktura ve SAP HANAm virtuálním počítači se zkopírovala do sdílené složky Azure.](media/sap-hana-backup-file-level/image040.png)

Na tomto snímku obrazovky vidíte, že se zdrojová adresářová struktura ve SAP HANA virtuálním počítači zkopírovala do sdílené složky Azure: jeden adresář (Hana\_Backup\_FSL\_15gb) a 19 jednotlivých záložních souborů.

Ukládání SAP HANA záložních souborů do souborů Azure může být zajímavou možností v budoucnu, pokud SAP HANA zálohování souborů podporuje přímo. Nebo když bude možné připojit soubory Azure přes systém souborů NFS a maximální kvóta je podstatně vyšší než 5 TB.

## <a name="next-steps"></a>Další kroky
* [Průvodce zálohováním pro SAP HANA v Azure Virtual Machines](sap-hana-backup-guide.md) poskytuje přehled a informace o tom, jak začít.
* [SAP HANA zálohování na základě snímků úložiště](sap-hana-backup-storage-snapshots.md) popisuje možnost zálohování na základě snímků úložiště.
* Informace o tom, jak vytvořit vysokou dostupnost a naplánovat zotavení po havárii SAP HANA v Azure (velké instance), najdete v tématu [SAP Hana (velké instance) vysoká dostupnost a zotavení po havárii v Azure](hana-overview-high-availability-disaster-recovery.md).
