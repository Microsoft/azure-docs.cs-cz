---
title: Přehled akcelerátoru řešení vzdáleného monitorování – Azure | Dokumenty společnosti Microsoft
description: Tento článek obsahuje přehled některých klíčových prvků řešení vzdáleného monitorování, které vám umožní pochopit, jak funguje.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 03/08/2019
ms.author: dobett
ms.openlocfilehash: f501eb55f72811063ddf1d8e02a0ce2137d598f3
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/02/2020
ms.locfileid: "80546315"
---
# <a name="remote-monitoring-solution-accelerator-overview"></a>Přehled akcelerátorů řešení pro vzdálené monitorování

[Akcelerátor řešení](../iot-accelerators/about-iot-accelerators.md) vzdáleného monitorování implementuje komplexní řešení monitorování pro více počítačů ve vzdálených umístěních. Řešení kombinuje klíčové služby Azure a poskytuje obecnou implementaci obchodního scénáře. Můžete ho použít jako výchozí bod pro vlastní implementaci a [přizpůsobit](../iot-accelerators/iot-accelerators-remote-monitoring-customize.md) si ho tak, aby splňovalo vaše konkrétní obchodní požadavky.

Tento článek vás provede některé klíčové prvky řešení vzdáleného monitorování, které vám umožní pochopit, jak to funguje. Díky tomu budete moct:

* Odstraňovat potíže v řešení.
* Naplánujte, jak řešení přizpůsobit podle konkrétních požadavků.
* Navrhněte vlastní řešení IoT, které používá služby Azure.

Kód akcelerátoru řešení vzdáleného monitorování je k dispozici na GitHubu:

