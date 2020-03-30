---
title: Specifikace souboru Microsoft Azure FXT Edge Filer | Dokumenty společnosti Microsoft
description: Fyzické a environmentální specifikace hardwaru Azure FXT Edge Filer
author: ekpgh
ms.service: fxt-edge-filer
ms.topic: conceptual
ms.date: 06/20/2019
ms.author: rohogue
ms.openlocfilehash: c06b0c79e01257eebf566b9752269cb88c072d8a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79264723"
---
# <a name="azure-fxt-edge-filer-specifications"></a>Specifikace Azure FXT Edge Filer

Tento článek vysvětluje hardwarové specifikace hardwarových uzlů Azure FXT Edge Filer. V praxi jsou tři nebo více uzlů nakonfigurovány společně tak, aby poskytovaly systém clusterované mezipaměti.

## <a name="hardware-specifications"></a>Specifikace hardwaru

| Komponenta | FXT 6600 | FXT 6400 |
|----------|-----------|-----------|
| Procesorová jádra |  16 | 16 |
| Arménský  | 1536 GB | 768 GB |
| Síťové porty | 6 x 25/10 Gb + 2 x 1 Gb | 6 x 25/10 Gb + 2 x 1 Gb |
| Kapacita NVMe SSD | 25,6 TB | 12,8 TB |

## <a name="drive-specifications"></a>Specifikace jednotky

Systém má deset diskových pozic, přístupných zepředu. Každá naplněná jednotka je na pravé straně označena informacemi o kapacitě. 

Čísla jednotek jsou vytištěna na prostoru mezi jednotkami. V Azure FXT Edge Filer je jednotka 0 levá horní jednotka a jednotka 1 je přímo pod ní.

![fotografie jedné pozice na pevném disku v šasi FXT, zobrazující čísla disků a popisky kapacity](media/fxt-drives-photo.png)

| Čísla jednotek    |  Použití   |  Specifikace |
|------------------|--------|-----------------|
| 0, 1             | Operační systém     | 480 GB SSD SATA |
| 2, 3, 4, 5, 6, 7, 8, 9 | Data   | FXT 6600: 3,2 TB NVMe SSD <br> FXT 6400: 1,6 TB NVMe SSD |


## <a name="dimensions-and-weight"></a>Rozměry a hmotnost

Azure FXT Edge Filer je navržený tak, aby se vešel do standardního 19" racku a je o jednu rackovou jednotku vysokou (1U). 

<!-- 10x2.5 inches version -->

| Rozměry fileru           |                          |
|-----------------------------|--------------------------|
| Vlastnost Height                      | 42,8 mm (1,68 palců)    |
| Šířka (včetně rackových uší) | 482,0 mm (18,97 palců)  |
| Šířka - hlavní skříň      | 434,0 mm (17,08 palců) |
| Hloubka - rackové uši na zadní straně hlavního krytu                   | 733,82 mm (29,61 palců) |
| Hloubka - rackové uši pro nejvzdálenější zadní výčnělek                 | 772,67 mm (30,42 palců) |
| Hloubka - rackové uši k nejvzdálenějšímu přednímu výčnělku, bez rámečku | 22,0 mm (0,87 palců)  |
| Hloubka - rackové uši k nejvzdálenějšímu přednímu výčnělku, s rámečkem    | 35,84 mm (1,41 palců) |

| Hmotnost | |
|-----------------|----------------------|
| Hmotnost uzlu (bez obalu, bez příslušenství) | 48,1 kg (18,1 kg) |
| Čistá hmotnost (bez obalu, včetně příslušenství) | 53 liber (23,1 kg)|
| Hrubá hmotnost (v případě odeslání, včetně všech obalů) |  69 liber (29,0 kg) |

### <a name="shipping-dimensions"></a>Rozměry expedice

