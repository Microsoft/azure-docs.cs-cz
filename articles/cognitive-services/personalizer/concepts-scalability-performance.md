---
title: Škálovatelnost a výkon – personalizátor
titleSuffix: Azure Cognitive Services
description: 'Vysoce výkonné a vysoce přenosité weby a aplikace mají dva hlavní faktory, které je třeba zvážit s personalizámem pro škálovatelnost a výkon: latence a propustnost školení.'
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 10/24/2019
ms.author: diberry
ms.openlocfilehash: 5ac9a870cb05328f040febd0f8161a97f0982e09
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "73490773"
---
# <a name="scalability-and-performance"></a>Škálovatelnost a výkon

Vysoce výkonné a vysoce přenosité weby a aplikace mají dva hlavní faktory, které je třeba zvážit s personalizámem pro škálovatelnost a výkon:

* Zachování nízké latence při volání rozhraní API hodnocení
* Ujistěte se, že propustnost tréninku drží krok se vstupem události

Přizpůsobení můžete vrátit hodnost rychle, s většinou trvání volání vyhrazené pro komunikaci prostřednictvím rozhraní REST API. Azure automaticky škáluje schopnost rychle reagovat na požadavky.

##  <a name="low-latency-scenarios"></a>Scénáře s nízkou latencí

Některé aplikace vyžadují nízkou latenci při vrácení pořadí. Nízké latence jsou nezbytné:

* Chcete-li zabránit uživateli v čekání na znatelné množství času před zobrazením seřazeného obsahu.
* Chcete-li pomoci serveru, který zažívá extrémní provoz vyhnout vázání se vzácný výpočetní čas a síťová připojení.


## <a name="scalability-and-training-throughput"></a>Škálovatelnost a propustnost školení

Personalizátor funguje tak, že aktualizuje model, který je přetrénovaný na základě zpráv odeslaných asynchronně personalista po pořadí a odměny rozhraní API. Tyto zprávy se posílají pomocí Azure EventHub pro aplikaci.

 Je nepravděpodobné, že většina aplikací dosáhne maximální propustnosti propřizpůsobení a školení personalistu. Při dosažení tohoto maxima nebude zpomalovat aplikaci, znamenalo by to, že fronty centra událostí jsou stále vyplněny interně rychleji, než je lze vyčistit.

## <a name="how-to-estimate-your-throughput-requirements"></a>Jak odhadnout požadavky na propustnost

* Odhadněte průměrný počet bajtů na událost hodnocení, který přidá délky dokumentů JSON kontextu a akce.
* Vydělte 20 MB/s podle tohoto odhadovaného průměru bajtů.

Například pokud průměrná datová část má 500 funkcí a každý je odhadem 20 znaků, pak každá událost je přibližně 10 kb. S těmito odhady, 20,000,000 / 10,000 = 2,000 události / s, což je asi 173 milionů událostí / den. 

Pokud dosahujete těchto limitů, obraťte se na náš tým podpory pro radu s architekturou.

## <a name="next-steps"></a>Další kroky

[Vytvořte a nakonfigurujte personalizátku](how-to-settings.md).