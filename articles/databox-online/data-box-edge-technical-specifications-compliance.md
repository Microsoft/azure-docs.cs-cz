---
title: Microsoft Azure Data Box Edge technické specifikace a dodržování předpisů | Dokumentace Microsoftu
description: Další informace o technické specifikace a dodržování předpisů pro váš Azure Data Box Edge
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 03/25/2019
ms.author: alkohli
ms.openlocfilehash: 52fb32a8b34c62fe94ab35e2c051d996ab8bef10
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "60755130"
---
# <a name="azure-data-box-edge-technical-specifications"></a>Azure Data Box Edge technických specifikací

Hardwarové komponenty Microsoft Azure Data Box hraniční zařízení naprogramujete dodržovat technické specifikace a zákonných norem uvedených v tomto článku. Technické specifikace popisují Power dodavatelského jednotek (PSUs), kapacita úložiště, skříně a prostředí standardy. 

## <a name="power-supply-unit-specifications"></a>Specifikace jednotky dodavatelského napájení

Zařízení Data Box Edge má dvě, 100 240 V Power a dodávky jednotky (PSUs) s vysoce výkonné fanoušků. Dvě PSUs poskytují redundantní napájení konfiguraci. Pokud se nezdaří PSU, zařízení bude fungovat normálně dál na jiné PSU dokud se nahradí modulu se nezdařilo. V následující tabulce jsou uvedeny technických specifikací PSUs.

| Specifikace           | 750 W PSU                  |
|-------------------------|----------------------------|
| Maximální výstupní výkon    | 750 W                     |
| Frekvence               | 50/60 Hz                   |
| Výběr rozsahu napětí | Automatické rozsahu: 100-240 V AC |
| Horká modulární           | Ano                        |

<!--## Power consumption statistics

The following table lists the typical power consumption data (actual values may vary from the published) for the Data Box Edge device.-->

## <a name="storage-specifications"></a>Specifikace úložiště

Data Box hraničních zařízeních mít 10 X 2,5" NVMe SSD disků, každý s kapacitou 1.6 TB. Z těchto disků SSD 2 jsou disky operačního systému a 8 jsou datové disky. Celková kapacita použitelný pro toto zařízení je přibližně 12,5 TB. Následující tabulka obsahuje podrobnosti o za kapacitu úložiště zařízení.

|     Specifikace                          |     Hodnota             |
|--------------------------------------------|-----------------------|
|    Počet jednotek SSD (Solid-State Drive)     |    8                  |
|    Jeden kapacitu disků SSD                     |    1,6 TB             |
|    Celková kapacita                          |    12.8 TB            |
|    Celkový počet použitelné kapacity *                  |    ~ 12,5 TB            |

**Místo na disku je rezervovaná pro interní použití.*

## <a name="enclosure-dimensions-and-weight-specifications"></a>Dimenze skříň a váhy specifikace

Následující tabulky uvádějí různé specifikace skříň pro dimenze a váhy záznamu.

### <a name="enclosure-dimensions"></a>Dimenze skříň

V následující tabulce jsou uvedeny dimenze skříň v milimetrech a palců.

|     Skříň     |     Milimetrech     |     Palců     |
|-------------------|---------------------|----------------|
|    Výška         |    44.45            |    1.75"          |
|    Šířka          |    434.1           |    17.09"          |
|    Délka          |    740.4           |    29.15"          |

V následující tabulce jsou uvedeny dimenze přesouvání balíčku v milimetrech a palců.

|     Balíček     |     Milimetrech     |     Palců     |
|-------------------|---------------------|----------------|
|    Výška         |    311.2            |    12.25"          |
|    Šířka          |    642.8          |    25.31"          |
|    Délka          |   1,051.1          |    41.38"          |

### <a name="enclosure-weight"></a>Váha skříň

Balíček zařízení váží 66 lbs. a vyžaduje dvě osoby, aby to zvládnul. Váha zařízení závisí na konfiguraci přílohu.

|     Skříň                                 |     Hmotnost          |
|-----------------------------------------------|---------------------|
|    Celková váha včetně balení       |    61 lbs.          |
|    Váha zařízení                       |    35 lbs.          |

