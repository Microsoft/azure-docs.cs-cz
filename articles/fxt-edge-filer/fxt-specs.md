---
title: Microsoft Azure FXT hrany Filer specifikace | Dokumentace Microsoftu
description: Specifikace fyzické a prostředí pro Azure FXT hrany Filer hardware
author: ekpgh
ms.service: fxt-edge-filer
ms.topic: overview
ms.date: 06/20/2019
ms.author: v-erkell
ms.openlocfilehash: 0679bce8eae515aa6b90e34fcfd15ee9b4e56b31
ms.sourcegitcommit: 5bdd50e769a4d50ccb89e135cfd38b788ade594d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/03/2019
ms.locfileid: "67542878"
---
# <a name="azure-fxt-edge-filer-specifications"></a>Specifikace Azure Filer FXT hrany

Tento článek vysvětluje, hardwarové specifikace pro Azure FXT hrany Filer hardware do uzlů. V praxi konfigurují tři nebo více uzlů, které poskytuje systému Clusterové mezipaměti společně.

## <a name="hardware-specifications"></a>Specifikace hardwaru

| Komponenta | FXT 6600 | FXT 6400 |
|----------|-----------|-----------|
| Procesorová jádra |  16 | 16 |
| DRAM  | 1536 GB | 768 GB |
| Síťové porty | 6 x 25/10 Gb + 2 x 1 Gb | 6 x 25/10 Gb + 2 x 1 Gb |
| Kapacitu disků SSD NVMe | 25,6 TB | 12.8 TB |

## <a name="drive-specifications"></a>Specifikace jednotky

Systém má deset disky, přístupné z front. Každé jednotky, mají údaj vyplněný je popisek na pravé straně s informací o kapacitě. 

Jednotka čísla se vytisknou na mezeru mezi jednotky. V Azure FXT hrany vyfiltrovat disk 0 je jednotka vlevo nahoře a jednotka 1 je přímo pod ním.

![fotografie jeden pevný disk ve skříni FXT zobrazující čísla a kapacitu popisek jednotky](media/fxt-drives-photo.png)

| Jednotka čísla    |  Použití   |  Specifikace |
|------------------|--------|-----------------|
| 0, 1             | OS     | 480 GB SATA SSD |
| 2, 3, 4, 5, 6, 7, 8, 9 | Data   | FXT 6600: 3.2 TB NVMe SSD <br> FXT 6400: 1.6 TB NVMe SSD |


## <a name="dimensions-and-weight"></a>Rozměry a váhy záznamu

Vyfiltrovat Edge FXT Azure je navržená pro zařízení rack standardní 19" a je jeden stojan jednotka vysokou (1U). 

<!-- 10x2.5 inches version -->

| Filtr dimenzí           |                          |
|-----------------------------|--------------------------|
| Výška                      | 42.8 mm (1.68 palců)    |
| Šířka (včetně uší rack) | 482.0 mm (18.97 palců)  |
| Šířka – hlavní skříň      | 434.0 mm (17.08 palců) |
| Hloubka - uší regálu na zadní hlavní skříň                   | 733.82 mm (29.61 palců) |
| Hloubka - uší regálu k nejvzdálenější zadní protrusion                 | 772.67 mm (30.42 palců) |
| Hloubka - uší regálu k nejvzdálenější přední protrusion bez lůžka | 22.0 mm (0.87 palec)  |
| Hloubka - uší rack k nejvzdálenější přední protrusion s lůžkem    | 35.84 mm (1.41 palců) |

| Hmotnost | |
|-----------------|----------------------|
| Váha uzlu (bez balení bez příslušenství) | 40 lbs (18,1 kg) |
| Čistém (bez balení, včetně příslušenství) | pole 51 lbs (23.1 kg)|
| Hmotnosti (stejně jako dodán, včetně všech balení) |  64 lbs (29,0 kg) |

### <a name="shipping-dimensions"></a>Přesouvání dimenze

