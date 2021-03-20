---
title: Specifikace souborového Microsoft Azure Edge FXT | Microsoft Docs
description: Seznamte se s fyzickými a environmentálními specifikacemi pro Microsoft Azure FXT Edge souborového hardware.
author: ekpgh
ms.service: fxt-edge-filer
ms.topic: conceptual
ms.date: 06/20/2019
ms.author: rohogue
ms.openlocfilehash: b1a172adb54593421ef56e09c131e0ddcaee9f6e
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "92215820"
---
# <a name="azure-fxt-edge-filer-specifications"></a>Specifikace souborového pro Azure FXT Edge

Tento článek vysvětluje hardwarové specifikace pro hardwarové uzly Azure FXT Edge souborového. V praxi jsou nakonfigurovány tři nebo více uzlů, aby poskytovaly clusterový systém mezipaměti.

## <a name="hardware-specifications"></a>Specifikace hardwaru

| Součást | FXT 6600 | FXT 6400 |
|----------|-----------|-----------|
| Procesorová jádra |  16 | 16 |
| DRAM  | 1536 GB | 768 GB |
| Síťové porty | 6 × 25/10 GB a 2 × 1 GB | 6 × 25/10 GB a 2 × 1 GB |
| Kapacita NVMe SSD | 25,6 TB | 12,8 TB |

## <a name="drive-specifications"></a>Specifikace jednotky

Systém má deset diskových pozic, které jsou přístupné z front. Každá naplněná jednotka je označená na pravé straně s informacemi o kapacitě.

Čísla jednotek se tisknou na místo mezi jednotkami. Ve službě Azure FXT Edge souborového je jednotka 0 levou horní jednotkou a jednotka 1 je přímo pod ní.

![Fotografie jedné pozice pevného disku na skříni FXT, která zobrazuje čísla jednotek a popisky kapacity](media/fxt-drives-photo.png)

| Čísla jednotek    |  Použití   |  Specifikace |
|------------------|--------|-----------------|
| 0, 1             | Operační systém     | 480 GB SATA SSD |
| 2, 3, 4, 5, 6, 7, 8, 9 | Data   | FXT 6600:3,2 TB NVMe SSD <br> FXT 6400:1,6 TB NVMe SSD |

## <a name="dimensions-and-weight"></a>Rozměry a váha

Souborového Edge pro Azure FXT je navržený tak, aby odpovídal standardu 19 "vybavení v racku a je jedním rozvaděčem vysoké jednotky (1U).

<!-- 10x2.5 inches version -->

| Souborového dimenze            | Hodnota                    |
|-----------------------------|--------------------------|
| Height (Výška)                      | 42,8 mm (1,68 palců)    |
| Šířka (včetně racku EARS) | 482,0 mm (18,97 palců)  |
| Šířka – hlavní skříň      | 434,0 mm (17,08 palců) |
| Hloubka EARS stojanu na zadní část hlavního skříně                   | 733,82 mm (29,61 palců) |
| Hloubka EARS stojanu na nejvzdálenější zadní protrusion                 | 772,67 mm (30,42 palců) |
| Earsa hloubkového stojanu na nejvzdálenější přední protrusion, bez čelního krytu | 22,0 mm (0,87 palce)  |
| Hloubka EARS stojanu na nejvzdálenější přední protrusion s čelním krytem    | 35,84 mm (1,41 palců) |

| Hmotnost | Hodnota |
|-----------------|----------------------|
| Váha uzlu (bez balení, bez příslušenství) | 40 kg (18,1 kg) |
| Čistou hmotnost (bez balení, včetně příslušenství) | 51 kg (23,1 kg)|
| Hrubá váha (jak je expedována, zahrnuje všechny obaly) |  64 kg (29,0 kg) |

### <a name="shipping-dimensions"></a>Rozměry expedice

