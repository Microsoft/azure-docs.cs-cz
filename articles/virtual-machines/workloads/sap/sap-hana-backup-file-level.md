---
title: SAP HANA Azure Backup na úrovni souborů | Dokumentace Microsoftu
description: Existují dvě hlavní zálohování možnosti pro SAP HANA na virtuálních počítačích Azure, tento článek se týká SAP HANA Azure Backup na úrovni souborů
services: virtual-machines-linux
documentationcenter: ''
author: hermanndms
manager: jeconnoc
editor: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ums.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 07/05/2018
ms.author: rclaus
ms.openlocfilehash: d3d1769766053b513a98df153cb635ae148f26b1
ms.sourcegitcommit: ab3b2482704758ed13cccafcf24345e833ceaff3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/06/2018
ms.locfileid: "37867366"
---
# <a name="sap-hana-azure-backup-on-file-level"></a>SAP HANA Azure Backup na úrovni souborů

## <a name="introduction"></a>Úvod

Toto je část z třídílné série článků související na zálohování SAP HANA. [Průvodce zálohováním pro SAP HANA v Azure Virtual Machines](./sap-hana-backup-guide.md) poskytuje přehled a informace pro zahájení práce a [zálohování SAP HANA založené na snímcích úložiště](./sap-hana-backup-storage-snapshots.md) zahrnuje zálohování možnost založených na snímcích úložiště.

Různé typy virtuálních počítačů v Azure umožňují jiný počet připojených virtuálních pevných disků. Podrobnosti o jsou dokumentovány v článku [velikostí pro virtuální počítače s Linuxem v Azure](../../linux/sizes.md). Pro testy uvedené v této dokumentaci jsme použili na virtuálním počítači GS5 Azure, což umožní 64 připojenými datovými disky. Pro větší systémy SAP HANA může být velký počet disků už používá pro data a soubory protokolu, případně v kombinaci s prokládáním softwaru pro propustnost vstupně-výstupních operací disku optimální. Podrobné informace o konfiguraci navrhované disku pro nasazení SAP HANA na virtuálních počítačích Azure najdete v článku [SAP HANA v Azure Provozní příručka](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations). Doporučení jsou včetně doporučení místa na disku pro místní záložní kopie.

