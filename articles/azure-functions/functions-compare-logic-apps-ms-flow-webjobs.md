---
title: Co je Microsoft Flow, Logic Apps, Functions a WebJobs? – Azure
description: 'Porovnání cloudových služeb Microsoftu, které jsou optimalizované pro úlohy integrace: Microsoft Flow, Logic Apps, Functions a WebJobs.'
services: functions, logic-apps
documentationcenter: na
author: ggailey777
manager: jeconnoc
keywords: microsoft flow, flow, logic apps, azure functions, functions, azure webjobs, webjobs, event processing, dynamic compute, serverless architecture
ms.service: azure-functions
ms.devlang: multiple
ms.topic: overview
ms.date: 04/09/2018
ms.author: glenga
ms.custom: mvc
ms.openlocfilehash: 2c0eb50e962af5b67ef9c01588df42f74dd44368
ms.sourcegitcommit: 39397603c8534d3d0623ae4efbeca153df8ed791
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/12/2019
ms.locfileid: "56100291"
---
# <a name="what-are-microsoft-flow-logic-apps-functions-and-webjobs"></a>Co je Microsoft Flow, Logic Apps, Functions a WebJobs?

Tento článek porovnává tyto cloudové služby společnosti Microsoft:

* [Microsoft Flow](https://flow.microsoft.com/)
* [Azure Logic Apps](https://azure.microsoft.com/services/logic-apps/)
* [Azure Functions](https://azure.microsoft.com/services/functions/)
* [Azure App Service WebJobs](../app-service/webjobs-create.md)

Všechny tyto služby dokáží řešit problémy s integrací a automatizovat obchodní procesy. Všechny můžou definovat vstup, akce, podmínky a výstup. Každou z nich můžete spouštět podle plánu nebo triggerem. Každá služba má jedinečné výhody a tento článek vysvětluje rozdíly.

## <a name="compare-microsoft-flow-and-azure-logic-apps"></a>Porovnání služeb Microsoft Flow a Azure Logic Apps

Microsoft Flow a Logic Apps jsou obě *založeno na návrháři* integrační služby, které můžete vytvářet pracovní postupy. Obě tyto služby se integrují s různými aplikacemi SaaS a podnikovými aplikacemi. 

Microsoft Flow je postavená na službě Logic Apps. Sdílejí stejného návrháře pracovních postupů a stejné [konektory](../connectors/apis-list.md). 

Microsoft Flow umožňuje libovolné pracovníkovi provádět jednoduché integrace (například proces schvalování v knihovně dokumentů Sharepointu) bez nutnosti kontaktovat vývojáře nebo IT. Logic Apps můžete také povolit pokročilé integrace (například procesy B2B), kde se vyžadují postupy zabezpečení a DevOps Azure podnikové úrovni. U obchodních pracovních postupů je typické, že se jejich složitost v průběhu času zvyšuje. Odpovídajícím způsobem začněte s tokem zpočátku a pak ho převést na aplikaci logiky podle potřeby.

Následující tabulka vám pomůže určit, jestli je nejlepší pro konkrétní integrace Microsoft Flow nebo Logic Apps:

|  | Microsoft Flow | Logic Apps |
| --- | --- | --- |
| Uživatelé |Administrativní pracovníci, podnikoví uživatelé, správci služby SharePoint |Profesionální integrátoři a vývojáře, IT profesionálové |
| Scénáře |Samoobslužné |Pokročilé integrace |
| Návrhářský nástroj |V prohlížeči a mobilní aplikaci, pouze uživatelské rozhraní |V prohlížeči a sadě [Visual Studio](../logic-apps/logic-apps-deploy-from-vs.md), k dispozici je [zobrazení kódu](../logic-apps/logic-apps-author-definitions.md) |
| Správa životního cyklu aplikací (ALM) |Navrhování a testování v neprodukčním prostředí, Přesun do produkčního prostředí až to budete mít |Azure DevOps: zdrojového ovládacího prvku, testování, podpora, automatizace a možnosti správy ve [Azure Resource Manageru](../logic-apps/logic-apps-create-deploy-azure-resource-manager-templates.md) |
| Prostředí pro správu |Správa zásad ochrany před únikem informací (DLP) ke ztrátě Microsoft Flow pro prostředí a dat, sledování licencí: [Centrum pro správu Microsoft Flow](https://admin.flow.microsoft.com) |Správa skupin prostředků, připojení, správy přístupu a protokolování: [Azure Portal](https://portal.azure.com) |
| Zabezpečení |Protokoly ochrany před únikem informací, auditu Office 365 zabezpečení a dodržování předpisů [šifrování v klidovém stavu](https://wikipedia.org/wiki/Data_at_rest#Encryption) pro citlivá data |Zajištění zabezpečení Azure: [Zabezpečení Azure](https://www.microsoft.com/en-us/trustcenter/Security/AzureSecurity), [Azure Security Center](https://azure.microsoft.com/services/security-center/), [protokoly auditu](https://azure.microsoft.com/blog/azure-audit-logs-ux-refresh/) |

## <a name="compare-azure-functions-and-azure-logic-apps"></a>Porovnání služeb Azure Functions a Azure Logic Apps

Functions a Logic Apps jsou služby Azure, které umožňují provádění úloh bez serveru. Azure Functions je výpočetní prostředí služby, zatímco Azure Logic Apps poskytuje pracovních postupů bez serveru. Jak vytvořit komplexní *Orchestrace*. Orchestrace je kolekce funkcí nebo kroků (v Logic Apps se nazývají *akce*), jejichž spouštěním se provede složitý úkol. Při zpracování dávky objednávek, například může spustit velký počet instancí funkce současně, počkat na dokončení všech instancí a pak spustit funkci, která vypočítá výsledek pro agregaci.

K vývoji orchestrací pro Azure Functions se používá zápis kódu a [rozšíření Durable Functions](durable/durable-functions-concepts.md). K vytváření orchestrací pro Logic Apps se používá grafické rozhraní (GUI) nebo úprava konfiguračních souborů.

Při sestavování orchestrace lze služby kombinovat, takže je možné volat funkce z aplikací logiky a volat aplikace logiky z funkcí. Způsob sestavování orchestrace volte na základě možností těchto služeb nebo podle svých potřeb. Následující tabulka uvádí některé hlavní rozdíly mezi těmito službami:
 
|  | Odolná služba Functions | Logic Apps |
| --- | --- | --- |
| Vývoj | Založeno na kódu (závazné) | Založeno na návrháři (deklarativní) |
| Připojení | [Zhruba tucet předdefinovaných typů vazeb](functions-triggers-bindings.md#supported-bindings), zápis kódu pro vlastní vazby | [Rozsáhlá kolekce konektorů](../connectors/apis-list.md), [Enterprise Integration Pack pro scénáře B2B](../logic-apps/logic-apps-enterprise-integration-overview.md), [sestavení vlastních konektorů](../logic-apps/custom-connector-overview.md) |
| Akce | Každá aktivita je funkce Azure; zápis kódu pro funkce aktivity |[Rozsáhlá kolekce předdefinovaných akcí](../logic-apps/logic-apps-workflow-actions-triggers.md)|
| Monitorování | [Azure Application Insights](../azure-monitor/app/app-insights-overview.md) | [Azure Portal](../logic-apps/quickstart-create-first-logic-app-workflow.md), [Log Analytics](../logic-apps/logic-apps-monitor-your-logic-apps.md)|
| Správa | [REST API](durable/durable-functions-http-api.md), [Visual Studio](https://docs.microsoft.com/azure/vs-azure-tools-resources-managing-with-cloud-explorer) | [Azure Portal](../logic-apps/quickstart-create-first-logic-app-workflow.md), [REST API](https://docs.microsoft.com/rest/api/logic/), [PowerShell](https://docs.microsoft.com/powershell/module/az.logicapp), [Visual Studio](https://docs.microsoft.com/azure/logic-apps/manage-logic-apps-with-visual-studio) |
| Kontext spuštění | Můžete spustit [místně](functions-runtime-overview.md) nebo v cloudu | Spustí jenom v cloudu|

<a name="function"></a>

## <a name="compare-functions-and-webjobs"></a>Porovnání služeb Functions a WebJobs

Podobně jako Azure Functions je služba Azure App Service WebJobs se sadou WebJobs SDK integrační služba *založená na kódu* a navržená pro vývojáře. Obě jsou postavené na službě [Azure App Service](../app-service/overview.md) a podporují funkce, jako jsou [integrace správy zdrojového kódu](../app-service/deploy-continuous-deployment.md), [ověřování](../app-service/overview-authentication-authorization.md) a [integrace monitorování pomocí Application Insights](functions-monitoring.md).

### <a name="webjobs-and-the-webjobs-sdk"></a>Služba WebJobs a sada WebJobs SDK

Můžete použít *WebJobs* funkce služby App Service pro spuštění skriptu nebo kódu v rámci služby App Service webovou aplikaci. Sada *WebJobs SDK* je architektura navržená pro službu WebJobs, která zjednodušuje psaní kódu pro reakci na události ve službách Azure. Například je může reagovat na vytvoření objektu blob obrázku ve službě Azure Storage vytvořením miniatury. Sada WebJobs SDK se spouští jako konzolová aplikace .NET, kterou nasadíte do webové úlohy. 

Služba WebJobs a sada WebJobs SDK fungují nejlépe společně, ale můžete použít i službu WebJobs bez sady WebJobs SDK a naopak. Ve službě WebJobs je možné spustit jakýkoli program nebo skript, který se spouští v sandboxu služby App Service. Konzolovou aplikaci WebJobs SDK je možné spustit kdekoli, kde se konzolová aplikace spouští, například na místních serverech.

### <a name="comparison-table"></a>Srovnávací tabulka

Služba Azure Functions je postavená na sadě WebJobs SDK, takže sdílí řadu stejných aktivačních událostí a připojení k dalším službám Azure. Zde jsou některé faktory vzít v úvahu, abyste dokázali vybrat mezi službami Azure Functions a WebJobs se sadou WebJobs SDK:

|  | Functions | Služba WebJobs se sadou WebJobs SDK |
| --- | --- | --- |
|[Model aplikace bez serveru](https://azure.microsoft.com/solutions/serverless/) s [automatickým škálováním](functions-scale.md#how-the-consumption-plan-works)|✔||
|[Vývoj a testování v prohlížeči](functions-create-first-azure-function.md) |✔||
|[Ceny s platbami za použití](functions-scale.md#consumption-plan)|✔||
|[Integrace s Logic Apps](functions-twitter-email.md)|✔||
| Aktivační události |[Časovač](functions-bindings-timer.md)<br>[Fronty a objekty blob služby Azure Storage](functions-bindings-storage-blob.md)<br>[Fronty a témata služby Azure Service Bus](functions-bindings-service-bus.md)<br>[Azure Cosmos DB](functions-bindings-cosmosdb.md)<br>[Azure Event Hubs](functions-bindings-event-hubs.md)<br>[HTTP/Webhook (GitHub, Slack)](functions-bindings-http-webhook.md)<br>[Azure Event Grid](functions-bindings-event-grid.md)|[Časovač](functions-bindings-timer.md)<br>[Fronty a objekty blob služby Azure Storage](functions-bindings-storage-blob.md)<br>[Fronty a témata služby Azure Service Bus](functions-bindings-service-bus.md)<br>[Azure Cosmos DB](functions-bindings-cosmosdb.md)<br>[Azure Event Hubs](functions-bindings-event-hubs.md)<br>[Systém souborů](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions/Extensions/Files/FileTriggerAttribute.cs)|
| Podporované jazyky  |C#<br>F#<br>JavaScript<br>Java (Preview) |Jazyk C#<sup>1</sup>|
|Správci balíčků|NPM a NuGet|NuGet<sup>2</sup>|

<sup>1</sup> WebJobs (bez sady WebJobs SDK) podporuje C#, JavaScript, Bash, .cmd, .bat, PowerShell, PHP, TypeScript, Python a další. Nejedná se o vyčerpávající seznam. Ve webové úloze je možné spustit jakýkoli program nebo skript, který je možné spustit v sandboxu služby App Service.

<sup>2</sup> WebJobs (bez sady WebJobs SDK) podporuje NPM a NuGet.

### <a name="summary"></a>Souhrn

Služba Azure Functions nabízí větší produktivity pro vývojáře, než Azure App Service WebJobs. Nabízí také další možnosti pro programovací jazyky, vývojových prostředích, integraci služeb Azure a ceny. Pro většinu scénářů se jedná o nejlepší volbu.

Tady jsou dva scénáře, pro které může být nejlepší volbou služba WebJobs:

* Potřebujete větší kontrolu nad kódem objektu `JobHost`, který naslouchá událostem. Služba Functions nabízí omezené možnosti přizpůsobení chování objektu `JobHost` v souboru [host.json](functions-host-json.md). Někdy potřebujete provádět operace, které není možné zadat řetězcem v souboru JSON. Například vlastní zásadu opakování pro službu Azure Storage umožňuje vytvořit pouze sada WebJobs SDK.
* Máte aplikaci služby App Service, pro které chcete spouštět fragmenty kódu a chcete je spravovat společně ve stejném prostředí Azure DevOps.

Pro jiné scénáře, kdy chcete spouštět fragmenty kódu pro integraci služeb Azure nebo třetích stran, místo služby WebJobs se sadou WebJobs SDK zvolte Azure Functions.

<a name="together"></a>

## <a name="microsoft-flow-logic-apps-functions-and-webjobs-together"></a>Microsoft Flow, Logic Apps, Functions a WebJobs společně

Není nutné si vybrat pouze jednu z těchto služeb. Integrují se mezi sebou i s externími službami dělají.

Tok může volat aplikaci logiky. Aplikace logiky může volat funkci a funkce může volat aplikaci logiky. Viz například [Vytvoření funkce, která se integruje s Azure Logic Apps](functions-twitter-email.md).

Integrace mezi Microsoft Flow, Logic Apps a Functions pokračuje na jejich zlepšení. Můžete něco vytvořit v jedné službě a používat to v ostatních službách.

Pomocí následujících odkazů můžete získat další informace o integračních službách:

* [Leveraging Azure Functions & Azure App Service for integration scenarios by Christopher Anderson](http://www.biztalk360.com/integrate-2016-resources/leveraging-azure-functions-azure-app-service-integration-scenarios/) (Využití služeb Azure Functions a Azure App Service pro scénáře integrace, autor: Christopher Anderson)
* [Integrations Made Simple by Charles Lamanna](http://www.biztalk360.com/integrate-2016-resources/integrations-made-simple/) (Zjednodušená integrace, autor: Charlese Lamanna)
* [Webové vysílání spuštěné aplikace logiky](https://aka.ms/logicappslive)
* [Nejčastější dotazy k Microsoft Flow](https://flow.microsoft.com/documentation/frequently-asked-questions/)

## <a name="next-steps"></a>Další postup

Začněte vytvořením svého prvního toku, aplikace logiky nebo aplikace funkcí. Vyberte některou z následujících odkazů:

* [Začínáme s Microsoft Flow](https://flow.microsoft.com/en-us/documentation/getting-started/)
* [Vytvoření aplikace logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md)
* [Vytvoření první funkce Azure](functions-create-first-azure-function.md)
