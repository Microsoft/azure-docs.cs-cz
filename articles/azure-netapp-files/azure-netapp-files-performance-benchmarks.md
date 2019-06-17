---
title: Srovnávací testy výkonu u souborů NetApp Azure | Dokumentace Microsoftu
description: Popisuje výsledky srovnávacích testů výkonu pro Azure Files NetApp na úrovni svazku.
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
ms.date: 04/22/2019
ms.author: b-juche
ms.openlocfilehash: aca0668fc364518fe45d9fe94d089ee366b25676
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "64870879"
---
# <a name="performance-benchmarks-for-azure-netapp-files"></a>Srovnávací testy výkonu pro Azure NetApp Files

Tento článek popisuje výsledky srovnávacích testů výkonu pro soubory Azure NetApp na úrovni svazku. 

## <a name="sample-application-used-for-the-tests"></a>Ukázkovou aplikací používanou pro testy

Testy výkonu nebyly spuštěny s ukázkovou aplikací pomocí souborů NetApp Azure. Aplikace má následující vlastnosti: 

* Aplikace založené na Linuxu vytvořená pro cloud
* Můžete se škálují lineárně s přidání virtuálních počítačů (VM) Chcete-li zvýšit výkon podle potřeby
* Vyžaduje rychlé usnadnění data lake
* Má vzory vstupů/výstupů, které jsou někdy náhodné a někdy sekvenční 
    * Náhodné vzor vyžaduje s nízkou latencí pro velké objemy vstupně-výstupních operací. 
    * Sekvenční vzor vyžaduje velkou šířku pásma. 

## <a name="about-the-workload-generator"></a>O generátor zatížení

Výsledky pocházet ze souborů souhrnů Vdbench. [Vdbench](https://www.oracle.com/technetwork/server-storage/vdbench-downloads-1901681.html) je nástroj příkazového řádku, který generuje diskové vstupně-výstupních operací úloh k ověření výkonu úložiště. Konfigurace klienta a serveru použitá je škálovatelný.  Obsahuje smíšený jeden hlavní server/klient a vyhrazený klientský 14 virtuálních počítačů.

## <a name="about-the-tests"></a>O testech

Testy byly navrženy k identifikaci omezení, která může mít ukázkovou aplikaci a doby odezvy této křivky až do omezení.  

Následující testy nebyly spuštěny: 

* 100 % 8 KiB náhodné čtení
* 100 % 8 KiB náhodného zápisu
* 100 % sekvenční 64 KiB čtení
* 100 % 64 KiB sekvenční zápisu
* Sekvenční čtení 64 KiB 50 %, 50 % 64 KiB sekvenční zápisu
* Čtení 8 KiB náhodné 50 %, 50 % 8 KiB náhodného zápisu

## <a name="bandwidth"></a>Šířka pásma

Služba soubory Azure NetApp nabízí několik [úrovně služby](azure-netapp-files-service-levels.md). Každá úroveň služeb nabízí různé šířku pásma za TiB zřízená kapacita (kvóty). Limit šířky pásma pro svazek se zřizuje, založené na kombinaci úrovně služby a kvóty svazku. Všimněte si, že limit šířky pásma je pouze jediný faktor při určování skutečného propustnost, které se dají realizovat.  

V současné době je 4 500 MiB nejvyšší propustnost, které bylo dosaženo úlohou na jednom svazku při testování.  S požadovanou úrovní aktualizace service Premium zřídí se svazek kvóta 70.31 TiB dostatečnou šířku pásma pro realizaci propustnost za výpočtu níže: 

![Vzorec šířky pásma](../media/azure-netapp-files/azure-netapp-files-bandwidth-formula.png)

![Kvóty a služba úrovně](../media/azure-netapp-files/azure-netapp-files-quota-service-level.png)

## <a name="throughput-intensive-workloads"></a>Úlohy náročné na propustnost

Propustnost test použil Vdbench a kombinaci 12xD32s V3 úložiště virtuálních počítačů. Ukázka svazku v testu dosáhnout následující čísla propustnost:

![Propustnost testu](../media/azure-netapp-files/azure-netapp-files-throughput-test.png)

## <a name="io-intensive-workloads"></a>Můžu intenzivních vstupně-výstupních operací

Vstupně-výstupní operace test použil Vdbench a kombinaci 12xD32s V3 úložiště virtuálních počítačů. Ukázka svazku v testu dosáhnout následující čísla vstupně-výstupních operací:

![Test vstupně-výstupních operací](../media/azure-netapp-files/azure-netapp-files-io-test.png)

## <a name="latency"></a>Latence

Vzdálenost mezi testovací virtuální počítače a svazek souborů NetApp Azure má vliv na výkon vstupně-výstupních operací.  Graf níže porovnává vstupně-výstupních operací a čekací doba odezvy křivky pro dvě různé sady virtuálních počítačů.  Jedna sada virtuálních počítačů je téměř NetApp soubory Azure a druhá sada je další okamžitě.  Všimněte si, že zvýší latence pro další sadu virtuálních počítačů má dopad na množství vstupně-výstupních operací dosáhnout na dané úrovni paralelismu.  Čtení na svazku bez ohledu na to, můžete překročit 300 000 vstupně-výstupních operací, jak je znázorněno níže: 

![Studie latence](../media/azure-netapp-files/azure-netapp-files-latency-study.png)

## <a name="summary"></a>Souhrn

Úlohy nízkou latenci (databáze) může mít jeden doba odezvy milisekund. Transakční výkon může být větší než 300 kB vstupně-výstupních operací pro samostatný svazek.

Citlivé na propustnost aplikace (pro streamování a vytvoření bitové kopie) může mít 4.5GiB / s propustností.
