---
title: SAP HANA Azure Backup na úrovni souborů | Microsoft Docs
description: Existují dvě hlavní zálohování možnosti pro SAP HANA na virtuálních počítačích Azure, tento článek se zabývá SAP HANA Azure Backup na úrovni souborů
services: virtual-machines-linux
documentationcenter: ''
author: hermanndms
manager: timlt
editor: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ums.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 3/13/2017
ms.author: rclaus
ms.openlocfilehash: 5db0ceb1648b5afa278e1cbe1c42fce8033bfdc1
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/23/2018
---
# <a name="sap-hana-azure-backup-on-file-level"></a>SAP HANA Azure Backup na úrovni souborů

## <a name="introduction"></a>Úvod

Toto je část třemi částmi řady související články v záloze SAP HANA. [Příručce zálohování pro SAP HANA ve virtuálních počítačích Azure](./sap-hana-backup-guide.md) poskytuje přehled a informace o začátcích, a [SAP HANA zálohování podle úložiště snímků](./sap-hana-backup-storage-snapshots.md) popisuje zálohování možnosti založené na snímku úložiště.

Prohlížení velikosti virtuálního počítače Azure, jeden můžete vidět, že GS5 umožňuje 64 disků připojených datových. U systémů SAP HANA může být velký počet disků už používá pro data a soubory protokolu, které by mohly mít v kombinaci s softwaru diskového pole RAID pro optimální disk propustnost vstupně-výstupní operace. Poté dotaz je pro uložení SAP HANA zálohování souborů, které může zaplnit připojené datové disky v čase? V tématu [velikostí pro virtuální počítače s Linuxem v Azure](../../linux/sizes.md) pro tabulky velikost virtuálního počítače Azure.

