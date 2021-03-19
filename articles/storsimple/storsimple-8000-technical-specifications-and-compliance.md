---
title: Technické specifikace StorSimple | Microsoft Docs
description: Popisuje technické specifikace a informace o dodržování předpisů regulativních standardů pro hardwarové součásti StorSimple.
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
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "68965292"
---
# <a name="technical-specifications-and-compliance-for-the-storsimple-device"></a>Technické specifikace a dodržování předpisů pro zařízení StorSimple

## <a name="overview"></a>Přehled

[!INCLUDE [storsimple-8000-eol-banner](../../includes/storsimple-8000-eol-banner.md)]

Hardwarové součásti zařízení Microsoft Azure StorSimple vyhovují technickým specifikacím a regulativním normám, které jsou uvedené v tomto článku. Technické specifikace popisují moduly napájení a chlazení (PCMs), diskové jednotky, kapacitu úložiště a skříně. Informace o dodržování předpisů se týkají takových věcí jako mezinárodních standardů, bezpečnosti a emisí a kabeláže.

## <a name="power-and-cooling-module-specifications"></a>Specifikace modulu napájení a chlazení

Zařízení StorSimple má dva duální ventilátory 100-240 V, SBB (PCMs), které jsou kompatibilní s. Tím je zajištěna redundantní konfigurace napájení. Pokud se PCM nedaří, zařízení bude i nadále fungovat normálně na jiném PCM, dokud se neúspěšně nenahradí modul.

EBOD skříň používá modul PCM 580 W a primární skříň používá 764 W PCM. V následujících tabulkách jsou uvedeny technické specifikace přidružené k PCMs.

| Specifikace | 580 W PCM (EBOD) | 764 W PCM (primární) |
| --- | --- | --- |
| Maximální výstupní výkon |580 W |764 |
| Frekvence |50/60 Hz |50/60 Hz |
| Výběr rozsahu napětí |Automatické rozsahy: 90 – 264 V AC, 47/63 Hz |Automatické rozsahy: 90-264 V AC, 47/63 Hz |
| Maximální inrush aktuální |20 A |20 A |
| Oprava Power Factor |Nominální vstupní napětí >95% |Nominální vstupní napětí >95% |
| Harmony |Splňuje EN61000-3 – 2 |Splňuje EN61000-3 – 2 |
| Výstup |Snímač 5V síťového pohotovostní napětí \@ 2,0 A |Snímač 5V síťového pohotovostní napětí \@ 2,7 A |
| + Snímač 5V síťového \@ 42 A |+ Snímač 5V síťového \@ 40 A | |
| + Snímač 12V síťového \@ 38 A |+ Snímač 12V síťového \@ 38 A | |
| Horká, připojitelná |Yes |Yes |
| Přepínače a diody LED |Přepínač zapnutí/vypnutí střídavého napájení a čtyři indikátory LED stavu |Přepínač zapnutí/vypnutí střídavého napájení a šest indikátorů stavu |
| Chlazení skříně |Axiální chladicí ventilátory s proměnlivým ovládáním rychlosti ventilátoru |Axiální chladicí ventilátory s proměnlivým ovládáním rychlosti ventilátoru |

## <a name="power-consumption-statistics"></a>Statistika spotřeby energie

V následující tabulce jsou uvedena typická data o spotřebě energie (skutečné hodnoty se mohou lišit od publikovaných) pro různé modely zařízení StorSimple.

| Podmínky | 240 V AC | 240 V AC | 240 V AC | 110 V AC | 110 V AC | 110 V AC |
| --- | --- | --- | --- | --- | --- | --- |
|  Ventilátory pomalu, nečinné jednotky |1,45 A |0,31 kW |1057,76 BTU/hod |3,19 A |0,34 kW |1160,13 BTU/hod |
|  Ventilátory pomalu, jednotky přistupují k |1,54 A |0,33 kW |1126,01 BTU/hod |3,27 A |0,36 kW |1228,37 BTU/hod |
|  Rychlé ventilátory, nečinné jednotky, dvě PSUs |2,14 A |0,49 kW |1671,95 BTU/hod |4,99 A |0,54 kW |1842,56 BTU/hod |
|  Rychlé ventilátory, nečinné jednotky, jedna PSUa s jedním nečinným |2,05 A |0,48 kW |1637,83 BTU/hod |4,58 A |0,50 kW |1706,07 BTU/hod |
|  Rychlé ventilátory, jednotky, ke kterým přistupují, dva PSUs |2,26 A |0,51 kW |1740,19 BTU/hod |4,95 A |0,54 kW |1842,56 BTU/hod |
|  Rychlé ventilátory, které přistupují k jednotkám, jeden PSU s jedním nečinným |2,14 A |0,49 kW |1671,95 BTU/hod |4,81 A |0,53 kW |1808,44 BTU/hod |