| Dimenze balíčku | Milimetrů | Palců |
|-------------------|-------------|--------|
| Vlastnost Height            | 311.2       | 12.25" |
| impulzu             | 642.8       | 25.31" |
| Délka            | 1,051.1     | 41.38" |

## <a name="power-and-thermal-specifications"></a>Specifikace výkonu a teploty

Tato část poskytuje hodnocení výkonu a měření pro Azure FXT Edge Filer.

### <a name="nameplate-ratings"></a>Hodnocení typové tabulky

| Hodnocení typových štítků pro modely řady FXT 6000 |
|----------------|
| 100 - 240V~    |
| 10A - 5A (X2)  |
| 50/60 Hz         |

<!-- matches the Dell regulatory label exactly -->

### <a name="power-and-thermal-measurements"></a>Měření výkonu a teploty 

Uzly Azure FXT Edge Filer používají ventilátory s proměnnými otáčkami, takže výkon závisí na teplotě a zatížení. Maximálních otáček ventilátoru lze dosáhnout při určitých kombinacích vysokého zatížení a zvýšených okolních teplot. 

Tyto grafy poskytují měření spotřeby energie a tepelného výkonu pro běžně používané kombinace napětí a frekvence. 

| Výkon FXT 6600 při pokojové teplotě <br />(22 °C, 71,6 °F) | 100 V, 60 Hz | 120 V, 60 Hz | 208 V, 60 Hz | 230 V, 50 Hz | 240 V, 50 Hz | 
|---------|---|---|---|---|---|
| Napětí (V) | 100 | 120 | 208 | 230 | 240 | 
| Frekvence (Hz) | 60 | 60 | 60 | 50 | 50 |
| Aktuální (A) | 5.02 | 4.16 |2.40 | 2.20 | 2.16 |
| Zdánlivý výkon (VA) | 502 | 499 | 499 | 506 | 518|
| Účiník | 0.99 | 0.99 |0.98 | 0.98 | 0.98 |
| Skutečný výkon (W) | 497 |494 | 489 | 496 | 508 |
| Teplotní rozptyl (BTU/Hr) |1696 | 1686 | 1 669 | 1692 | 1733 |

| Výkon FXT 6600 při maximálních otáčkách ventilátoru | 100 V, 60 Hz | 120 V, 60 Hz | 208 V, 60 Hz | 230 V, 50 Hz | 240 V, 50 Hz | 
|---------|---|---|---|---|---|
| Napětí (V) | 100 |120 | 208 | 230 | 240| 
| Frekvence (Hz) | 60 | 60 | 60 | 50 | 50 |
| Aktuální (A) | 5.98 | 5.01 | 2.81 | 2.55 | 2.48 |
| Zdánlivý výkon (VA) | 598 | 601 | 584 | 587 | 595 |
| Účiník | 0.99 | 0.99 | 0.98 | 0.98 | 0.98 |
| Skutečný výkon (W) | 592 | 595 | 573 | 575 | 583 |
| Teplotní rozptyl (BTU/Hr) | 2020 |2031 | 1954 | 1961 | 1990 |

| Výkon FXT 6400 při pokojové teplotě <br />(22 °C, 71,6 °F) | 100 V, 60 Hz | 120 V, 60 Hz | 208 V, 60 Hz | 230 V, 50 Hz | 240 V, 50 Hz | 
|---------|---|---|---|---|---|
| Napětí (V) | 100 | 120 | 208 | 230 | 240 |
| Frekvence (Hz) |60 | 60 | 60 | 50 | 50 |
| Aktuální (A) | 4.63 | 3.86 | 2.24 | 2.04 | 1.94 |
| Zdánlivý výkon (VA) | 463 | 463 | 466 | 469 | 466 |
| Účiník | 0.99 | 0.99 | 0.98 | 0.98 | 0.98 | 
| Skutečný výkon (W) | 458 | 459 | 457 | 460 | 456 |
| Teplotní rozptyl (BTU/Hr) | 1564 | 1565 | 1558 | 1569 | 1557 |

