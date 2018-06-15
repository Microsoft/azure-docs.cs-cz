---
title: Rozhraní hardwaru pro zařízení StorSimple 10 GbE | Microsoft Docs
description: Popisuje podporované malé faktor formuláře modulární vysílače (SFP), kabely a přepínače pro rozhraní sítě 10 GbE zařízení StorSimple.
services: storsimple
documentationcenter: NA
author: alkohli
manager: carmonm
editor: ''
ms.assetid: df8d40c7-f5ad-4f84-93eb-779fbd5f7243
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 09/21/2016
ms.author: alkohli
ms.openlocfilehash: db03b3cd668bf8e35913872ac4225de6d4d3edd1
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/11/2017
ms.locfileid: "23927751"
---
# <a name="supported-hardware-for-the-10-gbe-network-interfaces-on-your-storsimple-device"></a>Podporovaném hardwaru 10 GbE síťových rozhraních zařízení StorSimple
## <a name="overview"></a>Přehled
Tento článek obsahuje informace o doplňující hardwaru, který funguje s zařízení s Microsoft Azure StorSimple.

## <a name="list-of-devices-tested-by-microsoft"></a>Seznam zařízení testovaná společností Microsoft
Společnost Microsoft testovala následující malé formuláře factor modulární vysílače (SFP), kabely a přepínače zajistit, aby fungovaly optimálně s zařízení. (V následujících tabulkách bude aktualizovat, protože je testován nový hardware.)

### <a name="sfp-transceivers"></a>SFP + vysílače
| Ujistěte se | Model |
| --- | --- |
| Cisco |SFP-10G-SR |

### <a name="cables"></a>Kabely
| Sér. Ne. | Ujistěte se | Model |
| --- | --- | --- |
| 1. |Cisco |SFP. H10GB CU1M |
| 2. |Cisco |SFP. H10GB CU2M |
| 3. |Cisco |SFP. H10GB CU3M |
| 4. |Tripp Lite |N820 - 05M (OM3) |

### <a name="switches"></a>Přepínače
| Sér. Ne. | Ujistěte se | Model |
| --- | --- | --- |
| 1. |Cisco |N3K. C3172PQ 10GE |
| 2. |Cisco |N3K-C3048-ZM – F |
| 3. |Cisco |N5K-C5596UP – DM |

## <a name="list-of-devices-tested-in-the-field"></a>Seznam zařízení testována v poli
Tato část obsahuje seznam zařízení, která byla úspěšně nasazena v poli StorSimple zákazníků. Nebyly testovány společností Microsoft, ale pravděpodobně pracovat s zařízení StorSimple.

| Parametr | Hodnota |
| --- | --- |
| Ujistěte se, přepínače |Juniper |
| Přepínače modelu |ex4550 32F |
| Verze operačního systému přepínač |JunOS 12.3R9.4 |
| Okno modelu |Porty zařadit (PIC 0) |
| Zkontrolujte vysílač |Juniper |
| Vysílač modelu |Výrobní číslo 740 021308 <br></br> Výrobní číslo 740 030658 |
| Verze firmwaru vysílač |REV 01 verze 0,0 (hlášené) |
| Kabel modelu |Duplexní můstek LC/LC 50/125µ, OM3, LSZH |
| Model zařízení StorSimple |8600 |
| Verze softwaru zařízení StorSimple |6.3.9600.17491 |

## <a name="list-of-devices-tested-by-oem-provider-mellanox"></a>Seznam zařízení testována poskytovatelem OEM (Mellanox)
Mellanox testovala následující malé formuláře factor modulární vysílače (SFP), kabely a přepínače zajistit, aby fungovaly optimálně s Mellanox síťových rozhraní, například 10 GbE síťových rozhraní zařízení StorSimple.

### <a name="cables-and-modules-supported-by-mellanox"></a>Kabelů a moduly nepodporuje Mellanox
Následující tabulka uvádí kabelů a moduly nepodporuje Mellanox. Nebyly testovány společností Microsoft, ale pravděpodobně pracovat s zařízení StorSimple.

