---
title: 'Úvodní příručka: Detekce anomálií v datech časových řad pomocí klientské knihovny Detektor anomálií pro rozhraní .NET'
titleSuffix: Azure Cognitive Services
description: Naučte se, jak pomocí rozhraní API detektoru anomálií zjistit abnormality v datových řadách jako dávku nebo na streamovaná data.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: quickstart
ms.date: 03/24/2020
ms.author: aahi
ms.openlocfilehash: 268ceee5504e6e48b9eb8fdae18564482480e250
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "80239117"
---
# <a name="quickstart-anomaly-detector-client-library-for-net"></a>Úvodní příručka: Klientská knihovna Detektor anomálií pro rozhraní .NET

Začínáme s klientskou knihovnou Detektoranolií pro rozhraní .NET. Následujícím postupem nainstalujte balíček a vyzkoušejte ukázkový kód pro základní úkoly. Služba Detektor anomálií umožňuje najít abnormality v datech časových řad automaticky pomocí nejvhodnějších modelů, bez ohledu na odvětví, scénář nebo objem dat.

Pomocí klientské knihovny Detektor anomálií pro rozhraní .NET:

* Detekce anomálií v celé datové sadě časových řad jako dávkový požadavek
* Zjištění stavu anomálií nejnovějšího datového bodu v časových řadách

