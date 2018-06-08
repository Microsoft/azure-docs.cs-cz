---
title: zahrnout soubor
description: zahrnout soubor
services: storage
author: rogara
ms.service: storage
ms.topic: include
ms.date: 04/09/2018
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: f64645db782b055e1c544f257149411f29fc99d7
ms.sourcegitcommit: 3c3488fb16a3c3287c3e1cd11435174711e92126
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/08/2018
ms.locfileid: "34806311"
---
## <a name="about-vhds"></a>Virtuální pevné disky
Virtuální pevné disky používané v Azure jsou soubory .vhd uložené jako objekty blob stránky v účtu úložiště úrovně Standard nebo Premium v Azure. Podrobnosti o objektech blob stránky najdete v tématu [Vysvětlení objektů blob bloku a objektů blob stránky](/rest/api/storageservices/Understanding-Block-Blobs--Append-Blobs--and-Page-Blobs/). Podrobnosti o službě Premium Storage najdete v článku [Vysoce výkonné úložiště úrovně Premium a virtuální počítače Azure](../articles/virtual-machines/windows/premium-storage.md).

Azure podporuje virtuální pevné disky s pevným formátem. Pevný formát logický disk lineárně rozvrhne v rámci souboru, takže posun disku o X je uložen na pozici objektu blob s posunem o X. Malé zápatí na konci objektu blob popisuje vlastnosti virtuálního pevného disku. Formát odpady místo často, protože většina disky mají velké nepoužívané oblasti v nich. Azure však ukládá soubory .vhd ve zhuštěném formátu, takže současně můžete těžit z výhod pevných i dynamických disků. Další informace najdete v tématu [Začínáme s virtuálními pevnými disky](https://technet.microsoft.com/library/dd979539.aspx).

Všechny soubory virtuálního pevného disku v Azure, který chcete použít jako zdroj pro vytvoření disky nebo bitové kopie jsou jen pro čtení, s výjimkou souborů VHD nahrál nebo zkopírovat do úložiště Azure (může to být buď pro čtení a zápis, nebo jen pro čtení) uživatelem. Při vytváření disku nebo bitové kopie, Azure vytváří kopie zdrojové soubory VHD. Tyto kopie můžou být jen pro čtení nebo pro čtení a zápis, podle toho, jak virtuální pevný disk používáte.

Při vytváření virtuálního počítače z image Azure vytvoří pro virtuální počítač disk, který je kopií zdrojového souboru .vhd. Z důvodu ochrany před náhodným odstraněním Azure označí jako zapůjčený každý zdrojový soubor .vhd, který se používá k vytváření image, disku operačního systému nebo datového disku.

Před odstraněním zdrojového souboru .vhd bude nutné toto označení odebrat odstraněním disku nebo image. Pokud chcete odstranit soubor .vhd používaný některým virtuálním počítačem jako disk operačního systému, můžete najednou odstranit virtuální počítač, disk operačního systému a zdrojový soubor .vhd tak, že odstraníte virtuální počítač a všechny přidružené disky. Nicméně odstranění souboru .vhd, který je zdrojem pro datový disk, vyžaduje několik kroků v určitém pořadí. Nejprve odpojíte disk z virtuálního počítače, odstraníte disk a potom odstraníte soubor .vhd.

> [!WARNING]
> Pokud odstraníte zdrojový soubor .vhd z úložiště nebo pokud odstraníte účet úložiště, Microsoft pro vás tato data nebude moct obnovit.

## <a name="types-of-disks"></a>Typy disků 
Disky Azure jsou navržené pro 99,999% dostupnost. Disky systému Azure konzistentně mít doručit odolnost podnikové úrovni s špičkový NULOVÉ % Annualized míra selhání.

Existují tři úrovně výkonu pro úložiště, ze kterých si můžete vybrat při vytváření vaše disky – prémiové disky SSD, standardní SSD (Preview) a standardní HDD úložiště. Navíc existují dva typy disků – nespravované a spravované.

### <a name="standard-hdd-disks"></a>Standardní HDD disky
Standardní HDD disky jsou zajišťované pevné disky a poskytování nákladově efektivní úložiště. Standardní HDD úložiště je možné replikovat místně v datacentru jednu, nebo být geograficky redundantní s primární a sekundární datových centrech. Další informace o replikaci úložiště najdete v tématu [replikace Azure Storage](../articles/storage/common/storage-redundancy.md). 

Další informace o používání standardní HDD disků najdete v tématu [standardní úložiště a disky](../articles/virtual-machines/windows/standard-storage.md).

### <a name="standard-ssd-disks-preview"></a>Standardní disky SSD (preview)
Standardní disky SSD jsou navrženy pro stejný druh úloh jako standardní HDD disky adresa, ale nabízejí konzistentní výkon a spolehlivost než pevný disk. Standardní disky SSD zkombinovat elementy Premium SSD disků a standardní HDD disky na formulář nejlepší nákladově efektivní řešení, které jsou vhodné pro aplikace, jako jsou webové servery, které nepotřebují vysokou IOPS na discích. Pokud je k dispozici standardní SSD disky jsou doporučené nasazení možnost pro většinu úloh. Standardní disky SSD jsou dostupné jen jako spravované disky a při ve verzi preview jsou k dispozici pouze v [vyberte oblasti](../articles/virtual-machines/windows/faq-for-disks.md) a s typem odolnosti místně redundantní úložiště (LRS).

### <a name="premium-ssd-disks"></a>Pro prémiové disky SSD 
Disky SSD Premium jsou zajišťované disky SSD a nabízí podporu vysoce výkonné, nízkou latencí disků pro virtuální počítače spuštěné úlohy I náročnými. Obvykle můžete Premium SSD disky s velikostí, které zahrnují "s" v názvu řady. Například je Dv3-Series a Dsv3-series, Dsv3-series lze použít s Premium SSD disky.  Další informace najdete v článku [Premium Storage](../articles/virtual-machines/windows/premium-storage.md).

### <a name="unmanaged-disks"></a>Nespravované disky
Nespravované disky jsou tradičním typem disků, jaké používají virtuální počítače. S těmito disky vytvoření účtu úložiště a zadejte tento účet úložiště při vytváření disku. Ujistěte se, neuvádějte příliš mnoho disků ve stejném účtu úložiště, protože může být vyšší než [cíle škálovatelnosti](../articles/storage/common/storage-scalability-targets.md) účtu úložiště (20 000 IOPS, např.), což ve virtuálních počítačích omezené. U nespravovaných disků musíte zjistit, jak maximalizovat využití jednoho nebo více účtů úložiště a získat tak ze svých virtuálních počítačů nejlepší výkon.

### <a name="managed-disks"></a>Managed Disks 
Služba Managed Disks se na pozadí stará o vytvoření a správu účtu úložiště za vás a zajišťuje, že si nemusíte dělat starosti s omezením škálovatelnosti účtu úložiště. Jednoduše zadáte velikost disku a úroveň výkonu (Standard nebo Premium) a Azure za vás disk vytvoří a postará se o jeho správu. S využitím úložiště si nemusíte dělat starosti ani v případě, že přidáváte disky nebo vertikálně navyšujete a snižujete kapacitu virtuálního počítače. 

V jednom účtu úložiště na oblast Azure můžete také spravovat vlastní image a vytvářet pomocí nich stovky virtuálních počítačů v rámci stejného předplatného. Další informace o spravovaných discích najdete v tématu [Přehled služby Managed Disks](../articles/virtual-machines/windows/managed-disks-overview.md).

Doporučujeme pro nové virtuální počítače používat službu Azure Manager Disks, a abyste převedli výše uvedené nespravované disky na spravované disky a plně tak využili množství funkcí dostupných ve službě Managed Disks.

### <a name="disk-comparison"></a>Porovnání disků
Následující tabulka obsahuje porovnání standardní HDD, standardní SSD a Premium SSD disků a nespravované, které vám pomůžou rozhodnout se, jak používat.

|    | Disk Azure typu Premium |Azure SSD disků na úrovni Standard (preview)| Disk Azure standardní pevný disk 
|--- | ------------------ | ------------------------------- | ----------------------- 
| Typ disku | SSD | SSD | HDD  
| Přehled  | Založený na jednotkách SSD; poskytuje podporu vysoce výkonných disků s nízkou latencí pro virtuální počítače, na kterých se spouští náročné vstupně-výstupní úlohy nebo které hostují kriticky důležité produkční prostředí. |Více konzistentní výkon a spolehlivost než pevný disk. Optimalizovaná pro malou IOPS úlohy| Na základě HDD nákladově efektivní disku pro nepravidelným přístup
| Scénář  | Úlohy v produkčním prostředí a úlohy, u kterých záleží na výkonu |Webové servery, lehce použité podnikové aplikace a vývoje/testování| Zálohování, kritické, nepravidelným přístup 
| Kapacita disku | P4: 32 GiB (pouze spravované disků)<br>P6: 64 GiB (pouze spravované disků)<br>P10: 128 GiB<br>P15: 256 GiB (pouze spravované disků)<br>P20: 512 GiB<br>P30: GiB 1024<br>P40: GiB 2048<br>P50: GiB 4095 |Jenom spravované disky:<br>E10: 128 GiB<br>E15: 256 GiB<br>E20: 512 GiB<br>E30: GiB 1024<br>E40: GiB 2048<br>E50: GiB 4095 | Nespravované disky: GiB 1 – 4 TiB (4095 GiB) <br><br>Managed Disks:<br> S4: 32 GiB <br>S6: 64 GiB <br>S10: 128 GiB <br>S.15: 256 GiB <br>S20: 512 GiB <br>Úrovně S30: GiB 1024 <br>S40: GiB 2048<br>S50: GiB 4095
| Maximální propustnost na disk | 250 MiB/s | Až 60 MiB/s | Až 60 MiB/s 
| Maximum vstupně-výstupních operací za sekundu (IOPS) na disk | 7500 IOPS | Až 500 IOPS | Až 500 IOPS 