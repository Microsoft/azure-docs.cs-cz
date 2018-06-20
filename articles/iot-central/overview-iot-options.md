---
title: Možnosti Microsoft Azure IoT | Microsoft Docs
description: Zvolte, jak implementovat řešení Azure IoT pomocí Azure IoT Central, akcelerátorů řešení IoT nebo služby IoT Hub.
author: dominicbetts
ms.author: dobett
ms.date: 11/30/2017
ms.topic: overview
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: timlt
ms.openlocfilehash: 0314bf4d26fb0f777fd88debbf09814479ab225a
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34630523"
---
# <a name="compare-azure-iot-central-and-azure-iot-options"></a>Porovnání Azure IoT Central a Azure IoT Suite

Pokud chcete implementovat řešení IoT, nabízí vám Azure IoT Suite a Azure IoT Central několik možností vhodných pro různé sady zákaznických požadavků:

* [Azure IoT Central](overview-iot-central.md) je řešení SaaS (software jako služba), které využívá přístup založený na modelu a umožňuje vytvářet řešení IoT na podnikové úrovni, aniž by to vyžadovalo zkušenosti s vývojem cloudových řešení.

* [Akcelerátory řešení Azure IoT](https://docs.microsoft.com/azure/iot-accelerators/) jsou kolekce [akcelerátorů řešení](../iot-accelerators/iot-accelerators-what-are-solution-accelerators.md) na podnikové úrovni, která je založená na platformě Azure jako službě (PaaS) a umožňuje zrychlit vývoj vlastních řešení IoT.

## <a name="azure-iot-hub"></a>Azure IoT Hub

Služba Azure IoT Hub představuje základní Azure PaaS a využívají ji Azure IoT Central i akcelerátory řešení Azure IoT. IoT Hub umožňuje spolehlivou a zabezpečenou obousměrnou komunikaci mezi miliony zařízení IoT a cloudovým řešením. IoT Hub pomáhá plnit náročné úkoly implementace problémy, jako jsou:

* Správa a připojení zařízení velkého rozsahu
* Ingestování telemetrie velkého rozsahu
* Příkazy a ovládání zařízení
* Vynucení zabezpečení zařízení

## <a name="compare-azure-iot-central-and-azure-iot-solution-accelerators"></a>Porovnání akcelerátorů řešení Azure IoT a Azure IoT Central

Volba produktu Azure IoT je důležitou součástí plánování řešení IoT. IoT Hub je individuální služba Azure, která sama o sobě neposkytuje kompletní řešení IoT. Službu IoT Hub můžete použít jako výchozí bod pro jakékoli řešení IoT a k jejímu využití nepotřebujete akcelerátory řešení Azure IoT ani Azure IoT Central. Akcelerátory řešení IoT i Azure IoT Central využívají službu IoT Hub společně s dalšími službami Azure. Následující tabulka shrnuje hlavní rozdíly mezi akcelerátory řešení IoT a Azure IoT Central a pomůže vám při výběru vhodné varianty pro vaše požadavky:

|     | Azure IoT Central | Akcelerátory řešení Azure IoT |
| --- | ----------- | --------- |
| Primární využití                      | Ke zkrácení doby uvedení na trh pro jednoduchá řešení IoT, která nevyžadují rozsáhlá přizpůsobení služeb                                                    | Ke zrychlení vývoje vlastního řešení IoT, které vyžaduje maximální flexibilitu                                                                                                                             |
| Přístup k podkladovým službám PaaS | SaaS. Plně spravovaná řešení, podkladové služby nejsou zveřejněné.                                                                                            | Máte přístup k podkladovým službám Azure a můžete je spravovat nebo podle potřeby vyměnit.                                                                                                                    |
| Flexibilita                        | Střední. Můžete využít předdefinované uživatelské prostředí založené na prohlížeči a přizpůsobit model řešení a aspekty uživatelského rozhraní. Infrastrukturu nejde přizpůsobit, protože jednotlivé komponenty se nezveřejňují. | Vysoká. Kód pro mikroslužby je typu open source a můžete ho upravit libovolným způsobem podle svých potřeb. Kromě toho můžete přizpůsobit infrastrukturu nasazení.                                               |
| Úroveň dovedností                        | Nízká. K přizpůsobení řešení potřebujete znalosti modelování. Znalosti kódování nejsou potřeba.                                                                          | Středně vysoká. K přizpůsobení back-endu řešení potřebujete znalosti Javy nebo .NET. K přizpůsobení vizualizace potřebujete znalosti JavaScriptu.                                                                       |
| Úvodní prostředí             | Šablony aplikací a šablony zařízení poskytují předdefinované modely. Je možné je nasadit v řádu minut.                                                                                                  | Předkonfigurovaná řešení implementují běžné scénáře IoT. Je možné je nasadit v řádu minut.                                                                                                                            |
| Ceny                            | Jednoduchá a předvídatelná struktura cena.                                                                                                                           | Služby můžete doladit tak, abyste měli náklady pod kontrolou.                                                                                                                                                            |

Rozhodnutí, který produkt využít k sestavení vašeho řešení IoT, určují hlavně:

* Vaše obchodní požadavky
* Typ řešení, které chcete sestavit
* Dovednosti vaší organizace pro sestavování a správu řešení z dlouhodobého hlediska

## <a name="next-steps"></a>Další kroky

Na základě zvoleného produktu a přístupu jsou navrhujeme tyto další kroky:

* **Azure IoT Central**: [Azure IoT Central](overview-iot-central.md).
* **Akcelerátory řešení IoT:** [Co jsou akcelerátory řešení Azure IoT?](../iot-accelerators/iot-accelerators-what-are-solution-accelerators.md)
* **IoT Hub:** [Přehled služby Azure IoT Hub](https://docs.microsoft.com/azure/iot-hub/iot-hub-what-is-iot-hub).