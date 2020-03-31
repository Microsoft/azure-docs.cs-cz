---
title: Hardware pro rozhraní StorSimple 10 GbE | Dokumenty společnosti Microsoft
description: Popisuje podporované vysílače, kabely a přepínače s malým tvarovým faktorem (SFP) pro síťová rozhraní 10 GbE na zařízení StorSimple.
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
ms.openlocfilehash: 7fafe177ea0c6c618dc4ab0727ba14c83cbb0102
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "68965019"
---
# <a name="supported-hardware-for-the-10-gbe-network-interfaces-on-your-storsimple-device"></a>Podporovaný hardware pro síťová rozhraní 10 GbE na zařízení StorSimple

[!INCLUDE [storsimple-8000-eol-banner](../../includes/storsimple-8000-eol-banner.md)]

## <a name="overview"></a>Přehled
Tento článek obsahuje informace o doplňkovém hardwaru, který funguje s vaším zařízením Microsoft Azure StorSimple.

## <a name="list-of-devices-tested-by-microsoft"></a>Seznam zařízení testovaných společností Microsoft
Společnost Microsoft testovala následující vysílače, kabely a přepínače s malým tvarovým faktorem (SFP), aby bylo zajištěno, že fungují optimálně se zařízeními. (Následující tabulky budou aktualizovány při testování nového hardwaru.)

### <a name="sfp-transceivers"></a>Vysílače SFP+
| Značka | Model |
| --- | --- |
| Cisco |SFP-10G-SR |

### <a name="cables"></a>Kabely
| Sér. Ne. | Značka | Model |
| --- | --- | --- |
| 1. |Cisco |SFP-H10GB-CU1M |
| 2. |Cisco |SFP-H10GB-CU2M |
| 3. |Cisco |SFP-H10GB-CU3M |
| 4. |Tripp-Lite |N820-05M (OM3) |

### <a name="switches"></a>Přepínače
| Sér. Ne. | Značka | Model |
| --- | --- | --- |
| 1. |Cisco |N3K-C3172PQ-10GE |
| 2. |Cisco |N3K-C3048-ZM-F |
| 3. |Cisco |N5K-C5596UP-DM |

## <a name="list-of-devices-tested-in-the-field"></a>Seznam zařízení testovaných v terénu
Tato část obsahuje seznam zařízení, která byla úspěšně nasazena v poli zákazníky StorSimple. Ty nebyly testovány společností Microsoft, ale je pravděpodobné, že budou pracovat se zařízením StorSimple.

| Parametr | Hodnota |
| --- | --- |
| Přepínač make |Juniper |
| Přepnout model |ex4550-32F |
| Přepnout verzi operačního systému |Junos 12.3R9.4 |
| Model čepele |Porty na palubě (PIC 0) |
| Transceiver, aby |Juniper |
| Transceiver model |Číslo dílu 740-021308 <br></br> Číslo dílu 740-030658 |
| Verze firmwaru Vysílač |Rev 01 Verze 0.0 (hlášeno) |
| Kabelový model |Duplexní propojka LC/LC 50/125μ, OM3, LSZH |
| StorJednoduchý model |8600 |
| StorJednoduchá verze softwaru |6.3.9600.17491 |

## <a name="list-of-devices-tested-by-oem-provider-mellanox"></a>Seznam zařízení testovaných poskytovatelem OEM (Mellanox)
Mellanox testoval následující malé vysílače, kabely a přepínače s malým tvarovým faktorem (SFP), aby bylo zajištěno, že fungují optimálně s síťovými rozhraními Mellanox, jako jsou síťová rozhraní 10 GbE na vašem zařízení StorSimple.

### <a name="cables-and-modules-supported-by-mellanox"></a>Kabely a moduly podporované Mellanoxem
V následující tabulce jsou uvedeny kabely a moduly podporované mellanoxem. Ty nebyly testovány společností Microsoft, ale je pravděpodobné, že budou pracovat se zařízením StorSimple.

