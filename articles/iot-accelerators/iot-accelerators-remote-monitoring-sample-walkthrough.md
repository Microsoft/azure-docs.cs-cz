---
title: Přehled akcelerátoru řešení vzdáleného monitorování – Azure | Microsoft Docs
description: Tento článek poskytuje přehled některých klíčových prvků řešení vzdáleného monitorování, které vám umožní pochopit, jak to funguje.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 03/08/2019
ms.author: dobett
ms.openlocfilehash: f9477b11b52431211dc91a0ea6ff0767a9d495be
ms.sourcegitcommit: a92fbc09b859941ed64128db6ff72b7a7bcec6ab
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/15/2020
ms.locfileid: "92073593"
---
# <a name="remote-monitoring-solution-accelerator-overview"></a>Přehled akcelerátorů řešení pro vzdálené monitorování

[Akcelerátor řešení](../iot-accelerators/about-iot-accelerators.md) vzdáleného monitorování implementuje kompletní řešení monitorování pro více počítačů ve vzdálených umístěních. Řešení kombinuje klíčové služby Azure a poskytuje obecnou implementaci obchodního scénáře. Můžete ho použít jako výchozí bod pro vlastní implementaci a [přizpůsobit](../iot-accelerators/iot-accelerators-remote-monitoring-customize.md) si ho tak, aby splňovalo vaše konkrétní obchodní požadavky.

Tento článek vás provede některými z klíčových prvků řešení vzdáleného monitorování, které vám umožní pochopit, jak to funguje. Díky tomu budete moct:

* Odstraňovat potíže v řešení.
* Naplánujte, jak řešení přizpůsobit podle konkrétních požadavků.
* Navrhněte vlastní řešení IoT, které používá služby Azure.

Kód akcelerátoru řešení vzdáleného monitorování je k dispozici na GitHubu:

