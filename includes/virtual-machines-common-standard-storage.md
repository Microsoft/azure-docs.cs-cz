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
ms.openlocfilehash: ab085d6a5cb38c46cf46a51da6d294732e2fd879
ms.sourcegitcommit: 1b561b77aa080416b094b6f41fce5b6a4721e7d5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/17/2018
ms.locfileid: "45979256"
---
# <a name="cost-effective-standard-storage-and-unmanaged-and-managed-azure-vm-disks"></a>Nákladově efektivní úložiště úrovně Standard a spravované a nespravované disky virtuálních počítačů Azure

Azure Storage úrovně Standard poskytuje podporu spolehlivé, úsporné disků pro virtuální počítače běžící úlohu kterému latence nevadí. Také podporuje objekty BLOB, tabulky, fronty a soubory. Se standardním úložiště jsou data uložená na pevných disků (HDD). Při práci s virtuálními počítači, můžete SSD a HDD disky úrovně standard pro scénáře vývoje/testování a méně důležité úlohy a SSD disky úrovně premium pro důležité produkční aplikace. Storage úrovně Standard je k dispozici ve všech oblastech Azure. 

Tento článek se týká použití standardní disky SSD a HDD. Další informace o použití úložiště objektů BLOB, tabulky, fronty a soubory, naleznete v tématu [seznámení se službou Storage](../articles/storage/common/storage-introduction.md).

## <a name="disk-types"></a>Typy disků

Existují dva způsoby, jak vytvořit standardní disky pro virtuální počítače Azure:

**Nespravované disky**: Tento typ disku je původní metody, kde budete spravovat účty úložiště používané pro uložení souborů virtuálního pevného disku, které odpovídají na disky virtuálního počítače. Soubory virtuálního pevného disku se ukládají jako objekty BLOB stránky v účtech úložiště. Nespravované disky můžete připojit k libovolné velikosti virtuálního počítače Azure, včetně virtuálních počítačů, které se primárně používají službu Premium Storage, jako je řada DSv2 a GS. Virtuální počítače Azure podporují připojení několik standardních disků, což až 256 TB úložiště na každý virtuální počítač.

[**Služba Azure Managed Disks**](../articles/virtual-machines/windows/managed-disks-overview.md): Tato funkce spravuje účty úložiště používané pro disky virtuálních počítačů za vás. Zadejte typ (Premium SSD, SSD na úrovni Standard nebo standardní HDD) a velikost disku je nutné, a Azure vytvoří a spravuje disk za vás. Není nutné se starat o umisťováním disků ve více účtech úložiště, aby se vejdete do limitů škálovatelnosti pro účty úložiště – se postará Azure, který pro vás.

I když oba typy disků jsou k dispozici, doporučujeme použít Managed Disks a využít výhod jejich mnoho funkcí.

