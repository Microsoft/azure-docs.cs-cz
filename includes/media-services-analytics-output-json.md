---
author: Juliako
ms.service: media-services
ms.topic: include
ms.date: 11/09/2018
ms.author: juliako
ms.openlocfilehash: 065cb4daa9501ee658d364dad43b9e03798e4083
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "67175421"
---
Úloha vytvoří výstupní soubor JSON obsahující metadata o zjištěných a sledovaných plochách. Metadata obsahují souřadnice označující umístění obličeje a také číslo ID obličeje, které signalizuje sledování daného jednotlivce. Pokud dojde ke ztrátě nebo překrytí čelní plochy v rámci tohoto rámce, je pro ně možné resetovat čísla ID obličeje. Výsledkem je, že někteří jednotlivci mají přiřazené více ID.

Výstupní JSON obsahuje následující prvky:

### <a name="root-json-elements"></a>Kořenové elementy JSON

| Element | Popis |
| --- | --- |
| verze |To odkazuje na verzi rozhraní API pro video. |
| měřítk |"Takty" za sekundu videa. |
| posun |Toto je časový posun pro časová razítka. Ve verzi 1,0 rozhraní API pro video bude tato hodnota vždycky 0. V budoucích scénářích, které podporujeme, se tato hodnota může změnit. |
| Šířka, vysoká |Šířka a výška výstupního snímku videa v pixelech.|
| snímků |Počet snímků ve videu za sekundu. |
| [svalovin](#fragments-json-elements) |Metadata jsou rozdělená do různých segmentů s názvem fragmenty. Každý fragment obsahuje začátek, dobu trvání, číslo intervalu a události. |

### <a name="fragments-json-elements"></a>Fragmenty – elementy JSON

|Element|Popis|
|---|---|
| start |Čas zahájení první události v "taktech". |
| doba trvání |Délka fragmentu v "taktech". |
| index | (Platí pouze pro Azure Media Redactor) definuje index rámce aktuální události. |
| interval |Interval každé položky události v rámci fragmentu v části "osové". |
| stránka events |Každá událost obsahuje plošky zjištěné a sledované během této doby trvání. Jedná se o pole událostí. Vnější pole představuje jeden časový interval. Vnitřní pole se skládá z 0 nebo více událostí, které se v tomto bodu v čase odehrály. Prázdná hranatá závorka [] znamená, že nebyly zjištěny žádné plošky. |
| id |ID plochy, která je sledována. Toto číslo se může nechtěně změnit, pokud se obličej nerozpozná. Daný jednotlivec by měl mít stejné ID v celém obrazovém videu, ale nelze ho zaručit v důsledku omezení algoritmu detekce (překrytí atd.). |
| x, y |Horní levý souřadnici X a Y ohraničovacího rámečku plochy v normalizovaném rozsahu od 0,0 do 1,0. <br/>– Souřadnice X a Y jsou relativní vzhledem k krajině na šířku, takže pokud máte video na výšku (nebo je v případě iOS mimo provoz), budete muset souřadnice odpovídajícím způsobem transponovat. |
| Šířka, Výška |Šířka a výška ohraničovacího rámečku obličeje v normalizovaném rozsahu od 0,0 do 1,0. |
| facesDetected |Tato funkce se nachází na konci výsledků JSON a shrnuje počet ploch, které algoritmus zjistil během videa. Vzhledem k tomu, že ID se dají nechtěně resetovat, pokud se obličej přestane detekovat (například tvář se zobrazí mimo obrazovku), toto číslo se nemusí vždy rovnat skutečnému počtu ploch ve videu. |