## <a name="disk-drive-specifications"></a>Specifikace diskové jednotky

Vaše zařízení StorSimple podporuje diskové jednotky SAS (Serial Attached SCSI), a to až 12 3,5. Skutečnými jednotkami může být kombinace jednotek SSD (Solid-State Drive) nebo jednotek pevného disku (HDD) v závislosti na konfiguraci produktu. 12 slotů diskových jednotek se nachází v konfiguraci 3 o 4 před skříní. Skříň EBOD umožňuje další úložiště pro další 12 diskových jednotek. Ty jsou vždycky HDD.

## <a name="storage-specifications"></a>Specifikace úložiště

Zařízení StorSimple mají kombinaci pevných disků a jednotek SSD (Solid-State Drive) pro 8100 i 8600. Celková použitelná kapacita pro 8100 a 8600 je zhruba 15 TB a 38 TB. Následující tabulka uvádí podrobnosti o kapacitě SSD, HDD a cloudu v kontextu kapacity řešení StorSimple.

| Model/kapacita zařízení | 8100 | 8600 |
| --- | --- | --- |
| Počet jednotek pevného disku (HDD) |8 |19 |
| Počet jednotek SSD (Solid-State Drive) (SSD) |4 |5 |
| Kapacita jednoho HDD |4 TB |4 TB |
| Jedna kapacita SSD |400 GB |800 GB |
| Volná kapacita |4 TB |4 TB |
| Použitelná kapacita HDD |14 TB |36 TB |
| Použitelná kapacita SSD |800 GB |2 TB |
| Celková použitelná kapacita * |~ 15 TB |~ 38 TB |
| Maximální kapacita řešení (včetně cloudu) |200 TB |500 TB |

<sup>* </sup>- *Celková použitelná kapacita zahrnuje kapacitu dostupnou pro data, metadata a vyrovnávací paměti. Na zařízení 8100 můžete zřídit místně připojené svazky o velikosti až 8,5 TB nebo až 22,5 TB na větším zařízení s 8600. Další informace najdete v [StorSimple místně připnuté svazky](storsimple-8000-local-volume-faq.md).*

## <a name="enclosure-dimensions-and-weight-specifications"></a>Rozměry skříně a specifikace váhy

V následujících tabulkách jsou uvedeny různé specifikace velikosti skříně pro rozměry a váhu.

### <a name="enclosure-dimensions"></a>Rozměry skříně

V následující tabulce jsou uvedeny rozměry skříně v milimetrech a palcích.

| Skříně | Milimetrech | Cm |
| --- | --- | --- |
| Height (Výška) |87,9 |3,46 |
| Šířka mezi montážní přírubou |483 |19,02 |
| Šířka v těle skříně |443 |17,44 |
| Hloubka od přední přímontní příruby k okraji těla skříně |577 |22,72 |
| Hloubka z panelu operace až po krajní část skříně |630,5 |24,82 |
| Hloubka od montážní příruby až po krajní část skříně |603 |23,74 |

### <a name="enclosure-weight"></a>Váha skříně

V závislosti na konfiguraci může plně načtená primární skříň Navážit od 21 do 33 KGS a pro její zpracování vyžaduje dvě osoby.

| Skříně | Hmotnost |
| --- | --- |
| Maximální váha (závisí na konfiguraci) |30 kg – 33 kg |
| Prázdné (žádné jednotky nejsou namontované) |21 – 23 kg |

## <a name="enclosure-environment-specifications"></a>Specifikace prostředí skříně

V této části jsou uvedeny specifikace týkající se prostředí skříně. V této kategorii jsou zahrnuté teploty, vlhkosti, nadmořskou výškou, proti otřesům, vibracím, zabezpečení a elektromagnetické kompatibilitě (EMC).

### <a name="temperature-and-humidity"></a>Teplota a vlhkost

| Skříně | Rozsah okolních teplot | Relativní vlhkost okolí | Maximální mokrá cibule |
| --- | --- | --- | --- |
| Provoz |5 OC-35 OC (41 °F-95 °F) |20% až 80% nekondenzující- |28 °C (82 °F) |
| Není funkční |-40 °C-70 °C (40 °F-158 °F) |5% až 100% nekondenzující |29 °C (84 °F) |

### <a name="airflow-altitude-shock-vibration-orientation-safety-and-emc"></a>Provozní flow, nadmořská, náraz, vibrace, orientace, bezpečnost a EMC

