---
title: Monitorování Azure Functions
description: Naučte se používat Azure Application Insights s Azure Functions k monitorování provádění funkcí.
ms.assetid: 501722c3-f2f7-4224-a220-6d59da08a320
ms.topic: conceptual
ms.date: 10/14/2020
ms.custom: devx-track-csharp, fasttrack-edit, contperf-fy21q2, devx-track-js
ms.openlocfilehash: 7dbaa8712e09de9084e2bcb66d43f2181af292a0
ms.sourcegitcommit: 3ea45bbda81be0a869274353e7f6a99e4b83afe2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/10/2020
ms.locfileid: "97033030"
---
# <a name="monitor-azure-functions"></a>Monitorování Azure Functions

[Azure Functions](functions-overview.md) nabízí integrovanou integraci s [Azure Application Insights](../azure-monitor/app/app-insights-overview.md) k monitorování funkcí. Tento článek poskytuje přehled možností monitorování poskytovaných službou Azure pro monitorování Azure Functions.

Application Insights shromažďuje údaje o protokolech, výkonu a chybách. Díky automatické detekci anomálií výkonu a k účinným analytickým nástrojům můžete snadněji diagnostikovat problémy a lépe pochopit, jak se funkce používají. Tyto nástroje jsou navržené tak, aby vám pomohly průběžně zlepšovat výkon a použitelnost vašich funkcí. Můžete dokonce použít Application Insights během vývoje projektu místní funkce aplikace Function App. Další informace najdete v tématu [co je Application Insights?](../azure-monitor/app/app-insights-overview.md).

