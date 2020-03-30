---
title: StorJednoduché technické specifikace | Dokumenty společnosti Microsoft
description: Popisuje technické specifikace a informace o dodržování regulačních norem pro hardwarové součásti StorSimple.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 06/02/2017
ms.author: alkohli
ms.openlocfilehash: 061194422a8c1bc449dbef0c4f04bb8e1db10dea
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "68965292"
---
# <a name="technical-specifications-and-compliance-for-the-storsimple-device"></a>Technické specifikace a shoda pro zařízení StorSimple

## <a name="overview"></a>Přehled

[!INCLUDE [storsimple-8000-eol-banner](../../includes/storsimple-8000-eol-banner.md)]

Hardwarové součásti vašeho zařízení Microsoft Azure StorSimple odpovídají technickým specifikacím a regulačním normám popsaným v tomto článku. Technické specifikace popisují moduly napájení a chlazení (PCM), diskové jednotky, úložnou kapacitu a skříně. Informace o dodržování předpisů se týkají takových věcí, jako jsou mezinárodní normy, bezpečnost a emise a kabeláž.

## <a name="power-and-cooling-module-specifications"></a>Specifikace napájecích a chladicích modulů

Zařízení StorSimple má dva duální ventilátory 100-240 V, moduly napájecího chlazení (PCM) kompatibilní s SBB. To poskytuje redundantní konfiguraci napájení. Pokud pcm selže, zařízení nadále pracovat normálně na druhé PCM, dokud se nezdařilo modul je nahrazen.

Skříň EBOD používá 580 W PCM a primární skříň používá 764 W PCM. V následujících tabulkách jsou uvedeny technické specifikace spojené s PCM.

| Specifikace | 580 W PCM (EBOD) | 764 W PCM (primární) |
| --- | --- | --- |
| Maximální výstupní výkon |580 W |764 |
| Frequency |50/60 Hz |50/60 Hz |
| Výběr rozsahu napětí |Automatické rozmezí: 90 - 264 V AC, 47/63 Hz |Automatické rozmezí: 90- 264 V AC, 47/63 Hz |
| Maximální proud inrush |20 A |20 A |
| Korekce účiníku |>95% jmenovité vstupní napětí |>95% jmenovité vstupní napětí |
| Harmonické |Splňuje EN61000-3-2 |Splňuje EN61000-3-2 |
| Výstup |5V Pohotovostní \@ napětí 2,0 A |5V Pohotovostní \@ napětí 2,7 A |
| +5V \@ 42 A |+5V \@ 40 A | |
| +12V \@ 38 A |+12V \@ 38 A | |
| Připojitelný za provozu |Ano |Ano |
| Spínače a LED diody |Spínač Zapnutí/vypnutí AC a čtyři indikátory stavu LED |Spínač Zapnutí/vypnutí AC a šest indikátorů stavu LED |
| Chlazení skříně |Axiální chladicí ventilátory s regulací otáček ventilátoru |Axiální chladicí ventilátory s regulací otáček ventilátoru |

## <a name="power-consumption-statistics"></a>Statistiky spotřeby energie

V následující tabulce jsou uvedena typická data spotřeby energie (skutečné hodnoty se mohou lišit od publikovaných) pro různé modely zařízení StorSimple.

| Podmínky | 240 V AC | 240 V AC | 240 V AC | 110 V AC | 110 V AC | 110 V AC |
| --- | --- | --- | --- | --- | --- | --- |
|  Ventilátory pomalé, disky nečinné |1,45 A |0,31 kW |1057,76 BTU/hod. |3.19 A |0,34 kW |1160.13 BTU/hod. |
|  Ventilátory pomalé, disky přístup |1,54 A |0,33 kW |1126.01 BTU/hod. |3.27 A |0,36 kW |1228,37 BTU/hod. |
|  Ventilátory rychlé, disky nečinné, dva psu napájené |2.14 A |0,49 kW |1671,95 BTU/hod. |4.99 A |0,54 kW |1842,56 BTU/hod. |
|  Ventilátory rychle, disky nečinnosti, jeden PSU poháněl jeden nečinný |2.05 A |0,48 kW |1637.83 BTU/hod. |4,58 A |0,50 kW |1706,07 BTU/hod. |
|  Ventilátory rychle, disky přístup, dva PSU powered |2.26 A |0,51 kW |1740.19 BTU/hod. |4.95 A |0,54 kW |1842,56 BTU/hod. |
|  Ventilátory rychle, disky přístup, jeden PSU poháněl jeden nečinný |2.14 A |0,49 kW |1671,95 BTU/hod. |4.81 A |0,53 kW |1808.44 BTU/hod. |

