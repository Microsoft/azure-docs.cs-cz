---
title: Referenční příručka k nastavení aplikace pro Azure Functions
description: Referenční dokumentaci k nástroji pro nastavení aplikace Azure Functions nebo proměnné prostředí.
services: functions
author: tdykstra
manager: cfowler
editor: ''
tags: ''
keywords: ''
ms.service: functions
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 09/26/2017
ms.author: tdykstra
ms.openlocfilehash: bd5603b8f0e15eeae9dd3799d4e10952e115680f
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/16/2018
ms.locfileid: "34194263"
---
# <a name="app-settings-reference-for-azure-functions"></a>Referenční příručka k nastavení aplikace pro Azure Functions

Nastavení aplikace v aplikaci funkce obsahovat globální konfiguraci možnosti, které ovlivní všechny funkce pro tuto aplikaci funkce. Když spustíte místně, tato nastavení jsou v seznamu proměnných prostředí. Tento článek obsahuje seznam nastavení aplikace, které jsou k dispozici v aplikacích funkce.

Existují další možnosti globální konfigurace v [host.json](functions-host-json.md) soubor a [local.settings.json](functions-run-local.md#local-settings-file) souboru.

## <a name="appinsightsinstrumentationkey"></a>APPINSIGHTS_INSTRUMENTATIONKEY

Klíč instrumentace Application Insights Pokud používáte Application Insights. V tématu [monitorovat Azure Functions](functions-monitoring.md).

|Klíč|Ukázková hodnota|
|---|------------|
|APPINSIGHTS_INSTRUMENTATIONKEY|5dbdd5e9-af77-484b-9032-64f83bb83bb|

## <a name="azurewebjobsdashboard"></a>AzureWebJobsDashboard

Připojovací řetězce k účtu volitelné úložiště pro ukládání protokolů a jejich zobrazení v **monitorování** na portálu. Účet úložiště musí být pro obecné účely ten, který podporuje objekty BLOB, fronty a tabulky. V tématu [účet úložiště](functions-infrastructure-as-code.md#storage-account) a [požadavky na účet úložiště](functions-create-function-app-portal.md#storage-account-requirements).

|Klíč|Ukázková hodnota|
|---|------------|
|AzureWebJobsDashboard|DefaultEndpointsProtocol = https; AccountName = [name]; AccountKey = [klíč]|

## <a name="azurewebjobsdisablehomepage"></a>AzureWebJobsDisableHomepage

`true` znamená zakázat výchozí úvodní stránka, která se zobrazí pro adresy URL kořenového adresáře aplikace funkce. Výchozí hodnota je `false`.

|Klíč|Ukázková hodnota|
|---|------------|
|AzureWebJobsDisableHomepage|true (pravda)|

Při nastavení této aplikace je tento parametr vynechán nebo nastavte na `false`, podobně jako v následujícím příkladu stránky se zobrazí v reakci na adresu URL `<functionappname>.azurewebsites.net`.

![Cílová stránka aplikace – funkce](media/functions-app-settings/function-app-landing-page.png)

## <a name="azurewebjobsdotnetreleasecompilation"></a>AzureWebJobsDotNetReleaseCompilation

`true` znamená použití režimu vydání při kompilaci kódu .NET; `false` znamená použití režimu ladění. Výchozí hodnota je `true`.

|Klíč|Ukázková hodnota|
|---|------------|
|AzureWebJobsDotNetReleaseCompilation|true (pravda)|

## <a name="azurewebjobsfeatureflags"></a>AzureWebJobsFeatureFlags

Čárkami oddělený seznam beta verzi funkce, které chcete povolit. Beta verze funkce povolené tyto příznaky nejsou připravené produkční, lze ji však povolit pro použití s povolenými experimentálními před odesláním za provozu.

|Klíč|Ukázková hodnota|
|---|------------|
|AzureWebJobsFeatureFlags|feature1 feature2|

## <a name="azurewebjobsscriptroot"></a>AzureWebJobsScriptRoot

Cesta ke kořenovému adresáři kde *host.json* souboru a funkce složky jsou umístěny. V aplikaci function app, výchozí hodnota je `%HOME%\site\wwwroot`.

|Klíč|Ukázková hodnota|
|---|------------|
|AzureWebJobsScriptRoot|%Home%\site\wwwroot|

## <a name="azurewebjobssecretstoragetype"></a>AzureWebJobsSecretStorageType

Určuje úložiště nebo poskytovatele chcete použít pro úložiště klíčů. V současné době jsou podporované úložiště objektů blob ("Blob") a systému souborů ("Zakázat"). Výchozí hodnota je systém souborů ("Zakázat").

|Klíč|Ukázková hodnota|
|---|------------|
|AzureWebJobsSecretStorageType|zakázáno|

## <a name="azurewebjobsstorage"></a>AzureWebJobsStorage

Modul runtime Azure Functions používá tento připojovací řetězce k účtu úložiště pro všechny funkce, s výjimkou funkce protokolu HTTP aktivované. Účet úložiště musí být pro obecné účely ten, který podporuje objekty BLOB, fronty a tabulky. V tématu [účet úložiště](functions-infrastructure-as-code.md#storage-account) a [požadavky na účet úložiště](functions-create-function-app-portal.md#storage-account-requirements).

|Klíč|Ukázková hodnota|
|---|------------|
|AzureWebJobsStorage|DefaultEndpointsProtocol = https; AccountName = [name]; AccountKey = [klíč]|

## <a name="azurewebjobstypescriptpath"></a>AzureWebJobs_TypeScriptPath

Cesta pro kompilátor používá pro TypeScript. Umožňuje přepsat výchozí nastavení, pokud je potřeba.

|Klíč|Ukázková hodnota|
|---|------------|
|AzureWebJobs_TypeScriptPath|%Home%\typescript|

## <a name="functionappeditmode"></a>FUNKCE\_APLIKACE\_UPRAVIT\_REŽIMU

Platné hodnoty jsou "readwrite" a "jen pro čtení".

|Klíč|Ukázková hodnota|
|---|------------|
|FUNKCE\_APLIKACE\_UPRAVIT\_REŽIMU|jen pro čtení|

## <a name="functionsextensionversion"></a>FUNKCE\_ROZŠÍŘENÍ\_VERZE

Verze modulu runtime Azure Functions použijte v této aplikaci funkce. Tildou hlavní verzí znamená, použijte nejnovější verzi danou hlavní verzi (například ~ 1"). Jakmile je k dispozici nová verze pro stejnou hlavní verzi, jsou automaticky nainstalovány v aplikaci funkce. Chcete-li připnout aplikaci na konkrétní verzi, použijte plnou verzi číslo (například "1.0.12345"). Výchozí hodnota je "~ 1".

|Klíč|Ukázková hodnota|
|---|------------|
|FUNKCE\_ROZŠÍŘENÍ\_VERZE|~1|

## <a name="websitecontentazurefileconnectionstring"></a>WEBSITE_CONTENTAZUREFILECONNECTIONSTRING

Pro využívání pouze plány. Připojovací řetězec pro účet úložiště, které jsou uloženy funkce kódu aplikace a konfigurace. V tématu [vytvořit aplikaci funkce](functions-infrastructure-as-code.md#create-a-function-app).

|Klíč|Ukázková hodnota|
|---|------------|
|WEBSITE_CONTENTAZUREFILECONNECTIONSTRING|DefaultEndpointsProtocol = https; AccountName = [name]; AccountKey = [klíč]|

## <a name="websitecontentshare"></a>WEBSITE_CONTENTSHARE

Pro využívání pouze plány. Cesta k souboru se funkce kódu aplikace a konfigurací. Použít s WEBSITE_CONTENTAZUREFILECONNECTIONSTRING. Výchozí hodnota je jedinečného řetězce, který začíná název funkce aplikace. V tématu [vytvořit aplikaci funkce](functions-infrastructure-as-code.md#create-a-function-app).

|Klíč|Ukázková hodnota|
|---|------------|
|WEBSITE_CONTENTSHARE|functionapp091999e2|

## <a name="websitemaxdynamicapplicationscaleout"></a>WEB\_MAXIMÁLNÍ\_DYNAMICKÉ\_APLIKACE\_ŠKÁLOVÁNÍ\_OUT

Maximální počet instancí, které funkce aplikace lze škálovat na. Výchozí hodnota je žádné omezení.

> [!NOTE]
> Toto nastavení je funkce preview.

|Klíč|Ukázková hodnota|
|---|------------|
|WEB\_MAXIMÁLNÍ\_DYNAMICKÉ\_APLIKACE\_ŠKÁLOVÁNÍ\_OUT|10|

## <a name="websitenodedefaultversion"></a>WEB\_UZLU\_DEFAULT_VERSION

Výchozí hodnota je "6.5.0".

|Klíč|Ukázková hodnota|
|---|------------|
|WEB\_UZLU\_DEFAULT_VERSION|6.5.0|

## <a name="next-steps"></a>Další postup

[Zjistěte, jak se aktualizovat nastavení aplikace](functions-how-to-use-azure-function-app-settings.md#manage-app-service-settings)

[Najdete v části globální nastavení v souboru host.json](functions-host-json.md)

[Viz další nastavení aplikace pro aplikace služby App Service](https://github.com/projectkudu/kudu/wiki/Configurable-settings)