| Dimenze balíčku | Milimetrech | Cm |
|-------------------|-------------|--------|
| Height (Výška)            | 311,2       | 12,25 " |
| Width (Šířka)             | 642,8       | 25,31 " |
| Délka            | 1 051,1     | 41,38 " |

## <a name="power-and-thermal-specifications"></a>Specifikace napájení a tepelného řízení

Tato část poskytuje hodnocení a měření výkonu pro Azure FXT Edge souborového.

### <a name="nameplate-ratings"></a>Hodnocení Nameplate

| Hodnocení Nameplate pro modely řady FXT 6000 |
|----------------|
| 100 – 240V ~    |
| 10A-5A (X2)  |
| 50/60 Hz         |

<!-- matches the Dell regulatory label exactly -->

### <a name="power-and-thermal-measurements"></a>Výkon a tepelná měření

Uzly Azure FXT Edge souborového používají ventilátory s proměnlivou rychlostí, takže výkon závisí na teplotě a zatížení. Maximální rychlosti ventilátoru se dají dosáhnout v určitých kombinacích vysokého zatížení a zvýšených okolních teplot.

Tyto grafy poskytují spotřebu energie a měření na výstupu pro běžně používané kombinace frekvencí napětí.

| FXT 6600 napájení při pokojové teplotě <br />(22 ° C, 71,6 ° F) | 100 V, 60 Hz | 120 V, 60 Hz | 208 V, 60 Hz | 230 V, 50 Hz | 240 V, 50 Hz |
|---------|---|---|---|---|---|
| Napětí (V) | 100 | 120 | 208 | 230 | 240 |
| Frekvence (Hz) | 60 | 60 | 60 | 50 | 50 |
| Aktuální (A) | 5,02 | 4,16 |2,40 | 2,20 | 2,16 |
| Zdánlivá mocnina (VA) | 502 | 499 | 499 | 506 | 518|
| Faktor výkonu | 0.99 | 0.99 |0.98 | 0.98 | 0.98 |
| Skutečný výkon (W) | 497 |494 | 489 | 496 | 508 |
| Tepelné rozptyl (BTU/hr) |1696 | 1686 | 1 669 | 1692 | 1733 |

| FXT 6600 napájení s maximální rychlostí ventilátoru | 100 V, 60 Hz | 120 V, 60 Hz | 208 V, 60 Hz | 230 V, 50 Hz | 240 V, 50 Hz |
|---------|---|---|---|---|---|
| Napětí (V) | 100 |120 | 208 | 230 | 240|
| Frekvence (Hz) | 60 | 60 | 60 | 50 | 50 |
| Aktuální (A) | 5,98 | 5,01 | 2,81 | 2.55 | 2,48 |
| Zdánlivá mocnina (VA) | 598 | 601 | 584 | 587 | 595 |
| Faktor výkonu | 0.99 | 0.99 | 0.98 | 0.98 | 0.98 |
| Skutečný výkon (W) | 592 | 595 | 573 | 575 | 583 |
| Tepelné rozptyl (BTU/hr) | 2020 |2031 | 1954 | 1961 | 1990 |

| FXT 6400 napájení při pokojové teplotě <br />(22 ° C, 71,6 ° F) | 100 V, 60 Hz | 120 V, 60 Hz | 208 V, 60 Hz | 230 V, 50 Hz | 240 V, 50 Hz |
|---------|---|---|---|---|---|
| Napětí (V) | 100 | 120 | 208 | 230 | 240 |
| Frekvence (Hz) |60 | 60 | 60 | 50 | 50 |
| Aktuální (A) | 4.63 | 3,86 | 2.24 | 2,04 | 1,94 |
| Zdánlivá mocnina (VA) | 463 | 463 | 466 | 469 | 466 |
| Faktor výkonu | 0.99 | 0.99 | 0.98 | 0.98 | 0.98 |
| Skutečný výkon (W) | 458 | 459 | 457 | 460 | 456 |
| Tepelné rozptyl (BTU/hr) | 1564 | 1565 | 1558 | 1569 | 1557 |

