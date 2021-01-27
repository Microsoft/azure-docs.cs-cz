---
title: Referenční materiály k nastavení aplikací pro Azure Functions
description: Referenční dokumentace k nastavení aplikace Azure Functions nebo k proměnným prostředí.
ms.topic: conceptual
ms.date: 09/22/2018
ms.openlocfilehash: a28530fd4e4731065c4ddcc2f39e9a4660529921
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/27/2021
ms.locfileid: "98881919"
---
# <a name="app-settings-reference-for-azure-functions"></a>Referenční materiály k nastavení aplikací pro Azure Functions

Nastavení aplikace ve Function App obsahují globální možnosti konfigurace, které mají vliv na všechny funkce této aplikace Function App. Když spouštíte místně, tato nastavení jsou k dispozici jako [proměnné místního prostředí](functions-run-local.md#local-settings-file). Tento článek obsahuje seznam nastavení aplikace, která jsou k dispozici ve funkcích aplikace.

[!INCLUDE [Function app settings](../../includes/functions-app-settings.md)]

Existují další možnosti globální konfigurace v [host.js](functions-host-json.md) souboru a [local.settings.js](functions-run-local.md#local-settings-file) v souboru.

> [!NOTE]  
> Nastavení aplikace můžete použít k přepsání host.jsnastavením hodnot bez nutnosti měnit host.jsv samotném souboru. To je užitečné ve scénářích, kdy potřebujete nakonfigurovat nebo upravit konkrétní host.jsv nastavení pro konkrétní prostředí. To vám také umožní změnit host.jsnastavení bez nutnosti opětovného publikování projektu. Další informace najdete v části [host.jsv článku referenční článek](functions-host-json.md#override-hostjson-values).  

## <a name="appinsights_instrumentationkey"></a>APPINSIGHTS_INSTRUMENTATIONKEY

Klíč instrumentace pro Application Insights. Použijte pouze jeden z `APPINSIGHTS_INSTRUMENTATIONKEY` nebo `APPLICATIONINSIGHTS_CONNECTION_STRING` . Pokud Application Insights běží v cloudu svrchovan, použijte `APPLICATIONINSIGHTS_CONNECTION_STRING` . Další informace najdete v tématu [Konfigurace monitorování pro Azure Functions](configure-monitoring.md). 

|Klíč|Ukázková hodnota|
|---|------------|
|APPINSIGHTS_INSTRUMENTATIONKEY|55555555-af77-484b-9032-64f83bb83bb|

## <a name="applicationinsights_connection_string"></a>APPLICATIONINSIGHTS_CONNECTION_STRING

Připojovací řetězec pro Application Insights. Použijte `APPLICATIONINSIGHTS_CONNECTION_STRING` místo `APPINSIGHTS_INSTRUMENTATIONKEY` v následujících případech:

+ Když aplikace Function App vyžaduje přidaná přizpůsobení podporovaná pomocí připojovacího řetězce. 
+ Když instance Application Insights běží v cloudu svrchovaného prostředí, které vyžaduje vlastní koncový bod.

Další informace najdete v tématu [připojovací řetězce](../azure-monitor/app/sdk-connection-string.md). 

|Klíč|Ukázková hodnota|
|---|------------|
|APPLICATIONINSIGHTS_CONNECTION_STRING|InstrumentationKey = [klíč]; IngestionEndpoint = [URL]; LiveEndpoint = [URL]; ProfilerEndpoint = [URL]; SnapshotEndpoint = [URL];|

## <a name="azure_function_proxy_disable_local_call"></a>AZURE_FUNCTION_PROXY_DISABLE_LOCAL_CALL

Ve výchozím nastavení používají [proxy služby funkcí](functions-proxies.md) zástupce k odesílání volání rozhraní API z proxy serverů přímo do funkcí ve stejné aplikaci Function App. Místo vytvoření nové žádosti HTTP se používá tato zkratka. Toto nastavení umožňuje zakázat toto chování zástupce.

|Klíč|Hodnota|Popis|
|-|-|-|
|AZURE_FUNCTION_PROXY_DISABLE_LOCAL_CALL|true|Volání s adresou URL back-endu ukazující na funkci v místní aplikaci Function App nebudou odesílána přímo do funkce. Místo toho se požadavky přesměrují zpátky na front-endu HTTP pro aplikaci Function App.|
|AZURE_FUNCTION_PROXY_DISABLE_LOCAL_CALL|false (nepravda)|Volání s adresou URL back-endu ukazující na funkci v místní aplikaci Function App jsou předávána přímo do funkce. Toto je výchozí hodnota. |

## <a name="azure_function_proxy_backend_url_decode_slashes"></a>AZURE_FUNCTION_PROXY_BACKEND_URL_DECODE_SLASHES

Toto nastavení určuje, zda `%2F` jsou znaky dekódovat jako lomítka v parametrech směrování při jejich vložení do adresy URL back-endu. 

|Klíč|Hodnota|Popis|
|-|-|-|
|AZURE_FUNCTION_PROXY_BACKEND_URL_DECODE_SLASHES|true|Parametry směrování s kódovanými lomítky jsou dekódované. |
|AZURE_FUNCTION_PROXY_BACKEND_URL_DECODE_SLASHES|false (nepravda)|Všechny parametry tras jsou předány beze změny, což je výchozí chování. |

Zvažte například proxies.jsv souboru pro aplikaci Function App v `myfunction.com` doméně.

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

Když `AZURE_FUNCTION_PROXY_BACKEND_URL_DECODE_SLASHES` je nastaveno na `true` , adresa URL se `example.com/api%2ftest` přeloží na `example.com/api/test` . Ve výchozím nastavení zůstane adresa URL beze změny `example.com/test%2fapi` . Další informace najdete v tématu [proxy služby Functions](functions-proxies.md).

## <a name="azure_functions_environment"></a>AZURE_FUNCTIONS_ENVIRONMENT

Ve verzi 2. x a novějších verzích modulu runtime Functions konfiguruje chování aplikace na základě běhového prostředí. Tato hodnota je [čtena při inicializaci](https://github.com/Azure/azure-functions-host/blob/dev/src/WebJobs.Script.WebHost/Program.cs#L43). Můžete nastavit `AZURE_FUNCTIONS_ENVIRONMENT` na libovolnou hodnotu, ale podporují se [tři hodnoty](/dotnet/api/microsoft.aspnetcore.hosting.environmentname) : [vývoj](/dotnet/api/microsoft.aspnetcore.hosting.environmentname.development), [Příprava](/dotnet/api/microsoft.aspnetcore.hosting.environmentname.staging)a [produkce](/dotnet/api/microsoft.aspnetcore.hosting.environmentname.production). Pokud `AZURE_FUNCTIONS_ENVIRONMENT` není nastavené, použije se výchozí nastavení `Development` v místním prostředí a `Production` v Azure. Toto nastavení by se mělo použít místo `ASPNETCORE_ENVIRONMENT` pro nastavení běhového prostředí. 

## <a name="azurefunctionsjobhost__"></a>AzureFunctionsJobHost__\*

Ve verzi 2. x a novějších verzích modulu runtime Functions může nastavení aplikace přepsat [host.jsv](functions-host-json.md) nastavení v aktuálním prostředí. Tato přepsání jsou vyjádřena jako nastavení aplikace s názvem `AzureFunctionsJobHost__path__to__setting` . Další informace najdete v tématu [přepis host.jsna hodnotách](functions-host-json.md#override-hostjson-values).

## <a name="azurewebjobsdashboard"></a>AzureWebJobsDashboard

Volitelný připojovací řetězec účtu úložiště pro ukládání protokolů a jejich zobrazení na portálu na kartě **monitorování** . Toto nastavení je platné jenom pro aplikace, které cílí na verzi 1. x Azure Functions runtime. Účet úložiště musí být účet pro obecné účely, který podporuje objekty blob, fronty a tabulky. Další informace najdete v tématu [požadavky na účet úložiště](storage-considerations.md#storage-account-requirements).

|Klíč|Ukázková hodnota|
|---|------------|
|AzureWebJobsDashboard|DefaultEndpointsProtocol = https; Účet Account = <name> ; AccountKey =<key>|

> [!NOTE]
> Pro lepší výkon a prostředí používá modul runtime verze 2. x a novější verze APPINSIGHTS_INSTRUMENTATIONKEY a App Insights pro monitorování místo `AzureWebJobsDashboard` .

## <a name="azurewebjobsdisablehomepage"></a>AzureWebJobsDisableHomepage

`true` znamená zakázat výchozí cílovou stránku, která se zobrazí pro kořenovou adresu URL aplikace Function App. Výchozí je `false`.

|Klíč|Ukázková hodnota|
|---|------------|
|AzureWebJobsDisableHomepage|true|

Pokud je toto nastavení aplikace vynecháno nebo nastaveno na `false` , zobrazí se na stránce odpověď na adresu URL stránka podobná následujícímu příkladu `<functionappname>.azurewebsites.net` .

![Cílová stránka aplikace Function App](media/functions-app-settings/function-app-landing-page.png)

## <a name="azurewebjobsdotnetreleasecompilation"></a>AzureWebJobsDotNetReleaseCompilation

`true` znamená použití režimu vydání při kompilování kódu .NET; `false` znamená použít režim ladění. Výchozí je `true`.

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

Modul runtime Azure Functions používá tento připojovací řetězec k účtu úložiště pro normální provoz. Některá použití tohoto účtu úložiště zahrnují správu klíčů, správu aktivačních událostí časovače a Event Hubs kontrolní body. Účet úložiště musí být účet pro obecné účely, který podporuje objekty blob, fronty a tabulky. Viz požadavky na [účet úložiště](functions-infrastructure-as-code.md#storage-account) a [účet úložiště](storage-considerations.md#storage-account-requirements).

|Klíč|Ukázková hodnota|
|---|------------|
|AzureWebJobsStorage|DefaultEndpointsProtocol = https; Název účtu = [název]; AccountKey = [klíč]|

## <a name="azurewebjobs_typescriptpath"></a>AzureWebJobs_TypeScriptPath

Cesta k kompilátoru, který se používá pro TypeScript Umožňuje přepsat výchozí, pokud potřebujete.

|Klíč|Ukázková hodnota|
|---|------------|
|AzureWebJobs_TypeScriptPath|%HOME%\typescript|

## <a name="function_app_edit_mode"></a>\_režim úprav aplikace Function App \_ \_

Určuje, zda jsou povoleny úpravy v Azure Portal. Platné hodnoty jsou "ReadOnly" a "ReadOnly".

|Klíč|Ukázková hodnota|
|---|------------|
|\_režim úprav aplikace Function App \_ \_|readonly|

## <a name="functions_extension_version"></a>\_verze rozšíření Functions \_

Verze modulu runtime Functions, který má být použit v této aplikaci Function App. Vlnovka s hlavní verzí znamená použití nejnovější verze této hlavní verze (například ~ 2). Pokud jsou k dispozici nové verze pro stejnou hlavní verzi, automaticky se nainstalují do aplikace Function App. Pokud chcete aplikaci připnout na konkrétní verzi, použijte úplné číslo verze (například "2.0.12345"). Výchozí hodnota je "~ 2". Hodnota `~1` PIN vaší aplikace na verzi 1. x modulu runtime.

|Klíč|Ukázková hodnota|
|---|------------|
|\_verze rozšíření Functions \_|~ 2|

## <a name="functions_v2_compatibility_mode"></a>\_ \_ Režim kompatibility Functions v2 \_

Toto nastavení umožňuje, aby aplikace Function App běžela v režimu kompatibilním s verzí 2. x v modulu runtime verze 3. x. Toto nastavení použijte jenom v případě, že při [upgradování aplikace Function App z verze 2. x na 3. x z modulu runtime dojde k](functions-versions.md#migrating-from-2x-to-3x)problémům. 

>[!IMPORTANT]
> Toto nastavení je určeno pouze krátkodobým alternativním řešením při aktualizaci aplikace tak, aby běžela správně ve verzi 3. x. Toto nastavení se podporuje, pokud [je podporovaný modul runtime 2. x](functions-versions.md). Pokud narazíte na problémy, které brání spuštění vaší aplikace ve verzi 3. x bez použití tohoto nastavení, [nahlaste prosím svůj problém](https://github.com/Azure/azure-functions-host/issues/new?template=Bug_report.md).

Vyžaduje, aby se [ \_ \_ verze rozšíření Functions](functions-app-settings.md#functions_extension_version) nastavila na `~3` .

|Klíč|Ukázková hodnota|
|---|------------|
|\_ \_ Režim kompatibility Functions v2 \_|true|

## <a name="functions_worker_process_count"></a>\_počet pracovních \_ procesů \_ funkcí

Určuje maximální počet pracovních procesů jazyka s výchozí hodnotou `1` . Maximální povolená hodnota je `10` . Volání funkcí jsou rovnoměrně rozdělena mezi pracovní procesy jazyka. Pracovní procesy jazyka se spouští každých 10 sekund, dokud se \_ \_ nedosáhne počtu zpracovaných pracovních procesů funkcí Count \_ . Použití více pracovních procesů jazyka není stejné jako [škálování](functions-scale.md). Zvažte použití tohoto nastavení, pokud má vaše úloha kombinaci asynchronních volání vázaných na procesor a vstupně-výstupní operace. Toto nastavení platí pro všechny non-.NET jazyky.

|Klíč|Ukázková hodnota|
|---|------------|
|\_počet pracovních \_ procesů \_ funkcí|2|

## <a name="python_threadpool_thread_count"></a>\_ \_ počet vláken fondu vláken v Pythonu \_

Určuje maximální počet vláken, která by pracovník jazyka Python použil k provedení vyvolání funkcí s výchozí hodnotou `1` pro verzi Pythonu `3.8` a níže. V případě verze Pythonu `3.9` a vyšší je hodnota nastavena na `None` . Všimněte si, že toto nastavení nezaručuje počet vláken, která by byla nastavena při spuštění. Nastavení umožňuje Pythonu rozšířit počet vláken na zadanou hodnotu. Nastavení platí pouze pro aplikace Python Functions. Nastavení se navíc vztahuje na vyvolání synchronních funkcí a nikoli pro korutiny.

|Klíč|Ukázková hodnota|Max. hodnota|
|---|------------|---------|
|\_ \_ počet vláken fondu vláken v Pythonu \_|2|32|


## <a name="functions_worker_runtime"></a>FUNKCE \_ \_ modulu runtime pracovního procesu

Běhový modul runtime jazyka, který se má načíst do aplikace Function App.  Bude odpovídat jazyku používanému ve vaší aplikaci (například "dotnet"). V případě funkcí v různých jazycích je budete muset publikovat do více aplikací, z nichž každá má odpovídající běhovou hodnotu pracovního procesu.  Platné hodnoty jsou `dotnet` (C#/f #), `node` (JavaScript/TypeScript), `java` (Java), `powershell` (PowerShell) a `python` (Python).

|Klíč|Ukázková hodnota|
|---|------------|
|FUNKCE \_ \_ modulu runtime pracovního procesu|dotnet|

## <a name="pip_extra_index_url"></a>\_přídavná \_ \_ Adresa URL indexu PIP

Hodnota tohoto nastavení označuje adresu URL vlastního indexu balíčku pro aplikace v jazyce Python. Toto nastavení použijte, pokud potřebujete spustit vzdálené sestavení pomocí vlastních závislostí, které se nacházejí v nadbytečném indexu balíčku.   

|Klíč|Ukázková hodnota|
|---|------------|
|\_přídavná \_ \_ Adresa URL indexu PIP|http://my.custom.package.repo/simple |

Další informace najdete v tématu [vlastní závislosti](functions-reference-python.md#remote-build-with-extra-index-url) v referenční příručce pro vývojáře v Pythonu.

## <a name="scale_controller_logging_enable"></a>\_ \_ Povolení protokolování škálování \_ řadiče

_Toto nastavení je aktuálně ve verzi Preview._  

Toto nastavení řídí protokolování z Azure Functionsho řadiče škálování. Další informace najdete v tématu [škálování protokolů řadiče](functions-monitoring.md#scale-controller-logs).

|Klíč|Ukázková hodnota|
|-|-|
|SCALE_CONTROLLER_LOGGING_ENABLE|AppInsights: verbose|

Hodnota pro tento klíč je zadána ve formátu `<DESTINATION>:<VERBOSITY>` , který je definován následujícím způsobem:

[!INCLUDE [functions-scale-controller-logging](../../includes/functions-scale-controller-logging.md)]

## <a name="website_contentazurefileconnectionstring"></a>\_CONTENTAZUREFILECONNECTIONSTRING webu

Připojovací řetězec pro účet úložiště, ve kterém je kód a konfigurace aplikace Function App uložený v plánech škálování řízených událostmi, které běží v systému Windows. Další informace najdete v tématu [Vytvoření aplikace Function App](functions-infrastructure-as-code.md#windows).

|Klíč|Ukázková hodnota|
|---|------------|
|WEBSITE_CONTENTAZUREFILECONNECTIONSTRING|DefaultEndpointsProtocol = https; Název účtu = [název]; AccountKey = [klíč]|

Používá se jenom při nasazení na plány spotřeby nebo Premium, které běží na Windows. Nepodporováno pro Linux. Změna nebo odebrání tohoto nastavení může způsobit, že se vaše aplikace Function App nespustí. Další informace najdete v [tomto článku o řešení potíží](functions-recover-storage-account.md#storage-account-application-settings-were-deleted). 

## <a name="website_contentovervnet"></a>\_CONTENTOVERVNET webu

Jenom pro plány Premium. Hodnota `1` umožňuje škálování aplikace Function App, když máte účet úložiště omezený na virtuální síť. Toto nastavení byste měli povolit při omezení účtu úložiště na virtuální síť. Další informace najdete v tématu [omezení účtu úložiště na virtuální síť](functions-networking-options.md#restrict-your-storage-account-to-a-virtual-network).

|Klíč|Ukázková hodnota|
|---|------------|
|WEBSITE_CONTENTOVERVNET|1|

## <a name="website_contentshare"></a>\_CONTENTSHARE webu

Cesta k souboru s kódem a konfigurací aplikace funkcí v plánu škálování řízeného událostmi ve Windows. Používá se s WEBSITE_CONTENTAZUREFILECONNECTIONSTRING. Výchozí hodnota je jedinečný řetězec, který začíná názvem aplikace Function App. Viz [Vytvoření aplikace Function App](functions-infrastructure-as-code.md#windows).

|Klíč|Ukázková hodnota|
|---|------------|
|WEBSITE_CONTENTSHARE|functionapp091999e2|

Používá se jenom aplikace Function App na plánech spotřeby nebo Premium, které běží na Windows. Nepodporováno pro Linux. Změna nebo odebrání tohoto nastavení může způsobit, že se vaše aplikace Function App nespustí. Další informace najdete v [tomto článku o řešení potíží](functions-recover-storage-account.md#storage-account-application-settings-were-deleted).

Při použití Azure Resource Manager k vytvoření aplikace Function App během nasazení nezahrnujte do šablony WEBSITE_CONTENTSHARE. Toto nastavení aplikace se generuje během nasazování. Další informace najdete v tématu [Automatizace nasazení prostředků pro aplikaci Function App](functions-infrastructure-as-code.md#windows).   

## <a name="website_max_dynamic_application_scale_out"></a>maximální navýšení \_ \_ \_ kapacity dynamické aplikace \_ \_ na webu

Maximální počet instancí, na které může aplikace Function App navýšit horizontální navýšení kapacity. Výchozí hodnota není nijak omezena.

> [!IMPORTANT]
> Toto nastavení je ve verzi Preview.  Byla přidána [vlastnost aplikace pro maximum horizontálního](./event-driven-scaling.md#limit-scale-out) navýšení kapacity a je doporučeným způsobem, jak omezit horizontální navýšení kapacity.

|Klíč|Ukázková hodnota|
|---|------------|
|maximální navýšení \_ \_ \_ kapacity dynamické aplikace \_ \_ na webu|5|

## <a name="website_node_default_version"></a>\_DEFAULT_VERSION uzlu \_ webu

_Pouze Windows._  
Nastaví verzi Node.js, která se má použít při spuštění aplikace Function App ve Windows. Chcete-li, aby modul runtime používal nejnovější dostupnou verzi cílené hlavní verze, použijte vlnovku (~). Například při nastavení na je `~10` použita nejnovější verze Node.js 10. V případě, že je hlavní verze cílena na vlnovku, nemusíte ručně aktualizovat dílčí verzi. 

|Klíč|Ukázková hodnota|
|---|------------|
|\_DEFAULT_VERSION uzlu \_ webu|~ 10|

## <a name="website_run_from_package"></a>Web \_ spouštěný \_ z \_ balíčku

Povolí spuštění aplikace Function App z připojeného souboru balíčku.

|Klíč|Ukázková hodnota|
|---|------------|
|Web \_ spouštěný \_ z \_ balíčku|1|

Platné hodnoty jsou buď adresy URL, které se překládá na umístění souboru balíčku pro nasazení, nebo `1` . Při nastavení na `1` se balíček musí nacházet ve `d:\home\data\SitePackages` složce. Při použití nasazení zip s tímto nastavením se balíček automaticky nahraje do tohoto umístění. Ve verzi Preview bylo toto nastavení pojmenováno `WEBSITE_RUN_FROM_ZIP` . Další informace najdete v tématu [spuštění funkcí ze souboru balíčku](run-functions-from-deployment-package.md).

## <a name="website_time_zone"></a>\_časové \_ pásmo webu

Umožňuje nastavit časové pásmo pro aplikaci Function App. 

|Klíč|Operační systém|Ukázková hodnota|
|---|--|------------|
|\_časové \_ pásmo webu|Windows|Východní oblast (běžný čas)|
|\_časové \_ pásmo webu|Linux|America/New_York|

[!INCLUDE [functions-timezone](../../includes/functions-timezone.md)]

## <a name="next-steps"></a>Další kroky

[Informace o tom, jak aktualizovat nastavení aplikace](functions-how-to-use-azure-function-app-settings.md#settings)

[Zobrazit globální nastavení v host.jssouboru](functions-host-json.md)

[Zobrazit další nastavení aplikací pro App Service aplikace](https://github.com/projectkudu/kudu/wiki/Configurable-settings)
