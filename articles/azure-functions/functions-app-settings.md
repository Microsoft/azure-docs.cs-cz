---
title: Reference k nastavení aplikací pro službu Azure Functions
description: Referenční dokumentace pro nastavení aplikace Azure Functions nebo proměnné prostředí.
services: functions
author: ggailey777
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 09/22/2018
ms.author: glenga
ms.openlocfilehash: ec309bc5484c0ac96d1323c56670c147737e7c64
ms.sourcegitcommit: 3a7c1688d1f64ff7f1e68ec4bb799ba8a29a04a8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/17/2018
ms.locfileid: "49377859"
---
# <a name="app-settings-reference-for-azure-functions"></a>Reference k nastavení aplikací pro službu Azure Functions

Nastavení aplikace v aplikaci function app obsahovat globální konfiguraci možností, které ovlivňují všechny funkce pro tuto aplikaci function app. Když spouštíte místně, tato nastavení jsou v [proměnné prostředí](functions-run-local.md#local-settings-file). Tento článek uvádí nastavení aplikace, které jsou k dispozici v aplikace function App.

[!INCLUDE [Function app settings](../../includes/functions-app-settings.md)]

Existují další možnosti globální konfiguraci v [host.json](functions-host-json.md) souboru a [local.settings.json](functions-run-local.md#local-settings-file) souboru.

## <a name="appinsightsinstrumentationkey"></a>APPINSIGHTS_INSTRUMENTATIONKEY

Instrumentační klíč Application Insights při použití služby Application Insights. Zobrazit [monitorování Azure Functions](functions-monitoring.md).

|Klíč|Ukázková hodnota|
|---|------------|
|APPINSIGHTS_INSTRUMENTATIONKEY|5dbdd5e9 af77-484b-9032-64f83bb83bb|

## <a name="azurewebjobsdashboard"></a>AzureWebJobsDashboard

Připojovací řetězec účtu volitelné úložiště pro ukládání protokolů a jejich v zobrazení **monitorování** karta na portálu. Účet úložiště musí být pro obecné účely ten, který podporuje objekty BLOB, frontám a tabulkám. Zobrazit [účtu úložiště](functions-infrastructure-as-code.md#storage-account) a [požadavky na účet úložiště](functions-create-function-app-portal.md#storage-account-requirements).

|Klíč|Ukázková hodnota|
|---|------------|
|AzureWebJobsDashboard|DefaultEndpointsProtocol = https; AccountName = [název]; AccountKey = [klíč]|

> [!TIP]
> Pro činnosti a výkonu se doporučuje pro účely sledování místo AzureWebJobsDashboard APPINSIGHTS_INSTRUMENTATIONKEY a App Insights

## <a name="azurewebjobsdisablehomepage"></a>AzureWebJobsDisableHomepage

`true` znamená, že zakáže výchozí úvodní stránku, která se zobrazí pro kořenovou adresu URL aplikace function app. Výchozí hodnota je `false`.

|Klíč|Ukázková hodnota|
|---|------------|
|AzureWebJobsDisableHomepage|true (pravda)|

Když je toto nastavení aplikace vynechán nebo nastaven na `false`, zobrazí se stránka podobná následujícímu příkladu v reakci na adresu URL `<functionappname>.azurewebsites.net`.

![Cílová stránka aplikace – funkce](media/functions-app-settings/function-app-landing-page.png)

## <a name="azurewebjobsdotnetreleasecompilation"></a>AzureWebJobsDotNetReleaseCompilation

`true` znamená, že použití režimu vydání, při kompilaci kódu .NET; `false` znamená, že použití režimu ladění. Výchozí hodnota je `true`.

|Klíč|Ukázková hodnota|
|---|------------|
|AzureWebJobsDotNetReleaseCompilation|true (pravda)|

## <a name="azurewebjobsfeatureflags"></a>AzureWebJobsFeatureFlags

Čárkami oddělený seznam beta verze funkce, které chcete povolit. Beta verze funkce povolené tyto příznaky nejsou připravená pro výrobu, lze ji však povolit pro experimentální použití dříve, než přejdou za provozu.

|Klíč|Ukázková hodnota|
|---|------------|
|AzureWebJobsFeatureFlags|feature1 feature2|

## <a name="azurewebjobsscriptroot"></a>AzureWebJobsScriptRoot

Cesta ke kořenovému adresáři kde *host.json* složky souboru a funkce jsou umístěny. V aplikaci function app, výchozí hodnota je `%HOME%\site\wwwroot`.

|Klíč|Ukázková hodnota|
|---|------------|
|AzureWebJobsScriptRoot|%Home%\site\wwwroot|

## <a name="azurewebjobssecretstoragetype"></a>AzureWebJobsSecretStorageType

Určuje úložiště nebo zprostředkovatele má být použit pro úložiště klíčů. V současné době jsou podporované úložiště objektů blob ("Blob") a systém souborů ("zakázáno"). Výchozí hodnota je systém souborů ("zakázáno").

|Klíč|Ukázková hodnota|
|---|------------|
|AzureWebJobsSecretStorageType|zakázáno|

## <a name="azurewebjobsstorage"></a>AzureWebJobsStorage

Modul runtime Azure Functions používá tento připojovací řetězec účtu úložiště pro všechny funkce kromě funkcí aktivovanou protokolem HTTP. Účet úložiště musí být pro obecné účely ten, který podporuje objekty BLOB, frontám a tabulkám. Zobrazit [účtu úložiště](functions-infrastructure-as-code.md#storage-account) a [požadavky na účet úložiště](functions-create-function-app-portal.md#storage-account-requirements).

|Klíč|Ukázková hodnota|
|---|------------|
|AzureWebJobsStorage|DefaultEndpointsProtocol = https; AccountName = [název]; AccountKey = [klíč]|

## <a name="azurewebjobstypescriptpath"></a>AzureWebJobs_TypeScriptPath

Cestu ke kompilátoru používá pro TypeScript. Umožňuje přepsat výchozí hodnotu, pokud je potřeba.

|Klíč|Ukázková hodnota|
|---|------------|
|AzureWebJobs_TypeScriptPath|%Home%\typescript|

## <a name="functionappeditmode"></a>FUNKCE\_APLIKACE\_UPRAVIT\_REŽIMU

Platné hodnoty jsou "readwrite" a "jen pro čtení".

|Klíč|Ukázková hodnota|
|---|------------|
|FUNKCE\_APLIKACE\_UPRAVIT\_REŽIMU|jen pro čtení|

## <a name="functionsextensionversion"></a>FUNKCE\_ROZŠÍŘENÍ\_VERZE

Verze modulu runtime Functions pro použití v této aplikace function app. Tilda s hlavní verzí znamená, že používat nejnovější verzi Tato hlavní verze (například "~ 2"). Když jsou k dispozici nová verze pro stejný hlavní verze, jsou automaticky nainstalovány do aplikace function App. Chcete-li připnout aplikaci na konkrétní verzi, použijte celé číslo verze (například "2.0.12345"). Výchozí hodnota je "~ 2". Hodnota `~1` připíná vaší aplikace na verzi 1.x modulu runtime.

|Klíč|Ukázková hodnota|
|---|------------|
|FUNKCE\_ROZŠÍŘENÍ\_VERZE|~ 2|

## <a name="functionsworkerruntime"></a>FUNKCE\_PRACOVNÍHO PROCESU\_MODULU RUNTIME

Pracovního procesu CLR pro načtení do aplikace function App.  To bude odpovídat jazyku používán ve vaší aplikaci (například "dotnet"). Pro funkce v několika jazycích, je potřeba publikovat je do více aplikacemi, každý s odpovídající hodnotou modulu runtime pracovního procesu.  Platné hodnoty jsou `dotnet` (C# /F #), `node` (JavaScript), a `java` (Java).

|Klíč|Ukázková hodnota|
|---|------------|
|FUNKCE\_PRACOVNÍHO PROCESU\_MODULU RUNTIME|DotNet|

## <a name="websitecontentazurefileconnectionstring"></a>WEBSITE_CONTENTAZUREFILECONNECTIONSTRING

Pro pouze plány consumption. Připojovací řetězec pro účet úložiště, kde jsou uloženy kód aplikace funkcí a konfigurace. Zobrazit [vytvořit aplikaci function app](functions-infrastructure-as-code.md#create-a-function-app).

|Klíč|Ukázková hodnota|
|---|------------|
|WEBSITE_CONTENTAZUREFILECONNECTIONSTRING|DefaultEndpointsProtocol = https; AccountName = [název]; AccountKey = [klíč]|

## <a name="websitecontentshare"></a>WEB\_CONTENTSHARE

Pro pouze plány consumption. Cesta k souboru kódu aplikace funkcí a konfigurace. Použít s WEBSITE_CONTENTAZUREFILECONNECTIONSTRING. Výchozí hodnota je jedinečný řetězec, který začíná názvem aplikace function app. Zobrazit [vytvořit aplikaci function app](functions-infrastructure-as-code.md#create-a-function-app).

|Klíč|Ukázková hodnota|
|---|------------|
|WEBSITE_CONTENTSHARE|functionapp091999e2|

## <a name="websitemaxdynamicapplicationscaleout"></a>WEB\_MAXIMÁLNÍ\_DYNAMICKÉ\_APLIKACE\_ŠKÁLOVÁNÍ\_NAVÝŠENÍ KAPACITY

Maximální počet instancí, které aplikace function app můžete horizontální navýšení kapacity na. Výchozí hodnota je neomezený.

> [!NOTE]
> Toto nastavení je ve verzi preview funkce – a pouze spolehlivé if nastaven na hodnotu < = 5

|Klíč|Ukázková hodnota|
|---|------------|
|WEB\_MAXIMÁLNÍ\_DYNAMICKÉ\_APLIKACE\_ŠKÁLOVÁNÍ\_NAVÝŠENÍ KAPACITY|5|

## <a name="websitenodedefaultversion"></a>WEB\_UZEL\_DEFAULT_VERSION

Výchozí hodnota je "8.11.1".

|Klíč|Ukázková hodnota|
|---|------------|
|WEB\_UZEL\_DEFAULT_VERSION|8.11.1|

## <a name="websiterunfrompackage"></a>WEB\_SPUSTIT\_FROM\_BALÍČKU

Umožňuje aplikaci function app pro spuštění ze souboru balíčku připojené.

|Klíč|Ukázková hodnota|
|---|------------|
|WEB\_SPUSTIT\_FROM\_BALÍČKU|1|

Platné hodnoty jsou buď adresu URL, který se přeloží do umístění souboru balíčku nasazení, nebo `1`. Pokud je nastavena na `1`, velikost balíčku musí být v `d:\home\data\SitePackages` složky. Při použití s tímto nastavením zip nasazení, balíček je automaticky odeslána do tohoto umístění. Ve verzi preview, se toto nastavení s názvem `WEBSITE_RUN_FROM_ZIP`. Další informace najdete v tématu [spouštět funkce ze souboru balíčku](run-functions-from-deployment-package.md).

## <a name="next-steps"></a>Další postup

[Zjistěte, jak aktualizovat nastavení aplikace](functions-how-to-use-azure-function-app-settings.md#manage-app-service-settings)

[Zobrazit globální nastavení v souboru host.json](functions-host-json.md)

[Viz další nastavení aplikace pro aplikace služby App Service](https://github.com/projectkudu/kudu/wiki/Configurable-settings)
