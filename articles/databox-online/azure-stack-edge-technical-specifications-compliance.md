---
title: Technické specifikace a dodržování předpisů pro Microsoft Azure Stack Edge pro | Microsoft Docs
description: Seznamte se s technickými specifikacemi a dodržováním předpisů pro Azure Stack Edge pro
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 03/02/2020
ms.author: alkohli
ms.openlocfilehash: 91aa386311452ae08ead2b8eac9005b2c730f3f3
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/22/2020
ms.locfileid: "90883445"
---
# <a name="azure-stack-edge-pro-technical-specifications"></a>Technické specifikace Azure Stack Edge pro

Hardwarové součásti zařízení Microsoft Azure Stack Edge pro vyhovují technickým specifikacím a regulativním normám, které jsou uvedené v tomto článku. Technické specifikace popisují jednotky zdroje napájení (PSUs), kapacitu úložiště, skříně a environmentální standardy.

## <a name="compute-memory-specifications"></a>Výpočetní prostředí, specifikace paměti

Zařízení Azure Stack Edge pro má následující specifikace pro výpočetní výkon a paměť:

| Specifikace           | Hodnota                  |
|-------------------------|----------------------------|
| Procesor    | PROCESOR 2 X 10 Core                     |
| Memory (Paměť)              | 128 GB RAM                  |

## <a name="fpga-specifications"></a>Specifikace FPGA

Pole programovatelné brány (FPGA) je součástí každého zařízení Azure Stack Edge pro, které umožňuje použití scénářů Machine Learning (ML).

