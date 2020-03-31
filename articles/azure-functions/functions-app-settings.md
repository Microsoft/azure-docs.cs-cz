---
title: Referenční materiály k nastavení aplikací pro Azure Functions
description: Referenční dokumentace pro nastavení aplikace Azure Functions nebo proměnné prostředí.
ms.topic: conceptual
ms.date: 09/22/2018
ms.openlocfilehash: 3853ccbfd492bfaf4a82d62e6d31ab938285ee2e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79277827"
---
# <a name="app-settings-reference-for-azure-functions"></a>Referenční materiály k nastavení aplikací pro Azure Functions

Nastavení aplikace v aplikaci funkce obsahují možnosti globální konfigurace, které ovlivňují všechny funkce pro tuto aplikaci funkce. Při místním spuštění jsou tato nastavení přístupná jako [proměnné místního prostředí](functions-run-local.md#local-settings-file). V tomto článku jsou uvedena nastavení aplikací, která jsou k dispozici v aplikacích funkcí.

[!INCLUDE [Function app settings](../../includes/functions-app-settings.md)]

V souboru [host.json](functions-host-json.md) a v souboru [local.settings.json](functions-run-local.md#local-settings-file) jsou další možnosti globální konfigurace.

## <a name="appinsights_instrumentationkey"></a>APPINSIGHTS_INSTRUMENTATIONKEY

Klíč instrumentace Application Insights, pokud používáte Application Insights. Viz [Monitorování funkcí Azure](functions-monitoring.md).

|Klíč|Ukázková hodnota|
|---|------------|
|APPINSIGHTS_INSTRUMENTATIONKEY|5dbdd5e9-af77-484b-9032-64f83bb83bb|

## <a name="azure_functions_environment"></a>AZURE_FUNCTIONS_ENVIRONMENT

Ve verzi 2.x a novějších verzích běhu Functions konfiguruje chování aplikací na základě prostředí runtime. Tato hodnota se [čte během inicializace](https://github.com/Azure/azure-functions-host/blob/dev/src/WebJobs.Script.WebHost/Program.cs#L43). Můžete nastavit `AZURE_FUNCTIONS_ENVIRONMENT` libovolnou hodnotu, ale jsou [podporovány tři hodnoty:](/dotnet/api/microsoft.aspnetcore.hosting.environmentname) [Vývoj](/dotnet/api/microsoft.aspnetcore.hosting.environmentname.development), [Pracovní](/dotnet/api/microsoft.aspnetcore.hosting.environmentname.staging)a [Produkční .](/dotnet/api/microsoft.aspnetcore.hosting.environmentname.production) Když `AZURE_FUNCTIONS_ENVIRONMENT` není nastavená, ve `Development` výchozím nastavení se `Production` použije v místním prostředí a v Azure. Toto nastavení by `ASPNETCORE_ENVIRONMENT` mělo být použito místo k nastavení prostředí runtime. 

## <a name="azurewebjobsdashboard"></a>AzureWebJobsDashboard

Volitelný připojovací řetězec účtu úložiště pro ukládání protokolů a jejich zobrazení na kartě **Monitorování** na portálu. Toto nastavení platí jenom pro aplikace, které cílí na verzi 1.x runtime Azure Functions. Účet úložiště musí být univerzální, který podporuje objekty BLOB, fronty a tabulky. Další informace najdete v [tématu Požadavky na účet úložiště](storage-considerations.md#storage-account-requirements).

|Klíč|Ukázková hodnota|
|---|------------|
|AzureWebJobsDashboard|Výchozí EndpointsProtocol=https; AccountName=<name>; AccountKey=<key>|

> [!NOTE]
> Pro lepší výkon a zkušenosti, runtime verze 2.x a novější verze `AzureWebJobsDashboard`používají APPINSIGHTS_INSTRUMENTATIONKEY a App Insights pro monitorování namísto .

## <a name="azurewebjobsdisablehomepage"></a>AzureWebJobsDisableDomovpage

`true`znamená zakázat výchozí vstupní stránku, která se zobrazí pro kořenovou adresu URL aplikace funkce. Výchozí je `false`.

|Klíč|Ukázková hodnota|
|---|------------|
|AzureWebJobsDisableDomovpage|true|

Pokud je toto nastavení aplikace `false`vynecháno nebo nastaveno na , zobrazí se `<functionappname>.azurewebsites.net`v reakci na adresu URL stránka podobná následujícímu příkladu .

![Vstupní stránka aplikace function](media/functions-app-settings/function-app-landing-page.png)

## <a name="azurewebjobsdotnetreleasecompilation"></a>AzureWebJobsDotNetReleaseCompilation

`true`znamená použít režim uvolnění při kompilaci kódu .NET; `false` znamená použít režim ladění. Výchozí je `true`.

|Klíč|Ukázková hodnota|
|---|------------|
|AzureWebJobsDotNetReleaseCompilation|true|

## <a name="azurewebjobsfeatureflags"></a>AzureWebJobsFeatureFlags

Seznam funkcí beta verze oddělených čárkami, které chcete povolit. Funkce beta verze povolené těmito příznaky nejsou připraveny k výrobě, ale mohou být povoleny pro experimentální použití před jejich vysíláním.

|Klíč|Ukázková hodnota|
|---|------------|
|AzureWebJobsFeatureFlags|feature1,feature2|

## <a name="azurewebjobssecretstoragetype"></a>AzureWebJobsSecretStorageType

Určuje úložiště nebo zprostředkovatele, který má být používán pro úložiště klíčů. V současné době jsou podporované úložiště objektu blob úložiště ("Objekt blob") a místní systém souborů ("Soubory"). Výchozí hodnota je objekt blob ve verzi 2 a systém souborů ve verzi 1.

|Klíč|Ukázková hodnota|
|---|------------|
|AzureWebJobsSecretStorageType|Soubory|

## <a name="azurewebjobsstorage"></a>AzureWebJobsStorage

Runtime Azure Functions používá tento připojovací řetězec účtu úložiště pro všechny funkce s výjimkou funkcí aktivovaných protokolem HTTP. Účet úložiště musí být univerzální, který podporuje objekty BLOB, fronty a tabulky. Viz Požadavky [na](storage-considerations.md#storage-account-requirements)účet [úložiště](functions-infrastructure-as-code.md#storage-account) a účet úložiště .

|Klíč|Ukázková hodnota|
|---|------------|
|AzureWebJobsStorage|Výchozí EndpointsProtocol=https; AccountName=[název]; AccountKey=[klíč]|

## <a name="azurewebjobs_typescriptpath"></a>AzureWebJobs_TypeScriptPath

Cesta k kompilátoru použitému pro TypeScript. Umožňuje přepsat výchozí nastavení, pokud potřebujete.

|Klíč|Ukázková hodnota|
|---|------------|
|AzureWebJobs_TypeScriptPath|%HOME%\psací stroj|

## <a name="function_app_edit_mode"></a>REŽIM\_\_ÚPRAVY\_APLIKACE FUNKCE

Určuje, jestli je povolené úpravy na webu Azure Portal. Platné hodnoty jsou "write" a "readonly".

|Klíč|Ukázková hodnota|
|---|------------|
|REŽIM\_\_ÚPRAVY\_APLIKACE FUNKCE|readonly|

## <a name="functions_extension_version"></a>VERZE\_\_ROZŠÍŘENÍ FUNKCÍ

Verze functions runtime pro použití v této aplikaci funkce. Vlnovka s hlavní verzí znamená použít nejnovější verzi této hlavní verze (například "~2"). Pokud jsou k dispozici nové verze pro stejnou hlavní verzi, jsou automaticky nainstalovány v aplikaci funkce. Chcete-li aplikaci připnout na konkrétní verzi, použijte celé číslo verze (například "2.0.12345"). Výchozí hodnota je "~2". Hodnota `~1` připne vaši aplikaci na verzi 1.x běhu.

|Klíč|Ukázková hodnota|
|---|------------|
|VERZE\_\_ROZŠÍŘENÍ FUNKCÍ|~2|

## <a name="functions_v2_compatibility_mode"></a>FUNKCE\_\_V2\_REŽIM KOMPATIBILITY

Toto nastavení umožňuje spuštění aplikace funkce v režimu kompatibilním s verzí 2.x v době runtime verze 3.x. Toto nastavení použijte pouze v případě, že při upgradu aplikace funkce dojde k problémům [z verze 2.x na 3.x běhu](functions-versions.md#migrating-from-2x-to-3x). 

>[!IMPORTANT]
> Toto nastavení je určeno pouze jako krátkodobé řešení při aktualizaci aplikace tak, aby správně fungovala ve verzi 3.x. Toto nastavení je podporováno, pokud [je podporován modul runtime 2.x](functions-versions.md). Pokud narazíte na problémy, které brání spuštění aplikace ve verzi 3.x bez použití tohoto nastavení, [nahlaste svůj problém](https://github.com/Azure/azure-functions-host/issues/new?template=Bug_report.md).

Vyžaduje, aby [verze ROZŠÍŘENÍ\_\_FUNKCE](functions-app-settings.md#functions_extension_version) byla nastavena na `~3`.

|Klíč|Ukázková hodnota|
|---|------------|
|FUNKCE\_\_V2\_REŽIM KOMPATIBILITY|true|

## <a name="functions_worker_process_count"></a>POČET\_\_PRACOVNÍCH PROCESŮ\_FUNKCE

Určuje maximální počet pracovních procesů v jazyce `1`s výchozí hodnotou aplikace . Maximální povolená `10`hodnota je . Vyvolání funkcí jsou rovnoměrně rozdělena mezi procesy jazykového pracovního procesu. Jazykové pracovní procesy jsou zplozeny každých\_10 sekund, dokud není dosaženo počtu nastavených funkcemi POČET PRACOVNÍCH\_PROCESŮ.\_ Použití více jazykových pracovních procesů není stejné jako [škálování](functions-scale.md). Zvažte použití tohoto nastavení, pokud vaše úloha má kombinaci vyvolání vázaných na procesor a vstupně-výstupních vod. Toto nastavení platí pro všechny non-.NET jazyky.

|Klíč|Ukázková hodnota|
|---|------------|
|POČET\_\_PRACOVNÍCH PROCESŮ\_FUNKCE|2|


## <a name="functions_worker_runtime"></a>FUNKCE\_\_PRACOVNÍHO BĚHU

Za běhu pracovního procesu jazyka, který se má načíst v aplikaci funkce.  To bude odpovídat jazyk u používaného ve vaší aplikaci (například "dotnet"). Pro funkce ve více jazycích je budete muset publikovat do více aplikací, z nichž každá má odpovídající hodnotu pracovního běhu.  Platné hodnoty `dotnet` `node` jsou (C#/F#), `java` (JavaScript/TypeScript), (Java), `python` `powershell` (PowerShell) a (Python).

|Klíč|Ukázková hodnota|
|---|------------|
|FUNKCE\_\_PRACOVNÍHO BĚHU|dotnet|

## <a name="website_contentazurefileconnectionstring"></a>WEBSITE_CONTENTAZUREFILECONNECTIONSTRING

Pouze pro plány Spotřeba & Premium. Připojovací řetězec pro účet úložiště, kde jsou uloženy kód aplikace funkce a konfigurace. Viz [Vytvoření aplikace pro funkci](functions-infrastructure-as-code.md#create-a-function-app).

|Klíč|Ukázková hodnota|
|---|------------|
|WEBSITE_CONTENTAZUREFILECONNECTIONSTRING|Výchozí EndpointsProtocol=https; AccountName=[název]; AccountKey=[klíč]|

## <a name="website_contentshare"></a>SDÍLENÍ\_OBSAHU WEBOVÝCH STRÁNEK

Pouze pro plány Spotřeba & Premium. Cesta k kódu a konfiguraci aplikace funkce. Používá se s WEBSITE_CONTENTAZUREFILECONNECTIONSTRING. Výchozí je jedinečný řetězec, který začíná názvem aplikace funkce. Viz [Vytvoření aplikace pro funkci](functions-infrastructure-as-code.md#create-a-function-app).

|Klíč|Ukázková hodnota|
|---|------------|
|WEBSITE_CONTENTSHARE|funkceapp091999e2|

## <a name="website_max_dynamic_application_scale_out"></a>MAXIMÁLNÍ\_\_VYUŽITÍ\_\_DYNAMICKÉ\_APLIKACE WEBU

Maximální počet instancí, které aplikace funkce můžete horizontální navýšení kapacity. Výchozí hodnota není omezena.

> [!NOTE]
> Toto nastavení je funkce náhledu - a pouze spolehlivé, pokud je nastaveno na hodnotu <= 5

|Klíč|Ukázková hodnota|
|---|------------|
|MAXIMÁLNÍ\_\_VYUŽITÍ\_\_DYNAMICKÉ\_APLIKACE WEBU|5|

## <a name="website_node_default_version"></a>DEFAULT_VERSION\_NODE\_WEBOVÝCH STRÁNEK

_Pouze pro Windows._  
Nastaví verzi souboru Node.js, která se použije při spuštění aplikace funkce ve Windows. Měli byste použít vlnovku (~) k tomu, aby za běhu používala nejnovější dostupnou verzi cílové hlavní verze. Pokud je například `~10`nastavena na , použije se nejnovější verze souboru Node.js 10. Pokud je hlavní verze cílená pomocí vlnovky, není nutné ji ručně aktualizovat. 

|Klíč|Ukázková hodnota|
|---|------------|
|DEFAULT_VERSION\_NODE\_WEBOVÝCH STRÁNEK|~10|

## <a name="website_run_from_package"></a>WEBOVÉ\_\_STRÁNKY\_SPUSTIT Z BALÍČKU

Umožňuje spuštění aplikace funkce z připojeného souboru balíčku.

|Klíč|Ukázková hodnota|
|---|------------|
|WEBOVÉ\_\_STRÁNKY\_SPUSTIT Z BALÍČKU|1|

Platné hodnoty jsou adresa URL, která se překládá do `1`umístění souboru balíčku nasazení, nebo . Pokud je `1`nastavena na , `d:\home\data\SitePackages` balíček musí být ve složce. Při použití nasazení zip s tímto nastavením je balíček automaticky odeslán do tohoto umístění. Ve verzi Preview bylo `WEBSITE_RUN_FROM_ZIP`toto nastavení pojmenováno . Další informace naleznete v [tématu Spuštění funkcí ze souboru balíčku](run-functions-from-deployment-package.md).

## <a name="azure_function_proxy_disable_local_call"></a>AZURE_FUNCTION_PROXY_DISABLE_LOCAL_CALL

Ve výchozím nastavení funkce proxy bude používat zástupce pro odesílání volání rozhraní API z proxy přímo do funkcí ve stejné aplikaci funkce, spíše než vytvoření nového požadavku HTTP. Toto nastavení umožňuje zakázat toto chování.

|Klíč|Hodnota|Popis|
|-|-|-|
|AZURE_FUNCTION_PROXY_DISABLE_LOCAL_CALL|true|Volání s adresou URL back-endu ukazující na funkci v místní aplikaci funkce již nebudou odesílána přímo do funkce a místo toho budou směrována zpět na front-end HTTP pro aplikaci function App|
|AZURE_FUNCTION_PROXY_DISABLE_LOCAL_CALL|false (nepravda)|Toto je výchozí hodnota. Volání s adresou URL back-endu ukazující na funkci v místní aplikaci funkcí budou přeposílána přímo této funkci|


## <a name="azure_function_proxy_backend_url_decode_slashes"></a>AZURE_FUNCTION_PROXY_BACKEND_URL_DECODE_SLASHES

Toto nastavení určuje, zda je %2F dekódováno jako lomítka v parametrech trasy, když jsou vložena do adresy URL back-endu. 

|Klíč|Hodnota|Popis|
|-|-|-|
|AZURE_FUNCTION_PROXY_BACKEND_URL_DECODE_SLASHES|true|Parametry trasy s kódovanámi lomítky je dekódují. `example.com/api%2ftest`se stane`example.com/api/test`|
|AZURE_FUNCTION_PROXY_BACKEND_URL_DECODE_SLASHES|false (nepravda)|Toto je výchozí chování. Všechny parametry trasy budou předány beze změny.|

### <a name="example"></a>Příklad

Zde je příklad proxy.json ve funkci aplikace na adrese URL myfunction.com

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
|false (nepravda)|myfunction.com/test%2fapi|example.com/test%2fapi|


## <a name="next-steps"></a>Další kroky

[Přečtěte si, jak aktualizovat nastavení aplikace.](functions-how-to-use-azure-function-app-settings.md#settings)

[Zobrazení globálního nastavení v souboru host.json](functions-host-json.md)

[Zobrazení dalších nastavení aplikací pro aplikace Služby App Service](https://github.com/projectkudu/kudu/wiki/Configurable-settings)
