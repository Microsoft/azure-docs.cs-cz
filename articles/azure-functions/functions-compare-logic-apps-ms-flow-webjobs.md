---
title: What are Microsoft Flow, Logic Apps, Functions, and WebJobs? – Azure
description: 'Compare Microsoft cloud services that are optimized for integration tasks: Microsoft Flow, Logic Apps, Functions, and WebJobs.'
ms.topic: overview
ms.date: 04/09/2018
ms.custom: mvc
ms.openlocfilehash: 6cf820af8a5b97de536a9d07e49ec822ffb76681
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/20/2019
ms.locfileid: "74230872"
---
# <a name="what-are-microsoft-flow-logic-apps-functions-and-webjobs"></a>What are Microsoft Flow, Logic Apps, Functions, and WebJobs?

This article compares the following Microsoft cloud 
* [Microsoft Flow](https://flow.microsoft.com/)
* [Azure Logic Apps](https://azure.microsoft.com/services/logic-apps/)
* [Azure Functions](https://azure.microsoft.com/services/functions/)
* [Azure App Service WebJobs](../app-service/webjobs-create.md)

Všechny tyto služby dokáží řešit problémy s integrací a automatizovat obchodní procesy. Všechny můžou definovat vstup, akce, podmínky a výstup. Každou z nich můžete spouštět podle plánu nebo triggerem. Each service has unique advantages, and this article explains the differences. 

If you're looking for a more general comparison between Azure Functions and other Azure compute options, see [Criteria for choosing an Azure compute service](/azure/architecture/guide/technology-choices/compute-comparison) and [Choosing an Azure compute option for microservices](/azure/architecture/microservices/design/compute-options).

## <a name="compare-microsoft-flow-and-azure-logic-apps"></a>Porovnání služeb Microsoft Flow a Azure Logic Apps

Microsoft Flow and Logic Apps are both *designer-first* integration services that can create workflows. Obě tyto služby se integrují s různými aplikacemi SaaS a podnikovými aplikacemi. 

Microsoft Flow is built on top of Logic Apps. They share the same workflow designer and the same [connectors](../connectors/apis-list.md). 

Microsoft Flow empowers any office worker to perform simple integrations (for example, an approval process on a SharePoint Document Library) without going through developers or IT. Logic Apps can also enable advanced integrations (for example, B2B processes) where enterprise-level Azure DevOps and security practices are required. U obchodních pracovních postupů je typické, že se jejich složitost v průběhu času zvyšuje. Accordingly, you can start with a flow at first, and then convert it to a logic app as needed.

The following table helps you determine whether Microsoft Flow or Logic Apps is best for a particular integration:

|  | Microsoft Flow | Logic Apps |
| --- | --- | --- |
| Uživatelé |Administrativní pracovníci, podnikoví uživatelé, správci služby SharePoint |Profesionální integrátoři a vývojáře, IT profesionálové |
| Scénáře |Samoobsluha |Pokročilé integrace |
| Design tool |V prohlížeči a mobilní aplikaci, pouze uživatelské rozhraní |V prohlížeči a sadě [Visual Studio](../logic-apps/logic-apps-deploy-from-vs.md), k dispozici je [zobrazení kódu](../logic-apps/logic-apps-author-definitions.md) |
| Application lifecycle management (ALM) |Design and test in non-production environments, promote to production when ready |Azure DevOps: source control, testing, support, automation, and manageability in [Azure Resource Manager](../logic-apps/logic-apps-create-deploy-azure-resource-manager-templates.md) |
| Admin experience |Manage Microsoft Flow environments and data loss prevention (DLP) policies, track licensing: [Microsoft Flow Admin Center](https://admin.flow.microsoft.com) |Manage resource groups, connections, access management, and logging: [Azure portal](https://portal.azure.com) |
| Zabezpečení |Office 365 Security and Compliance audit logs, DLP, [encryption at rest](https://wikipedia.org/wiki/Data_at_rest#Encryption) for sensitive data |Security assurance of Azure: [Azure security](https://www.microsoft.com/en-us/trustcenter/Security/AzureSecurity), [Azure Security Center](https://azure.microsoft.com/services/security-center/), [audit logs](https://azure.microsoft.com/blog/azure-audit-logs-ux-refresh/) |

## <a name="compare-azure-functions-and-azure-logic-apps"></a>Porovnání služeb Azure Functions a Azure Logic Apps

Functions a Logic Apps jsou služby Azure, které umožňují provádění úloh bez serveru. Azure Functions is a serverless compute service, whereas Azure Logic Apps provides serverless workflows. Both can create complex *orchestrations*. Orchestrace je kolekce funkcí nebo kroků (v Logic Apps se nazývají *akce*), jejichž spouštěním se provede složitý úkol. For example, to process a batch of orders, you might execute many instances of a function in parallel, wait for all instances to finish, and then execute a function that computes a result on the aggregate.

K vývoji orchestrací pro Azure Functions se používá zápis kódu a [rozšíření Durable Functions](durable/durable-functions-overview.md). K vytváření orchestrací pro Logic Apps se používá grafické rozhraní (GUI) nebo úprava konfiguračních souborů.

Při sestavování orchestrace lze služby kombinovat, takže je možné volat funkce z aplikací logiky a volat aplikace logiky z funkcí. Způsob sestavování orchestrace volte na základě možností těchto služeb nebo podle svých potřeb. The following table lists some of the key differences between these  
|  | Odolná služba Functions | Logic Apps |
| --- | --- | --- |
| Vývoj | Založeno na kódu (závazné) | Založeno na návrháři (deklarativní) |
| Připojení | [Zhruba tucet předdefinovaných typů vazeb](functions-triggers-bindings.md#supported-bindings), zápis kódu pro vlastní vazby | [Rozsáhlá kolekce konektorů](../connectors/apis-list.md), [Enterprise Integration Pack pro scénáře B2B](../logic-apps/logic-apps-enterprise-integration-overview.md), [sestavení vlastních konektorů](../logic-apps/custom-connector-overview.md) |
| Akce | Každá aktivita je funkce Azure; zápis kódu pro funkce aktivity |[Rozsáhlá kolekce předdefinovaných akcí](../logic-apps/logic-apps-workflow-actions-triggers.md)|
| Sledování | [Azure Application Insights](../azure-monitor/app/app-insights-overview.md) | [Azure portal](../logic-apps/quickstart-create-first-logic-app-workflow.md), [Azure Monitor logs](../logic-apps/logic-apps-monitor-your-logic-apps.md)|
| Správa | [REST API](durable/durable-functions-http-api.md), [Visual Studio](https://docs.microsoft.com/azure/vs-azure-tools-resources-managing-with-cloud-explorer) | [Azure Portal](../logic-apps/quickstart-create-first-logic-app-workflow.md), [REST API](https://docs.microsoft.com/rest/api/logic/), [PowerShell](https://docs.microsoft.com/powershell/module/az.logicapp), [Visual Studio](https://docs.microsoft.com/azure/logic-apps/manage-logic-apps-with-visual-studio) |
| Kontext spuštění | Can run [locally](functions-runtime-overview.md) or in the cloud | Runs only in the cloud|

<a name="function"></a>

## <a name="compare-functions-and-webjobs"></a>Porovnání služeb Functions a WebJobs

Podobně jako Azure Functions je služba Azure App Service WebJobs se sadou WebJobs SDK integrační služba *založená na kódu* a navržená pro vývojáře. Obě jsou postavené na službě [Azure App Service](../app-service/overview.md) a podporují funkce, jako jsou [integrace správy zdrojového kódu](../app-service/deploy-continuous-deployment.md), [ověřování](../app-service/overview-authentication-authorization.md) a [integrace monitorování pomocí Application Insights](functions-monitoring.md).

### <a name="webjobs-and-the-webjobs-sdk"></a>Služba WebJobs a sada WebJobs SDK

You can use the *WebJobs* feature of App Service to run a script or code in the context of an App Service web app. Sada *WebJobs SDK* je architektura navržená pro službu WebJobs, která zjednodušuje psaní kódu pro reakci na události ve službách Azure. For example, you might respond to the creation of an image blob in Azure Storage by creating a thumbnail image. Sada WebJobs SDK se spouští jako konzolová aplikace .NET, kterou nasadíte do webové úlohy. 

Služba WebJobs a sada WebJobs SDK fungují nejlépe společně, ale můžete použít i službu WebJobs bez sady WebJobs SDK a naopak. Ve službě WebJobs je možné spustit jakýkoli program nebo skript, který se spouští v sandboxu služby App Service. Konzolovou aplikaci WebJobs SDK je možné spustit kdekoli, kde se konzolová aplikace spouští, například na místních serverech.

### <a name="comparison-table"></a>Srovnávací tabulka

Služba Azure Functions je postavená na sadě WebJobs SDK, takže sdílí řadu stejných aktivačních událostí a připojení k dalším službám Azure. Here are some factors to consider when you're choosing between Azure Functions and WebJobs with the WebJobs SDK:

|  | Functions | Služba WebJobs se sadou WebJobs SDK |
| --- | --- | --- |
|[Model aplikace bez serveru](https://azure.microsoft.com/solutions/serverless/) s [automatickým škálováním](functions-scale.md#how-the-consumption-and-premium-plans-work)|✔||
|[Vývoj a testování v prohlížeči](functions-create-first-azure-function.md) |✔||
|[Ceny s platbami za použití](functions-scale.md#consumption-plan)|✔||
|[Integrace s Logic Apps](functions-twitter-email.md)|✔||
| Aktivační události |[Časovač](functions-bindings-timer.md)<br>[Fronty a objekty blob služby Azure Storage](functions-bindings-storage-blob.md)<br>[Fronty a témata služby Azure Service Bus](functions-bindings-service-bus.md)<br>[Azure Cosmos DB](functions-bindings-cosmosdb.md)<br>[Azure Event Hubs](functions-bindings-event-hubs.md)<br>[HTTP/Webhook (GitHub, Slack)](functions-bindings-http-webhook.md)<br>[Azure Event Grid](functions-bindings-event-grid.md)|[Časovač](functions-bindings-timer.md)<br>[Fronty a objekty blob služby Azure Storage](functions-bindings-storage-blob.md)<br>[Fronty a témata služby Azure Service Bus](functions-bindings-service-bus.md)<br>[Azure Cosmos DB](functions-bindings-cosmosdb.md)<br>[Azure Event Hubs](functions-bindings-event-hubs.md)<br>[Systém souborů](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions/Extensions/Files/FileTriggerAttribute.cs)|
| Podporované jazyky  |C#<br>F#<br>JavaScript<br>Java<br>Python |Jazyk C#<sup>1</sup>|
|Správci balíčků|NPM a NuGet|NuGet<sup>2</sup>|

<sup>1</sup> WebJobs (without the WebJobs SDK) supports C#, Java, JavaScript, Bash, .cmd, .bat, PowerShell, PHP, TypeScript, Python, and more. This is not a comprehensive list. Ve webové úloze je možné spustit jakýkoli program nebo skript, který je možné spustit v sandboxu služby App Service.

<sup>2</sup> WebJobs (without the WebJobs SDK) supports NPM and NuGet.

### <a name="summary"></a>Souhrn

Azure Functions offers more developer productivity than Azure App Service WebJobs does. It also offers more options for programming languages, development environments, Azure service integration, and pricing. Pro většinu scénářů se jedná o nejlepší volbu.

Tady jsou dva scénáře, pro které může být nejlepší volbou služba WebJobs:

* Potřebujete větší kontrolu nad kódem objektu `JobHost`, který naslouchá událostem. Služba Functions nabízí omezené možnosti přizpůsobení chování objektu `JobHost` v souboru [host.json](functions-host-json.md). Někdy potřebujete provádět operace, které není možné zadat řetězcem v souboru JSON. Například vlastní zásadu opakování pro službu Azure Storage umožňuje vytvořit pouze sada WebJobs SDK.
* You have an App Service app for which you want to run code snippets, and you want to manage them together in the same Azure DevOps environment.

Pro jiné scénáře, kdy chcete spouštět fragmenty kódu pro integraci služeb Azure nebo třetích stran, místo služby WebJobs se sadou WebJobs SDK zvolte Azure Functions.

<a name="together"></a>

## <a name="microsoft-flow-logic-apps-functions-and-webjobs-together"></a>Microsoft Flow, Logic Apps, Functions, and WebJobs together

You don't have to choose just one of these services. They integrate with each other as well as they do with external services.

Tok může volat aplikaci logiky. Aplikace logiky může volat funkci a funkce může volat aplikaci logiky. Viz například [Vytvoření funkce, která se integruje s Azure Logic Apps](functions-twitter-email.md).

The integration between Microsoft Flow, Logic Apps, and Functions continues to improve over time. Můžete něco vytvořit v jedné službě a používat to v ostatních službách.

You can get more information on integration services by using the following links:

* [Leveraging Azure Functions & Azure App Service for integration scenarios by Christopher Anderson](https://www.biztalk360.com/integrate-2016-resources/leveraging-azure-functions-azure-app-service-integration-scenarios/) (Využití služeb Azure Functions a Azure App Service pro scénáře integrace, autor: Christopher Anderson)
* [Integrations Made Simple by Charles Lamanna](https://www.biztalk360.com/integrate-2016-resources/integrations-made-simple/) (Zjednodušená integrace, autor: Charlese Lamanna)
* [Logic Apps Live webcast](https://aka.ms/logicappslive)
* [Microsoft Flow frequently asked questions](/power-automate/frequently-asked-questions)

## <a name="next-steps"></a>Další kroky

Začněte vytvořením svého prvního toku, aplikace logiky nebo aplikace funkcí. Select any of the following links:

* [Začínáme s Microsoft Flow](/power-automate/getting-started)
* [Vytvoření aplikace logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md)
* [Create your first Azure function](functions-create-first-azure-function.md)
