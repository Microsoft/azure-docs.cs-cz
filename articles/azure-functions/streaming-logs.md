---
title: Protokoly spuštění streamu v Azure Functions
description: 115-145 znaků včetně mezer Tento souhrn se zobrazí ve výsledcích vyhledávání.
ms.date: 9/1/2020
ms.topic: how-to
ms.custom: contperf-fy21q2, devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: 4afb1068acda69c9dd65a423d887abea80c695cd
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/21/2021
ms.locfileid: "107830893"
---
# <a name="enable-streaming-execution-logs-in-azure-functions"></a>Povolit protokoly spouštění streamování v Azure Functions

Při vývoji aplikace často chcete zjistit, co se do protokolů zapisuje téměř v reálném čase, když běží v Azure.

Existují dva způsoby, jak zobrazit datový proud souborů protokolu generovaných spuštěním vaší funkce.

* **Integrované streamování protokolů**: platforma App Service umožňuje zobrazit datový proud souborů protokolu aplikace. Jedná se o ekvivalent výstupu zobrazeného při ladění funkcí během [místního vývoje](functions-develop-local.md) a při použití karty **test** na portálu. Zobrazí se všechny informace založené na protokolu. Další informace najdete v tématu [protokoly streamování](../app-service/troubleshoot-diagnostic-logs.md#stream-logs). Tato metoda streamování podporuje pouze jednu instanci a nelze ji použít s aplikací spuštěnou v systému Linux v plánu spotřeby.

* **Live Metrics Stream**: když je aplikace funkcí [připojená k Application Insights](configure-monitoring.md#enable-application-insights-integration), můžete zobrazit data protokolu a další metriky téměř v reálném čase v Azure Portal pomocí [Live Metrics Stream](../azure-monitor/app/live-stream.md). Tuto metodu použijte, když chcete monitorovat funkce běžící na více instancích nebo v systému Linux v plánu spotřeby. Tato metoda používá [ukázková data](configure-monitoring.md#configure-sampling).

Streamy protokolů je možné zobrazit na portálu i ve většině místních vývojových prostředí. 

## <a name="portal"></a>Portál

Oba typy datových proudů protokolů můžete zobrazit na portálu.

### <a name="built-in-log-streaming"></a>Integrované streamování protokolů

Pokud chcete zobrazit protokoly streamování na portálu, vyberte v aplikaci Function App kartu **funkce platformy** . Pak v části **monitorování** zvolte **streamování protokolů**.

![Povolit streamování protokolů na portálu](./media/functions-monitoring/enable-streaming-logs-portal.png)

Tím se vaše aplikace připojí ke službě streamování protokolů a protokoly aplikací se zobrazí v okně. Můžete přepínat mezi **protokoly aplikací** a **protokoly webového serveru**.  

![Zobrazení protokolů streamování na portálu](./media/functions-monitoring/streaming-logs-window.png)

### <a name="live-metrics-stream"></a>Live Metrics Stream

Pokud chcete zobrazit Live Metrics Stream pro vaši aplikaci, vyberte kartu **Přehled** aplikace Function App. Když Application Insights povolíte, zobrazí se v části **nakonfigurované funkce** odkaz **Application Insights** . Tento odkaz vás přesměruje na stránku Application Insights vaší aplikace.

V Application Insights vyberte **Live Metrics Stream**. [Ukázkové položky protokolu](configure-monitoring.md#configure-sampling) se zobrazují v části **ukázková telemetrie**.

![Zobrazit Live Metrics Stream na portálu](./media/functions-monitoring/live-metrics-stream.png) 

## <a name="visual-studio-code"></a>Visual Studio Code

[!INCLUDE [functions-enable-log-stream-vs-code](../../includes/functions-enable-log-stream-vs-code.md)]

## <a name="core-tools"></a>Základní nástroje

[!INCLUDE [functions-streaming-logs-core-tools](../../includes/functions-streaming-logs-core-tools.md)]

## <a name="azure-cli"></a>Azure CLI

Protokoly streamování můžete povolit pomocí [Azure CLI](/cli/azure/install-azure-cli). Pomocí následujících příkazů se přihlaste, vyberte své předplatné a soubory protokolu streamu:

```azurecli
az login
az account list
az account set --subscription <subscriptionNameOrId>
az webapp log tail --resource-group <RESOURCE_GROUP_NAME> --name <FUNCTION_APP_NAME>
```

## <a name="azure-powershell"></a>Azure PowerShell

Protokoly streamování můžete povolit pomocí [Azure PowerShell](/powershell/azure/). Pro prostředí PowerShell pomocí příkazu [set-AzWebApp](/powershell/module/az.websites/set-azwebapp) Povolte protokolování aplikace Function App, jak je znázorněno v následujícím fragmentu kódu: 

:::code language="powershell" source="~/powershell_scripts/app-service/monitor-with-logs/monitor-with-logs.ps1" range="19-20":::

Další informace naleznete v tématu [úplný příklad kódu](../app-service/scripts/powershell-monitor.md#sample-script). 

## <a name="next-steps"></a>Další kroky

+ [Monitorování Azure Functions](functions-monitoring.md)
+ [Analýza telemetrie Azure Functions v Application Insights](analyze-telemetry-data.md)
