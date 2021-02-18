---
title: Doporučení založená na agentech
titleSuffix: Azure Defender for IoT
description: Přečtěte si o konceptu doporučení zabezpečení a o tom, jak se používají pro Defender pro zařízení IoT.
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: shhazam-ms
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/16/2021
ms.author: shhazam
ms.openlocfilehash: c7407db1460c2d0a83f2262a3348c81b4de300a7
ms.sourcegitcommit: 58ff80474cd8b3b30b0e29be78b8bf559ab0caa1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/17/2021
ms.locfileid: "100642418"
---
# <a name="security-recommendations-for-iot-devices"></a>Doporučení zabezpečení pro zařízení IoT

Defender pro IoT kontroluje vaše prostředky Azure a zařízení IoT a poskytuje doporučení pro zabezpečení pro omezení prostoru pro útok.
Doporučení zabezpečení jsou vhodná a zaměřují se na pomoc zákazníkům v souladu s osvědčenými postupy zabezpečení.

V tomto článku najdete seznam doporučení, která se můžou aktivovat na zařízeních IoT.

## <a name="agent-based-recommendations"></a>Doporučení založená na agentech

Doporučení k zařízením poskytují přehledy a návrhy na vylepšení stav zabezpečení zařízení.

| Závažnost | Name | Zdroj dat | Description |
|--|--|--|--|
| Střední | Otevřít porty na zařízení | Klasický modul zabezpečení | V zařízení byl nalezen koncový bod naslouchání. |
| Střední | Opravňující zásady brány firewall nalezené v jednom z řetězů. | Klasický modul zabezpečení | Byly nalezeny povolené zásady brány firewall (vstup/výstup). Zásada brány firewall by měla ve výchozím nastavení odepřít veškerý provoz a definovat pravidla, která budou umožňovat potřebnou komunikaci do a ze zařízení. |
| Střední | Bylo nalezeno opravňující pravidlo brány firewall ve vstupním řetězci. | Klasický modul zabezpečení | Bylo zjištěno pravidlo v bráně firewall, které obsahuje povolující vzor pro široké spektrum IP adres nebo portů. |
| Střední | Bylo nalezeno opravňující pravidlo brány firewall ve výstupním řetězci. | Klasický modul zabezpečení | Bylo zjištěno pravidlo v bráně firewall, které obsahuje povolující vzor pro široké spektrum IP adres nebo portů. |
| Střední | Ověření standardních hodnot operačního systému se nezdařilo. | Klasický modul zabezpečení | Zařízení nedodržuje [srovnávací testy modelu SNS pro Linux](https://www.cisecurity.org/cis-benchmarks/). |

### <a name="agent-based-operational-recommendations"></a>Provozní doporučení založená na agentech

Provozní doporučení poskytují přehledy a návrhy na vylepšení konfigurace agenta zabezpečení.

| Závažnost | Name | Zdroj dat | Description |
|--|--|--|--|
| Nízká | Agent odesílá nevyužité zprávy. | Klasický modul zabezpečení | 10% nebo více zpráv zabezpečení bylo méně než 4 KB za posledních 24 hodin. |
| Nízká | Konfigurace se zdvojeným zabezpečením není optimální | Klasický modul zabezpečení | Konfigurace s dvojitou bezpečností není optimální. |
| Nízká | Konflikt konfigurace se zdvojeným zabezpečením | Klasický modul zabezpečení | V konfiguraci se zdvojeným zabezpečením byly zjištěny konflikty. |  |

## <a name="next-steps"></a>Další kroky

- Defender pro službu IoT [– Přehled](overview.md)
- Informace o [přístupu k datům zabezpečení](how-to-security-data-access.md)
- Další informace o [prověřování zařízení](how-to-investigate-device.md)
