---
title: Vysvětlení doporučení zabezpečení IoT ve verzi Preview pro Azure Security Center | Dokumentace Microsoftu
description: Další informace o konceptu doporučení týkající se zabezpečení a jak se používají ve službě Azure Security Center pro IoT.
services: asc-for-iot
ms.service: ascforiot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: 02ced504-d3aa-4770-9d10-b79f80af366c
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/24/2019
ms.author: mlottner
ms.openlocfilehash: 122a1585abf43d1aedc7f329cd7e41a6228f8c3e
ms.sourcegitcommit: 0568c7aefd67185fd8e1400aed84c5af4f1597f9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/06/2019
ms.locfileid: "65200673"
---
# <a name="security-recommendations"></a>Doporučení zabezpečení

> [!IMPORTANT]
> Azure Security Center pro IoT je aktuálně ve verzi public preview.
> Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro úlohy v produkčním prostředí. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti. Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Azure Security Center (ASC) pro IoT kontroluje prostředky Azure a IoT zařízení a poskytuje doporučení ke snížení útoky týkající se zabezpečení. Doporučení zabezpečení jsou napadnutelné a zaměřte se na podporu zákazníků v souladu se na osvědčené postupy zabezpečení.

V tomto článku najdete seznam doporučení, která se dá spouštět na váš IoT Hub a/nebo zařízení IoT.

## <a name="recommendations-for-iot-devices"></a>Doporučení pro zařízení IoT

Doporučení zařízení poskytují přehledy a návrhy a zlepšit stav zabezpečení zařízení. 

| Severity | Název                                                      | Zdroj dat | Popis                                                                                                                                                                                           |
|----------|-----------------------------------------------------------|-------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Střednědobé používání   | Otevření portů na zařízení                                      | Agent       | Na zařízení se našel koncový bod naslouchacího procesu.                                                                                                                                                          |
| Střednědobé používání   | Zásady firewallu povolující nalezeny v jednom z řetězců. | Agent       | Povolené zásady brány firewall najít (vstup/výstup). Zásady brány firewall by měla odepřít veškerý provoz ve výchozím nastavení a definovat pravidla, která povolí nezbytné komunikaci ze zařízení.                               |
| Střednědobé používání   | Pravidlo povolující brány firewall ve vstupním řetězci nebyl nalezen.     | Agent       | Pravidlo v bráně firewall byla nalezena, která obsahuje povolující vzor pro širokou škálu IP adresy nebo porty.                                                                                    |
| Střednědobé používání   | Pravidlo brány firewall na povolující výstupní řetězec nebyl nalezen.    | Agent       | Pravidlo v bráně firewall byla nalezena, která obsahuje povolující vzor pro širokou škálu IP adresy nebo porty.                                                                                   |
| Střednědobé používání   | Operace systému základní ověření se nezdařilo           | Agent       | Zařízení nevyhovuje [srovnávací testy CIS Linux](https://www.cisecurity.org/cis-benchmarks/)                                                                                                         |

### <a name="operational-recommendations-for-iot-devices"></a>Provozní doporučení pro zařízení IoT

Provozní doporučení poskytují přehledy a návrhy pro zlepšení zabezpečení konfigurace agenta.

| Severity | Název                                    | Zdroj dat | Popis                                                                       |
|----------|-----------------------------------------|-------------|-----------------------------------------------------------------------------------|
| Nízká      | Agent odesílá zemědělsky nevyužitá zprávy          | Agent       | 10 % nebo více zpráv zabezpečení byly menší než 4kb během posledních 24 hodin.  |
| Nízká      | Není optimální konfigurace dvojčete zabezpečení | Agent       | Konfigurace zabezpečení dvojčete není ideální.                                        |
| Nízká      | Konflikt konfigurace dvojčete zabezpečení    | Agent       | V konfiguraci dvojčete zabezpečení byly zjištěny konflikty.                           |


## <a name="recommendations-for-iot-hub"></a>Doporučení pro službu IoT Hub

Doporučení výstrahy poskytují přehledy a návrhy pro akce a zlepšit stav zabezpečení vašeho prostředí.  

| Severity | Název                                                     | Zdroj dat | Popis                                                                                                                                                                                                             |
|----------|----------------------------------------------------------|-------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Vysoký     | Stejné ověřování přihlašovacích údajů, které používá více zařízení | IoT Hub     | Přihlašovací údaje pro ověření služby IoT Hub se používají ve více zařízeních. To může znamenat zařízení s nezákonných zosobnění legitimní zařízení. Použití přihlašovacích údajů duplicitní zvyšují riziko zosobnění zařízení podle škodlivý objekt actor. |
| Střednědobé používání   | Výchozí zásady filtru IP by měly odepřít.                  | IoT Hub     | Konfigurace IP adresy filtru by měl mít nastavená pravidla definovaná pro povolený provoz a měli byste ve výchozím nastavení, ve výchozím nastavení zakazují všechny ostatní provoz.                                                                                                     |
| Střednědobé používání   | Pravidla filtru IP obsahuje velký rozsah IP adres                   | IoT Hub     | Rozsah IP pravidlo zdroje povolit IP filtru je moc velká. Příliš benevolentními pravidly můžete zveřejnit služby IoT hub pro útočníky.                                                                                       |
| Nízká      | Povolit diagnostické protokoly v IoT Hubu                       | IoT Hub     | Povolení protokolů a udrželi si je pro až po roce. Uchování protokolů umožňuje znovu vytvořit záznamy aktivit za účelem šetření, když dojde k incidentu zabezpečení nebo dojde k ohrožení vaší sítě.                                       |
|