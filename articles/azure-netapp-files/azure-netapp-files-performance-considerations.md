---
title: Požadavky na výkon pro Azure NetApp Files | Microsoft Docs
description: Přečtěte si o výkonu pro Azure NetApp Files, včetně vztahu kvóty a limitu propustnosti a o tom, jak dynamicky zvyšovat a snižovat kvótu.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/22/2020
ms.author: b-juche
ms.openlocfilehash: edfebe3d9470defbe70b3694d5574e58ca3b5938
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2020
ms.locfileid: "91325517"
---
# <a name="performance-considerations-for-azure-netapp-files"></a>Aspekty výkonu pro Azure NetApp Files

[Omezení propustnosti](azure-netapp-files-service-levels.md) pro svazek s automatickou QoS je určeno kombinací kvóty přiřazené ke svazku a vybrané úrovně služby. U svazků s ruční technologií QoS se dá limit propustnosti definovat individuálně. Při provádění plánů výkonu o Azure NetApp Files je třeba pochopit několik důležitých informací. 

## <a name="quota-and-throughput"></a>Kvóta a propustnost  

Limit propustnosti je pouze jeden determinant skutečného výkonu, který bude realizován.  

Typickými aspekty výkonu úložiště, včetně kombinace čtení a zápisu, velikosti přenosu, náhodných nebo sekvenčních vzorů a mnoha dalších faktorů, přispějete k celkovému množství dodaného výkonu.  

Maximální empirická propustnost, která byla pozorována při testování, je 4 500 MiB/s.  V úrovni Premium Storage zřídí Automatická kvóta svazku QoS 70,31 TiB limit propustnosti, který je dostatečně vysoký, aby dosáhl této úrovně výkonu.  

V případě automatických svazků technologie QoS platí, že pokud se chystáte přidělit objemové kvóty na více než 70,31 TiB, může být ke svazku přiřazená další kvóta pro ukládání dalších dat. Přidaná kvóta ale nebude mít za následek další zvýšení skutečné propustnosti.  

Stejný empirický strop propustnosti se vztahuje na svazky s ruční technologií QoS. Maximální propustnost může být přiřazena ke svazku je 4 500 MiB/s.

## <a name="automatic-qos-volume-quota-and-throughput"></a>Automatická kvóta a propustnost svazku QoS

Tato část popisuje správu kvót a propustnost pro svazky s automatickým typem QoS.

### <a name="overprovisioning-the-volume-quota"></a>Přezřizování kvóty svazků

Pokud je výkon úlohy vázaný na limit propustnosti, je možné přezřídit automatickou kvótu technologie QoS pro zajištění vyšší úrovně propustnosti a dosáhnout vyššího výkonu.  

Pokud například automatická technologie QoS v úrovni Premium Storage má jenom 500 GiB dat, ale vyžaduje 128 MiB/s, můžete nastavit kvótu na 2 TiB, aby se úroveň propustnosti patřičně nastavila (64 MiB/s na TB × 2 TiB = 128 MiB/s).  

Pokud jste trvale přezřídili svazek pro dosažení vyšší propustnosti, zvažte použití ručních svazků technologie QoS nebo použití vyšší úrovně služby.  V předchozím příkladu můžete dosáhnout stejného limitu propustnosti s poloviční automatickou kvótou svazku QoS pomocí úrovně Ultra Storage místo toho (128 MiB/s za TiB * 1 TiB = 128 MiB/s).

### <a name="dynamically-increasing-or-decreasing-volume-quota"></a>Dynamické zvýšení nebo snížení kvóty svazku

Pokud jsou vaše požadavky na výkon dočasná, nebo pokud jste zvýšili nároky na výkon po určitou dobu, můžete kvótu pro okamžitou úpravu limitu propustnosti dynamicky zvýšit nebo snížit.  Vezměte na vědomí následující skutečnosti: 

* Kvóta svazku se dá zvýšit nebo snížit, aniž by bylo nutné pozastavit v/v a přístup k tomuto svazku není přerušený ani ovlivněný.  

    Kvótu můžete upravit během aktivní vstupně-výstupní transakce se svazkem.  Počítejte s tím, že kvótu svazku nelze nikdy snížit pod množství logických dat, která jsou uložena ve svazku.

* Po změně kvóty svazku je odpovídající změna v limitu propustnosti skoro okamžitá. 

    Tato změna neovlivní nebo neovlivní přístup ke svazku nebo vstupně-výstupní operace.  

* Úprava kvóty svazku může vyžadovat změnu velikosti fondu kapacity.  

    Velikost fondu kapacity se dá upravit dynamicky a aniž by to ovlivnilo dostupnost svazku nebo vstupně-výstupní operace.

## <a name="manual-qos-volume-quota-and-throughput"></a>Ruční kvóta a propustnost svazku QoS 

Pokud používáte ruční svazky technologie QoS, nemusíte převýšit kvótu svazku, aby se dosáhlo vyšší propustnosti, protože propustnost je možné přiřadit každému svazku nezávisle. Stále ale potřebujete zajistit, aby fond kapacit byl předem zajištěný s dostatečnou propustností pro potřeby výkonu. Propustnost fondu kapacity se zřizuje podle velikosti a úrovně služeb. Další podrobnosti najdete v tématu [úrovně služeb pro Azure NetApp Files](azure-netapp-files-service-levels.md) .


## <a name="next-steps"></a>Další kroky

- [Úrovně služeb pro Azure NetApp Files](azure-netapp-files-service-levels.md)
- [Srovnávací testy výkonu pro Linux](performance-benchmarks-linux.md)