---
title: Možnosti platformy pro integraci a automatizaci v Azure
description: 'Porovnejte cloudové služby Microsoftu, které jsou optimalizované pro úlohy integrace: Power Automate, Logic Apps, Functions a WebJobs.'
ms.topic: overview
ms.date: 04/09/2018
ms.custom: mvc
ms.openlocfilehash: 40293056a73fd88e9ad6b3922aebfe0ba71f07dd
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/08/2020
ms.locfileid: "80878132"
---
# <a name="choose-the-right-integration-and-automation-services-in-azure"></a>Vyberte si správné služby integrace a automatizace v Azure

Tento článek porovnává tyto cloudové služby společnosti Microsoft:

* [Microsoft Power Automate](https://flow.microsoft.com/) (byl Microsoft Flow)
* [Azure Logic Apps](https://azure.microsoft.com/services/logic-apps/)
* [Azure Functions](https://azure.microsoft.com/services/functions/)
* [Azure App Service WebJobs](../app-service/webjobs-create.md)

Všechny tyto služby dokáží řešit problémy s integrací a automatizovat obchodní procesy. Všechny můžou definovat vstup, akce, podmínky a výstup. Každou z nich můžete spouštět podle plánu nebo triggerem. Každá služba má jedinečné výhody a tento článek vysvětluje rozdíly. 

Pokud hledáte obecnější porovnání mezi funkcemi Azure a dalšími možnostmi výpočetních prostředků Azure, přečtěte [si část Kritéria pro výběr výpočetní služby Azure](/azure/architecture/guide/technology-choices/compute-comparison) a Výběr výpočetní [možnosti Azure pro mikroslužby](/azure/architecture/microservices/design/compute-options).

## <a name="compare-microsoft-power-automate-and-azure-logic-apps"></a>Porovnání microsoft power automatů a logických aplikací Azure

Power Automate a Logic Apps jsou obě integrační služby *pro návrháře,* které můžou vytvářet pracovní postupy. Obě tyto služby se integrují s různými aplikacemi SaaS a podnikovými aplikacemi. 

Power Automate je postaven na logic apps. Sdílejí stejný návrhář pracovního postupu a stejné [konektory](../connectors/apis-list.md). 

Power Automate umožňuje každému kancelářskému pracovníkovi provádět jednoduché integrace (například schvalovací proces v knihovně dokumentů SharePointu), aniž by museli procházet vývojáři nebo IT. Logic Apps můžete také povolit pokročilé integrace (například B2B procesy), kde jsou vyžadovány azure devops na podnikové úrovni a postupy zabezpečení. U obchodních pracovních postupů je typické, že se jejich složitost v průběhu času zvyšuje. V souladu s tím můžete nejprve začít s tokem a pak jej převést do aplikace logiky podle potřeby.

Následující tabulka vám pomůže určit, jestli je power automat nebo aplikace logiky nejlepší pro konkrétní integraci:

|  | Power Automate | Logic Apps |
| --- | --- | --- |
| **Uživatelé** |Administrativní pracovníci, podnikoví uživatelé, správci služby SharePoint |Profesionální integrátoři a vývojáře, IT profesionálové |
| **Scénáře** |Samoobslužné |Pokročilé integrace |
| **Návrhový nástroj** |V prohlížeči a mobilní aplikaci, pouze uživatelské rozhraní |V prohlížeči a sadě [Visual Studio](../logic-apps/logic-apps-deploy-from-vs.md), k dispozici je [zobrazení kódu](../logic-apps/logic-apps-author-definitions.md) |
| **Správa životního cyklu aplikací (ALM)** |Navrhujte a otestujte v neprodukčním prostředí, propagujte výrobu, až budete připraveni |Azure DevOps: správa zdrojového kódu, testování, podpora, automatizace a možnosti správy ve [Správci prostředků Azure](../logic-apps/logic-apps-create-deploy-azure-resource-manager-templates.md) |
| **Prostředí pro správu** |Správa prostředí power automatu a zásady ochrany před únikem informací (DLP), sledování licencování: [Centrum pro správu](https://admin.flow.microsoft.com) |Správa skupin prostředků, připojení, správa přístupu a protokolování: [Portál Azure](https://portal.azure.com) |
| **Zabezpečení** |Protokoly auditu zabezpečení a dodržování předpisů office 365, ochrana před únikem, [šifrování v klidovém stavu](https://wikipedia.org/wiki/Data_at_rest#Encryption) pro citlivá data |Zabezpečení Azure: [Azure security](https://www.microsoft.com/en-us/trustcenter/Security/AzureSecurity), [Azure Security Center](https://azure.microsoft.com/services/security-center/), [protokoly auditování](https://azure.microsoft.com/blog/azure-audit-logs-ux-refresh/) |

## <a name="compare-azure-functions-and-azure-logic-apps"></a>Porovnání služeb Azure Functions a Azure Logic Apps

Functions a Logic Apps jsou služby Azure, které umožňují provádění úloh bez serveru. Azure Functions je výpočetní služba bez serveru, zatímco Azure Logic Apps poskytuje pracovní postupy bez serveru. Oba mohou vytvářet složité *orchestrace*. Orchestrace je kolekce funkcí nebo kroků (v Logic Apps se nazývají *akce*), jejichž spouštěním se provede složitý úkol. Chcete-li například zpracovat dávku objednávek, můžete souběžně spustit mnoho instancí funkce, počkat na dokončení všech instancí a potom spustit funkci, která vypočítá výsledek na agregaci.

K vývoji orchestrací pro Azure Functions se používá zápis kódu a [rozšíření Durable Functions](durable/durable-functions-overview.md). K vytváření orchestrací pro Logic Apps se používá grafické rozhraní (GUI) nebo úprava konfiguračních souborů.

Při sestavování orchestrace lze služby kombinovat, takže je možné volat funkce z aplikací logiky a volat aplikace logiky z funkcí. Způsob sestavování orchestrace volte na základě možností těchto služeb nebo podle svých potřeb. V následující tabulce jsou uvedeny některé klíčové rozdíly mezi těmito:

|  | Odolná služba Functions | Logic Apps |
| --- | --- | --- |
| **Vývoj** | Založeno na kódu (závazné) | Založeno na návrháři (deklarativní) |
| **Připojení** | [Zhruba tucet předdefinovaných typů vazeb](functions-triggers-bindings.md#supported-bindings), zápis kódu pro vlastní vazby | [Rozsáhlá kolekce konektorů](../connectors/apis-list.md), [Enterprise Integration Pack pro scénáře B2B](../logic-apps/logic-apps-enterprise-integration-overview.md), [sestavení vlastních konektorů](../logic-apps/custom-connector-overview.md) |
| **Akce** | Každá aktivita je funkce Azure; zápis kódu pro funkce aktivity |[Rozsáhlá kolekce předdefinovaných akcí](../logic-apps/logic-apps-workflow-actions-triggers.md)|
| **Monitorování** | [Azure Application Insights](../azure-monitor/app/app-insights-overview.md) | [Portál Azure](../logic-apps/quickstart-create-first-logic-app-workflow.md), [protokoly Azure Monitor](../logic-apps/logic-apps-monitor-your-logic-apps.md)|
| **správy** | [REST API](durable/durable-functions-http-api.md), [Visual Studio](https://docs.microsoft.com/azure/vs-azure-tools-resources-managing-with-cloud-explorer) | [Azure Portal](../logic-apps/quickstart-create-first-logic-app-workflow.md), [REST API](https://docs.microsoft.com/rest/api/logic/), [PowerShell](https://docs.microsoft.com/powershell/module/az.logicapp), [Visual Studio](https://docs.microsoft.com/azure/logic-apps/manage-logic-apps-with-visual-studio) |
| **Kontext spuštění** | Lze spustit [místně](functions-runtime-overview.md) nebo v cloudu | Spouští se jenom v cloudu.|

<a name="function"></a>

## <a name="compare-functions-and-webjobs"></a>Porovnání služeb Functions a WebJobs

Podobně jako Azure Functions je služba Azure App Service WebJobs se sadou WebJobs SDK integrační služba *založená na kódu* a navržená pro vývojáře. Obě jsou postavené na službě [Azure App Service](../app-service/overview.md) a podporují funkce, jako jsou [integrace správy zdrojového kódu](../app-service/deploy-continuous-deployment.md), [ověřování](../app-service/overview-authentication-authorization.md) a [integrace monitorování pomocí Application Insights](functions-monitoring.md).

### <a name="webjobs-and-the-webjobs-sdk"></a>Služba WebJobs a sada WebJobs SDK

Pomocí funkce *WebJobs* služby App Service můžete spustit skript nebo kód v kontextu webové aplikace App Service. Sada *WebJobs SDK* je architektura navržená pro službu WebJobs, která zjednodušuje psaní kódu pro reakci na události ve službách Azure. Například můžete reagovat na vytvoření objektu blob image ve službě Azure Storage vytvořením miniatury. Sada WebJobs SDK se spouští jako konzolová aplikace .NET, kterou nasadíte do webové úlohy. 

Služba WebJobs a sada WebJobs SDK fungují nejlépe společně, ale můžete použít i službu WebJobs bez sady WebJobs SDK a naopak. Ve službě WebJobs je možné spustit jakýkoli program nebo skript, který se spouští v sandboxu služby App Service. Konzolovou aplikaci WebJobs SDK je možné spustit kdekoli, kde se konzolová aplikace spouští, například na místních serverech.

### <a name="comparison-table"></a>Srovnávací tabulka

Služba Azure Functions je postavená na sadě WebJobs SDK, takže sdílí řadu stejných aktivačních událostí a připojení k dalším službám Azure. Tady jsou některé faktory, které je třeba zvážit, když si vybíráte mezi funkcemi Azure a WebJobs pomocí sady WebJobs SDK:

|  | Functions | Služba WebJobs se sadou WebJobs SDK |
| --- | --- | --- |
|[Model aplikace bez serveru](https://azure.microsoft.com/solutions/serverless/) s [automatickým škálováním](functions-scale.md#how-the-consumption-and-premium-plans-work)|✔||
|[Vývoj a testování v prohlížeči](functions-create-first-azure-function.md) |✔||
|[Ceny s platbami za použití](functions-scale.md#consumption-plan)|✔||
|[Integrace s Logic Apps](functions-twitter-email.md)|✔||
| Aktivační události |[Časovač](functions-bindings-timer.md)<br>[Fronty a objekty blob služby Azure Storage](functions-bindings-storage-blob.md)<br>[Fronty a témata služby Azure Service Bus](functions-bindings-service-bus.md)<br>[Azure Cosmos DB](functions-bindings-cosmosdb.md)<br>[Azure Event Hubs](functions-bindings-event-hubs.md)<br>[HTTP/Webhook (GitHub, Slack)](functions-bindings-http-webhook.md)<br>[Azure Event Grid](functions-bindings-event-grid.md)|[Časovač](functions-bindings-timer.md)<br>[Fronty a objekty blob služby Azure Storage](functions-bindings-storage-blob.md)<br>[Fronty a témata služby Azure Service Bus](functions-bindings-service-bus.md)<br>[Azure Cosmos DB](functions-bindings-cosmosdb.md)<br>[Azure Event Hubs](functions-bindings-event-hubs.md)<br>[Systém souborů](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions/Extensions/Files/FileTriggerAttribute.cs)|
| Podporované jazyky  |C#<br>F#<br>JavaScript<br>Java<br>Python<br>PowerShell |Jazyk C#<sup>1</sup>|
|Správci balíčků|NPM a NuGet|NuGet<sup>2</sup>|

<sup>1</sup> WebJobs (bez WebJobs SDK) podporuje C#, Java, JavaScript, Bash, .cmd, .bat, PowerShell, PHP, TypeScript, Python a další. Nejedná se o úplný seznam. Ve webové úloze je možné spustit jakýkoli program nebo skript, který je možné spustit v sandboxu služby App Service.

<sup>2</sup> WebJobs (bez WebJobs SDK) podporuje NPM a NuGet.

### <a name="summary"></a>Souhrn

Azure Functions nabízí větší produktivitu vývojářů než Azure App Service WebJobs. Nabízí také další možnosti pro programovací jazyky, vývojová prostředí, integraci služeb Azure a ceny. Pro většinu scénářů se jedná o nejlepší volbu.

Tady jsou dva scénáře, pro které může být nejlepší volbou služba WebJobs:

* Potřebujete větší kontrolu nad kódem objektu `JobHost`, který naslouchá událostem. Služba Functions nabízí omezené možnosti přizpůsobení chování objektu `JobHost` v souboru [host.json](functions-host-json.md). Někdy potřebujete provádět operace, které není možné zadat řetězcem v souboru JSON. Například vlastní zásadu opakování pro službu Azure Storage umožňuje vytvořit pouze sada WebJobs SDK.
* Máte aplikaci služby App Service, pro kterou chcete spustit fragmenty kódu a chcete je spravovat společně ve stejném prostředí Azure DevOps.

Pro jiné scénáře, kdy chcete spouštět fragmenty kódu pro integraci služeb Azure nebo třetích stran, místo služby WebJobs se sadou WebJobs SDK zvolte Azure Functions.

<a name="together"></a>

## <a name="power-automate-logic-apps-functions-and-webjobs-together"></a>Power Automate, logic apps, funkce a webjobs společně

Nemusíte si vybrat pouze jednu z těchto služeb. Vzájemně se integrují stejně jako externí služby.

Tok může volat aplikaci logiky. Aplikace logiky může volat funkci a funkce může volat aplikaci logiky. Viz například [Vytvoření funkce, která se integruje s Azure Logic Apps](functions-twitter-email.md).

Integrace mezi Power Automate, Logic Apps a funkcemi se v průběhu času neustále zlepšuje. Můžete něco vytvořit v jedné službě a používat to v ostatních službách.

Další informace o integračních službách můžete získat pomocí následujících odkazů:

* [Leveraging Azure Functions & Azure App Service for integration scenarios by Christopher Anderson](https://www.biztalk360.com/integrate-2016-resources/leveraging-azure-functions-azure-app-service-integration-scenarios/) (Využití služeb Azure Functions a Azure App Service pro scénáře integrace, autor: Christopher Anderson)
* [Integrations Made Simple by Charles Lamanna](https://www.biztalk360.com/integrate-2016-resources/integrations-made-simple/) (Zjednodušená integrace, autor: Charlese Lamanna)
* [Živé webové vysílání aplikací Logika](https://aka.ms/logicappslive)
* [Nejčastější dotazy týkající se power automatů](/power-automate/frequently-asked-questions)

## <a name="next-steps"></a>Další kroky

Začněte vytvořením svého prvního toku, aplikace logiky nebo aplikace funkcí. Vyberte některý z následujících odkazů:

* [Začínáme s Power Automate](/power-automate/getting-started)
* [Vytvoření aplikace logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md)
* [Vytvoření první funkce Azure](functions-create-first-azure-function.md)
