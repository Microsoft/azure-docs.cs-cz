---
title: Hardware pro rozhraní StorSimple 10 GbE | Microsoft Docs
description: V této části najdete popis podporovaného malého přijímače, kabelů a přepínačů pro síťová rozhraní 10 GbE na zařízení StorSimple.
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
ms.openlocfilehash: fd30c7ec15eadd3d7945349a8aae4ac824bfdc7f
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/20/2020
ms.locfileid: "94954082"
---
# <a name="supported-hardware-for-the-10-gbe-network-interfaces-on-your-storsimple-device"></a>Podporovaný hardware pro síťová rozhraní 10 GbE na zařízení StorSimple

[!INCLUDE [storsimple-8000-eol-banner](../../includes/storsimple-8000-eol-banner.md)]

## <a name="overview"></a>Přehled
Tento článek poskytuje informace o doplňkovém hardwaru, který spolupracuje se zařízením s Microsoft Azure StorSimple.

## <a name="list-of-devices-tested-by-microsoft"></a>Seznam zařízení testovaných společností Microsoft
Společnost Microsoft testovala následující malé vysílače, kabely a přepínače, a zajišťuje tak optimální fungování zařízení pomocí malých Form, kabelů a přepínačů. (Následující tabulky budou aktualizovány při testování nového hardwaru.)

### <a name="sfp-transceivers"></a>SFP + vysílače
| Značka | Modelování |
| --- | --- |
| Cisco |SFP – 10G – SR |

### <a name="cables"></a>Kabely
| USA: Ne. | Značka | Modelování |
| --- | --- | --- |
| 1. |Cisco |SFP – H10GB – CU1M |
| 2. |Cisco |SFP – H10GB – CU2M |
| 3. |Cisco |SFP – H10GB – CU3M |
| 4. |Tripp-Lite |N820-05M (OM3) |

### <a name="switches"></a>Přepínače
| USA: Ne. | Značka | Modelování |
| --- | --- | --- |
| 1. |Cisco |N3K-C3172PQ-10GE |
| 2. |Cisco |N3K-C3048-ZM-F |
| 3. |Cisco |N5K-C5596UP-FA |

## <a name="list-of-devices-tested-in-the-field"></a>Seznam zařízení testovaných v poli
Tato část obsahuje seznam zařízení, která byla úspěšně nasazena v poli StorSimple zákazníky. Tyto testy nebyly testovány společností Microsoft, ale pravděpodobně budou pracovat se zařízením StorSimple.

| Parametr | Hodnota |
| --- | --- |
| Přepnout |Juniper |
| Přepnout model |ex4550-32F |
| Přepnout verzi operačního systému |JunOS 12.3 R 9.4 |
| Model okna |Zprovoznění portů (PIC 0) |
| Vytvořit vysílač |Juniper |
| Model vysílače |Číslo součásti 740-021308 <br></br> Číslo součásti 740-030658 |
| Verze firmwaru vysílače |Rev 01 verze 0,0 (oznámeno) |
| Model kabelu |Duplexní můstek LC/LC 50/125μ, OM3, LSZH |
| Model StorSimple |8600 |
| Verze StorSimple softwaru |6.3.9600.17491 |

## <a name="list-of-devices-tested-by-oem-provider-mellanox"></a>Seznam zařízení testovaných poskytovatelem OEM (Mellanox)
Mellanox testoval následující malé přijímače, kabely a přepínače, aby fungovaly optimálně s Mellanox síťovými rozhraními, jako jsou například Síťová rozhraní 10 GbE na zařízení StorSimple.

### <a name="cables-and-modules-supported-by-mellanox"></a>Kabely a moduly podporované Mellanox
V následující tabulce jsou uvedeny kabely a moduly, které podporuje Mellanox. Tyto testy nebyly testovány společností Microsoft, ale pravděpodobně budou pracovat se zařízením StorSimple.