| Výkon FXT 6400 při maximálních otáčkách ventilátoru | 100 V, 60 Hz | 120 V, 60 Hz | 208 V, 60 Hz | 230 V, 50 Hz | 240 V, 50 Hz |
|---------|---|---|---|---|---|
| Napětí (V) | 100 | 120 | 208 | 230 | 240 |
| Frekvence (Hz) | 60 | 60 | 60 | 50 | 50 |
| Aktuální (A) | 5.15 | 4.28 | 2.48 | 2.28 | 2.13 |
| Zdánlivý výkon (VA) | 515 | 514 | 516 | 524 | 511 |
| Účiník | 0.99 | 0.99 | 0.98 | 0.98 | 0.98 |
| Skutečný výkon (W) | 510 | 508 | 506 | 514 | 501 |
| Teplotní rozptyl (BTU/Hr) | 1740 | 1735 | 1725 | 1753 | 1709 |

## <a name="environmental-requirements"></a>Požadavky na životní prostředí

Tato část obsahuje specifikace okolního prostředí hardwaru.

### <a name="temperature-and-humidity"></a>Teplota a vlhkost

| Atribut prostředí   | Provozní rozsah                   | Neprovozní rozsah         |
|---------------------------|-----------------------------------|-----------------------------|
| Rozsah okolníteploty | 10°C až 35°C (50 - 86°F)          | -40 °C až 65 °C (-40 - 149 °F) |
| Okolní relativní vlhkost | 10% - 80% nekondenzující          | 5% - 95% nekondenzující     |
| Maximální rosné místo         | 29 °C (84 °F)                       | 33 °C (91 °F)                 |
| Nadmořské výšce                  | až 3048 metrů (10 000 stop), s výhradou teplotního odstupňování uvedeného níže | až 12 000 metrů |

> [!NOTE] 
> **Odhodnocení teploty nadmořské výšky:** Maximální teplota je snížena o 1°C/300 m (1°F/547 ft) nad 950 m (3,117 ft).

### <a name="airflow-shock-and-vibration"></a>Proudění vzduchu, nárazy a vibrace 

| Atribut         | Specifikace |
|-------------------|---------------|
| Proudění vzduchu                    | Proudění vzduchu systému je zepředu dozadu. Systém musí být provozován s nízkotlakou zadní výfukovou instalací. |
| Šok, provozní         | 6 G po dobu 11 milisekund (testováno v 6 orientacích) |
| Nárazový, nefunkční     | 71 G po dobu 2 milisekund (testováno v 6 orientacích) |
| Vibrace, provozní     | 0,26 G<sub>RMS</sub> 5 Hz až 350 Hz náhodné         |
| Vibrace, neprovozní | 1,88 G<sub>RMS</sub> 10 Hz až 500 Hz po dobu 15 minut (všech šest testovaných stran)  |

## <a name="safety-regulation-compliance"></a>Dodržování bezpečnostních předpisů 

Azure FXT Edge Filer splňuje uvedené předpisy. 

| Kategorie       | Regulační specifikace | 
|----------------|--------------------------|
| Obecná bezpečnost | EN 60950-1:2006 +A1:2010 +A2:2013 +A11:2009 +A12:2011/IEC 60950-1:2005 ed2 +A1:2009 +A2:2013 <br>EN 62311:2008 | 
| Emc            | FCC A, ICES-003  <br>EN 55032:2012/CISPR 32:2012  <br>EN 55032:2015/CISPR 32:2015  <br>EN 55024:2010 +A1:2015/CISPR 24:2010 +A1:2015  <br>EN 61000-3-2:2014/IEC 61000-3-2:2014 (třída D)   <br>EN 61000-3-3:2013/IEC 61000-3-3:2013 |
| Energetický sektor         | Nařízení Komise (EU) č. 617/2013  |
| Rohs           |    EN 50581:2012   |