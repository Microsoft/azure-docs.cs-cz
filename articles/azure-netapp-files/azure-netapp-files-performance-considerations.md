---
title: Požadavky na výkon pro Azure NetApp Files | Microsoft Docs
description: Popisuje požadavky na výkon pro Azure NetApp Files.
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
ms.date: 06/25/2019
ms.author: b-juche
ms.openlocfilehash: 4badf550809df6cc63711c47e6e6f5b3690fdfca
ms.sourcegitcommit: 366e95d58d5311ca4b62e6d0b2b47549e06a0d6d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/01/2020
ms.locfileid: "82691318"
---
# <a name="performance-considerations-for-azure-netapp-files"></a>Aspekty výkonu pro Azure NetApp Files

[Omezení propustnosti](azure-netapp-files-service-levels.md) svazku je určeno kombinací kvóty přiřazené ke svazku a vybrané úrovně služby. Při provádění plánů výkonu o Azure NetApp Files je třeba pochopit několik důležitých informací. 

## <a name="quota-and-throughput"></a>Kvóta a propustnost  

Limit propustnosti je pouze jeden determinant skutečného výkonu, který bude realizován.  

Typickými aspekty výkonu úložiště, včetně kombinace čtení a zápisu, velikosti přenosu, náhodných nebo sekvenčních vzorů a mnoha dalších faktorů, přispějete k celkovému množství dodaného výkonu.  

Maximální empirická propustnost, která byla pozorována při testování, je 4 500 MiB/s.  V úrovni Premium Storage zřídí kvóta 70,31 TiB limit propustnosti, který je dostatečně vysoký pro dosažení této úrovně výkonu.  

Pokud se chystáte přidělit objemové kvóty na více než 70,31 TiB, může být ke svazku přiřazená další kvóta pro ukládání dalších dat. Přidaná kvóta ale nebude mít za následek další zvýšení skutečné propustnosti.  

## <a name="overprovisioning-the-volume-quota"></a>Přezřizování kvóty svazků

Pokud je výkon úlohy vázaný na limit propustnosti, je možné převýšit kvótu svazku na nastavení vyšší úrovně propustnosti a dosáhnout vyššího výkonu.  

Pokud například svazek v úrovni Premium Storage má jenom 500 GiB dat, ale vyžaduje 128 MiB/s, můžete nastavit kvótu na 2 TiB, aby se úroveň propustnosti nastavila odpovídajícím způsobem (64 MiB/s za TB × 2 TiB = 128 MiB/s).  

Pokud jste trvale přezřídili svazek pro dosažení vyšší propustnosti, zvažte místo toho použití vyšší úrovně služby.  V předchozím příkladu můžete dosáhnout stejného limitu propustnosti s poloviční kvótou svazku pomocí úrovně Ultra Storage místo toho (128 MiB/s za TiB * 1 TiB = 128 MiB/s).

## <a name="dynamically-increasing-or-decreasing-volume-quota"></a>Dynamické zvýšení nebo snížení kvóty svazku

Pokud jsou vaše požadavky na výkon dočasná, nebo pokud jste zvýšili nároky na výkon po určitou dobu, můžete kvótu pro okamžitou úpravu limitu propustnosti dynamicky zvýšit nebo snížit.  Vezměte na vědomí následující skutečnosti: 

* Kvóta svazku se dá zvýšit nebo snížit, aniž by bylo nutné pozastavit v/v a přístup k tomuto svazku není přerušený ani ovlivněný.  

    Kvótu můžete upravit během aktivní vstupně-výstupní transakce se svazkem.  Počítejte s tím, že kvótu svazku nelze nikdy snížit pod množství logických dat, která jsou uložena ve svazku.

* Po změně kvóty svazku je odpovídající změna v limitu propustnosti skoro okamžitá. 

    Tato změna neovlivní nebo neovlivní přístup ke svazku nebo vstupně-výstupní operace.  

* Úprava kvóty svazku vyžaduje změnu velikosti fondu kapacity.  

    Velikost fondu kapacity se dá upravit dynamicky a aniž by to ovlivnilo dostupnost svazku nebo vstupně-výstupní operace.

## <a name="next-steps"></a>Další kroky

- [Úrovně služeb pro Azure NetApp Files](azure-netapp-files-service-levels.md)
- [Srovnávací testy výkonu pro Linux](performance-benchmarks-linux.md)