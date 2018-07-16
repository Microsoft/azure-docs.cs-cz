---
title: Technické specifikace StorSimple | Dokumentace Microsoftu
description: Popisuje technické specifikace a zákonných norem informace o dodržování předpisů pro StorSimple hardwarové komponenty.
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
ms.openlocfilehash: 855ea6c34082b859bb5b5b6e69b3e3f2fa54eb4a
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/14/2018
ms.locfileid: "39056459"
---
# <a name="technical-specifications-and-compliance-for-the-storsimple-device"></a>Technické specifikace a dodržování předpisů pro zařízení StorSimple

## <a name="overview"></a>Přehled

Hardwarové komponenty zařízení Microsoft Azure StorSimple dodržovat technické specifikace a zákonných norem uvedených v tomto článku. Technické specifikace popisují napájení a chlazení moduly (PCMs), diskových jednotek, kapacita úložiště a skříně. Informace o dodržování předpisů zahrnuje takové věci jako mezinárodními standardy bezpečnosti a emisí a kabeláž.

## <a name="power-and-cooling-module-specifications"></a>Specifikace napájení a chlazení modulu

Zařízení StorSimple má dvě 100 240 V duální ventilátor, CLS propojení sběrnice SBB moduly Power chlazení (PCMs). To poskytuje redundantní napájení konfiguraci. Pokud se nezdaří PCM, zařízení bude fungovat normálně dál na jiné PCM dokud se nahradí modulu se nezdařilo.

Skříň EBOD používá 580 W PCM a 764 W PCM používá primární skříň. V následujících tabulkách jsou přidružené k PCMs technických specifikací.

| Specifikace | 580 PCM W (EBOD) | 764 PCM W (primární) |
| --- | --- | --- |
| Maximální výstupní výkon |580 W |764 |
| Frekvence |50/60 Hz |50/60 Hz |
| Výběr rozsahu napětí |Automatické rozsahu: 47/63 Hz, V AC 90 – 264 |Automatické rozsahu: 90-264 V střídavý proud, Hz 47/63 |
| Maximální průvalu aktuální |20 A |20 A |
| Korekce faktor výkonu |> 95 % nominální vstupní napětí |> 95 % nominální vstupní napětí |
| Harmonické |Splňuje EN61000-3-2 |Splňuje EN61000-3-2 |
| Výstup |Snímač 5v síťového napětí pohotovostní \@ 2.0 A |Snímač 5v síťového napětí pohotovostní \@ 2.7 A |
| + 5 \@ 42 A |+ 5 \@ 40 A | |
| SNÍMAČ 12V SÍŤOVÉHO + \@ 38 A |SNÍMAČ 12V SÍŤOVÉHO + \@ 38 A | |
| Horká modulární |Ano |Ano |
| Přepínače a indikátorů LED |Přepínač nebo vypnout AC a čtyři stav indikátorů LED |Přepínač nebo vypnout AC a šest stav indikátorů LED |
| Chladicí zařízení |Axiální chladicí ventilátory, řízením ventilátoru proměnné rychlost |Axiální chladicí ventilátory, řízením ventilátoru proměnné rychlost |

## <a name="power-consumption-statistics"></a>Statistické údaje o spotřebě energie

Následující tabulka uvádí data o spotřebě typické napájení (skutečné hodnoty se mohou lišit od publikovanému) pro různé modely zařízení StorSimple.

| Podmínky | 240 V AC | 240 V AC | 240 V AC | 110 V AC | 110 V AC | 110 V AC |
| --- | --- | --- | --- | --- | --- | --- |
|  Pomalé, jednotky fanoušky nečinnosti |1,45 A |0.31 kW |1057.76 KJ/hod |3.19 A |0.34 kW |1160.13 KJ/hod |
|  Přístup k pomalé, jednotky fanoušků. |1.54 A |0.33 kW |1126.01 KJ/hod |3.27 A |0.36 kW |1228.37 KJ/hod |
|  S využitím dvou PSUs fanoušků rychlé, disky nečinné, |2.14 A |0.49 kW |1671.95 KJ/hod |4,99 A |0.54 kW |1842.56 KJ/hod |
|  Rychlé, fanoušků jednotkami nečinné, jeden PSU využívající jednu nečinnosti |2.05 A |0.48 kW |1637.83 KJ/hod |4.58 A |0,50 kW |1706.07 KJ/hod |
|  Přístup ke dvěma PSUs s využitím rychlé, jednotky fanoušků. |2.26 A |0,51 kW |1740.19 KJ/hod |4.95 A |0.54 kW |1842.56 KJ/hod |
|  Rychlé ventilátory, jednotky přístup, jeden PSU využívající jednu nečinnosti |2.14 A |0.49 kW |1671.95 KJ/hod |4.81 A |0.53 kW |1808.44 KJ/hod |