V tuto chvíli není k dispozici služba Azure Backup bez integrace zálohování SAP HANA. Standardní způsob pro správu zálohování a obnovení na úrovni souborů je službou backup souborové SAP HANA Studio nebo SAP HANA SQL příkazy. Zobrazit [SAP HANA SQL a odkaz na zobrazení systém](https://help.sap.com/hana/SAP_HANA_SQL_and_System_Views_Reference_en.pdf) Další informace.

![Tento obrázek ukazuje dialog záložní položku v sadě Studio SAP HANA](media/sap-hana-backup-file-level/image022.png)

Tento obrázek ukazuje dialog záložní položku v sadě Studio SAP HANA. Když vyberete typ &quot;souboru&quot; je nutné zadat cestu v systému souborů SAP HANA, kam zapisuje záložní soubory. Obnovení funguje stejným způsobem.

Přestože tato volba zvuky jednoduché a rovnou vpřed, existují některé aspekty. Jak jsme zmínili, virtuální počítač Azure má omezení, kolik datových disků, které je možné připojit. Nemusí být kapacitou pro ukládání záložních souborů SAP HANA v systémech souborů virtuálního počítače, v závislosti na velikosti databáze a disk požadavků propustnosti, které může zahrnovat software prokládáním napříč více datových disků. Různé možnosti pro přesunutí těchto záložních souborů a správu omezení velikosti souboru a výkon při zpracování terabajtů dat, najdete dál v tomto článku.

Další možností, která nabízí větší volnost týkající se celková kapacita, je úložiště objektů blob v Azure. Jeden objekt blob je také omezena na 1 TB, celková kapacita kontejneru jeden objekt blob je aktuálně 500 TB. Kromě toho poskytuje zákazníkům možnost vybrat takzvané &quot;cool&quot; služby blob storage, který má snížení nákladů. Zobrazit [Azure Blob Storage: horká a studená úroveň úložiště](../../../storage/blobs/storage-blob-storage-tiers.md) podrobnosti o cool blob storage.

Pro dodatečné zabezpečení použijte účet geograficky replikované úložiště k ukládání záloh SAP HANA. Zobrazit [replikace Azure Storage](../../../storage/common/storage-redundancy.md) podrobnosti o replikace účtu úložiště.

V účtu vyhrazené záložní úložiště, který je geograficky replikovaného jeden by mohlo způsobit vyhrazené virtuální pevné disky pro zálohování SAP HANA. Jinak jeden zkopírovat virtuální pevné disky, které udržují zálohování SAP HANA na účet geograficky replikované úložiště nebo do účtu úložiště, který je v jiné oblasti.

## <a name="azure-backup-agent"></a>Agenta Azure backup

Azure backup nabízí možnost nejen zálohovat kompletní virtuální počítače, ale také soubory a adresáře prostřednictvím agenta zálohování, které musí být nainstalovaná na hostovaném operačním systému. Ale tento agent je podporována pouze na Windows (viz [zálohování Windows serveru nebo klienta do Azure s využitím modelu nasazení Resource Manager](../../../backup/backup-configure-vault.md)).

Alternativní řešení je nejprve zkopírujte záložní soubory SAP HANA do virtuálního počítače s Windows v Azure (třeba přes SAMBA sdílet) a potom pomocí agenta Azure backup z něj. I když je technicky možné, ho by přidat složitost a zpomalit zálohování nebo obnovení procesu odlišují kvůli kopírování mezi systémy Linux a Windows virtuální počítač. Není doporučeno tímto přístupem.

## <a name="azure-blobxfer-utility-details"></a>Podrobnosti o Azure blobxfer nástroje

K ukládání adresářů a souborů ve službě Azure storage, jeden může pomocí Powershellu nebo rozhraní příkazového řádku nebo Vyviňte nástroj pomocí jedné z [sady Azure SDK](https://azure.microsoft.com/downloads/). Pro kopírování dat do úložiště Azure je také připravené k použití nástroje AzCopy, ale je Windows jenom (viz [přenos dat pomocí nástroje příkazového řádku AzCopy](../../../storage/common/storage-use-azcopy.md)).

Proto blobxfer se použil pro kopírování souborů zálohování SAP HANA. To je open source, používá mnoho zákazníků v produkčním prostředí a dostupný na [Githubu](https://github.com/Azure/blobxfer). Tento nástroj umožňuje jeden ke zkopírování dat přímo do Azure blob storage nebo sdílené složky Azure. Také nabízí celou řadu užitečných funkcí, jako je hodnota hash md5 nebo automatické paralelismu při kopírování do adresáře s více soubory.

## <a name="sap-hana-backup-performance"></a>Výkon zálohování SAP HANA

![Tento snímek obrazovky je konzola zálohování SAP HANA v SAP HANA Studio](media/sap-hana-backup-file-level/image023.png)

Tento snímek obrazovky je konzola zálohování SAP HANA v SAP HANA Studio. Proveďte zálohu 230 GB na disk jednoho úložiště Azure úrovně standard připojených k virtuálnímu počítači HANA pomocí systému souborů, XFS trvalo asi 42 minut.

![Tento snímek obrazovky je YaST SAP HANA testovacího virtuálního počítače](media/sap-hana-backup-file-level/image024.png)

Tento snímek obrazovky je YaST SAP HANA testovacího virtuálního počítače. Jeden disk 1 TB pro zálohování SAP HANA jeden můžete vidět, jak je uvedeno nahoře. Trvalo asi 42 minut zálohování 230 GB. Kromě toho pět 200 GB disky a vytvořili md0 softwaru diskového pole RAID, s prokládáním nad rámec těchto pět disků Azure data.

![Opakující se stejnou zálohování softwaru diskového pole RAID, s prokládáním všech 5 připojené datové disky Azure storage úrovně standard](media/sap-hana-backup-file-level/image025.png)

Opakující se stejnou zálohování softwaru diskového pole RAID, s prokládáním všech 5 připojené úložiště Azure úrovně standard datové disky převést do režimu čas zálohování 42 minut až 10 minut. Disky byly připojené bez ukládání do mezipaměti k virtuálnímu počítači. Proto je zřejmé jak důležité propustnost zápisu disku určený čas zálohování. Jeden může přepnout na Azure Premium Storage dále zrychlit proces pro zajištění optimálního výkonu. Obecně platí by měla sloužit Azure Premium Storage pro produkční systémy.

## <a name="copy-sap-hana-backup-files-to-azure-blob-storage"></a>Zkopírujte záložní soubory SAP HANA do Azure blob storage

Další možností pro rychlé ukládání záložních souborů SAP HANA je úložiště objektů blob v Azure. Jeden kontejner pro jeden objekt blob má limit 500 TB, akorát na několik menších systémů SAP HANA pomocí typy M32ts, M32ls, M64ls a virtuálním počítačem GS5 virtuálního počítače Azure, zajistit dostatek zálohování SAP HANA. Zákazníci mají možnost mezi &quot;horké&quot; a &quot;studenou&quot; úložiště objektů blob (naleznete v tématu [Azure Blob Storage: horká a studená úroveň úložiště](../../../storage/blobs/storage-blob-storage-tiers.md)).

Pomocí nástroje blobxfer je snadné zkopírujte záložní soubory SAP HANA přímo do Azure blob storage.

![Zde jeden naleznete v souborech úplného zálohování souboru SAP HANA](media/sap-hana-backup-file-level/image026.png)

Zde jeden můžete zobrazit soubory úplné zálohování souboru SAP HANA. Existují čtyři soubory a největší je přibližně 230 GB.

![Jakou trvalo přibližně 3000 sekund se má zkopírovat 230 GB do kontejneru objektů blob v účtu úložiště Azure úrovně standard](media/sap-hana-backup-file-level/image027.png)

V počátečním testu bez použití algoritmu hash md5, trvalo přibližně 3000 sekund se má zkopírovat 230 GB do kontejneru objektů blob v účtu úložiště Azure úrovně standard.

![Na tomto snímku obrazovky one najdete, jak bude vypadat na webu Azure portal](media/sap-hana-backup-file-level/image028.png)

Na tomto snímku obrazovky one najdete, jak bude vypadat na webu Azure portal. Kontejner objektů blob s názvem &quot;sap-hana zálohy&quot; byla vytvořena a zahrnuje čtyři objekty BLOB, které představují záložní soubory SAP HANA. Jeden z nich má velikost přibližně 230 GB.

Konzola zálohování HANA Studio umožňuje z nich se má omezení maximální velikost souboru HANA záložních souborů. Ukázková prostředí vyšší výkon tím, že je možné mít několik menších zálohování souborů, místo jednoho velkého souboru 230 GB.

![Nastavení omezení velikosti záložní soubor kódu na straně HANA&#39;t zlepšit čas zálohování](media/sap-hana-backup-file-level/image029.png)

Nastavení omezení velikosti záložní soubor kódu na straně HANA&#39;t zvýšit čas zálohování, protože soubory jsou zapsány postupně, jak je znázorněno na tomto obrázku. Limit velikosti souboru byla nastavena na 60 GB, takže záloha vytvořit čtyři velkých datových souborů místo jediného souboru 230 GB. Použití více záložních souborů je nezbytná pro zálohování databáze HANA, které využívají paměti větší virtuální počítače Azure jako M64s, M64ms, M128s a M128ms.

![K otestování paralelismu blobxfer nástroje maximální velikost souboru pro HANA zálohy pak nastavila na 15 GB](media/sap-hana-backup-file-level/image030.png)

K otestování paralelismu blobxfer nástroje, byla nastavte maximální velikost souboru pro zálohy HANA až 15 GB, což vedlo 19 záložní soubory. Tuto konfiguraci převést do režimu dobu blobxfer ke zkopírování 230 GB do Azure blob storage z 3000 sekund dolů 875 sekund.

Tento výsledek je kvůli omezení 60 MB za sekundu pro zápis objektu blob Azure. Problémové místo řeší paralelismu přes víc objektů BLOB, ale neexistuje nevýhodou: zvýšení výkonu blobxfer nástroje pro kopírování všech těchto HANA záložní soubory do Azure blob storage dává zatížení na HANA virtuálního počítače a sítě. Operace systému HANA stane vliv.

## <a name="blob-copy-of-dedicated-azure-data-disks-in-backup-software-raid"></a>Objekt BLOB kopírování vyhrazené datové disky Azure v zálohování softwarového pole RAID

Na rozdíl od ručního VM data zálohování disku v rámci tohoto přístupu jeden zálohovat všechny datové disky na virtuálním počítači k uložení celé instalace SAP HANA data, včetně HANA neprotokoluje soubory a soubory konfigurace. Místo toho myšlenka je mít vyhrazené softwarového pole RAID s prokládáním napříč více virtuálních pevných disků Azure data pro ukládání úplné zálohování souboru SAP HANA. Jeden zkopíruje pouze tyto disky, které mají zálohování SAP HANA. Se může snadno udržovat v vyhrazený účet úložiště pro zálohování HANA nebo připojené k vyhrazené &quot;zálohování správy virtuálního počítače&quot; k dalšímu zpracování.

![Všechny virtuální pevné disky používané byly zkopírovány pomocí ** start-azurestorageblobcopy ** příkazu prostředí PowerShell](media/sap-hana-backup-file-level/image031.png)

Po dokončení zálohování na místní softwarového pole RAID, všechny virtuální pevné disky používané byly zkopírovány pomocí **start azurestorageblobcopy** příkaz Powershellu (naleznete v tématu [Start AzureStorageBlobCopy](/powershell/module/azure.storage/start-azurestorageblobcopy)). Jak ovlivňuje to jenom systému souborů vyhrazené pro udržování záložní soubory, neexistují žádné obavy o SAP HANA dat či protokolu konzistence souboru na disku. Výhodou tohoto příkazu je, že to funguje, zatímco zůstane online virtuálního počítače. Ujistěte se, že žádný proces zapisuje do zálohování prokládané sady, je potřeba odpojit před kopírováním objektů blob a připojit ji znovu později. Nebo jeden vhodný způsob pro &quot;ukotvit&quot; systému souborů. Třeba přes xfs\_ukotvit pro systém souborů XFS.

![Tento snímek obrazovky ukazuje seznam objektů BLOB v kontejneru virtuální pevné disky na portálu Azure portal](media/sap-hana-backup-file-level/image032.png)

Tento snímek obrazovky se zobrazí seznam objektů BLOB v &quot;virtuální pevné disky&quot; kontejneru na webu Azure portal. Na snímku obrazovky vidíte pět virtuální pevné disky, které byly připojeny k serveru SAP HANA VM, která bude sloužit jako softwarového pole RAID, budou se SAP HANA zálohy uchovávat. Ukazuje také pět kopií, které byly provedeny prostřednictvím příkazu kopírování objektu blob.

![Pro účely testování, byla připojena kopie disků RAID zálohovacího softwaru SAP HANA na aplikační server VM](media/sap-hana-backup-file-level/image033.png)

Pro účely testování, byla připojena kopie disků RAID zálohovacího softwaru SAP HANA na aplikační server virtuálního počítače.

![Zkopíruje aplikačního serveru, na které byl virtuální počítač vypnutý připojit disk](media/sap-hana-backup-file-level/image034.png)

Aplikační server virtuální počítač byl vypnut připojte disk zkopíruje. Po spuštění virtuálního počítače, disky a RAID byly zjištěny správně (připojený přes UUID). Pouze přípojný bod nebyl nalezen, který se vytvořil prostřednictvím YaST rozdělovač. Potom zkopíruje soubor zálohy SAP HANA začala být viditelná na úrovni operačního systému.

## <a name="copy-sap-hana-backup-files-to-nfs-share"></a>Zkopírujte záložní soubory SAP HANA do sdílené složky systému souborů NFS

Pokud chcete snížit potenciální dopad na systému SAP HANA z perspektivy místa na disku a výkonu, jeden zvážit ukládání záložních souborů SAP HANA ve sdílené složce systému souborů NFS. Technicky vzato to funguje, ale to znamená, že pomocí druhého virtuálního počítače Azure jako hostitele sdílené složky NFS. Neměl by být malou velikost virtuálního počítače z důvodu šířky pásma sítě virtuálních počítačů. To dává smysl potom vypnout to &quot;zálohování virtuálního počítače&quot; a přiřaďte jej pouze pro provádění zálohování SAP HANA. Zápis v systému souborů NFS sdílenou složku vloží zatížení v síti a má vliv na systému SAP HANA, ale pouze Správa později na záložní soubory &quot;zálohování virtuálního počítače&quot; nebude vůbec ovlivnit systému SAP HANA.

![Sdílené složky NFS z jiného virtuálního počítače Azure se připojit k serveru SAP HANA VM](media/sap-hana-backup-file-level/image035.png)

Pokud chcete ověřit, že případ použití systému souborů NFS, se sdílené složky NFS z jiného virtuálního počítače Azure připojit k serveru SAP HANA VM. Došlo k dispozici žádné speciální systému souborů NFS ladění použít.

![Trvala 1 hodinu a 46 minut provedete zálohování přímo](media/sap-hana-backup-file-level/image036.png)

Sdílené složky systému souborů NFS se rychle prokládané sady, jako je ten, na serveru SAP HANA. Nicméně trvala 1 hodinu a 46 minut zálohování přímo na místo 10 minut, při zápisu do místní stripe nastavení sdílené složky systému souborů NFS.

![Alternativním nebyl mnohem rychlejší, protože na 1 hodinu až 43 minut](media/sap-hana-backup-file-level/image037.png)

Alternativní provádí zálohování do sady místních stripe a zkopírování do sdílené složky systému souborů NFS na úrovni operačního systému (jednoduchý **cp - avr** příkaz) se mnohem rychleji. Trvala 1 hodinu až 43 minut.

Takže to funguje, ale výkon se po dobu zálohování testování 230 GB. Měl by vypadat ještě horší pro více terabajtů.

## <a name="copy-sap-hana-backup-files-to-azure-files"></a>Zkopírujte záložní soubory SAP HANA do služby soubory Azure

Je možné pro připojení sdílené složky služby soubory Azure uvnitř virtuálního počítače Azure s Linuxem. Tento článek [postupy používání Azure File storage s Linuxem](../../../storage/files/storage-how-to-use-files-linux.md) poskytuje podrobné informace o tom, jak to udělat. Uvědomte si, že je aktuálně kvótou 5 TB omezení jednu sdílenou složku Azure a maximální velikost souboru 1 TB na soubor. Zobrazit [Azure Storage škálovatelnost a cíle výkonnosti](../../../storage/common/storage-scalability-targets.md) informace o omezení úložiště.

Testy ukázaly, ale která není zálohování SAP HANA&#39;t aktuálně pracovat přímo s Tento typ připojení protokolu CIFS. Je také uvádí [1820529 Poznámka SAP](https://launchpad.support.sap.com/#/notes/1820529) Nedoporučená CIFS.

![Tento obrázek ukazuje chybu, v dialogovém okně Zálohování v SAP HANA Studio](media/sap-hana-backup-file-level/image038.png)

Tento obrázek ukazuje chybu v dialogovém okně Zálohování v SAP HANA Studio při pokusu o zálohovat přímo na připojeném CIFS sdílené složky Azure. Proto je nutné nejprve provést standardní zálohování SAP HANA do systému souborů virtuálního počítače a potom zkopírujte záložní soubory z něj do služby Azure file.

![Tento obrázek ukazuje, že trvalo asi 929 sekund zkopírujte záložní soubory 19 SAP HANA](media/sap-hana-backup-file-level/image039.png)

Tento obrázek ukazuje, že trvalo asi 929 sekund zkopírujte záložní soubory 19 SAP HANA s celkovou velikostí zhruba 230 GB do sdílené složky Azure.

![Zdrojové adresářové struktury na virtuální počítač SAP HANA byl zkopírován do sdílené složky Azure](media/sap-hana-backup-file-level/image040.png)

Na tomto snímku obrazovky jeden uvidí, že adresářové struktury zdroje na virtuální počítač SAP HANA byl zkopírován do sdílené složky Azure: jeden adresář (hana\_zálohování\_fsl\_15 gb) a 19 individuální záložních souborů.

Ukládání záložních souborů SAP HANA v Azure souborů může být zajímavé možnost v budoucnu, kdy záloh souboru SAP HANA podporují ho přímo. Nebo když je možné pro připojení služby soubory Azure pomocí systému souborů NFS a maximální kvótu vyplývající ze je výrazně vyšší než 5 TB.

## <a name="next-steps"></a>Další postup
* [Průvodce zálohováním pro SAP HANA v Azure Virtual Machines](sap-hana-backup-guide.md) poskytuje přehled a informace pro zahájení práce.
* [Zálohování SAP HANA založené na snímcích úložiště](sap-hana-backup-storage-snapshots.md) popisuje zálohování možnost založených na snímcích úložiště.
* Informace o vytvoření vysoké dostupnosti a plánování zotavení po havárii SAP Hana v Azure (velké instance), najdete v článku [SAP HANA (velké instance) vysokou dostupnost a zotavení po havárii v Azure](hana-overview-high-availability-disaster-recovery.md).
