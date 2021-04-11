---
title: Scénáře mezipaměti HPC Azure
description: Popisuje, jak zjistit, jestli vaše výpočetní úloha dobře funguje s mezipamětí Azure HPC.
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 03/29/2021
ms.author: v-erkel
ms.openlocfilehash: 36e0135102fbede5505e96fb1aa255588b2f2ae2
ms.sourcegitcommit: 20f8bf22d621a34df5374ddf0cd324d3a762d46d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/09/2021
ms.locfileid: "107259950"
---
# <a name="is-your-job-a-good-fit-for-azure-hpc-cache"></a>Je vaše úloha vhodná pro mezipaměť prostředí Azure HPC?

Mezipaměť HPC Azure může urychlit přístup k datům pro vysoce výkonné výpočetní úlohy v různých oborech. Není to ale ideální pro všechny typy pracovních postupů. Tento článek obsahuje pokyny, jak se rozhodnout, jestli je mezipaměť HPC dobrá volbou pro vaše potřeby.

V tomto článku najdete také stručné [informace](hpc-cache-overview.md) o tom, kdy použít mezipaměť prostředí Azure HPC a některé příklady případů použití.

Přečtěte si také [Tento článek](nfs-blob-considerations.md) o tom, jak zajistit efektivní využití [úložiště objektů BLOB připojené k systému souborů NFS](../storage/blobs/network-file-system-protocol-support.md), které je ve verzi Preview.

## <a name="nfs-version-30-applications"></a>Aplikace NFS verze 3,0

Mezipaměť HPC Azure podporuje jenom klienty NFS 3,0.

## <a name="high-read-to-write-ratio"></a>Vysoký poměr pro čtení a zápis

Úlohy, u kterých jsou výpočetní klienti více čteny než zápisy, jsou obvykle dobrými kandidáty na mezipaměť. Pokud je například poměr čtení k zápisu 80/20 nebo 70/30, mezipaměť prostředí Azure HPC může pomáhat při obsluze často požadovaných souborů z mezipaměti, aniž by musela je načítat ze vzdáleného úložiště.

Načítají se soubory a jejich ukládání do mezipaměti poprvé má malou další latenci při normální žádosti klienta přímo do úložiště, takže zvýšení efektivity se dokončí při dalším požadavku na stejný soubor klientem. To platí hlavně pro velké soubory. Pokud je každá žádost klienta jedinečná, je dopad mezipaměti HPC omezený. Ale čím větší je soubor, tím lepší je výkon v průběhu času po prvním přístupu.

## <a name="file-based-analytic-workload"></a>Analytické úlohy na základě souborů

Mezipaměť prostředí Azure HPC je ideální pro kanál, který používá data založená na souborech a běží napříč velkým počtem výpočetních klientů, zejména pokud jsou výpočetní klienti virtuálními počítači Azure. Může pomoct vyřešit pomalý nebo nekonzistentní výkon, který je způsoben dlouhým časem přístupu k souboru.

## <a name="remote-data-access"></a>Vzdálený přístup k datům

Mezipaměť HPC Azure může přispět ke snížení latence, pokud vaše úlohy potřebují přístup ke vzdáleným datům, která se nedají přesunout blíž k výpočetním prostředkům. Vaše záznamy můžou být například na konci prostředí sítě WAN, v jiné oblasti Azure nebo v datovém centru zákazníka. (Někdy se označuje jako "shlukování souborů".)

## <a name="heavy-request-load"></a>Silné zatížení požadavku

Pokud velký počet klientů požaduje data ze zdroje ve stejnou dobu, může Azure HPC cache zrychlit přístup k souborům. Například při použití s vysoce výkonným výpočetním clusterem poskytuje mezipaměť prostředí Azure HPC škálovatelnost pro vysoké počty souběžných požadavků prostřednictvím mezipaměti.

## <a name="compute-resources-are-located-in-azure"></a>Výpočetní prostředky se nacházejí v Azure.

Virtuální počítače Azure představují škálovatelnou a cenově výhodné odpověď na vysoce výkonné výpočetní úlohy. Mezipaměť HPC Azure vám může přispět tak, že získá informace, které k nim potřebují, zejména v případě, že jsou původní data uložená ve vzdáleném systému.

Pokud chce zákazník spustit aktuální kanál "tak, jak je" ve virtuálních počítačích Azure, mezipaměť prostředí Azure HPC může poskytovat řešení sdíleného úložiště (nebo ukládání do mezipaměti) založené na POSIX pro škálovatelnost.

Pomocí Azure HPC cache nemusíte znovu architektovat pracovní kanál, aby bylo možné provádět nativní volání do úložiště objektů BLOB v Azure. K datům můžete přistupovat v původním systému, nebo můžete použít mezipaměť HPC k přesunu do nového kontejneru objektů BLOB.

## <a name="next-steps"></a>Další kroky

* Další informace o plánování a konfiguraci mezipaměti najdete v článcích [Přehled](hpc-cache-overview.md) a [předpoklady](hpc-cache-prerequisites.md) .
* Informace o tom, jak používat [úložiště objektů BLOB s podporou NFS](nfs-blob-considerations.md) (ve verzi Preview) s mezipamětí Azure HPC
