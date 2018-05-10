---
title: Porovnání služeb Flow, Logic Apps, Functions a WebJobs – Azure
description: 'Porovnání cloudových služeb společnosti Microsoft, které jsou optimalizované pro úlohy integrace: Flow, Logic Apps, Functions a WebJobs'
services: functions,app-service\logic
documentationcenter: na
author: tdykstra
manager: cfowler
tags: ''
keywords: microsoft flow, flow, logic apps, azure functions, functions, azure webjobs, webjobs, event processing, dynamic compute, serverless architecture
ms.service: functions
ms.devlang: multiple
ms.topic: overview
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 04/09/2018
ms.author: tdykstra
ms.custom: mvc
ms.openlocfilehash: 6df97a40be7bf1c437c5228006d114ace768f8ca
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2018
---
# <a name="compare-flow-logic-apps-functions-and-webjobs"></a>Porovnání služeb Flow, Logic Apps, Functions a WebJobs

Tento článek porovnává tyto cloudové služby společnosti Microsoft:

* [Microsoft Flow](https://flow.microsoft.com/)
* [Azure Logic Apps](https://azure.microsoft.com/services/logic-apps/)
* [Azure Functions](https://azure.microsoft.com/services/functions/)
* [Azure App Service WebJobs](../app-service/web-sites-create-web-jobs.md)

Všechny tyto služby dokáží řešit problémy s integrací a automatizovat obchodní procesy. Všechny můžou definovat vstup, akce, podmínky a výstup. Každou z nich můžete spouštět podle plánu nebo triggerem. Každá služba má ale jedinečné výhody a tento článek vysvětluje rozdíly mezi nimi.

## <a name="compare-microsoft-flow-and-azure-logic-apps"></a>Porovnání služeb Microsoft Flow a Azure Logic Apps

Flow a Logic Apps jsou integrační služby *založené na návrháři*, které umožňují vytvářet pracovní postupy. Obě tyto služby se integrují s různými aplikacemi SaaS a podnikovými aplikacemi. 

Služba Flow je postavená na službě Logic Apps. Obě sdílí stejného návrháře pracovních postupů a stejné [konektory](../connectors/apis-list.md). 

Flow umožňuje každému administrativnímu pracovníkovi provádět jednoduché integrace (například proces schvalování v knihovně dokumentů služby SharePoint) bez nutnosti kontaktovat vývojáře nebo IT oddělení. Logic Apps na druhé straně může povolovat pokročilé integrace (například procesy B2B), kde se vyžadují postupy zabezpečení a DevOps na podnikové úrovni. U obchodních pracovních postupů je typické, že se jejich složitost v průběhu času zvyšuje. Proto můžete nejprve začít s tokem a pak ho podle potřeby převést na aplikaci logiky.

Následující tabulka vám pomůže určit, jestli je pro danou integraci nejlepší Flow nebo Logic Apps.

|  | Tok | Logic Apps |
| --- | --- | --- |
| Uživatelé |Administrativní pracovníci, podnikoví uživatelé, správci služby SharePoint |Profesionální integrátoři a vývojáře, IT profesionálové |
| Scénáře |Samoobslužné |Pokročilé integrace |
| Návrhářský nástroj |V prohlížeči a mobilní aplikaci, pouze uživatelské rozhraní |V prohlížeči a sadě [Visual Studio](../logic-apps/logic-apps-deploy-from-vs.md), k dispozici je [zobrazení kódu](../logic-apps/logic-apps-author-definitions.md) |
| Správa životního cyklu aplikací (ALM) |Návrh a testování v neprodukčním prostředí, přesun do produkčního prostředí až po ověření připravenosti. |DevOps: správa zdrojového kódu, testování, podpora, automatizace a možnosti správy ve [správě prostředků Azure](../logic-apps/logic-apps-create-deploy-azure-resource-manager-templates.md) |
| Prostředí pro správu |Správa zásad prostředí služby Flow a prevence ztráty dat (DLP), sledování licencí [https://admin.flow.microsoft.com](https://admin.flow.microsoft.com) |Správa skupin prostředků, připojení, správy přístupu a protokolování [https://portal.azure.com](https://portal.azure.com) |
| Zabezpečení |Protokoly auditu zabezpečení a dodržování předpisů Office 365, prevence ztráty dat (DLP), [šifrování v klidovém stavu](https://wikipedia.org/wiki/Data_at_rest#Encryption) pro citlivá data atd. |Zajištění zabezpečení Azure: [zabezpečení Azure](https://www.microsoft.com/trustcenter/Security/AzureSecurity), [Security Center](https://azure.microsoft.com/services/security-center/), [protokoly auditu](https://azure.microsoft.com/blog/azure-audit-logs-ux-refresh/) a další. |

## <a name="compare-azure-functions-and-azure-logic-apps"></a>Porovnání služeb Azure Functions a Azure Logic Apps

Functions a Logic Apps jsou služby Azure, které umožňují provádění úloh bez serveru. Azure Functions je služba pro provádění výpočtů bez serveru, zatímco služba Azure Logic Apps umožňuje provádění pracovních postupů bez serveru. Obě služby můžou vytvářet komplexní *orchestrace*. Orchestrace je kolekce funkcí nebo kroků (v Logic Apps se nazývají *akce*), jejichž spouštěním se provede složitý úkol. Při zpracování dávky objednávek můžete například spustit velký počet instancí funkce současně, počkat na dokončení všech instancí a pak spustit funkci, která vypočítá výsledek pro agregaci.

K vývoji orchestrací pro Azure Functions se používá zápis kódu a [rozšíření Durable Functions](durable-functions-overview.md) (ve verzi Preview). K vytváření orchestrací pro Logic Apps se používá grafické rozhraní (GUI) nebo úprava konfiguračních souborů.

Při sestavování orchestrace lze služby kombinovat, takže je možné volat funkce z aplikací logiky a volat aplikace logiky z funkcí. Způsob sestavování orchestrace volte na základě možností těchto služeb nebo podle svých potřeb. Následující tabulka uvádí některé hlavní rozdíly mezi těmito službami:
 
|  | Odolná služba Functions | Logic Apps |
| --- | --- | --- |
| Vývoj | Založeno na kódu (závazné) | Založeno na návrháři (deklarativní) |
| Připojení | [Zhruba tucet předdefinovaných typů vazeb](functions-triggers-bindings.md#supported-bindings), zápis kódu pro vlastní vazby | [Rozsáhlá kolekce konektorů](../connectors/apis-list.md), [Enterprise Integration Pack pro scénáře B2B](../logic-apps/logic-apps-enterprise-integration-overview.md), [sestavení vlastních konektorů](../logic-apps/custom-connector-overview.md) |
| Akce | Každá aktivita je funkce Azure; zápis kódu pro funkce aktivity |[Rozsáhlá kolekce předdefinovaných akcí](../logic-apps/logic-apps-workflow-actions-triggers.md)|
| Monitorování | [Azure Application Insights](../application-insights/app-insights-overview.md) | [Azure Portal](../logic-apps/quickstart-create-first-logic-app-workflow.md), [Operations Management Suite](../logic-apps/logic-apps-monitor-your-logic-apps-oms.md), [Log Analytics](../logic-apps/logic-apps-monitor-your-logic-apps.md)|
| Správa | [REST API](durable-functions-http-api.md), [Visual Studio](https://docs.microsoft.com/azure/vs-azure-tools-resources-managing-with-cloud-explorer) | [Azure Portal](../logic-apps/quickstart-create-first-logic-app-workflow.md), [REST API](https://docs.microsoft.com/rest/api/logic/), [PowerShell](https://docs.microsoft.com/powershell/module/azurerm.logicapp/?view=azurermps-5.6.0), [Visual Studio](https://docs.microsoft.com/azure/logic-apps/manage-logic-apps-with-visual-studio) |
| Kontext spuštění | Může se spustit [místně](functions-runtime-overview.md) nebo v cloudu. | Spouští se jenom v cloudu.|

<a name="function"></a>

## <a name="compare-functions-and-webjobs"></a>Porovnání služeb Functions a WebJobs

Podobně jako Azure Functions je služba Azure App Service WebJobs se sadou WebJobs SDK integrační služba *založená na kódu* a navržená pro vývojáře. Obě jsou postavené na službě [Azure App Service](../app-service/app-service-web-overview.md) a podporují funkce, jako jsou [integrace správy zdrojového kódu](../app-service/app-service-continuous-deployment.md), [ověřování](../app-service/app-service-authentication-overview.md) a [integrace monitorování pomocí Application Insights](functions-monitoring.md).

### <a name="webjobs-and-the-webjobs-sdk"></a>Služba WebJobs a sada WebJobs SDK

Funkce *WebJobs* služby App Service umožňuje spuštění skriptu nebo kódu v kontextu webové aplikace App Service. Sada *WebJobs SDK* je architektura navržená pro službu WebJobs, která zjednodušuje psaní kódu pro reakci na události ve službách Azure. Můžete například reagovat na vytvoření objektu blob obrázku ve službě Azure Storage vytvořením miniatury. Sada WebJobs SDK se spouští jako konzolová aplikace .NET, kterou nasadíte do webové úlohy. 

Služba WebJobs a sada WebJobs SDK fungují nejlépe společně, ale můžete použít i službu WebJobs bez sady WebJobs SDK a naopak. Ve službě WebJobs je možné spustit jakýkoli program nebo skript, který se spouští v sandboxu služby App Service. Konzolovou aplikaci WebJobs SDK je možné spustit kdekoli, kde se konzolová aplikace spouští, například na místních serverech.

### <a name="comparison-table"></a>Srovnávací tabulka

Služba Azure Functions je postavená na sadě WebJobs SDK, takže sdílí řadu stejných aktivačních událostí a připojení k dalším službám Azure. Tady je několik faktorů, které byste měli zvážit při výběru mezi službami Azure Functions a WebJobs se sadou WebJobs SDK:

|  | Funkce | Služba WebJobs se sadou WebJobs SDK |
| --- | --- | --- |
|[Model aplikace bez serveru](https://azure.microsoft.com/overview/serverless-computing/) s [automatickým škálováním](functions-scale.md#how-the-consumption-plan-works)|✔||
|[Vývoj a testování v prohlížeči](functions-create-first-azure-function.md) |✔||
|[Ceny s platbami za použití](functions-scale.md#consumption-plan)|✔||
|[Integrace s Logic Apps](functions-twitter-email.md)|✔||
| Aktivační události |[Časovač](functions-bindings-timer.md)<br>[Fronty a objekty blob služby Azure Storage](functions-bindings-storage-blob.md)<br>[Fronty a témata služby Azure Service Bus](functions-bindings-service-bus.md)<br>[Azure Cosmos DB](functions-bindings-cosmosdb.md)<br>[Azure Event Hubs](functions-bindings-event-hubs.md)<br>[HTTP/Webhook (GitHub, Slack)](functions-bindings-http-webhook.md)<br>[Azure Event Grid](functions-bindings-event-grid.md)|[Časovač](functions-bindings-timer.md)<br>[Fronty a objekty blob služby Azure Storage](functions-bindings-storage-blob.md)<br>[Fronty a témata služby Azure Service Bus](functions-bindings-service-bus.md)<br>[Azure Cosmos DB](functions-bindings-cosmosdb.md)<br>[Azure Event Hubs](functions-bindings-event-hubs.md)<br>[Systém souborů](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions/Extensions/Files/FileTriggerAttribute.cs)|
| Podporované jazyky  |C#<br>F#<br>JavaScript<br>Java (Preview) |Jazyk C#<sup>1</sup>|
|Správci balíčků|NPM a NuGet|NuGet<sup>2</sup>|

<sup>1</sup> Služba WebJobs (bez sady WebJobs SDK) podporuje jazyk C#, JavaScript, Bash, .cmd, .bat, PowerShell, PHP, TypeScript, Python a další. Toto není kompletní seznam. Ve webové úloze je možné spustit jakýkoli program nebo skript, který je možné spustit v sandboxu služby App Service.

<sup>2</sup> Služba WebJobs (bez sady WebJobs SDK) podporuje NPM a NuGet.

### <a name="summary"></a>Souhrn

Služba Azure Functions nabízí větší produktivitu vývojářů, více možností programovacích jazyků, více možností vývojového prostředí, více možností integrace služeb Azure a více cenových možností. Pro většinu scénářů se jedná o nejlepší volbu.

Tady jsou dva scénáře, pro které může být nejlepší volbou služba WebJobs:

* Potřebujete větší kontrolu nad kódem objektu `JobHost`, který naslouchá událostem. Služba Functions nabízí omezené možnosti přizpůsobení chování objektu `JobHost` v souboru [host.json](functions-host-json.md). Někdy potřebujete provádět operace, které není možné zadat řetězcem v souboru JSON. Například vlastní zásadu opakování pro službu Azure Storage umožňuje vytvořit pouze sada WebJobs SDK.
* Máte aplikaci App Service, pro kterou chcete spouštět fragmenty kódu, a chcete je spravovat společně ve stejném prostředí DevOps.

Pro jiné scénáře, kdy chcete spouštět fragmenty kódu pro integraci služeb Azure nebo třetích stran, místo služby WebJobs se sadou WebJobs SDK zvolte Azure Functions.

<a name="together"></a>

## <a name="flow-logic-apps-functions-and-webjobs-together"></a>Společné použití služeb Flow, Logic Apps, Functions a WebJobs

Nemusíte si vybrat pouze jednu z těchto služeb, integrují se mezi sebou i s externími službami.

Tok může volat aplikaci logiky. Aplikace logiky může volat funkci a funkce může volat aplikaci logiky. Viz například [Vytvoření funkce, která se integruje s Azure Logic Apps](functions-twitter-email.md).

Integrace mezi službami Flow, Logic Apps a Functions se v průběhu času neustále zlepšuje. Můžete něco vytvořit v jedné službě a používat to v ostatních službách.

## <a name="next-steps"></a>Další kroky

Začněte vytvořením svého prvního toku, aplikace logiky nebo aplikace funkcí. Klikněte na některý z následujících odkazů:

* [Začínáme s Microsoft Flow](https://flow.microsoft.com/en-us/documentation/getting-started/)
* [Vytvoření aplikace logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md)
* [Vytvoření první funkce Azure](functions-create-first-azure-function.md)

Případně si můžete prostudovat další informace o těchto integračních službách na následujících odkazech:

* [Leveraging Azure Functions & Azure App Service for integration scenarios by Christopher Anderson](http://www.biztalk360.com/integrate-2016-resources/leveraging-azure-functions-azure-app-service-integration-scenarios/) (Využití služeb Azure Functions a Azure App Service pro scénáře integrace, autor: Christopher Anderson)
* [Integrations Made Simple by Charles Lamanna](http://www.biztalk360.com/integrate-2016-resources/integrations-made-simple/) (Zjednodušená integrace, autor: Charlese Lamanna)
* [Živý webcast o Logic Apps](http://aka.ms/logicappslive)
* [Nejčastější dotazy k Microsoft Flow](https://flow.microsoft.com/documentation/frequently-asked-questions/)
