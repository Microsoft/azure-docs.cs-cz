---
title: Technické specifikace a dodržování předpisů v oblasti Microsoft Azure Data Box Edge| Dokumenty společnosti Microsoft
description: Informace o technických specifikacích a dodržování předpisů pro azure data box edge
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 03/02/2020
ms.author: alkohli
ms.openlocfilehash: b646ee9b727d5adf4ec1c8b5c769b3d8f5c0fc1c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78252033"
---
# <a name="azure-data-box-edge-technical-specifications"></a>Technické specifikace Azure Data Box Edge

Hardwarové součásti vašeho zařízení Microsoft Azure Data Box Edge odpovídají technickým specifikacím a regulačním normám popsaným v tomto článku. Technické specifikace popisují napájecí zdroje (PSU), úložnou kapacitu, skříně a ekologické normy. 

## <a name="compute-memory-specifications"></a>Výpočetní výkon, specifikace paměti

Zařízení Data Box Edge má následující specifikace pro výpočetní prostředky a paměť:

| Specifikace           | Hodnota                  |
|-------------------------|----------------------------|
| Procesor    | 2 x 10 jádrový procesor                     |
| Memory (Paměť)              | 128 GB RAM                  |


## <a name="fpga-specifications"></a>Specifikace FPGA

Pole Programovatelné Gate Array (FPGA) je součástí každého zařízení Data Box Edge, který umožňuje scénáře strojového učení (ML). 