## <a name="disk-drive-specifications"></a>Specifikace diskovou jednotku

Zařízení StorSimple podporuje až 12 disky Serial Attached (SCSI SAS) faktor 3,5 formuláře. Skutečné jednotky se může jednat o kombinaci disků SSD (Solid-State Drive) a jednotky pevného disku (HDD), v závislosti na konfiguraci produktu. 12 sloty diskovou jednotku se nacházejí v konfiguraci s číslem 3 a 4 před přílohu. Skříň EBOD umožňuje další úložiště pro jiné 12 diskové jednotky. Toto jsou vždy pevných disků.

## <a name="storage-specifications"></a>Specifikace úložiště

Zařízení StorSimple mají pevných disků i disků SSD 8100 a 8600. Celková kapacita použitelné 8100 a 8600 jsou přibližně 15 nebo 38 TB. Následující tabulka popisuje podrobnosti SSD, HDD a kapacita cloudu v rámci kapacity řešení StorSimple.

| Model zařízení / kapacity | 8100 | 8600 |
| --- | --- | --- |
| Počet pevných disků (HDD) |8 |19 |
| Počet jednotek SSD (Solid-State Drive) |4 |5 |
| Kapacity jednoho HDD |4 TB |4 TB |
| Jeden kapacitu disků SSD |400 GB |800 GB |
| Rezervní kapacity |4 TB |4 TB |
| Využitelné kapacity pevný disk |14 TB |36 TB |
| Použitelné kapacitu disků SSD |800 GB |2 TB |
| Celkový počet použitelné kapacity * |~ 15 TB |~ 38 TB |
| Řešení maximální kapacita (včetně cloud) |200 TB |500 TB |

<sup>* </sup>- *Celková kapacita použitelné zahrnuje kapacitu k dispozici pro data, metadata a vyrovnávací paměti. Umožňuje zřizovat místně vázané svazky o velikosti až 8.5 TB v zařízení 8100 nebo až 22.5 TB větší zařízení 8600. Další informace najdete v části [StorSimple místně připojené svazky](storsimple-8000-local-volume-faq.md).*

## <a name="enclosure-dimensions-and-weight-specifications"></a>Dimenze skříň a váhy specifikace

Následující tabulky uvádějí různé specifikace skříň pro dimenze a váhy záznamu.

### <a name="enclosure-dimensions"></a>Dimenze skříň

V následující tabulce jsou uvedeny dimenze skříň v milimetrech a palců.

| Skříň | Milimetrech | Palců |
| --- | --- | --- |
| Výška |87.9 |3.46 |
| Šířka přes připojení ohraničení |483 |19.02 |
| Šířka v těle skříň |443 |17.44 |
| Hloubka od předního okraj těla skříň |577 |22.72 |
| Hloubka z operace panelů nejvzdálenější okraj skříň |630.5 |24.82 |
| Hloubka z připojení ohraničení nejvzdálenější okraj skříň |603 |23.74 |

### <a name="enclosure-weight"></a>Váha skříň

V závislosti na konfiguraci úplným načtením primární skříně můžete naváží od 21 33 kg a vyžaduje dvě osoby, aby to zvládnul.

| Skříň | Váha |
| --- | --- |
| Maximální tloušťka (závisí na konfiguraci) |30 kg – 33 kg |
| Prázdný (instalováno žádné jednotky) |21 – kg 23 |

## <a name="enclosure-environment-specifications"></a>Specifikace prostředí skříň

Tato část uvádí požadavky související s skříň prostředí. Teploty, vlhkosti, výšku, otřást, vibrace, orientace, zabezpečení a elektromagnetická kompatibility (EMC) jsou zahrnutá v této kategorii.

### <a name="temperature-and-humidity"></a>Teploty a vlhkosti

| Skříň | Rozsah okolní teploty | Ambientní relativní vlhkosti | Maximální vlhkou žárovky |
| --- | --- | --- | --- |
| Provozní |OC 5-35° C (41° F – 95° F) |20 – 80 % non kondenzačních- |28° C (82° F) |
| Nefunkční |-OC 40-70° C (40° F - 158° F) |5 – 100 % nekondenzující |29° C (84° F) |

### <a name="airflow-altitude-shock-vibration-orientation-safety-and-emc"></a>Vzduchu, výšku, otřást, vibrace, orientace, zabezpečení a EMC

