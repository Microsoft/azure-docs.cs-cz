---
title: Konfigurace monitorování pro Azure Functions
description: Naučte se, jak připojit aplikaci Function App k Application Insights ke sledování a jak konfigurovat shromažďování dat.
ms.date: 8/31/2020
ms.topic: how-to
ms.custom: contperfq2, devx-track-azurecli
ms.openlocfilehash: f5b1b00c534abf1e7f82d2aca69dd4763b40d5ad
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/18/2020
ms.locfileid: "94833074"
---
# <a name="how-to-configure-monitoring-for-azure-functions"></a>Postup konfigurace monitorování pro Azure Functions

Azure Functions se integruje s Application Insights, která vám umožní monitorovat aplikace Function App. Application Insights funkce Azure Monitor je rozšiřitelná služba pro správu výkonu aplikací (APM), která shromažďuje data generovaná aplikací Function App, včetně informací, které vaše aplikace zapisuje do protokolů. Při vytváření aplikace Function App je obvykle povolená integrace Application Insights. Pokud vaše aplikace nemá nastaven klíč instrumentace, musíte nejdřív [Povolit integraci Application Insights](#enable-application-insights-integration). 

Application Insights můžete použít bez vlastní konfigurace. Výchozí konfigurace může mít za následek velké objemy dat. Pokud používáte předplatné sady Visual Studio Azure, můžete pro Application Insights použít svůj limit dat. Další informace o Application Insightsch nákladů najdete v tématu [Správa využití a nákladů pro Application Insights](../azure-monitor/app/pricing.md).

Později v tomto článku se dozvíte, jak nakonfigurovat a přizpůsobit data, která vaše funkce odesílají do Application Insights. V případě aplikace Function App se protokolování nakonfiguruje v [host.jsv] souboru. 

> [!NOTE]
> Pomocí speciálně nakonfigurovaných nastavení aplikace můžete reprezentovat konkrétní nastavení v host.jssouboru pro konkrétní prostředí. To vám umožní efektivně měnit host.jsnastavení bez nutnosti opětovného publikování host.jsna soubor v projektu. Další informace najdete v tématu [přepis host.jsna hodnotách](functions-host-json.md#override-hostjson-values).

## <a name="configure-categories"></a>Konfigurovat kategorie

Protokolovací nástroj Azure Functions zahrnuje *kategorii* pro každý protokol. Kategorie označuje, která část běhového kódu nebo kód vaší funkce zapsaly protokol. Kategorie se liší v rozmezí od verze 1. x do novější verze. Následující graf popisuje hlavní kategorie protokolů, které modul runtime vytvoří. 

# <a name="v2x"></a>[v2. x +](#tab/v2)

| Kategorie | Tabulka | Popis |
| ----- | ----- | ----- |
| **`Function.<YOUR_FUNCTION_NAME>`** | **závislosti**| Data závislostí se automaticky shromažďují pro některé služby. V případě úspěšného spuštění jsou tyto protokoly na `Information` úrovni. Další informace najdete v tématu [závislosti](functions-monitoring.md#dependencies). Výjimky jsou protokolovány na `Error` úrovni. Modul runtime také vytváří `Warning` protokoly úrovně, například při posílání zpráv fronty do [nepoškozené fronty](functions-bindings-storage-queue-trigger.md#poison-messages). | 
| **`Function.<YOUR_FUNCTION_NAME>`** | **customMetrics**<br/>**customEvents** | Sady SDK pro C# a JavaScript umožňují shromažďovat vlastní metriky a protokolovat vlastní události. Další informace najdete v tématu [vlastní data telemetrie](functions-monitoring.md#custom-telemetry-data).|
| **`Function.<YOUR_FUNCTION_NAME>`** | **trasování**| Zahrnuje spuštění funkce a dokončené protokoly pro konkrétní funkce. V případě úspěšného spuštění jsou tyto protokoly na `Information` úrovni. Výjimky jsou protokolovány na `Error` úrovni. Modul runtime také vytváří `Warning` protokoly úrovně, například při posílání zpráv fronty do [nepoškozené fronty](functions-bindings-storage-queue-trigger.md#poison-messages). | 
| **`Function.<YOUR_FUNCTION_NAME>.User`** | **trasování**| Uživatelem vygenerované protokoly, které mohou být libovolné úrovně protokolu. Další informace o zápisu do protokolů z vašich funkcí najdete v tématu [zápis do protokolů](functions-monitoring.md#writing-to-logs). | 
| **`Host.Aggregator`** | **customMetrics** | Tyto protokoly generované modulem runtime poskytují počty a průměry volání funkcí v [konfigurovatelném](#configure-the-aggregator) časovém intervalu. Výchozí doba je 30 sekund nebo 1 000 výsledků, podle toho, co nastane dřív. Jedná se o počet spuštění, úspěšnost a dobu trvání. Všechny tyto protokoly jsou zapisovány na `Information` úrovni. Pokud filtrujete `Warning` nad nebo výše, nezobrazí se žádná z těchto dat. |
| **`Host.Results`** | **požadavky** | Tyto protokoly generované modulem runtime označují úspěch nebo neúspěch funkce. Všechny tyto protokoly jsou zapisovány na `Information` úrovni. Pokud filtrujete `Warning` nad nebo výše, nezobrazí se žádná z těchto dat. |
| **`Microsoft`** | **trasování** | Plně kvalifikovaná kategorie protokolu, která odráží komponentu modulu runtime .NET vyvolanou hostitelem.  |
| **`Worker`** | **trasování** | Protokoly generované pracovním procesem jazyka pro jazyky non-.NET Do kategorie se můžou zapisovat i v případě, že se zaprotokoluje v kategorii pracovních pracovních procesů `Microsoft.*` `Microsoft.Azure.WebJobs.Script.Workers.Rpc.RpcFunctionInvocationDispatcher` . Tyto protokoly jsou zapisovány na `Information` úrovni.|

# <a name="v1x"></a>[V1. x](#tab/v1)

| Kategorie | Tabulka | Popis |
| ----- | ----- | ----- |
| **`Function`** | **trasování**| Uživatelem vygenerované protokoly, které mohou být libovolné úrovně protokolu. Další informace o zápisu do protokolů z vašich funkcí najdete v tématu [zápis do protokolů](functions-monitoring.md#writing-to-logs). | 
| **`Host.Aggregator`** | **customMetrics** | Tyto protokoly generované modulem runtime poskytují počty a průměry volání funkcí v [konfigurovatelném](#configure-the-aggregator) časovém intervalu. Výchozí doba je 30 sekund nebo 1 000 výsledků, podle toho, co nastane dřív. Jedná se o počet spuštění, úspěšnost a dobu trvání. Všechny tyto protokoly jsou zapisovány na `Information` úrovni. Pokud filtrujete `Warning` nad nebo výše, nezobrazí se žádná z těchto dat. |
| **`Host.Executor`** | **trasování** | Zahrnuje **spuštěnou funkci** a **funkce dokončených** protokolů pro konkrétní spuštění funkce. V případě úspěšného spuštění jsou tyto protokoly na `Information` úrovni. Výjimky jsou protokolovány na `Error` úrovni. Modul runtime také vytváří `Warning` protokoly úrovně, například při posílání zpráv fronty do [nepoškozené fronty](functions-bindings-storage-queue-trigger.md#poison-messages).  |
| **`Host.Results`** | **požadavky** | Tyto protokoly generované modulem runtime označují úspěch nebo neúspěch funkce. Všechny tyto protokoly jsou zapisovány na `Information` úrovni. Pokud filtrujete `Warning` nad nebo výše, nezobrazí se žádná z těchto dat. |

---

Sloupec **tabulky** určuje, která tabulka v Application Insights protokol je zapsán. 

## <a name="configure-log-levels"></a>Konfigurovat úrovně protokolu

[!INCLUDE [functions-log-levels](../../includes/functions-log-levels.md)]

Pro každou kategorii označíte minimální úroveň protokolu k odeslání. host.jsv nastavení se liší v závislosti na [verzi modulu runtime Functions](functions-versions.md). 

Následující příklad definuje protokolování na základě následujících pravidel:

+ Pro protokoly `Host.Results` nebo `Function` se protokolují pouze události na `Error` úrovni nebo vyšší. 
+ V případě protokolů aplikace `Host.Aggregator` protokolujte všechny vygenerované metriky ( `Trace` ).
+ Pro všechny ostatní protokoly, včetně protokolů uživatelů, úrovně protokolu `Information` a vyšších událostí.

# <a name="v2x"></a>[v2. x +](#tab/v2)

```json
{
  "logging": {
    "fileLoggingMode": "always",
    "logLevel": {
      "default": "Information",
      "Host.Results": "Error",
      "Function": "Error",
      "Host.Aggregator": "Trace"
    }
  }
}
```

# <a name="v1x"></a>[V1. x](#tab/v1) 

```json
{
  "logger": {
    "categoryFilter": {
      "defaultLevel": "Information",
      "categoryLevels": {
        "Host.Results": "Error",
        "Function": "Error",
        "Host.Aggregator": "Trace"
      }
    }
  }
}
```

---

Pokud [host.jsna] zahrnuje více protokolů, které začínají stejným řetězcem, jsou nejprve porovnány ještě definované protokoly. Vezměte v úvahu následující příklad, který protokoluje všechno v modulu runtime, s výjimkou na `Host.Aggregator` `Error` úrovni:

# <a name="v2x"></a>[v2. x +](#tab/v2)

```json
{
  "logging": {
    "fileLoggingMode": "always",
    "logLevel": {
      "default": "Information",
      "Host": "Error",
      "Function": "Error",
      "Host.Aggregator": "Information"
    }
  }
}
```

# <a name="v1x"></a>[V1. x](#tab/v1) 

```json
{
  "logger": {
    "categoryFilter": {
      "defaultLevel": "Information",
      "categoryLevels": {
        "Host": "Error",
        "Function": "Error",
        "Host.Aggregator": "Information"
      }
    }
  }
}
```

---

Můžete použít nastavení na úrovni protokolu, které `None` zabraňuje zápisu jakýchkoli protokolů pro kategorii. 

## <a name="configure-the-aggregator"></a>Konfigurace Agregátoru

Jak je uvedeno v předchozí části, modul runtime agreguje data o spouštěních funkcí v časovém intervalu. Výchozí doba je 30 sekund nebo 1 000 spuštění, podle toho, co nastane dřív. Toto nastavení můžete nakonfigurovat v [host.js] souboru.  Tady je příklad:

```json
{
    "aggregator": {
      "batchSize": 1000,
      "flushTimeout": "00:00:30"
    }
}
```

## <a name="configure-sampling"></a>Konfigurace vzorkování

Application Insights má funkci [vzorkování](../azure-monitor/app/sampling.md) , která vám může chránit při vytváření příliš velkého množství dat telemetrie při dokončeném provádění v době špičky zatížení. Když frekvence příchozího provádění překročí zadanou prahovou hodnotu, Application Insights začne náhodně ignorovat některé příchozí spuštění. Výchozí nastavení maximálního počtu spuštění za sekundu je 20 (pět ve verzi 1. x). Vzorkování můžete nakonfigurovat v [host.jszapnuto](./functions-host-json.md#applicationinsights).  Tady je příklad:

# <a name="v2x"></a>[v2. x +](#tab/v2)

```json
{
  "logging": {
    "applicationInsights": {
      "samplingSettings": {
        "isEnabled": true,
        "maxTelemetryItemsPerSecond" : 20,
        "excludedTypes": "Request;Exception"
      }
    }
  }
}
```


Z vzorkování můžete vyloučit určité typy telemetrie. V tomto příkladu data typu `Request` a `Exception` jsou vyloučena z vzorkování. Tím se zajistí, že se zaprotokolují *všechny* provádění funkcí (požadavky) a výjimky, zatímco jiné typy telemetrie nadále podléhají vzorkování. 

# <a name="v1x"></a>[V1. x](#tab/v1)  

```json
{
  "applicationInsights": {
    "sampling": {
      "isEnabled": true,
      "maxTelemetryItemsPerSecond" : 5
    }
  }
}
```
---

Další informace najdete v tématu [vzorkování v Application Insights](../azure-monitor/app/sampling.md).

## <a name="configure-scale-controller-logs"></a>Konfigurovat protokoly řadiče škálování

_Tato funkce je ve verzi Preview._ 

[Řadič Azure Functions Scale](./functions-scale.md#runtime-scaling) můžete mít k dispozici protokoly pro Application Insights nebo do úložiště objektů blob, abyste lépe pochopili rozhodnutí, které kontroler škálování vytváří pro vaši aplikaci Function App.

Chcete-li povolit tuto funkci, přidejte nastavení aplikace s názvem `SCALE_CONTROLLER_LOGGING_ENABLED` do nastavení aplikace Function App. Hodnota tohoto nastavení musí být ve formátu `<DESTINATION>:<VERBOSITY>` , a to na základě následujícího:

[!INCLUDE [functions-scale-controller-logging](../../includes/functions-scale-controller-logging.md)]

Například následující příkaz Azure CLI zapne podrobné protokolování z kontroleru škálování na Application Insights:

```azurecli-interactive
az functionapp config appsettings set --name <FUNCTION_APP_NAME> \
--resource-group <RESOURCE_GROUP_NAME> \
--settings SCALE_CONTROLLER_LOGGING_ENABLED=AppInsights:Verbose
```

V tomto příkladu nahraďte `<FUNCTION_APP_NAME>` a `<RESOURCE_GROUP_NAME>` názvem vaší aplikace Function App a názvem skupiny prostředků v uvedeném pořadí. 

Následující příkaz rozhraní příkazového řádku Azure zakáže protokolování nastavením podrobností na `None` :

```azurecli-interactive
az functionapp config appsettings set --name <FUNCTION_APP_NAME> \
--resource-group <RESOURCE_GROUP_NAME> \
--settings SCALE_CONTROLLER_LOGGING_ENABLED=AppInsights:None
```

Protokolování můžete také zakázat odebráním `SCALE_CONTROLLER_LOGGING_ENABLED` nastavení pomocí následujícího příkazu rozhraní příkazového řádku Azure:

```azurecli-interactive
az functionapp config appsettings delete --name <FUNCTION_APP_NAME> \
--resource-group <RESOURCE_GROUP_NAME> \
--setting-names SCALE_CONTROLLER_LOGGING_ENABLED
```

## <a name="enable-application-insights-integration"></a>Povolení integrace Application Insights

Aby aplikace Function App odesílala data Application Insights, musí znát klíč instrumentace prostředku Application Insights. Klíč musí být v nastavení aplikace s názvem **APPINSIGHTS_INSTRUMENTATIONKEY**.

Když vytvoříte aplikaci Function App [v Azure Portal](functions-create-first-azure-function.md), z příkazového řádku pomocí [Azure Functions Core Tools](functions-create-first-azure-function-azure-cli.md)nebo pomocí [Visual Studio Code](functions-create-first-function-vs-code.md)je ve výchozím nastavení povolená integrace Application Insights. Prostředek Application Insights má stejný název jako aplikace Function App a je vytvořený buď ve stejné oblasti, nebo v nejbližší oblasti.

### <a name="new-function-app-in-the-portal"></a>Nová aplikace Function App na portálu

Chcete-li zkontrolovat vytvářené prostředky Application Insights, vyberte ji a rozbalte okno **Application Insights** . Můžete změnit **název nového prostředku** nebo zvolit jiné **umístění** v [geografických oblastech Azure](https://azure.microsoft.com/global-infrastructure/geographies/) , kam chcete data uložit.

![Povolit Application Insights při vytváření aplikace Function App](media/functions-monitoring/enable-ai-new-function-app.png)

Když zvolíte **vytvořit**, vytvoří se prostředek Application Insights s aplikací Function App, která má `APPINSIGHTS_INSTRUMENTATIONKEY` nastavenou hodnotu v nastavení aplikace. Všechno je připravené k přechodu.

<a id="manually-connect-an-app-insights-resource"></a>
### <a name="add-to-an-existing-function-app"></a>Přidat do existující aplikace Function App 

Pokud se ve vaší aplikaci Function App nevytvořily prostředky Application Insights, vytvořte prostředek pomocí následujícího postupu. Potom můžete do aplikace Function app přidat klíč instrumentace z tohoto prostředku jako [nastavení aplikace](functions-how-to-use-azure-function-app-settings.md#settings) .

1. V [Azure Portal](https://portal.azure.com)vyhledejte a vyberte **aplikace Function App** a pak zvolte aplikaci Function App. 

1. Vyberte v horní části okna banner **Služba Application Insights není nakonfigurovaná**. Pokud se vám nápis nezobrazuje, může být aplikace již Application Insights povolena.

    :::image type="content" source="media/configure-monitoring/enable-application-insights.png" alt-text="Povolení služby Application Insights z portálu":::

1. Rozbalte položku **změnit prostředek** a vytvořte prostředek Application Insights pomocí nastavení uvedeného v následující tabulce.  

    | Nastavení      | Navrhovaná hodnota  | Popis                                        |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Nový název prostředku** | Jedinečný název aplikace | Nejjednodušší je použít stejný název, jako má vaše aplikace funkcí, který musí být ve vašem předplatném jedinečný. | 
    | **Umístění** | Západní Evropa | Pokud je to možné, použijte stejnou [oblast](https://azure.microsoft.com/regions/), jakou má vaše aplikace funkcí, nebo tu, která je blízko této oblasti. |

    :::image type="content" source="media/configure-monitoring/ai-general.png" alt-text="Vytvoření prostředku Application Insights":::

1. Vyberte **Použít**. 

   Prostředek Application Insights se vytvoří ve stejné skupině prostředků a předplatném jako vaše aplikace funkcí. Po vytvoření prostředku zavřete okno Application Insights.

1. Ve vaší aplikaci Function App vyberte v části **Nastavení** položku **Konfigurace** a pak vyberte **nastavení aplikace**. Pokud se zobrazí nastavení s názvem `APPINSIGHTS_INSTRUMENTATIONKEY`, je pro vaši aplikaci funkcí běžící v Azure povolena integrace Application Insights. Pokud z nějakého důvodu toto nastavení neexistuje, přidejte ho jako hodnotu pomocí Application Insights klíč instrumentace.

> [!NOTE]
> Dřívější verze funkcí používaly integrované monitorování, které se už nedoporučuje. Když povolíte integraci Application Insights pro takovou aplikaci Function App, musíte taky [zakázat integrované protokolování](#disable-built-in-logging).  

## <a name="disable-built-in-logging"></a>Zákaz integrovaného protokolování

Pokud povolíte Application Insights, zakažte integrované protokolování, které používá Azure Storage. Integrované protokolování je užitečné pro testování s využitím lehkých úloh, ale není určené pro použití v produkčním prostředí s vysokou zátěží. Pro produkční monitorování doporučujeme Application Insights. Pokud se v produkčním prostředí používá integrované protokolování, může být záznam protokolování neúplný z důvodu omezování Azure Storage.

Pokud chcete zakázat integrované protokolování, odstraňte `AzureWebJobsDashboard` nastavení aplikace. Informace o tom, jak odstranit nastavení aplikace v Azure Portal, najdete v části **nastavení aplikace** v tématu [Správa aplikace Function App](functions-how-to-use-azure-function-app-settings.md#settings). Před odstraněním nastavení aplikace se ujistěte, že žádné existující funkce ve stejné aplikaci Function App nepoužívají nastavení pro Azure Storage triggery nebo vazby.

## <a name="next-steps"></a>Další kroky

Další informace o monitorování najdete tady:

+ [Monitorování Azure Functions](functions-monitoring.md)
+ [Analýza dat Azure Functions telemetrie v Application Insights](analyze-telemetry-data.md)
+ [Application Insights](/azure/application-insights/)


[host.jsna]: functions-host-json.md
