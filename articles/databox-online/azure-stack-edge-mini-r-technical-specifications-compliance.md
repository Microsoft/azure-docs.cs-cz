---
title: Microsoft Azure Stackch Mini R Technical Specification a dodržování předpisů | Microsoft Docs
description: Seznamte se s technickými specifikacemi a dodržováním předpisů pro Azure Stack hraničních zařízení v R.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: conceptual
ms.date: 03/01/2021
ms.author: alkohli
ms.openlocfilehash: 3a0b87f04e60fd56d543c7c7a752cd788e087c78
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "101727477"
---
# <a name="azure-stack-edge-mini-r-technical-specifications"></a>Azure Stack – Mini – technické specifikace R

Hardwarové součásti Microsoft Azure Stack hraničního zařízení v rámci programu R vyhovují technickým specifikacím a regulativním normám uvedeným v tomto článku. Technické specifikace popisují procesor, paměť, jednotky napájení (PSUs), kapacitu úložiště, rozměry skříně a váhu.


## <a name="compute-memory-specifications"></a>Výpočetní prostředí, specifikace paměti

Azure Stack hraničních zařízení v jazyce R obsahuje následující specifikace pro výpočetní výkon a paměť:

| Specifikace           | Hodnota                  |
|-------------------------|------------------------|
| Procesor    | PROCESOR s 16 jádry, Intel Xeon-D 1577 |
| Memory (Paměť)              | 48 GB RAM (2400 MT/s)                  |


## <a name="compute-acceleration-specifications"></a>Specifikace zrychlení výpočtů

VPU (Vision Processing Unit) je součástí každé Azure Stack hraničního zařízení R, které umožňuje Kubernetes, rozsáhlou neuronové síť a aplikace založené na počítačové vidění.

| Specifikace           | Hodnota                  |
|-------------------------|------------------------|
| Karta výpočetní akcelerace         | Intel Movidius nesčetných X VPU <br> Další informace najdete v tématu [Intel Movidius nesčetných X VPU](https://www.movidius.com/MyriadX) |


## <a name="storage-specifications"></a>Specifikace úložiště

Azure Stack hraničních zařízení R má 1 datový disk a 1 spouštěcí disk (který slouží jako úložiště operačního systému). V následující tabulce jsou uvedeny podrobnosti o kapacitě úložiště zařízení.

|     Specifikace                          |     Hodnota             |
|--------------------------------------------|-----------------------|
|    Počet jednotek SSD (Solid-State Drive) (SSD)     |    2 × 1 TB disků <br> Jeden datový disk a jeden spouštěcí disk                  |
|    Jedna kapacita SSD                     |    1 TB               |
|    Celková kapacita (jenom data)              |    1 TB              |
|    Celková použitelná kapacita *                  |    ~ 750 GB        |

**Místo pro interní použití je rezervované.*

## <a name="network-specifications"></a>Specifikace sítě

Azure Stack hraničních zařízení v jazyce R obsahuje následující specifikace pro síť:


|Specifikace  |Hodnota  |
|---------|---------|
|Síťová rozhraní    |2 x 10 GbE SFP + <br> Zobrazuje se jako PORT 3 a PORT 4 v místním uživatelském rozhraní.           |
|Síťová rozhraní    |2 x 1 GbE RJ45 <br> Zobrazuje se jako PORT 1 a PORT 2 v místním uživatelském rozhraní.          |
|Wi-Fi   |802.11ac         |


## <a name="power-supply-unit-specifications"></a>Specifikace jednotek napájení

Azure Stack hraničních zařízení v jazyce R obsahuje externí adaptér 85 W AC, který umožňuje napájení a nabití baterie na desce.

V následující tabulce jsou uvedeny specifikace jednotek napájení:

| Specifikace           | Hodnota                      |
|-------------------------|----------------------------|
| Maximální výstupní výkon    | 85 W                       |
| Frekvence               | 50/60 Hz                   |
| Výběr rozsahu napětí | Automatické rozsahy: 100-240 V AC |



## <a name="included-battery"></a>Zahrnutá baterie

Azure Stack hraničních zařízení R navíc zahrnuje baterii, která se účtuje zdrojem napájení.

Další [Typ baterie 2590](https://www.bren-tronics.com/bt-70791ck.html) se dá použít ve spojení s baterií připojení a prodloužit tak používání zařízení mezi poplatky. Tato baterie by měla být v souladu se všemi předpisy pro bezpečnost, přepravu a prostředí, které jsou použitelné v zemi použití.


| Specifikace           | Hodnota                      |
|-------------------------|----------------------------|
| Kapacita baterie | 73 Wh                    |

## <a name="enclosure-dimensions-and-weight-specifications"></a>Rozměry skříně a specifikace váhy

V následujících tabulkách jsou uvedeny různé specifikace velikosti skříně pro rozměry a váhu.

### <a name="enclosure-dimensions"></a>Rozměry skříně

V následující tabulce jsou uvedeny rozměry zařízení a USP s robustním případem v milimetrech a palcích.

|     Skříně     |     Milimetrech     |     Cm     |
|-------------------|---------------------|----------------|
|    Height (Výška)         |    68            |    2,68          |
|    Width (Šířka)          |    208          |      8,19          |
|    Délka          |   259           |    10,20          |


### <a name="enclosure-weight"></a>Váha skříně

V následující tabulce je uvedena váha zařízení, včetně baterie.

|     Skříně                                 |     Hmotnost          |
|-----------------------------------------------|---------------------|
|    Celková váha zařízení     |    7 kg          |

## <a name="enclosure-environment-specifications"></a>Specifikace prostředí skříně


V této části jsou uvedeny specifikace týkající se prostředí skříně, jako je teplota, vlhkost a nadmořská výška.


|     Specifikace             |     Description                                                          |
|--------------------------------|--------------------------------------------------------------------------|
|     Rozsah teploty          |     0 – 43 °c (provozní)                                              |
|     Vibrační                  |     MIL-STD-810 metoda 514,7 *<br> Postup I CAT 4, 20                  |
|     Náraz                      |     MIL-STD-810 metoda 516,7 *<br> Postup IV, logistická                 |
|     Mořské                   |     Provoz: 10 000 metrů<br> Nefunkční: 40 000 metrů          |

**Všechny odkazy jsou MIL-STD-810G Change 1 (2014)*


## <a name="next-steps"></a>Další kroky

- [Nasazení Azure Stack hraničních zařízení na Mini R](azure-stack-edge-placeholder.md)