| Skříň | Provozní specifikace |
| --- | --- |
| Vzduchu |Systém vzduchu je přední, zadní. Systém musí provozovat s Nízkotlaké zadní výfukového instalace. Protitlak vytvořené dveře do racku a překážky může být maximálně 5 pascalech (0,5 mm vody měřidla). |
| Výška, provozní |-30 měřiče 3045 měřiče (stop-100 na 10 000 stopy) s maximální provozní teplota zrušit hodnoceno 5 ° C výše 7000 stopy. |
| Výška, nefunkční |měření-305 až do 12 192 měřiče (-1,000 nohou na 40 000 stopy) |
| Otřást provozní |5g 10 ms, půlvlnný sinus |
| Otřást nefunkční |sinus 10 ms, půlvlnný 30g |
| Vibrace, provozní |0.21g RMS 5 500 Hz náhodné |
| Vibrace, nefunkční |1.04g RMS 2 200 Hz náhodné |
| Vibrace, přemístění |3g Hz 2 200 sinus |
| Orientace a připojení |19" namontovat (2 EIA jednotky) |
| Stojan rails |Hloubka minimální 700 mm (31.50 palců) podle racků kompatibilní s IEC 297 |
| Bezpečnost a schválení |CE a UL EN 61000-3, IEC 61000-3, UL 61000 3 |
| EMC |EN55022 (CISPR - A), FCC A |

## <a name="international-standards-compliance"></a>Mezinárodními standardy dodržování předpisů

Zařízení Microsoft Azure StorSimple splňuje následující mezinárodními standardy:  

* CE - EN 60950-1
* Sestava CB IEC 60950 1
* UL a cUL k UL 60950-1

## <a name="safety-compliance"></a>Dodržování předpisů zabezpečení

Vaše zařízení Microsoft Azure StorSimple splňuje následující bezpečnostní hodnocení:

* Schválení typ produktu systému: UL cUL CE
* Dodržování bezpečnostních předpisů: UL 60950 IEC 60950, cs 60950

## <a name="emc-compliance"></a>Dodržování předpisů EMC

Vaše zařízení Microsoft Azure StorSimple splňuje následující hodnocení EMC.

### <a name="emissions"></a>Emise

Zařízení kompatibilní se standardem EMC provedeny a vyzářený emisí úrovní.

* Provedeny emisí omezení úrovní: 47 CFR oddíl 15B třídy A EN55022 třídy A CISPR třída A
* Vyzářený emisí omezení úrovní: 47 CFR oddíl 15B třídy A EN55022 třídy A CISPR třída A

### <a name="harmonics-and-flicker"></a>Harmonické a blikání

Bude zařízení v souladu s EN61000-3-2/3.

### <a name="immunity-limit-levels"></a>Omezení úrovně odolnosti

Bude zařízení v souladu s EN55024.

## <a name="ac-power-cord-compliance"></a>Dodržování předpisů kabel power AC.

Moduly a kabel sestavení dokončeno power musí splňovat standardy, které jsou vhodné pro zemi, ve kterém je používán zařízení a musí mít bezpečný přístup z více schválení, které jsou přípustné v dané zemi. Následující tabulky uvádějí standardy pro USA a Evropě.

### <a name="ac-power-cords---usa-must-be-nrtl-listed"></a>Napájecích kabelů AC – USA (musí být uvedeny NRTL)

| Komponenta | Specifikace |
| --- | --- |
| Typ kabel |SV nebo SVT 18 AWG minimální, 3 conductor maximální délku měřiče 2.0 |
| Moduly |Moduly se seznámíte s obecnými typ přílohy 5 – 15P NEMA hodnocení 120 V, 10 A; nebo IEC 320 C14, 250 V, 10 A |
| Soketu |IEC 320 C-13 250 V 10 A |

### <a name="ac-power-cords---europe"></a>Napájecích kabelů AC – Evropa

| Komponenta | Specifikace |
| --- | --- |
| Typ kabel |Harmonizovaného H05. VVF 3G1.0 |
| Soketu |IEC 320 C-13 250 V 10 A |

## <a name="supported-network-cables"></a>Podporované síťové kabely

Pro rozhraní sítí 10 GbE DATA 2 a DATA 3, odkazuje na [seznam podporovaných síťové kabely a moduly](storsimple-supported-hardware-for-10-gbe-network-interfaces.md).

## <a name="next-steps"></a>Další postup

Nyní jste připraveni k nasazení zařízení StorSimple ve vašem datovém centru. Další informace najdete v tématu [nasazení místního zařízení](storsimple-8000-deployment-walkthrough-u2.md).