| Dimenze balíčku | Milimetrech | Palců |
|-------------------|-------------|--------|
| Výška            | 311.2       | 12.25" |
| Šířka             | 642.8       | 25.31" |
| Délka            | 1,051.1     | 41.38" |

## <a name="power-and-thermal-specifications"></a>Výkon a teplotní specifikace

Tato část poskytuje power hodnocení a měření pro filtr Azure FXT hrany.

### <a name="nameplate-ratings"></a>Jmenovka hodnocení

| Jmenovka hodnocení pro modely FXT 6000 řady |
|----------------|
| 100 – 240 V ~    |
| 10A - 5A (X2)  |
| 50 / 60Hz         |

<!-- matches the Dell regulatory label exactly -->

### <a name="power-and-thermal-measurements"></a>Výkon a teplotní měření 

Azure uzly FXT hrany Filer používat proměnné rychlost ventilátory, takže power závisí na teploty a zatížení. Ventilátor maximální rychlostí lze dosáhnout na určité kombinace vysokého zatížení a teplotu prostředí se zvýšenými oprávněními. 

Tyto grafy zadejte spotřebu energie a měření teplotní výstup pro běžně používané napětí frekvencí kombinace. 

| Napájení FXT 6600 na pokojovou teplotu <br />(22° C, 71.6° F) | 100 V, 60 Hz | 120 V, 60 Hz | 208 V, 60 Hz | 230 V, 50 Hz | 240 V, 50 Hz | 
|---------|---|---|---|---|---|
| Snímač napětí (V) | 100 | 120 | 208 | 230 | 240 | 
| Frekvence (Hz) | 60 | 60 | 60 | 50 | 50 |
| Aktuální (A) | 5.02 | 4.16 |2.40 | 2.20 | 2.16 |
| Pozná napájení (VA) | 502 | 499 | 499 | 506 | 518|
| Faktor výkonu | 0,99. | 0,99. |0.98 | 0.98 | 0.98 |
| Skutečná síla (W) | 497 |494 | 489 | 496 | 508 |
| Teplotní rozptylu (KJ/hod) |1696 | 1686 | 1669 | 1692 | 1733 |

| Napájení FXT 6600 ventilátor maximální rychlostí | 100 V, 60 Hz | 120 V, 60 Hz | 208 V, 60 Hz | 230 V, 50 Hz | 240 V, 50 Hz | 
|---------|---|---|---|---|---|
| Snímač napětí (V) | 100 |120 | 208 | 230 | 240| 
| Frekvence (Hz) | 60 | 60 | 60 | 50 | 50 |
| Aktuální (A) | 5.98 | 5.01 | 2.81 | 2.55 | 2.48 |
| Pozná napájení (VA) | 598 | 601 | 584 | 587 | 595 |
| Faktor výkonu | 0,99. | 0,99. | 0.98 | 0.98 | 0.98 |
| Skutečná síla (W) | 592 | 595 | 573 | 575 | 583 |
| Teplotní rozptylu (KJ/hod) | 2020 |2031 | 1954 | 1961 | 1990 |

| Napájení FXT 6400 na pokojovou teplotu <br />(22° C, 71.6° F) | 100 V, 60 Hz | 120 V, 60 Hz | 208 V, 60 Hz | 230 V, 50 Hz | 240 V, 50 Hz | 
|---------|---|---|---|---|---|
| Snímač napětí (V) | 100 | 120 | 208 | 230 | 240 |
| Frekvence (Hz) |60 | 60 | 60 | 50 | 50 |
| Aktuální (A) | 4.63 | 3.86 | 2.24 | 2.04 | 1.94 |
| Pozná napájení (VA) | 463 | 463 | 466 | 469 | 466 |
| Faktor výkonu | 0,99. | 0,99. | 0.98 | 0.98 | 0.98 | 
| Skutečná síla (W) | 458 | 459 | 457 | 460 | 456 |
| Teplotní rozptylu (KJ/hod) | 1564 | 1565 | 1558 | 1569 | 1557 |

