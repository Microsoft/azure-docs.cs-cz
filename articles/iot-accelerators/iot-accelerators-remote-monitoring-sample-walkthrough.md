---
title: Vzdálené monitorování přehled akcelerátorů řešení – Azure | Dokumentace Microsoftu
description: Přehled akcelerátorů řešení vzdáleného monitorování.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 11/10/2017
ms.author: dobett
ms.openlocfilehash: dfe584532efeab1dbc0d2928b7afb0a6695a21ee
ms.sourcegitcommit: bf522c6af890984e8b7bd7d633208cb88f62a841
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/20/2018
ms.locfileid: "39184941"
---
# <a name="remote-monitoring-solution-accelerator-overview"></a>Přehled akcelerátorů řešení vzdáleného monitorování

Vzdálené monitorování [akcelerátor řešení](../iot-accelerators/about-iot-accelerators.md) implementuje řešení monitorování v začátku do konce pro více počítačů ve vzdálených umístěních. Řešení kombinuje klíčové služby Azure a poskytuje obecnou implementaci obchodního scénáře. Řešení můžete použít jako výchozí bod pro vlastní implementaci a [přizpůsobit](../iot-accelerators/iot-accelerators-remote-monitoring-customize.md) ho podle konkrétních podnikových požadavků.

Tento článek vás provede některými z klíčových prvků řešení vzdáleného monitorování tak, aby vám pomohl pochopit, jak to funguje. Díky tomu budete moct:

* Odstraňovat potíže v řešení.
* Naplánujte, jak řešení přizpůsobit podle konkrétních požadavků.
* Navrhněte vlastní řešení IoT, které používá služby Azure.

## <a name="logical-architecture"></a>Logická architektura

Následující diagram popisuje logické součásti jako překryvný obrázek na akcelerátor řešení vzdálené monitorování [architektury IoT](../iot-fundamentals/iot-introduction.md):

![Logická architektura](./media/iot-accelerators-remote-monitoring-sample-walkthrough/remote-monitoring-architecture.png)

## <a name="why-microservices"></a>Proč mikroslužby?

