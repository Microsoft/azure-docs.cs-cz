---
title: Důležité informace o výkonu souborů Azure NetApp | Dokumenty společnosti Microsoft
description: Popisuje aspekty výkonu pro soubory Azure NetApp.
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
ms.openlocfilehash: 97e3c6212edd2ade4eabb96db3543e9b3b68e2ae
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "67454136"
---
# <a name="performance-considerations-for-azure-netapp-files"></a>Aspekty výkonu pro Azure NetApp Files

[Limit propustnost](azure-netapp-files-service-levels.md) pro svazek je určen kombinací kvóty přiřazené ke svazku a vybrané úrovni služby. Při provádění plánů výkonu o soubory Azure NetApp, musíte pochopit několik aspektů. 

## <a name="quota-and-throughput"></a>Kvóta a propustnost  

Limit propustnost je pouze jeden determinant skutečného výkonu, který bude realizován.  

Typické aspekty výkonu úložiště, včetně kombinace čtení a zápisu, velikost přenosu, náhodné nebo sekvenční vzory a mnoho dalších faktorů přispěje k celkovému výkonu dodané.  

Maximální empirická propustnost, která byla pozorována při testování, je 4 500 MiB/s.  Na úrovni úložiště Premium bude objemová kvóta 70.31 TiB zřídit limit propustnost, který je dostatečně vysoký, aby bylo dosaženo této úrovně výkonu.  

Pokud uvažujete o přiřazení částek objemových kvót nad 70,31 TiB, může být ke svazku přiřazena další kvóta pro ukládání dalších dat. Přidaná kvóta však nebude mít za následek další zvýšení skutečné propustnost.  

Další informace najdete [v tématu Benchmarky výkonu pro soubory Azure NetApp.](azure-netapp-files-performance-benchmarks.md)

## <a name="overprovisioning-the-volume-quota"></a>Nadměrné zřizování hromadné kvóty

Pokud je výkon pracovního vytížení vázán na limit propustnosti, je možné nadurčitou kvótu svazku nastavit vyšší úroveň propustnosti a dosáhnout vyššího výkonu.  

Například pokud svazek ve vrstvě úložiště Premium má pouze 500 GiB dat, ale vyžaduje 128 MiB/s propustnost, můžete nastavit kvótu na 2 TiB tak, aby úroveň propustnost je nastavena odpovídajícím způsobem (64 MiB/s na TB * 2 TiB = 128 MiB/s).  

Pokud konzistentně přeznesete svazek pro dosažení vyšší propustnost, zvažte místo toho použití vyšší úrovně služeb.  Ve výše uvedeném příkladu můžete dosáhnout stejného limitu propustnosti s polovinou kvóty svazku pomocí úrovně úložiště Ultra místo (128 MiB/s na TiB * 1 TiB = 128 MiB/s).

## <a name="dynamically-increasing-or-decreasing-volume-quota"></a>Dynamicky se zvyšující nebo klesající objemová kvóta

Pokud jsou požadavky na výkon dočasné povahy nebo pokud máte zvýšené potřeby výkonu po pevně stanovenou dobu, můžete dynamicky zvýšit nebo snížit kvótu svazku a okamžitě upravit limit propustnost.  Všimněte si následujících úvah: 

* Kvóta svazku může být zvýšena nebo snížena bez nutnosti pozastavit viliky a přístup ke svazku není přerušennebo ovlivněn.  

    Kvótu můžete upravit během aktivní vstupně-v.a. transakce vůči svazku.  Všimněte si, že kvóta svazku nemůže být nikdy snížena pod množství logických dat uložených ve svazku.

* Při změně kvóty svazku je odpovídající změna limitu propustnosti téměř okamžitá. 

    Změna nepřeruší ani neovlivní přístup ke svazku nebo vstupně-va.  

* Úprava kvóty hlasitosti vyžaduje změnu velikosti fondu kapacity.  

    Velikost fondu kapacity lze upravit dynamicky a bez dopadu na dostupnost svazku nebo vstupně-va.

## <a name="next-steps"></a>Další kroky

- [Úrovně služeb pro Azure NetApp Files](azure-netapp-files-service-levels.md)
- [Srovnávací testy výkonu pro Azure NetApp Files](azure-netapp-files-performance-benchmarks.md)