---
title: Škálovatelnost a cíle výkonnosti služby Azure Files | Microsoft Docs
description: Seznamte se s cíli škálovatelnosti a výkonu pro soubory Azure, včetně kapacity, míry požadavků a omezení šířky pásma pro příchozí a odchozí připojení.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 10/16/2019
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 267a63eba90c74b79078a7c04c1d2d8929cf2a44
ms.sourcegitcommit: b2fb32ae73b12cf2d180e6e4ffffa13a31aa4c6f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/05/2019
ms.locfileid: "73615775"
---
# <a name="azure-files-scalability-and-performance-targets"></a>Škálovatelnost a cíle výkonnosti souborů Azure

[Soubory Azure](storage-files-introduction.md) nabízí plně spravované sdílené složky v cloudu, které jsou přístupné přes standardní průmyslový protokol SMB. Tento článek popisuje škálovatelnost a výkonnostní cíle pro Azure Files a Azure File Sync.

Zde uvedené cíle škálovatelnosti a výkonu jsou špičkové cíle, ale mohou být ovlivněny jinými proměnnými v nasazení. Například propustnost souboru může být omezena dostupnou šířkou pásma sítě, nejen servery hostujícími službu soubory Azure. Důrazně doporučujeme testovat vzor používání, abyste zjistili, jestli vaše požadavky vyhovují škálovatelnosti a výkonu souborů Azure. Potvrdili jsme také, že se tato omezení zvyšují v průběhu času. Nemusíte si prosím váhajíi sdělit svůj názor, a to buď v komentářích níže, nebo ve [službě Azure Files UserVoice](https://feedback.azure.com/forums/217298-storage/category/180670-files), o kterých omezeních byste chtěli zobrazit zvýšení.

## <a name="azure-storage-account-scale-targets"></a>Cíle škálování účtu Azure Storage

Nadřazeným prostředkem sdílené složky Azure je účet úložiště Azure. Účet úložiště představuje fond úložiště v Azure, který může používat víc služeb úložiště, včetně souborů Azure, pro ukládání dat. Další služby, které ukládají data v účtech úložiště, jsou Azure Blob Storage, Azure Queue Storage a Azure Table Storage. Následující cíle platí pro všechny služby úložiště, které ukládají data v účtu úložiště:

[!INCLUDE [azure-storage-limits](../../../includes/azure-storage-limits.md)]

[!INCLUDE [azure-storage-limits-azure-resource-manager](../../../includes/azure-storage-limits-azure-resource-manager.md)]

> [!Important]  
> Využití účtu úložiště pro obecné účely z jiných služeb úložiště má vliv na sdílené složky Azure v účtu úložiště. Pokud například dosáhnete maximální kapacity účtu úložiště ve službě Azure Blob Storage, nebudete moct ve sdílené složce Azure vytvářet nové soubory, a to ani v případě, že je vaše sdílená složka Azure menší než maximální velikost sdílené složky.

## <a name="azure-files-scale-targets"></a>Cíle škálování souborů Azure

Existují tři kategorie omezení, které je třeba vzít v úvahu pro soubory Azure: účty úložiště, sdílené složky a soubory.

Například: u souborů úrovně Premium může jedna sdílená složka dosahovat 100 000 vstupně-výstupních operací a jeden soubor může škálovat až 5 000 IOPS. Takže pokud máte tři soubory v jedné sdílené složce, maximální IOPS, kterou můžete z této sdílené složky získat, je 15 000.

### <a name="standard-storage-account-limits"></a>Omezení účtu úložiště úrovně Standard

Tato omezení najdete v části [cíle škálování účtu Azure Storage](#azure-storage-account-scale-targets) .

### <a name="premium-filestorage-account-limits"></a>Omezení účtu úložiště na úrovni Premium

[!INCLUDE [azure-storage-limits-filestorage](../../../includes/azure-storage-limits-filestorage.md)]

> [!IMPORTANT]
> Limity účtu úložiště se vztahují na všechny sdílené složky. Horizontální navýšení kapacity účtů úložiště je možné pouze v případě, že je pro každý účet úložiště k dispozici pouze jedna sdílená složka.

### <a name="file-share-and-file-scale-targets"></a>Sdílení souborů a cíle pro škálování souborů

> [!NOTE]
> Standardní sdílené složky větší než 5 TiB mají určitá omezení a regionální omezení.
> Seznam omezení, regionálních informací a pokynů, jak tyto větší velikosti sdílených složek povolit, najdete v části připojení [k větším sdíleným složkám](storage-files-planning.md#onboard-to-larger-file-shares-standard-tier) v příručce pro plánování souborů.

[!INCLUDE [storage-files-scale-targets](../../../includes/storage-files-scale-targets.md)]

[!INCLUDE [storage-files-premium-scale-targets](../../../includes/storage-files-premium-scale-targets.md)]

## <a name="azure-file-sync-scale-targets"></a>Azure File Sync cíle škálování

Azure File Sync byla navržena s cílem neomezeného využití, ale neomezené využití není vždy možné. Následující tabulka uvádí hranice testování Microsoftu a také uvádí, které cíle jsou pevné meze:

[!INCLUDE [storage-sync-files-scale-targets](../../../includes/storage-sync-files-scale-targets.md)]

### <a name="azure-file-sync-performance-metrics"></a>Azure File Sync metriky výkonu

Vzhledem k tomu, že agent Azure File Sync běží na počítači s Windows serverem, který se připojuje ke sdíleným složkám Azure, výkon efektivní synchronizace závisí na několika faktorech v infrastruktuře: Windows Server a základní konfigurace disku, Šířka pásma sítě. mezi serverem a úložištěm Azure, velikostí souboru, celkovou velikostí datové sady a aktivitou pro datovou sadu. Vzhledem k tomu, že Azure File Sync pracuje na úrovni souboru, jsou výkonnostní charakteristiky řešení založeného na Azure File Sync lépe měřeny v počtu objektů (souborů a adresářů) zpracovaných za sekundu.

Pro Azure File Sync je výkon kritický ve dvou fázích:

1. **Prvotní zřizování**: pro optimalizaci výkonu při počátečním zřizování najdete informace o optimálních podrobnostech o nasazení [pomocí Azure File Sync](storage-sync-files-deployment-guide.md#onboarding-with-azure-file-sync) .
2. **Průběžná synchronizace**: po počátečním navýšení dat ve sdílených složkách Azure Azure File Sync udržuje několik koncových bodů v synchronizaci.

Abychom vám pomohli naplánovat nasazení pro každou fázi, níže jsou výsledky zjištěné během interního testování v systému s konfigurací.

| Konfigurace systému |  |
|-|-|
| Procesor | 64 virtuálních jader s 64 MiB L3 cache |
| Memory (Paměť) | 128 GiB |
| Disk | Disky SAS s RAID 10 s mezipamětí zálohovanou pro baterie |
| Síť | 1 GB/s sítě |
| Úloha | Pro obecné účely souborový server|

| Prvotní zřízení v jednom čase  |  |
|-|-|
| Počet objektů | objekty 25 000 000 |
| Velikost datové sady| ~ 4,7 TiB |
| Průměrná velikost souboru | ~ 200 KiB (největší soubor: 100 GiB) |
| Propustnost nahrávání | 20 objektů za sekundu |
| Propustnost stahování oboru názvů * | 400 objektů za sekundu |

\* Při vytvoření nového koncového bodu serveru nestáhne agent Azure File Sync žádný obsah souboru. Nejprve synchronizuje celý obor názvů a potom aktivuje odvolání na pozadí pro stažení souborů, a to buď v celém rozsahu, nebo v případě, že je povolená vrstva cloudu, do zásady clouding nastavené na koncovém bodu serveru.

| Průběžná synchronizace  |   |
|-|--|
| Počet synchronizovaných objektů| 125 000 objektů (počet změn: 1%) |
| Velikost datové sady| 50 GiB |
| Průměrná velikost souboru | ~ 500 KiB |
| Propustnost nahrávání | 20 objektů za sekundu |
| Úplná propustnost ke stažení * | 60 objektů za sekundu |

\* Pokud je zapnutá vrstva cloudu, budete pravděpodobně sledovat lepší výkon, protože se stáhnou jenom některá z těchto souborů. Azure File Sync stahuje data souborů uložených v mezipaměti pouze při jejich změně v libovolném koncovém bodu. U všech vrstvených nebo nově vytvořených souborů agent nestáhne data souboru a místo toho pouze synchronizuje obor názvů se všemi koncovými body serveru. Agent také podporuje částečné stahování vrstvených souborů, ke kterým uživatel přistupoval. 

> [!Note]  
> Výše uvedená čísla nezpůsobují indikaci výkonu, ke kterému dojde. Skutečný výkon bude záviset na několika faktorech, jak je uvedeno na začátku této části.

Jako obecné vodítko pro vaše nasazení byste měli mít na paměti pár věcí:

- Propustnost objektu se přibližně škáluje v poměru k počtu skupin synchronizace na serveru. Rozdělení dat do více skupin synchronizace na serveru má za důsledek lepší propustnost, což je také omezeno serverem a sítí.
- Propustnost objektu je v opačném poměrně úměrná propustnosti MiB za sekundu. Pro menší soubory dojde k vyšší propustnosti z hlediska počtu zpracovaných objektů za sekundu, ale nižší propustnosti MiB za sekundu. Naopak pro větší soubory získáte méně zpracovaných objektů za sekundu, ale vyšší propustnost MiB za sekundu. Propustnost MiB za sekundu je omezená na cíle škálování souborů Azure.

## <a name="see-also"></a>Viz také

- [Plánování nasazení služby Soubory Azure](storage-files-planning.md)
- [Plánování nasazení Synchronizace souborů Azure](storage-sync-files-planning.md)
- [Škálovatelnost a výkonnostní cíle pro jiné služby úložiště](../common/storage-scalability-targets.md)
