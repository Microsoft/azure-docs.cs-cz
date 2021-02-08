---
title: Doporučení zabezpečení
description: Přečtěte si o konceptu doporučení zabezpečení a o tom, jak se používají v programu Defender pro IoT.
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
ms.date: 01/25/2021
ms.author: shhazam
ms.openlocfilehash: d11154e26e2ffe97a1ea102a11dddb746db9433a
ms.sourcegitcommit: 4784fbba18bab59b203734b6e3a4d62d1dadf031
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/08/2021
ms.locfileid: "99809265"
---
# <a name="security-recommendations"></a>Doporučení zabezpečení

Defender pro IoT kontroluje vaše prostředky Azure a zařízení IoT a poskytuje doporučení pro zabezpečení pro omezení prostoru pro útok.
Doporučení zabezpečení jsou vhodná a zaměřují se na pomoc zákazníkům v souladu s osvědčenými postupy zabezpečení.

V tomto článku najdete seznam doporučení, která se můžou aktivovat na zařízeních IoT Hub nebo IoT.

## <a name="agent-based-recommendations"></a>Doporučení založená na agentovi

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


## <a name="built-in-recommendations-in-iot-hub"></a>Integrovaná doporučení v IoT Hub

Výstrahy doporučení poskytují přehled a návrhy akcí pro zlepšení stav zabezpečení vašeho prostředí.

| Závažnost | Name | Zdroj dat | Description |
|--|--|--|--|
| Vysoká | Totožná pověření ověřování používaná více zařízeními | IoT Hub | IoT Hub přihlašovací údaje pro ověřování používají více zařízení. Tento proces může znamenat, že zařízení illegitimate zosobňuje legitimní zařízení. Duplicitní použití přihlašovacích údajů zvyšuje riziko zosobnění zařízení škodlivým objektem actor. |
| Střední | Výchozí zásady filtru IP adres by se měly odepřít. | IoT Hub | Konfigurace filtru IP adres by měla mít pravidla definovaná pro povolený provoz a měla by ve výchozím nastavení Odepřít všechny ostatní přenosy. |
| Střední | Pravidlo filtru IP obsahuje velký rozsah IP adres. | IoT Hub | Rozsah IP adres zdroje povoleného pravidla filtru IP je příliš velký. Přesná povolující pravidla můžou vaše centrum IoT vystavovat škodlivým aktérům. |
| Nízká | Povolit diagnostické protokoly v IoT Hub | IoT Hub | Povolte protokoly a zachovejte je po dobu až do roku. Uchovávání protokolů umožňuje znovu vytvořit stopy aktivity pro účely šetření, pokud dojde k bezpečnostnímu incidentu nebo dojde k ohrožení vaší sítě. |


## <a name="next-steps"></a>Další kroky

- Defender pro službu IoT [– Přehled](overview.md)
- Informace o [přístupu k datům zabezpečení](how-to-security-data-access.md)
- Další informace o [prověřování zařízení](how-to-investigate-device.md)
