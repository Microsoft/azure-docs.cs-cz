---
title: Možnosti Microsoft Azure IoT | Dokumentace Microsoftu
description: Vyberte, jak implementovat řešení Azure IoT pomocí IoT Central Azure, akcelerátorů řešení IoT nebo IoT Hub.
author: dominicbetts
ms.author: dobett
ms.date: 06/09/2019
ms.topic: overview
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: timlt
ms.openlocfilehash: 2ec6e1d00d331a7bb8dd7182dd4b0c91dd3d25a7
ms.sourcegitcommit: b1a8f3ab79c605684336c6e9a45ef2334200844b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/13/2019
ms.locfileid: "74049094"
---
# <a name="compare-azure-iot-central-and-azure-iot-options"></a>Porovnání Azure IoT Central a Azure IoT Suite

Microsoft Azure IoT Central a Azure IoT nabízí několik možností pro sestavování řešení IoT. Tyto možnosti jsou vhodné pro různé sady zákaznických požadavků:

* [Azure IoT Central](overview-iot-central.md) je aplikační platforma IoT, která využívá přístup založený na modelu, který vám umožňuje vytvářet řešení IoT na podnikové úrovni, aniž by bylo nutné mít zkušenosti s vývojem cloudových řešení.

* [Akcelerátory řešení Azure IoT](https://docs.microsoft.com/azure/iot-accelerators/) jsou kolekce [akcelerátorů řešení](../../iot-accelerators/iot-accelerators-what-are-solution-accelerators.md) založených na podnikové úrovni založené na platformě Azure jako služba (PaaS), které vám pomůžou zrychlit vývoj vlastních řešení IoT.

## <a name="azure-iot-hub"></a>Azure IoT Hub

Služba Azure IoT Hub představuje základní Azure PaaS a využívají ji Azure IoT Central i akcelerátory řešení Azure IoT. IoT Hub podporuje spolehlivou a zabezpečenou obousměrnou komunikaci mezi miliony zařízení IoT a cloudovým řešením. IoT Hub pomáhá splnit problémy implementace IoT, jako je:

* Připojení a Správa vysokého objemu zařízení
* Ingestování telemetrie s vysokým objemem
* Příkaz a ovládání zařízení
* Vynucení zabezpečení zařízení

## <a name="compare-azure-iot-central-and-azure-iot-solution-accelerators"></a>Porovnání akcelerátorů řešení Azure IoT a Azure IoT Central

Volba produktu Azure IoT je důležitou součástí plánování řešení IoT. IoT Hub je individuální služba Azure, která sama o sobě neposkytuje kompletní řešení IoT. Jako výchozí bod pro jakékoli řešení IoT můžete použít IoT Hub. Nemusíte také používat akcelerátory řešení Azure IoT nebo Azure IoT Central k použití IoT Hub. Akcelerátory řešení IoT i Azure IoT Central využívají službu IoT Hub společně s dalšími službami Azure.

Následující tabulka shrnuje hlavní rozdíly mezi akcelerátory řešení IoT a Azure IoT Central a pomůže vám při výběru vhodné varianty pro vaše požadavky:

|     | Azure IoT Central | Akcelerátory řešení Azure IoT |
| --- | ----------- | --------- |
| Primární využití                      | Ke zkrácení doby uvedení na trh pro jednoduchá řešení IoT, která nevyžadují rozsáhlá přizpůsobení služeb                                                    | Ke zrychlení vývoje vlastního řešení IoT, které vyžaduje maximální flexibilitu                                                                                                                             |
| Přístup k podkladovým službám PaaS | Vzhledem k tomu, že se jedná o plně spravované řešení, základní služby se nezveřejňují.                                                                                            | Máte přístup k podkladovým službám Azure a můžete je spravovat nebo podle potřeby vyměnit.                                                                                                                    |
| Flexibilita                        | Střední. Můžete využít předdefinované uživatelské prostředí založené na prohlížeči a přizpůsobit model řešení a aspekty uživatelského rozhraní. Infrastruktura není přizpůsobitelná, protože různé součásti nejsou vystavené. | Vysoká. Kód pro mikroslužby je open source a můžete ho upravit jakýmkoli způsobem, který se vám bude zobrazovat. Kromě toho můžete přizpůsobit infrastrukturu nasazení.                                               |
| Úroveň dovedností                        | Nízká. K přizpůsobení řešení potřebujete znalosti modelování. Znalosti kódování nejsou potřeba.                                                                          | Středně vysoká. K přizpůsobení back-endu řešení potřebujete dovednosti Java nebo .NET. K přizpůsobení vizualizace potřebujete znalosti JavaScriptu.                                                                       |
| Úvodní prostředí             | Šablony aplikací a šablony zařízení poskytují předem připravené modely. Je možné je nasadit v řádu minut.                                                                                                  | Předkonfigurovaná řešení implementují běžné scénáře IoT. Je možné je nasadit v řádu minut.                                                                                                                            |
| Ceny                            | Jednoduchá a předvídatelná struktura cena.                                                                                                                           | Služby můžete doladit tak, abyste měli náklady pod kontrolou.                                                                                                                                                            |

Který produkt, který se má použít k sestavení řešení IoT, závisí na:

* Vaše obchodní požadavky
* Typ řešení, které chcete sestavit.
* Dovednosti vaší organizace pro sestavování a správu řešení z dlouhodobého hlediska

## <a name="next-steps"></a>Další kroky

Na základě zvoleného produktu a přístupu jsou navrhujeme tyto další kroky:

* **Azure IoT Central**: [co je Azure IoT Central?](overview-iot-central.md)
* **Akcelerátory řešení IoT**: [co jsou akcelerátory řešení Azure IoT?](../../iot-accelerators/iot-accelerators-what-are-solution-accelerators.md)
* **IoT Hub**: [co je Azure IoT Hub?](https://docs.microsoft.com/azure/iot-hub/iot-hub-what-is-iot-hub)