| Specifikace           | Hodnota                  |
|-------------------------|----------------------------|
| FPGA   | Intel Arria 10 <br> Dostupné modely neuronové sítě (DNN) jsou stejné jako ty, které [podporuje instance cloudu FPGA](https://docs.microsoft.com/azure/machine-learning/how-to-deploy-fpga-web-service#whats-supported-on-azure).|

## <a name="power-supply-unit-specifications"></a>Specifikace jednotek napájení

Zařízení Azure Stack Edge pro má dvě 100-240 V jednotkách napájení (PSUs) s vysoce výkonnými ventilátory. Tato dvě PSUsa poskytují redundantní konfiguraci napájení. Pokud dojde k selhání PSU, zařízení bude nadále fungovat normálně na ostatních PSU, dokud se neúspěšně nenahradí modul. V následující tabulce jsou uvedeny technické specifikace PSUs.

| Specifikace           | 750 W PSU                  |
|-------------------------|----------------------------|
| Maximální výstupní výkon    | 750 W                     |
| Frequency               | 50/60 Hz                   |
| Výběr rozsahu napětí | Automatické rozsahy: 100-240 V AC |
| Horká, připojitelná           | Yes                        |

### <a name="azure-stack-edge-pro-power-cord-specifications-by-region"></a>Azure Stack specifikace napájecího kabelu pro Edge podle oblasti

Vaše zařízení Azure Stack Edge pro potřebuje napájecí kabel, který se liší v závislosti na vaší oblasti Azure.
Technické specifikace všech podporovaných napájecích kabelů najdete v článku [specifikace napájecích kabelů Azure Stack Edge pro podle oblasti](azure-stack-edge-technical-specifications-power-cords-regional.md).

<!--## Power consumption statistics

The following table lists the typical power consumption data (actual values may vary from the published) for the Azure Stack Edge Pro device.-->

## <a name="network-interface-specifications"></a>Specifikace síťového rozhraní

Vaše zařízení Azure Stack Edge pro má 6 síťových rozhraní PORT1-PORT6.

| Specifikace           | Description                 |
|-------------------------|----------------------------|
|  Síťová rozhraní    | 2× rozhraní 1 GbE, z nichž jedno slouží pro správu, uživatelé ho nemohou konfigurovat a používá se pro počáteční instalaci. Druhé rozhraní je uživatelsky konfigurovatelné, dá se použít k přenosu dat a je ve výchozím nastavení DHCP. <br>2× rozhraní 25 GbE – mohou fungovat také jako rozhraní 10 GbE. Tato datová rozhraní mohou uživatelé konfigurovat jako DHCP (výchozí) nebo statická. <br> 2× rozhraní 25 GbE – tato datová rozhraní mohou uživatelé konfigurovat jako DHCP (výchozí) nebo statická.                  |

## <a name="storage-specifications"></a>Specifikace úložiště

Zařízení Azure Stack Edge pro mají 9 X 2,5 "NVMe SSD, každé s kapacitou 1,6 TB. Z těchto SSD je 1 disk operačního systému a ostatní 8 jsou datové disky. Celková použitelná kapacita pro zařízení je zhruba 12,5 TB. Následující tabulka obsahuje podrobné informace o kapacitě úložiště zařízení.

|     Specifikace                          |     Hodnota             |
|--------------------------------------------|-----------------------|
|    Počet jednotek SSD (Solid-State Drive) (SSD)     |    8                  |
|    Jedna kapacita SSD                     |    1,6 TB             |
|    Celková kapacita                          |    12,8 TB            |
|    Celková použitelná kapacita *                  |    ~ 12,5 TB            |

**Místo pro interní použití je rezervované.*

## <a name="enclosure-dimensions-and-weight-specifications"></a>Rozměry skříně a specifikace váhy

V následujících tabulkách jsou uvedeny různé specifikace velikosti skříně pro rozměry a váhu.

### <a name="enclosure-dimensions"></a>Rozměry skříně

V následující tabulce jsou uvedeny rozměry skříně v milimetrech a palcích.

|     Skříně     |     Milimetrech     |     Cm     |
|-------------------|---------------------|----------------|
|    Height (Výška)         |    44,45            |    1,75 "          |
|    Width (Šířka)          |    434,1           |    17,09 "          |
|    Délka          |    740,4           |    29,15 "          |

V následující tabulce jsou uvedeny rozměry balíčku pro expedici v milimetrech a palcích.

|     Balíček     |     Milimetrech     |     Cm     |
|-------------------|---------------------|----------------|
|    Height (Výška)         |    311,2            |    12,25 "          |
|    Width (Šířka)          |    642,8          |    25,31 "          |
|    Délka          |   1 051,1          |    41,38 "          |

### <a name="enclosure-weight"></a>Váha skříně

Balíček zařízení má hmotnost 61 kg. a ke zpracování vyžaduje dvě osoby. Váha zařízení závisí na konfiguraci skříně.

|     Skříně                                 |     Hmotnost          |
|-----------------------------------------------|---------------------|
|    Celková váha včetně balení       |    61 kg          |
|    Váha zařízení                       |    35 kg          |

## <a name="enclosure-environment-specifications"></a>Specifikace prostředí skříně

V této části jsou uvedeny specifikace týkající se prostředí skříně, jako je teplota, vlhkost a nadmořská výška.

### <a name="temperature-and-humidity"></a>Teplota a vlhkost

|     Skříně         |     Rozsah okolních teplot     |     Relativní vlhkost okolí     |     Maximální bod Dew     |
|-----------------------|--------------------------------------|--------------------------------------|---------------------------|
|    Provoz        |    10 °C-35 OC (50 °F-86 °F)         |    10% až 80% nekondenzující.         |    29 °C (84 °F)            |
|    Není funkční    |    -40 oC až 65 oC (-40 °F-149 °F)     |    5% až 95% nekondenzující.          |    33 OC (91 °F)            |

### <a name="airflow-altitude-shock-vibration-orientation-safety-and-emc"></a>Provozní flow, nadmořská, náraz, vibrace, orientace, bezpečnost a EMC

|     Skříně                           |     Provozní specifikace                                                                                                                                                                                         |
|-----------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
|    Tok dat                              |    Tok systému je zepředu zezadu. Systém musí být provozován s nízkým tlakem na zadní výfukovou instalaci. <!--Back pressure created by rack doors and obstacles should not exceed 5 pascals (0.5 mm water gauge).-->    |
|    Maximální výška, provozní        |    3048 měřičů (10 000 metrů) s maximální provozní teplotou rozhodnocenou [specifikací pro stanovení provozní teploty](#operating-temperature-de-rating-specifications).                                                                                |
|    Maximální nadlimitní hodnota, která není funkční    |    12 000 měřičů (39 370 metrů)                                                                                                                                                                                         |
|    Úraz, provozní                   |    6 G až 11 milisekund v 6 orientaci                                                                                                                                                                         |
|    Úraz, jiný než provozní               |    71 G na 2 milisekundy v 6 orientaci                                                                                                                                                                           |
|    Vibrace, provozní               |    0,26 G<sub>RMS</sub> 5 Hz až 350 Hz náhodně                                                                                                                                                                                     |
|    Vibrace, jiné než provozní           |    1,88 G<sub>RMS</sub> 10 Hz až 500 Hz po dobu 15 minut (všech šest stran testováno.)                                                                                                                                                  |
|    Orientace a připojení             |    19 připojení stojanu                                                                                                                                                                                        |
|    Bezpečnost a schválení                 |    EN 60950-1:2006 + a1:2010 + a2:2013 + A11:2009 + A12:2011/IEC 60950-1:2005 ED2 + a1:2009 + a2:2013 EN 62311:2008                                                                                                                                                                       |
|    SOFTWARE                                  |    FCC A, ICES – 003 <br>EN 55032:2012/CISPR 32:2012  <br>EN 55032:2015/CISPR 32:2015  <br>EN 55024:2010 + A1:2015/CISPR 24:2010 + A1:2015  <br>EN 61000-3-2:2014/IEC 61000-3-2:2014 (třída D)   <br>EN 61000-3-3:2013/IEC 61000-3-3:2013                                                                                                                                                                                         |
|    Energetický sektor             |    Nařízení Komise (EU) – ne. 617/2013                                                                                                                                                                                        |
|    RoHS           |    EN 50581:2012                                                                                                                                                                                        |

### <a name="operating-temperature-de-rating-specifications"></a>Specifikace pro dehodnocení provozní teploty

|     Dehodnocení provozní teploty     |     Rozsah okolních teplot                                                         |
|--------------------------------------------|------------------------------------------------------------------------------------------|
|    Až 35 oC (95 °F)                       |    Maximální teplota se sníží o 1 °C/300 m (1 °F/547 ft) nad 950 m (3 117 ft).    |
|    35 oC až 40 oC (95 °F až 104 °F)            |    Maximální teplota se zmenší o 1 °C/175 m (1 °F/319 ft) nad 950 m (3 117 ft).    |
|    40 oC až 45 °C (104 °F až 113 °F)           |    Maximální teplota se sníží o 1 °C/125 m (1 °F/228 ft) nad 950 m (3 117 ft).    |

## <a name="next-steps"></a>Další kroky

- [Nasazení Azure Stack Edge pro](azure-stack-edge-deploy-prep.md)