[Dokumentace ke referencím knihovny](https://docs.microsoft.com/dotnet/api/Microsoft.Azure.CognitiveServices.AnomalyDetector?view=azure-dotnet-preview) | [Balíček zdrojového kódu](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/AnomalyDetector) | [knihovny (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.AnomalyDetector/) | Najít kód na[GitHubu](https://github.com/Azure-Samples/AnomalyDetector/blob/master/quickstarts/sdk/csharp-sdk-sample.cs)

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure – [vytvořte si ho zdarma](https://azure.microsoft.com/free/)
* Aktuální verze [rozhraní .NET Core](https://dotnet.microsoft.com/download/dotnet-core)
* Klíč a koncový bod detektoru anomálií

## <a name="setting-up"></a>Nastavení

### <a name="create-an-anomaly-detector-resource"></a>Vytvoření prostředku detektoru anomálií

[!INCLUDE [anomaly-detector-resource-creation](../../../../includes/cognitive-services-anomaly-detector-resource-cli.md)]

### <a name="create-a-new-net-core-application"></a>Vytvoření nové aplikace .NET Core

V okně konzoly (například cmd, PowerShell `dotnet new` nebo Bash) vytvořte pomocí `anomaly-detector-quickstart`příkazu novou konzolovou aplikaci s názvem . Tento příkaz vytvoří jednoduchý projekt "Hello World" s jedním zdrojovým souborem Jazyka C#: *Program.cs*. 

```dotnetcli
dotnet new console -n anomaly-detector-quickstart
```

Změňte adresář do nově vytvořené složky aplikace. Aplikaci můžete sestavit pomocí:

```dotnetcli
dotnet build
```

Výstup sestavení by měl obsahovat žádná upozornění nebo chyby. 

```output
...
Build succeeded.
 0 Warning(s)
 0 Error(s)
...
```

### <a name="install-the-client-library"></a>Instalace klientské knihovny

V adresáři aplikace nainstalujte klientskou knihovnu detektoru anomálií pro rozhraní .NET pomocí následujícího příkazu:

```dotnetcli
dotnet add package Microsoft.Azure.CognitiveServices.AnomalyDetector --version 0.8.0-preview
```

V adresáři projektu otevřete *soubor program.cs* `directives`a přidejte následující použití :

[!code-csharp[using statements](~/samples-anomaly-detector/quickstarts/sdk/csharp-sdk-sample.cs?name=usingStatements)]

V `main()` metodě aplikace vytvořte proměnné pro umístění azure vašeho prostředku a klíč jako proměnnou prostředí. Pokud jste vytvořili proměnnou prostředí po spuštění aplikace, editor, IDE nebo prostředí běží bude nutné zavřít a znovu načíst pro přístup k proměnné.

[!code-csharp[Main method](~/samples-anomaly-detector/quickstarts/sdk/csharp-sdk-sample.cs?name=mainMethod)]

## <a name="object-model"></a>Objektový model

Klient detektoru anomálií je objekt [AnomalyDetectorClient,](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.anomalydetectorclient) který se ověřuje v Azure pomocí [apikeyserviceclientcredentials](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.apikeyserviceclientcredentials), který obsahuje váš klíč. Klient poskytuje dvě metody detekce anomálií: Na celou datovou sadu pomocí [EntireDetectAsync()](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.anomalydetectorclientextensions.entiredetectasync)a na nejnovější datový bod pomocí [LastDetectAsync()](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.anomalydetectorclientextensions.lastdetectasync). 

Data časových řad jsou odesílána jako řada [bodů](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.models.request.series?view=azure-dotnet-preview#Microsoft_Azure_CognitiveServices_AnomalyDetector_Models_Request_Series) v objektu [Požadavku.](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.models.request) Objekt `Request` obsahuje vlastnosti popisující data (například[rozlišovací schopnost)](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.models.request.granularity) a parametry pro detekci anomálií. 

Odpověď detektoru anomálií je buď [WholeDetectResponse](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.models.entiredetectresponse) nebo [LastDetectResponse](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.models.lastdetectresponse) objekt, v závislosti na použité metodě. 

## <a name="code-examples"></a>Příklady kódu

Tyto fragmenty kódu ukazují, jak provést následující akce s klientskou knihovnou detektoru anomálií pro rozhraní .NET:

* [Ověření klienta](#authenticate-the-client)
* [Načtení datové sady časových řad ze souboru](#load-time-series-data-from-a-file)
* [Detekce anomálií v celé sadě dat](#detect-anomalies-in-the-entire-data-set) 
* [Zjištění stavu anomálií nejnovějšího datového bodu](#detect-the-anomaly-status-of-the-latest-data-point)

## <a name="authenticate-the-client"></a>Ověření klienta

V nové metodě vytvořte instanci klienta pomocí koncového bodu a klíče. Vytvořte objekt [ApiKeyServiceClientCredentials](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.apikeyserviceclientcredentials?view=azure-dotnet-preview) s klíčem a použijte ho s koncovým bodem k vytvoření objektu [AnomalyDetectorClient.](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.anomalydetectorclient?view=azure-dotnet-preview) 

[!code-csharp[Client authentication function](~/samples-anomaly-detector/quickstarts/sdk/csharp-sdk-sample.cs?name=createClient)]
    
## <a name="load-time-series-data-from-a-file"></a>Načtení dat časových řad ze souboru

Stáhněte si ukázková data pro tento rychlý start z [GitHubu](https://github.com/Azure-Samples/AnomalyDetector/blob/master/example-data/request-data.csv):
1. V prohlížeči klepněte pravým tlačítkem myši na **položku Nerejzovní**.
2. Klepněte na **tlačítko Uložit odkaz jako**.
3. Uložte soubor do adresáře aplikace jako soubor .csv.

Tato data časové řady jsou formátována jako soubor .csv a budou odeslána do rozhraní API detektoru anomálií.

Vytvořte novou metodu pro čtení v datech časových řad a přidejte ji do objektu [Request.](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.models.request?view=azure-dotnet-preview) Volání `File.ReadAllLines()` s cestou k souboru a vytvoření seznamu [objektů Point](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.models.point?view=azure-dotnet-preview) a odstranění všech nových znaků řádku. Extrahujte hodnoty a oddělte datumrazítko od jeho `Point` číselné hodnoty a přidejte je do nového objektu. 

Vytvořte `Request` objekt s řadou `Granularity.Daily` bodů a pro [rozlišovací schopnost](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.models.granularity?view=azure-dotnet-preview) (nebo periodicitu) datových bodů.

[!code-csharp[load the time series data file](~/samples-anomaly-detector/quickstarts/sdk/csharp-sdk-sample.cs?name=GetSeriesFromFile)]

## <a name="detect-anomalies-in-the-entire-data-set"></a>Detekce anomálií v celé sadě dat 

Vytvořte metodu pro volání metody [WholeDetectAsync() klienta](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.anomalydetectorclientextensions.entiredetectasync?view=azure-dotnet-preview#Microsoft_Azure_CognitiveServices_AnomalyDetector_AnomalyDetectorClientExtensions_EntireDetectAsync_Microsoft_Azure_CognitiveServices_AnomalyDetector_IAnomalyDetectorClient_Microsoft_Azure_CognitiveServices_AnomalyDetector_Models_Request_System_Threading_CancellationToken_) s objektem `Request` a počkejte na odpověď jako objekt [WholeDetectResponse.](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.models.entiredetectresponse?view=azure-dotnet-preview) Pokud časové řady obsahuje všechny anomálie, iterate prostřednictvím odpovědi [IsAnomaly](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.models.entiredetectresponse.isanomaly?view=azure-dotnet-preview) hodnoty a vytisknout všechny, které jsou `true`. Tyto hodnoty odpovídají indexu anomálních datových bodů, pokud byly nalezeny.

[!code-csharp[EntireDetectSampleAsync() function](~/samples-anomaly-detector/quickstarts/sdk/csharp-sdk-sample.cs?name=entireDatasetExample)]

## <a name="detect-the-anomaly-status-of-the-latest-data-point"></a>Zjištění stavu anomálií nejnovějšího datového bodu

Vytvořte metodu pro volání metody [LastDetectAsync() klienta](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.anomalydetectorclientextensions.lastdetectasync?view=azure-dotnet-preview#Microsoft_Azure_CognitiveServices_AnomalyDetector_AnomalyDetectorClientExtensions_LastDetectAsync_Microsoft_Azure_CognitiveServices_AnomalyDetector_IAnomalyDetectorClient_Microsoft_Azure_CognitiveServices_AnomalyDetector_Models_Request_System_Threading_CancellationToken_) s objektem `Request` a počkejte na odpověď jako objekt [LastDetectResponse.](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.models.lastdetectresponse?view=azure-dotnet-preview) Zkontrolujte atribut [IsAnomaly](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.models.lastdetectresponse.isanomaly?view=azure-dotnet-preview) odpovědi k určení, pokud poslední datový bod odeslaný byl anomálie nebo ne. 

[!code-csharp[LastDetectSampleAsync() function](~/samples-anomaly-detector/quickstarts/sdk/csharp-sdk-sample.cs?name=latestPointExample)]

## <a name="run-the-application"></a>Spuštění aplikace

Spusťte aplikaci pomocí příkazu `dotnet run` z adresáře aplikace.

```dotnetcli
dotnet run
```

[!INCLUDE [anomaly-detector-next-steps](../includes/quickstart-cleanup-next-steps.md)]
