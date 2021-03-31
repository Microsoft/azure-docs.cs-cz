---
title: Seznámení s akcelerátory řešení IoT – Azure | Microsoft Docs
description: Seznamte se s akcelerátory řešení Azure IoT. Akcelerátory řešení IoT jsou kompletní a ucelená řešení IoT připravená k nasazení.
author: dominicbetts
ms.author: dobett
ms.date: 03/09/2019
ms.topic: overview
ms.custom: mvc
ms.service: iot-accelerators
services: iot-accelerators
manager: timlt
ms.openlocfilehash: 5012383e64a85ee025273f5339b828f5338e1d4f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "97629064"
---
# <a name="what-are-azure-iot-solution-accelerators"></a>Co jsou akcelerátory řešení Azure IoT?

Cloudové řešení IoT obvykle používá vlastní kód a cloudové služby ke správě připojení zařízení, zpracování dat a analýz a prezentaci.

Akcelerátory řešení IoT jsou kompletní a ucelená řešení IoT připravená k nasazení, která implementují běžné scénáře IoT. Scénáře zahrnují simulaci propojené továrny a zařízení. Když nasadíte akcelerátor řešení, součástí nasazení jsou všechny požadované cloudové služby i veškerý požadovaný kód aplikace.

Akcelerátory řešení představují výchozí body pro vaše vlastní řešení IoT. Zdrojový kód všech akcelerátorů řešení je open source a dostupný na GitHubu. Akcelerátory řešení si můžete stáhnout a přizpůsobit podle svých potřeb.

Akcelerátory řešení můžete využít také jako učební nástroje před tím, než vytvoříte vlastní řešení IoT od nuly. Akcelerátory řešení implementují osvědčené postupy pro cloudová řešení IoT, které byste měli dodržovat.

Kód aplikace v každém akcelerátoru řešení obsahuje webovou aplikaci, ve které můžete akcelerátor řešení spravovat.

> [!NOTE]
> Řešení vzdáleného monitorování a prediktivní údržby byla odebrána z webu [akcelerátorů řešení Azure IoT](https://www.azureiotsolutions.com/Accelerators) . Další informace najdete v tématu [co jsou akcelerátory řešení Azure IoT? (předchozí verze)](/previous-versions/azure/iot-accelerators/about-iot-accelerators).

## <a name="supported-iot-scenarios"></a>Podporované scénáře IoT

V současné době máte k dispozici dva akcelerátory řešení, které můžete nasadit:

### <a name="connected-factory"></a>Propojená továrna

Pomocí [akcelerátoru řešení propojené továrny](iot-accelerators-connected-factory-features.md) můžete shromažďovat telemetrii od průmyslových prostředků přes rozhraní [OPC Unified Architecture](https://opcfoundation.org/about/opc-technologies/opc-ua/) a řídit je. Mezi průmyslové prostředky můžou patřit montážní a testovací stanice na výrobní lince továrny.

Pomocí řídicího panelu propojené továrny můžete monitorovat a spravovat svá průmyslová zařízení:

:::image type="content" source="./media/about-iot-accelerators/cf-dashboard-inline.png" alt-text="Snímek obrazovky zobrazující řídicí panel řešení propojené továrny." lightbox="./media/about-iot-accelerators/cf-dashboard-expanded.png":::

### <a name="device-simulation"></a>Simulace zařízení

Pomocí [akcelerátoru řešení pro simulaci zařízení](iot-accelerators-device-simulation-overview.md) můžete spouštět simulovaná zařízení, která generují realistickou telemetrii. Pomocí tohoto akcelerátoru řešení můžete testovat chování ostatních akcelerátorů řešení nebo testovat vlastní řešení IoT.

Pomocí webové aplikace simulace zařízení můžete konfigurovat a spouštět simulace:

:::image type="content" source="./media/about-iot-accelerators/ds-dashboard-inline.png" alt-text="Snímek obrazovky zobrazující řídicí panel řešení pro simulaci zařízení" lightbox="./media/about-iot-accelerators/ds-dashboard-expanded.png":::

## <a name="design-principles"></a>Principy návrhu

Všechny akcelerátory řešení se řídí stejnými principy návrhu a mají stejné cíle. Jsou navržené tak, aby byly:

* **Škálovatelné** – umožňují vám připojit a spravovat miliony připojených zařízení.
* **Rozšiřitelné** – umožňují vám přizpůsobit je tak, aby splňovaly vaše požadavky.
* **Srozumitelné** – umožňují vám pochopit, jak fungují a jak se implementují.
* **Modulární** – umožňují vám vyměňovat služby za alternativy.
* **Bezpečné** – kombinují zabezpečení Azure s integrovanými možnostmi připojení a funkcemi zabezpečení zařízení.

## <a name="architectures-and-languages"></a>Architektury a jazyky

Původní akcelerátory řešení byly napsané v .NET a využívaly architekturu MVC (model-view-controller). Společnost Microsoft aktualizuje akcelerátory řešení, aby využívaly novou architekturu mikroslužeb. Následující tabulka ukazuje aktuální stav akcelerátorů řešení s odkazy na úložiště GitHub:

| Akcelerátor řešení   | Architektura  | Jazyky     |
| ---------------------- | ------------- | ------------- |
| Propojená továrna      | MVC           | [.NET](https://github.com/Azure/azure-iot-connected-factory)          |
| Simulace zařízení      | Mikroslužby | [.NET](https://github.com/Azure/device-simulation-dotnet)          |

Další informace o architektuře mikroslužeb najdete v tématu [Úvod do referenční architektury Azure IoT](/azure/architecture/reference-architectures/iot/).

## <a name="deployment-options"></a>Možnosti nasazení

Akcelerátory řešení můžete nasadit na webu [Akcelerátory řešení Microsoft Azure IoT](https://www.azureiotsolutions.com/Accelerators#) nebo pomocí příkazového řádku.

Cena za spuštění akcelerátoru řešení je celková [cena za provozování základních služeb Azure](https://azure.microsoft.com/pricing). Podrobnosti o použitých službách Azure se zobrazí po výběru možností nasazení.

## <a name="next-steps"></a>Další kroky

Pokud si chcete vyzkoušet některý z akcelerátorů řešení IoT, podívejte se na rychlé zprovoznění [vyzkoušet řešení propojené továrny](quickstart-connected-factory-deploy.md).