| Sér. Ne. | Rychlost | Model | Popis | Ujistěte se |
| --- | --- | --- | --- | --- |
| 1. |10 GbE |SOUBOR CAB-SFP-SFP - 1 MILION |pasivní mědi kabel 1 milion SFP + 10 Gb/s |Arista |
| 2. |10 GbE |SOUBOR CAB-SFP-SFP - 2M |pasivní mědi kabel SFP + 10 Gb/s 2m |Arista |
| 3. |10 GbE |SOUBOR CAB-SFP-SFP - 3M |pasivní mědi kabel SFP + 10 Gb/s 3m |Arista |
| 4. |10 GbE |SOUBOR CAB-SFP-SFP - 5M |pasivní mědi kabel SFP + 10 Gb/s 5m |Arista |
| 5. |10 GbE |Cisco SFP-H10GBCU1M |Cisco SFP + kabel |Cisco |
| 6. |10 GbE |Cisco SFP-H10GBCU3M |Cisco SFP + kabel |Cisco |
| 7. |10 GbE |Cisco SFP-H10GBCU5M |Cisco SFP + kabel |Cisco |
| 8. |10 GbE |J9281B HP X242 10 G |SFP + pro SFP + 1 milion mědi kabel s přímým přístupem |HP |
| 9. |10 GbE |BLc 455883 B21 HP |10Gb SR SFP + Opt |HP |
| 10. |10 GbE |BLc 455886 B21 HP |10Gb LR SFP + Opt |HP |
| 11. |10 GbE |BLc 487649 B21 HP |SFP + m 0,5 10GbE Cu kabel |HP |
| 12. |10 GbE |BLc 487652 B21 HP |1 milion 10GbE SFP + Cu kabel |HP |
| 13. |10 GbE |BLc 487655 B21 HP |3m 10GbE SFP + Cu kabel |HP |
| 14. |10 GbE |BLc 487658 B21 HP |SFP + 7m 10GbE Cu kabel |HP |
| 15. |10 GbE |BLc 537963 B21 HP |SFP + 5m 10GbE Cu kabel |HP |
| 16. |10 GbE |AP784A HP |3m C-series pasivní mědi SFP + kabel |HP |
| 17. |10 GbE |AP785A HP |5m C-series pasivní mědi SFP + kabel |HP |
| 18. |10 GbE |AP818A HP |1 milion B-series Active mědi SFP + kabel |HP |
| 19. |10 GbE |AP819A HP |3m B-series Active mědi SFP + kabel |HP |
| 20. |10 GbE |J9150A HP |X132 10 G SFP + LC SR vysílač |HP |
| 21. |10 GbE |J9151A HP |X132 10 G SFP + LC LR vysílač |HP |
| 22. |10 GbE |J9283B HP |X242 10 G SFP + SFP + 3 m DAC kabel |HP |
| 23. |10 GbE |J9285B HP |X242 10 G SFP + SFP + 7 m DAC kabel |HP |
| 24. |10 GbE |JD095B HP |X240 10 G SFP + SFP + 0.65 m DAC kabel |HP |
| 25. |10 GbE |JD096B HP |X240 10 G SFP + SFP + 1.2 m DAC kabel |HP |
| 26. |10 GbE |JD097B HP |X240 10 G SFP + SFP + 3 m DAD kabel |HP |
| 27. |10 GbE |MAM1Q00A QSA Mellanox |QSFP SFP + adaptéru |Mellanox technologie |
| 28. |10 GbE |MT MC2309124-006 – |X SFP+ pasivní mědi kabel 1 do 10 Gb/s 24awg QSFP 7 m |Mellanox technologie |
| 29. |10 GbE |MC2309124 007 Mt |X SFP+ pasivní mědi kabel 1 do 10 Gb/s 24awg QSFP 7 m |Mellanox technologie |
| 30. |10 GbE |Mt MC2309130 003 |X SFP+ pasivní mědi kabel 1 do 10 Gb/s 30awg QSFP 3 m |Mellanox technologie |
| 31. |10 GbE |Mt MC2309130 00A |X SFP+ pasivní mědi kabel 1 do 10 Gb/s 30awg QSFP 0,5 m |Mellanox technologie |
| 32. |10 GbE |Mt MC3309124 005 |Pasivní mědi zapojení 1 24awg 10 Gb/s x SFP+ 5 m |Mellanox technologie |
| 33. |10 GbE |MC3309124 007 Mt |Pasivní mědi zapojení 1 24awg 10 Gb/s x SFP+ 7 m |Mellanox technologie |
| 34. |10 GbE |Mt MC3309130 003 |Pasivní mědi zapojení 1 30awg 10 Gb/s x SFP+ 3 m |Mellanox technologie |
| 35. |10 GbE |Mt MC3309130 00A |Pasivní mědi zapojení 1 30awg 10 Gb/s x SFP+ 0,5 m |Mellanox technologie |

### <a name="switches-supported-by-mellanox"></a>Přepínače nepodporuje Mellanox
Následující tabulka uvádí přepínače nepodporuje Mellanox. Nebyly testovány společností Microsoft, ale pravděpodobně pracovat s zařízení StorSimple.

| Sér. Ne. | Rychlost | Model | Popis | Ujistěte se |
| --- | --- | --- | --- | --- |
| 1. |10GbE |516733 B21 |Přepínač okno HP ProCurve 6120XG 10GbE Ethernet |HP |
| 2. |10GbE |538113 B21 |HP 10GbE průchozí modulu (druh) |HP |
| 3. |10GbE |EN4093 |IBM PureFlex systému prostředků infrastruktury EN4093 10gigabitový škálovatelné přepínač modulu |IBM |
| 4. |1GbE |3020 |Okno přepínač 1GbE zprostředkující 3020 Cisco |Cisco |
| 5. |1GbE |3020 X |Okno přepínač zprostředkující 3020 X 1GbE Cisco |Cisco |
| 6. |1GbE |438030 B21 |Modul přepínače 1GbE HP - přepínač Blade sítě Ethernet vrstvy 2 nebo 3 GbE2c |HP |
| 7. |1GbE |6120G |HP ProCurve 6120G nebo XG 1GbE přepínač okna |HP |

## <a name="next-steps"></a>Další kroky
[Další informace o zařízení StorSimple hardwarové součásti a stav](storsimple-monitor-hardware-status.md).

