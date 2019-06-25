---
title: Hardware pro StorSimple 10 GbE rozhraní | Dokumentace Microsoftu
description: Popisuje podporované malé uspořádání formuláře modulární vysílače (SFP), kabely a přepínače pro rozhraní sítí 10 GbE pro zařízení StorSimple.
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
ms.openlocfilehash: 8303195f0f3228ee145cbba9e322ea4e5e4c1264
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "64726960"
---
# <a name="supported-hardware-for-the-10-gbe-network-interfaces-on-your-storsimple-device"></a>Podporované hardware pro rozhraní sítí 10 GbE na zařízení StorSimple
## <a name="overview"></a>Přehled
Tento článek obsahuje informace o doplňkových hardwaru, který spolupracuje s Microsoft Azure StorSimple zařízení.

## <a name="list-of-devices-tested-by-microsoft"></a>Seznam zařízení otestované microsoftem
Společnost Microsoft testovala následující (SFP) vysílače modulární malé provedení, kabely a přepínače k zajištění, že fungují optimálně se zařízeními. (V následujících tabulkách se aktualizovat, protože je testován nový hardware.)

### <a name="sfp-transceivers"></a>SFP + vysílače
| Vytvoření | Model |
| --- | --- |
| Cisco |SFP-10G-SR |

### <a name="cables"></a>Kabely
| Sér. Ne. | Vytvoření | Model |
| --- | --- | --- |
| 1. |Cisco |SFP-H10GB-CU1M |
| 2. |Cisco |SFP-H10GB-CU2M |
| 3. |Cisco |SFP-H10GB-CU3M |
| 4. |Tripp Lite |N820-05M (OM3) |

### <a name="switches"></a>Přepínače
| Sér. Ne. | Vytvoření | Model |
| --- | --- | --- |
| 1. |Cisco |N3K-C3172PQ-10GE |
| 2. |Cisco |N3K-C3048-ZM-F |
| 3. |Cisco |N5K-C5596UP-FA |

## <a name="list-of-devices-tested-in-the-field"></a>Seznam zařízení testovány v poli
Tato část obsahuje seznam zařízení, které byly úspěšně nasazeny v poli zákazníky StorSimple. Nebyly testovány od Microsoftu, ale jsou pravděpodobně fungovat s vaším zařízením StorSimple.

| Parametr | Hodnota |
| --- | --- |
| Ujistěte se, přepínače |Juniper |
| Přepínač modelu |ex4550-32F |
| Verze operačního systému přepínač |JunOS 12.3R9.4 |
| Okno modelu |Připojit porty (PIC 0) |
| Zkontrolujte vysílač |Juniper |
| Vysílač modelu |Výrobní číslo 740 021308 <br></br> Výrobní číslo 740 030658 |
| Vysílač verze firmwaru |REV 01 verze 0,0 (uvedené) |
| Kabel modelu |Duplexní můstek LC/LC 50/125µ, OM3, LSZH |
| StorSimple modelu |8600 |
| Verze softwaru StorSimple |6.3.9600.17491 |

## <a name="list-of-devices-tested-by-oem-provider-mellanox"></a>Seznam zařízení testovat poskytovatelem OEM (Mellanox)
Mellanox testovala následující (SFP) vysílače modulární malé provedení, kabely a přepínače k zajištění, že fungují optimálně Mellanox síťových rozhraní, jako je například rozhraní sítí 10 GbE na zařízení StorSimple.

### <a name="cables-and-modules-supported-by-mellanox"></a>Moduly nepodporuje Mellanox a kabely
V následující tabulce jsou uvedeny kabely a moduly nepodporuje Mellanox. Nebyly testovány od Microsoftu, ale jsou pravděpodobně fungovat s vaším zařízením StorSimple.