Protože společnost Microsoft vydala první akcelerátory řešení se vyvinula cloudové architektury. [Mikroslužby](https://azure.microsoft.com/blog/microservices-an-application-revolution-powered-by-the-cloud/) vznikly prověřené postupem zajistit škálovatelnost a flexibilitu bez obětování rychlosti vývoje. Různými službami společnosti Microsoft pomocí tohoto z možných architektonických interně skvělé spolehlivosti a škálovatelnosti výsledky. Akcelerátory řešení aktualizované vložte tyto poznatky o do praxe, mohou také těžit z nich.

> [!TIP]
> Další informace o architekturách mikroslužeb najdete v tématech věnovaných [architektuře aplikací .NET](https://www.microsoft.com/net/learn/architecture) a [mikroslužbám jako revoluci v aplikacích, kterou umožnil cloud](https://azure.microsoft.com/blog/microservices-an-application-revolution-powered-by-the-cloud/).

## <a name="device-connectivity"></a>Připojení zařízení

Toto řešení zahrnuje následující součásti v části připojení k zařízení logickou architekturu:

### <a name="simulated-devices"></a>Simulovaná zařízení

Toto řešení zahrnuje mikroslužeb, která umožňují Správa fondu s Simulovaná zařízení pro testování tohoto toku začátku do konce v řešení. Simulovaná zařízení:

* Generování telemetrie zařízení cloud.
* Reakce na volání metody typu cloud zařízení ze služby IoT Hub.

Mikroslužeb poskytuje koncový bod RESTful vám vytvoření, spuštění a zastavení simulace. Každou simulaci se skládá ze sady různých typů, které odesílání telemetrických dat a reakce na volání metody virtuální zařízení.

Zřízení simulovaného zařízení na řídicím panelu portálu řešení.

### <a name="physical-devices"></a>Fyzické zařízení

Připojení fyzických zařízení k řešení. Můžete implementovat chování simulovaných zařízení pomocí sady SDK pro zařízení Azure IoT.

Můžete zřídit fyzické zařízení na řídicím panelu portálu řešení.

### <a name="iot-hub-and-the-iot-manager-microservice"></a>IoT Hub a správce mikroslužeb IoT

[Služby IoT hub](../iot-hub/index.yml) přijímá data odesílaná ze zařízení do cloudu a zpřístupňuje `telemetry-agent` mikroslužeb.

Služba IoT Hub v řešení také:

* Udržuje registr identit, které jsou uloženy identifikátory a ověřovací klíče všech zařízení, povolit připojení k portálu. Prostřednictvím registru identit můžete zařízení povolit nebo zakázat.
* Jménem portálu řešení vyvolává metody v zařízeních.
* Udržuje dvojčata zařízení pro všechna registrovaná zařízení. Dvojče zařízení ukládá hodnoty vlastností ohlášené zařízením. Dvojče zařízení také ukládá požadované vlastnosti nastavené na portálu řešení, aby si je zařízení při dalším připojení mohlo načíst.
* Plánuje úlohy, které nastaví vlastnosti pro více zařízení, nebo vyvolává metody ve více zařízeních.

Toto řešení zahrnuje `iot-manager` mikroslužeb pro zpracování interakce s centrem IoT, jako:

* Vytváření a správa zařízení IoT.
* Správa dvojčata zařízení.
* Volání metod v zařízení.
* Správa přihlašovacích údajů IoT.

Tato služba také spouští služby IoT Hub dotazy se načíst zařízení, které patří do uživatelem definované skupiny.

Mikroslužeb poskytuje koncový bod RESTful ke správě zařízení a dvojčata zařízení, vyvolání metod a spouštění dotazů služby IoT Hub.

## <a name="data-processing-and-analytics"></a>Zpracování a analýza dat

Toto řešení zahrnuje následující součásti v zpracování dat a analýz součástí logickou architekturu:

### <a name="device-telemetry"></a>Telemetrie zařízení

Toto řešení zahrnuje dvě mikroslužeb pro zpracování telemetrie zařízení.

[Agenta telemetrie](https://github.com/Azure/telemetry-agent-dotnet) mikroslužeb:

* Telemetrická data se uloží ve službě Azure Cosmos DB.
* Analyzuje datový proud telemetrie ze zařízení.
* Generuje alarmy podle definovaných pravidel.

Alarmy se ukládají ve službě Azure Cosmos DB.

[Agenta telemetrie](https://github.com/Azure/telemetry-agent-dotnet) mikroslužeb umožňuje číst telemetrická data odesílaná ze zařízení na portálu řešení. Portál řešení také využívá tuto službu do:

* Definovat pravidla monitorování, jako je například prahové hodnoty, které aktivují upozornění
* Načte seznam posledních alarmy.

Koncový bod RESTful, poskytuje tato mikroslužeb použijte ke správě telemetrie, pravidel a alarmů.

### <a name="storage"></a>Úložiště

[Adaptér úložiště](https://github.com/Azure/pcs-storage-adapter-dotnet) mikroslužba je adaptér před hlavní úložiště služba používaná pro akcelerátor řešení. Poskytuje jednoduché kolekce a úložiště klíč / hodnota.

Standardní nasazení akcelerátoru řešení používá jako jeho hlavní úložiště služby Azure Cosmos DB.

Databáze Azure Cosmos DB ukládá data v akcelerátoru řešení. **Adaptér úložiště** mikroslužeb funguje jako adaptér pro mikroslužby v řešení pro přístup ke službám úložiště.

## <a name="presentation"></a>Prezentace

Toto řešení zahrnuje následující součásti v prezentaci součástí logickou architekturu:

[Webovým uživatelským rozhraním je aplikace React Javascript](https://github.com/Azure/pcs-remote-monitoring-webui). Aplikace:

* Pouze pomocí Javascript React a spustí zcela v prohlížeči.
* Je stylem CSS.
* Komunikuje s veřejný internetový mikroslužeb prostřednictvím volání AJAX.

Uživatelské rozhraní zobrazí všechny funkce akcelerátor řešení a komunikuje s dalšími službami, jako:

* [Ověřování](https://github.com/Azure/pcs-auth-dotnet) mikroslužeb pro ochranu dat uživatele.
* [Iothub-manager](https://github.com/Azure/iothub-manager-dotnet) mikroslužeb k zobrazení a správa zařízení IoT.

[Uživatelského rozhraní config](https://github.com/Azure/pcs-config-dotnet) mikroslužeb umožňuje ukládat a načítat nastavení konfigurace uživatelského rozhraní.

## <a name="next-steps"></a>Další postup

Pokud chcete prozkoumat dokumentaci zdrojového kódu a pro vývojáře, začněte s jedním dva hlavní úložiště GitHub:

* [Akcelerátor řešení vzdálené monitorování s Azure IoT (.NET)](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/).
* [Akcelerátor řešení vzdálené monitorování s Azure IoT (Java)](https://github.com/Azure/azure-iot-pcs-remote-monitoring-java).

Diagramy architektury podrobné řešení:
* [Akcelerátor řešení vzdálené monitorování architektury](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Architecture).

Další koncepční informace o akcelerátoru řešení vzdáleného monitorování najdete v tématu [přizpůsobení akcelerátoru řešení](../iot-accelerators/iot-accelerators-remote-monitoring-customize.md).