| Napájení FXT 6400 ventilátor maximální rychlostí | 100 V, 60 Hz | 120 V, 60 Hz | 208 V, 60 Hz | 230 V, 50 Hz | 240 V, 50 Hz |
|---------|---|---|---|---|---|
| Snímač napětí (V) | 100 | 120 | 208 | 230 | 240 |
| Frekvence (Hz) | 60 | 60 | 60 | 50 | 50 |
| Aktuální (A) | 5.15 | 4.28 | 2.48 | 2.28 | 2.13 |
| Pozná napájení (VA) | 515 | 514 | 516 | 524 | 511 |
| Faktor výkonu | 0,99. | 0,99. | 0.98 | 0.98 | 0.98 |
| Skutečná síla (W) | 510 | 508 | 506 | 514 | 501 |
| Teplotní rozptylu (KJ/hod) | 1740 | 1735 | 1725 | 1753 | 1709 |

## <a name="environmental-requirements"></a>Požadavky na prostředí

Tato část obsahuje specifikace hardwaru okolního prostředí.

### <a name="temperature-and-humidity"></a>Teploty a vlhkosti

| Atribut prostředí   | Provozní rozsah                   | Provozní mimo rozsah         |
|---------------------------|-----------------------------------|-----------------------------|
| Rozsah okolní teploty | 10° C až 35° C (50-86° F)          | OC-40 až 65 ° C (-40-149 ° F) |
| Ambientní relativní vlhkosti | 10-80 % nekondenzující          | 5 – 95 % nekondenzující     |
| Maximální sráží bodu         | 29° C (84° F)                       | 33°C (91°F)                 |
| Altitude                  | níže uvedené až 3048 měřiče (10 000 nohou), v souladu s teploty zrušit hodnocení. | až 12 000 měřiče (39,370 nohou) |

> [!NOTE] 
> **Výška teploty zrušit hodnocení:** Nejvyšší teplota se snižuje podle oC 1/300 m (1° F/547 ft) nad 950 m (3,117 ft).

### <a name="airflow-shock-and-vibration"></a>Vzduchu, otřást a vibrace 

| Atribut         | Specifikace |
|-------------------|---------------|
| Vzduchu                    | Systém vzduchu je přední, zadní. Systém musí pracovat s instalací Nízkotlaké zadní výfukového. |
| Otřást provozní         | 6 G 11 milisekund (testovány v orientace 6) |
| Otřást nefunkční     | 71 G, 2 milisekund (testovány v orientace 6) |
| Vibrace, provozní     | 0,26 G<sub>RMS</sub> 5 Hz na 350 Hz náhodné         |
| Vibrace, nefunkční | 1,88 G<sub>RMS</sub> Hz 10 až 500 Hz 15 minut (všech šesti stranách testování)  |

## <a name="safety-regulation-compliance"></a>Dodržování nařízení bezpečnosti 

Azure FXT hrany Filer splňuje uvedené předpisy. 

| Category       | Dodržování specifikace | 
|----------------|--------------------------|
| Obecné zabezpečení | CS 60950-1:2006 A1:2010 + A2:2013 + A11:2009 + A12:2011 / IEC 60950-1:2005 ed2 + A1:2009 A2:2013 <br>EN 62311:2008 | 
| EMC            | FCC A, ICES-003  <br>EN 55032:2012/CISPR 32:2012  <br>EN 55032:2015/CISPR 32:2015  <br>EN 55024:2010 +A1:2015/CISPR 24:2010 +A1:2015  <br>CS 61000-3-2:2014 / IEC 61000-3-2:2014 (třídy D)   <br>EN 61000-3-3:2013/IEC 61000-3-3:2013 |
| Energetický sektor         | Ne. nařízení (EU) Komise 617/2013  |
| RoHS           |    EN 50581:2012   |