| Sér. Ne. | Rychlost | Model | Popis | Vytvoření |
| --- | --- | --- | --- | --- |
| 1. |10 GbE |CAB-SFP-SFP-1M |pasivní měděného kabelu SFP + 10 Gb/s 1 min |Arista |
| 2. |10 GbE |CAB-SFP-SFP-2M |pasivní měděného kabelu SFP + 10 Gb/s 2 min |Arista |
| 3. |10 GbE |CAB-SFP-SFP-3M |pasivní měděného kabelu SFP + 10 Gb/s 3m |Arista |
| 4. |10 GbE |CAB-SFP-SFP-5M |pasivní měděného kabelu SFP + 10 Gb/s 5 min |Arista |
| 5. |10 GbE |Cisco SFP-H10GBCU1M |Cisco SFP + kabel |Cisco |
| 6. |10 GbE |Cisco SFP-H10GBCU3M |Cisco SFP + kabel |Cisco |
| 7. |10 GbE |Cisco SFP-H10GBCU5M |Cisco SFP + kabel |Cisco |
| 8. |10 GbE |J9281B HP X242 10G |SFP + SFP + 1 milion měděného kabelu s přímým přístupem |HP |
| 9. |10 GbE |455883-B21 HP BLc |10Gb SR SFP + Opt |HP |
| 10. |10 GbE |455886-B21 HP BLc |10Gb LR SFP + Opt |HP |
| 11. |10 GbE |487649-B21 HP BLc |SFP + 0,5 m 10GbE Cu kabel |HP |
| 12. |10 GbE |487652-B21 HP BLc |1 milion 10GbE SFP + Cu kabel |HP |
| 13. |10 GbE |487655-B21 HP BLc |3m 10GbE SFP + Cu kabel |HP |
| 14. |10 GbE |487658-B21 HP BLc |SFP + 7 min 10GbE Cu kabel |HP |
| 15. |10 GbE |537963-B21 HP BLc |SFP + 5 min 10GbE Cu kabel |HP |
| 16. |10 GbE |AP784A HP |3m C-series pasivní mědi SFP + kabel |HP |
| 17. |10 GbE |AP785A HP |5 min C-series pasivní mědi SFP + kabel |HP |
| 18. |10 GbE |AP818A HP |1 milion řady B-series aktivní mědi SFP + kabel |HP |
| 19. |10 GbE |AP819A HP |3m řady B-series aktivní mědi SFP + kabel |HP |
| 20. |10 GbE |J9150A HP |X132 10 G SFP + LC SR vysílač |HP |
| 21. |10 GbE |J9151A HP |X132 10 G SFP + LC LR vysílač |HP |
| 22. |10 GbE |J9283B HP |X242 10 G SFP + SFP + 3 m DAC kabel |HP |
| 23. |10 GbE |J9285B HP |X242 10 G SFP + SFP + 7 min DAC kabel |HP |
| 24. |10 GbE |JD095B HP |X240 10 G SFP + SFP + 0.65 m DAC kabel |HP |
| 25. |10 GbE |JD096B HP |X240 10 G SFP + SFP + 1.2 m DAC kabel |HP |
| 26. |10 GbE |JD097B HP |X240 10 G SFP + SFP + 3 m TÁTA kabel |HP |
| 27. |10 GbE |MAM1Q00A-QSA Mellanox |QSFP SFP + adaptéru |Mellanox technologie |
| 28. |10 GbE |MC2309124-006 Mt |X SFP+ pasivní měděného kabelu 1 do 10 Gb/s 24awg QSFP 7 min |Mellanox technologie |
| 29. |10 GbE |MC2309124-007 Mt |X SFP+ pasivní měděného kabelu 1 do 10 Gb/s 24awg QSFP 7 min |Mellanox technologie |
| 30. |10 GbE |MC2309130-003 Mt |X SFP+ pasivní měděného kabelu 1 do 10 Gb/s 30awg QSFP 3 m |Mellanox technologie |
| 31. |10 GbE |MC2309130-00A Mt |X SFP+ pasivní měděného kabelu 1 do 10 Gb/s 30awg QSFP 0,5 m |Mellanox technologie |
| 32. |10 GbE |MC3309124-005 Mt |Pasivní mědi zapojení 1 24awg x SFP+ 10 Gb/s 5 min |Mellanox technologie |
| 33. |10 GbE |MC3309124-007 Mt |Pasivní mědi zapojení 24awg 10 Gb/s 1 x SFP+ 7 min |Mellanox technologie |
| 34. |10 GbE |MC3309130-003 Mt |Pasivní mědi zapojení 30awg 10 Gb/s 1 x SFP+ 3 m |Mellanox technologie |
| 35. |10 GbE |MC3309130-00A Mt |Pasivní mědi zapojení 30awg 10 Gb/s 1 x SFP+ 0,5 m |Mellanox technologie |

### <a name="switches-supported-by-mellanox"></a>Podporuje Mellanox přepínače
V následující tabulce jsou uvedeny přepínačů podporuje Mellanox. Nebyly testovány od Microsoftu, ale jsou pravděpodobně fungovat s vaším zařízením StorSimple.

| Sér. Ne. | Rychlost | Model | Popis | Vytvoření |
| --- | --- | --- | --- | --- |
| 1. |10GbE |516733-B21 |Přepínač okno HP ProCurve 6120XG 10GbE Ethernet |HP |
| 2. |10GbE |538113-B21 |HP 10GbE předávací modulu (druh) |HP |
| 3. |10GbE |EN4093 |Škálovatelné přepínač modulu EN4093 10 gigabitů IBM PureFlex systému prostředků infrastruktury |IBM |
| 4. |1GbE |3020 |Cisco zprostředkující 3020 1GbE přepínač okno |Cisco |
| 5. |1GbE |3020X |Okno přepínač zprostředkující 3020 X 1GbE Cisco |Cisco |
| 6. |1GbE |438030-B21 |Modul přepínače 1GbE HP - Blade Ethernet vrstvy 2 nebo 3 GbE2c Switch |HP |
| 7. |1GbE |6120G |HP ProCurve 6120G/XG 1GbE přepínač okno |HP |

## <a name="next-steps"></a>Další postup
[Další informace o StorSimple hardwarové součásti a stav](storsimple-monitor-hardware-status.md).

