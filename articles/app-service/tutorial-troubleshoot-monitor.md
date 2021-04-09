---
title: 'Kurz: řešení potíží pomocí Azure Monitor'
description: Přečtěte si, jak Azure Monitor a Log Analytics pomáhají monitorovat webovou aplikaci App Service. Azure Monitor maximalizuje dostupnost tím, že dodává ucelené řešení pro monitorování vašich prostředí.
author: msangapu-msft
ms.author: msangapu
ms.topic: tutorial
ms.date: 06/20/2020
ms.openlocfilehash: d45a8b8f426df32b9f5ac6f64237107083e0f9ab
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "100586283"
---
# <a name="tutorial-troubleshoot-an-app-service-app-with-azure-monitor"></a>Kurz: řešení potíží s aplikací App Service pomocí Azure Monitor

> [!NOTE]
> Azure Monitor integrace s App Service je ve [verzi Preview](https://aka.ms/appsvcblog-azmon).
>

V tomto kurzu se dozvíte, jak řešit [App Service](overview.md) aplikaci pomocí [Azure monitor](../azure-monitor/overview.md). Ukázková aplikace obsahuje kód určený k vyčerpání paměti a způsobuje chyby HTTP 500, takže můžete diagnostikovat a vyřešit problém pomocí Azure Monitor. Až budete hotovi, budete mít ukázkovou aplikaci spuštěnou na App Service na platformě Linux integrovanou s [Azure monitor](../azure-monitor/overview.md).

[Azure monitor](../azure-monitor/overview.md) maximalizuje dostupnost a výkon vašich aplikací a služeb tím, že poskytuje ucelené řešení pro shromažďování, analýzu a vykonávání telemetrie z vašich cloudových a místních prostředí.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Konfigurace webové aplikace s Azure Monitor
> * Odeslání protokolů konzoly do Log Analytics
> * Použití dotazů protokolu k identifikaci a řešení chyb webových aplikací

Podle kroků v tomto kurzu můžete postupovat v systémech macOS, Linux a Windows.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Požadavky

K dokončení tohoto kurzu budete potřebovat:

- [Předplatné Azure](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)
- [Azure CLI](/cli/azure/install-azure-cli)
- [Git](https://git-scm.com/)

## <a name="create-azure-resources"></a>Vytvoření prostředků Azure

Za prvé můžete spustit několik příkazů místně a nastavit ukázkovou aplikaci, která se použije v tomto kurzu. Příkazy naklonují ukázkovou aplikaci, vytvoří prostředky Azure, vytvoří uživatele nasazení a nasadí aplikaci do Azure. Zobrazí se výzva k zadání hesla, které jste zadali jako součást vytvoření uživatele nasazení. 

```bash
git clone https://github.com/Azure-Samples/App-Service-Troubleshoot-Azure-Monitor
az group create --name myResourceGroup --location "South Central US"
az webapp deployment user set --user-name <username> --password <password>
az appservice plan create --name myAppServicePlan --resource-group myResourceGroup --sku B1 --is-linux
az webapp create --resource-group myResourceGroup --plan myAppServicePlan --name <app-name> --runtime "PHP|7.3" --deployment-local-git
git remote add azure <url_from_previous_step>
git push azure main
```

## <a name="configure-azure-monitor-preview"></a>Konfigurace Azure Monitor (Preview)

### <a name="create-a-log-analytics-workspace"></a>Vytvoření pracovního prostoru Log Analytics

Teď, když jste nasadili ukázkovou aplikaci na Azure App Service, nakonfigurujete možnost monitorování, abyste aplikaci mohli řešit, když dojde k problémům. Azure Monitor ukládá data protokolu v pracovním prostoru Log Analytics. Pracovní prostor je kontejner, který obsahuje data a informace o konfiguraci.

V tomto kroku vytvoříte Log Analytics pracovní prostor pro konfiguraci Azure Monitor s vaší aplikací.

```bash
az monitor log-analytics workspace create --resource-group myResourceGroup --workspace-name myMonitorWorkspace
```

> [!NOTE]
> [V případě Azure Monitor Log Analytics platíte za příjem dat a uchovávání dat.](https://azure.microsoft.com/pricing/details/monitor/)
>

### <a name="create-a-diagnostic-setting"></a>Vytvoření nastavení diagnostiky

Nastavení diagnostiky lze použít ke shromáždění metrik pro určité služby Azure do protokolů Azure Monitor k analýze s ostatními daty monitorování pomocí dotazů protokolu. V tomto kurzu povolíte webový server a standardní protokoly výstupu a chyb. Úplný seznam typů protokolů a popisů najdete v části [podporované typy protokolů](./troubleshoot-diagnostic-logs.md#supported-log-types) .

Spuštěním následujících příkazů vytvořte nastavení diagnostiky pro AppServiceConsoleLogs (standardní výstup/chyba) a AppServiceHTTPLogs (protokoly webového serveru). Nahraďte _\<app-name>_ a _\<workspace-name>_ hodnotami. 

> [!NOTE]
> První dva příkazy, `resourceID` a `workspaceID` , jsou proměnné, které mají být použity v `az monitor diagnostic-settings create` příkazu. Další informace o tomto příkazu najdete v tématu [Vytvoření nastavení diagnostiky pomocí Azure CLI](../azure-monitor/essentials/diagnostic-settings.md#create-using-azure-cli) .
>

```bash
resourceID=$(az webapp show -g myResourceGroup -n <app-name> --query id --output tsv)

workspaceID=$(az monitor log-analytics workspace show -g myResourceGroup --workspace-name <workspace-name> --query id --output tsv)

az monitor diagnostic-settings create --resource $resourceID \
 --workspace $workspaceID \
 -n myMonitorLogs \
 --logs '[{"category": "AppServiceConsoleLogs", "enabled": true},
  {"category": "AppServiceHTTPLogs", "enabled": true}]'
```

## <a name="troubleshoot-the-app"></a>Řešení potíží s aplikací

Přejděte na adresu `http://<app-name>.azurewebsites.net`.

Ukázková aplikace, ImageConverter, převede zahrnuté obrázky z `JPG` na `PNG` . Chyba byla záměrně umístěna do kódu pro tento kurz. Pokud vyberete dostatek imagí, aplikace během převodu imagí vytvoří chybu HTTP 500. Představte si, že tento scénář nebyl posouzen během fáze vývoje. K odstranění chyby použijte Azure Monitor.

### <a name="verify-the-app-is-works"></a>Ověřte, že aplikace funguje.

Chcete-li převést obrázky, klikněte na tlačítko `Tools` a vyberte `Convert to PNG` .

![Klikněte na nástroje a vyberte převést na PNG.](./media/tutorial-azure-monitor/sample-monitor-app-tools-menu.png)

Vyberte první dva obrázky a klikněte na `convert` . Tím se úspěšně převede.

![Výběr prvních dvou imagí](./media/tutorial-azure-monitor/sample-monitor-app-convert-two-images.png)

### <a name="break-the-app"></a>Přerušení aplikace

Teď, když jste aplikaci ověřili tak, že úspěšně převedete dvě image, zkusíme převést prvních pět imagí.

![Převést prvních pět imagí](./media/tutorial-azure-monitor/sample-monitor-app-convert-five-images.png)

Tato akce se nezdařila a vytvoří `HTTP 500` chybu, která nebyla testována během vývoje.

![Výsledkem převodu bude chyba HTTP 500](./media/tutorial-azure-monitor/sample-monitor-app-http-500.png)

## <a name="use-log-query-to-view-azure-monitor-logs"></a>Použití dotazu protokolu k zobrazení protokolů Azure Monitor

Pojďme se podívat, jaké protokoly jsou k dispozici v pracovním prostoru Log Analytics. 

Kliknutím na tento [odkaz Log Analytics pracovního prostoru](https://portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.OperationalInsights%2Fworkspaces) získáte přístup k pracovnímu prostoru v Azure Portal.

V Azure Portal vyberte pracovní prostor Log Analytics.

### <a name="log-queries"></a>Dotazy na protokoly

Dotazy protokolu umožňují plně využít hodnoty dat shromažďovaných v protokolech Azure Monitor. Pomocí dotazů protokolu můžete identifikovat protokoly v AppServiceHTTPLogs i AppServiceConsoleLogs. Další informace o dotazech protokolu najdete v tématu [Přehled dotazů protokolu](../azure-monitor/logs/log-query-overview.md) .

### <a name="view-appservicehttplogs-with-log-query"></a>Zobrazit AppServiceHTTPLogs s dotazem protokolu

Teď, když jsme získali aplikaci, Podívejme se na data přidružená k požadavkům HTTP, která najdete v části `AppServiceHTTPLogs` .

1. Klikněte na tlačítko `Logs` z levé navigační části.

![Protokoly Worksace protokolu Anlytics](./media/tutorial-azure-monitor/log-analytics-workspace-logs.png)

2. Vyhledejte `appservice` a dvakrát klikněte na tlačítko `AppServiceHTTPLogs` .

![Tabulky pracovních prostorů Log Analytics](./media/tutorial-azure-monitor/log-analytics-workspace-app-service-tables.png)

3. Klikněte na `Run` (Další).

![Log Analytics pracovního prostoru App Service protokoly HTTP](./media/tutorial-azure-monitor/log-analytics-workspace-app-service-http-logs.png)

`AppServiceHTTPLogs`Dotaz vrátí všechny požadavky za posledních 24 hodin. Sloupec `ScStatus` obsahuje stav HTTP. Chcete-li diagnostikovat `HTTP 500` chyby, omezte na `ScStatus` 500 a spusťte dotaz, jak je znázorněno níže:

```kusto
AppServiceHTTPLogs
| where ScStatus == 500
```

### <a name="view-appserviceconsolelogs-with-log-query"></a>Zobrazit AppServiceConsoleLogs s dotazem protokolu

Teď, když jste potvrdili HTTP 500, Pojďme se podívat na standardní výstup a chyby z aplikace. Tyto protokoly jsou k dispozici v ' AppServiceConsoleLogs '.

(1) kliknutím `+` vytvoříte nový dotaz. 

(2) poklikejte na `AppServiceConsoleLogs` tabulku a klikněte na `Run` . 

Vzhledem k tomu, že při převodu pěti imagí dojde k chybám serveru, můžete zjistit, jestli je aplikace také zapsána chybami filtrováním `ResultDescription` chyb, jak je vidět níže:

```kusto
AppServiceConsoleLogs |
where ResultDescription  contains "error" 
```

Ve `ResultDescription` sloupci se zobrazí následující chyba:

```output
PHP Fatal error:  Allowed memory size of 134217728 bytes exhausted 
(tried to allocate 16384 bytes) in /home/site/wwwroot/process.php on line 20, 
referer: http://<app-name>.azurewebsites.net/
```

### <a name="join-appservicehttplogs-and-appserviceconsolelogs"></a>Připojit AppServiceHTTPLogs a AppServiceConsoleLogs

Teď, když jste identifikovali HTTP 500 i standardní chyby, je potřeba potvrdit, jestli existuje korelace mezi těmito zprávami. V dalším kroku spojíte tabulky na základě časového razítka `TimeGenerated` .

> [!NOTE]
> Pro vás byl připraven dotaz, který provede následující akce:
>
> - HTTPLogs filtry pro chyby 500
> - Dotazy na protokoly konzoly
> - Spojí tabulky na. `TimeGenerated`
>

Spusťte tento dotaz:

```kusto
let myHttp = AppServiceHTTPLogs | where  ScStatus == 500 | project TimeGen=substring(TimeGenerated, 0, 19), CsUriStem, ScStatus;  

let myConsole = AppServiceConsoleLogs | project TimeGen=substring(TimeGenerated, 0, 19), ResultDescription;

myHttp | join myConsole on TimeGen | project TimeGen, CsUriStem, ScStatus, ResultDescription;
```

Ve `ResultDescription` sloupci se zobrazí následující chyba ve stejnou dobu jako chyby webového serveru:

```output
PHP Fatal error:  Allowed memory size of 134217728 bytes exhausted 
(tried to allocate 16384 bytes) in /home/site/wwwroot/process.php on line 20, 
referer: http://<app-name>.azurewebsites.net/
```

Zpráva o stavu paměti byla vyčerpána na řádku 20 `process.php` . Nyní jste potvrdili, že aplikace vytvořila chybu během chyby HTTP 500. Pojďme se podívat na kód pro identifikaci problému.

## <a name="identify-the-error"></a>Určení chyby

V místním adresáři otevřete `process.php` a podívejte se na řádek 20. 

```php
imagepng($imgArray[$x], $filename);
```

První argument, `$imgArray[$x]` , je proměnná, která uchovává všechny JPGs (v paměti), které potřebují převod. Ale `imagepng` potřebuje jenom převáděnou image, ne všechny image. Předběžné načtení imagí není nutné a může způsobovat vyčerpání paměti, což vede k 500 HTTP. Pojďme aktualizovat kód, aby se načetly obrázky na vyžádání, aby se zobrazilo, jestli problém vyřešil. Dále Vylepšete kód pro vyřešení problému s pamětí.

## <a name="fix-the-app"></a>Oprava aplikace

### <a name="update-locally-and-redeploy-the-code"></a>Místní aktualizace a opětovné nasazení kódu

Pro zpracování vyčerpání paměti provedete následující změny `process.php` :

```php
<?php

//Retrieve query parameters
$maxImages = $_GET['images'];
$imgNames  = explode(",",$_GET['imgNames']);

//Load JPEGs into an array (in memory)
for ($x=0; $x<$maxImages; $x++){
    $filename = './images/converted_' . substr($imgNames[$x],0,-4) . '.png';
    imagepng(imagecreatefromjpeg("./images/" . $imgNames[$x]), $filename);
}
```

Potvrďte změny v Gitu a potom odešlete změny kódu do Azure.

```bash
git commit -am "Load images on-demand in process.php"
git push azure main
```

### <a name="browse-to-the-azure-app"></a>Přejít k aplikaci Azure

Přejděte na adresu `http://<app-name>.azurewebsites.net`. 

Převod obrázků by neměl nadále vydávat chyby HTTP 500.

![Aplikace PHP spuštěná ve službě Azure App Service](./media/tutorial-azure-monitor/sample-monitor-app-working.png)

[!INCLUDE [cli-samples-clean-up](../../includes/cli-samples-clean-up.md)]

Odstraňte nastavení diagnostiky pomocí následujícího příkazu:

```bash
az monitor diagnostic-settings delete --resource $resourceID -n myMonitorLogs
```
Naučili jste se:

> [!div class="checklist"]
> * Nakonfigurovali jste webovou aplikaci pomocí Azure Monitor
> * Odeslané protokoly do Log Analytics
> * Použití dotazů protokolu k identifikaci a řešení chyb webových aplikací

## <a name="next-steps"></a><a name="nextsteps"></a> Další kroky
* [Dotazování protokolů pomocí Azure Monitor](../azure-monitor/logs/log-query-overview.md)
* [Řešení potíží s Azure App Service v aplikaci Visual Studio](troubleshoot-dotnet-visual-studio.md)
* [Analýza protokolů aplikací v HDInsight](https://gallery.technet.microsoft.com/scriptcenter/Analyses-Windows-Azure-web-0b27d413)