Protože Application Insights instrumentace je integrovaná do Azure Functions, potřebujete platný klíč instrumentace pro připojení aplikace Function App k prostředku Application Insights. Klíč instrumentace se přidá do nastavení aplikace při vytváření prostředku Function App v Azure. Pokud vaše aplikace Function ještě tento klíč nemá, můžete [ji nastavit ručně](configure-monitoring.md#enable-application-insights-integration).  

## <a name="application-insights-pricing-and-limits"></a>Application Insights ceny a omezení

Můžete si vyzkoušet Application Insights integraci s Azure Functions zdarma a získáte denní limit na to, kolik dat se zpracovává zdarma.

Pokud povolíte Application Insights během vývoje, můžete během testování dosáhnout tohoto limitu. Azure poskytuje přístup k portálu a e-mailovým oznámením při přístupu k dennímu limitu. Pokud jste tyto výstrahy nepřišli a dosáhli jste limitu, nové protokoly se v Application Insightsch dotazech nezobrazují. Nezapomeňte omezit omezení, aby nedocházelo k zbytečnému času řešení potíží. Další informace najdete v tématu [Správa cen a objemu dat v Application Insights](../azure-monitor/app/pricing.md).

> [!IMPORTANT]
> Application Insights má funkci [vzorkování](../azure-monitor/app/sampling.md) , která vám může chránit při vytváření příliš velkého množství dat telemetrie při dokončeném provádění v době špičky zatížení. Vzorkování je ve výchozím nastavení povolené. Pokud se zdá, že chybí data, možná budete muset upravit nastavení vzorkování tak, aby vyhovovalo vašemu konkrétnímu scénáři monitorování. Další informace najdete v tématu [Konfigurace vzorkování](configure-monitoring.md#configure-sampling).

Úplný seznam funkcí Application Insights, které jsou k dispozici pro aplikaci Function App, je podrobně popsán v [Application Insights pro Azure Functions podporované funkce](../azure-monitor/app/azure-functions-supported-features.md).

## <a name="application-insights-integration"></a>Integrace Application Insights

Při vytváření aplikace Function App se obvykle vytváří instance Application Insights. V takovém případě je klíč instrumentace vyžadovaný pro integraci již nastaven jako nastavení aplikace s názvem *APPINSIGHTS_INSTRUMENTATIONKEY*. Pokud z nějakého důvodu nemá vaše aplikace Functions nastaven klíč instrumentace, je potřeba [Povolit integraci Application Insights](configure-monitoring.md#enable-application-insights-integration).  

## <a name="collecting-telemetry-data"></a>Shromažďování dat telemetrie

S povolenou integrací Application Insights se data telemetrie odesílají do vaší připojené instance Application Insights. Tato data zahrnují protokoly generované hostitelem Functions, trasování napsané z kódu vašich funkcí a data o výkonu. 

>[!NOTE]
>Kromě dat z vašich funkcí a hostitele funkcí můžete také shromažďovat data z [kontroleru škálování funkcí](#scale-controller-logs).   

### <a name="log-levels-and-categories"></a>Úrovně protokolu a kategorie

Při psaní trasování z kódu aplikace byste měli k trasování přiřadit úroveň protokolu. Úrovně protokolu poskytují způsob, jak omezit množství dat shromažďovaných z vašich trasování.  

[!INCLUDE [functions-log-levels](../../includes/functions-log-levels.md)]

Další informace o úrovních protokolu najdete v tématu [Konfigurace úrovní protokolu](configure-monitoring.md#configure-log-levels).

Přiřazením protokolovaných položek do kategorie získáte větší kontrolu nad telemetrie vygenerovanou z konkrétních zdrojů ve vaší aplikaci Function App. Kategorie usnadňují spouštění analýz prostřednictvím shromážděných dat. Trasování vytvořená z kódu funkce jsou přiřazena jednotlivým kategoriím na základě názvu funkce. Další informace o kategoriích naleznete v tématu [Configure Categories](configure-monitoring.md#configure-categories).

### <a name="custom-telemetry-data"></a>Vlastní data telemetrie

V [jazyce C#](functions-dotnet-class-library.md#log-custom-telemetry-in-c-functions) a [JavaScriptu](functions-reference-node.md#log-custom-telemetry)můžete k psaní vlastních dat telemetrie použít sadu Application Insights SDK.

### <a name="dependencies"></a>Závislosti

Od verze 2. x funkce modul runtime automaticky shromažďuje data na závislosti pro vazby, které používají určité sady SDK klienta. Application Insights shromažďuje data na následujících závislostech:

+ Azure Cosmos DB 
+ Azure Event Hubs
+ Azure Service Bus
+ Služba Azure Storage Services (objekt blob, fronta a tabulka)

Jsou také zachyceny požadavky HTTP a volání databáze pomocí aplikace `SqlClient` . Úplný seznam závislostí podporovaných nástrojem Application Insights najdete v tématu [Automatické sledované závislosti](../azure-monitor/app/asp-net-dependencies.md#automatically-tracked-dependencies).

Application Insights generuje _mapu aplikace_ pro shromážděná data závislostí. V následujícím příkladu je mapa aplikace funkce triggeru HTTP s výstupní vazbou úložiště Queue.  

![Mapa aplikace se závislostí](./media/functions-monitoring/app-map.png)

Závislosti jsou zapisovány na `Information` úrovni. Pokud filtrujete v `Warning` nebo výše, neuvidíte data závislostí. Automatická shromažďování závislostí se také provádí v oboru bez uživatele. Chcete-li zachytit data závislostí, ujistěte se, že je úroveň nastavena alespoň `Information` mimo rozsah uživatele ( `Function.<YOUR_FUNCTION_NAME>.User` ) na hostiteli.

Kromě automatického shromažďování dat závislostí můžete také použít jednu z Application Insights sad SDK pro konkrétní jazyk k zápisu vlastních informací o závislostech do protokolů. Příklad, jak napsat vlastní závislosti, najdete v jednom z následujících příkladů pro konkrétní jazyk:

+ [Protokolování vlastní telemetrie ve funkcích jazyka C#](functions-dotnet-class-library.md#log-custom-telemetry-in-c-functions)
+ [Protokolování vlastní telemetrie ve funkcích JavaScriptu](functions-reference-node.md#log-custom-telemetry) 

## <a name="writing-to-logs"></a>Zápis do protokolů 

Způsob psaní do protokolů a používaných rozhraní API závisí na jazyku projektu Function App.   
Další informace o zápisu protokolů z vašich funkcí najdete v příručce pro vývojáře.

+ [C# (knihovna tříd .NET)](functions-dotnet-class-library.md#logging)
+ [Java](functions-reference-java.md#logger)
+ [JavaScript](functions-reference-node.md#write-trace-output-to-logs) 
+ [PowerShell](functions-reference-powershell.md#logging)
+ [Python](functions-reference-python.md#logging)

## <a name="streaming-logs"></a>Protokoly streamování

Při vývoji aplikace často chcete zjistit, co se do protokolů zapisuje téměř v reálném čase, když běží v Azure.

Existují dva způsoby, jak zobrazit datový proud dat protokolu generovaných spuštěním vaší funkce.

* **Integrované streamování protokolů**: platforma App Service umožňuje zobrazit datový proud souborů protokolu aplikace. Tento datový proud je stejný jako výstup, který se zobrazuje při ladění funkcí během [místního vývoje](functions-develop-local.md) a při použití karty **test** na portálu. Zobrazí se všechny informace založené na protokolu. Další informace najdete v tématu [protokoly streamování](../app-service/troubleshoot-diagnostic-logs.md#stream-logs). Tato metoda streamování podporuje pouze jednu instanci a nelze ji použít s aplikací spuštěnou v systému Linux v plánu spotřeby.

* **Live Metrics Stream**: když je aplikace funkcí [připojená k Application Insights](configure-monitoring.md#enable-application-insights-integration), můžete zobrazit data protokolu a další metriky téměř v reálném čase v Azure Portal pomocí [Live Metrics Stream](../azure-monitor/app/live-stream.md). Tuto metodu použijte, když chcete monitorovat funkce běžící na více instancích nebo v systému Linux v plánu spotřeby. Tato metoda používá [ukázková data](configure-monitoring.md#configure-sampling).

Streamy protokolů je možné zobrazit na portálu i ve většině místních vývojových prostředí. Další informace o tom, jak povolit streamy protokolů, najdete [v tématu Povolení protokolů spouštění streamování v Azure Functions](streaming-logs.md).

## <a name="diagnostic-logs"></a>Diagnostické protokoly

_Tato funkce je ve verzi Preview._ 

Application Insights umožňuje exportovat data telemetrie do dlouhodobého úložiště nebo jiné služby Analysis Services.  

Protože funkce jsou také integrovány s Azure Monitor, můžete také použít nastavení diagnostiky k posílání dat telemetrie do různých míst, včetně protokolů Azure Monitor. Další informace najdete v tématu [monitorování Azure Functions pomocí protokolů Azure monitor](functions-monitor-log-analytics.md).

## <a name="scale-controller-logs"></a>Škálování protokolů řadičů

_Tato funkce je ve verzi Preview._ 

[Kontroler škálování Azure Functions](./functions-scale.md#runtime-scaling) sleduje instance Azure Functionsho hostitele, na kterém vaše aplikace běží. Tento kontroler provádí rozhodnutí o tom, kdy se instance na základě aktuálního výkonu přidávají nebo odebírají. Můžete mít k dispozici protokoly Application Insights pro vygenerování protokolů škálování, které vám umožní lépe pochopit rozhodnutí, které kontroler škálování provádí pro vaši aplikaci Function App. Vygenerované protokoly můžete také ukládat do úložiště objektů BLOB pro účely analýzy pomocí jiné služby. 

Chcete-li povolit tuto funkci, přidejte nastavení aplikace s názvem `SCALE_CONTROLLER_LOGGING_ENABLED` do nastavení aplikace Function App. Další informace najdete v tématu [Konfigurace protokolů řadiče škálování](configure-monitoring.md#configure-scale-controller-logs).

## <a name="report-issues"></a>Nahlášení potíží

Pokud chcete ohlásit problém s Application Insights integrací v rámci funkcí nebo udělat návrh nebo žádost, [vytvořte problém na GitHubu](https://github.com/Azure/Azure-Functions/issues/new).

## <a name="next-steps"></a>Další kroky

Další informace naleznete v následujících zdrojích:

* [Application Insights](/azure/application-insights/)
* [Protokolování ASP.NET Core](/aspnet/core/fundamentals/logging/)
