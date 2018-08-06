---
title: Seznámení s akcelerátory řešení Azure IoT | Microsoft Docs
description: Seznamte se s akcelerátory řešení Azure IoT. Akcelerátory řešení IoT jsou kompletní a ucelená řešení IoT připravená k nasazení.
author: dominicbetts
ms.author: dobett
ms.date: 07/24/2018
ms.topic: overview
ms.custom: mvc
ms.service: iot-accelerators
services: iot-accelerators
manager: timlt
ms.openlocfilehash: 7020d8a1756702d8c2b1998eef5a3fc64809ca5e
ms.sourcegitcommit: cfff72e240193b5a802532de12651162c31778b6
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/27/2018
ms.locfileid: "39308108"
---
# <a name="what-are-azure-iot-solution-accelerators"></a>Co jsou akcelerátory řešení Azure IoT?

Cloudová řešení IoT obvykle ke správě připojení zařízení, zpracování a analýze dat a jejich prezentaci využívají vlastní kód a několik cloudových služeb.

Akcelerátory řešení IoT jsou kolekce kompletních a ucelených řešení IoT připravených k nasazení, která implementují běžné scénáře IoT, jako je vzdálené monitorování, propojená továrna, prediktivní údržba a simulace zařízení. Když nasadíte akcelerátor řešení, součástí nasazení jsou všechny požadované cloudové služby i veškerý požadovaný kód aplikace.

Akcelerátory řešení představují výchozí body pro vaše vlastní řešení IoT. Zdrojový kód všech akcelerátorů řešení je open source a dostupný na GitHubu. Akcelerátory řešení si můžete stáhnout a přizpůsobit podle svých potřeb.

Akcelerátory řešení můžete využít také jako učební nástroje před tím, než vytvoříte vlastní řešení IoT od nuly. Akcelerátory řešení implementují osvědčené postupy pro cloudová řešení IoT, které byste měli dodržovat.

Kód aplikace v každém akcelerátoru řešení obsahuje webovou aplikaci, ve které můžete akcelerátor řešení spravovat.

## <a name="supported-iot-scenarios"></a>Podporované scénáře IoT

V současné době jsou k dispozici čtyři akcelerátory řešení, které můžete nasadit:

### <a name="remote-monitoring"></a>Vzdálené monitorování

Tento akcelerátor řešení můžete použít ke shromažďování telemetrie z několika vzdálených zařízení a k jejich ovládání. Mezi příklady zařízení patří chladící systémy nainstalované u vašich zákazníků nebo ventily nainstalované na vzdálených čerpacích stanicích.

Pomocí řídicího panelu vzdáleného monitorování můžete zobrazit telemetrii z připojených zařízení, zřizovat nová zařízení nebo upgradovat firmware na připojených zařízeních:

[![Řídicí panel řešení vzdáleného monitorování](./media/about-iot-accelerators/rm-dashboard-inline.png)](./media/about-iot-accelerators/rm-dashboard-expanded.png#lightbox)

### <a name="connected-factory"></a>Propojená továrna

Tento akcelerátor řešení můžete použít ke shromažďování telemetrie z průmyslových prostředků s využitím rozhraní [OPC Unified Architecture](https://opcfoundation.org/about/opc-technologies/opc-ua/) a k jejich ovládání. Mezi průmyslové prostředky můžou patřit montážní a testovací stanice na výrobní lince továrny.

Pomocí řídicího panelu propojené továrny můžete monitorovat a spravovat svá průmyslová zařízení:

[![Řídicí panel řešení propojené továrny](./media/about-iot-accelerators/cf-dashboard-inline.png)](./media/about-iot-accelerators/cf-dashboard-expanded.png#lightbox)

### <a name="predictive-maintenance"></a>Prediktivní údržba

Tento akcelerátor řešení můžete použít k předpovědi očekávaného selhání vzdáleného zařízení, abyste mohli provést údržbu před tím, než k předpokládanému selhání dojde. Tento akcelerátor řešení k předvídání selhání na základě telemetrie zařízení využívá algoritmy strojového učení. Příklady zařízení můžou být letadlové motory nebo výtahy.

Pomocí řídicího panelu prediktivní údržby můžete zobrazit analýzu prediktivní údržby:

[![Řídicí panel řešení propojené továrny](./media/about-iot-accelerators/pm-dashboard-inline.png)](./media/about-iot-accelerators/pm-dashboard-expanded.png#lightbox)

### <a name="device-simulation"></a>Simulace zařízení

Tento akcelerátor řešení můžete použít ke spuštění několika simulovaných zařízení, která generují realistickou telemetrii. Pomocí tohoto akcelerátoru řešení můžete testovat chování ostatních akcelerátorů řešení nebo testovat vlastní řešení IoT.

Pomocí webové aplikace simulace zařízení můžete konfigurovat a spouštět simulace:

[![Řídicí panel řešení propojené továrny](./media/about-iot-accelerators/ds-dashboard-inline.png)](./media/about-iot-accelerators/ds-dashboard-expanded.png#lightbox)

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
| Vzdálené monitorování      | Mikroslužby | [Java](https://github.com/Azure/azure-iot-pcs-remote-monitoring-java) a [.NET](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet) |
| Prediktivní údržba | MVC           | [.NET](https://github.com/Azure/azure-iot-predictive-maintenance)          |
| Propojená továrna      | MVC           | [.NET](https://github.com/Azure/azure-iot-connected-factory)          |
| Simulace zařízení      | Mikroslužby | [.NET](https://github.com/Azure/device-simulation-dotnet)          |

Další informace o architekturách mikroslužeb najdete v tématech věnovaných [architektuře aplikací .NET](https://www.microsoft.com/net/learn/architecture) a [mikroslužbám jako revoluci v aplikacích, kterou umožnil cloud](https://azure.microsoft.com/blog/microservices-an-application-revolution-powered-by-the-cloud/).

## <a name="deployment-options"></a>Možnosti nasazení

Akcelerátory řešení můžete nasadit na webu [Akcelerátory řešení Microsoft Azure IoT](https://www.azureiotsolutions.com/Accelerators#) nebo pomocí příkazového řádku.

Akcelerátor řešení vzdáleného monitorování můžete nasadit v následujících konfiguracích:

* **Standard:** Nasazení rozšířené infrastruktury pro vývoj produkčního nasazení. Azure Container Service nasazuje mikroslužby do několika virtuálních počítačů Azure. Kubernetes orchestruje kontejnery Dockeru, které jsou hostiteli jednotlivých mikroslužeb.
* **Basic:** Verze s nižšími náklady pro ukázku nebo otestování nasazení. Všechny mikroslužby se nasazují do jednoho virtuálního počítače Azure.
* **Místní:** Nasazení do místního počítače pro účely vývoje a testování. S tímto přístupem se mikroslužby nasazují do místního kontejneru Dockeru a připojují se ke službám IoT Hub, Azure Cosmos DB a Azure Storage v cloudu.

Náklady na provoz akcelerátoru řešení představují celkovou hodnotu všech [nákladů na podpůrné služby Azure](https://azure.microsoft.com/pricing). Podrobnosti o použitých službách Azure se zobrazí po výběru možností nasazení.

## <a name="next-steps"></a>Další kroky

Pokud si chcete vyzkoušet některý z akcelerátorů řešení IoT, projděte si následující rychlé starty:

* [Vyzkoušení řešení vzdáleného monitorování](quickstart-remote-monitoring-deploy.md)
* [Vyzkoušení řešení propojené továrny](quickstart-connected-factory-deploy.md)
* [Vyzkoušení řešení prediktivní údržby](quickstart-predictive-maintenance-deploy.md)
* [Vyzkoušení řešení simulace zařízení](quickstart-device-simulation-deploy.md)
