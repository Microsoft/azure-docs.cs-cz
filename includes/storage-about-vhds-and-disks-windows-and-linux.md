---
title: zahrnout soubor
description: zahrnout soubor
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 04/09/2018
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: b4d208ca28f6287489f104ba4e2ea9696e7a1f58
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2018
---
## <a name="about-vhds"></a>Virtuální pevné disky

Virtuální pevné disky používané v Azure jsou soubory .vhd uložené jako objekty blob stránky v účtu úložiště úrovně Standard nebo Premium v Azure. Podrobnosti o objektech blob stránky najdete v tématu [Vysvětlení objektů blob bloku a objektů blob stránky](/rest/api/storageservices/Understanding-Block-Blobs--Append-Blobs--and-Page-Blobs/). Podrobnosti o službě Premium Storage najdete v článku [Vysoce výkonné úložiště úrovně Premium a virtuální počítače Azure](../articles/virtual-machines/windows/premium-storage.md).

Azure podporuje virtuální pevné disky s pevným formátem. Pevný formát logický disk lineárně rozvrhne v rámci souboru, takže posun disku o X je uložen na pozici objektu blob s posunem o X. Malé zápatí na konci objektu blob popisuje vlastnosti virtuálního pevného disku. Pevný formát často plýtvá místem, protože většina disků obsahuje velké nevyužité oblasti dat. Azure však ukládá soubory .vhd ve zhuštěném formátu, takže současně můžete těžit z výhod pevných i dynamických disků. Další podrobnosti najdete v tématu [Začínáme s virtuálními pevnými disky](https://technet.microsoft.com/library/dd979539.aspx).

Všechny soubory VHD v Azure, který chcete použít jako zdroj pro vytvoření disky nebo bitové kopie jsou jen pro čtení, s výjimkou souborů VHD nahrál nebo zkopírovat do úložiště Azure (může to být buď pro čtení a zápis, nebo jen pro čtení) uživatelem. Při vytváření disku nebo bitové kopie, Azure vytváří kopie zdrojové soubory VHD. Tyto kopie můžou být jen pro čtení nebo pro čtení a zápis, podle toho, jak virtuální pevný disk používáte.

Při vytváření virtuálního počítače z image Azure vytvoří pro virtuální počítač disk, který je kopií zdrojového souboru .vhd. Z důvodu ochrany před náhodným odstraněním Azure označí jako zapůjčený každý zdrojový soubor .vhd, který se používá k vytváření image, disku operačního systému nebo datového disku.

Před odstraněním zdrojového souboru .vhd bude nutné toto označení odebrat odstraněním disku nebo image. Pokud chcete odstranit soubor .vhd používaný některým virtuálním počítačem jako disk operačního systému, můžete najednou odstranit virtuální počítač, disk operačního systému a zdrojový soubor .vhd tak, že odstraníte virtuální počítač a všechny přidružené disky. Nicméně odstranění souboru .vhd, který je zdrojem pro datový disk, vyžaduje několik kroků v určitém pořadí. Nejprve odpojíte disk z virtuálního počítače, odstraníte disk a potom odstraníte soubor .vhd.
> [!WARNING]
> Pokud odstraníte zdrojový soubor .vhd z úložiště nebo pokud odstraníte účet úložiště, Microsoft pro vás tato data nebude moct obnovit.
> 
> Objekty BLOB stránky v Storage úrovně Premium jsou navrženy pro použití jako virtuální pevné disky pouze. Společnost Microsoft nedoporučuje ukládání dalších typů dat v objekty BLOB stránky v Storage úrovně Premium, jak může být výrazně větší náklady. Používejte objekty BLOB bloku pro ukládání dat, která není na disku VHD.

## <a name="types-of-disks"></a>Typy disků 

Disky Azure jsou navržené pro 99,999% dostupnost. Disky systému Azure konzistentně mít doručit odolnost podnikové úrovni s špičkový NULOVÉ % Annualized míra selhání.

Při vytváření disků si můžete vybrat ze dvou úrovní výkonu úložiště – Storage úrovně Standard a Premium Storage. Existují také dva typy disků, spravované a nespravované, které můžou být umístěné v obou úrovních výkonu.


### <a name="standard-storage"></a>Storage úrovně Standard 

Služba Storage úrovně Standard je založená na jednotkách HDD a poskytuje nákladově efektivní úložiště se zachováním výkonu. Službu Storage úrovně Standard je možné místně replikovat v jednom datacentru, nebo může být geograficky redundantní pomocí primárních a sekundárních datových center. Další informace o replikaci úložiště najdete v tématu [Replikace služby Azure Storage](../articles/storage/common/storage-redundancy.md). 

Další informace o používání služby Storage úrovně Standard s disky virtuálních počítačů najdete v tématu [Storage úrovně Standard a disky](../articles/virtual-machines/windows/standard-storage.md).

### <a name="premium-storage"></a>Premium Storage 

Služba Premium Storage je založená na jednotkách SSD a poskytuje podporu vysoce výkonných disků s nízkou latencí pro virtuální počítače, na kterých se spouští náročné vstupně-výstupní úlohy. Premium Storage můžete obvykle použít s velikostí, které zahrnují "s" v názvu řady. Například je Dv3-Series a Dsv3-series, Dsv3-series lze použít s Storage úrovně Premium.  Další informace najdete v článku [Premium Storage](../articles/virtual-machines/windows/premium-storage.md).

### <a name="unmanaged-disks"></a>Nespravované disky

Nespravované disky jsou tradičním typem disků, jaké používají virtuální počítače. U těchto disků si vytvoříte vlastní účet úložiště, který zadáte při vytváření disku. Je třeba zajistit, abyste do stejného účtu úložiště neumístili příliš mnoho disků, protože byste mohli překročit [cíle škálovatelnosti](../articles/storage/common/storage-scalability-targets.md) účtu úložiště (například 20 000 IOPS), což by vyústilo v omezení virtuálních počítačů. U nespravovaných disků musíte zjistit, jak maximalizovat využití jednoho nebo více účtů úložiště a získat tak ze svých virtuálních počítačů nejlepší výkon.

### <a name="managed-disks"></a>Managed Disks 

Služba Managed Disks se na pozadí stará o vytvoření a správu účtu úložiště za vás a zajišťuje, že si nemusíte dělat starosti s omezením škálovatelnosti účtu úložiště. Jednoduše zadáte velikost disku a úroveň výkonu (Standard nebo Premium) a Azure za vás disk vytvoří a postará se o jeho správu. S využitím úložiště si nemusíte dělat starosti ani v případě, že přidáváte disky nebo vertikálně navyšujete a snižujete kapacitu virtuálního počítače. 

V jednom účtu úložiště na oblast Azure můžete také spravovat vlastní image a vytvářet pomocí nich stovky virtuálních počítačů v rámci stejného předplatného. Další informace o službě Managed Disks najdete v tématu [Přehled služby Managed Disks](../articles/virtual-machines/windows/managed-disks-overview.md).

Doporučujeme pro nové virtuální počítače používat službu Azure Manager Disks, a abyste převedli výše uvedené nespravované disky na spravované disky a plně tak využili množství funkcí dostupných ve službě Managed Disks.

### <a name="disk-comparison"></a>Porovnání disků

Následující tabulka obsahuje porovnání úrovní Premium a Standard pro spravované a nespravované disky, které vám pomůže rozhodnout co použít.

|    | Disk Azure typu Premium | Disk Azure typu Standard |
|--- | ------------------ | ------------------- |
| Typ disku | SSD | HDD  |
| Přehled  | Založený na jednotkách SSD; poskytuje podporu vysoce výkonných disků s nízkou latencí pro virtuální počítače, na kterých se spouští náročné vstupně-výstupní úlohy nebo které hostují kriticky důležité produkční prostředí. | Založený na jednotkách HDD; poskytuje nákladově efektivní podporu pro scénáře vývoje nebo testování virtuálních počítačů. |
| Scénář  | Úlohy v produkčním prostředí a úlohy, u kterých záleží na výkonu | Vývoj a testování, nekritické úlohy, <br>úlohy s nepravidelným přístupem |
| Velikost disku | P4: 32 GB (pouze spravované disků)<br>P6: 64 GB (pouze spravované disků)<br>P10: 128 GB<br>P20: 512 GB<br>P30: 1 024 GB<br>P40: 2 048 GB<br>P50: 4095 GB | Nespravované disky: 1 GB – 4 TB (4095 GB) <br><br>Managed Disks:<br> S4: 32 GB <br>S6: 64 GB <br>S10: 128 GB <br>S20: 512 GB <br>S30: 1 024 GB <br>S40: 2 048 GB<br>S50: 4095 GB| 
| Maximální propustnost na disk | 250 MB/s | 60 MB/s | 
| Maximum vstupně-výstupních operací za sekundu (IOPS) na disk | 7500 IOPS | 500 IOPS | 