| FXT 6400 napájení s maximální rychlostí ventilátoru | 100 V, 60 Hz | 120 V, 60 Hz | 208 V, 60 Hz | 230 V, 50 Hz | 240 V, 50 Hz |
|---------|---|---|---|---|---|
| Napětí (V) | 100 | 120 | 208 | 230 | 240 |
| Frekvence (Hz) | 60 | 60 | 60 | 50 | 50 |
| Aktuální (A) | 5,15 | 4,28 | 2,48 | 2,28 | 2,13 |
| Zdánlivá mocnina (VA) | 515 | 514 | 516 | 524 | 511 |
| Faktor výkonu | 0.99 | 0.99 | 0.98 | 0.98 | 0.98 |
| Skutečný výkon (W) | 510 | 508 | 506 | 514 | 501 |
| Tepelné rozptyl (BTU/hr) | 1740 | 1735 | 1725 | 1753 | 1709 |

## <a name="environmental-requirements"></a>Požadavky na prostředí

Tato část obsahuje specifikace pro okolní prostředí hardwaru.

### <a name="temperature-and-humidity"></a>Teplota a vlhkost

| Atribut prostředí   | Provozní rozsah                   | Nefunkční rozsah         |
|---------------------------|-----------------------------------|-----------------------------|
| Rozsah okolních teplot | 10 oC až 35 oC (50 – 86 °F)          | -40 oC až 65 °C (-40 – 149 °F) |
| Relativní vlhkost okolí | 10% až 80% nekondenzující          | 5% až 95% nekondenzující     |
| Maximální bod Dew         | 29 °C (84 °F)                       | 33 OC (91 °F)                 |
| Mořské                  | až 3048 měřičů (10 000 metrů) podléhajících teplotnímu rozhodnocení, které je uvedeno níže | až 12 000 měřičů (39 370 metrů) |

> [!NOTE]
> **Hodnocení teploty Nadmořského:** Maximální teplota se sníží o 1 °C/300 m (1 °F/547 ft) nad 950 m (3 117 ft).

### <a name="airflow-shock-and-vibration"></a>Tok, náraz a vibrace

| Atribut         | Specifikace |
|-------------------|---------------|
| Tok dat                    | Tok systému je zepředu zezadu. Systém musí být provozován s nízkým zatížením pro zadní výfukovou instalaci. |
| Úraz, provozní         | 6 G až 11 milisekund (testováno v 6 orientací) |
| Úraz, jiný než provozní     | 71 G na 2 milisekundy (testováno 6 orientací) |
| Vibrace, provozní     | 0,26 G<sub>RMS</sub> 5 Hz až 350 Hz náhodně         |
| Vibrace, jiné než provozní | 1,88 G<sub>RMS</sub> 10 Hz až 500 Hz po dobu 15 minut (všechny šest stran testováno)  |

## <a name="safety-regulation-compliance"></a>Dodržování předpisů pro bezpečnostní předpisy

Azure FXT Edge souborového odpovídá uvedeným nařízením.

| Kategorie       | Specifikace zákonného charakteru |
|----------------|--------------------------|
| Obecné zabezpečení | EN 60950-1:2006 + a1:2010 + a2:2013 + A11:2009 + A12:2011/IEC 60950-1:2005 ED2 + a1:2009 + a2:2013 <br>EN 62311:2008 |
| SOFTWARE            | FCC A, ICES – 003  <br>EN 55032:2012/CISPR 32:2012  <br>EN 55032:2015/CISPR 32:2015  <br>EN 55024:2010 + A1:2015/CISPR 24:2010 + A1:2015  <br>EN 61000-3-2:2014/IEC 61000-3-2:2014 (třída D)   <br>EN 61000-3-3:2013/IEC 61000-3-3:2013 |
| Energetický sektor         | Nařízení Komise (EU) – ne. 617/2013  |
| RoHS           |    EN 50581:2012   |
