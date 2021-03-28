---
title: Možnosti platformy pro integraci a automatizaci v Azure
description: 'Porovnání cloudových služeb Microsoftu, které jsou optimalizované pro úlohy integrace: automatizace, Logic Apps, funkce a WebJobs.'
ms.topic: overview
ms.date: 04/09/2018
ms.custom: mvc
ms.openlocfilehash: e6bd97469a6b7b6c42427e130ac103954377c652
ms.sourcegitcommit: c8b50a8aa8d9596ee3d4f3905bde94c984fc8aa2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2021
ms.locfileid: "105643369"
---
# <a name="choose-the-right-integration-and-automation-services-in-azure"></a>Volba správných služeb integrace a automatizace v Azure

Tento článek porovnává tyto cloudové služby společnosti Microsoft:

* [Microsoft Power automatizuje](https://flow.microsoft.com/) (was Microsoft flow)
* [Azure Logic Apps](https://azure.microsoft.com/services/logic-apps/)
* [Azure Functions](https://azure.microsoft.com/services/functions/)
* [Azure App Service WebJobs](../app-service/webjobs-create.md)

Všechny tyto služby dokáží řešit problémy s integrací a automatizovat obchodní procesy. Všechny můžou definovat vstup, akce, podmínky a výstup. Každou z nich můžete spouštět podle plánu nebo triggerem. Každá služba má jedinečné výhody a tento článek vysvětluje rozdíly. 

Pokud hledáte obecnější srovnání mezi Azure Functions a dalšími možnostmi služby Azure COMPUTE, přečtěte si téma [kritéria pro výběr služby COMPUTE Azure](/azure/architecture/guide/technology-choices/compute-comparison) a [Výběr možnosti Azure COMPUTE pro mikroslužby](/azure/architecture/microservices/design/compute-options).

## <a name="compare-microsoft-power-automate-and-azure-logic-apps"></a>Porovnání Microsoft Power automatizuje a Azure Logic Apps

Automatizace a Logic Apps jsou první integrační služby pro *vývojáře* , které můžou vytvářet pracovní postupy. Obě tyto služby se integrují s různými aplikacemi SaaS a podnikovými aplikacemi. 

Automatické automatizace je postavená na Logic Apps. Sdílejí stejného návrháře pracovních postupů a stejné [konektory](../connectors/apis-list.md). 

Power Automate umožňuje libovolnému pracovníkovi Office provádět jednoduché integrace (například proces schvalování v knihovně dokumentů služby SharePoint) bez nutnosti procházet vývojáře nebo IT oddělení. Logic Apps taky můžou povolit pokročilou integraci (například procesy B2B), kde se vyžadují postupy zabezpečení Azure na podnikové úrovni a DevOps. U obchodních pracovních postupů je typické, že se jejich složitost v průběhu času zvyšuje. Proto můžete začít s tokem v prvním a pak ho převést na aplikaci logiky podle potřeby.

Následující tabulka vám pomůže určit, jestli je pro určitou integraci nejvhodnější automatizace nebo Logic Apps:

|  | Power Automate | Logic Apps |
| --- | --- | --- |
| **Uživatelé** |Administrativní pracovníci, podnikoví uživatelé, správci služby SharePoint |Profesionální integrátoři a vývojáře, IT profesionálové |
| **Scénáře** |Samoobsluha |Pokročilé integrace |
| **Nástroj pro návrh** |V prohlížeči a mobilní aplikaci, pouze uživatelské rozhraní |V prohlížeči a sadě [Visual Studio](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md), k dispozici je [zobrazení kódu](../logic-apps/logic-apps-author-definitions.md) |
| **Správa životního cyklu aplikací (ALM)** |Návrh a testování v neprodukčních prostředích po připraveném zvýšení úrovně na produkční |Azure DevOps: Správa zdrojového kódu, testování, podpora, automatizace a možnosti správy v [Azure Resource Manager](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md) |
| **Prostředí pro správu** |Správa zásad automatizace prostředí a ochrany před únikem informací, sledování licencování: [Centrum pro správu](https://admin.flow.microsoft.com) |Správa skupin prostředků, připojení, správy přístupu a protokolování: [Azure Portal](https://portal.azure.com) |
| **Zabezpečení** |Microsoft 365 protokoly auditu zabezpečení, DLP, [šifrování v klidovém umístění](https://wikipedia.org/wiki/Data_at_rest#Encryption) pro citlivá data |Security Assurance of Azure: [zabezpečení Azure](https://www.microsoft.com/en-us/trustcenter/Security/AzureSecurity), [Azure Security Center](https://azure.microsoft.com/services/security-center/), [protokoly auditu](https://azure.microsoft.com/blog/azure-audit-logs-ux-refresh/) |

## <a name="compare-azure-functions-and-azure-logic-apps"></a>Porovnání služeb Azure Functions a Azure Logic Apps

Functions a Logic Apps jsou služby Azure, které umožňují provádění úloh bez serveru. Azure Functions je služba COMPUTE bez serveru, zatímco Azure Logic Apps poskytuje pracovní postupy bez serveru. Oba můžou vytvářet složitá *orchestrace*. Orchestrace je kolekce funkcí nebo kroků (v Logic Apps se nazývají *akce*), jejichž spouštěním se provede složitý úkol. Například pro zpracování dávky objednávek můžete spustit mnoho instancí funkce paralelně, počkat na dokončení všech instancí a potom spustit funkci, která vypočítá výsledek agregace.

K vývoji orchestrací pro Azure Functions se používá zápis kódu a [rozšíření Durable Functions](durable/durable-functions-overview.md). K vytváření orchestrací pro Logic Apps se používá grafické rozhraní (GUI) nebo úprava konfiguračních souborů.

Při sestavování orchestrace lze služby kombinovat, takže je možné volat funkce z aplikací logiky a volat aplikace logiky z funkcí. Způsob sestavování orchestrace volte na základě možností těchto služeb nebo podle svých potřeb. Následující tabulka uvádí některé z klíčových rozdílů mezi těmito:

|  | Odolná služba Functions | Logic Apps |
| --- | --- | --- |
| **Vývoj** | Založeno na kódu (imperativní) | Založeno na návrháři (deklarativní) |
| **Připojení** | [O tucet vestavěných typů vazeb](functions-triggers-bindings.md#supported-bindings), psaní kódu pro vlastní vazby | [Velká kolekce konektorů](../connectors/apis-list.md), [Enterprise Integration Pack pro scénáře B2B](../logic-apps/logic-apps-enterprise-integration-overview.md), [sestavení vlastních konektorů](../logic-apps/custom-connector-overview.md) |
| **Akce** | Každá aktivita je funkce Azure; zápis kódu pro funkce aktivity |[Rozsáhlá kolekce předdefinovaných akcí](../logic-apps/logic-apps-workflow-actions-triggers.md)|
| **Monitorování** | [Application Insights Azure](../azure-monitor/app/app-insights-overview.md) | [Azure Portal](../logic-apps/quickstart-create-first-logic-app-workflow.md), [protokoly Azure monitor](../logic-apps/monitor-logic-apps.md)|
| **správy** | [REST API](durable/durable-functions-http-api.md), [Visual Studio](/visualstudio/azure/vs-azure-tools-resources-managing-with-cloud-explorer) | [Azure Portal](../logic-apps/quickstart-create-first-logic-app-workflow.md), [REST API](/rest/api/logic/), [PowerShell](/powershell/module/az.logicapp), [Visual Studio](../logic-apps/manage-logic-apps-with-visual-studio.md) |
| **Kontext spuštění** | Může běžet [místně](functions-runtime-overview.md) nebo v cloudu. | Spouští se jenom v cloudu.|

<a name="function"></a>

## <a name="compare-functions-and-webjobs"></a>Porovnání služeb Functions a WebJobs

Podobně jako Azure Functions je služba Azure App Service WebJobs se sadou WebJobs SDK integrační služba *založená na kódu* a navržená pro vývojáře. Obě jsou postavené na službě [Azure App Service](../app-service/overview.md) a podporují funkce, jako jsou [integrace správy zdrojového kódu](../app-service/deploy-continuous-deployment.md), [ověřování](../app-service/overview-authentication-authorization.md) a [integrace monitorování pomocí Application Insights](functions-monitoring.md).

### <a name="webjobs-and-the-webjobs-sdk"></a>Služba WebJobs a sada WebJobs SDK

Funkci *WebJobs* App Service můžete použít ke spuštění skriptu nebo kódu v kontextu webové aplikace App Service. Sada *WebJobs SDK* je architektura navržená pro službu WebJobs, která zjednodušuje psaní kódu pro reakci na události ve službách Azure. Například můžete reagovat na vytvoření objektu BLOB obrázku v Azure Storage vytvořením obrázku miniatury. Sada WebJobs SDK se spouští jako konzolová aplikace .NET, kterou nasadíte do webové úlohy. 

Služba WebJobs a sada WebJobs SDK fungují nejlépe společně, ale můžete použít i službu WebJobs bez sady WebJobs SDK a naopak. Ve službě WebJobs je možné spustit jakýkoli program nebo skript, který se spouští v sandboxu služby App Service. Konzolovou aplikaci WebJobs SDK je možné spustit kdekoli, kde se konzolová aplikace spouští, například na místních serverech.

### <a name="comparison-table"></a>Srovnávací tabulka

Služba Azure Functions je postavená na sadě WebJobs SDK, takže sdílí řadu stejných aktivačních událostí a připojení k dalším službám Azure. Tady je několik faktorů, které je potřeba vzít v úvahu při výběru mezi Azure Functions a WebJobs se sadou WebJobs SDK:

|  | Functions | Služba WebJobs se sadou WebJobs SDK |
| --- | --- | --- |
|**[Model aplikace bez serveru](https://azure.microsoft.com/solutions/serverless/) s [automatickým škálováním](event-driven-scaling.md)**|✔||
|**[Vývoj a testování v prohlížeči](./functions-get-started.md)** |✔||
|**[Ceny s platbami za použití](consumption-plan.md)**|✔||
|**[Integrace s Logic Apps](functions-twitter-email.md)**|✔||
| **Aktivační události** |[Časovač](functions-bindings-timer.md)<br>[Fronty a objekty blob služby Azure Storage](functions-bindings-storage-blob.md)<br>[Fronty a témata služby Azure Service Bus](functions-bindings-service-bus.md)<br>[Azure Cosmos DB](functions-bindings-cosmosdb.md)<br>[Azure Event Hubs](functions-bindings-event-hubs.md)<br>[HTTP/Webhook (GitHub, Slack)](functions-bindings-http-webhook.md)<br>[Azure Event Grid](functions-bindings-event-grid.md)|[Časovač](functions-bindings-timer.md)<br>[Fronty a objekty blob služby Azure Storage](functions-bindings-storage-blob.md)<br>[Fronty a témata služby Azure Service Bus](functions-bindings-service-bus.md)<br>[Azure Cosmos DB](functions-bindings-cosmosdb.md)<br>[Azure Event Hubs](functions-bindings-event-hubs.md)<br>[Systém souborů](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions/Extensions/Files/FileTriggerAttribute.cs)|
| **Podporované jazyky**  |C#<br>F#<br>JavaScript<br>Java<br>Python<br>PowerShell |C#<sup>1</sup>|
|**Správci balíčků**|NPM a NuGet|NuGet<sup>2</sup>|

<sup>1</sup> WebJobs (bez sady WebJobs SDK) podporuje jazyky C#, Java, JavaScript, bash,. cmd,. bat, POWERSHELL, php, TypeScript, Python a další. Nejedná se o vyčerpávající seznam. Ve webové úloze je možné spustit jakýkoli program nebo skript, který je možné spustit v sandboxu služby App Service.

<sup>2</sup> webové úlohy (bez sady WebJobs SDK) podporují npm a NuGet.

### <a name="summary"></a>Souhrn

Azure Functions nabízí větší produktivitu vývojářů než Azure App Service WebJobs. Nabízí také více možností pro programovací jazyky, vývojová prostředí, integraci služeb Azure a ceny. Pro většinu scénářů se jedná o nejlepší volbu.

Tady jsou dva scénáře, pro které může být nejlepší volbou služba WebJobs:

* Potřebujete větší kontrolu nad kódem objektu `JobHost`, který naslouchá událostem. Služba Functions nabízí omezené možnosti přizpůsobení chování objektu `JobHost` v souboru [host.json](functions-host-json.md). Někdy potřebujete provádět operace, které není možné zadat řetězcem v souboru JSON. Například vlastní zásadu opakování pro službu Azure Storage umožňuje vytvořit pouze sada WebJobs SDK.
* Máte aplikaci App Service, pro kterou chcete spouštět fragmenty kódu, a chcete je spravovat společně ve stejném prostředí Azure DevOps.

Pro jiné scénáře, kdy chcete spouštět fragmenty kódu pro integraci služeb Azure nebo třetích stran, místo služby WebJobs se sadou WebJobs SDK zvolte Azure Functions.

<a name="together"></a>

## <a name="power-automate-logic-apps-functions-and-webjobs-together"></a>Automatizace, Logic Apps, funkce a WebJobs společně

Nemusíte volit jenom jednu z těchto služeb. Vzájemně se integrují i s externími službami.

Tok může volat aplikaci logiky. Aplikace logiky může volat funkci a funkce může volat aplikaci logiky. Viz například [Vytvoření funkce, která se integruje s Azure Logic Apps](functions-twitter-email.md).

Integrace mezi Power Automate, Logic Apps a funkcemi se v průběhu času neustále vylepšuje. Můžete něco vytvořit v jedné službě a používat to v ostatních službách.

Další informace o integračních službách můžete získat pomocí následujících odkazů:

* [Leveraging Azure Functions & Azure App Service for integration scenarios by Christopher Anderson](https://www.biztalk360.com/integrate-2016-resources/leveraging-azure-functions-azure-app-service-integration-scenarios/) (Využití služeb Azure Functions a Azure App Service pro scénáře integrace, autor: Christopher Anderson)
* [Integrations Made Simple by Charles Lamanna](https://www.biztalk360.com/integrate-2016-resources/integrations-made-simple/) (Zjednodušená integrace, autor: Charlese Lamanna)
* [Logic Apps živým webcast](https://aka.ms/logicappslive)
* [Automatizace nejčastějších dotazů](/power-automate/frequently-asked-questions)

## <a name="next-steps"></a>Další kroky

Začněte vytvořením svého prvního toku, aplikace logiky nebo aplikace funkcí. Vyberte některý z následujících odkazů:

* [Začínáme s Power Automate](/power-automate/getting-started)
* [Vytvoření aplikace logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md)
* [Vytvoření první funkce Azure Functions](./functions-get-started.md)