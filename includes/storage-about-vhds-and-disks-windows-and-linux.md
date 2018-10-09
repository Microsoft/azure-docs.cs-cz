---
title: zahrnout soubor
description: zahrnout soubor
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 04/09/2018
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 796614a9e60ccb7ab155bc986b39c04c781224e8
ms.sourcegitcommit: 0bb8db9fe3369ee90f4a5973a69c26bff43eae00
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/08/2018
ms.locfileid: "48874105"
---
## <a name="about-vhds"></a>Virtuální pevné disky

Virtuální pevné disky používané v Azure jsou soubory .vhd uložené jako objekty blob stránky v účtu úložiště úrovně Standard nebo Premium v Azure. Podrobnosti o objektech blob stránky najdete v tématu [Vysvětlení objektů blob bloku a objektů blob stránky](/rest/api/storageservices/Understanding-Block-Blobs--Append-Blobs--and-Page-Blobs/). Podrobnosti o službě Premium Storage najdete v článku [Vysoce výkonné úložiště úrovně Premium a virtuální počítače Azure](../articles/virtual-machines/windows/premium-storage.md).

Azure podporuje virtuální pevné disky s pevným formátem. Pevný formát logický disk lineárně rozvrhne v rámci souboru, takže posun disku o X je uložen na pozici objektu blob s posunem o X. Malé zápatí na konci objektu blob popisuje vlastnosti virtuálního pevného disku. Odpad pevného formátu místo často, protože většina disků obsahuje velké nevyužité oblasti v nich. Azure však ukládá soubory .vhd ve zhuštěném formátu, takže současně můžete těžit z výhod pevných i dynamických disků. Další informace najdete v tématu [Začínáme s virtuálními pevnými disky](https://technet.microsoft.com/library/dd979539.aspx).

Všechny soubory virtuálního pevného disku v Azure, který chcete použít jako zdroj pro vytvoření disků nebo imagí jsou jen pro čtení, s výjimkou souborů .vhd nahraje nebo zkopírovat do úložiště Azure podle uživatele (která může být čtení a zápis nebo jen pro čtení). Při vytváření disku nebo image Azure vytváří kopie zdrojové soubory .vhd. Tyto kopie můžou být jen pro čtení nebo pro čtení a zápis, podle toho, jak virtuální pevný disk používáte.

Při vytváření virtuálního počítače z image Azure vytvoří pro virtuální počítač disk, který je kopií zdrojového souboru .vhd. Z důvodu ochrany před náhodným odstraněním Azure označí jako zapůjčený každý zdrojový soubor .vhd, který se používá k vytváření image, disku operačního systému nebo datového disku.

Před odstraněním zdrojového souboru .vhd bude nutné toto označení odebrat odstraněním disku nebo image. Pokud chcete odstranit soubor .vhd používaný některým virtuálním počítačem jako disk operačního systému, můžete najednou odstranit virtuální počítač, disk operačního systému a zdrojový soubor .vhd tak, že odstraníte virtuální počítač a všechny přidružené disky. Nicméně odstranění souboru .vhd, který je zdrojem pro datový disk, vyžaduje několik kroků v určitém pořadí. Nejprve odpojíte disk z virtuálního počítače, odstraníte disk a potom odstraníte soubor .vhd.

> [!WARNING]
> Pokud odstraníte zdrojový soubor .vhd z úložiště nebo pokud odstraníte účet úložiště, Microsoft pro vás tato data nebude moct obnovit.

## <a name="types-of-disks"></a>Typy disků

Disky Azure jsou navržené pro 99,999% dostupnost. Disky Azure konzistentně dodá odolnost na podnikové úrovni, se špičkovou NULOVOU % Annualized chybovost.

Existují tři úrovní výkonu úložiště, ze kterých si můžete vybrat při vytváření disků – Premium SSD disky SSD na úrovni Standard a Storage úrovně Standard pevný disk. Navíc existují dva typy disků – nespravované a spravované.

### <a name="standard-hdd-disks"></a>Disky Standard pevný disk

Standardní HDD disky jsou založená na jednotkách HDD a poskytování nákladově efektivních úložišť. Storage úrovně Standard pevný disk je možné místně replikovat v jednom datacentru, nebo být geograficky redundantní pomocí primárních a sekundárních datových center. Další informace o replikaci úložiště najdete v tématu [replikace Azure Storage](../articles/storage/common/storage-redundancy.md).

Další informace o použití standardních pevných disků, naleznete v tématu [Storage úrovně Standard a disky](../articles/virtual-machines/windows/standard-storage.md).

### <a name="standard-ssd-disks"></a>Standardní disky SSD

Standardní disky SSD jsou navrženy pro adresu stejný druh úlohy jako standardní HDD disky, ale nabízejí konzistentní výkon a spolehlivost než pevný disk. Standardní disky SSD kombinovat elementy disky Premium SSD a HDD standardní disky do formuláře nejlepší nákladově efektivní řešení vhodné pro aplikace, jako jsou webové servery, které nevyžadují vysoký počet IOPS na disky. Pokud je k dispozici, jsou disky SSD na úrovni Standard možnost doporučujeme pro většinu úloh. Standardní disky SSD jsou k dispozici jako Managed Disks ve všech oblastech, ale jsou aktuálně k dispozici pouze s typem odolnosti proti chybám místně redundantní úložiště (LRS).

### <a name="premium-ssd-disks"></a>SSD disky úrovně Premium

SSD disky Premium jsou založená na jednotkách SSD a poskytuje podporu vysoce výkonných disků s nízkou latencí pro virtuální počítače, které můžu intenzivních vstupně-výstupních operací. Můžete obvykle použít disky Premium SSD s velikostí, mezi které patří "s" v názvu řady. Například je Dv3-Series a řady Dsv3-series, Dsv3-series je možné s disky Premium SSD.  Další informace najdete v článku [Premium Storage](../articles/virtual-machines/windows/premium-storage.md).

### <a name="unmanaged-disks"></a>Nespravované disky

Nespravované disky jsou tradičním typem disků, jaké používají virtuální počítače. S těmito disky vytvořte si vlastní účet úložiště a zadat Tento účet úložiště při vytváření disku. Ujistěte se, neumisťujte příliš mnoho disků ve stejném účtu úložiště, protože by mohla překročit [cíle škálovatelnosti](../articles/storage/common/storage-scalability-targets.md) účtu služby storage (20 000 IOPS, například), výsledkem je omezení ze strany virtuálních počítačů. U nespravovaných disků musíte zjistit, jak maximalizovat využití jednoho nebo více účtů úložiště a získat tak ze svých virtuálních počítačů nejlepší výkon.

### <a name="managed-disks"></a>Managed Disks

Služba Managed Disks se na pozadí stará o vytvoření a správu účtu úložiště za vás a zajišťuje, že si nemusíte dělat starosti s omezením škálovatelnosti účtu úložiště. Jednoduše zadáte velikost disku a úroveň výkonu (Standard nebo Premium) a Azure za vás disk vytvoří a postará se o jeho správu. S využitím úložiště si nemusíte dělat starosti ani v případě, že přidáváte disky nebo vertikálně navyšujete a snižujete kapacitu virtuálního počítače.

V jednom účtu úložiště na oblast Azure můžete také spravovat vlastní image a vytvářet pomocí nich stovky virtuálních počítačů v rámci stejného předplatného. Další informace o spravovaných discích najdete v tématu [Přehled služby Managed Disks](../articles/virtual-machines/windows/managed-disks-overview.md).

Doporučujeme pro nové virtuální počítače používat službu Azure Manager Disks, a abyste převedli výše uvedené nespravované disky na spravované disky a plně tak využili množství funkcí dostupných ve službě Managed Disks.

### <a name="disk-comparison"></a>Porovnání disků

Následující tabulka obsahuje porovnání standardní HDD, SSD na úrovni Standard a Premium SSD pro spravované a nespravované disky vám pomůže rozhodnout co použít. Velikosti označený hvězdičkou jsou aktuálně ve verzi preview.

|    | Disk Azure typu Premium |Disk Azure SSD na úrovni Standard | Azure Standard pevného disku
|--- | ------------------ | ------------------------------- | -----------------------
| Typ disku | SSD | SSD | HDD  
| Přehled  | Založený na jednotkách SSD; poskytuje podporu vysoce výkonných disků s nízkou latencí pro virtuální počítače, na kterých se spouští náročné vstupně-výstupní úlohy nebo které hostují kriticky důležité produkční prostředí. |Konzistentní výkon a spolehlivost než pevný disk. Optimalizované pro úlohy s nízkou – IOPS| Nákladově efektivní disku založeného na pevný disk pro úlohy s řídkým přístupem
| Scénář  | Úlohy v produkčním prostředí a úlohy, u kterých záleží na výkonu |Webové servery, málo používaná podnikové aplikace a pro vývoj/testování| Zálohování, Nekritická, úlohy s Řídkým přístupem
| Velikost disku | P4: 32 GiB (pouze spravované disky)<br>P6: 64 GiB (pouze spravované disky)<br>P10: 128 GB<br>P15: 256 GB (pouze spravované disky)<br>P20: 512 GB<br>P30: 1024 GB<br>P40: 2048 GiB<br>P50: 4095 GiB<br>P60: 8 192 GiB * (8 TB)<br>P70: 16 384 GB * (16 TB)<br>P80: 32 767 GiB * (32 TB) |Jenom spravované disky:<br>E10: 128 GB<br>E15: 256 GB<br>E20: 512 GB<br>E30: 1024 GB<br>E40: 2048 GiB<br>E50: 4095 GiB<br>E60: 8 192 GiB * (8 TB)<br>E70: 16 384 GB * (16 TB)<br> E80: 32 767 GiB * (32 TB) | Nespravované disky: 1 GiB – 4 TB (4095 GiB) <br><br>Managed Disks:<br> S4: 32 GiB <br>S6: 64 GiB <br>S10: 128 GB <br>S.15: 256 GB <br>S20: 512 GB <br>S30: 1024 GB <br>S40: 2048 GiB<br>S50: 4095 GiB<br>S60: 8 192 GiB * (8 TB)<br>S70: 16 384 GiB * (16 TB)<br>S80: 32,384 GiB * (32 TB)
| Maximální propustnost na disk | P4: 25 MiB/s<br> P6: 50 MiB/s<br> P10: 100 MiB/s<br> P15: 200 MiB/s<br> P20: 150 MiB/s<br> P30: 200 MiB/s<br> P40 P50: 250 MiB/s<br> P60: 480 MiB/s<br> P70 P80: 750 MiB/s | E10 E50: až 60 MiB/s<br> E60: až 300 MiB/s *<br> E70 E80: 500 MiB/s *| S4 - S50: Upt o 60 MiB/s<br> S60: až 300 MiB/s *<br> S70 S80: až 500 MiB/s *
| Maximum vstupně-výstupních operací za sekundu (IOPS) na disk | P4: 120 VSTUPNĚ-VÝSTUPNÍCH OPERACÍ<br> P6: 240 VSTUPNĚ-VÝSTUPNÍCH OPERACÍ<br> P10: 500 IOPS<br> P15: IOPS 1100<br> P20: IOPS 2300<br> P30: 5000 VSTUPNĚ-VÝSTUPNÍCH OPERACÍ<br> P40 P50: 7500 VSTUPNĚ-VÝSTUPNÍCH OPERACÍ<br> P60: 12 500 IOPS *<br> P70: 15 000 VSTUPNĚ-VÝSTUPNÍCH OPERACÍ *<br> P80: 20 000 IOPS * | E10 E50: až 500 IOPS<br> E60: až 1300 vstupně-výstupních operací *<br> E70 E80: až 2000 vstupně-výstupních operací * | S4 S50: až 500 IOPS<br> S60: až 1300 vstupně-výstupních operací *<br> S70 S80: až 2000 vstupně-výstupních operací *
