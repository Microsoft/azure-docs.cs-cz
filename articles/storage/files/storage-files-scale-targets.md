---
title: Škálovatelnost a cíle výkonnosti služby Azure Files
description: Seznamte se s cíli škálovatelnosti a výkonu pro soubory Azure, včetně kapacity, rychlosti požadavků a omezení šířky pásma příchozích a odchozích.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 10/16/2019
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 46c46faf8f7ee52978ae5542ab7ebd72a41b8357
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81536424"
---
# <a name="azure-files-scalability-and-performance-targets"></a>Škálovatelnost a cíle výkonnosti služby Azure Files

[Azure Files](storage-files-introduction.md) nabízí plně spravované sdílené složky v cloudu, které jsou přístupné prostřednictvím standardního protokolu SMB. Tento článek popisuje škálovatelnost a výkonnostní cíle pro soubory Azure a Azure File Sync.

Škálovatelnost a výkonnostní cíle uvedené zde jsou špičkové cíle, ale mohou být ovlivněny jinými proměnnými ve vašem nasazení. Propustnost souboru může být například také omezena dostupnou šířkou pásma sítě, nikoli pouze servery hostujícími službu Azure Files. Důrazně doporučujeme otestovat vzor využití, abyste zjistili, jestli škálovatelnost a výkon souborů Azure splňují vaše požadavky. Jsme také odhodláni tyto limity časem zvyšovat. Neváhejte nám poskytnout zpětnou vazbu, ať už v komentářích níže nebo na [Azure Files UserVoice](https://feedback.azure.com/forums/217298-storage/category/180670-files), o kterých limitech byste chtěli, abychom se zvýšili.

## <a name="azure-storage-account-scale-targets"></a>Cíle škálování účtu úložiště Azure

Nadřazený prostředek pro sdílenou složku Azure je účet úložiště Azure. Účet úložiště představuje fond úložiště v Azure, který může používat více služeb úložiště, včetně souborů Azure, k ukládání dat. Další služby, které ukládají data v účtech úložiště, jsou úložiště objektů blob Azure, úložiště fronty Azure a úložiště Azure Table. Následující cíle platí pro všechny služby úložiště, které ukládají data v účtu úložiště:

[!INCLUDE [azure-storage-account-limits-standard](../../../includes/azure-storage-account-limits-standard.md)]

[!INCLUDE [azure-storage-limits-azure-resource-manager](../../../includes/azure-storage-limits-azure-resource-manager.md)]

> [!Important]  
> Využití účtu úložiště pro obecné účely z jiných služeb úložiště ovlivňuje vaše sdílené složky Azure ve vašem účtu úložiště. Pokud například dosáhnete maximální kapacity účtu úložiště úložiště objektů blob Azure, nebudete moct vytvářet nové soubory ve sdílené složce Azure, i když je sdílená složka Azure pod maximální velikostí sdílené složky.

## <a name="azure-files-scale-targets"></a>Cíle škálování souborů Azure

Existují tři kategorie omezení, která je třeba zvážit pro soubory Azure: účty úložiště, sdílené složky a soubory.

Příklad: U prémiových sdílených složek může jedna sdílená složka dosáhnout 100 000 vstupně-upů a jeden soubor může škálovat až na 5 000 vstupně-upů. Takže pokud máte tři soubory v jedné sdílené složce, maximální viops můžete získat z této sdílené položky je 15 000.

### <a name="standard-storage-account-limits"></a>Standardní limity účtů úložiště

Viz oddíl [cíle škálování účtu úložiště Azure](#azure-storage-account-scale-targets) pro tato omezení.

### <a name="premium-filestorage-account-limits"></a>Omezení účtu Premium FileStorage

[!INCLUDE [azure-storage-limits-filestorage](../../../includes/azure-storage-limits-filestorage.md)]

> [!IMPORTANT]
> Limity účtu úložiště platí pro všechny sdílené složky. Škálování až na max pro účty FileStorage je dosažitelné pouze v případě, že existuje pouze jedna sdílená složka na účet FileStorage.

### <a name="file-share-and-file-scale-targets"></a>Cíle sdílení souborů a škálování souborů

> [!NOTE]
> Standardní sdílené složky větší než 5 TiB mají určitá omezení. Seznam omezení a pokyny umožňující větší velikosti sdílené složky naleznete v příručce plánování v části [povolit větší sdílené složky ve standardních sdílených složek.](storage-files-planning.md#enable-standard-file-shares-to-span-up-to-100-tib)

[!INCLUDE [storage-files-scale-targets](../../../includes/storage-files-scale-targets.md)]

[!INCLUDE [storage-files-premium-scale-targets](../../../includes/storage-files-premium-scale-targets.md)]

## <a name="azure-file-sync-scale-targets"></a>Cíle škálování v Synchronizaci souborů Azure

Azure File Sync byl navržen s cílem neomezené využití, ale neomezené využití není vždy možné. Následující tabulka označuje hranice testování společnosti Microsoft a také označuje, které cíle jsou pevné limity:

[!INCLUDE [storage-sync-files-scale-targets](../../../includes/storage-sync-files-scale-targets.md)]

### <a name="azure-file-sync-performance-metrics"></a>Metriky výkonu v Synchronizaci souborů Azure

Vzhledem k tomu, že agent Azure File Sync běží na počítači se systémem Windows Server, který se připojuje ke sdíleným položkám souborů Azure, závisí efektivní výkon synchronizace na řadě faktorů ve vaší infrastruktuře: Windows Server a základní konfigurace disku, šířka pásma sítě mezi serverem a úložištěm Azure, velikost souboru, celková velikost datové sady a aktivita v datové sadě. Vzhledem k tomu, že Azure File Sync funguje na úrovni souborů, výkonové charakteristiky řešení založené na synchronizaci souborů Azure se lépe měří v počtu objektů (souborů a adresářů) zpracovaných za sekundu.

Pro Azure File Sync je výkon důležitý ve dvou fázích:

1. **Počáteční jednorázové zřizování : Chcete-li**optimalizovat výkon při počátečním zřizování, podívejte se [na onboarding s Azure File Sync](storage-sync-files-deployment-guide.md#onboarding-with-azure-file-sync) pro podrobnosti o optimálním nasazení.
2. **Průběžná synchronizace**: Po počátečním nastavení dat ve sdílených složek souborů Azure azure file sync udržuje více koncových bodů v synchronizaci.

Chcete-li naplánovat nasazení pro každou z fází, níže jsou níže zjištěné výsledky během interního testování v systému s konfigurací

| Konfigurace systému |  |
|-|-|
| Procesor | 64 virtuálních jader s 64 mib l3 cache |
| Memory (Paměť) | 128 GiB |
| Disk | Disky SAS s raidem 10 s bateriovou zpětnou mezipaměťí |
| Síť | Síť 1 Gb/s |
| Úloha | Souborový server pro obecné účely|

| Počáteční jednorázové zřizování  |  |
|-|-|
| Počet objektů | 25 milionů objektů |
| Velikost datové sady| ~4,7 TiB |
| Průměrná velikost souboru | ~ 200 KiB (největší soubor: 100 GiB) |
| Propustnost nahrávání | 20 objektů za sekundu na skupinu synchronizace |
| Propustnost ke stažení oboru názvů* | 400 objektů za sekundu |

*Při vytvoření nového koncového bodu serveru agent Azure File Sync nestáhne žádný obsah souboru. Nejprve synchronizuje celý obor názvů a potom aktivuje vyvolání pozadí ke stažení souborů, a to buď v plném rozsahu, nebo, pokud je povoleno vrstvení cloudu, do zásady vrstvení cloudu nastavené na koncovém bodu serveru.

| Probíhající synchronizace  |   |
|-|--|
| Počet synchronizovaných objektů| 125 000 objektů (~1% konve) |
| Velikost datové sady| 50 GiB |
| Průměrná velikost souboru | ~500 KiB |
| Propustnost nahrávání | 20 objektů za sekundu na skupinu synchronizace |
| Úplná propustnost ke stažení* | 60 objektů za sekundu |

*Pokud je povoleno vrstvení cloudu, budete pravděpodobně sledovat lepší výkon, protože se stahují pouze některá data souboru. Azure File Sync stáhne data souborů uložených v mezipaměti pouze v případě, že jsou změněny na některý z koncových bodů. U všech vrstvených nebo nově vytvořených souborů agent nestáhne data souboru a místo toho pouze synchronizuje obor názvů se všemi koncovými body serveru. Agent také podporuje částečné stahování vrstvených souborů, jak jsou přístupné uživatelem. 

> [!Note]  
> Výše uvedená čísla nejsou známkou výkonu, který zažijete. Skutečný výkon bude záviset na více faktorech, jak je uvedeno na začátku této části.

Jako obecný průvodce pro vaše nasazení, měli byste mít na paměti několik věcí:

- Propustnost objektu se přibližně škáluje v poměru k počtu skupin synchronizace na serveru. Rozdělení dat do více skupin synchronizace na serveru poskytuje lepší propustnost, která je také omezena serverem a sítí.
- Propustnost objektu je nepřímo úměrná propustnostmi MiB za sekundu. U menších souborů dojde k vyšší propustnosti z hlediska počtu objektů zpracovaných za sekundu, ale nižší propustnost MiB za sekundu. Naopak u větších souborů získáte méně objektů zpracovaných za sekundu, ale vyšší propustnost MiB za sekundu. Propustnost MiB za sekundu je omezena cíli škálování souborů Azure.

## <a name="see-also"></a>Viz také

- [Plánování nasazení služby Soubory Azure](storage-files-planning.md)
- [Plánování nasazení Synchronizace souborů Azure](storage-sync-files-planning.md)