| Sér. Ne. | Rychlost | Model | Popis | Značka |
| --- | --- | --- | --- | --- |
| 1. |10 GbE |CAB-SFP-SFP-1M |pasivní měděný kabel SFP+ 10 Gb/s 1m |Arista |
| 2. |10 GbE |CAB-SFP-SFP-2M |pasivní měděný kabel SFP+ 10 Gb/s 2m |Arista |
| 3. |10 GbE |CAB-SFP-SFP-3M |pasivní měděný kabel SFP+ 10 Gb/s 3m |Arista |
| 4. |10 GbE |CAB-SFP-SFP-5M |pasivní měděný kabel SFP+ 10 Gb/s 5m |Arista |
| 5. |10 GbE |Cisco SFP-H10GBCU1M |Kabel Cisco SFP+ |Cisco |
| 6. |10 GbE |Cisco SFP-H10GBCU3M |Kabel Cisco SFP+ |Cisco |
| 7. |10 GbE |Cisco SFP-H10GBCU5M |Kabel Cisco SFP+ |Cisco |
| 8. |10 GbE |J9281B HP X242 10G |SFP+ to SFP+ 1m Měděný kabel s přímým připojením |Hp |
| 9. |10 GbE |455883-B21 HP BLc |10gb SR SFP+ Opt |Hp |
| 10. |10 GbE |455886-B21 HP BLc |10gb LR SFP+ Opt 10gb LR SFP+ Opt 10gb LR SFP+ Opt 10 |Hp |
| 11. |10 GbE |487649-B21 HP BLc |SFP+ 0,5m 10GbE měděný kabel |Hp |
| 12. |10 GbE |487652-B21 HP BLc |SFP+ 1m 10GbE měděný kabel |Hp |
| 13. |10 GbE |487655-B21 HP BLc |SFP+ 3m 10GbE měděný kabel |Hp |
| 14. |10 GbE |487658-B21 HP BLc |SFP+ 7m 10GbE měděný kabel |Hp |
| 15. |10 GbE |537963-B21 HP BLc |SFP+ 5m 10GbE měděný kabel |Hp |
| 16. |10 GbE |AP784A HP |3m C-series pasivní měděný kabel SFP+ |Hp |
| 17. |10 GbE |AP785A HP |5m C-series pasivní měděný kabel SFP+ |Hp |
| 18. |10 GbE |AP818A HP |1m B-série Active Copper SFP + Kabel |Hp |
| 19. |10 GbE |AP819A HP |3m B-série Active Copper SFP + Kabel |Hp |
| 20. |10 GbE |J9150A HP |X132 10G SFP+ LC SR vysílač |Hp |
| 21. |10 GbE |J9151A HP |X132 10G SFP+ LC LR vysílač |Hp |
| 22. |10 GbE |J9283B HP |X242 10G SFP+ SFP+ 3m DAC kabel |Hp |
| 23. |10 GbE |J9285B HP |X242 10G SFP+ SFP+ 7m DAC kabel |Hp |
| 24. |10 GbE |JD095B HP |X240 10G SFP+ SFP+ 0,65m DAC kabel |Hp |
| 25. |10 GbE |JD096B HP |X240 10G SFP+ SFP+ 1,2m DAC kabel |Hp |
| 26. |10 GbE |JD097B HP |X240 10G SFP+ SFP+ 3m DAD kabel |Hp |
| 27. |10 GbE |MAM1Q00A-QSA Mellanox |Adaptér QSFP na SFP+ |Mellanox Technologie |
| 28. |10 GbE |MC2309124-006 Mt |Pasivní měděný kabel 1x SFP+ To QSFP 10Gb/s 24awg 7m |Mellanox Technologie |
| 29. |10 GbE |MC2309124-007 Mt |Pasivní měděný kabel 1x SFP+ To QSFP 10Gb/s 24awg 7m |Mellanox Technologie |
| 30. |10 GbE |MC2309130-003 Mt |Pasivní měděný kabel 1x SFP+ To QSFP 10Gb/s 30awg 3m |Mellanox Technologie |
| 31. |10 GbE |MC2309130-00A Mt |Pasivní měděný kabel 1x SFP+ To QSFP 10Gb/s 30awg 0,5m |Mellanox Technologie |
| 32. |10 GbE |MC3309124-005 Mt |Pasivní měděný kabel 1x SFP+ 10Gb/s 24awg 5m |Mellanox Technologie |
| 33. |10 GbE |MC3309124-007 Mt |Pasivní měděný kabel 1x SFP+ 10Gb/s 24awg 7m |Mellanox Technologie |
| 34. |10 GbE |MC3309130-003 Mt |Pasivní měděný kabel 1x SFP+ 10Gb/s 30awg 3m |Mellanox Technologie |
| 35. |10 GbE |MC3309130-00A Mt |Pasivní měděný kabel 1x SFP+ 10Gb/s 30awg 0,5m |Mellanox Technologie |

### <a name="switches-supported-by-mellanox"></a>Přepínače podporované Mellanoxem
V následující tabulce jsou uvedeny přepínače podporované mellanoxem. Ty nebyly testovány společností Microsoft, ale je pravděpodobné, že budou pracovat se zařízením StorSimple.

| Sér. Ne. | Rychlost | Model | Popis | Značka |
| --- | --- | --- | --- | --- |
| 1. |10gbe |516733-B21 |HP ProCurve 6120XG 10GbE Ethernet Blade Switch |Hp |
| 2. |10gbe |538113-B21 |Průchozí modul HP 10GbE (PTM) |Hp |
| 3. |10gbe |EN4093 |IBM PureFlex System Fabric EN4093 10 Gigabit Scalable Switch Module |IBM |
| 4. |1gbe |3020 |Přepínač Cisco Catalyst 3020 1GbE |Cisco |
| 5. |1gbe |3020x |Přepínač Cisco Catalyst 3020X 1GbE |Cisco |
| 6. |1gbe |438030-B21 |Přepínací modul HP 1GbE - Přepínač listů GbE2c Layer 2/3 |Hp |
| 7. |1gbe |6120G |Výhybka HP ProCurve 6120G/XG 1GbE |Hp |

## <a name="next-steps"></a>Další kroky
[Další informace o hardwarových součástech A stavu StorSimple](storsimple-monitor-hardware-status.md).

