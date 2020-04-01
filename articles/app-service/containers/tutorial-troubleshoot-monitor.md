---
title: 'Kurz: Poradce při potížích s Azure Monitorem'
description: Zjistěte, jak vám Azure Monitor a Log Analytics pomáhají monitorovat webovou aplikaci App Service. Azure Monitor maximalizuje dostupnost tím, že dodává komplexní řešení pro monitorování prostředí.
author: msangapu-msft
ms.author: msangapu
ms.topic: tutorial
ms.date: 2/28/2020
ms.openlocfilehash: d543a9364311b2cf5f0258fbf9185d27bb1bfb2f
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "78399527"
---
# <a name="tutorial-troubleshoot-an-app-service-app-with-azure-monitor"></a>Kurz: Poradce při potížích s aplikací App Service pomocí Azure Monitoru

> [!NOTE]
> Integrace Azure Monitoru se službou App Service je ve [verzi Preview](https://aka.ms/appsvcblog-azmon).
>

[App Service na Linuxu](app-service-linux-intro.md) poskytuje vysoce škálovatelnou webhostingovou službu s vlastní mizákou pomocí operačního systému Linux. [Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/overview) maximalizuje dostupnost a výkon vašich aplikací a služeb tím, že poskytuje komplexní řešení pro shromažďování, analýzu a působení na telemetrii z vašeho cloudu a místních prostředí.

Tento kurz ukazuje, jak řešit potíže s aplikací pomocí [Azure Monitoru](https://docs.microsoft.com/azure/azure-monitor/overview). Ukázková aplikace obsahuje kód určený k vyčerpání paměti a způsobit chyby HTTP 500, takže můžete diagnostikovat a opravit problém pomocí Azure Monitor.

Až skončíte, budete mít ukázkovou aplikaci spuštěnou v App Service na Linuxu integrovanou s [Azure Monitorem](https://docs.microsoft.com/azure/azure-monitor/overview).

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Konfigurace webové aplikace pomocí Azure Monitoru
> * Odeslání protokolů konzoly do služby Log Analytics
> * Použití dotazů protokolu k identifikaci a řešení potíží s chybami webových aplikací

Podle kroků v tomto kurzu můžete postupovat v systémech macOS, Linux a Windows.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Požadavky

K dokončení tohoto kurzu budete potřebovat:

- [Předplatné Azure](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)
- [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli)
- [Git](https://git-scm.com/)

## <a name="create-azure-resources"></a>Vytvoření prostředků Azure

Nejprve spustit několik příkazů místně nastavit ukázkovou aplikaci pro použití v tomto kurzu. Příkazy klonovat ukázkovou aplikaci, vytvářet prostředky Azure, vytvořit uživatele nasazení a nasadit aplikaci do Azure. Budete vyzváni k zadání hesla jako součást vytvoření uživatele nasazení. 

```bash
git clone https://github.com/Azure-Samples/App-Service-Troubleshoot-Azure-Monitor
az group create --name myResourceGroup --location "South Central US"
az webapp deployment user set --user-name <username> --password <password>
az appservice plan create --name myAppServicePlan --resource-group myResourceGroup --sku B1 --is-linux
az webapp create --resource-group myResourceGroup --plan myAppServicePlan --name <app-name> --runtime "PHP|7.3" --deployment-local-git
git remote add azure <url_from_previous_step>
git push azure master
```

## <a name="configure-azure-monitor-preview"></a>Konfigurace Azure Monitoru (preview)

### <a name="create-a-log-analytics-workspace"></a>Vytvoření pracovního prostoru analýzy protokolů

Teď, když jste nasadili ukázkovou aplikaci do služby Azure App Service, nakonfigurujete možnost monitorování k řešení problémů s aplikací, když nastanou problémy. Azure Monitor ukládá data protokolu v pracovním prostoru Analýzy protokolů. Pracovní prostor je kontejner, který obsahuje data a informace o konfiguraci.

V tomto kroku vytvoříte pracovní prostor Analýzy protokolů pro konfiguraci Azure Monitoru s vaší aplikací.

```bash
az monitor log-analytics workspace create --resource-group myResourceGroup --workspace-name myMonitorWorkspace
```

> [!NOTE]
> [V azure monitoru Log Analytics platíte za ingestování dat a uchovávání dat.](https://azure.microsoft.com/pricing/details/monitor/)
>

### <a name="create-a-diagnostic-setting"></a>Vytvoření diagnostického nastavení

Diagnostická nastavení se dá použít ke shromažďování metrik pro určité služby Azure do protokolů monitorování Azure pro analýzu s dalšími daty monitorování pomocí dotazů protokolu. V tomto kurzu povolíte webový server a standardní výstupní/chybové protokoly. Úplný seznam typů protokolů a popisů naleznete v [podporovaných typech protokolů.](https://docs.microsoft.com/azure/app-service/troubleshoot-diagnostic-logs#supported-log-types)

Spuštěním následujících příkazů vytvoříte diagnostická nastavení pro aplikace AppServiceConsoleLogs (standardní výstup/chyba) a protokoly HTTPLogs služby AppService (protokoly webového serveru). Nahraďte _ \<>názvu aplikace_ a _ \<>názvu pracovního prostoru_ hodnotami. 

> [!NOTE]
> První dva příkazy `resourceID` `workspaceID`a , jsou proměnné, `az monitor diagnostic-settings create` které mají být použity v příkazu. Další informace o tomto příkazu najdete [v tématu Vytvoření diagnostických nastavení pomocí azure cli.](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings#create-diagnostic-settings-using-azure-cli)
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

## <a name="troubleshoot-the-app"></a>Poradce při potížích s aplikací

Přejděte na `http://<app-name>.azurewebsites.net`.

Ukázková aplikace ImageConverter převádí zahrnuté obrázky z `JPG` na `PNG`. Chyba byla záměrně umístěna v kódu pro tento kurz. Pokud vyberete dostatek obrázků, aplikace vytvoří chybu HTTP 500 během převodu obrázku. Představte si, že tento scénář nebyl považován během fáze vývoje. Azure Monitor použijete k řešení chyby.

### <a name="verify-the-app-is-works"></a>Ověření, že aplikace funguje

Chcete-li převést obrázky, klepněte `Tools` a vyberte . `Convert to PNG`

![Klikněte na 'Nástroje' a vyberte 'Převést na PNG'](./media/tutorial-azure-monitor/sample-monitor-app-tools-menu.png)

Vyberte první dva `convert`obrazy a klepněte na tlačítko . To bude úspěšně převést.

![Výběr prvních dvou obrázků](./media/tutorial-azure-monitor/sample-monitor-app-convert-two-images.png)

### <a name="break-the-app"></a>Přerušení aplikace

Nyní, když jste aplikaci ověřili úspěšným převodem dvou obrázků, pokusíme se převést prvních pět obrázků.

![Převést prvních pět obrázků](./media/tutorial-azure-monitor/sample-monitor-app-convert-five-images.png)

Tato akce se nezdaří a vytvoří `HTTP 500` chybu, která nebyla testována během vývoje.

![Převod bude mít za následek chybu HTTP 500](./media/tutorial-azure-monitor/sample-monitor-app-http-500.png)

## <a name="use-log-query-to-view-azure-monitor-logs"></a>Zobrazení protokolů Azure Monitoru pomocí dotazu protokolu

Podívejme se, jaké protokoly jsou k dispozici v pracovním prostoru Log Analytics. 

Kliknutím na tento [odkaz pracovního prostoru Log Analytics](https://portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.OperationalInsights%2Fworkspaces) přenesete přístup k vašemu pracovnímu prostoru na webu Azure Portal.

Na webu Azure Portal vyberte pracovní prostor Analýzy protokolů.

### <a name="log-queries"></a>Dotazy na protokoly

Protokolové dotazy vám pomohou plně využít hodnotu dat shromážděných v protokolech monitorování Azure. Dotazy protokolu se používají k identifikaci protokolů v protokolech httplogů appservice i v logech AppService. Další informace o dotazech protokolu naleznete v [přehledu dotazů](https://docs.microsoft.com/azure/azure-monitor/log-query/log-query-overview) protokolu.

### <a name="view-appservicehttplogs-with-log-query"></a>Zobrazení protokolů HTTPLogs služby AppService s protokolovým dotazem

Teď, když jsme k aplikaci přistupovali, podívejme data spojená s `AppServiceHTTPLogs`požadavky HTTP, která se nacházejí v aplikaci .

1. Klikněte `Logs` z levé navigace.

![Protokolovat protokoly Anlytics Worksace](./media/tutorial-azure-monitor/log-analytics-workspace-logs.png)

2. Vyhledejte `appservice` a poklepejte na něj `AppServiceHTTPLogs`.

![Tabulky pracovních prostorů analýzy protokolů](./media/tutorial-azure-monitor/log-analytics-workspace-app-service-tables.png)

3. Klikněte na `Run` (Další).

![Protokoly HTTP služby HTTP služby Log Analytics Workspace App Service](./media/tutorial-azure-monitor/log-analytics-workspace-app-service-http-logs.png)

Dotaz `AppServiceHTTPLogs` vrátí všechny požadavky za posledních 24 hodin. Sloupec `ScStatus` obsahuje stav HTTP. Chcete-li `HTTP 500` diagnostikovat `ScStatus` chyby, omezte na 500 a spusťte dotaz, jak je znázorněno níže:

```kusto
AppServiceHTTPLogs
| where ScStatus == 500
```

### <a name="view-appserviceconsolelogs-with-log-query"></a>Zobrazení appserviceconsolelogů s protokolovým dotazem

Nyní, když jste potvrdili HTTP 500s, podívejme se na standardní výstup / chyby z aplikace. Tyto protokoly se nacházejí v 'AppServiceConsoleLogs'.

(1) `+` Kliknutím vytvoříte nový dotaz. 

(2) Poklepejte `AppServiceConsoleLogs` na `Run`tabulku a klepněte na tlačítko . 

Vzhledem k tomu, že převod pěti obrázků má za následek chyby serveru, můžete zjistit, zda aplikace také píše chyby filtrováním `ResultDescription` chyb, jak je uvedeno níže:

```kusto
AppServiceConsoleLogs |
where ResultDescription  contains "error" 
```

Ve `ResultDescription` sloupci se zobrazí následující chyba:

```
PHP Fatal error:  Allowed memory size of 134217728 bytes exhausted 
(tried to allocate 16384 bytes) in /home/site/wwwroot/process.php on line 20, 
referer: http://<app-name>.azurewebsites.net/
```

### <a name="join-appservicehttplogs-and-appserviceconsolelogs"></a>Připojení k protokolům HTTPLogs appservice a logům AppServiceConsole

Nyní, když jste identifikovali chyby HTTP 500s i standardní chyby, je třeba potvrdit, zda existuje korelace mezi těmito zprávami. Dále se spojíte tabulky dohromady na `TimeGenerated`základě časového razítka , .

> [!NOTE]
> Byl pro vás připraven dotaz, který provádí následující akce:
>
> - Filtruje protokoly HTTPLogs pro 500 chyb.
> - Protokoly konzoly dotazů
> - Připojí se ke stolům`TimeGenerated`
>

Spusťte tento dotaz:

```kusto
let myHttp = AppServiceHTTPLogs | where  ScStatus == 500 | project TimeGen=substring(TimeGenerated, 0, 19), CsUriStem, ScStatus;  

let myConsole = AppServiceConsoleLogs | project TimeGen=substring(TimeGenerated, 0, 19), ResultDescription;

myHttp | join myConsole on TimeGen | project TimeGen, CsUriStem, ScStatus, ResultDescription;
```

Ve `ResultDescription` sloupci se zobrazí následující chyba současně s chybami webového serveru:

```
PHP Fatal error:  Allowed memory size of 134217728 bytes exhausted 
(tried to allocate 16384 bytes) in /home/site/wwwroot/process.php on line 20, 
referer: http://<app-name>.azurewebsites.net/
```

Zpráva uvádí, že paměť byla vyčerpána na řádku 20 z `process.php`. Nyní jste potvrdili, že aplikace způsobila chybu během chyby HTTP 500. Podívejme se na kód k identifikaci problému.

## <a name="identify-the-error"></a>Určení chyby

V místním adresáři `process.php` otevřete a podívejte se na řádek 20. 

```php
imagepng($imgArray[$x], $filename);
```

První argument `$imgArray[$x]`, je proměnná obsahující všechny soubory JG (v paměti), které potřebují převod. Však `imagepng` potřebuje pouze obraz, který se převádí a ne všechny obrázky. Předběžné načítání obrázků není nutné a může být příčinou vyčerpání paměti, což vede k HTTP 500s. Pojďme aktualizovat kód načíst obrázky na vyžádání, aby zjistili, zda se problém vyřeší. Dále zlepšíte kód k řešení problému s pamětí.

## <a name="fix-the-app"></a>Oprava aplikace

### <a name="update-locally-and-redeploy-the-code"></a>Místní aktualizace a opětovné nasazení kódu

Provedete následující změny `process.php` pro zpracování vyčerpání paměti:

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
git push azure master
```

### <a name="browse-to-the-azure-app"></a>Přejděte do aplikace Azure

Přejděte na `http://<app-name>.azurewebsites.net`. 

Převod obrazů by již neměl způsobit chyby protokolu HTTP 500.

![Aplikace PHP spuštěná ve službě Azure App Service](./media/tutorial-azure-monitor/sample-monitor-app-working.png)

[!INCLUDE [cli-samples-clean-up](../../../includes/cli-samples-clean-up.md)]

Odstraňte diagnostické nastavení pomocí následujícího příkazu:

```bash
az monitor diagnostic-settings delete --resource $resourceID -n myMonitorLogs
```
Naučili jste se:

> [!div class="checklist"]
> * Konfigurace webové aplikace pomocí Azure Monitoru
> * Odeslané protokoly do služby Log Analytics
> * Použití dotazů protokolu k identifikaci a řešení potíží s chybami webových aplikací

## <a name="next-steps"></a><a name="nextsteps"></a>Další kroky
* [Protokoly dotazů pomocí Azure Monitoru](../../azure-monitor/log-query/log-query-overview.md)
* [Poradce při potížích s azure app service ve Visual Studiu](../troubleshoot-dotnet-visual-studio.md)
* [Analýza protokolů aplikací v HDInsightu](https://gallery.technet.microsoft.com/scriptcenter/Analyses-Windows-Azure-web-0b27d413)