* [.NET](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet)
* [Java](https://github.com/Azure/azure-iot-pcs-remote-monitoring-java)

## <a name="logical-architecture"></a>Logická architektura

Následující diagram popisuje logické součásti akcelerátoru řešení vzdáleného monitorování, které se překrývají v [architektuře IoT](../iot-fundamentals/iot-introduction.md):

![Logická architektura](./media/iot-accelerators-remote-monitoring-sample-walkthrough/remote-monitoring-architecture.png)

## <a name="why-microservices"></a>Proč mikroslužby?

Cloudová architektura se vyvinula, protože společnost Microsoft zveřejnila první akcelerátory řešení. [Mikroslužby](https://azure.microsoft.com/blog/microservices-an-application-revolution-powered-by-the-cloud/) se ukázaly jako osvědčené postupy pro dosažení škálování a flexibility, aniž by došlo k obětování rychlosti vývoje. Několik služeb společnosti Microsoft používá tento model architektury interně s velkou spolehlivostí a škálovatelností výsledků. Aktualizované akcelerátory řešení tyto výukové postupy vloží do praxe, abyste je mohli také využít.

> [!TIP]
> Další informace o architekturách mikroslužeb najdete v tématech věnovaných [architektuře aplikací .NET](https://www.microsoft.com/net/learn/architecture) a [mikroslužbám jako revoluci v aplikacích, kterou umožnil cloud](https://azure.microsoft.com/blog/microservices-an-application-revolution-powered-by-the-cloud/).

## <a name="device-connectivity"></a>Připojení zařízení

Toto řešení zahrnuje následující komponenty v části připojení zařízení v rámci logické architektury:

### <a name="real-devices"></a>Skutečná zařízení

K řešení můžete propojit skutečná zařízení. Chování simulovaných zařízení můžete implementovat pomocí sad SDK pro zařízení Azure IoT.

Skutečná zařízení můžete zřídit z řídicího panelu na portálu řešení.

### <a name="device-simulation-microservice"></a>Mikroslužba simulace zařízení

Toto řešení zahrnuje [mikroslužbu simulace zařízení](https://github.com/Azure/remote-monitoring-services-dotnet/tree/master/device-simulation) , která umožňuje správu fondu simulovaných zařízení z portálu řešení a testování kompletního toku v řešení. Simulovaná zařízení:

* Vygenerujte telemetrii ze zařízení do cloudu.
* Reakce na volání metod cloudu na zařízení z IoT Hub.

Mikroslužba poskytuje RESTful koncový bod pro vytvoření, spuštění a zastavení simulace. Každá simulace sestává ze sady virtuálních zařízení různých typů, které odesílají telemetrii a reagují na volání metod.

Simulovaná zařízení můžete zřídit z řídicího panelu na portálu řešení.

### <a name="iot-hub"></a>IoT Hub

[IoT Hub](../iot-hub/index.yml) ingestuje telemetrii odesílaná z reálných i simulovaných zařízení do cloudu. Služba IoT Hub zpřístupňuje telemetrii službám v back-endu řešení IoT pro zpracování.

Služba IoT Hub v řešení také:

* Udržuje registr identit, ve kterém jsou uložené ID a ověřovací klíče všech zařízení s povoleným připojením k portálu.
* Vyvolá metody v zařízeních jménem akcelerátoru řešení.
* Udržuje dvojčata zařízení pro všechna registrovaná zařízení. Dvojče zařízení ukládá hodnoty vlastností ohlášené zařízením. Dvojče zařízení také ukládá požadované vlastnosti nastavené na portálu řešení, aby si je zařízení při dalším připojení mohlo načíst.
* Plánuje úlohy, které nastaví vlastnosti pro více zařízení, nebo vyvolává metody ve více zařízeních.

## <a name="data-processing-and-analytics"></a>Zpracování a analýza dat

Řešení zahrnuje následující komponenty v části zpracování a analýza dat logické architektury:

### <a name="iot-hub-manager-microservice"></a>Mikroslužba IoT Hub Manager

Řešení zahrnuje [mikroslužbu IoT Hub Manageru](https://github.com/Azure/remote-monitoring-services-dotnet/tree/master/iothub-manager) ke zpracování interakcí se službou IoT Hub, jako je:

* Vytváření a Správa zařízení IoT.
* Správa vláken zařízení.
* Vyvolávání metod na zařízeních.
* Správa přihlašovacích údajů IoT

Tato služba také spouští IoT Hub dotazy, aby bylo možné načíst zařízení patřící do uživatelem definovaných skupin.

Mikroslužba poskytuje RESTful koncový bod pro správu zařízení a vlákna zařízení, vyvolání metod a spouštění dotazů IoT Hub.

### <a name="device-telemetry-microservice"></a>Mikroslužba telemetrie zařízení

[Mikroslužba telemetrie zařízení](https://github.com/Azure/remote-monitoring-services-dotnet/tree/master/device-telemetry) poskytuje koncový bod RESTful pro přístup ke službě telemetrie zařízení uložený v Time Series Insights. Koncový bod RESTful také umožňuje operace CRUD pro pravidla a přístup pro čtení a zápis pro definice alarmů z úložiště.

### <a name="storage-adapter-microservice"></a>Mikroslužba adaptéru úložiště

[Mikroslužba adaptéru úložiště](https://github.com/Azure/remote-monitoring-services-dotnet/tree/master/storage-adapter) spravuje páry klíč-hodnota, abstrakci sémantiky služby úložiště a prezentuje jednoduché rozhraní pro ukládání dat libovolného formátu pomocí Azure Cosmos DB.

Hodnoty jsou uspořádány do kolekcí. Můžete pracovat na jednotlivých hodnotách nebo načíst celé kolekce. Komplexní datové struktury jsou serializovány klienty a spravovány jako jednoduchá datová část textu.

Služba poskytuje koncový bod RESTful pro operace CRUD pro páry klíč-hodnota. values

### <a name="azure-cosmos-db"></a>Azure Cosmos DB

Nasazení akcelerátorů řešení používá [Azure Cosmos DB](../cosmos-db/index.yml) k ukládání pravidel, upozornění, nastavení konfigurace a všech ostatních chladírenských úložišť.

### <a name="azure-stream-analytics-manager-microservice"></a>Mikroslužba Azure Stream Analytics Manager

[Mikroslužba Azure Stream Analytics Manager](https://github.com/Azure/remote-monitoring-services-dotnet/tree/master/asa-manager) spravuje úlohy Azure Stream Analytics (ASA), včetně nastavení jejich konfigurace, spouštění a zastavování a monitorování jejich stavu.

Úloha ASA podporuje dvě referenční datové sady. Jedna datová sada definuje pravidla a jednu definuje skupiny zařízení. Pravidla referenčních dat se generují z informací spravovaných mikroslužbou telemetrie zařízení. Mikroslužba Azure Stream Analytics Manager transformuje pravidla telemetrie na logiku zpracování streamu.

Referenční data skupin zařízení slouží k identifikaci skupiny pravidel, která se mají použít u příchozí zprávy telemetrie. Skupiny zařízení se spravují pomocí konfigurace mikroslužeb a používají zdvojené dotazy zařízení v Azure IoT Hub.

Úlohy ASA dodávají telemetrii z připojených zařízení a Time Series Insights k ukládání a analýze.

### <a name="azure-stream-analytics"></a>Azure Stream Analytics

[Azure Stream Analytics](../stream-analytics/index.yml) je modul pro zpracování událostí, který umožňuje kontrolovat velké objemy streamování dat ze zařízení.

### <a name="azure-time-series-insights"></a>Azure Time Series Insights

[Azure Time Series Insights](../time-series-insights/index.yml) ukládá telemetrii ze zařízení připojených k akcelerátoru řešení. Umožňuje také vizualizaci a dotazování telemetrie zařízení ve webovém uživatelském rozhraní řešení.

### <a name="configuration-microservice"></a>Konfigurace mikroslužeb

[Mikroslužba konfigurace](https://github.com/Azure/remote-monitoring-services-dotnet/tree/master/config) poskytuje koncový bod RESTful pro operace CRUD u skupin zařízení, nastavení řešení a uživatelských nastavení v akcelerátoru řešení. Funguje s mikroslužbou adaptéru úložiště, aby zachovala konfigurační data.

### <a name="authentication-and-authorization-microservice"></a>Mikroslužba pro ověřování a autorizaci

[Mikroslužba ověřování a autorizace](https://github.com/Azure/remote-monitoring-services-dotnet/tree/master/auth) spravuje uživatele, kteří mají oprávnění k přístupu k akcelerátoru řešení. Správu uživatelů můžete provádět pomocí libovolného poskytovatele služby identity, který podporuje [OpenId Connect](https://openid.net/connect/).

### <a name="azure-active-directory"></a>Azure Active Directory

Nasazení akcelerátorů řešení používají [Azure Active Directory](../active-directory/index.yml) jako poskytovatele OpenID Connect. Azure Active Directory ukládá informace o uživateli a poskytuje certifikáty k ověření podpisů tokenu JWT.

## <a name="presentation"></a>Zobrazení

Řešení zahrnuje následující komponenty v rámci prezentace v rámci logické architektury:

[Webové uživatelské rozhraní je reakce aplikace JavaScriptu](https://github.com/Azure/pcs-remote-monitoring-webui). Aplikace:

* Používá pouze reakci JavaScriptu a běží zcela v prohlížeči.
* Má styl CSS.
* Vzájemně spolupracuje s veřejnými mikroslužbami prostřednictvím volání AJAX.

Uživatelské rozhraní prezentuje všechny funkce akcelerátoru řešení a spolupracuje s dalšími mikroslužbami, jako jsou:

* Mikroslužba ověřování a autorizace pro ochranu uživatelských dat.
* Mikroslužba IoT Hub Manageru k vypsání a správě zařízení IoT.

Uživatelské rozhraní integruje Průzkumníka Azure Time Series Insights, aby umožnilo dotazování a analýzu telemetrie zařízení.

Mikroslužba konfigurace umožňuje uživatelskému rozhraní ukládat a načítat nastavení konfigurace.

## <a name="next-steps"></a>Další kroky

Pokud chcete prozkoumat zdrojový kód a dokumentaci pro vývojáře, začněte s jedním ze dvou úložišť GitHub:

* [Akcelerátor řešení pro vzdálené monitorování s využitím Azure IoT (.NET)](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet).
* [Akcelerátor řešení pro vzdálené monitorování s využitím Azure IoT (Java)](https://github.com/Azure/azure-iot-pcs-remote-monitoring-java)

Podrobné diagramy architektury řešení:
* [Akcelerátor řešení pro architekturu vzdáleného monitorování](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Architecture).

Další koncepční informace o akcelerátoru řešení vzdáleného monitorování najdete v tématu [přizpůsobení akcelerátoru řešení](../iot-accelerators/iot-accelerators-remote-monitoring-customize.md).