Začínáme s Azure Standard Storage, najdete v tématu [začít zdarma](https://azure.microsoft.com/pricing/free-trial/). 

Informace o tom, jak vytvořit virtuální počítač se spravovanými disky najdete v jednom z následujících článků.

* [Vytvoření virtuálního počítače pomocí Resource Manageru a PowerShellu](../articles/virtual-machines/windows/quick-create-powershell.md)
* [Vytvoření virtuálního počítače s Linuxem pomocí rozhraní příkazového řádku Azure CLI 2.0](../articles/virtual-machines/linux/quick-create-cli.md)

## <a name="standard-storage-features"></a>Funkce úložiště úrovně Standard 

Pojďme se podívat na některé z funkcí úložiště úrovně Standard. Další podrobnosti najdete v tématu [Úvod do služby Azure Storage](../articles/storage/common/storage-introduction.md).

**Storage úrovně standard**: úložiště Azure úrovně Standard podporuje disky Azure, Azure Blobs, soubory Azure, tabulky Azure a fronty Azure. Použití služby Storage úrovně Standard, začněte s [vytvoření účtu služby Azure Storage](../articles/storage/common/storage-quickstart-create-account.md).

**Standardní disky SSD:** disků SSD na úrovni Standard nabízí spolehlivější výkon než standardní HDD disky a jsou aktuálně k dispozici ve verzi preview. Další informace o dostupnosti oblast disků SSD na úrovni Standard najdete v tématu [regionální dostupnost disků SSD na úrovni Standard (Preview)](../articles/virtual-machines/windows/faq-for-disks.md#standard-ssds-azure-regions).

**Disky Standard HDD:** standardní HDD disků je možné připojit ke všem virtuálním počítačům Azure včetně velikost series virtuálních počítačů použít díky službě Premium Storage, jako je řada DSv2 a GS. Standardní pevného disku lze připojit pouze k jednomu virtuálnímu počítači. Ale můžete připojit jeden nebo více z těchto disků k virtuálnímu počítači, až do maximální disku počet definovaný pro velikost tohoto virtuálního počítače. V následující části na Standard Storage škálovatelnost a výkonnostní cíle popisujeme specifikace podrobněji.

**Objekt blob stránky standardní**: objekty BLOB standardní stránky se používají k uložení trvalé disky pro virtuální počítače a je přístupný také přímo prostřednictvím REST podobně jako ostatní typy objektů BLOB Azure. [Objekty BLOB stránky](/rest/api/storageservices/Understanding-Block-Blobs--Append-Blobs--and-Page-Blobs) jsou kolekce stránek 512 bajtů, které jsou optimalizované pro náhodné čtení a zápisu operace. 

**Replikace úložiště:** ve většině oblastí, data v účtu úložiště úrovně standard může být replikované místně nebo geograficky replikovaného napříč několika datových centrech. Čtyři typy replikace je k dispozici jsou místně redundantní úložiště (LRS), zónově redundantní úložiště (ZRS), geograficky redundantní úložiště (GRS) a geograficky redundantní úložiště přístup pro čtení (RA-GRS). Spravované disky v Storage úrovně Standard je místně redundantní úložiště (LRS) aktuálně podporují pouze. Další informace najdete v tématu [replikace úložiště mezi](../articles/storage/common/storage-redundancy.md).

## <a name="scalability-and-performance-targets"></a>Cíle škálovatelnost a výkonnosti

V této části popisujeme škálovatelnost a výkonnostní cíle, které je potřeba zvážit při použití úložiště úrovně standard.

### <a name="account-limits--does-not-apply-to-managed-disks"></a>Limity účtu – se nevztahují na spravované disky

| **Prostředek** | **Výchozí omezení** |
|--------------|-------------------|
| TB na jeden účet úložiště  | 500 TB |
| Maximální příchozí<sup>1</sup> jeden účet úložiště (nám oblasti) | 10 GB/s je-li povolena GRS/ZRS, 20 GB/s pro LRS |
| Maximální počet odchozího přenosu dat<sup>1</sup> jeden účet úložiště (nám oblasti) | 20 GB/s je-li povolena RA-GRS/GRS/ZRS, 30 GB/s pro LRS |
| Maximální příchozí<sup>1</sup> jeden účet úložiště (Evropské a oblasti Asie) | 5 GB/s je-li povolena GRS/ZRS, 10 GB/s pro LRS |
| Maximální počet odchozího přenosu dat<sup>1</sup> jeden účet úložiště (Evropské a oblasti Asie) | 10 GB/s je-li povolena RA-GRS/GRS/ZRS, 15 GB/s pro LRS |
| Celkový počet žádostí rychlost (za předpokladu, že velikost objektu 1 KB) jeden účet úložiště | Až 20 000 IOPS, entity za sekundu nebo zpráv za sekundu |

<sup>1</sup> příchozího přenosu dat odkazuje na všechna data (požadavky) odesílané do účtu úložiště. Výchozí přenos dat se odkazuje na všechna data (požadavky) přijímané z účtu úložiště.

Další informace najdete v tématu [Azure Storage škálovatelnost a cíle výkonnosti](../articles/storage/common/storage-scalability-targets.md).

Potřeb svojí aplikace překročení cíle škálovatelnosti z jednoho účtu úložiště, sestavení aplikace pro použití více účtů úložiště a data rozdělit mezi tyto účty úložiště. Alternativně je možné Azure Managed Disks a vytváření oddílů a umístění dat pro vás spravuje Platforma Azure.

### <a name="standard-disks-limits"></a>Disky Standard omezení

Na rozdíl od prémiové disky nebyly zřízeny vstupně výstupní operace za sekundu (IOPS) a propustnost (šířka pásma) disky Standard. Výkon standardní disky se liší podle velikosti virtuálního počítače, na který je připojený disk, nikoli na velikosti disku. Očekáváte může dosáhnout až do limitu výkonu uvedené v následující tabulce.

**Omezení standardní disky (spravované a nespravované)**

| **Úroveň virtuálního počítače**            | **Virtuální počítač na základní úrovni** | **Standardní úroveň virtuálního počítače** |
|------------------------|-------------------|----------------------|
| Velikost disku Max          | 4095 GB           | 4095 GB              |
| Maximálně 8 KB IOPS na disk | Až 300         | Až 500            |
| Maximální šířka pásma na disku | Až 60 MB/s     | Až 60 MB/s        |

Pokud vaše úloha vyžaduje podporu vysoce výkonných disků s nízkou latencí, zvažte použití služby Premium Storage. Už víte, další výhody služby Premium Storage, najdete v tématu [vysoce výkonné úložiště úrovně Premium a disky virtuálních počítačů Azure](../articles/virtual-machines/windows/premium-storage.md). 

## <a name="snapshots-and-copy-blob"></a>Snímků a kopírování objektů blob

Na službu úložiště souboru virtuálního pevného disku je objekt blob stránky. Můžete pořizovat snímky objektů BLOB stránky a zkopírujte je do jiného umístění, jako je například jiný účet úložiště.

### <a name="unmanaged-disks"></a>Nespravované disky

Můžete vytvořit [přírůstkových snímků](../articles/virtual-machines/windows/incremental-snapshots.md) pro nespravované disky standard stejným způsobem pomocí snímků úložiště úrovně Standard. Doporučujeme vytvářet snímky a pak zkopírujte tyto snímky účet geograficky redundantního úložiště úrovně standard, pokud je zdrojový disk v účet místně redundantního úložiště. Další informace najdete v tématu [možnosti redundance Azure Storage](../articles/storage/common/storage-redundancy.md).

Pokud disk připojen k virtuálnímu počítači, nejsou povolené určité operace rozhraní API. na discích. Například nelze provést [objekt Blob kopírování](/rest/api/storageservices/Copy-Blob) operace tohoto objektu blob, dokud je disk připojen k virtuálnímu počítači. Místo toho nejprve vytvořit snímek tohoto objektu blob pomocí [pořízení snímku objektu Blob](/rest/api/storageservices/Snapshot-Blob) rozhraní REST API metody a pak proveďte [objekt Blob kopírování](/rest/api/storageservices/Copy-Blob) snímku kopírování připojený disk. Alternativně můžete odpojit disk a potom proveďte všechny potřebné operace.

Zachování geograficky redundantní kopie vašich snímky, můžete zkopírovat snímky z účtů místně redundantního úložiště na účet geograficky redundantního úložiště úrovně standard s využitím AzCopy nebo objekt Blob kopírování. Další informace najdete v tématu [přenos dat pomocí nástroje příkazového řádku Azcopy](../articles/storage/common/storage-use-azcopy.md) a [objekt Blob kopírování](/rest/api/storageservices/Copy-Blob).

Podrobné informace o provádění operací REST k objektům BLOB stránky v účtech úložiště úrovně standard najdete v tématu [Azure Storage Services REST API](/rest/api/storageservices/Azure-Storage-Services-REST-API-Reference).

### <a name="managed-disks"></a>Managed Disks

Snímek spravovaného disku je jen pro čtení kopie spravovaného disku, který je uložený jako standardní spravovaný disk. Přírůstkových snímků nejsou aktuálně podporovány pro Managed Disks, ale bude podporovat v budoucnu.

Pokud spravovaný disk připojen k virtuálnímu počítači, nejsou povolené určité operace rozhraní API. na discích. Například nelze vygenerovat sdílený přístupový podpis (SAS) k provedení operace kopírování, zatímco disk připojen k virtuálnímu počítači. Místo toho nejprve vytvořte snímek disku a pak proveďte kopírování snímku. Alternativně můžete odpojit disk a pak vygenerovat sdílený přístupový podpis (SAS) k provedení této operace kopírování.

## <a name="pricing-and-billing"></a>Ceny a fakturace

Pokud používáte Storage úrovně Standard, platí následující aspekty fakturace:

* Velikost úložiště úrovně Standard nespravované disky/dat 
* Spravované disky úrovně Standard
* Snímky úložiště úrovně Standard
* Přenosy odchozích dat
* Transakce

**Nespravovaného úložiště dat a velikost disku:** pro nespravované disky a další data (objekty BLOB, tabulky, fronty a soubory), bude se vám účtovat jenom množství místa, kterou používáte. Například pokud máte virtuální počítač, jehož objektů blob stránky je zřízená jako 127 GB, ale virtuální počítač je ve skutečnosti pouze pomocí 10 GB místa, se účtují za 10 GB místa. Storage úrovně Standard podporuje až 8191 GB a standardní nespravované disky až 4 095 GB. 

**Spravované disky:** fakturace za spravované disky úrovně standard závisí na velikosti zřízeného disku. Zřízená velikost (zaokrouhluje nahoru) Azure mapuje na nejbližší možnost Managed Disks uvedená v následujících tabulkách. Každý spravovaný disk mapuje na jednu z podporovaných velikostí zřízené a účtuje se odpovídajícím způsobem. Například pokud vytvoříte standardní spravovaný disk a zadejte zřízená velikost 200 GB, účtuje se vám ceníku typ s.15 disku.

| **Standardní HDD spravované <br>typ disku** | **S4** | **S6** | **S10** | **S.15** | **S20** | **S30** | **S40** | **S50** |
|------------------|---------|---------|--------|--------|--------|----------------|----------------|----------------| 
| Velikost disku        | 32 GiB  | 64 GiB  | 128 GiB | 256 GiB | 512 GiB | 1024 giB (1 TB) | 2048 giB (2 TB) | 4095 GiB (4 TiB) | 


**Snímky**: snímky disků standard se účtují další zvýšení kapacity používané snímky. Informace o snímků, najdete v části [vytvoření snímku objektu Blob](/rest/api/storageservices/Creating-a-Snapshot-of-a-Blob).

**Přenosy odchozích dat**: [přenosy odchozích dat](https://azure.microsoft.com/pricing/details/data-transfers/) (data odcházející z Datacenter Azure) jsou zpoplatněné využití šířky pásma.

**Transakce**: Azure účtuje 0.0036 na 100 000 transakcí pro úložiště úrovně standard. Transakcemi se rozumí operace čtení a zápisu u úložiště.

Podrobné informace o cenách pro úložiště úrovně Standard Virtual Machines a Managed Disks najdete tady:

* [Ceny za Azure Storage](https://azure.microsoft.com/pricing/details/storage/)
* [Ceny Virtual Machines](https://azure.microsoft.com/pricing/details/virtual-machines/)
* [Ceny služby Managed Disks](https://azure.microsoft.com/pricing/details/managed-disks)

## <a name="azure-backup-service-support"></a>Podpora služby Azure Backup 

Virtuální počítače s nespravovanými disky můžete zálohovat pomocí služby Azure Backup. [Další podrobnosti](../articles/backup/backup-azure-vms-first-look-arm.md).

Také vám pomůže služba Azure Backup se službou Managed Disks vytváření úlohy zálohování s časovou synchronizací zálohy, snadné obnovení virtuálního počítače a zásady uchovávání záloh. Další informace o tomto na [služby pomocí Azure Backup pro virtuální počítače se spravovanými disky](../articles/backup/backup-introduction-to-azure-backup.md#using-managed-disk-vms-with-azure-backup).

## <a name="next-steps"></a>Další postup

* [Seznámení se službou Azure Storage](../articles/storage/common/storage-introduction.md)

* [vytvořit účet úložiště](../articles/storage/common/storage-quickstart-create-account.md)

* [Přehled služby Managed Disks](../articles/virtual-machines/linux/managed-disks-overview.md)

* [Vytvoření virtuálního počítače pomocí Resource Manageru a PowerShellu](../articles/virtual-machines/windows/quick-create-powershell.md)

* [Vytvoření virtuálního počítače s Linuxem pomocí rozhraní příkazového řádku Azure CLI 2.0](../articles/virtual-machines/linux/quick-create-cli.md)
