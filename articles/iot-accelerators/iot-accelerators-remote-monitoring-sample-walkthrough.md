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
ms.openlocfilehash: f059c57396610a10f9e35a6dad8408c6be1d89cb
ms.sourcegitcommit: 616e63d6258f036a2863acd96b73770e35ff54f8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/14/2018
ms.locfileid: "45604298"
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

### <a name="physical-devices"></a>Fyzická zařízení

Připojení fyzických zařízení k řešení. Můžete implementovat chování simulovaných zařízení pomocí sady SDK pro zařízení Azure IoT.

Můžete zřídit fyzické zařízení na řídicím panelu portálu řešení.

### <a name="device-simulation-microservice"></a>Mikroslužby simulace zařízení

Toto řešení zahrnuje [mikroslužeb simulace zařízení](https://github.com/Azure/remote-monitoring-services-dotnet/tree/master/device-simulation) , která umožňuje Správa fondu s Simulovaná zařízení z portálu řešení pro testování tohoto toku začátku do konce v řešení. Simulovaná zařízení:

* Generování telemetrie zařízení cloud.
* Reakce na volání metody typu cloud zařízení ze služby IoT Hub.

Mikroslužeb poskytuje koncový bod RESTful vám vytvoření, spuštění a zastavení simulace. Každou simulaci se skládá ze sady různých typů, které odesílání telemetrických dat a reakce na volání metody virtuální zařízení.

Zřízení simulovaného zařízení na řídicím panelu portálu řešení.

### <a name="iot-hub"></a>IoT Hub

[Služby IoT hub](../iot-hub/index.yml) ingestuje telemetrická data odesílaná z fyzických a simulovaných zařízení do cloudu. IoT hub zpřístupní telemetrická data do služeb back-endu řešení IoT pro zpracování.

Služba IoT Hub v řešení také:

* Udržuje registr identit, které jsou uloženy identifikátory a ověřovací klíče všech zařízení, povolit připojení k portálu.
* Vyvolá metody v zařízení jménem akcelerátor řešení.
* Udržuje dvojčata zařízení pro všechna registrovaná zařízení. Dvojče zařízení ukládá hodnoty vlastností ohlášené zařízením. Dvojče zařízení také ukládá požadované vlastnosti nastavené na portálu řešení, aby si je zařízení při dalším připojení mohlo načíst.
* Plánuje úlohy, které nastaví vlastnosti pro více zařízení, nebo vyvolává metody ve více zařízeních.

## <a name="data-processing-and-analytics"></a>Zpracování a analýza dat

Toto řešení zahrnuje následující součásti v zpracování dat a analýz součástí logickou architekturu:

### <a name="iot-hub-manager-microservice"></a>Mikroslužby Správce služby IoT Hub

Toto řešení zahrnuje [mikroslužeb Správce služby IoT Hub](https://github.com/Azure/remote-monitoring-services-dotnet/tree/master/iothub-manager) zpracování interakce s centrem IoT, jako:

* Vytváření a správa zařízení IoT.
* Správa dvojčata zařízení.
* Volání metod v zařízení.
* Správa přihlašovacích údajů IoT.

Tato služba také spouští služby IoT Hub dotazy se načíst zařízení, které patří do uživatelem definované skupiny.

Mikroslužeb poskytuje koncový bod RESTful ke správě zařízení a dvojčata zařízení, vyvolání metod a spouštění dotazů služby IoT Hub.

### <a name="device-telemetry-microservice"></a>Mikroslužby telemetrie zařízení

[Mikroslužeb telemetrie zařízení](https://github.com/Azure/remote-monitoring-services-dotnet/tree/master/device-telemetry) přístup pro čtení k telemetrii zařízení ukládají v Time Series Insights poskytuje koncový bod RESTful. Koncový bod RESTful, taky umožňuje operací CRUD u pravidla a přístup pro čtení a zápisu pro definice upozornění ze služby storage.

### <a name="storage-adapter-microservice"></a>Mikroslužby adaptér úložiště

[Mikroslužeb adaptér úložiště](https://github.com/Azure/remote-monitoring-services-dotnet/tree/master/storage-adapter) spravuje páry klíč hodnota, poskytuje abstrakci úložiště sémantikou služby a nabízí ten samý jednoduché rozhraní pro ukládání dat libovolný formát pomocí služby Azure Cosmos DB.

Hodnoty jsou uspořádány do kolekce. Můžete pracovat na jednotlivé hodnoty nebo načítání změn celé kolekce. Komplexní datové struktury jsou serializovat klienty a spravovat jako datovou část prostého textu.

Tato služba poskytuje koncový bod RESTful pro operace CRUD s páry klíč hodnota. Hodnoty

### <a name="azure-cosmos-db"></a>Azure Cosmos DB

Nasazení akcelerátoru řešení používat [služby Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/) k uložení pravidel, alarmy, nastavení konfigurace a všechny ostatní studeného úložiště.

### <a name="azure-stream-analytics-manager-microservice"></a>Azure Stream Analytics správce mikroslužeb

[Mikroslužeb Azure Stream Analytics správce](https://github.com/Azure/remote-monitoring-services-dotnet/tree/master/asa-manager) spravuje úlohy Azure Stream Analytics (ASA), včetně nastavení jejich konfigurace, spouštění a zastavování je a jejich stavu monitorování.

Úlohy Azure Stream Analytics podporuje dvě referenční datové sady. Jeden datový soubor definuje pravidla a jeden definuje skupiny zařízení. Referenční data pravidla se generuje z informace spravuje mikroslužby telemetrie zařízení. Správce mikroslužeb Azure Stream Analytics transformuje pravidla telemetrie na logiku zpracování datového proudu.

Referenční data pro skupiny zařízení slouží k identifikaci která skupina platná pravidla pro příchozí zprávy telemetrická data. Skupiny zařízení spravuje mikroslužby konfigurace a použití dotazů na dvojčata zařízení Azure IoT Hub.

Úlohy ASA dodat telemetrie z připojených zařízení Time Series Insights pro ukládání a analýzu.

### <a name="azure-stream-analytics"></a>Azure Stream Analytics

[Azure Stream Analytics](https://docs.microsoft.com/azure/stream-analytics/) je modul pro zpracování událostí, který umožňuje zkoumat velké objemy dat streamované ze zařízení.

### <a name="azure-time-series-insights"></a>Azure Time Series Insights

[Azure Time Series Insights](https://docs.microsoft.com/azure/time-series-insights/) úložišť telemetrie ze zařízení připojených k akcelerátoru řešení. Umožňuje také vizualizaci a dotazování na ně telemetrie zařízení v řešení webového uživatelského rozhraní.

> [!NOTE]
> Time Series Insights není aktuálně k dispozici v cloud Azure China. Nové vzdálené monitorování akcelerátoru nasazení řešení v cloudu Azure China pomocí služby Cosmos DB pro všechna úložiště.

### <a name="configuration-microservice"></a>Konfigurace mikroslužeb

[Konfigurace mikroslužeb](https://github.com/Azure/remote-monitoring-services-dotnet/tree/master/config) poskytuje koncový bod RESTful pro operací CRUD u skupiny zařízení, nastavení řešení a nastavení uživatele v akcelerátoru řešení. Funguje to mikroslužby adaptér úložiště se zachovat data konfigurace.

### <a name="authentication-and-authorization-microservice"></a>Ověřování a autorizace mikroslužeb

[Ověřování a autorizace mikroslužeb](https://github.com/Azure/remote-monitoring-services-dotnet/tree/master/auth) slouží ke správě uživatelů, oprávnění pro přístup k akcelerátoru řešení. Správa uživatelů lze provést pomocí všech poskytovatelů služeb identita, která podporuje [OpenId Connect](http://openid.net/connect/).

### <a name="azure-active-directory"></a>Azure Active Directory

Nasazení akcelerátoru řešení používat [Azure Active Directory](https://docs.microsoft.com/azure/active-directory/) jako poskytovatele OpenID Connect. Azure Active Directory uchovává informace o uživateli a zajišťuje, že se certifikáty pro ověření tokenů JWT token podpisů.

## <a name="presentation"></a>Prezentace

Toto řešení zahrnuje následující součásti v prezentaci součástí logickou architekturu:

[Webovým uživatelským rozhraním je aplikace React Javascript](https://github.com/Azure/pcs-remote-monitoring-webui). Aplikace:

* Pouze pomocí Javascript React a spustí zcela v prohlížeči.
* Je stylem CSS.
* Komunikuje s veřejný internetový mikroslužeb prostřednictvím volání AJAX.

Uživatelské rozhraní zobrazí všechny funkce akcelerátoru řešení a komunikuje s další mikroslužeb, jako:

* Ověřování a autorizace mikroslužeb pro ochranu dat uživatele.
* Mikroslužby Správce služby IoT Hub k zobrazení a správa zařízení IoT.

Uživatelské rozhraní se integruje se v Průzkumníku služby Azure Time Series Insights umožňuje dotazování a analýze telemetrických dat zařízení.

Konfigurace mikroslužeb umožňuje ukládat a načítat nastavení konfigurace uživatelského rozhraní.

## <a name="next-steps"></a>Další postup

Pokud chcete prozkoumat dokumentaci zdrojového kódu a pro vývojáře, začněte s jedním ze dvou úložišť GitHub:

* [Akcelerátor řešení vzdálené monitorování s Azure IoT (.NET)](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet).
* [Akcelerátor řešení vzdálené monitorování s Azure IoT (Java)](https://github.com/Azure/azure-iot-pcs-remote-monitoring-java).

Diagramy architektury podrobné řešení:
* [Akcelerátor řešení vzdálené monitorování architektury](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Architecture).

Další koncepční informace o akcelerátoru řešení vzdáleného monitorování najdete v tématu [přizpůsobení akcelerátoru řešení](../iot-accelerators/iot-accelerators-remote-monitoring-customize.md).