| USA: Ne. | Rychlost | Modelování | Popis | Značka |
| --- | --- | --- | --- | --- |
| 1. |10 GbE |CAB-SFP-SFP-1 1M |pasivní dráty Copper – kabel + 10 GB/s 1m |Arista |
| 2. |10 GbE |CAB – SFP – SFP – 2 MIN |pasivní kabel Copper kabelové SFP + 10 GB/s 2 min |Arista |
| 3. |10 GbE |CAB – SFP – SFP – 3M |pasivní kabel Copper kabelové SFP + 10 GB/s 3m |Arista |
| 4. |10 GbE |CAB – SFP – SFP – 5 MIN |pasivní kabel Copper kabelové SFP + 10 GB/s 5 min |Arista |
| 5. |10 GbE |Cisco SFP – H10GBCU1M |Cisco SFP + – kabel |Cisco |
| 6. |10 GbE |Cisco SFP – H10GBCU3M |Cisco SFP + – kabel |Cisco |
| 7. |10 GbE |Cisco SFP – H10GBCU5M |Cisco SFP + – kabel |Cisco |
| 8. |10 GbE |J9281B HP X242 10G |SFP + až SFP + 1m připojit měděný kabel |EMULEX |
| 9. |10 GbE |455883 – B21 HP BLc |10Gb SR SFP + opt |EMULEX |
| 10. |10 GbE |455886 – B21 HP BLc |10Gb LR SFP + opt |EMULEX |
| 11. |10 GbE |487649 – B21 HP BLc |10GbE Copper kabel SFP + 0,5 m |EMULEX |
| 12. |10 GbE |487652 – B21 HP BLc |SFP + 1 milion 10GbE Copper kabel |EMULEX |
| 13,5. |10 GbE |487655 – B21 HP BLc |3m 10GbE SFP + Copper kabel |EMULEX |
| čtrnáct. |10 GbE |487658 – B21 HP BLc |7 min 10GbE SFP + Copper kabel |EMULEX |
| 15. |10 GbE |537963 – B21 HP BLc |5 min 10GbE SFP + Copper kabel |EMULEX |
| 16bitovém. |10 GbE |AP784A HP |3m-Series pasivní Copper SFP + kabel |EMULEX |
| sedmnáct. |10 GbE |AP785A HP |5 min-Series pasivní Copper SFP + kabel |EMULEX |
| let. |10 GbE |AP818A HP |1 milion B-Series aktivní Copper SFP + kabel |EMULEX |
| čl. |10 GbE |AP819A HP |3m B-Series aktivní Copper SFP + kabel |EMULEX |
| 20o. |10 GbE |J9150A HP |X132 10G SFP + LC SR vysílač |EMULEX |
| 20. |10 GbE |J9151A HP |X132 10G SFP + LC LR vysílač |EMULEX |
| 22. |10 GbE |J9283B HP |X242 10G SFP + SFP + 3m DAC Cable |EMULEX |
| listopadu. |10 GbE |J9285B HP |X242 10G SFP + SFP + 7 min DAC Cable |EMULEX |
| 24. |10 GbE |JD095B HP |X240 10G SFP + SFP + 0.65 m DAC Cable |EMULEX |
| 0,25. |10 GbE |JD096B HP |X240 10G SFP + SFP + 1,2 m – kabel DAC |EMULEX |
| 26. |10 GbE |JD097B HP |X240 10G SFP + SFP + 3m, kabel DÁD |EMULEX |
| dlouhý. |10 GbE |MAM1Q00A – QUALIFIED Security ASSESSOR Mellanox |QSFP na SFP + Adapter |Mellanox technologie |
| 28. |10 GbE |MC2309124-006 Antiqua |Pasivní kabel Copper pro 1x SFP + až QSFP 10 GB/s 24AWG 7 min |Mellanox technologie |
| 29. |10 GbE |MC2309124-007 Antiqua |Pasivní kabel Copper pro 1x SFP + až QSFP 10 GB/s 24AWG 7 min |Mellanox technologie |
| 30. |10 GbE |MC2309130 – 003 MT |Pasivní kabel Copper pro 1x SFP + až QSFP 10 GB/s 30AWG 3m |Mellanox technologie |
| čl. |10 GbE |MC2309130-00A Antiqua |Pasivní kabel Copper pro 1x SFP + až QSFP 10Gb/s 30AWG 0.5 m |Mellanox technologie |
| 32. |10 GbE |MC3309124-005 Antiqua |Pasivní kabel Copper pro 1x SFP + 10 GB/s 24AWG 5 min |Mellanox technologie |
| 33. |10 GbE |MC3309124-007 Antiqua |Pasivní kabel Copper pro 1x SFP + 10 GB/s 24AWG 7 min |Mellanox technologie |
| 34. |10 GbE |MC3309130 – 003 MT |Pasivní kabel Copper pro 1x SFP + 10 GB/s 30AWG 3m |Mellanox technologie |
| 35. |10 GbE |MC3309130-00A Antiqua |Pasivní kabel mědi s číslem 1x + 10 GB/s 30AWG 0.5 m |Mellanox technologie |

### <a name="switches-supported-by-mellanox"></a>Přepínače podporované Mellanox
V následující tabulce jsou uvedeny přepínače podporované nástrojem Mellanox. Tyto testy nebyly testovány společností Microsoft, ale pravděpodobně budou pracovat se zařízením StorSimple.

| USA: Ne. | Rychlost | Modelování | Popis | Značka |
| --- | --- | --- | --- | --- |
| 1. |10GbE |516733 – B21 |Přepínač HP prozatáčka 6120XG 10GbE Ethernet Switch |EMULEX |
| 2. |10GbE |538113 – B21 |Modul PTM (HP 10GbE Pass-Through Module) |EMULEX |
| 3. |10GbE |EN4093 |Modul IBM PureFlex System Fabric EN4093 10 Gigabit-Scalable Switch Module |IBM |
| 4. |10 GbE LOM |3020 |Okno přepínače Cisco Catalyst 3020 10 GbE LOM |Cisco |
| 5. |10 GbE LOM |3020X |Okno přepínače Cisco Catalyst 3020X 10 GbE LOM |Cisco |
| 6. |10 GbE LOM |438030 – B21 |Modul HP 10 GbE LOM Switch – přepínač GbE2c vrstvy 2/3 Ethernet |EMULEX |
| 7. |10 GbE LOM |6120G |Okno 6120G spínače HP prokřivení/XG 10 GbE LOM |EMULEX |

## <a name="next-steps"></a>Další kroky
[Přečtěte si další informace o StorSimple hardwarových komponentách a stavu](./storsimple-8000-monitor-hardware-status.md).