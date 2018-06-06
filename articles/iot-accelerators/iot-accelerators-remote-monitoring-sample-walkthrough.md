---
title: Architektura řešení vzdáleného monitorování - Azure | Microsoft Docs
description: Návod architektury vzdálené monitorování akcelerátoru řešení.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 11/10/2017
ms.author: dobett
ms.openlocfilehash: af7feb6c95a7de1d2211378c5eb71f09907221ff
ms.sourcegitcommit: 4f9fa86166b50e86cf089f31d85e16155b60559f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/04/2018
ms.locfileid: "34757429"
---
# <a name="remote-monitoring-solution-accelerator-architecture"></a>Vzdálené monitorování řešení akcelerátoru architektura

Vzdálené monitorování [řešení akcelerátoru](../iot-accelerators/iot-accelerators-what-are-solution-accelerators.md) implementuje řešení monitorování v začátku do konce pro víc počítačů ve vzdálených umístěních. Řešení kombinuje klíčové služby Azure a poskytuje obecnou implementaci obchodního scénáře. Řešení můžete použít jako výchozí bod pro vlastní implementaci a [přizpůsobit](../iot-accelerators/iot-accelerators-remote-monitoring-customize.md) ho podle konkrétních podnikových požadavků.

Tento článek vás provede procesem některé klíčové prvky řešení vzdáleného monitorování tak, aby vám umožní pochopit, jak funguje. Díky tomu budete moct:

* Odstraňovat potíže v řešení.
* Naplánujte, jak řešení přizpůsobit podle konkrétních požadavků.
* Navrhněte vlastní řešení IoT, které používá služby Azure.

## <a name="logical-architecture"></a>Logická architektura

Následující diagram popisuje logické součásti tohoto překrývajícím akcelerátoru řešení vzdáleného monitorování [architektuře IoT](../iot-accelerators/iot-accelerators-what-is-azure-iot.md):

![Logická architektura](./media/iot-accelerators-remote-monitoring-sample-walkthrough/remote-monitoring-architecture.png)

## <a name="why-microservices"></a>Proč mikroslužeb?