* [.NET](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet)
* [Java](https://github.com/Azure/azure-iot-pcs-remote-monitoring-java)

## <a name="logical-architecture"></a>Logická architektura

Následující diagram popisuje logické součásti akcelerátoru řešení vzdáleného monitorování překryté architekturou [IoT](../iot-fundamentals/iot-introduction.md):

![Logická architektura](./media/iot-accelerators-remote-monitoring-sample-walkthrough/remote-monitoring-architecture.png)

## <a name="why-microservices"></a>Proč mikroslužby?

Cloudová architektura se vyvíjela od doby, kdy Společnost Microsoft vydala první akcelerátory řešení. [Mikroslužby](https://azure.microsoft.com/blog/microservices-an-application-revolution-powered-by-the-cloud/) se ukázaly jako osvědčená praxe k dosažení rozsahu a flexibility bez obětování rychlosti vývoje. Několik služeb společnosti Microsoft používá tento architektonický vzor interně s velkou spolehlivostí a škálovatelností. Aktualizované akcelerátory řešení uvést tyto poznatky do praxe, takže můžete také těžit z nich.

> [!TIP]
> Další informace o architekturách mikroslužeb najdete v tématech věnovaných [architektuře aplikací .NET](https://www.microsoft.com/net/learn/architecture) a [mikroslužbám jako revoluci v aplikacích, kterou umožnil cloud](https://azure.microsoft.com/blog/microservices-an-application-revolution-powered-by-the-cloud/).

## <a name="device-connectivity"></a>Připojení zařízení

Řešení obsahuje následující součásti v části připojení zařízení logické architektury:

### <a name="real-devices"></a>Skutečná zařízení

K řešení můžete připojit skutečná zařízení. Můžete implementovat chování simulovaných zařízení pomocí sad SDK zařízení Azure IoT.

Skutečná zařízení můžete zřídit z řídicího panelu na portálu řešení.

### <a name="device-simulation-microservice"></a>Mikroservis simulace zařízení

Řešení zahrnuje [mikroslužbu simulace zařízení,](https://github.com/Azure/remote-monitoring-services-dotnet/tree/master/device-simulation) která umožňuje spravovat fond simulovaných zařízení z portálu řešení k testování toku začátku do konce v řešení. Simulovaná zařízení:

* Vygenerujte telemetrii mezi zařízeními.
* Reagujte na volání metod cloud-zařízení z ioT hubu.

Mikroslužba poskytuje koncový bod RESTful pro vytváření, spuštění a zastavení simulace. Každá simulace se skládá ze sady virtuálních zařízení různých typů, které odesílají telemetrii a reagují na volání metod.

Simulovaná zařízení můžete zřídit z řídicího panelu na portálu řešení.

### <a name="iot-hub"></a>IoT Hub

Centrum [IoT](../iot-hub/index.yml) ingestuje telemetrická data odeslaná ze skutečných i simulovaných zařízení do cloudu. Služba IoT hub zpřístupňuje telemetrická data pro služby v back-endu řešení IoT pro zpracování.

Služba IoT Hub v řešení také:

* Udržuje registr identit, který ukládá ID a ověřovací klíče všech zařízení povolených pro připojení k portálu.
* Vyvolá metody na vašich zařízeních jménem akcelerátoru řešení.
* Udržuje dvojčata zařízení pro všechna registrovaná zařízení. Dvojče zařízení ukládá hodnoty vlastností ohlášené zařízením. Dvojče zařízení také ukládá požadované vlastnosti nastavené na portálu řešení, aby si je zařízení při dalším připojení mohlo načíst.
* Plánuje úlohy, které nastaví vlastnosti pro více zařízení, nebo vyvolává metody ve více zařízeních.

## <a name="data-processing-and-analytics"></a>Zpracování a analýza dat

Řešení obsahuje následující součásti v části zpracování dat a analýzy součástí logické architektury:

### <a name="iot-hub-manager-microservice"></a>Mikroservis správce ioT hubu

Řešení zahrnuje [mikroslužbu správce ioT hubu](https://github.com/Azure/remote-monitoring-services-dotnet/tree/master/iothub-manager) pro zpracování interakcí s vaším centrem IoT hub, jako jsou:

* Vytváření a správa zařízení IoT.
* Správa dvojčat zařízení.
* Vyvolání metod na zařízeních.
* Správa přihlašovacích údajů ioT.

Tato služba také spouští dotazy služby IoT Hub k načtení zařízení patřících do skupin definovaných uživatelem.

Mikroslužba poskytuje koncový bod RESTful pro správu zařízení a dvojčata zařízení, vyvolání metod a spouštění dotazů služby IoT Hub.

### <a name="device-telemetry-microservice"></a>Mikroslužba telemetrie zařízení

[Mikroslužba telemetrie zařízení](https://github.com/Azure/remote-monitoring-services-dotnet/tree/master/device-telemetry) poskytuje koncový bod RESTful pro přístup pro čtení k telemetrii zařízení uložené v Přehledech časové řady. Koncový bod RESTful také umožňuje operace CRUD s pravidly a přístupem pro čtení a zápis pro definice alarmů z úložiště.

### <a name="storage-adapter-microservice"></a>Mikroslužba adaptéru úložiště

[Mikroslužba adaptéru úložiště](https://github.com/Azure/remote-monitoring-services-dotnet/tree/master/storage-adapter) spravuje dvojice klíč-hodnota, abstrahuje sémantiku služby úložiště a představuje jednoduché rozhraní pro ukládání dat libovolného formátu pomocí Azure Cosmos DB.

Hodnoty jsou uspořádány v kolekcích. Můžete pracovat na jednotlivých hodnotách nebo načíst celé kolekce. Komplexní datové struktury jsou serializovány klienty a spravovány jako jednoduchá datová část textu.

Služba poskytuje koncový bod RESTful pro operace CRUD na páry klíč hodnota. values

### <a name="azure-cosmos-db"></a>Azure Cosmos DB

Nasazení akcelerátorů řešení používají [Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/) k ukládání pravidel, výstrah, nastavení konfigurace a všech ostatních studených úložišť.

### <a name="azure-stream-analytics-manager-microservice"></a>Mikroservis správce Azure Stream Analytics

[Mikroslužba správce Azure Stream Analytics](https://github.com/Azure/remote-monitoring-services-dotnet/tree/master/asa-manager) spravuje úlohy Azure Stream Analytics (ASA), včetně nastavení jejich konfigurace, jejich spuštění a zastavení a sledování jejich stavu.

Úloha ASA je podporována dvěma referenčními datovými sadami. Jedna sada dat definuje pravidla a jedna definuje skupiny zařízení. Referenční data pravidel jsou generována z informací spravovaných mikroslužbou telemetrie zařízení. Mikroslužba správce Azure Stream Analytics transformuje pravidla telemetrie do logiky zpracování datového proudu.

Referenční data skupin zařízení se používají k identifikaci skupiny pravidel, která se mají použít pro příchozí telemetrickou zprávu. Skupiny zařízení jsou spravované konfigurační mikroslužbou a používají dotazy na dvojče zařízení služby Azure IoT Hub.

Úlohy ASA poskytují telemetrii z připojených zařízení do time series insights pro ukládání a analýzu.

### <a name="azure-stream-analytics"></a>Azure Stream Analytics

[Azure Stream Analytics](https://docs.microsoft.com/azure/stream-analytics/) je modul pro zpracování událostí, který umožňuje zkoumat velké objemy datových proudů ze zařízení.

### <a name="azure-time-series-insights"></a>Azure Time Series Insights

[Azure Time Series Insights](https://docs.microsoft.com/azure/time-series-insights/) ukládá telemetrii ze zařízení připojených k akcelerátoru řešení. Umožňuje také vizualizaci a dotazování telemetrie zařízení v uživatelském rozhraní řešení webu.

### <a name="configuration-microservice"></a>Konfigurační mikroslužba

[Konfigurační mikroslužba](https://github.com/Azure/remote-monitoring-services-dotnet/tree/master/config) poskytuje koncový bod RESTful pro operace CRUD na skupinách zařízení, nastavení řešení a nastavení uživatele v akcelerátoru řešení. Pracuje s mikroslužbou adaptéru úložiště zachovat konfigurační data.

### <a name="authentication-and-authorization-microservice"></a>Mikroslužba ověřování a autorizace

[Mikroslužba ověřování a autorizace](https://github.com/Azure/remote-monitoring-services-dotnet/tree/master/auth) spravuje uživatele oprávněné k přístupu k akcelerátoru řešení. Správu uživatelů lze provádět pomocí libovolného poskytovatele služeb identity, který podporuje [OpenId Connect](https://openid.net/connect/).

### <a name="azure-active-directory"></a>Azure Active Directory

Nasazení akcelerátorů řešení používají [Službu Azure Active Directory](https://docs.microsoft.com/azure/active-directory/) jako poskytovatele OpenID Connect. Služba Azure Active Directory ukládá informace o uživateli a poskytuje certifikáty k ověření podpisů tokenů JWT.

## <a name="presentation"></a>Zobrazení

Řešení obsahuje následující součásti v prezentační části logické architektury:

[Webové uživatelské rozhraní je react javascriptová aplikace](https://github.com/Azure/pcs-remote-monitoring-webui). Aplikace:

* Používá pouze Javascript React a běží výhradně v prohlížeči.
* Je stylizovaný s CSS.
* Spolupracuje s veřejnými čelí mikroslužeb prostřednictvím volání AJAX.

Uživatelské rozhraní představuje všechny funkce akcelerátoru řešení a spolupracuje s jinými mikroslužbami, jako jsou:

* Ověřování a autorizace mikroslužby pro ochranu uživatelských dat.
* Mikroslužba správce ioT hubu pro seznam a správu zařízení IoT.

Uživatelské rozhraní integruje průzkumník Azure Time Series Insights, který umožňuje dotazování a analýzu telemetrie zařízení.

Konfigurační mikroslužba umožňuje uživatelskému rozhraní ukládat a načítat nastavení konfigurace.

## <a name="next-steps"></a>Další kroky

Pokud chcete prozkoumat zdrojový kód a dokumentaci pro vývojáře, začněte s jedním ze dvou úložišť GitHub:

* [Akcelerátor řešení pro vzdálené monitorování pomocí Azure IoT (.NET)](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet).
* [Akcelerátor řešení pro vzdálené monitorování pomocí Azure IoT (Java)](https://github.com/Azure/azure-iot-pcs-remote-monitoring-java).

Podrobné diagramy architektury řešení:
* [Akcelerátor řešení pro architekturu vzdáleného monitorování](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Architecture).

Další rámcové informace o akcelerátoru řešení vzdáleného monitorování naleznete [v tématu Customize the solution accelerator](../iot-accelerators/iot-accelerators-remote-monitoring-customize.md).
