---
title: Možnosti Microsoft Azure IoT | Dokumentace Microsoftu
description: Zvolte, jak implementovat řešení IoT pomocí akcelerátorů řešení Azure IoT, Microsoft IoT Central nebo Azure IoT Hub.
services: iot-suite
suite: iot-suite
author: dominicbetts
manager: timlt
ms.assetid: 2d38d08a-4133-4e5c-8b28-f93cadb5df05
ms.service: iot-suite
ms.topic: get-started-article
ms.date: 11/10/2017
ms.author: dobett
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: ddda6bc265cbbaa61025891cf9bc7c28c998160c
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/07/2018
---
# <a name="compare-azure-iot-options"></a>Porovnání možností Azure IoT

Článek [Azure a Internet věcí](iot-suite-what-is-azure-iot.md) popisuje typickou architekturu řešení IoT s následujícími vrstvami:

* Správa a připojení zařízení
* Zpracování a analýza dat
* Prezentace a obchodní integrace

Azure IoT pro implementaci této architektury nabízí několik možností, které jsou vhodné pro různé sady zákaznických požadavků:

* [Akcelerátory řešení Azure IoT](index.md) jsou kolekce [akcelerátorů řešení](iot-suite-what-are-solution-accelerators.md) na podnikové úrovni, která je založená na platformě Azure jako službě (PaaS) a umožňuje zrychlit vývoj vlastních řešení IoT.

* [Microsoft IoT Central](https://www.microsoft.com/internet-of-things/iot-central-saas-solutions) je řešení SaaS (software jako služba), které využívá přístup založený na modelu a umožňuje vytvářet řešení IoT na podnikové úrovni, aniž by to vyžadovalo zkušenosti s vývojem cloudových řešení.

## <a name="azure-iot-hub"></a>Azure IoT Hub

Služba Azure IoT Hub představuje základní Azure PaaS a využívá ji Microsoft IoT Central i akcelerátory řešení Azure IoT. IoT Hub umožňuje spolehlivou a zabezpečenou obousměrnou komunikaci mezi miliony zařízení IoT a cloudovým řešením. IoT Hub pomáhá plnit náročné úkoly implementace problémy, jako jsou:

* Správa a připojení zařízení velkého rozsahu
* Ingestování telemetrie velkého rozsahu
* Příkazy a ovládání zařízení
* Vynucení zabezpečení zařízení

## <a name="compare-azure-iot-solution-accelerators-and-microsoft-iot-central"></a>Porovnání akcelerátorů řešení Azure IoT a Microsoft IoT Central

Volba produktu Azure IoT je důležitou součástí plánování řešení IoT. IoT Hub je individuální služba Azure, která sama o sobě neposkytuje kompletní řešení IoT. Službu IoT Hub můžete použít jako výchozí bod pro jakékoli řešení IoT a k jejímu využití nepotřebujete akcelerátory řešení Azure IoT ani Microsoft IoT Central. Akcelerátory řešení Azure IoT i Microsoft IoT Central využívají službu IoT Hub společně s dalšími službami Azure. Následující tabulka shrnuje hlavní rozdíly mezi akcelerátory řešení Azure IoT a Microsoft IoT Central a pomůže vám při výběru vhodné varianty pro vaše požadavky:

|                        | Akcelerátory řešení Azure IoT | Microsoft IoT Central |
| ---------------------- | --------- | ----------- |
| Primární využití | Ke zrychlení vývoje vlastního řešení IoT, které vyžaduje maximální flexibilitu | Ke zkrácení doby uvedení na trh pro jednoduchá řešení IoT, která nevyžadují rozsáhlá přizpůsobení služeb |
| Přístup k podkladovým službám PaaS          | Máte přístup k podkladovým službám Azure a můžete je spravovat nebo podle potřeby vyměnit. | SaaS. Plně spravovaná řešení, podkladové služby nejsou zveřejněné. |
| Flexibilita            | Vysoká. Kód pro mikroslužby je typu open source a můžete ho upravit libovolným způsobem podle svých potřeb. Kromě toho můžete přizpůsobit infrastrukturu nasazení.| Střední. Můžete využít předdefinované uživatelské prostředí založené na prohlížeči a přizpůsobit model řešení a aspekty uživatelského rozhraní. Infrastrukturu nejde přizpůsobit, protože jednotlivé komponenty se nezveřejňují.|
| Úroveň dovedností                 | Středně vysoká. K přizpůsobení back-endu řešení potřebujete znalosti Javy nebo .NET. K přizpůsobení vizualizace potřebujete znalosti JavaScriptu. | Nízká. K přizpůsobení řešení potřebujete znalosti modelování. Znalosti kódování nejsou potřeba. |
| Úvodní prostředí | Akcelerátory řešení implementují běžné scénáře IoT. Je možné je nasadit v řádu minut. | Šablony aplikací a šablony zařízení poskytují předdefinované modely. Je možné je nasadit v řádu minut. |
| Ceny                | Služby můžete doladit tak, abyste měli náklady pod kontrolou. | Jednoduchá a předvídatelná struktura cena. |

Rozhodnutí, který produkt využít k sestavení vašeho řešení IoT, určují hlavně:

* Vaše obchodní požadavky
* Typ řešení, které chcete sestavit
* Dovednosti vaší organizace pro sestavování a správu řešení z dlouhodobého hlediska

## <a name="next-steps"></a>Další kroky

Na základě zvoleného produktu a přístupu jsou navrhujeme tyto další kroky:

* **Akcelerátory řešení Azure IoT:** [Co jsou akcelerátory řešení Azure IoT?](iot-suite-what-are-solution-accelerators.md)
* **Microsoft IoT Central:** [Microsoft IoT Central](https://www.microsoft.com/internet-of-things/iot-central-saas-solutions).
* **IoT Hub:** [Přehled služby Azure IoT Hub](../iot-hub/iot-hub-what-is-iot-hub.md).