Architektura Cloud má vyvinuly vzhledem k tomu, že společnost Microsoft vydala první akcelerátorů řešení. [Mikroslužeb](https://azure.microsoft.com/blog/microservices-an-application-revolution-powered-by-the-cloud/) této služby jako osvědčené postupy pro zajištění škálování a flexibilitu, aniž by došlo ke ztrátě vývoj rychlostí. Tento vzor architektury několika služeb Microsoft interně použít s skvělé spolehlivosti a škálovatelnosti výsledky. Aktualizované řešení akcelerátorů uveďte tyto learnings do praxe, mohou také těžit z nich.

> [!TIP]
> Další informace o architekturách mikroslužeb najdete v tématech věnovaných [architektuře aplikací .NET](https://www.microsoft.com/net/learn/architecture) a [mikroslužbám jako revoluci v aplikacích, kterou umožnil cloud](https://azure.microsoft.com/blog/microservices-an-application-revolution-powered-by-the-cloud/).

## <a name="device-connectivity"></a>Připojení zařízení

Řešení obsahuje následující součásti v části připojení k zařízení Logická architektura:

### <a name="simulated-devices"></a>Simulovaná zařízení

Řešení obsahuje mikroslužbu, která umožňuje spravovat fond Simulovaná zařízení k testování začátku do konce toku v řešení. Simulovaná zařízení:

* Generování telemetrie zařízení cloud.
* Reagovat na volání metod cloud zařízení ze služby IoT Hub.

Mikroslužbu poskytuje RESTful koncový bod pro k vytvoření, spuštění a zastavení simulace. Každý simulace se skládá z sadu různých typů, které odesílat telemetrická data a reagovat na volání metod virtuální zařízení.

Simulovaná zařízení na řídicím panelu portálu řešení můžete zřídit.

### <a name="physical-devices"></a>Fyzické zařízení

Fyzické zařízení můžete připojit k řešení. Můžete implementovat chování Simulovaná zařízení pomocí sady SDK zařízení Azure IoT.

Můžete zřídit fyzické zařízení na řídicím panelu portálu řešení.

### <a name="iot-hub-and-the-iot-manager-microservice"></a>IoT Hub a mikroslužbu manager IoT

[Služby IoT hub](../iot-hub/index.yml) ingestuje dat odesílaných ze zařízení do cloudu a můžete ho `telemetry-agent` mikroslužby.

Služba IoT Hub v řešení také:

* Udržuje registru identit, která ukládá ID a ověřovací klíče všech zařízení lze připojit k portálu. Prostřednictvím registru identit můžete zařízení povolit nebo zakázat.
* Jménem portálu řešení vyvolává metody v zařízeních.
* Udržuje dvojčata zařízení pro všechna registrovaná zařízení. Dvojče zařízení ukládá hodnoty vlastností ohlášené zařízením. Dvojče zařízení také ukládá požadované vlastnosti nastavené na portálu řešení, aby si je zařízení při dalším připojení mohlo načíst.
* Plánuje úlohy, které nastaví vlastnosti pro více zařízení, nebo vyvolává metody ve více zařízeních.

Řešení obsahuje `iot-manager` mikroslužbu pro zpracování interakce s služby IoT hub, jako:

* Vytváření a správa zařízení IoT.
* Správa dvojčata zařízení.
* Volání metod na zařízení.
* Správa přihlašovacích údajů IoT.

Tato služba také spouští služby IoT Hub dotazy k načtení zařízení patřící do uživatelské skupiny.

Mikroslužbu poskytuje RESTful koncový bod pro správu zařízení a dvojčata zařízení, volat metody a spouštět dotazy IoT Hub.

## <a name="data-processing-and-analytics"></a>Zpracování a analýza dat

Řešení obsahuje následující součásti v zpracování dat a analýzy součástí Logická architektura:

### <a name="device-telemetry"></a>Zařízení telemetrie

Řešení obsahuje dva mikroslužeb pro zpracování telemetrie zařízení.

[Telemetrie agenta](https://github.com/Azure/telemetry-agent-dotnet) mikroslužbu:

* Telemetrie úložiště v Azure Cosmos DB.
* Analyzuje datový proud telemetrie ze zařízení.
* Generuje výstrahy podle definovaná pravidla.

Signály jsou uloženy v databázi Azure Cosmos.

[Telemetrie agenta](https://github.com/Azure/telemetry-agent-dotnet) mikroslužbu umožňuje portálu řešení číst telemetrická data odesílaná ze zařízení. Portál řešení používá také této služby:

* Definovat pravidla monitorování, například prahové hodnoty, které aktivují výstrahy
* Načtení seznamu posledních výstrahy.

RESTful koncový bod poskytl tento mikroslužbu použijte ke správě telemetrie, pravidla a nastavení upozornění.

### <a name="storage"></a>Úložiště

[Adaptéru úložiště](https://github.com/Azure/pcs-storage-adapter-dotnet) mikroslužbu je adaptér před hlavní úložiště službě, kterou používá akcelerátoru řešení. Poskytuje jednoduché kolekce a klíč hodnota úložiště.

Standardní nasazení akcelerátoru řešení používá Azure Cosmos DB jako jeho hlavní úložiště služby.

Databázi Azure Cosmos DB ukládá data do akcelerátoru řešení. **Adaptéru úložiště** mikroslužbu funguje jako adaptér pro ostatní mikroslužeb v řešení pro přístup ke službě úložiště.

## <a name="presentation"></a>Prezentace

Řešení obsahuje následující součásti v části prezentace Logická architektura:

[Webové uživatelské rozhraní je aplikace React Javascript](https://github.com/Azure/pcs-remote-monitoring-webui). Aplikace:

* Pouze používá reagovat, Javascript a spustí zcela v prohlížeči.
* Je navržen s šablon stylů CSS.
* Komunikuje s veřejné mikroslužeb přístupných prostřednictvím volání AJAX.

Uživatelské rozhraní uvede všechny funkce akcelerátoru řešení a komunikuje s dalšími službami, jako například:

* [Ověřování](https://github.com/Azure/pcs-auth-dotnet) mikroslužbu pro ochranu dat uživatele.
* [Iothub-manager](https://github.com/Azure/iothub-manager-dotnet) mikroslužbu seznamu a správa zařízení IoT.

[Uživatelského rozhraní config](https://github.com/Azure/pcs-config-dotnet) mikroslužbu umožňuje uživatelské rozhraní pro ukládání a načítání nastavení konfigurace.

## <a name="next-steps"></a>Další postup

Pokud chcete prozkoumat zdrojový kód a vývojáře dokumentaci, začněte s jedním dva hlavní úložišť GitHub:

* [Akcelerátor řešení pro vzdálené monitorování s Azure IoT (.NET)](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/).
* [Akcelerátor řešení pro vzdálené monitorování s Azure IoT (Java)](https://github.com/Azure/azure-iot-pcs-remote-monitoring-java).

Podrobné diagramy Architektura řešení:
* [Accelerator řešení pro vzdálené monitorování architekturu](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Architecture).

Další koncepční informace o akcelerátoru řešení vzdáleného monitorování najdete v tématu [přizpůsobit akcelerátoru řešení](../iot-accelerators/iot-accelerators-remote-monitoring-customize.md).
