---
title: Vysvětlení Azure Security Center pro doporučení zabezpečení IoT | Microsoft Docs
description: Přečtěte si o konceptu doporučení zabezpečení a o tom, jak se používají v Azure Security Center pro IoT.
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
ms.openlocfilehash: a8de821abcedf6bb9a331852a2c0af9b6439667a
ms.sourcegitcommit: f915d8b43a3cefe532062ca7d7dbbf569d2583d8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/05/2020
ms.locfileid: "78303575"
---
# <a name="security-recommendations"></a>Doporučení zabezpečení

Azure Security Center pro IoT kontroluje vaše prostředky Azure a zařízení IoT a poskytuje doporučení pro zabezpečení pro omezení prostoru pro útok. Doporučení zabezpečení jsou vhodná a zaměřují se na pomoc zákazníkům, kteří dodržují osvědčené postupy zabezpečení.

V tomto článku najdete seznam doporučení, která se můžou aktivovat na zařízeních IoT Hub a IoT.

## <a name="recommendations-for-iot-devices"></a>Doporučení pro zařízení IoT

Doporučení k zařízením poskytují přehledy a návrhy na vylepšení stav zabezpečení zařízení. 

| Severity | Název                                                      | Zdroj dat | Popis                                                                                                                                                                                           |
|----------|-----------------------------------------------------------|-------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Střednědobé používání   | Otevřít porty na zařízení                                      | Agent       | V zařízení byl nalezen koncový bod naslouchání.                                                                                                                                                        |
| Střednědobé používání   | Opravňující zásady brány firewall nalezené v jednom z řetězů. | Agent       | Byly nalezeny povolené zásady brány firewall (vstup/výstup). Zásada brány firewall by měla ve výchozím nastavení odepřít veškerý provoz a definovat pravidla, která budou umožňovat potřebnou komunikaci do a ze zařízení.                               |
| Střednědobé používání   | Bylo nalezeno opravňující pravidlo brány firewall ve vstupním řetězci.     | Agent       | Bylo zjištěno pravidlo v bráně firewall, které obsahuje povolující vzor pro široké spektrum IP adres nebo portů.                                                                                    |
| Střednědobé používání   | Bylo nalezeno opravňující pravidlo brány firewall ve výstupním řetězci.    | Agent       | Bylo zjištěno pravidlo v bráně firewall, které obsahuje povolující vzor pro široké spektrum IP adres nebo portů.                                                                                   |
| Střednědobé používání   | Ověření standardních hodnot operačního systému se nezdařilo.           | Agent       | Zařízení nedodržuje [srovnávací testy modelu SNS pro Linux](https://www.cisecurity.org/cis-benchmarks/).                                                                                                        |

### <a name="operational-recommendations-for-iot-devices"></a>Provozní doporučení pro zařízení IoT

Provozní doporučení poskytují přehledy a návrhy na vylepšení konfigurace agenta zabezpečení.

| Severity | Název                                    | Zdroj dat | Popis                                                                       |
|----------|-----------------------------------------|-------------|-----------------------------------------------------------------------------------|
| Nízká      | Agent odesílá nevyužité zprávy.          | Agent       | 10% nebo více zpráv zabezpečení bylo méně než 4 KB za posledních 24 hodin.  |
| Nízká      | Konfigurace se zdvojeným zabezpečením není optimální | Agent       | Konfigurace s dvojitou bezpečností není optimální.                                        |
| Nízká      | Konflikt konfigurace se zdvojeným zabezpečením    | Agent       | V konfiguraci se zdvojeným zabezpečením byly zjištěny konflikty. |                          |
|

## <a name="recommendations-for-iot-hub"></a>Doporučení pro IoT Hub

Výstrahy doporučení poskytují přehled a návrhy akcí pro zlepšení stav zabezpečení vašeho prostředí.  

| Severity | Název                                                     | Zdroj dat | Popis                                                                                                                                                                                                             |
|----------|----------------------------------------------------------|-------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Vysoký     | Totožná pověření ověřování používaná více zařízeními | IoT Hub     | IoT Hub přihlašovací údaje pro ověřování používají více zařízení. To může znamenat, že zařízení illegitimate zosobňuje legitimní zařízení. Duplicitní použití přihlašovacích údajů zvyšuje riziko zosobnění zařízení škodlivým objektem actor. |
| Střednědobé používání   | Výchozí zásady filtru IP adres by se měly odepřít.                  | IoT Hub     | Konfigurace filtru IP adres by měla mít pravidla definovaná pro povolený provoz a měla by ve výchozím nastavení Odepřít všechny ostatní přenosy.                                                                                                     |
| Střednědobé používání   | Pravidlo filtru IP obsahuje velký rozsah IP adres.                   | IoT Hub     | Rozsah IP adres zdroje povoleného pravidla filtru IP je příliš velký. Přesná povolující pravidla můžou vaše centrum IoT vystavovat škodlivým aktérům.                                                                                       |
| Nízká      | Povolit diagnostické protokoly v IoT Hub                       | IoT Hub     | Povolte protokoly a zachovejte je po dobu až do roku. Uchovávání protokolů umožňuje znovu vytvořit stopy aktivity pro účely šetření, pokud dojde k bezpečnostnímu incidentu nebo dojde k ohrožení vaší sítě.                                       |
|

## <a name="next-steps"></a>Další kroky

- [Přehled](overview.md) služby Azure Security Center for IoT
- Informace o [přístupu k datům zabezpečení](how-to-security-data-access.md)
- Další informace o [prověřování zařízení](how-to-investigate-device.md)