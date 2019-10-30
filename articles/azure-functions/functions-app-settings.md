---
title: Referenční informace k nastavení aplikace pro Azure Functions
description: Referenční dokumentace k nastavení aplikace Azure Functions nebo k proměnným prostředí.
services: functions
author: ggailey777
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.topic: conceptual
ms.date: 09/22/2018
ms.author: glenga
ms.openlocfilehash: 896179a393b870390991a8e9942f6e7287ec5c90
ms.sourcegitcommit: f7f70c9bd6c2253860e346245d6e2d8a85e8a91b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/30/2019
ms.locfileid: "73063307"
---
# <a name="app-settings-reference-for-azure-functions"></a>Referenční informace k nastavení aplikace pro Azure Functions

Nastavení aplikace ve Function App obsahují globální možnosti konfigurace, které mají vliv na všechny funkce této aplikace Function App. Když spouštíte místně, tato nastavení jsou k dispozici jako [proměnné místního prostředí](functions-run-local.md#local-settings-file). Tento článek obsahuje seznam nastavení aplikace, která jsou k dispozici ve funkcích aplikace.

[!INCLUDE [Function app settings](../../includes/functions-app-settings.md)]

V souboru [Host. JSON](functions-host-json.md) a v souboru [Local. Settings. JSON](functions-run-local.md#local-settings-file) existují i další globální možnosti konfigurace.

## <a name="appinsights_instrumentationkey"></a>APPINSIGHTS_INSTRUMENTATIONKEY

Klíč instrumentace Application Insights, pokud používáte Application Insights. Viz [Monitor Azure Functions](functions-monitoring.md).

|Klíč|Ukázková hodnota|
|---|------------|
|APPINSIGHTS_INSTRUMENTATIONKEY|5dbdd5e9-af77-484b-9032-64f83bb83bb|

## <a name="azure_functions_environment"></a>AZURE_FUNCTIONS_ENVIRONMENT

Ve verzi 2. x modulu runtime Functions konfiguruje chování aplikace na základě běhového prostředí. Tato hodnota je [čtena při inicializaci](https://github.com/Azure/azure-functions-host/blob/dev/src/WebJobs.Script.WebHost/Program.cs#L43). Můžete nastavit `AZURE_FUNCTIONS_ENVIRONMENT` na libovolnou hodnotu, ale podporují se [tři hodnoty](/dotnet/api/microsoft.aspnetcore.hosting.environmentname) : [vývoj](/dotnet/api/microsoft.aspnetcore.hosting.environmentname.development), [Příprava](/dotnet/api/microsoft.aspnetcore.hosting.environmentname.staging)a [produkce](/dotnet/api/microsoft.aspnetcore.hosting.environmentname.production). Pokud `AZURE_FUNCTIONS_ENVIRONMENT` není nastavené, použije se výchozí nastavení `Development` v místním prostředí a `Production` v Azure. Toto nastavení by se mělo používat místo `ASPNETCORE_ENVIRONMENT` k nastavení běhového prostředí. 

## <a name="azurewebjobsdashboard"></a>AzureWebJobsDashboard

Volitelný připojovací řetězec účtu úložiště pro ukládání protokolů a jejich zobrazení na portálu na kartě **monitorování** . Účet úložiště musí být účet pro obecné účely, který podporuje objekty blob, fronty a tabulky. Viz požadavky na [účet úložiště](functions-infrastructure-as-code.md#storage-account) a [účet úložiště](functions-create-function-app-portal.md#storage-account-requirements).

|Klíč|Ukázková hodnota|
|---|------------|
|AzureWebJobsDashboard|DefaultEndpointsProtocol = https; Název účtu = [název]; AccountKey = [klíč]|

> [!TIP]
> V případě výkonu a zkušeností doporučujeme místo AzureWebJobsDashboard používat APPINSIGHTS_INSTRUMENTATIONKEY a App Insights pro monitorování.

## <a name="azurewebjobsdisablehomepage"></a>AzureWebJobsDisableHomepage

`true` znamená zakázat výchozí cílovou stránku, která se zobrazí pro kořenovou adresu URL aplikace Function App. Výchozí hodnota je `false`.

|Klíč|Ukázková hodnota|
|---|------------|
|AzureWebJobsDisableHomepage|true|

Pokud je toto nastavení aplikace vynecháno nebo je nastaveno na `false`, zobrazí se stránka podobná následujícímu příkladu jako odpověď na adresu URL `<functionappname>.azurewebsites.net`.

![Cílová stránka aplikace Function App](media/functions-app-settings/function-app-landing-page.png)

## <a name="azurewebjobsdotnetreleasecompilation"></a>AzureWebJobsDotNetReleaseCompilation

`true` znamená použití režimu vydání při kompilování kódu .NET; `false` znamená použít režim ladění. Výchozí hodnota je `true`.

|Klíč|Ukázková hodnota|
|---|------------|
|AzureWebJobsDotNetReleaseCompilation|true|

## <a name="azurewebjobsfeatureflags"></a>AzureWebJobsFeatureFlags

Seznam funkcí beta oddělených čárkami, které mají být povoleny. Funkce beta verze povolené těmito příznaky nejsou připravené pro produkční prostředí, ale je možné je povolit pro experimentální použití předtím, než budou živá.

|Klíč|Ukázková hodnota|
|---|------------|
|AzureWebJobsFeatureFlags|feature1,feature2|

## <a name="azurewebjobssecretstoragetype"></a>AzureWebJobsSecretStorageType

Určuje úložiště nebo poskytovatele, které se použijí pro úložiště klíčů. V současné době jsou podporovaná úložiště BLOB Storage ("blob") a místní systém souborů ("soubory"). Výchozí hodnota je objekt BLOB ve verzi 2 a systém souborů ve verzi 1.

|Klíč|Ukázková hodnota|
|---|------------|
|AzureWebJobsSecretStorageType|Soubory|

## <a name="azurewebjobsstorage"></a>AzureWebJobsStorage

Modul runtime Azure Functions používá tento připojovací řetězec účtu úložiště pro všechny funkce s výjimkou funkcí aktivovaných protokolem HTTP. Účet úložiště musí být účet pro obecné účely, který podporuje objekty blob, fronty a tabulky. Viz požadavky na [účet úložiště](functions-infrastructure-as-code.md#storage-account) a [účet úložiště](functions-create-function-app-portal.md#storage-account-requirements).

|Klíč|Ukázková hodnota|
|---|------------|
|AzureWebJobsStorage|DefaultEndpointsProtocol = https; Název účtu = [název]; AccountKey = [klíč]|

## <a name="azurewebjobs_typescriptpath"></a>AzureWebJobs_TypeScriptPath

Cesta k kompilátoru, který se používá pro TypeScript Umožňuje přepsat výchozí, pokud potřebujete.

|Klíč|Ukázková hodnota|
|---|------------|
|AzureWebJobs_TypeScriptPath|%HOME%\typescript|

## <a name="function_app_edit_mode"></a>FUNKCE\_APP\_upravit režim\_

Určuje, zda jsou povoleny úpravy v Azure Portal. Platné hodnoty jsou "ReadOnly" a "ReadOnly".

|Klíč|Ukázková hodnota|
|---|------------|
|FUNKCE\_APP\_upravit režim\_|ReadOnly|

## <a name="functions_extension_version"></a>FUNKCE\_rozšíření\_verze

Verze modulu runtime Functions, který má být použit v této aplikaci Function App. Vlnovka s hlavní verzí znamená použití nejnovější verze této hlavní verze (například ~ 2). Pokud jsou k dispozici nové verze pro stejnou hlavní verzi, automaticky se nainstalují do aplikace Function App. Pokud chcete aplikaci připnout na konkrétní verzi, použijte úplné číslo verze (například "2.0.12345"). Výchozí hodnota je "~ 2". Hodnota `~1` připnete aplikaci k verzi 1. x modulu runtime.

|Klíč|Ukázková hodnota|
|---|------------|
|FUNKCE\_rozšíření\_verze|~ 2|

## <a name="functions_worker_process_count"></a>POČET funkcí\_pracovní proces\_procesu\_

Určuje maximální počet pracovních procesů jazyka s výchozí hodnotou `1`. Maximální povolená hodnota je `10`. Volání funkcí jsou rovnoměrně rozdělena mezi pracovní procesy jazyka. Pracovní procesy jazyka se spouští každých 10 sekund, dokud se nedosáhne počtu, který je nastaven funkcemi\_pracovní proces\_procesu\_. Použití více pracovních procesů jazyka není stejné jako [škálování](functions-scale.md). Zvažte použití tohoto nastavení, pokud má vaše úloha kombinaci asynchronních volání vázaných na procesor a vstupně-výstupní operace. Toto nastavení platí pro všechny non-.NET jazyky.

|Klíč|Ukázková hodnota|
|---|------------|
|POČET funkcí\_pracovní proces\_procesu\_|2|


## <a name="functions_worker_runtime"></a>FUNCTIONs\_WORKer\_RUNTIME

Běhový modul runtime jazyka, který se má načíst do aplikace Function App.  Bude odpovídat jazyku používanému ve vaší aplikaci (například "dotnet"). V případě funkcí v různých jazycích je budete muset publikovat do více aplikací, z nichž každá má odpovídající běhovou hodnotu pracovního procesu.  Platné hodnoty jsou `dotnet` (C#/F#), `node` (JavaScript/TypeScript), `java` (Java), `powershell` (PowerShell) a `python` (Python).

|Klíč|Ukázková hodnota|
|---|------------|
|FUNCTIONs\_WORKer\_RUNTIME|dotnet|

## <a name="website_contentazurefileconnectionstring"></a>WEBSITE_CONTENTAZUREFILECONNECTIONSTRING

Pro spotřebu & jenom plánů Premium. Připojovací řetězec pro účet úložiště, ve kterém je uložený kód a konfigurace aplikace Function App Viz [Vytvoření aplikace Function App](functions-infrastructure-as-code.md#create-a-function-app).

|Klíč|Ukázková hodnota|
|---|------------|
|WEBSITE_CONTENTAZUREFILECONNECTIONSTRING|DefaultEndpointsProtocol = https; Název účtu = [název]; AccountKey = [klíč]|

## <a name="website_contentshare"></a>Web\_CONTENTSHARE

Pro spotřebu & jenom plánů Premium. Cesta k souboru s kódem a konfigurací aplikace Function App Používá se s WEBSITE_CONTENTAZUREFILECONNECTIONSTRING. Výchozí hodnota je jedinečný řetězec, který začíná názvem aplikace Function App. Viz [Vytvoření aplikace Function App](functions-infrastructure-as-code.md#create-a-function-app).

|Klíč|Ukázková hodnota|
|---|------------|
|WEBSITE_CONTENTSHARE|functionapp091999e2|

## <a name="website_max_dynamic_application_scale_out"></a>Web\_MAX\_DYNAMIC\_aplikace\_ŠKÁLOVÁNÍ\_

Maximální počet instancí, na které může aplikace Function App navýšit horizontální navýšení kapacity. Výchozí hodnota není nijak omezena.

> [!NOTE]
> Toto nastavení je funkce ve verzi Preview a je spolehlivá, jenom pokud je nastavená hodnota < = 5.

|Klíč|Ukázková hodnota|
|---|------------|
|Web\_MAX\_DYNAMIC\_aplikace\_ŠKÁLOVÁNÍ\_|5|

## <a name="website_node_default_version"></a>Web\_NODE\_DEFAULT_VERSION

_Pouze Windows._  
Nastaví verzi Node. js, která se má použít při spuštění aplikace Function App ve Windows. Chcete-li, aby modul runtime používal nejnovější dostupnou verzi cílené hlavní verze, použijte vlnovku (~). Například pokud je nastavena na `~10`, je použita nejnovější verze Node. js 10. V případě, že je hlavní verze cílena na vlnovku, nemusíte ručně aktualizovat dílčí verzi. 

|Klíč|Ukázková hodnota|
|---|------------|
|Web\_NODE\_DEFAULT_VERSION|~ 10|

## <a name="website_run_from_package"></a>Web\_spustit\_z\_balíčku

Povolí spuštění aplikace Function App z připojeného souboru balíčku.

|Klíč|Ukázková hodnota|
|---|------------|
|Web\_spustit\_z\_balíčku|1\. místo|

Platné hodnoty jsou buď adresy URL, které se přeloží na umístění souboru balíčku pro nasazení, nebo `1`. Když nastavíte `1`, balíček musí být ve složce `d:\home\data\SitePackages`. Při použití nasazení zip s tímto nastavením se balíček automaticky nahraje do tohoto umístění. Ve verzi Preview bylo toto nastavení pojmenováno `WEBSITE_RUN_FROM_ZIP`. Další informace najdete v tématu [spuštění funkcí ze souboru balíčku](run-functions-from-deployment-package.md).

## <a name="azure_function_proxy_disable_local_call"></a>AZURE_FUNCTION_PROXY_DISABLE_LOCAL_CALL

Ve výchozím nastavení budou proxy služby funkcí používat zástupce pro odesílání volání rozhraní API z proxy serverů přímo do funkcí ve stejné Function App namísto vytváření nového požadavku HTTP. Toto nastavení umožňuje toto chování zakázat.

|Klíč|Hodnota|Popis|
|-|-|-|
|AZURE_FUNCTION_PROXY_DISABLE_LOCAL_CALL|true|Volání s adresou URL back-endu ukazující na funkci v místním Function App již nebudou odesílána přímo do funkce a místo toho budou přesměrována zpět na front-end protokolu HTTP pro Function App|
|AZURE_FUNCTION_PROXY_DISABLE_LOCAL_CALL|false|Toto je výchozí hodnota. Volání s adresou URL back-endu ukazující na funkci v místním Function App budou předána přímo této funkci.|


## <a name="azure_function_proxy_backend_url_decode_slashes"></a>AZURE_FUNCTION_PROXY_BACKEND_URL_DECODE_SLASHES

Toto nastavení určuje, jestli se% 2F dekóduje jako lomítka v parametrech směrování při jejich vložení do adresy URL back-endu. 

|Klíč|Hodnota|Popis|
|-|-|-|
|AZURE_FUNCTION_PROXY_BACKEND_URL_DECODE_SLASHES|true|Parametry směrování s kódovanými lomítky budou mít dekódované. `example.com/api%2ftest` se stanou `example.com/api/test`|
|AZURE_FUNCTION_PROXY_BACKEND_URL_DECODE_SLASHES|false|Toto je výchozí chování. Všechny parametry tras budou předány beze změny.|

### <a name="example"></a>Příklad:

Tady je příklad proxy. JSON ve Function App na adrese URL myfunction.com

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
|Dekódování adresy URL|Vstup|Výstup|
|-|-|-|
|true|myfunction.com/test%2fapi|example.com/test/api
|false|myfunction.com/test%2fapi|example.com/test%2fapi|


## <a name="next-steps"></a>Další kroky

[Informace o tom, jak aktualizovat nastavení aplikace](functions-how-to-use-azure-function-app-settings.md#settings)

[Viz globální nastavení v souboru Host. JSON.](functions-host-json.md)

[Zobrazit další nastavení aplikací pro App Service aplikace](https://github.com/projectkudu/kudu/wiki/Configurable-settings)