| Specifikace           | Hodnota                  |
|-------------------------|----------------------------|
| Fpga   | Intel Arria 10 <br> Dostupné modely Deep Neural Network (DNN) jsou stejné jako modely [podporované instancemi FPGA v cloudu](https://docs.microsoft.com/azure/machine-learning/how-to-deploy-fpga-web-service#whats-supported-on-azure).| 


## <a name="power-supply-unit-specifications"></a>Specifikace napájecího zdroje

Zařízení Data Box Edge má dva napájecí zdroje 100-240 V (PSU) s vysoce výkonnými ventilátory. Tyto dva napájecí zdroje poskytují redundantní konfiguraci napájení. Pokud psu selže, zařízení pokračuje v normálním provozu na druhém napájecím počítači, dokud není vadný modul vyměněn. V následující tabulce jsou uvedeny technické specifikace služeb pro používání služeb.

| Specifikace           | 750 W napájecího dechu                  |
|-------------------------|----------------------------|
| Maximální výstupní výkon    | 750 W                     |
| Frequency               | 50/60 Hz                   |
| Výběr rozsahu napětí | Auto v rozmezí: 100-240 V AC |
| Připojitelný za provozu           | Ano                        |

<!--## Power consumption statistics

The following table lists the typical power consumption data (actual values may vary from the published) for the Data Box Edge device.-->

## <a name="network-interface-specifications"></a>Specifikace síťového rozhraní

Zařízení You Data Box Edge má 6 síťových rozhraní, PORT1- PORT6.

| Specifikace           | Popis                 |
|-------------------------|----------------------------|
|  Síťová rozhraní    | 2× rozhraní 1 GbE, z nichž jedno slouží pro správu, uživatelé ho nemohou konfigurovat a používá se pro počáteční instalaci. Druhé rozhraní je uživatelsky konfigurovatelné, lze jej použít pro přenos dat a ve výchozím nastavení je DHCP. <br>2× rozhraní 25 GbE – mohou fungovat také jako rozhraní 10 GbE. Tato datová rozhraní mohou uživatelé konfigurovat jako DHCP (výchozí) nebo statická. <br> 2× rozhraní 25 GbE – tato datová rozhraní mohou uživatelé konfigurovat jako DHCP (výchozí) nebo statická.                  |

## <a name="storage-specifications"></a>Specifikace úložiště

Zařízení Data Box Edge mají 9 x 2,5" NVMe SSD, z nichž každá má kapacitu 1,6 TB. Z těchto SSD disků je 1 disk operačního systému a dalších 8 jsou datové disky. Celková využitelná kapacita zařízení je zhruba 12,5 TB. V následující tabulce jsou uvedeny podrobnosti o úložné kapacitě zařízení.

|     Specifikace                          |     Hodnota             |
|--------------------------------------------|-----------------------|
|    Počet ssd jednotek (SSD)     |    8                  |
|    Kapacita ssd s jedním ssd                     |    1,6 TB             |
|    Celková kapacita                          |    12,8 TB            |
|    Celková využitelná kapacita*                  |    ~ 12,5 TB            |

**Některé místo je vyhrazeno pro vnitřní použití.*

## <a name="enclosure-dimensions-and-weight-specifications"></a>Rozměry skříně a specifikace hmotnosti

V následujících tabulkách jsou uvedeny různé specifikace skříně pro rozměry a hmotnost.

### <a name="enclosure-dimensions"></a>Rozměry skříně

V následující tabulce jsou uvedeny rozměry skříně v milimetrech a palcích.

|     Kabiny     |     Milimetrů     |     Palců     |
|-------------------|---------------------|----------------|
|    Vlastnost Height         |    44.45            |    1.75"          |
|    impulzu          |    434.1           |    17.09"          |
|    Délka          |    740.4           |    29.15"          |

V následující tabulce jsou uvedeny rozměry přepravního balíku v milimetrech a palcích.

|     Balíček     |     Milimetrů     |     Palců     |
|-------------------|---------------------|----------------|
|    Vlastnost Height         |    311.2            |    12.25"          |
|    impulzu          |    642.8          |    25.31"          |
|    Délka          |   1,051.1          |    41.38"          |

### <a name="enclosure-weight"></a>Hmotnost krytu

Balíček zařízení váží 66 liber. a vyžaduje dvě osoby, aby se s ním vypořádaly. Hmotnost zařízení závisí na konfiguraci skříně.

|     Kabiny                                 |     Hmotnost          |
|-----------------------------------------------|---------------------|
|    Celková hmotnost včetně obalu       |    61 liber.          |
|    Hmotnost zařízení                       |    35 liber.          |

## <a name="enclosure-environment-specifications"></a>Specifikace prostředí skříně

V této části jsou uvedeny specifikace týkající se prostředí skříně, jako je teplota, vlhkost a nadmořská výška.

### <a name="temperature-and-humidity"></a>Teplota a vlhkost

|     Kabiny         |     Rozsah okolníteploty     |     Okolní relativní vlhkost     |     Maximální rosné místo     |
|-----------------------|--------------------------------------|--------------------------------------|---------------------------|
|    Operační        |    10°C - 35°C (50°F - 86°F)         |    10% - 80% nekondenzující.         |    29 °C (84 °F)            |
|    Neprovozní    |    -40 °C až 65 °C (-40°F - 149°F)     |    5% - 95% nekondenzující.          |    33 °C (91 °F)            |

### <a name="airflow-altitude-shock-vibration-orientation-safety-and-emc"></a>Proudění vzduchu, nadmořská výška, nárazy, vibrace, orientace, bezpečnost a EMC

|     Kabiny                           |     Provozní specifikace                                                                                                                                                                                         |
|-----------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
|    Proudění vzduchu                              |    Proudění vzduchu systému je zepředu dozadu. Systém musí být provozován s nízkotlakou instalací zadního výfuku. <!--Back pressure created by rack doors and obstacles should not exceed 5 pascals (0.5 mm water gauge).-->    |
|    Maximální nadmořská výška, provozní        |    3048 metrů (10 000 stop) s maximální provozní teplotou, která je dejmenovitá, stanovená [specifikacemi pro odměření provozní teploty](#operating-temperature-de-rating-specifications).                                                                                |
|    Maximální nadmořská výška, neprovozní    |    12 000 metrů                                                                                                                                                                                         |
|    Šok, provozní                   |    6 G po dobu 11 milisekund v 6 orientacích                                                                                                                                                                         |
|    Nárazový, nefunkční               |    71 G po dobu 2 milisekund v 6 orientacích                                                                                                                                                                           |
|    Vibrace, provozní               |    0,26 G<sub>RMS</sub> 5 Hz až 350 Hz náhodné                                                                                                                                                                                     |
|    Vibrace, neprovozní           |    1,88 G<sub>RMS</sub> 10 Hz až 500 Hz po dobu 15 minut (všech šest testovaných stran).)                                                                                                                                                  |
|    Orientace a montáž             |    19" držák do racku                                                                                                                                                                                        |
|    Bezpečnost a schválení                 |    EN 60950-1:2006 +A1:2010 +A2:2013 +A11:2009 +A12:2011/IEC 60950-1:2005 ed2 +A1:2009 +A2:2013 EN 62311:2008                                                                                                                                                                       |
|    Emc                                  |    FCC A, ICES-003 <br>EN 55032:2012/CISPR 32:2012  <br>EN 55032:2015/CISPR 32:2015  <br>EN 55024:2010 +A1:2015/CISPR 24:2010 +A1:2015  <br>EN 61000-3-2:2014/IEC 61000-3-2:2014 (třída D)   <br>EN 61000-3-3:2013/IEC 61000-3-3:2013                                                                                                                                                                                         |
|    Energetický sektor             |    Nařízení Komise (EU) č. 617/2013                                                                                                                                                                                        |
|    Rohs           |    EN 50581:2012                                                                                                                                                                                        |


### <a name="operating-temperature-de-rating-specifications"></a>Specifikace deratingní hodnoty provozní teploty

|     Odměření provozní teploty     |     Rozsah okolníteploty                                                         |
|--------------------------------------------|------------------------------------------------------------------------------------------|
|    Až 35 °C (95 °F)                       |    Maximální teplota je snížena o 1°C/300 m (1°F/547 ft) nad 950 m (3,117 ft).    |
|    35 °C až 40 °C (95 až 104 °F)            |    Maximální teplota je snížena o 1°C/175 m (1°F/319 ft) nad 950 m (3,117 ft).    |
|    40°C až 45°C (104°F až 113°F)           |    Maximální teplota je snížena o 1°C/125 m (1°F/228 ft) nad 950 m (3,117 ft).    |


## <a name="next-steps"></a>Další kroky

- [Nasazení azure data boxu Edge](data-box-edge-deploy-prep.md)