V tuto chvíli není k dispozici u služby zálohování Azure bez integrace zálohování SAP HANA. Standardní způsob, jak spravovat zálohování a obnovení na úrovni souborů je s zálohu na základě souborů prostřednictvím SAP HANA Studio nebo pomocí příkazů SAP HANA SQL. V tématu [SAP HANA SQL a Reference zobrazení systému](https://help.sap.com/hana/SAP_HANA_SQL_and_System_Views_Reference_en.pdf) Další informace.

![Na tomto obrázku je dialogové okno položky zálohování nabídky v SAP HANA Studio](media/sap-hana-backup-file-level/image022.png)

Následující obrázek ukazuje dialogové okno položky zálohování nabídky v SAP HANA Studio. Při výběru typu &quot;souboru&quot; jeden má v systému souborů zadejte cestu, kam SAP HANA zapisuje záložní soubory. Obnovení funguje stejným způsobem.

Když tato volba vyznívá jednoduché a rovnou předat dál, existují některé aspekty. Jak je uvedeno nahoře, virtuální počítač Azure má omezenou na počet datových disků, které je možné připojit. Nemusí být kapacitou pro ukládání záložních souborů SAP HANA v systémech souborů virtuálního počítače, v závislosti na velikosti databáze a disku požadavky propustnost, které mohou zahrnovat softwaru RAID pomocí prokládání napříč více dat disky. Různé možnosti pro přesunutí těchto záložních souborů a správu omezení velikosti souborů a výkon při zpracování terabajtů dat, jsou uvedeny dále v tomto článku.

Další možnost, která nabízí dává větší svobodu týkající se celkové kapacity, je úložiště objektů blob Azure. Zatímco jediného objektu blob je také omezen na 1 TB, právě 500 TB celkové kapacity jediného objektu blob kontejneru. Kromě toho nabízí zákazníkům možnost vybrat takzvané &quot;nástrojů&quot; úložiště objektů blob, který má výhody náklady. V tématu [Azure Blob Storage: za provozu a cool vrstvy úložiště](../../../storage/blobs/storage-blob-storage-tiers.md) podrobnosti o úložiště na studených objektů blob.

Pro dodatečné zabezpečení použijte účet geograficky replikované úložiště pro ukládání záloh SAP HANA. V tématu [replikace Azure Storage](../../../storage/common/storage-redundancy.md) podrobnosti o replikaci účet úložiště.

V účtu vyhrazeného zálohování úložiště, který je geograficky replikované jeden by mohlo způsobit vyhrazené virtuální pevné disky pro SAP HANA zálohy. Jinak jeden zkopírovat virtuální pevné disky, které udržují SAP HANA zálohy na účet geograficky replikované úložiště nebo na účet úložiště, který je v jiné oblasti.

## <a name="azure-backup-agent"></a>Azure backup agent

Zálohování Azure nabízí možnost nejen zálohovat dokončení virtuálních počítačů, ale také soubory a adresáře prostřednictvím agenta zálohování, které musí být nainstalovaná na hostovaný operační systém. Ale od prosince 2016 tohoto agenta je podporována pouze v systému Windows (viz [zálohování Windows serveru nebo klienta do Azure pomocí modelu nasazení Resource Manager](../../../backup/backup-configure-vault.md)).

Alternativní řešení, je nejdříve zkopírovat SAP HANA záložní soubory pro virtuální počítač s Windows v Azure (například prostřednictvím SAMBA sdílené složky) a potom pomocí aplikace Azure backup agent z ní. I když je technicky možné, ho by přidání složitosti a zpomalit zálohování nebo procesu obnovení odlišují kvůli kopírování mezi sady Linux a Windows virtuálního počítače. Není doporučeno postupujte podle tohoto přístupu.

## <a name="azure-blobxfer-utility-details"></a>Podrobnosti nástroj Azure blobxfer

K uložení adresářů a souborů na úložiště Azure, jeden může používat rozhraní příkazového řádku nebo prostředí PowerShell nebo vytvořte nástroj, pomocí jedné z [sady Azure SDK](https://azure.microsoft.com/downloads/). Pro kopírování dat do úložiště Azure je také nástroj připravené k použití, AzCopy, ale je Windows jenom (viz [přenos dat pomocí nástroje příkazového řádku Azcopy](../../../storage/common/storage-use-azcopy.md)).

Proto blobxfer byl použit pro kopírování SAP HANA záložní soubory. Je open source mnoho zákazníků v produkčním prostředí, dostupná a použitá na [Githubu](https://github.com/Azure/blobxfer). Tento nástroj umožňuje jeden ke zkopírování dat přímo do úložiště objektů blob v Azure nebo sdílenou složku Azure. Také nabízí celou řadu užitečných funkcí, jako je hodnota hash md5 nebo automatické paralelismus při kopírování adresáře s více soubory.

## <a name="sap-hana-backup-performance"></a>Výkon zálohování SAP HANA

![Je tento snímek zálohy konzoly SAP HANA ve SAP HANA Studio](media/sap-hana-backup-file-level/image023.png)

Zálohování konzoly SAP HANA ve SAP HANA Studio je tento snímek. Proveďte zálohu 230 GB na disku jednoho Azure standardní úložiště připojené k virtuálnímu počítači HANA používající systém souborů XFS trvalo asi 42 minut.

![Tomto snímku obrazovky je YaST SAP HANA testovacího virtuálního počítače](media/sap-hana-backup-file-level/image024.png)

Tomto snímku obrazovky je YaST SAP HANA testovacího virtuálního počítače. Jediný disk 1 TB pro SAP HANA zálohování jeden můžete vidět, jak je uvedeno nahoře. Trvalo asi 42 minut zálohování 230 GB. Kromě toho byla připojena pět disků 200 GB a vytvořit md0 softwaru diskového pole RAID, s proložení na základě těchto pět disků dat Azure.

![Opakující se stejnou zálohování na softwaru diskového pole RAID s proložení mezi pěti připojenými disky dat úložiště Azure úrovně standard](media/sap-hana-backup-file-level/image025.png)

Opakující se stejnou zálohování na softwaru diskového pole RAID s proložení mezi pěti připojit standardní úložiště Azure datových disků brzy čas zálohování z 42 minut do 10 minut. Disky byla připojena bez ukládání do mezipaměti na virtuální počítač. Proto je zřejmé jak důležité rychlost zapisování na disk se pro čas zálohování. Jeden může přepnout na Azure premium úložiště na další urychlit proces pro optimální výkon. Obecně platí Azure premium storage je třeba použít pro produkční systémy.

## <a name="copy-sap-hana-backup-files-to-azure-blob-storage"></a>Zkopírujte záložní soubory SAP HANA do Azure blob storage

Od prosince 2016 je nejlepší možnost rychle ukládání záložních souborů SAP HANA úložiště objektů blob Azure. Jednoho kontejneru typu jediného objektu blob může obsahovat maximálně 500 TB, dostatek většina systémů SAP HANA spuštěné ve virtuálním počítači GS5 v Azure, aby dostatečná zálohy SAP HANA. Volba mezi mají zákazníci &quot;aktivní&quot; a &quot;studenou&quot; úložiště objektů blob (najdete v části [Azure Blob Storage: za provozu a cool vrstvy úložiště](../../../storage/blobs/storage-blob-storage-tiers.md)).

Pomocí nástroje blobxfer je snadné zkopírujte záložní soubory SAP HANA přímo do Azure blob storage.

![Zde jeden uvidí soubory úplné zálohování souboru SAP HANA](media/sap-hana-backup-file-level/image026.png)

Zde jeden můžete zobrazit soubory úplné zálohování souboru SAP HANA. Existují čtyři soubory a největších jeden má zhruba 230 GB.

![Trvalo zhruba 3000 sekund pro kopírování 230 GB na kontejner objektů blob účet úložiště Azure úrovně standard](media/sap-hana-backup-file-level/image027.png)

Hodnota hash md5 není v počátečním testu, trvalo zhruba 3000 sekund pro kopírování 230 GB na kontejner objektů blob účet úložiště Azure úrovně standard.

![Na tomto snímku obrazovky jeden viděli, jak vypadá na portálu Azure](media/sap-hana-backup-file-level/image028.png)

Na tomto snímku obrazovky jeden můžete zobrazit, jak vypadá na portálu Azure. Kontejner objektů blob s názvem &quot;sap hana zálohování&quot; byl vytvořen a zahrnuje čtyři objekty BLOB, které představují záložní soubory SAP HANA. Jeden z nich má velikost přibližně 230 GB.

Konzole zálohování HANA Studio umožňuje jednu pro omezení maximální velikost HANA záložní soubory. V prostředí ukázka zvýšení výkonu tím, že je možné, že několik menších souborů zálohování, místo jednoho velkého souboru 230 GB.

![Nastavení omezení velikosti záložní soubor na nemá straně HANA&#39;t zlepšit čas zálohování](media/sap-hana-backup-file-level/image029.png)

Nastavení omezení velikosti záložní soubor na nemá straně HANA&#39;t zlepšit čas zálohování, protože soubory jsou zapsány postupně, jak je vidět na tomto obrázku. Limit velikosti souborů byla nastavena na 60 GB, proto zálohování vytvořit čtyři velké datové soubory místo 230 GB jeden soubor.

![K testování paralelismus nástroj blobxfer, byla maximální velikost souboru pro zálohování HANA pak nastavena na 15 GB](media/sap-hana-backup-file-level/image030.png)

K testování paralelismus blobxfer nástroje, maximální velikost souboru pro zálohování HANA pak byla nastavena na 15 GB, což vedlo 19 záložní soubory. Tato konfigurace začlenění dobu blobxfer zkopírujte 230 GB do Azure blob storage z 3000 sekund dolů 875 sekund.

Tento výsledek je z důvodu limit 60 MB/s pro zápis objektů blob Azure. Paralelismus prostřednictvím více objektů BLOB řeší problémové místo, ale je nevýhodou: zvýšení výkonu blobxfer nástroj a zkopírujte všechny tyto HANA záložní soubory do Azure blob storage dochází zatížení HANA virtuálního počítače a sítě. Operace systému HANA stane dopad.

## <a name="blob-copy-of-dedicated-azure-data-disks-in-backup-software-raid"></a>Objekt BLOB kopii vyhrazené Azure datové disky v zálohování softwaru diskového pole RAID

Na rozdíl od ručního virtuálních počítačů data zálohování disku v tomto přístupu jeden zálohovat všechny datové disky na virtuálním počítači k uložení celého zařízení SAP HANA data, včetně HANA neprotokoluje soubory a soubory konfigurace. Místo toho na nápad je mít vyhrazený softwaru diskového pole RAID s proložení napříč více dat Azure virtuální pevné disky pro ukládání úplné zálohování souboru SAP HANA. Jeden zkopíruje pouze tyto disky, které mají zálohování SAP HANA. Můžou snadno se uchovává se v vyhrazený účet úložiště pro zálohy HANA, nebo připojené k vyhrazená &quot;zálohování správu virtuálních počítačů&quot; pro další zpracování.

![Všechny virtuální pevné disky související se situací byly zkopírovány pomocí ** start-azurestorageblobcopy ** příkaz prostředí PowerShell](media/sap-hana-backup-file-level/image031.png)

Po dokončení zálohování na místní softwaru diskového pole RAID, byly zkopírovány všechny virtuální pevné disky související se situací pomocí **start-azurestorageblobcopy** příkaz prostředí PowerShell (najdete v části [Start-AzureStorageBlobCopy](/powershell/module/azure.storage/start-azurestorageblobcopy)). Jak ovlivňuje pouze systému souborů vyhrazené pro udržování záložní soubory, neexistují žádné pochybnostmi SAP HANA dat či protokolu konzistence souborů na disku. Výhodou tohoto příkazu je, že funguje při virtuální počítač zůstane online. Ujistěte se, že žádný proces zapíše do sady zálohování stripe, nezapomeňte odpojit před kopírováním objektů blob a připojit ji znovu později. Nebo jeden použít vhodný způsob k &quot;freeze&quot; systému souborů. Například prostřednictvím xfs\_freeze pro systém souborů XFS.

![Tento snímek obrazovky ukazuje seznam objektů BLOB v kontejneru virtuální pevné disky na portálu Azure](media/sap-hana-backup-file-level/image032.png)

Zobrazí seznam objektů BLOB v tomto snímku obrazovky &quot;virtuální pevné disky&quot; kontejneru na portálu Azure. Na snímku obrazovky vidíte pět virtuální pevné disky, které byly připojené k serveru SAP HANA virtuálních počítačů, která bude sloužit jako softwaru diskového pole RAID zachovat SAP HANA záložní soubory. Také ukazuje pět kopií, které byly provedeny prostřednictvím příkazu Kopírovat objekt blob.

![Pro účely testování byla připojena kopie disků RAID SAP HANA zálohovací software na aplikační server virtuálního počítače](media/sap-hana-backup-file-level/image033.png)

Pro účely testování byla připojena kopie disků RAID SAP HANA zálohovací software na aplikační server virtuálního počítače.

![Zkopíruje na aplikační server, virtuální počítač byl vypnut připojit disk](media/sap-hana-backup-file-level/image034.png)

Server aplikace virtuální počítač byl vypnut připojit disk zkopíruje. Po spuštění virtuálního počítače, disky a jednotky RAID byly zjištěny správně (připojené prostřednictvím UUID). Pouze přípojný bod nebyl nalezen, který se vytvořil prostřednictvím YaST dělicí metody. Kopie záložní soubor SAP HANA stala později viditelné na úrovni operačního systému.

## <a name="copy-sap-hana-backup-files-to-nfs-share"></a>Zkopírujte SAP HANA záložní soubory do sdílené složky NFS

Pokud chcete snížit potenciální dopad v systému SAP HANA z hlediska místa na disku nebo výkonu, jeden zvážit ukládání záložních souborů SAP HANA na sdílené složky NFS. Technicky funguje, ale znamená použití druhý virtuální počítač Azure jako hostitele sdílené složky NFS. Neměl by být malá velikost virtuálního počítače, z důvodu šířku pásma sítě virtuálních počítačů. Ho by smysl pak tento vypnout &quot;zálohování virtuálních počítačů&quot; a převeďte ho pouze pro provádění zálohování SAP HANA. Zápis v systému souborů NFS sdílené složky vloží zatížení v síti a má to dopad SAP HANA systému, ale jenom správu později na zálohování souborů &quot;zálohování virtuálních počítačů&quot; nebude vůbec ovlivnit systému SAP HANA.

![Sdílené složky NFS z jiného virtuálního počítače Azure byla připojena k serveru SAP HANA virtuálních počítačů](media/sap-hana-backup-file-level/image035.png)

Pokud chcete ověřit, že případ použití systému souborů NFS, sdílené složky NFS z jiného virtuálního počítače Azure byla připojena k serveru SAP HANA virtuálních počítačů. Došlo k dispozici žádné zvláštní systému souborů NFS ladění použít.

![Trvalo 1 hodinu a 46 minut provedete zálohování přímo](media/sap-hana-backup-file-level/image036.png)

Sdílené složky NFS byl sadu rychlého stripe jako ten, na serveru SAP HANA. Nicméně trvalo 1 hodinu a 46 minut zálohy přímo na místo 10 minut, pokud nastavíte zápis do místní stripe sdílené složky systému souborů NFS.

![Alternativním se mnohem rychlejší v 1 a hod](media/sap-hana-backup-file-level/image037.png)

Alternativní provádění zálohování do sady místní stripe a kopírování do sdílené složky NFS na úrovni operačního systému (jednoduchou **cp - avr** příkaz) nebyla mnohem rychlejší. Trvalo 1 a hod.

Proto funguje, ale nebyla výkonu vhodné pro testovací zálohování 230 GB. Aby vypadal i zhoršení pro více terabajtů.

## <a name="copy-sap-hana-backup-files-to-azure-file-service"></a>Zkopírujte záložní soubory SAP HANA do služby Azure souborů

Je možné připojit sdílenou složku Azure uvnitř virtuálního počítače Azure Linux. Článek [postup používání Azure File storage s Linuxem](../../../storage/files/storage-how-to-use-files-linux.md) obsahuje podrobné informace o tom, jak to provést. Uvědomte si, že je aktuálně 5 TB kvótu jednu sdílenou složku Azure a maximální velikost souboru 1 TB na soubor. V tématu [a cíle výkonnosti služby Azure Storage Scalability](../../../storage/common/storage-scalability-targets.md) informace o omezení úložiště.

Testy ukázaly, ale která zálohování není SAP HANA&#39;t aktuálně pracovní přímo s tímto typem CIFS připojení. Je také uvádí [1820529 Poznámka SAP](https://launchpad.support.sap.com/#/notes/1820529) , CIFS se nedoporučuje.

![Tento obrázek ukazuje chybu, v dialogovém okně Zálohování v SAP HANA Studio](media/sap-hana-backup-file-level/image038.png)

Následující obrázek ukazuje chybu v dialogovém okně Zálohování v nástroji SAP HANA Studio při pokusu o zálohovat přímo na připojený CIFS Azure sdílené složky. Proto je nutné nejprve provést standardní SAP HANA zálohování do systému souborů virtuálních počítačů a poté zkopírujte záložní soubory z ní do služby Azure souborů.

![Následující obrázek ukazuje, že trvalo sekundám 929 zkopírujte záložní soubory 19 SAP HANA](media/sap-hana-backup-file-level/image039.png)

Následující obrázek ukazuje, že trvalo sekundám 929 zkopírujte záložní soubory 19 SAP HANA s celkovou velikost přibližně 230 GB do Azure sdílené složky.

![Struktura adresáře zdroje ve virtuálním počítači SAP HANA byla zkopírována do Azure sdílené složky](media/sap-hana-backup-file-level/image040.png)

Na tomto snímku obrazovky jeden uvidí, že zdroj strukturu adresáře na virtuální počítač SAP HANA jste zkopírovali do sdílené složky Azure file: jeden adresář (hana\_zálohování\_fsl\_15 gb) a 19 jednotlivých záložní soubory.

Ukládání záložních souborů SAP HANA na soubory Azure může být zajímavé možnost v budoucnu, pokud přímo záloh souboru SAP HANA ho podporují. Nebo pokud bude možné soubory Azure připojení prostřednictvím systému souborů NFS a limit maximální kvóty je podstatně vyšší než 5 TB.

## <a name="next-steps"></a>Další postup
* [Příručce zálohování pro SAP HANA ve virtuálních počítačích Azure](sap-hana-backup-guide.md) nabízí přehled a informace o zahájení práce.
* [SAP HANA zálohování podle úložiště snímků](sap-hana-backup-storage-snapshots.md) popisuje zálohování možnosti založené na snímku úložiště.
* Další informace o vytvoření vysoké dostupnosti a plán pro zotavení po havárii SAP HANA v Azure (velké instance) naleznete v tématu [SAP HANA (velké instance) vysoké dostupnosti a zotavení po havárii v Azure](hana-overview-high-availability-disaster-recovery.md).
