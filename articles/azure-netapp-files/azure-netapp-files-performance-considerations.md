---
title: Faktory ovlivňující výkon u souborů NetApp Azure | Dokumentace Microsoftu
description: Popisuje faktory ovlivňující výkon u souborů NetApp Azure.
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
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/28/2019
ms.locfileid: "67454136"
---
# <a name="performance-considerations-for-azure-netapp-files"></a>Aspekty výkonu pro Azure NetApp Files

[Limit propustnosti](azure-netapp-files-service-levels.md) pro svazek je dáno kombinaci kvóty přiřadí svazku a vybraná úroveň služby. Pokud provedete výkonu plánů o službě soubory Azure NetApp, je potřeba pochopit, třeba mít na paměti. 

## <a name="quota-and-throughput"></a>Kvóty a propustnosti  

Limit propustnosti je pouze jeden považuje za klíčový faktor skutečný výkon, které se dají realizovat.  

Důležité informace o výkonu typické úložiště, včetně čtení a zápis kombinace, velikost přenosu, náhodných nebo sekvenčních vzory a řadu dalších faktorů přispěje k doručení celkový výkon.  

Maximální propustnost empirical, která byla zjištěna v testování je 4 500 MiB/s.  Na úrovni Premium storage zřídí se svazek kvóta 70.31 TiB omezení propustnosti, který je dostatečně vysoká, aby této úrovně výkonu.  

Pokud uvažujete o přiřazení svazku kvóty částky za 70.31 TiB, může být přiřazen další kvótu na svazek pro uložení další data. Ale přidání kvóty opozdí další zvýšení Skutečná propustnost.  

Zobrazit [srovnávacího testu výkonu pro soubory Azure NetApp](azure-netapp-files-performance-benchmarks.md) pro další informace.

## <a name="overprovisioning-the-volume-quota"></a>Předimenzování kvóta svazku

Pokud výkon úlohy, která je vázána limit propustnosti, je možné za cenu značných kvóta svazku nastavit vyšší úroveň propustnosti a dosahovat vyšších výkonů.  

Například pokud svazek na úrovni Premium storage má pouze 500 GB dat, ale vyžaduje 128 MiB/s propustnosti, můžete nastavit kvótu na 2 TB tak, aby je odpovídajícím způsobem nastavit úroveň propustnosti (64 MiB/s za TB * 2 TB = 128 MiB/s).  

Pokud konzistentně značných svazku pro dosažení vyšší propustnost, zvažte místo toho použití vyšší úroveň služby.  V předchozím příkladu můžete dosáhnout stejné omezení propustnosti se polovina kvóta svazku pomocí úrovně Ultra úložiště (128 MiB/s na TiB * 1 TB = 128 MiB/s).

## <a name="dynamically-increasing-or-decreasing-volume-quota"></a>Dynamicky zvýšením nebo snížením kvóty

Pokud jsou vaše požadavky na výkon ve své podstatě dočasné, nebo pokud jste zvýšili požadavkům na výkon pro pevné časové období, můžete dynamicky zvětšit nebo zmenšit kvóty hned. už nechtějí upravit limit propustnosti.  Mějte na paměti následující aspekty: 

* Kvóty můžou zvýší nebo sníží bez nutnosti pozastavit vstupně-výstupní operace a přístup ke svazku není přerušena ani vliv.  

    Upravit kvótu během aktivní transakci vstupně-výstupních operací na svazku.  Všimněte si, že této kvóty můžou snížit nikdy pod množství logických dat, která je uložena ve svazku.

* Při změně kvóty je téměř okamžité odpovídající změnu v hodnotě limit propustnosti. 

    Tato změna nemá přerušení nebo mít vliv na přístup ke svazku nebo vstupně-výstupních operací.  

* Kvóty přizpůsobení vyžaduje změnu velikosti kapacity fondu.  

    Velikost kapacity fondu je možné upravit dynamicky a bez dopadu na dostupnost svazku nebo vstupně-výstupních operací.

## <a name="next-steps"></a>Další postup

- [Úrovně služeb pro soubory Azure NetApp](azure-netapp-files-service-levels.md)
- [Srovnávací testy výkonu pro soubory Azure NetApp](azure-netapp-files-performance-benchmarks.md)