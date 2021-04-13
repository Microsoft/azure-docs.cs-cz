---
title: Architektura údržby Predicative pro použití rozhraní lineární API detektoru anomálií
titleSuffix: Azure Cognitive Services
description: Referenční architektura pro použití rozhraní API detektoru anomálií lineární k použití detekce anomálií na data časových řad pro prediktivní údržbu.
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: conceptual
ms.date: 04/01/2021
ms.author: mbullwin
keywords: detekce anomálií, strojové učení, algoritmy
ms.openlocfilehash: 4d379cfe01dcbd88531b98a32b45e0b30f6adeef
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/13/2021
ms.locfileid: "107315475"
---
# <a name="predictive-maintenance-solution-with-anomaly-detector-multivariate"></a>Řešení prediktivní údržby s lineární detektorem anomálií

Mnohé různé obory potřebují řešení prediktivní údržby, aby se snížila rizika a získali užitečné poznatky prostřednictvím zpracování dat ze svých zařízení. Prediktivní údržba vyhodnocuje stav zařízení při provádění online monitorování. Cílem je provést údržbu před tím, než se zařízení sníží nebo rozdělí.

Monitorování stavu zařízení může být náročné, protože každá součást v rámci tohoto zařízení může generovat desítky signálů, například vibrace, orientaci a rotace.  To může být ještě složitější, pokud tyto signály mají implicitní vztah a je potřeba je monitorovat a analyzovat společně. Definování různých pravidel pro tyto signály a jejich vzájemné vzájemné vzájemné korelace může být nákladné. Funkce lineární detektoru anomálií umožňuje:

* Několik korelačních signálů, které se mají monitorovat dohromady, a mezi nimi jsou v modelu účty.
* V každé zachycené anomálii může být výsledkem příspěvku různých signálů, které vám pomůžou s vysvětlením anomálií a analýzou původní příčiny incidentů.
* Model detekce anomálií lineární je sestavený způsobem, který není pod dohledem. Modely je možné vyškolet specificky pro různé typy zařízení.

Tady poskytujeme referenční architekturu řešení prediktivní údržby založenou na lineární detektoru anomálií.

## <a name="reference-architecture"></a>Referenční architektura

[![Diagram architektury, který začíná na datech ze senzorů shromážděných na hraničních zařízeních, a sleduje kanál zpracování/analýzy až po spuštění detektoru anomálie na konci výstupu výstrahy incidentu. ](../media/multivariate-architecture/multivariate-architecture.png)](../media/multivariate-architecture/multivariate-architecture.png#lightbox)

V rámci výše uvedené architektury se události streamování ze senzorních dat uloží do Azure Data Lake a pak se zpracují modulem transformace dat, který se má převést na formát časové řady. Mezitím událost streamování spustí detekci v reálném čase s vycvičeným modelem. Obecně platí, že bude existovat modul pro správu životního cyklu modelu lineární, jako je například *přemostění služby* v této architektuře.

**Školení modelů**: před použitím lineární detektoru anomálií ke zjištění anomálií pro součást nebo zařízení. Musíme vyškolit model pro konkrétní signály (Time-Series) vygenerované touto entitou. *Služba mostu* načte historická data a odešle školicí úlohu do detektoru anomálií a potom zachová ID modelu v *meta úložišti modelu* .

**Ověření modelu**: doba trvání školení určitého modelu se může lišit v závislosti na objemu školicích dat. *Služba mostu* se může pravidelně dotazovat na stav modelu a diagnostické informace. Ověřování kvality modelu může být nutné před vložením do režimu online. Pokud ve scénáři existují popisky, dají se tyto popisky použít k ověření kvality modelu. V opačném případě se diagnostické informace dají použít k vyhodnocení kvality modelu a také můžete provést detekci historických dat pomocí trained model a vyhodnotit výsledek pro netest platnosti modelu.

**Odvození modelu**: online zjišťování bude provedeno s platným modelem a ID výsledku může být uloženo v *tabulce odvození*. Proces přípravy i proces odvození jsou prováděny asynchronním způsobem. Obecně platí, že úloha zjišťování může být dokončena během několika sekund. Signály využívané k detekci by měly být stejné jako při výuce. Pokud například používáme vibrace, orientaci a rotaci pro školení, při detekci by měly být jako vstup zahrnuty tři signály.

**Upozornění na incidenty** K výsledkům zjišťování se dá zadat dotaz s ID výsledku. Každý výsledek obsahuje závažnost každé anomálie a pořadí příspěvků. Pomocí pořadí příspěvků můžete pochopit, proč k této anomálii došlo, a signál, který tento incident způsobil. U závažnosti je možné nastavit různé prahové hodnoty, aby se generovaly výstrahy a oznámení, které se budou posílat technikům v terénu za účelem provádění údržby.

## <a name="next-steps"></a>Další kroky

- [Rychlé starty](../quickstarts/client-libraries-multivariate.md).
- [Osvědčené postupy](../concepts/best-practices-multivariate.md): Tento článek se týká doporučených vzorů pro použití s rozhraními API lineární.