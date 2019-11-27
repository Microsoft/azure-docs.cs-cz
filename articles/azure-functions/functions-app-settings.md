---
title: Reference k nastavení aplikací pro službu Azure Functions
description: Referenční dokumentace pro nastavení aplikace Azure Functions nebo proměnné prostředí.
ms.topic: conceptual
ms.date: 09/22/2018
ms.openlocfilehash: 35ecebfb1956422470bf20e6d510543897ca0910
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/20/2019
ms.locfileid: "74227386"
---
# <a name="app-settings-reference-for-azure-functions"></a>Reference k nastavení aplikací pro službu Azure Functions

Nastavení aplikace v aplikaci function app obsahovat globální konfiguraci možností, které ovlivňují všechny funkce pro tuto aplikaci function app. Když spouštíte místně, tato nastavení jsou k dispozici jako [proměnné místního prostředí](functions-run-local.md#local-settings-file). Tento článek uvádí nastavení aplikace, které jsou k dispozici v aplikace function App.

[!INCLUDE [Function app settings](../../includes/functions-app-settings.md)]

V souboru [Host. JSON](functions-host-json.md) a v souboru [Local. Settings. JSON](functions-run-local.md#local-settings-file) existují i další globální možnosti konfigurace.

## <a name="appinsights_instrumentationkey"></a>APPINSIGHTS_INSTRUMENTATIONKEY

Instrumentační klíč Application Insights při použití služby Application Insights. Viz [Monitor Azure Functions](functions-monitoring.md).

|Klíč|Ukázková hodnota|
|---|------------|
|APPINSIGHTS_INSTRUMENTATIONKEY|5dbdd5e9 af77-484b-9032-64f83bb83bb|

## <a name="azure_functions_environment"></a>AZURE_FUNCTIONS_ENVIRONMENT

Ve verzi 2. x modulu runtime Functions konfiguruje chování aplikace na základě běhového prostředí. Tato hodnota je [čtena při inicializaci](https://github.com/Azure/azure-functions-host/blob/dev/src/WebJobs.Script.WebHost/Program.cs#L43). Můžete nastavit `AZURE_FUNCTIONS_ENVIRONMENT` na libovolnou hodnotu, ale podporují se [tři hodnoty](/dotnet/api/microsoft.aspnetcore.hosting.environmentname) : [vývoj](/dotnet/api/microsoft.aspnetcore.hosting.environmentname.development), [Příprava](/dotnet/api/microsoft.aspnetcore.hosting.environmentname.staging)a [produkce](/dotnet/api/microsoft.aspnetcore.hosting.environmentname.production). Pokud `AZURE_FUNCTIONS_ENVIRONMENT` není nastavené, použije se výchozí nastavení `Development` v místním prostředí a `Production` v Azure. Toto nastavení by se mělo používat místo `ASPNETCORE_ENVIRONMENT` k nastavení běhového prostředí. 

## <a name="azurewebjobsdashboard"></a>AzureWebJobsDashboard

Volitelný připojovací řetězec účtu úložiště pro ukládání protokolů a jejich zobrazení na portálu na kartě **monitorování** . Účet úložiště musí být pro obecné účely ten, který podporuje objekty BLOB, frontám a tabulkám. Viz požadavky na [účet úložiště](functions-infrastructure-as-code.md#storage-account) a [účet úložiště](functions-create-function-app-portal.md#storage-account-requirements).

|Klíč|Ukázková hodnota|
|---|------------|
|AzureWebJobsDashboard|DefaultEndpointsProtocol = https; AccountName = [název]; AccountKey = [klíč]|

> [!TIP]
> Pro činnosti a výkonu se doporučuje pro účely sledování místo AzureWebJobsDashboard APPINSIGHTS_INSTRUMENTATIONKEY a App Insights

## <a name="azurewebjobsdisablehomepage"></a>AzureWebJobsDisableHomepage

`true` znamená zakázat výchozí cílovou stránku, která se zobrazí pro kořenovou adresu URL aplikace Function App. Výchozí je `false`.

|Klíč|Ukázková hodnota|
|---|------------|
|AzureWebJobsDisableHomepage|true (pravda)|

Pokud je toto nastavení aplikace vynecháno nebo je nastaveno na `false`, zobrazí se stránka podobná následujícímu příkladu jako odpověď na adresu URL `<functionappname>.azurewebsites.net`.

![Cílová stránka aplikace – funkce](media/functions-app-settings/function-app-landing-page.png)

## <a name="azurewebjobsdotnetreleasecompilation"></a>AzureWebJobsDotNetReleaseCompilation

`true` znamená použití režimu vydání při kompilování kódu .NET; `false` znamená použít režim ladění. Výchozí je `true`.

|Klíč|Ukázková hodnota|
|---|------------|
|AzureWebJobsDotNetReleaseCompilation|true (pravda)|

## <a name="azurewebjobsfeatureflags"></a>AzureWebJobsFeatureFlags

Čárkami oddělený seznam beta verze funkce, které chcete povolit. Beta verze funkce povolené tyto příznaky nejsou připravená pro výrobu, lze ji však povolit pro experimentální použití dříve, než přejdou za provozu.

|Klíč|Ukázková hodnota|
|---|------------|
|AzureWebJobsFeatureFlags|feature1 feature2|

## <a name="azurewebjobssecretstoragetype"></a>AzureWebJobsSecretStorageType

Určuje úložiště nebo zprostředkovatele má být použit pro úložiště klíčů. V současné době jsou podporované úložiště úložiště objektů blob ("Blob") a místního systému souborů ("Files"). Výchozí hodnota je objekt blob ve verzi 2 a systému souborů verze 1.

|Klíč|Ukázková hodnota|
|---|------------|
|AzureWebJobsSecretStorageType|Soubory|

## <a name="azurewebjobsstorage"></a>AzureWebJobsStorage

Modul runtime Azure Functions používá tento připojovací řetězec účtu úložiště pro všechny funkce kromě funkcí aktivovanou protokolem HTTP. Účet úložiště musí být pro obecné účely ten, který podporuje objekty BLOB, frontám a tabulkám. Viz požadavky na [účet úložiště](functions-infrastructure-as-code.md#storage-account) a [účet úložiště](functions-create-function-app-portal.md#storage-account-requirements).

|Klíč|Ukázková hodnota|
|---|------------|
|AzureWebJobsStorage|DefaultEndpointsProtocol = https; AccountName = [název]; AccountKey = [klíč]|

## <a name="azurewebjobs_typescriptpath"></a>AzureWebJobs_TypeScriptPath

Cestu ke kompilátoru používá pro TypeScript. Umožňuje přepsat výchozí hodnotu, pokud je potřeba.

|Klíč|Ukázková hodnota|
|---|------------|
|AzureWebJobs_TypeScriptPath|%Home%\typescript|

## <a name="function_app_edit_mode"></a>FUNKCE\_APP\_upravit režim\_

Určuje, zda jsou povoleny úpravy v Azure Portal. Platné hodnoty jsou "readwrite" a "jen pro čtení".

|Klíč|Ukázková hodnota|
|---|------------|
|FUNKCE\_APP\_upravit režim\_|jen pro čtení|

## <a name="functions_extension_version"></a>FUNKCE\_rozšíření\_verze

Verze modulu runtime Functions pro použití v této aplikace function app. Tilda s hlavní verzí znamená, že používat nejnovější verzi Tato hlavní verze (například "~ 2"). Když jsou k dispozici nová verze pro stejný hlavní verze, jsou automaticky nainstalovány do aplikace function App. Chcete-li připnout aplikaci na konkrétní verzi, použijte celé číslo verze (například "2.0.12345"). Výchozí hodnota je "~ 2". Hodnota `~1` připnete aplikaci k verzi 1. x modulu runtime.

|Klíč|Ukázková hodnota|
|---|------------|
|FUNKCE\_rozšíření\_verze|~ 2|

## <a name="functions_worker_process_count"></a>POČET funkcí\_pracovní proces\_procesu\_

Určuje maximální počet pracovních procesů jazyka s výchozí hodnotou `1`. Maximální povolená hodnota je `10`. Volání funkcí jsou rovnoměrně rozdělena mezi pracovní procesy jazyka. Pracovní procesy jazyka se spouští každých 10 sekund, dokud se nedosáhne počtu, který je nastaven funkcemi\_pracovní proces\_procesu\_. Použití více pracovních procesů jazyka není stejné jako [škálování](functions-scale.md). Zvažte použití tohoto nastavení, pokud má vaše úloha kombinaci asynchronních volání vázaných na procesor a vstupně-výstupní operace. Toto nastavení platí pro všechny non-.NET jazyky.

|Klíč|Ukázková hodnota|
|---|------------|
|POČET funkcí\_pracovní proces\_procesu\_|2|


## <a name="functions_worker_runtime"></a>FUNCTIONs\_WORKer\_RUNTIME

Pracovního procesu CLR pro načtení do aplikace function App.  To bude odpovídat jazyku používán ve vaší aplikaci (například "dotnet"). Pro funkce v několika jazycích, je potřeba publikovat je do více aplikacemi, každý s odpovídající hodnotou modulu runtime pracovního procesu.  Platné hodnoty jsou `dotnet` (C#/F#), `node` (JavaScript/TypeScript), `java` (Java), `powershell` (PowerShell) a `python` (Python).

|Klíč|Ukázková hodnota|
|---|------------|
|FUNCTIONs\_WORKer\_RUNTIME|DotNet|

## <a name="website_contentazurefileconnectionstring"></a>WEBSITE_CONTENTAZUREFILECONNECTIONSTRING

Pro spotřebu & jenom plánů Premium. Připojovací řetězec pro účet úložiště, kde jsou uloženy kód aplikace funkcí a konfigurace. Viz [Vytvoření aplikace Function App](functions-infrastructure-as-code.md#create-a-function-app).

|Klíč|Ukázková hodnota|
|---|------------|
|WEBSITE_CONTENTAZUREFILECONNECTIONSTRING|DefaultEndpointsProtocol = https; AccountName = [název]; AccountKey = [klíč]|

## <a name="website_contentshare"></a>Web\_CONTENTSHARE

Pro spotřebu & jenom plánů Premium. Cesta k souboru kódu aplikace funkcí a konfigurace. Použít s WEBSITE_CONTENTAZUREFILECONNECTIONSTRING. Výchozí hodnota je jedinečný řetězec, který začíná názvem aplikace function app. Viz [Vytvoření aplikace Function App](functions-infrastructure-as-code.md#create-a-function-app).

|Klíč|Ukázková hodnota|
|---|------------|
|WEBSITE_CONTENTSHARE|functionapp091999e2|

## <a name="website_max_dynamic_application_scale_out"></a>Web\_MAX\_DYNAMIC\_aplikace\_ŠKÁLOVÁNÍ\_

Maximální počet instancí, které aplikace function app můžete horizontální navýšení kapacity na. Výchozí hodnota je neomezený.

> [!NOTE]
> Toto nastavení je ve verzi preview funkce – a pouze spolehlivé if nastaven na hodnotu < = 5

|Klíč|Ukázková hodnota|
|---|------------|
|Web\_MAX\_DYNAMIC\_aplikace\_ŠKÁLOVÁNÍ\_|5|

## <a name="website_node_default_version"></a>\_\_uzlů webu DEFAULT_VERSION

_Pouze Windows._  
Nastaví verzi Node. js, která se má použít při spuštění aplikace Function App ve Windows. Chcete-li, aby modul runtime používal nejnovější dostupnou verzi cílené hlavní verze, použijte vlnovku (~). Například pokud je nastavena na `~10`, je použita nejnovější verze Node. js 10. V případě, že je hlavní verze cílena na vlnovku, nemusíte ručně aktualizovat dílčí verzi. 

|Klíč|Ukázková hodnota|
|---|------------|
|\_\_uzlů webu DEFAULT_VERSION|~ 10|

## <a name="website_run_from_package"></a>Web\_spustit\_z\_balíčku

Umožňuje aplikaci function app pro spuštění ze souboru balíčku připojené.

|Klíč|Ukázková hodnota|
|---|------------|
|Web\_spustit\_z\_balíčku|1|

Platné hodnoty jsou buď adresy URL, které se přeloží na umístění souboru balíčku pro nasazení, nebo `1`. Když nastavíte `1`, balíček musí být ve složce `d:\home\data\SitePackages`. Při použití s tímto nastavením zip nasazení, balíček je automaticky odeslána do tohoto umístění. Ve verzi Preview bylo toto nastavení pojmenováno `WEBSITE_RUN_FROM_ZIP`. Další informace najdete v tématu [spuštění funkcí ze souboru balíčku](run-functions-from-deployment-package.md).

## <a name="azure_function_proxy_disable_local_call"></a>AZURE_FUNCTION_PROXY_DISABLE_LOCAL_CALL

Ve výchozím nastavení proxy služby Functions bude využívat zástupce k odesílání volání rozhraní API z proxy přímo do funkce ve stejné aplikaci funkce a nevytvářejte nový požadavek HTTP. Toto nastavení umožňuje zakázat toto chování.

|Klíč|Hodnota|Popis|
|-|-|-|
|AZURE_FUNCTION_PROXY_DISABLE_LOCAL_CALL|true (pravda)|Volání s adresou URL back-endu ukazující na funkci v místním Function App již nebudou odesílána přímo do funkce a místo toho budou přesměrována zpět na front-end protokolu HTTP pro Function App|
|AZURE_FUNCTION_PROXY_DISABLE_LOCAL_CALL|false (nepravda)|Toto je výchozí hodnota. Volání s back-end adresy url odkazující na funkci v místní aplikaci Function App se předají přímo do této funkce|


## <a name="azure_function_proxy_backend_url_decode_slashes"></a>AZURE_FUNCTION_PROXY_BACKEND_URL_DECODE_SLASHES

Toto nastavení určuje, zda je % 2F dekódovat jako ve parametry trasy vloženým do adresy URL back-endu. 

|Klíč|Hodnota|Popis|
|-|-|-|
|AZURE_FUNCTION_PROXY_BACKEND_URL_DECODE_SLASHES|true (pravda)|Parametry trasy s kódováním lomítka kliknul dekódovat. `example.com/api%2ftest` se stanou `example.com/api/test`|
|AZURE_FUNCTION_PROXY_BACKEND_URL_DECODE_SLASHES|false (nepravda)|Toto je výchozí chování. Všechny trasy, které se předají parametry beze změny|

### <a name="example"></a>Příklad

Zde je příkladu proxies.json v aplikaci function app na myfunction.com adresy URL

```JSON
{
    "$schema": "http://json.schemastore.org/proxies",
    "proxies": {
        "root": {
            "matchCondition": {
                "route": "/{*all}"
            },
            "backendUri": "example.com/{all}"
        }
    }
}
```
|Dekódování adres URL|Vstup|Výstup|
|-|-|-|
|true (pravda)|myFunction.com/test%2fapi|example.com/test/API
|false (nepravda)|myFunction.com/test%2fapi|example.com/test%2fapi|


## <a name="next-steps"></a>Další kroky

[Informace o tom, jak aktualizovat nastavení aplikace](functions-how-to-use-azure-function-app-settings.md#settings)

[Viz globální nastavení v souboru Host. JSON.](functions-host-json.md)

[Zobrazit další nastavení aplikací pro App Service aplikace](https://github.com/projectkudu/kudu/wiki/Configurable-settings)
