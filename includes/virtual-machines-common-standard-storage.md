---
title: zahrnout soubor
description: zahrnout soubor
services: storage
author: yuemlu
ms.service: storage
ms.topic: include
ms.date: 06/05/2018
ms.author: yuemlu
ms.custom: include file
ms.openlocfilehash: 4e62342a32456787863da775ea98df178ab1d559
ms.sourcegitcommit: b7290b2cede85db346bb88fe3a5b3b316620808d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/05/2018
ms.locfileid: "34806294"
---
# <a name="cost-effective-standard-storage-and-unmanaged-and-managed-azure-vm-disks"></a>Nákladově efektivní úložiště Standard a nespravované a spravované disky virtuálních počítačů Azure

Azure Standard Storage nabízí podporu spolehlivou a cenově disků pro virtuální počítače spuštěné úlohy kterému latence nevadí. Podporuje také objekty BLOB, tabulky, fronty a soubory. S použitím standardního úložiště jsou data uložena v pevných disků (HDD). Při práci s virtuálními počítači, můžete použít standardní disky SSD a HDD pro scénáře vývoje/testování a méně důležité úlohy a prémiové disky SSD pro kritické výrobní aplikace. Standardní úložiště je k dispozici ve všech oblastech Azure. 

Tento článek se týká, používat standardní disky SSD a HDD. Další informace o použití úložiště s objekty BLOB, tabulky, fronty a souborů najdete v tématu [Úvod do Storage](../articles/storage/common/storage-introduction.md).

## <a name="disk-types"></a>Typy disků

Existují dva způsoby, jak vytvořit standardní disky pro virtuální počítače Azure:

**Nespravované disky**: Tento typ disku je původní metody, kde budete spravovat účty úložiště používá k ukládání souborů virtuálního pevného disku, které odpovídají disky virtuálních počítačů. Soubory VHD jsou uloženy jako objekty BLOB stránky v účtech úložiště. Nespravované disky lze připojit k libovolné velikosti virtuálního počítače Azure, včetně virtuálních počítačů, které především používat úložiště úrovně Premium, jako je například DSv2 a GS řady. Virtuální počítače Azure podporují připojení několik standardní disky, což až 256 TB úložiště na virtuální počítač.

[**Disky systému Azure spravované**](../articles/virtual-machines/windows/managed-disks-overview.md): Tato funkce spravuje účty úložiště pro vás použil pro disky virtuálních počítačů. Zadejte typ (Premium SSD, standardní SSD nebo standardní HDD) a velikost disku je nutné, a Azure vytváří a spravuje disku za vás. Nemusíte si dělat starosti o uvedení disky napříč více účtů úložiště, aby se zajistilo, zůstat v limitech škálovatelnosti pro účty úložiště – Azure zpracovává, který pro vás.

Přestože oba typy disků jsou k dispozici, doporučujeme používat spravované disky využívat výhod jejich řadu funkcí.

