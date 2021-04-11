---
title: Co je služba Azure Digital Twins?
titleSuffix: Azure Digital Twins
description: Přehled toho, co je možné provést pomocí digitálních vláken Azure
author: baanders
ms.author: baanders
ms.date: 3/12/2020
ms.topic: overview
ms.service: digital-twins
ms.openlocfilehash: 315519472baa672e802855b5ab21b8bae8f51935
ms.sourcegitcommit: 5fd1f72a96f4f343543072eadd7cdec52e86511e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/01/2021
ms.locfileid: "106109845"
---
# <a name="what-is-azure-digital-twins"></a>Co je služba Azure Digital Twins?

**Digitální vlákna Azure** je nabídka typu platforma jako služba (PaaS), která umožňuje vytváření diagramů znalostí na základě digitálních modelů celého prostředí. Tato prostředí můžou být budovy, továrny, farmy, energetické sítě, železnice, Stadium a další – dokonce i celá města. Tyto digitální modely se dají využít k získání přehledů, které poskytují lepší produkty, optimalizované operace, snížené náklady a převratná prostředí pro zákazníky.

Využijte své odbornosti v doméně na základě digitálních vláken Azure k vytvoření vlastních, propojených řešení, která:
* Modelujte jakékoliv prostředí a přiveďte digitální vlákna do životně škálovatelného a bezpečného způsobu.
* Připojení prostředků, jako jsou zařízení IoT a stávající obchodní systémy
* Použití robustního systému událostí k sestavení dynamické obchodní logiky a zpracování dat
* Integrujte se službami Azure data, Analytics a AI, které vám pomůžou sledovat předchozí a pak předpovědět budoucnost.

## <a name="azure-digital-twins-capabilities"></a>Možnosti digitálních vláken Azure

Tady je souhrn funkcí poskytovaných pomocí digitálních vláken Azure.

### <a name="open-modeling-language"></a>Otevřít jazyk modelování

V rámci digitálních vláken Azure definujete digitální entity, které reprezentují osoby, místa a věci ve svém fyzickém prostředí, a to pomocí vlastních typů vláken nazývaných [**modely**](concepts-models.md). 

Tyto definice modelu si můžete představit jako specializované SLOVNÍEK pro popis vaší firmy. V případě řešení pro správu budovy můžete například definovat modely, jako je "budova", "patra" a "výtah". Pak můžete vytvořit **digitální vlákna** na základě těchto modelů, které reprezentují konkrétní prostředí.

[!INCLUDE [digital-twins-versus-device-twins](../../includes/digital-twins-versus-device-twins.md)]

Modely jsou definovány v jazyce podobném formátu JSON, který se nazývá [Digital DTDL (digitální vlákna)](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md), a popisují vlákna z pohledu jejich vlastností stavu, událostí telemetrie, příkazů, komponent a vztahů.
* Modely definují sémantické **vztahy** mezi vašimi entitami, abyste mohli připojit své vlákna ke znalostnímu grafu, který odráží jejich interakce. Modely si můžete představit jako podstatná jména v popisu svého světa a vztahy jako slovesa.
* Můžete také specializovat vlákna pomocí dědičnosti modelu. Jeden model může dědit z jiného modelu.

DTDL se používá pro datové modely v jiných službách Azure IoT, včetně služby [PnP (IoT technologie Plug and Play)](../iot-pnp/overview-iot-plug-and-play.md) a [Time Series Insights (TSI)](../time-series-insights/overview-what-is-tsi.md). Díky tomu můžete zajistit, aby řešení digitálních vláken Azure bylo propojené a kompatibilní s jinými částmi ekosystému Azure.

### <a name="live-execution-environment"></a>Živé spouštěcí prostředí

Digitální modely v rámci digitálních vláken Azure jsou živé a aktuální reprezentace reálného světa. Pomocí vztahů ve vlastních DTDL modelech spojíte vlákna do **živého grafu** představujícího vaše prostředí.

Vizualizaci grafu digitálních vláken Azure můžete zobrazit pomocí ukázkové aplikace v [**Průzkumníkovi digitálních vláken Azure**](/samples/azure-samples/digital-twins-explorer/digital-twins-explorer/).

Tady je přehled toho, jak vypadá ukázková vizualizace:

:::image type="content" source="media/includes/azure-digital-twins-explorer.png" alt-text="Snímek obrazovky s ukázkovou aplikací Průzkumníka digitálních vláken Azure a zobrazením grafu uzlů reprezentujících digitální vlákna" lightbox="media/includes/azure-digital-twins-explorer.png":::

Digitální vlákna Azure poskytuje bohatý **systém událostí** , který umožňuje zajistit, aby graf aktuální se zpracováním dat a obchodní logikou. Můžete propojit externí výpočetní prostředky, jako je [Azure Functions](../azure-functions/functions-overview.md), aby se zpracování těchto dat mohlo řídit flexibilním, přizpůsobeným způsobem.

Přehledy můžete také extrahovat z prostředí pro spouštění v reálném čase pomocí výkonného **rozhraní API pro dotazy** Azure Digital revláken. Rozhraní API vám umožní dotazovat se s bohatou vyhledávacími podmínkami, včetně hodnot vlastností, vztahů, vlastností vztahu, informací o modelu a dalších. Můžete také kombinovat dotazy a shromažďovat širokou škálu přehledů o vašem prostředí a odpovídání na vlastní otázky, které jsou pro vás důležité.

