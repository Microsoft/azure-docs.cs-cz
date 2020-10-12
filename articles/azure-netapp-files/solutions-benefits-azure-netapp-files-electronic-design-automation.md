---
title: Výhody použití Azure NetApp Files pro automatizaci elektronického návrhu | Microsoft Docs
description: Vysvětluje řešení Azure NetApp Files poskytuje pro splnění potřeb oboru pro návrh polovodičů a čipu. Prezentují scénáře testování, které používají standardní oborové srovnávací testy pro automatizaci elektronického návrhu (EDA) pomocí Azure NetApp Files.
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
ms.date: 04/24/2020
ms.author: b-juche
ms.openlocfilehash: fcede16619e8488796adc6f4c60af30643c1aadf
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "82160149"
---
# <a name="benefits-of-using-azure-netapp-files-for-electronic-design-automation"></a>Výhody použití Azure NetApp Files pro automatizaci elektronických návrhů

Doba uvedení na trh (TTM) je zásadním aspektem pro odvětví návrhu polovodičů a čipu. Obory mají vysokou šířku pásma a nízkou latenci pro úložiště. Tento článek vysvětluje, jak Azure NetApp Files řešení zajišťuje splnění potřeb odvětví. Představuje scénáře testů, které používají standardní oborové srovnávací testy pro automatizaci elektronického návrhu (EDA) pomocí Azure NetApp Files. 

## <a name="test-scenario-configurations"></a>Konfigurace testovacích scénářů

Testy zahrnují tři scénáře s následujícími konfiguracemi. 

|    Scénář    |    Svazky    |    Klienti<br> SLES15 D16s_v3  |
|----------------|---------------|--------------------------------|
|    Jednu         |    1          |    1                           |
|    Dvě         |    6          |    24                          |
|    Tři       |    12         |    24                          |

První scénář řeší, jak daleko může být řízený jeden svazek.  

Druhý a třetí scénář vyhodnotí omezení jednoho Azure NetApp Filesho koncového bodu. Prozkoumají možné výhody horní meze a latence v/v.

## <a name="test-scenario-results"></a>Výsledky testovacího scénáře

Následující tabulka shrnuje výsledky testovacích scénářů.

|    Scénář       |    Rychlost v/v<br>  2 MS     |    Rychlost v/v<br>  na hraničních zařízeních     |    Propustnost<br>  2 MS     |    Propustnost<br>  na hraničních zařízeních     |
|-------------------|---------------------------|--------------------------------|-----------------------------|----------------------------------|
|    1 svazek       |    39 601                 |    49 502                      |    soubory MiB 692/s                 |    soubory MiB 866/s                      |
|    6 svazků      |    255 613                |    317 000                     |    soubory MiB 4 577/s               |    soubory MiB 5 568/s                    |
|    12 svazků     |    256 612                |    319 196                     |    soubory MiB 4 577/s               |    soubory MiB 5 709/s                    |

Scénář s jedním svazkem představuje konfiguraci základní aplikace. Jedná se o základní scénář pro testovací scénáře pro následné testování.  

Scénář o šesti svazcích ukazuje lineární navýšení (600%) relativní ke úlohám s jedním svazkem.  Všechny svazky v rámci jedné virtuální sítě jsou dostupné přes jednu IP adresu.  

Scénář 12-Volume demonstruje celkový pokles latence v případě scénáře se šesti svazky. Ale nemá odpovídající nárůst dosažitelné propustnosti.   

Následující graf znázorňuje latenci a provozní rychlost pro EDA úlohy na Azure NetApp Files.  

![Latence a provozní frekvence pro EDA úlohy na Azure NetApp Files](../media/azure-netapp-files/solutions-electronic-design-automation-workload-latency-operation-rate.png)   

Následující graf znázorňuje latenci a propustnost pro EDA úlohy na Azure NetApp Files.  

![Latence a propustnost pro EDA úlohy na Azure NetApp Files](../media/azure-netapp-files/solutions-electronic-design-automation-workload-latency-throughput.png) 

## <a name="layout-of-test-scenarios"></a>Rozložení testovacích scénářů 

Následující tabulka shrnuje rozložení testovacích scénářů.

|    Scénář testu     |    Celkový počet adresářů     |    Celkový počet souborů     |
|----------------------|------------------------------------|------------------------------|
|    1 svazek          |    88 000                          |    880 000                   |
|    6 svazků         |    568 000                         |    5 680 000                 |
|    12 svazků        |    568 000                         |    5 680 000                 |

Kompletní zatížení je kombinace souběžně běžících funkčních a fyzických fází. Představuje typický tok z jedné sady EDA nástrojů na jiný.   

Funkční fáze se skládá z počáteční specifikace a logického návrhu. K fyzické fázi dochází, když je logický návrh převeden na fyzický čip. Během fáze odhlašování a odhlášení se dokončily závěrečné kontroly a návrh se doručí do slévárenského výrobního oddělení.  

Funkční fáze obsahuje kombinaci sekvenčního a náhodného vstupně-výstupních operací čtení a zápisu. Funkční fáze je náročné na metadata, jako je například File stat a Access calls. I když jsou operace s metadaty efektivně bez velikosti, rozsah operací čtení a zápisu mezi méně než 1 a a 16 KB. Většina čtení je mezi 4 a 16 K.  Většina zápisů je 4 KB nebo méně. Fyzická fáze se zcela skládá ze všech operací průběžného čtení a zápisu s kombinací velikosti 32 K a 64 K OP.  

Ve výše uvedených grafech je většina propustnosti ze sekvenční fyzické fáze úlohy. Vstup/výstup přichází z malé náhodné a funkční fáze náročné na metadata. Obě fáze probíhají paralelně. 

V závěru můžete párovat výpočetní prostředí Azure s Azure NetApp Files pro návrh EDA a získat tak škálovatelnou šířku pásma. 

## <a name="next-steps"></a>Další kroky

- [Architektury řešení s využitím Azure NetApp Files](azure-netapp-files-solution-architectures.md)
