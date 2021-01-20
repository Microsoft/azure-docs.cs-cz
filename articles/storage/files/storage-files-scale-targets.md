---
title: Škálovatelnost a cíle výkonnosti služby Azure Files
description: Seznamte se s cíli škálovatelnosti a výkonu pro soubory Azure, včetně kapacity, míry požadavků a omezení šířky pásma pro příchozí a odchozí připojení.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 10/16/2019
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: e10f45af89e19f6fe62ff729f96d870e008c96ec
ms.sourcegitcommit: 8a74ab1beba4522367aef8cb39c92c1147d5ec13
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/20/2021
ms.locfileid: "98611096"
---
# <a name="azure-files-scalability-and-performance-targets"></a>Škálovatelnost a cíle výkonnosti služby Azure Files

[Soubory Azure](storage-files-introduction.md) nabízí plně spravované sdílené složky v cloudu, které jsou přístupné přes standardní průmyslový protokol SMB. Tento článek popisuje škálovatelnost a výkonnostní cíle pro Azure Files a Azure File Sync.

Zde uvedené cíle škálovatelnosti a výkonu jsou špičkové cíle, ale mohou být ovlivněny jinými proměnnými v nasazení. Například propustnost souboru může být omezena dostupnou šířkou pásma sítě, nejen servery hostujícími službu soubory Azure. Důrazně doporučujeme testovat vzor používání, abyste zjistili, jestli vaše požadavky vyhovují škálovatelnosti a výkonu souborů Azure. Potvrdili jsme také, že se tato omezení zvyšují v průběhu času. Nemusíte si prosím váhajíi sdělit svůj názor, a to buď v komentářích níže, nebo ve [službě Azure Files UserVoice](https://feedback.azure.com/forums/217298-storage/category/180670-files), o kterých omezeních byste chtěli zobrazit zvýšení.

## <a name="azure-storage-account-scale-targets"></a>Cíle škálování účtu Azure Storage

Nadřazeným prostředkem sdílené složky Azure je účet úložiště Azure. Účet úložiště představuje fond úložiště v Azure, který může používat víc služeb úložiště, včetně souborů Azure, pro ukládání dat. Další služby, které ukládají data v účtech úložiště, jsou Azure Blob Storage, Azure Queue Storage a Azure Table Storage. Následující cíle platí pro všechny služby úložiště, které ukládají data v účtu úložiště:

[!INCLUDE [azure-storage-account-limits-standard](../../../includes/azure-storage-account-limits-standard.md)]

[!INCLUDE [azure-storage-limits-azure-resource-manager](../../../includes/azure-storage-limits-azure-resource-manager.md)]

> [!Important]  
> Využití účtu úložiště pro obecné účely z jiných služeb úložiště má vliv na sdílené složky Azure v účtu úložiště. Pokud například dosáhnete maximální kapacity účtu úložiště ve službě Azure Blob Storage, nebudete moct ve sdílené složce Azure vytvářet nové soubory, a to ani v případě, že je vaše sdílená složka Azure menší než maximální velikost sdílené složky.

## <a name="azure-files-scale-targets"></a>Cíle škálování služby Azure Files

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
> Standardní sdílené složky větší než 5 TiB mají určitá omezení. Seznam omezení a pokyny, jak povolit větší velikost sdílených složek, najdete v části [Povolení větších sdílených složek ve standardních sdílených složkách](storage-files-planning.md#enable-standard-file-shares-to-span-up-to-100-tib) Průvodce plánováním.

[!INCLUDE [storage-files-scale-targets](../../../includes/storage-files-scale-targets.md)]

[!INCLUDE [storage-files-premium-scale-targets](../../../includes/storage-files-premium-scale-targets.md)]

## <a name="azure-file-sync-scale-targets"></a>Cíle škálování v Synchronizaci souborů Azure

Azure File Sync byla navržena s cílem neomezeného využití, ale neomezené využití není vždy možné. Následující tabulka uvádí hranice testování Microsoftu a také uvádí, které cíle jsou pevné meze:

[!INCLUDE [storage-sync-files-scale-targets](../../../includes/storage-sync-files-scale-targets.md)]

### <a name="azure-file-sync-performance-metrics"></a>Metriky výkonu v Synchronizaci souborů Azure

Vzhledem k tomu, že agent Azure File Sync běží na počítači s Windows serverem, který se připojuje ke sdíleným složkám Azure, výkon efektivní synchronizace závisí na několika faktorech v infrastruktuře: Windows Server a základní konfigurace disku, Šířka pásma sítě mezi serverem a úložištěm Azure, velikost souboru, celková velikost datové sady a aktivita v datové sadě. Vzhledem k tomu, že Azure File Sync pracuje na úrovni souboru, jsou výkonnostní charakteristiky řešení založeného na Azure File Sync lépe měřeny v počtu objektů (souborů a adresářů) zpracovaných za sekundu.

Pro Azure File Sync je výkon kritický ve dvou fázích:

1. **Prvotní zřizování**: pro optimalizaci výkonu při počátečním zřizování najdete informace o optimálních podrobnostech o nasazení [pomocí Azure File Sync](storage-sync-files-deployment-guide.md#onboarding-with-azure-file-sync) .
2. **Průběžná synchronizace**: po počátečním navýšení dat ve sdílených složkách Azure Azure File Sync udržuje několik koncových bodů v synchronizaci.

Abychom vám pomohli naplánovat nasazení pro každou fázi, níže jsou výsledky zjištěné během interního testování v systému s konfigurací.

| Konfigurace systému | Podrobnosti |
|-|-|
| Procesor | 64 virtuálních jader s 64 MiB L3 cache |
| Memory (Paměť) | 128 GiB |
| Disk | Disky SAS s RAID 10 s mezipamětí zálohovanou pro baterie |
| Síť | 1 GB/s sítě |
| Úloha | Pro obecné účely souborový server|

| Prvotní zřízení v jednom čase  | Podrobnosti |
|-|-|
| Počet objektů | objekty 25 000 000 |
| Velikost datové sady| ~ 4,7 TiB |
| Průměrná velikost souboru | ~ 200 KiB (největší soubor: 100 GiB) |
| Počáteční výčet změn v cloudu | 20 objektů za sekundu  |
| Propustnost nahrávání | 20 objektů za sekundu na skupinu synchronizace |
| Propustnost stahování oboru názvů | 400 objektů za sekundu |

### <a name="initial-one-time-provisioning"></a>Prvotní zřízení v jednom čase

**Počáteční výčet změn v cloudu**: když se vytvoří nová skupina synchronizace, bude první krok, který se spustí, počáteční výčet změn v cloudu. V tomto procesu bude systém vypsat všechny položky ve sdílené složce Azure. Během tohoto procesu nebude žádná aktivita synchronizace, tj. žádné položky nebudou staženy z koncového bodu cloudu do koncového bodu serveru a žádné položky nebudou odeslány z koncového bodu serveru do koncového bodu cloudu. Po dokončení počátečního výčtu změn v cloudu bude aktivita synchronizace pokračovat.
Míra výkonu je 20 objektů za sekundu. Zákazníci si můžou odhadnout čas, který bude trvat, aby dokončili počáteční výčet změn v cloudu tím, že určí počet položek ve sdílené složce cloudu a pomocí následujících vzorců Získá čas ve dnech. 

   **Čas (ve dnech) počátečního výčtu cloudu = (počet objektů v koncovém bodu cloudu)/(20 × 60 × 60 × 24)**

**Propustnost stahování oboru názvů** Při přidání nového koncového bodu serveru do existující skupiny synchronizace agent Azure File Sync nestáhne žádný obsah souboru z koncového bodu cloudu. Nejprve synchronizuje celý obor názvů a potom aktivuje odvolání na pozadí pro stažení souborů, a to buď v celém rozsahu, nebo v případě, že je povolená vrstva cloudu, do zásady clouding nastavené na koncovém bodu serveru.


| Průběžná synchronizace  | Podrobnosti  |
|-|--|
| Počet synchronizovaných objektů| 125 000 objektů (počet změn: 1%) |
| Velikost datové sady| 50 GiB |
| Průměrná velikost souboru | ~ 500 KiB |
| Propustnost nahrávání | 20 objektů za sekundu na skupinu synchronizace |
| Úplná propustnost ke stažení * | 60 objektů za sekundu |

* Pokud je zapnutá vrstva cloudu, budete pravděpodobně sledovat lepší výkon, protože se stáhnou jenom některá z těchto souborů. Azure File Sync stahuje data souborů uložených v mezipaměti pouze při jejich změně v libovolném koncovém bodu. U všech vrstvených nebo nově vytvořených souborů agent nestáhne data souboru a místo toho pouze synchronizuje obor názvů se všemi koncovými body serveru. Agent také podporuje částečné stahování vrstvených souborů, ke kterým uživatel přistupoval. 

> [!Note]  
> Výše uvedená čísla nezpůsobují indikaci výkonu, ke kterému dojde. Skutečný výkon bude záviset na několika faktorech, jak je uvedeno na začátku této části.

Jako obecné vodítko pro vaše nasazení byste měli mít na paměti pár věcí:

- Propustnost objektu se přibližně škáluje v poměru k počtu skupin synchronizace na serveru. Rozdělení dat do více skupin synchronizace na serveru má za důsledek lepší propustnost, což je také omezeno serverem a sítí.
- Propustnost objektu je v opačném poměrně úměrná propustnosti MiB za sekundu. Pro menší soubory dojde k vyšší propustnosti z hlediska počtu zpracovaných objektů za sekundu, ale nižší propustnosti MiB za sekundu. Naopak pro větší soubory získáte méně zpracovaných objektů za sekundu, ale vyšší propustnost MiB za sekundu. Propustnost MiB za sekundu je omezená na cíle škálování souborů Azure.

## <a name="see-also"></a>Viz také

- [Plánování nasazení Azure Files](storage-files-planning.md)
- [Plánování nasazení Synchronizace souborů Azure](storage-sync-files-planning.md)