### <a name="input-from-iot-and-business-systems"></a>Vstup z IoT a obchodních systémů

Aby se zajistilo aktivní prostředí pro spouštění digitálních vláken Azure s reálným světem, můžete k připojení vašeho řešení k zařízením IoT a IoT Edge použít [IoT Hub](../iot-hub/about-iot-hub.md) . Tato zařízení spravovaná rozbočovačem jsou reprezentována jako součást grafu s dvojitou platností a poskytují data, která řídí váš model.

Pro tento účel můžete vytvořit novou IoT Hub s využitím digitálních vláken Azure nebo připojit stávající IoT Hub spolu se zařízeními, která už spravuje.

Můžete také řídit digitální vlákna Azure z jiných zdrojů dat pomocí rozhraní REST API nebo konektorů k jiným službám, jako je [Logic Apps](../logic-apps/logic-apps-overview.md).

### <a name="output-to-tsi-storage-and-analytics"></a>Výstup do TSI, úložiště a analýzy

Data v modelu digitálních vláken Azure je možné směrovat do navazujících služeb Azure za účelem dalších analýz nebo úložiště. Tato akce je k dispozici prostřednictvím **tras událostí**, které používají [centrum událostí](../event-hubs/event-hubs-about.md), [Event Grid](../event-grid/overview.md)nebo [Service Bus](../service-bus-messaging/service-bus-messaging-overview.md) k řízení požadovaných toků dat.

Mezi trasy událostí můžete dělat tyto věci:
* Ukládání dat Azure Digital Twins v [Azure Data Lake](../storage/blobs/data-lake-storage-introduction.md)
* Analýza dat Azure Digital Twins pomocí [Azure Synapse Analytics](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md) nebo jiných nástrojů Microsoft pro analýzu dat
* Integrace větších pracovních postupů s Logic Apps
* Připojení digitálních vláken Azure k Time Series Insights ke sledování historie časových řad každého vlákna
* Sladění modelu Time Series v Time Series Insights se zdrojem v rámci Azure Digital Twins

To je jiný způsob, jakým se může digitální vlákna Azure připojit k většímu řešení a podporovat vaše vlastní potřeby pro pokračování práce s těmito přehledy.

## <a name="azure-digital-twins-in-a-solution-context"></a>Digitální vlákna Azure v kontextu řešení

Digitální vlákna Azure se běžně používají v kombinaci s jinými službami Azure jako součást rozsáhlejšího řešení IoT. 

Kompletní řešení využívající digitální vlákna Azure může obsahovat tyto části:
* Instance služby digitálních vláken Azure To ukládá vaše dvojitá model a váš graf se stavem a orchestruje zpracování událostí.
* Jedna nebo více klientských aplikací, které zanášejí instanci digitálního vlákna Azure pomocí konfigurace modelů, vytváření topologie a extrahování přehledů z vytvářených grafů.
* Jeden nebo více externích výpočetních prostředků pro zpracování událostí generovaných pomocí digitálních vláken Azure nebo propojených zdrojů dat, jako jsou zařízení. Jeden běžný způsob, jak zajistit výpočetní prostředky, je prostřednictvím [Azure Functions](../azure-functions/functions-overview.md).
* IoT Hub, který poskytuje možnosti správy zařízení a datových proudů IoT.
* Služby pro příjem dat, jako je například integrace pracovního postupu (například [Logic Apps](../logic-apps/logic-apps-overview.md), studené úložiště, integrace časových řad nebo analýza).

Následující diagram znázorňuje, kde se digitální vlákna Azure nachází v kontextu většího řešení Azure IoT.

:::image type="content" source="media/overview/solution-context.png" alt-text="Diagram znázorňující vstupní zdroje, výstupní služby a obousměrnou komunikaci s klientskými aplikacemi i s externími výpočetními prostředky." border="false" lightbox="media/overview/solution-context.png":::

## <a name="service-limits"></a>Omezení služby

Další informace o **omezeních služeb** pro digitální vlákna Azure najdete tady: [omezení služby digitálních vláken Azure](reference-service-limits.md). To může být užitečné při práci se službou pro pochopení funkčnosti služby a omezení rychlosti a omezení, která je v případě potřeby možné upravit.

## <a name="terminology"></a>Terminologie

Seznam **běžných termínů IoT** a jejich použití můžete zobrazit v rámci služeb Azure IoT, včetně digitálních vláken Azure, tady: [Glosář Azure IoT](../iot-fundamentals/iot-glossary.md?toc=/azure/digital-twins/toc.json&bc=/azure/digital-twins/breadcrumb/toc.json). To může být užitečný odkaz, který vám umožní začít s digitálními událostmi Azure a sestavovat řešení IoT.

## <a name="next-steps"></a>Další kroky

* Podrobně se na práci s digitálními podmnožinami Azure v rychlém startu: [*rychlý Start: Prozkoumejte ukázkový scénář*](quickstart-adt-explorer.md).

* Nebo začněte číst informace o konceptech digitálních vláken Azure s [*Koncepty: vlastní modely*](concepts-models.md).