## <a name="enclosure-environment-specifications"></a>Specifikace prostředí skříň

Tato část uvádí požadavky související s skříň prostředí jako je například teploty, vlhkosti a výšku.

### <a name="temperature-and-humidity"></a>Teploty a vlhkosti

|     Skříň         |     Rozsah okolní teploty     |     Ambientní relativní vlhkosti     |     Maximální sráží bodu     |
|-----------------------|--------------------------------------|--------------------------------------|---------------------------|
|    Provozní        |    10° C - 35° C (50° F - 86° F)         |    10-80 % nekondenzující.         |    29° C (84° F)            |
|    Nefunkční    |    OC-40 až 65 ° C (-40 ° F - 149 ° F)     |    5 – 95 % nekondenzující.          |    33°C (91°F)            |

### <a name="airflow-altitude-shock-vibration-orientation-safety-and-emc"></a>Vzduchu, výšku, otřást, vibrace, orientace, zabezpečení a EMC

|     Skříň                           |     Provozní specifikace                                                                                                                                                                                         |
|-----------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
|    Vzduchu                              |    Systém vzduchu je přední, zadní. Systém musí provozovat s Nízkotlaké zadní výfukového instalace. <!--Back pressure created by rack doors and obstacles should not exceed 5 pascals (0.5 mm water gauge).-->    |
|    Maximální výška, provozní        |    určená 3048 měřiče (10 000 nohou) s maximální provozní teploty zrušit hodnocení [provozní teplota zrušit hodnocení specifikace](#operating-temperature-de-rating-specifications).                                                                                |
|    Maximální výška, nefunkční    |    12 000 měřiče (39,370 nohou)                                                                                                                                                                                         |
|    Otřást provozní                   |    6 G 11 milisekund v 6 orientace                                                                                                                                                                         |
|    Otřást nefunkční               |    71 G, 2 milisekund v 6 orientace                                                                                                                                                                           |
|    Vibrace, provozní               |    0,26 G<sub>RMS</sub> 5 Hz na 350 Hz náhodné                                                                                                                                                                                     |
|    Vibrace, nefunkční           |    1,88 G<sub>RMS</sub> Hz 10 až 500 Hz 15 minut (všech šesti stranách testovány.)                                                                                                                                                  |
|    Orientace a připojení             |    19" namontovat                                                                                                                                                                                        |
|    Bezpečnost a schválení                 |    EN 60950-1:2006 +A1:2010 +A2:2013 +A11:2009 +A12:2011/IEC 60950-1:2005 ed2 +A1:2009 +A2:2013 EN 62311:2008                                                                                                                                                                       |
|    EMC                                  |    FCC A, ICES-003 <br>EN 55032:2012/CISPR 32:2012  <br>EN 55032:2015/CISPR 32:2015  <br>EN 55024:2010 +A1:2015/CISPR 24:2010 +A1:2015  <br>CS 61000-3-2:2014 / IEC 61000-3-2:2014 (třídy D)   <br>EN 61000-3-3:2013/IEC 61000-3-3:2013                                                                                                                                                                                         |
|    Energetický sektor             |    Ne. nařízení (EU) Komise 617/2013                                                                                                                                                                                        |
|    RoHS           |    EN 50581:2012                                                                                                                                                                                        |


### <a name="operating-temperature-de-rating-specifications"></a>Provozní teplota zrušit hodnocení specifikace

|     Provozní teploty zrušit hodnocení     |     Rozsah okolní teploty                                                         |
|--------------------------------------------|------------------------------------------------------------------------------------------|
|    Až po 35 ° C (95° F)                       |    Nejvyšší teplota se snižuje podle oC 1/300 m (1° F/547 ft) nad 950 m (3,117 ft).    |
|    35° C až 40° C (95° F 104° f)            |    Nejvyšší teplota se snižuje podle oC 1/175 m (1° F/319 ft) nad 950 m (3,117 ft).    |
|    40° C až 45° C (104° F 113° f)           |    Nejvyšší teplota se snižuje podle oC 1/125 m (1° F/228 ft) nad 950 m (3,117 ft).    |


## <a name="next-steps"></a>Další postup

- [Nasazení Azure Data Box hranici](data-box-edge-deploy-prep.md)
