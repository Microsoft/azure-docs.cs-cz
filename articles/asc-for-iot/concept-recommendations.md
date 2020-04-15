---
title: Doporučení zabezpečení
description: Seznamte se s konceptem doporučení zabezpečení a jejich používáním v Centru zabezpečení Azure pro IoT.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: 02ced504-d3aa-4770-9d10-b79f80af366c
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/24/2019
ms.author: mlottner
ms.openlocfilehash: 213595ac69efc90ec855b2891641e1f00bd1ba92
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/14/2020
ms.locfileid: "81311548"
---
# <a name="security-recommendations"></a>Doporučení zabezpečení

Azure Security Center pro IoT prohledá vaše prostředky Azure a zařízení IoT a poskytuje bezpečnostní doporučení pro snížení vašeho útoku povrchu.
Bezpečnostní doporučení jsou žalovatelná a jejich cílem je pomoci zákazníkům dodržovat osvědčené postupy zabezpečení.

V tomto článku najdete seznam doporučení, která se dá aktivovat na vašich zařízeních IoT Hub a/nebo IoT.

## <a name="recommendations-for-iot-devices"></a>Doporučení pro zařízení IoT

Doporučení zařízení poskytují přehledy a návrhy na zlepšení stavu zabezpečení zařízení.

| Severity | Name (Název)                                                      | Zdroj dat | Popis                                                                                                                                                                                           |
|----------|-----------------------------------------------------------|-------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Střednědobé používání   | Otevřít porty na zařízení                                      | Agent       | V zařízení byl nalezen koncový bod naslouchání .                                                                                                                                                        |
| Střednědobé používání   | Pomůckná zásada brány firewall nalezená v jednom z řetězců. | Agent       | Byla nalezena povolená zásada brány firewall (INPUT/OUTPUT). Zásady brány firewall by měly ve výchozím nastavení odepřít veškerý provoz a definovat pravidla umožňující potřebnou komunikaci se zařízením nebo z ní.                               |
| Střednědobé používání   | Bylo nalezeno pravidlo permisivní brány firewall ve vstupním řetězci.     | Agent       | Bylo nalezeno pravidlo brány firewall, které obsahuje tolerantní vzor pro širokou škálu adres IP nebo portů.                                                                                    |
| Střednědobé používání   | Bylo nalezeno pravidlo permisivní brány firewall ve výstupním řetězci.    | Agent       | Bylo nalezeno pravidlo brány firewall, které obsahuje tolerantní vzor pro širokou škálu adres IP nebo portů.                                                                                   |
| Střednědobé používání   | Ověření směrného plánu operačního systému se nezdařilo.           | Agent       | Zařízení není v souladu s [referenčními hodnotami CIS Linux](https://www.cisecurity.org/cis-benchmarks/).                                                                                                        |

### <a name="operational-recommendations-for-iot-devices"></a>Provozní doporučení pro zařízení IoT

Provozní doporučení poskytují přehledy a návrhy na zlepšení konfigurace agenta zabezpečení.

| Severity | Name (Název)                                    | Zdroj dat | Popis                                                                       |
|----------|-----------------------------------------|-------------|-----------------------------------------------------------------------------------|
| Nízká      | Agent odesílá nevyužité zprávy          | Agent       | 10 % nebo více bezpečnostních zpráv bylo za posledních 24 hodin menší než 4 KB.  |
| Nízká      | Konfigurace dvojčete zabezpečení není optimální | Agent       | Konfigurace dvojčete zabezpečení není optimální.                                        |
| Nízká      | Konflikt konfigurace dvojčete zabezpečení    | Agent       | Konflikty byly zjištěny v konfiguraci dvojčete zabezpečení. |                          |
|

## <a name="recommendations-for-iot-hub"></a>Doporučení pro ioT hub

Upozornění na doporučení poskytují přehled a návrhy akcí ke zlepšení stavu zabezpečení vašeho prostředí.

| Severity | Name (Název)                                                     | Zdroj dat | Popis                                                                                                                                                                                                             |
|----------|----------------------------------------------------------|-------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Vysoká     | Identické ověřovací pověření používaná více zařízeními | IoT Hub     | Ověřovací pověření služby IoT Hub používají více zařízení. To může znamenat nelegitimní zařízení zosobnění legitimní zařízení. Použití duplicitních pověření zvyšuje riziko zosobnění zařízení škodlivým objektem actor. |
| Střednědobé používání   | Výchozí zásady filtru IP by měly být odepřeny                  | IoT Hub     | Konfigurace filtru IP by měla mít pravidla definovaná pro povolený provoz a ve výchozím nastavení by měla ve výchozím nastavení odepřít všechny ostatní přenosy.                                                                                                     |
| Střednědobé používání   | Pravidlo filtru IP obsahuje velký rozsah IP adres                   | IoT Hub     | Rozsah IP adres ip filtru povolit ip filtr je příliš velký. Příliš tolerantní pravidla mohou vystavit vaše centrum IoT škodlivým aktérům.                                                                                       |
| Nízká      | Povolení protokolů diagnostiky v centru IoT Hub                       | IoT Hub     | Povolte protokoly a uchovávejte je po dobu až jednoho roku. Uchovávání protokolů umožňuje znovu vytvořit stopy aktivit pro účely šetření, když dojde k incidentu zabezpečení nebo dojde k ohrožení sítě.                                       |
|

## <a name="next-steps"></a>Další kroky

- [Přehled](overview.md) služby Azure Security Center for IoT
- Přečtěte si, jak získat přístup k [bezpečnostním datům](how-to-security-data-access.md)
- Další informace o [zkoumání zařízení](how-to-investigate-device.md)