| Skříně | Provozní specifikace |
| --- | --- |
| Tok dat |Tok systému je zepředu zezadu. Systém musí být provozován s nízkým tlakem na zadní výfukovou instalaci. Zpětný tlak vytvořený dveřmi dveří a překážkami by neměl překročit 5 pascals (0,5 mm měřič vody). |
| Nadmořská výška, provozní |– 30 metrů až 3045 měřičů (-100 metrů až 10 000 metrů) s maximální provozní teplotou od 5 °C nad 7000 metrů. |
| Nadmořská výška, není funkční |-305 měřičů na 12 192 metrů (-1 000 metrů až na 40 000 stopy) |
| Úraz, provozní |5g 10 ms 1/2 – sinus |
| Úraz, jiný než provozní |30g 10 ms 1/2 – sinus |
| Vibrace, provozní |0.21 g RMS 5-500 Hz náhodně |
| Vibrace, jiné než provozní |1.04 g RMS 2-200 Hz náhodně |
| Vibrace, přemístění |sinus 3g 2-200 Hz |
| Orientace a připojení |19 připojení stojanu (2 jednotky EIA) |
| Rackové kolejnice |Chcete-li přizpůsobit minimální 700 mm (31,50 palců) hloubkovou stojany kompatibilní s IEC 297 |
| Bezpečnost a schválení |CE a UL EN 61000-3, IEC 61000-3, UL 61000-3 |
| SOFTWARE |EN55022 (CISPR-A), FCC A |

## <a name="international-standards-compliance"></a>Dodržování předpisů mezinárodními normami

Vaše zařízení Microsoft Azure StorSimple splňuje následující mezinárodní standardy:  

* CE-EN 60950-1
* Zpráva o 1,5 až IEC 60950-1
* UL a cUL na UL 60950-1

## <a name="safety-compliance"></a>Bezpečnostní dodržování předpisů

Vaše zařízení Microsoft Azure StorSimple splňuje následující bezpečnostní hodnocení:

* Schválení typu systémového produktu: UL, cUL, CE
* Bezpečnostní dodržování předpisů: UL 60950, IEC 60950, EN 60950

## <a name="emc-compliance"></a>Dodržování předpisů EMC

Vaše zařízení Microsoft Azure StorSimple splňuje následující hodnocení EMC.

### <a name="emissions"></a>Emisí

Zařízení je kompatibilní s EMC pro řízené a vycházející úrovně emisí.

* Mezní hodnoty emisí: CFR 47 část 15B třídy A EN55022 třídy A CISPR třídy A
* Počet vycházejících úrovní emisí: CFR 47 část 15B třídy A EN55022 třídy A CISPR třídy a

### <a name="harmonics-and-flicker"></a>Harmonické a blikání

Zařízení je v souladu s EN61000-3-2/3.

### <a name="immunity-limit-levels"></a>Úrovně omezení odolnosti

Zařízení je v souladu s EN55024.

## <a name="ac-power-cord-compliance"></a>Dodržování předpisů AC napájecí šňůry

Sestavení plug-and kompletního napájecího kabelu musí splňovat standardy vhodné pro zemi nebo oblast, ve které se zařízení používá, a musí mít bezpečnostní schválení, která jsou přijatelná v dané zemi nebo oblasti. V následujících tabulkách jsou uvedeny standardy pro USA a Evropu.

### <a name="ac-power-cords---usa-must-be-nrtl-listed"></a>Napájecí šňůra – USA (musí být uvedená v NRTL)

| Součást | Specifikace |
| --- | --- |
| Typ šňůry |SV nebo SVT, 18 AWG minimum, 3 vodič, maximální délka 2,0 metrů |
| Zapojit |NEMA 5 – 15P pro uzemnění typu Příloha s příhodnocením 120 V, 10 A; nebo IEC 320 C14, 250 V, 10 A |
| Zásuvky |IEC 320 C-13, 250 V, 10 A |

### <a name="ac-power-cords---europe"></a>Napájecí šňůra – Evropa

| Součást | Specifikace |
| --- | --- |
| Typ šňůry |Harmonizované, H05-VVF-3G 1.0 |
| Zásuvky |IEC 320 C-13, 250 V, 10 A |

## <a name="supported-network-cables"></a>Podporované síťové kabely

Pro síťová rozhraní 10 GbE, DATA 2 a DATA 3, najdete [seznam podporovaných síťových kabelů a modulů](storsimple-supported-hardware-for-10-gbe-network-interfaces.md).

## <a name="next-steps"></a>Další kroky

Teď jste připraveni nasadit zařízení StorSimple do svého datacentra. Další informace najdete v tématu [nasazení místního zařízení](storsimple-8000-deployment-walkthrough-u2.md).

