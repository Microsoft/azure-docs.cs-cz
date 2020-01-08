---
title: Azure soubory škálovatelnost a výkonnostní cíle
description: Další informace o škálovatelnost a výkonnostní cíle pro soubory Azure, včetně kapacity, frekvence požadavků a omezení šířky příchozího a odchozího pásma.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 10/16/2019
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 060b4a8992ab1c48cceb9506b4f556d9098b9be0
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/25/2019
ms.locfileid: "75452954"
---
# <a name="azure-files-scalability-and-performance-targets"></a>Azure soubory škálovatelnost a výkonnostní cíle

[Služba soubory Azure](storage-files-introduction.md) nabízí plně spravované sdílené složky v cloudu, které jsou přístupné přes standardní protokol SMB. Tento článek popisuje škálovatelnost a výkonnostní cíle pro soubory Azure a Azure File Sync.

Škálovatelnost a výkonnostní cíle, které jsou zde uvedeny jsou vyšší kategorie cíle, ale mohou být ovlivněny jiné proměnné ve vašem nasazení. Například propustnost pro soubor může být také omezen ve vaši dostupnou šířku pásma sítě, nikoli pouze servery, které hostují služby soubory Azure. Důrazně doporučujeme testování vaší vzor používání a zjistit, zda škálovatelnost a výkon Azure Files splňují vaše požadavky. Můžeme také usilujeme o to zvyšujícími se tato omezení v čase. Neváhejte sdělte nám svůj názor, buď v komentářích pod nebo na [UserVoice soubory Azure](https://feedback.azure.com/forums/217298-storage/category/180670-files), o které limity byste chtěli vidět nám zvýšit.

## <a name="azure-storage-account-scale-targets"></a>Cíle škálování účtu Azure storage

Nadřazený prostředek pro sdílené složky Azure je účet úložiště Azure. Účet úložiště představuje fond úložiště v Azure, které je možné k ukládání dat pomocí několika služeb úložiště, včetně souborů Azure. Jiné služby, které ukládají data v účtech úložiště, jsou Azure Blob storage, Azure Queue storage a Azure Table storage. Následující cíle, které se vztahují všechny služby storage, ukládání dat v účtu úložiště:

[!INCLUDE [azure-storage-account-limits-standard](../../../includes/azure-storage-account-limits-standard.md)]

[!INCLUDE [azure-storage-limits-azure-resource-manager](../../../includes/azure-storage-limits-azure-resource-manager.md)]

> [!Important]  
> Využití účtu úložiště pro obecné účely z jiných služeb úložiště má vliv na sdílené složky Azure v účtu úložiště. Například pokud dosáhnete maximální úložnou kapacitu účtu úložiště objektů Blob v Azure, nebudete moct vytvořit nové soubory ve sdílené složky Azure, i v případě, že sdílené složky Azure je nižší než maximální sdílené složky size.

## <a name="azure-files-scale-targets"></a>Azure soubory měřítko cíle

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

## <a name="azure-file-sync-scale-targets"></a>Azure File Sync měřítko cíle

Azure File Sync byla navržena s cílem neomezeného využití, ale neomezené využití není vždy možné. Následující tabulka uvádí hranice testování Microsoftu a také uvádí, které cíle jsou pevné meze:

[!INCLUDE [storage-sync-files-scale-targets](../../../includes/storage-sync-files-scale-targets.md)]

### <a name="azure-file-sync-performance-metrics"></a>Metriky výkonu služby Azure File Sync

Agenta Azure File Sync běží na počítače s Windows serverem, který se připojuje k sdílené složky Azure, výkonu efektivní synchronizace závisí na řadě faktorů ve vaší infrastruktuře: Windows Server a základní konfiguraci disku, šířka pásma sítě mezi serverem a Azure storage, velikost souboru, velikost celkového datové sady a aktivit na datové sadě. Protože Azure File Sync funguje na úrovni souboru, výkonové charakteristiky řešení založené na Azure File Sync se lépe měří v počet objektů (soubory a adresáře) zpracovaných za sekundu.

Výkon pro Azure File Sync, je důležité ve dvou fázích:

1. **Počáteční jednorázové zřizování**: Optimalizace výkonu v počátečním zřizování, najdete v tématu [připojování pomocí služby Azure File Sync](storage-sync-files-deployment-guide.md#onboarding-with-azure-file-sync) podrobnosti optimální nasazení.
2. **Probíhající synchronizace**: po dat je zpočátku nasadí do sdílené složky Azure, Azure File Sync udržuje několik koncových bodů synchronizované.

Při plánování nasazení pro každou z fází, níže jsou výsledky pozorovány při interním testování v systému, konfigurace

| Konfigurace systému |  |
|-|-|
| Procesor | 64 virtuálních jader s 64 MiB L3 |
| Paměť | 128 GiB |
| Disk | Disky SAS pomocí diskového pole RAID 10 s baterie podporou mezipaměti |
| Network (Síť) | 1 GB/s síť |
| Úloha | Souborový Server pro obecné účely|

| Počáteční jednorázové zřizování  |  |
|-|-|
| Počet objektů | objekty 25 000 000 |
| Velikost datové sady| ~ 4,7 TiB |
| Průměrná velikost souboru | ~ 200 KiB (největší soubor: 100 GiB) |
| Nahrát propustnost | 20 objekty za sekundu |
| Namespace stahování propustnost * | 400 objektů za sekundu |

\* Když se vytvoří nový koncový bod serveru, agenta Azure File Sync nebude stahovat žádný obsah souboru. Nejprve synchronizuje úplný obor názvů a pak aktivační události na pozadí spojené s vracením ke stažení souborů, buď v plné výši nebo vrstvení cloudu, pokud je povoleno, nastavte na koncovém bodu serveru zásad vrstvení cloudu.

| Probíhající synchronizace  |   |
|-|--|
| Počet objektů, které jsou synchronizovány| 125,000 objekty (klidové vytížení ~ 1 %) |
| Velikost datové sady| 50 GB |
| Průměrná velikost souboru | ~ 500 KiB |
| Nahrát propustnost | 20 objekty za sekundu |
| Úplné stažení propustnost * | 60 objektů za sekundu |

\* Pokud cloudu ovládání datových vrstev je povolená, budete pravděpodobně sledovat lepší výkon jako pouze některá data se stáhne soubor. Azure File Sync stáhne jenom data uložená v mezipaměti souborů, když se změní na žádném z koncových bodů. Vrstvené nebo nově vytvořené soubory agent nebude stahovat data souborů a místo toho synchronizuje pouze obor názvů pro všechny koncové body serveru. Agent také podporuje částečné stažení vrstvené soubory jsou přístupné uživatelem. 

> [!Note]  
> Výše uvedených čísel nejsou údaj o výkon, který bude probíhat. Skutečný výkon bude záviset na několika faktorech, jak je uvedeno na začátku této části.

Jako obecné vodítko pro vaše nasazení byste měli mít na paměti několik věcí:

- Objekt propustnost přibližně škáluje poměru k počtu skupin synchronizace na serveru. Rozdělení dat do více skupin synchronizace na serveru poskytuje vyšší propustnost, což je také omezena serveru a sítě.
- Objekt propustnost je nepřímo úměrná MiB za druhé propustnost. Pro menší soubory se budou mít vyšší výkon z hlediska počtu objektů zpracovaných za druhé, ale nižší MiB za druhé propustnost. Naopak pro větší soubory, zobrazí se méně objektů zpracovaných za druhé, ale vyšší MiB za druhé propustnost. MiB za druhé propustnost je omezená cíle škálování Azure Files.

## <a name="see-also"></a>Další informace najdete v tématech

- [Plánování nasazení služby Soubory Azure](storage-files-planning.md)
- [Plánování nasazení Synchronizace souborů Azure](storage-sync-files-planning.md)
- [Škálovatelnost a výkonnostní cíle pro ostatní služby úložiště](../common/storage-scalability-targets.md)