Chcete-li začít s Azure Standard Storage, navštivte [začněte zadarmo](https://azure.microsoft.com/pricing/free-trial/). 

Informace o tom, jak vytvořit virtuální počítač s spravované disky najdete v jednom z následujících článků.

* [Vytvoření virtuálního počítače pomocí Resource Manageru a PowerShellu](../articles/virtual-machines/windows/quick-create-powershell.md)
* [Vytvoření virtuálního počítače s Linuxem pomocí rozhraní příkazového řádku Azure CLI 2.0](../articles/virtual-machines/linux/quick-create-cli.md)

## <a name="standard-storage-features"></a>Funkce úložiště Standard Storage 

Podívejme se na některé funkce úložiště Standard Storage. Další podrobnosti najdete v tématu [Úvod do Azure Storage](../articles/storage/common/storage-introduction.md).

**Standardní úložiště**: Azure Standard Storage podporuje disky Azure, objektů BLOB služby Azure, Azure Files, Azure tabulek a front Azure. Použít službu pro standardní úložiště, začínat [vytvoření účtu úložiště Azure](../articles/storage/common/storage-create-storage-account.md#create-a-storage-account).

**Standardní disky SSD:** standardní SSD disků poskytují větší spolehlivost výkon než standardní HDD disky a jsou nyní k dispozici ve verzi preview. Další informace o dostupnosti oblast standardní SSD disků najdete v tématu [dostupnost v oblastech standardní SSD disků (Preview)](../articles/virtual-machines/windows/faq-for-disks.md#standard-ssds-azure-regions).

**Standardní disky HDD:** standardní HDD disky lze připojit ke všem virtuálním počítačům Azure včetně velikost řady virtuálních počítačů použít s Storage úrovně Premium například DSv2 a GS řady. Standardní HDD disku lze připojit pouze pro jeden virtuální počítač. Můžete však připojit nejméně jeden z těchto disků pro virtuální počítač, až počet maximální disků, které jsou definované pro velikost tohoto virtuálního počítače. V následující části na standardní úložiště škálovatelnost a cíle výkonnosti jsme popisují specifikace podrobněji.

**Objekt blob stránky standardní**: objekty BLOB standardní stránky jsou používané pro udržení trvalé disky pro virtuální počítače a také být přístup přímo prostřednictvím REST podobně jako ostatní typy objektů BLOB Azure. [Objekty BLOB stránek](/rest/api/storageservices/Understanding-Block-Blobs--Append-Blobs--and-Page-Blobs) jsou kolekce 512 bajtů stránky optimalizované pro náhodné čtení a zápisu operace. 

**Replikace úložiště:** ve většině oblastí, data ve standardní účet úložiště může být místně replikované nebo geograficky replikované v několika datových centrech. Čtyři typy replikace, které jsou k dispozici jsou místně redundantní úložiště (LRS), Zónově redundantní úložiště (ZRS), geograficky redundantní úložiště (GRS) a geograficky redundantní úložiště s přístupem pro čtení (RA-GRS). Spravované disků ve standardní úložiště aktuálně pouze podporují místně redundantní úložiště (LRS). Další informace najdete v tématu [replikace úložiště](../articles/storage/common/storage-redundancy.md).

## <a name="scalability-and-performance-targets"></a>Cíle škálovatelnost a výkonnosti

V této části popisují jsme škálovatelnost a výkon cíle, které je potřeba zvážit při použití standardní úložiště.

### <a name="account-limits--does-not-apply-to-managed-disks"></a>Limity účtu – nevztahuje na spravované disky

| **Prostředek** | **Výchozí omezení** |
|--------------|-------------------|
| TB na účet úložiště  | 500 TB |
| Příjem příchozích dat Max<sup>1</sup> na účet úložiště (nám oblastech) | 10 GB/s Pokud je povoleno GRS nebo ZRS, 20 GB/s pro LRS |
| Maximální počet odchozí<sup>1</sup> na účet úložiště (nám oblastech) | 20 GB/s Pokud je povoleno RA-GRS nebo GRS nebo ZRS, 30 GB/s pro LRS |
| Příjem příchozích dat Max<sup>1</sup> na účet úložiště (Evropské a asijské oblasti) | 5 GB/s Pokud je povoleno GRS nebo ZRS, 10 GB/s pro LRS |
| Maximální počet odchozí<sup>1</sup> na účet úložiště (Evropské a asijské oblasti) | 10 GB/s Pokud je povoleno RA-GRS nebo GRS nebo ZRS, 15 GB/s pro LRS |
| Celkový počet požadavků (za předpokladu, že velikost objektu 1 KB) na účet úložiště | Až 20 000 IOPS, entity za sekundu nebo zpráv za sekundu |

<sup>1</sup> příjem příchozích dat odkazuje na všech dat (počet požadavků) odesílány do účtu úložiště. Odchozí odkazuje na všechna data (odpovědí) přijímání z účtu úložiště.

Další informace najdete v tématu [a cíle výkonnosti služby Azure Storage Scalability](../articles/storage/common/storage-scalability-targets.md).

Pokud potřebám vaší aplikace přesahují cíle škálovatelnosti účtu jedno úložiště, sestavte aplikaci a používat více účtů úložiště oddílu data mezi různými tyto účty úložiště. Alternativně můžete spravovat disky Azure a Azure spravuje vytváření oddílů a umístění vašich dat za vás.

### <a name="standard-disks-limits"></a>Standardní disky omezení

Na rozdíl od prémiové disky nebyly zřízené vstupně výstupních operací za sekundu (IOPS) a propustnost (šířka pásma) standardní disky. Výkon standardní disky se liší podle velikosti virtuálního počítače, který je připojen na disk, nikoli při velikost disku. Může se stát, že byste měli dosáhnout až do limitu výkonu uvedené v následující tabulce.

**Standardní disky omezení (spravovaných a nespravovaných)**

| **Vrstvu virtuálního počítače**            | **Základní úroveň virtuálního počítače** | **Standardní vrstvy virtuálního počítače** |
|------------------------|-------------------|----------------------|
| Velikost disku maximální          | 4095 GB           | 4095 GB              |
| Max. 8 KB IOPS na disk | Až 300         | Až 500            |
| Maximální šířka pásma na disku | Až 60 MB/s     | Až 60 MB/s        |

Pokud vaše úlohy vyžadují podporu disků vysoce výkonné, nízkou latencí, měli byste zvážit použití služby Premium Storage. Pokud chcete vědět, další výhody úložiště Premium Storage, navštivte [vysoce výkonné úložiště Premium a disky virtuálních počítačů Azure](../articles/virtual-machines/windows/premium-storage.md). 

## <a name="snapshots-and-copy-blob"></a>Snímků a kopírování objektů blob

Soubor virtuálního pevného disku ke službě úložiště je objekt blob stránky. Můžete pořizovat snímky objekty BLOB stránky a zkopírujte je do jiného umístění, jako například jiný účet úložiště.

### <a name="unmanaged-disks"></a>Nespravované disky

Můžete vytvořit [přírůstkové snímky](../articles/virtual-machines/windows/incremental-snapshots.md) pro nespravovaná standardní disky stejným způsobem použít snímky s standardní úložiště. Doporučujeme vytvářet snímky a poté zkopírujte tyto snímky do geograficky redundantní standardní účet úložiště, pokud váš zdrojový disk je v účtu místně redundantní úložiště. Další informace najdete v tématu [možnosti redundance úložiště Azure](../articles/storage/common/storage-redundancy.md).

Pokud je disk připojen k virtuálnímu počítači, nejsou některé operace rozhraní API povolené na discích. Například nelze provést [kopírovat objekt Blob](/rest/api/storageservices/Copy-Blob) operaci u tohoto objektu blob, pokud je disk připojen k virtuálnímu počítači. Místo toho nejprve vytvořte snímek tomuto objektu blob pomocí [snímku Blob](/rest/api/storageservices/Snapshot-Blob) REST API metoda a potom proveďte [kopírovat objekt Blob](/rest/api/storageservices/Copy-Blob) snímku zkopírovat připojený disk. Alternativně můžete odpojit disk a potom proveďte všechny potřebné operace.

Udržovat geograficky redundantní kopie vašeho snímků, můžete zkopírovat snímky z účtu místně redundantní úložiště do geograficky redundantní standardní účet úložiště pomocí nástroje AzCopy nebo kopírovat objekt Blob. Další informace najdete v tématu [přenos dat pomocí nástroje příkazového řádku Azcopy](../articles/storage/common/storage-use-azcopy.md) a [kopírovat objekt Blob](/rest/api/storageservices/Copy-Blob).

Podrobné informace o provádění operací REST pro objekty BLOB stránky v účtech úložiště standard storage najdete v tématu [Azure Storage Services REST API](/rest/api/storageservices/Azure-Storage-Services-REST-API-Reference).

### <a name="managed-disks"></a>Managed Disks

Snímek se spravovaným diskem je jen pro čtení kopie spravovaného disku, který je uložený jako standardní se spravovaným diskem. Přírůstkové snímky nejsou aktuálně podporovány pro spravované disky, ale bude podporovaná v budoucnu.

Pokud se spravovaným diskem je připojen k virtuálnímu počítači, některé operace rozhraní API nejsou povoleny na discích. Například nelze vygenerovat sdílený přístupový podpis (SAS) k provedení operace kopírování, když je disk připojen k virtuálnímu počítači. Místo toho nejprve vytvořte snímek disku a pak proveďte kopírování snímku. Alternativně můžete odpojit disk a potom vygenerovat sdílený přístupový podpis (SAS) k provedení operace kopírování.

## <a name="pricing-and-billing"></a>Ceny a fakturace

Při použití standardní úložiště, platí následující fakturace aspekty:

* Velikost úložiště Standard storage nespravované disky nebo dat 
* Spravované disky úrovně Standard
* Standardní úložiště snímků
* Přenosy odchozích dat
* Transakce

**Nespravované velikost úložiště dat a disku:** pro nespravovaná disky a další data (objekty BLOB, tabulky, fronty a soubory), budou se vám účtovat pouze pro množství místa, kterou používáte. Například pokud máte virtuální počítač, jehož objekt blob stránky je zřízená jako 127 GB, ale virtuální počítač je ve skutečnosti jenom pomocí 10 GB místa, se vám účtuje 10 GB místa. Podporujeme až standardní úložiště 8191 GB a nespravované standardní disky až 4095 GB. 

**Spravované disky:** spravované disky se účtují zřízené velikosti. Pokud disk je zřízená jako 10 GB místa na disku a používáte pouze 5 GB, budou se vám účtovat zřídit velikosti 10 GB.

**Snímky**: snímky standardní disky se účtují dodatečnou kapacitu používané snímky. Informace o snímků, najdete v části [vytvoření snímku objektu Blob](/rest/api/storageservices/Creating-a-Snapshot-of-a-Blob).

**Odchozí přenosy dat**: [odchozí přenosy dat](https://azure.microsoft.com/pricing/details/data-transfers/) (data přejdete mimo datových center Azure) jsou zpoplatněné podle využití šířky pásma.

**Transakce**: Azure účtuje 0.0036 na 100 000 transakcí pro standardní úložiště. Transakcemi se rozumí operace čtení a zápisu u úložiště.

Podrobné informace o cenách pro standardní úložiště, virtuální počítače a spravovat disky najdete v části:

* [Azure Storage – ceny](https://azure.microsoft.com/pricing/details/storage/)
* [Ceny virtuálních počítačů](https://azure.microsoft.com/pricing/details/virtual-machines/)
* [Spravované disky ceny](https://azure.microsoft.com/pricing/details/managed-disks)

## <a name="azure-backup-service-support"></a>Podporu služby Azure Backup 

Virtuální počítače s nespravované disky lze zálohovat pomocí služby Azure Backup. [Další podrobnosti](../articles/backup/backup-azure-vms-first-look-arm.md).

K vytvoření úlohy zálohování se zálohy založené na čase, snadno obnovení virtuálních počítačů a zásady uchovávání záloh můžete použít taky služba Azure Backup s spravované disky. Můžete získat další informace v [služby pomocí zálohování Azure pro virtuální počítače s spravované disky](../articles/backup/backup-introduction-to-azure-backup.md#using-managed-disk-vms-with-azure-backup).

## <a name="next-steps"></a>Další postup

* [Seznámení se službou Azure Storage](../articles/storage/common/storage-introduction.md)

* [vytvořit účet úložiště](../articles/storage/common/storage-create-storage-account.md)

* [Přehled služby Managed Disks](../articles/virtual-machines/linux/managed-disks-overview.md)

* [Vytvoření virtuálního počítače pomocí Resource Manageru a PowerShellu](../articles/virtual-machines/windows/quick-create-powershell.md)

* [Vytvoření virtuálního počítače s Linuxem pomocí rozhraní příkazového řádku Azure CLI 2.0](../articles/virtual-machines/linux/quick-create-cli.md)