## <a name="disk-drive-specifications"></a>Specifikace diskové jednotky

Zařízení StorSimple podporuje až 12 3,5palcových diskových jednotek Serial Attached SCSI (SAS). Skutečné jednotky mohou být kombinací ssd disků (SSD) nebo pevných disků (HDD) v závislosti na konfiguraci produktu. 12 slotů pro diskové jednotky jsou umístěny v konfiguraci 3 o 4 před skříní. Skříň EBOD umožňuje další úložiště pro dalších 12 diskových jednotek. Jedná se vždy o pevné disky.

## <a name="storage-specifications"></a>Specifikace úložiště

Zařízení StorSimple mají kombinaci pevných disků a ssd disků pro jednotky 8100 a 8600. Celková využitelná kapacita 8100 a 8600 je zhruba 15 TB a 38 TB. V následující tabulce jsou uvedeny podrobnosti o kapacitě SSD, HDD a cloud v kontextu kapacity řešení StorSimple.

| Model zařízení / Kapacita | 8100 | 8600 |
| --- | --- | --- |
| Počet pevných disků (HDD) |8 |19 |
| Počet ssd jednotek (SSD) |4 |5 |
| Kapacita jednoho pevného disku |4 TB |4 TB |
| Kapacita ssd s jedním ssd |400 GB |800 GB |
| Volná kapacita |4 TB |4 TB |
| Využitelná kapacita pevného disku |14 TB |36 TB |
| Využitelná kapacita SSD |800 GB |2 TB |
| Celková využitelná kapacita* |~ 15 TB |~ 38 TB |
| Maximální kapacita řešení (včetně cloudu) |200 TB |500 TB |

<sup>* </sup>- *Celková využitelná kapacita zahrnuje kapacitu dostupnou pro data, metadata a vyrovnávací paměti. Na zařízení 8100 nebo až 22,5 TB na větším zařízení 8600 můžete zřídit místně připnuté svazky až 8,5 TB. Další informace naleznete na [místních vázaných svazcích StorSimple](storsimple-8000-local-volume-faq.md).*

## <a name="enclosure-dimensions-and-weight-specifications"></a>Rozměry skříně a specifikace hmotnosti

V následujících tabulkách jsou uvedeny různé specifikace skříně pro rozměry a hmotnost.

### <a name="enclosure-dimensions"></a>Rozměry skříně

V následující tabulce jsou uvedeny rozměry skříně v milimetrech a palcích.

| Kabiny | Milimetrů | Palců |
| --- | --- | --- |
| Vlastnost Height |87.9 |3.46 |
| Šířka přes montážní přírubu |483 |19.02 |
| Šířka napříč tělem krytu |443 |17.44 |
| Hloubka od přední montážní příruby až po končetiny těla skříně |577 |22.72 |
| Hloubka od provozního panelu až po nejvzdálenější konec krytu |630.5 |24.82 |
| Hloubka od montážní příruby až po nejvzdálenější konec krytu |603 |23.74 |

### <a name="enclosure-weight"></a>Hmotnost krytu

V závislosti na konfiguraci může plně naložená primární skříň vážit od 21 do 33 kg a vyžaduje dvě osoby, aby s ním manipulovaly.

| Kabiny | Hmotnost |
| --- | --- |
| Maximální hmotnost (závisí na konfiguraci) |30 kg – 33 kg |
| Prázdné (bez pohonů) |21 – 23 kg |

## <a name="enclosure-environment-specifications"></a>Specifikace prostředí skříně

V této části jsou uvedeny specifikace týkající se prostředí skříně. Do této kategorie patří teplota, vlhkost, nadmořská výška, nárazy, vibrace, orientace, bezpečnost a elektromagnetická kompatibilita (EMC).

### <a name="temperature-and-humidity"></a>Teplota a vlhkost

| Kabiny | Rozsah okolníteploty | Okolní relativní vlhkost | Maximální mokrá žárovka |
| --- | --- | --- | --- |
| Operační |5°C - 35°C (41°F - 95°F) |20% - 80% nekondenzační |28 °C (82 °F) |
| Neprovozní |-40°C - 70°C (40°F - 158°F) |5% - 100% nekondenzující |29 °C (84 °F) |

### <a name="airflow-altitude-shock-vibration-orientation-safety-and-emc"></a>Proudění vzduchu, nadmořská výška, nárazy, vibrace, orientace, bezpečnost a EMC

