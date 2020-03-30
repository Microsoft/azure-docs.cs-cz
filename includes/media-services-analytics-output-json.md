---
author: Juliako
ms.service: media-services
ms.topic: include
ms.date: 11/09/2018
ms.author: juliako
ms.openlocfilehash: 065cb4daa9501ee658d364dad43b9e03798e4083
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "67175421"
---
Úloha vytvoří výstupní soubor JSON, který obsahuje metadata o zjištěných a sledovaných plochách. Metadata obsahují souřadnice označující umístění ploch a číslo ID plochy označující sledování této osoby. Čísla Face ID jsou náchylná k resetování za okolností, kdy dojde ke ztrátě nebo překrytí čelní plochy v rámečku, což vede k tomu, že některým osobám je přiřazeno více ID.

Výstup JSON obsahuje následující prvky:

### <a name="root-json-elements"></a>Kořenové prvky JSON

| Element | Popis |
| --- | --- |
| version |To se vztahuje k verzi rozhraní API pro video. |
| Časové osy |"Klíšťata" za sekundu videa. |
| posun |Toto je časový posun pro časová razítka. Ve verzi 1.0 video API to bude vždy 0. V budoucích scénářích, které podporujeme, se tato hodnota může změnit. |
| šířka, výška |Šířka a výška výstupního snímku videa v obrazových bodech.|
| Framerate |Počet snímků ve videu za sekundu. |
| [Fragmenty](#fragments-json-elements) |Metadata jsou rozdělena do různých segmentů nazývaných fragmenty. Každý fragment obsahuje začátek, dobu trvání, číslo intervalu a události. |

### <a name="fragments-json-elements"></a>Fragmenty prvků JSON

|Element|Popis|
|---|---|
| start |Čas zahájení první události v "tiky". |
| doba trvání |Délka fragmentu v "klíšťají". |
| index | (Platí pouze pro orbor médií Azure) definuje index rámce aktuální události. |
| interval |Interval každé položky události v rámci fragmentu v "značky." |
| stránka events |Každá událost obsahuje zjištěné a sledované plochy během této doby. Jedná se o řadu událostí. Vnější pole představuje jeden časový interval. Vnitřní pole se skládá z 0 nebo více událostí, které se v tomto bodu v čase odehrály. Prázdná závorka [] znamená, že nebyly zjištěny žádné tváře. |
| id |ID obličeje, který je sledován. Toto číslo se může neúmyslně změnit, pokud se plocha stane nezjištěnou. Daný jedinec by měl mít stejné ID v celém videu, ale to nelze zaručit kvůli omezením v detekčním algoritmu (okluze atd.). |
| x, y |Souřadnice Ohraničující plochy vlevo nahoře X a Y v normalizované stupnici 0,0 až 1,0. <br/>-Souřadnice X a Y jsou vždy relativní k krajině, takže pokud máte video s portrétem (nebo vzhůru nohama, v případě iOS), budete muset odpovídajícím způsobem provést souřadnice. |
| šířka, výška |Šířka a výška rámečku čelního ohraničování v normalizované stupnici 0,0 až 1,0. |
| facesDetected |To se nachází na konci výsledků JSON a shrnuje počet tváří, které algoritmus zjištěných během videa. Vzhledem k tomu, že ID lze obnovit neúmyslně, pokud se plocha stane nezjištěnou (např. obličej se vypne, vypadá pryč), nemusí se toto číslo vždy rovnat skutečnému počtu tváří ve videu. |
