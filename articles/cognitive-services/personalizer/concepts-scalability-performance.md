---
title: Škálovatelnost a přizpůsobení výkonu
titleSuffix: Azure Cognitive Services
description: 'Vysoce výkonné a vysoce náročné weby a aplikace mají dva hlavní faktory, které je potřeba zvážit v oblasti škálovatelnosti a výkonu: propustnost a školení.'
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 06/07/2019
ms.author: diberry
ms.openlocfilehash: d116f6bd389b1404ea723c965111cd05880e6c30
ms.sourcegitcommit: e3b0fb00b27e6d2696acf0b73c6ba05b74efcd85
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/30/2019
ms.locfileid: "68662824"
---
# <a name="scalability-and-performance"></a>Škálovatelnost a výkon

Vysoce výkonné a vysoce náročné weby a aplikace mají dva hlavní faktory, které je potřeba zvážit s možností přizpůsobovat škálovatelnost a výkon:

* Udržování nízké latence při vytváření volání rozhraní API pořadí
* Zajištění zajištění propustnosti školení pomocí vstupu události

Individuální nastavení může vracet pořadí velmi rychle, s většinou dobu trvání volání vyhrazenou pro komunikaci prostřednictvím REST API. Azure vám umožní rychle reagovat na požadavky.

##  <a name="low-latency-scenarios"></a>Scénáře s nízkou latencí

Některé aplikace vyžadují při vracení pořadí nízkou latenci. To je nezbytné:

* Aby uživatel mohl před zobrazením hodnoceného obsahu čekat na znatelné množství času.
* Aby bylo možné zajistit, že server má extrémní provoz, vyhněte se tomu omezených výpočetním časem a síťovým připojením.

<!--

If your web site is scaled on your infrastructure, you can avoid making HTTP calls by hosting the Personalizer API in your own servers running a Docker container.

This change would be transparent to your application, other than using an endpoint URL referring to the running docker instances as opposed to an online service in the cloud.



### Extreme Low Latency Scenarios

If you require latencies under a millisecond, and have already tested using Personalizer via containers, please contact our support team so we can assess your scenario and provide guidance suited to your needs.

-->

## <a name="scalability-and-training-throughput"></a>Škálovatelnost a propustnost školení

Přizpůsobení funguje tak, že aktualizuje model, který se překládá na základě zpráv odeslaných asynchronně pomocí přizpůsobeného rozhraní API pro řazení a odměnu. Tyto zprávy jsou odesílány pomocí služby Azure EventHub pro aplikaci.

 Nepravděpodobné, že většina aplikací dosáhne maximálního počtu připojení a školení k propustnosti přizpůsobeného. I když se toto maximum nezpomalí, může to znamenat, že se fronty centra událostí vyplňují rychleji, než se dají vyčistit.

## <a name="how-to-estimate-your-throughput-requirements"></a>Odhad požadavků na propustnost

* Odhadem průměrného počtu bajtů na událost řazení přidejte délky dokumentů JSON kontextu a akce.
* Rozdělte 20MB za sekundu za tento předpokládaný průměrný počet bajtů.

Pokud například vaše průměrná datová část obsahuje 500 funkcí a každá z nich má odhadované 20 znaků, pak je každá událost přibližně 10 KB. V těchto odhadech 20 000 000/10 000 = 2 000 události/s, což znamená o událostech 173 000 000 za den. 

Pokud dosáhnou těchto limitů, obraťte se na náš tým podpory, kde najdete doporučení pro architekturu.

## <a name="next-steps"></a>Další postup

[Vytvořte a nakonfigurujte](how-to-settings.md)přizpůsobeného přizpůsobování.