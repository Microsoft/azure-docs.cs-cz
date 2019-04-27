---
title: Možnosti Microsoft Azure IoT | Dokumentace Microsoftu
description: Zvolte, jak implementovat řešení Azure IoT pomocí Azure IoT Central, akcelerátory řešení IoT nebo službu IoT Hub.
author: dominicbetts
ms.author: dobett
ms.date: 11/30/2017
ms.topic: overview
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: timlt
ms.openlocfilehash: 571c703609bac2e4414baf19edc39cf5e9f8820a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "60746152"
---
# <a name="compare-azure-iot-central-and-azure-iot-options"></a>Porovnání Azure IoT Central a Azure IoT Suite

Microsoft Azure IoT Central a Azure IoT nabízí několik možností pro vytváření řešení IoT. Tyto možnosti jsou vhodné pro různé sady zákaznických požadavků:

* [Azure IoT Central](overview-iot-central.md) je software jako služba (SaaS) řešení, které využívá přístup založený na základě modelu můžete vytvářet řešení IoT na podnikové úrovni, aniž by to vyžadovalo zkušenosti při vývoji řešení cloudové.

* [Akcelerátory řešení Azure IoT](https://docs.microsoft.com/azure/iot-accelerators/) na podnikové úrovni kolekce [akcelerátory řešení](../iot-accelerators/iot-accelerators-what-are-solution-accelerators.md) postavená na platformě Azure jako služba (PaaS), který vám pomůže zrychlit vývoj vlastních řešení IoT.

## <a name="azure-iot-hub"></a>Azure IoT Hub

Služba Azure IoT Hub představuje základní Azure PaaS a využívají ji Azure IoT Central i akcelerátory řešení Azure IoT. IoT Hub podporuje spolehlivou a zabezpečenou obousměrnou komunikaci mezi miliony zařízení IoT a cloudovým řešením. IoT Hub pomáhá plnit IoT náročné úkoly implementace problémy jako:

* Správa a připojení zařízení velkého rozsahu
* Ingestování telemetrie velkého rozsahu
* Příkazy a ovládání zařízení
* Vynucení zabezpečení zařízení

## <a name="compare-azure-iot-central-and-azure-iot-solution-accelerators"></a>Porovnání akcelerátorů řešení Azure IoT a Azure IoT Central

Volba produktu Azure IoT je důležitou součástí plánování řešení IoT. IoT Hub je individuální služba Azure, která sama o sobě neposkytuje kompletní řešení IoT. IoT Hub můžete použít jako výchozí bod pro jakékoli řešení IoT. Nepotřebujete také pomocí akcelerátory řešení Azure IoT nebo Azure IoT Central využívají IoT Hub. Akcelerátory řešení IoT i Azure IoT Central využívají službu IoT Hub společně s dalšími službami Azure.

Následující tabulka shrnuje hlavní rozdíly mezi akcelerátory řešení IoT a Azure IoT Central a pomůže vám při výběru vhodné varianty pro vaše požadavky:

|     | Azure IoT Central | Akcelerátory řešení Azure IoT |
| --- | ----------- | --------- |
| Primární využití                      | Ke zkrácení doby uvedení na trh pro jednoduchá řešení IoT, která nevyžadují rozsáhlá přizpůsobení služeb                                                    | Ke zrychlení vývoje vlastního řešení IoT, které vyžaduje maximální flexibilitu                                                                                                                             |
| Přístup k podkladovým službám PaaS | SaaS. Protože je to plně spravovaná řešení, podkladové služby nejsou zveřejněné.                                                                                            | Máte přístup k podkladovým službám Azure a můžete je spravovat nebo podle potřeby vyměnit.                                                                                                                    |
| Flexibilita                        | Střední. Můžete využít předdefinované uživatelské prostředí založené na prohlížeči a přizpůsobit model řešení a aspekty uživatelského rozhraní. Infrastrukturu není přizpůsobitelné, protože jednotlivé komponenty se nezveřejňují. | Vysoká. Kód pro mikroslužby je open source a můžete ho upravit libovolným způsobem podle svých potřeb. Kromě toho můžete přizpůsobit infrastrukturu nasazení.                                               |
| Úroveň dovedností                        | Nízká. K přizpůsobení řešení potřebujete znalosti modelování. Znalosti kódování nejsou potřeba.                                                                          | Středně vysoká. Je třeba Java nebo .NET dovednosti k přizpůsobení back-endu řešení. K přizpůsobení vizualizace potřebujete znalosti JavaScriptu.                                                                       |
| Získávání úvodní prostředí             | Šablony aplikací a šablony zařízení poskytují předem připravené modely. Je možné je nasadit v řádu minut.                                                                                                  | Předkonfigurovaná řešení implementují běžné scénáře IoT. Je možné je nasadit v řádu minut.                                                                                                                            |
| Ceny                            | Jednoduchá a předvídatelná struktura cena.                                                                                                                           | Služby můžete doladit tak, abyste měli náklady pod kontrolou.                                                                                                                                                            |

Který produkt využít k sestavení vašeho řešení IoT, závisí na:

* Vaše obchodní požadavky
* Typ řešení, které mají být sestaveny.
* Dovednosti vaší organizace pro sestavování a správu řešení z dlouhodobého hlediska

## <a name="next-steps"></a>Další postup

Na základě zvoleného produktu a přístupu jsou navrhujeme tyto další kroky:

* **Azure IoT Central**: [Co je Azure IoT Central?](overview-iot-central.md)
* **Akcelerátory řešení IoT**: [Co jsou akcelerátory řešení Azure IoT?](../iot-accelerators/iot-accelerators-what-are-solution-accelerators.md)
* **IoT Hub**: [Co je Azure IoT Hub?](https://docs.microsoft.com/azure/iot-hub/iot-hub-what-is-iot-hub)