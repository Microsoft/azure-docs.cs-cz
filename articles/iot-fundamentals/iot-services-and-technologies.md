---
title: Technologie a řešení Azure Internet věcí (IoT)
description: Popisuje shromažďování technologií a služeb, které můžete použít k sestavení řešení Azure IoT.
author: dominicbetts
ms.service: iot-fundamentals
services: iot-fundamentals
ms.topic: conceptual
ms.date: 01/15/2020
ms.author: dobett
ms.openlocfilehash: 0678b2c36d341b2bc2bd9be755b9a6213acdd462
ms.sourcegitcommit: 957c916118f87ea3d67a60e1d72a30f48bad0db6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/19/2020
ms.locfileid: "92206080"
---
# <a name="what-azure-technologies-and-services-can-you-use-to-create-iot-solutions"></a>Jaké technologie a služby Azure můžete použít k vytváření řešení IoT?

Technologie a služby Azure IoT poskytují možnosti pro vytváření nejrůznějších řešení IoT, která umožňují digitální transformaci vaší organizace. Můžete například:

- Pomocí služby [Azure IoT Central](https://apps.azureiotcentral.com), spravované aplikační platformy IoT můžete sestavovat a nasazovat zabezpečené řešení IoT na podnikové úrovni. IoT Central nabízí kolekci šablon aplikací specifických pro odvětví, jako je maloobchodní a zdravotní péče, k urychlení procesu vývoje vašeho řešení.
- Pokud chcete implementovat běžný scénář IoT, jako je vzdálené monitorování nebo prediktivní údržba, rozšíříte základ Open-Source kódu pro [akcelerátor řešení](https://www.azureiotsolutions.com) Azure IoT.
- Využijte služby Azure IoT Platform, jako je [azure IoT Hub](../iot-hub/about-iot-hub.md) a sady [SDK pro zařízení Azure IoT](../iot-hub/iot-hub-devguide-sdks.md) , abyste vytvořili vlastní řešení IoT od začátku.

![Technologie, služby a řešení Azure IoT](./media/iot-services-and-technologies/iot-technologies-services.png)

## <a name="azure-iot-central"></a>Azure IoT Central

[IoT Central aplikační platforma](https://apps.azureiotcentral.com) snižuje zatížení a náklady na vývoj, správu a údržbu řešení IoT na podnikové úrovni. Přizpůsobitelné webové uživatelské rozhraní IoT Central v nástroji umožňuje monitorovat podmínky zařízení, vytvářet pravidla a spravovat miliony zařízení a jejich data v průběhu svého životního cyklu. Plocha rozhraní API v rámci IoT Central poskytuje programový přístup ke konfiguraci a interakci s řešením IoT.

Azure IoT Central je plně spravovaná aplikační platforma, kterou můžete použít k vytvoření vlastních řešení IoT. IoT Central používá šablony aplikací k vytváření řešení. Existují šablony pro obecná řešení a pro konkrétní obory, jako je energie, zdravotnictví, státní správa a maloobchod. IoT Central šablony aplikací vám umožní nasadit IoT Central aplikaci během několika minut, kterou pak můžete přizpůsobit pomocí motivů, řídicích panelů a zobrazení.

Vyberte zařízení z [katalogu zařízení Azure Certified for IoT](https://catalog.azureiotsolutions.com) , abyste se mohli rychle připojit k vašemu řešení. Pomocí webového uživatelského rozhraní IoT Central můžete monitorovat a spravovat svoje zařízení, abyste je zachovali v pořádku a připojili. Pomocí konektorů a rozhraní API můžete svou aplikaci IoT Central integrovat s jinými podnikovými aplikacemi.

Jako plně spravovaná aplikační platforma má IoT Central jednoduchý a předvídatelný cenový model.

## <a name="azure-iot-solution-accelerators"></a>Akcelerátory řešení Azure IoT

[Akcelerátory řešení Azure IoT](https://www.azureiotsolutions.com) představují kolekci přizpůsobitelných řešení na podnikové úrovni. Tato řešení můžete nasadit tak, jak jsou, nebo můžete vyvinout vlastní řešení IoT pomocí Open Source zdrojového kódu Java nebo .NET.

Akcelerátory řešení Azure IoT poskytují vysokou úroveň kontroly nad řešením IoT. Akcelerátory řešení obsahují předem vytvořená řešení pro běžné scénáře IoT, které můžete nasadit do svého předplatného Azure během několika minut. Mezi tyto scénáře patří:

  - Vzdálené monitorování
  - Propojená továrna
  - Prediktivní údržba
  - Simulace zařízení

Základ Open-Source kódu pro všechny akcelerátory řešení je k dispozici na GitHubu. Stáhněte si kód pro přizpůsobení akcelerátoru řešení tak, aby splňoval konkrétní požadavky IoT.

Akcelerátory řešení používají služby Azure, jako je Azure IoT Hub a Azure Storage, které musíte spravovat ve vašem předplatném Azure.

## <a name="custom-solutions"></a>Vlastní řešení

Pokud chcete vytvořit řešení IoT od začátku nebo rozšíříte řešení vytvořené pomocí IoT Central nebo akcelerátoru řešení, použijte jednu nebo více z následujících technologií a služeb Azure IoT:

### <a name="devices"></a>Zařízení

Pomocí sady [Azure IoT Starter Kit](https://catalog.azureiotsolutions.com/kits) můžete vyvíjet zařízení IoT nebo si vybrat zařízení, které chcete použít v [katalogu zařízení s certifikací Azure Certified for IoT](https://catalog.azureiotsolutions.com). Implementujte vložený kód pomocí [sad SDK](../iot-hub/iot-hub-devguide-sdks.md)Open Source zařízení. Sady SDK pro zařízení podporují více operačních systémů, například Linux, Windows a operační systémy v reálném čase. Existují sady SDK pro více programovacích jazyků, například [C](https://github.com/Azure/azure-iot-sdk-c), [Node.js](https://github.com/Azure/azure-iot-sdk-node), [Java](https://github.com/Azure/azure-iot-sdk-java), [.NET](https://github.com/Azure/azure-iot-sdk-csharp)a [Python](https://github.com/Azure/azure-iot-sdk-python).

Můžete ještě více zjednodušit vytváření vloženého kódu pro vaše zařízení pomocí služby [IoT technologie Plug and Play](../iot-pnp/overview-iot-plug-and-play.md) . IoT technologie Plug and Play umožňuje vývojářům řešení integrovat zařízení s jejich řešeními bez psaní vloženého kódu. V jádru IoT technologie Plug and Play je schéma _modelu schopností zařízení_ , které popisuje možnosti zařízení. Pomocí modelu schopností zařízení vygenerujte svůj integrovaný kód zařízení a nakonfigurujte cloudové řešení, jako je například aplikace IoT Central.

[Azure IoT Edge](../iot-edge/about-iot-edge.md) umožňuje přesměrovat části úlohy IoT z cloudových služeb Azure na vaše zařízení. IoT Edge může snížit latenci ve vašem řešení, omezit objem dat, která vaše zařízení vyměňuje s cloudem, a povolit online scénáře. Zařízení IoT Edge můžete spravovat z IoT Central a některých akcelerátorů řešení.

[Azure sphere](/azure-sphere/product-overview/what-is-azure-sphere) je zabezpečená špičková aplikační platforma s integrovanými komunikačními funkcemi a funkcemi zabezpečení pro zařízení připojená k Internetu. Zahrnuje zabezpečenou mikrokontrolerů jednotku, vlastní operační systém Linux a cloudovou službu zabezpečení, která poskytuje nepřetržité a obnovitelné zabezpečení.

### <a name="cloud-connectivity"></a>Cloudové připojení

Služba [Azure IoT Hub](../iot-hub/about-iot-hub.md) umožňuje spolehlivou a zabezpečenou obousměrnou komunikaci mezi miliony zařízení IoT a cloudovým řešením. [Azure IoT Hub Device Provisioning Service](../iot-dps/about-iot-dps.md) je pomocná služba pro IoT Hub. Služba poskytuje v pravém centru IoT samoobslužné zřizování zařízení za běhu bez nutnosti zásahu člověka. Tyto možnosti umožňují zákazníkům zabezpečeným a škálovatelným způsobem zřizovat miliony zařízení.

IoT Hub je základní součástí akcelerátorů řešení a můžete ji použít ke splnění výzev implementace IoT, jako jsou:

* Správa a připojení zařízení velkého rozsahu
* Ingestování telemetrie velkého rozsahu
* Příkazy a ovládání zařízení
* Vynucení zabezpečení zařízení

### <a name="bridging-the-gap-between-the-physical-and-digital-worlds"></a>Přemostění mezery mezi fyzickým a digitálním světů

[Digitální vlákna Azure](../digital-twins/overview.md) je služba IoT, která umožňuje modelovat fyzické prostředí. K modelování vztahů mezi lidmi, mezerami a zařízeními používá graf prostorového zpravodajství. Pomocí spolusouvisejících dat napříč digitálními i fyzickými světů můžete vytvářet kontextová řešení s podporou.

IoT Central používá digitální vlákna k synchronizaci zařízení a dat v reálném světě s digitálními modely, které uživatelům umožňují monitorovat a spravovat tato připojená zařízení.

### <a name="data-and-analytics"></a>Data a analýza

Zařízení IoT typicky generují velké objemy dat časových řad, jako je například čtení teploty ze senzorů. [Azure Time Series Insights](../time-series-insights/time-series-insights-overview.md) se můžou připojit ke službě IoT Hub, číst datový proud telemetrie z vašich zařízení, ukládat tato data a používat je k dotazování a vizualizaci.

[Azure Maps](../azure-maps/index.yml) je kolekce geoprostorových služeb, které používají data z nového mapování k zajištění přesného geografického kontextu pro webové a mobilní aplikace. Můžete použít REST API, webový ovládací prvek JavaScriptu nebo Android SDK k sestavení aplikací.

## <a name="next-steps"></a>Další kroky

Pro praktické prostředí zkuste použít jeden z rychlých startů:

- [Vytvoření aplikace Azure IoT Central](../iot-central/core/quick-deploy-iot-central.md)
- [Odeslání telemetrie ze zařízení do služby IoT Hub](../iot-hub/quickstart-send-telemetry-cli.md)
- [Vyzkoušení cloudového řešení pro vzdálené monitorování](../iot-accelerators/quickstart-remote-monitoring-deploy.md)