| Kabiny | Provozní specifikace |
| --- | --- |
| Proudění vzduchu |Proudění vzduchu systému je zepředu dozadu. Systém musí být provozován s nízkotlakou instalací zadního výfuku. Zpětný tlak vytvářený dveřmi a překážkami do regálu by neměl překročit 5 pascalů (0,5 mm vodního měřidla). |
| Nadmořská výška, provozní |-30 metrů až 3045 metrů (-100 stop až 10 000 stop) s maximální provozní teplotou o 5 °C nad 7000 stop. |
| Nadmořská výška, neprovozní |-305 metrů až 12 192 metrů (-1000 stop až 40 000 stop) |
| Šok, provozní |5g 10 ms 1/2 sinus |
| Nárazový, nefunkční |30g 10 ms 1/2 sinus |
| Vibrace, provozní |0,21 g RMS 5-500 Hz náhodné |
| Vibrace, neprovozní |1.04g RMS 2-200 Hz náhodné |
| Vibrace, přemístění |3g 2-200 Hz sinus |
| Orientace a montáž |19" montáž do racku (2 jednotky EIA) |
| Regálové kolejnice |Pro uložení minimálně 700 mm (31,50 palců) hloubkových regálů vyhovujících IEC 297 |
| Bezpečnost a schválení |CE a UL EN 61000-3, IEC 61000-3, UL 61000-3 |
| Emc |EN55022 (CISPR - A), FCC A |

## <a name="international-standards-compliance"></a>Dodržování mezinárodních norem

Vaše zařízení Microsoft Azure StorSimple splňuje následující mezinárodní standardy:  

* CE - EN 60950 - 1
* Cb zpráva IEC 60950 - 1
* UL a cUL až UL 60950 - 1

## <a name="safety-compliance"></a>Bezpečnostní shoda

Vaše zařízení Microsoft Azure StorSimple splňuje následující bezpečnostní hodnocení:

* Schválení typu systémového výrobku: UL, cUL, CE
* Bezpečnostní shoda: UL 60950, IEC 60950, EN 60950

## <a name="emc-compliance"></a>Dodržování předpisů EMC

Vaše zařízení Microsoft Azure StorSimple splňuje následující hodnocení EMC.

### <a name="emissions"></a>Emisí

Zařízení je kompatibilní s EMC pro úrovně emisí prováděných a vyzařovaných.

* Mezní hodnoty emisí prováděné: CFR 47 část 15B třída A EN55022 třída A CISPR třída A
* Mezní hodnoty vyzařovaných emisí: CFR 47 část 15B třídy A EN55022 třídy A CISPR třídy A

### <a name="harmonics-and-flicker"></a>Harmonické a blikání

Zařízení vyhovuje normě EN61000-3-2/3.

### <a name="immunity-limit-levels"></a>Mezní hodnoty imunity

Zařízení vyhovuje normě EN55024.

## <a name="ac-power-cord-compliance"></a>Dodržování napájecího kabelu střídavého proudu

Zástrčka a kompletní sestava napájecího kabelu musí splňovat normy odpovídající zemi/oblasti, ve které je zařízení používáno, a musí mít bezpečnostní schválení, která jsou v této zemi/oblasti přijatelná. V následujících tabulkách jsou uvedeny standardy pro USA a Evropu.

### <a name="ac-power-cords---usa-must-be-nrtl-listed"></a>Napájecí kabely střídavého proudu - USA (musí být uvedeny v seznamu NRTL)

| Komponenta | Specifikace |
| --- | --- |
| Typ kabelu |SV nebo SVT, minimálně 18 AWG, 3 vodiče, maximální délka 2,0 metrů |
| Plug |NEMA 5-15P uzemňovací upevňovací zástrčka s jmenovitým hodnocením 120 V, 10 A; nebo IEC 320 C14, 250 V, 10 A |
| Zásuvky |IEC 320 C-13, 250 V, 10 A |

### <a name="ac-power-cords---europe"></a>Střídavé napájecí kabely - Evropa

| Komponenta | Specifikace |
| --- | --- |
| Typ kabelu |Harmonizované, H05-VVF-3G1.0 |
| Zásuvky |IEC 320 C-13, 250 V, 10 A |

## <a name="supported-network-cables"></a>Podporované síťové kabely

Síťová rozhraní 10 GbE DATA 2 a DATA 3 naleznete v [seznamu podporovaných síťových kabelů a modulů](storsimple-supported-hardware-for-10-gbe-network-interfaces.md).

## <a name="next-steps"></a>Další kroky

Nyní jste připraveni nasadit zařízení StorSimple v datovém centru. Další informace najdete [v tématu Nasazení místního zařízení](storsimple-8000-deployment-walkthrough-u2.md).

