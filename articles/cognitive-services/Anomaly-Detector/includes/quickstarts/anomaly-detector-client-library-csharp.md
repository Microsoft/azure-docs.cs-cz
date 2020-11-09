---
title: Rychlý Start klientské knihovny .NET detektoru anomálií
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 09/22/2020
ms.author: mbullwin
ms.openlocfilehash: 7969e2011a242152e27d7c1aa67b36d99d92adfe
ms.sourcegitcommit: 22da82c32accf97a82919bf50b9901668dc55c97
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/08/2020
ms.locfileid: "94371768"
---
Začínáme s klientskou knihovnou anomálie detektor pro .NET Pomocí těchto kroků nainstalujete balíček Start s použitím algoritmů poskytovaných službou. Služba detektoru anomálií umožňuje v datech časových řad najít neobvyklé typy, a to díky tomu, že automaticky používá nejvhodnější modely, bez ohledu na obor, scénář nebo objem dat.

Použijte klientskou knihovnu detektoru anomálií pro .NET:

* Detekovat anomálie v celé sadě dat časových řad jako dávkový požadavek
* Zjistit stav anomálií z nejnovějšího datového bodu v časové řadě
* Zjištění bodů změny trendu v datové sadě.

[Referenční dokumentace ke knihovně](https://aka.ms/anomaly-detector-dotnet-ref)  |  [Zdrojový kód knihovny](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/AnomalyDetector)  |  [Balíček (NuGet)](https://www.nuget.org/packages/Azure.AI.AnomalyDetector/3.0.0-preview.2)  |  [Vyhledání kódu na GitHubu](https://github.com/Azure-Samples/AnomalyDetector/blob/master/quickstarts/sdk/csharp-sdk-sample.cs)

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure – [Vytvořte si ho zdarma](https://azure.microsoft.com/free/cognitive-services) .
* Aktuální verze [.NET Core](https://dotnet.microsoft.com/download/dotnet-core)
* Jakmile budete mít předplatné Azure, <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAnomalyDetector"  title=" vytvořte prostředek pro detekci anomálií "  target="_blank"> vytvořením prostředku detektoru anomálií <span class="docon docon-navigate-external x-hidden-focus"></span> </a> v Azure Portal, abyste získali svůj klíč a koncový bod. Počkejte na nasazení a klikněte na tlačítko **Přejít k prostředku** .
    * K připojení aplikace k rozhraní API detektoru anomálií budete potřebovat klíč a koncový bod z prostředku, který vytvoříte. Svůj klíč a koncový bod vložíte do níže uvedeného kódu později v rychlém startu.
    K vyzkoušení služby můžete použít bezplatnou cenovou úroveň ( `F0` ) a upgradovat ji později na placenou úroveň pro produkční prostředí.

## <a name="setting-up"></a>Nastavení

[!INCLUDE [anomaly-detector-environment-variables](../environment-variables.md)]

### <a name="create-a-new-net-core-application"></a>Vytvoření nové aplikace .NET Core

V okně konzoly (například cmd, PowerShell nebo bash) použijte `dotnet new` příkaz k vytvoření nové aplikace konzoly s názvem `anomaly-detector-quickstart` . Tento příkaz vytvoří jednoduchý projekt "Hello World" s jedním zdrojovým souborem C#: *program.cs*.

```dotnetcli
dotnet new console -n anomaly-detector-quickstart
```

Změňte adresář na nově vytvořenou složku aplikace. Aplikaci můžete vytvořit pomocí:

```dotnetcli
dotnet build
```

Výstup sestavení by neměl obsahovat žádná upozornění ani chyby.

```output
...
Build succeeded.
 0 Warning(s)
 0 Error(s)
...
```

### <a name="install-the-client-library"></a>Instalace klientské knihovny

V adresáři aplikace nainstalujte knihovnu klienta pro rozpoznávání anomálií pro .NET pomocí následujícího příkazu:

```dotnetcli
dotnet add package Microsoft.Azure.CognitiveServices.AnomalyDetector
```

Z adresáře projektu otevřete soubor *program.cs* a přidejte následující příkaz pomocí `directives` :

[!code-csharp[using statements](~/samples-anomaly-detector/quickstarts/sdk/csharp-sdk-sample.cs?name=usingStatements)]

V `main()` metodě aplikace vytvořte proměnné pro umístění Azure prostředku a klíč jako proměnnou prostředí. Pokud jste po spuštění aplikace vytvořili proměnnou prostředí, bude nutné editor, rozhraní IDE nebo prostředí, které je spuštěno, zavřít a znovu načíst pro přístup k proměnné.

[!code-csharp[Main method](~/samples-anomaly-detector/quickstarts/sdk/csharp-sdk-sample.cs?name=mainMethod)]

## <a name="object-model"></a>Objektový model

Klientem detektoru anomálií je objekt [AnomalyDetectorClient](/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.anomalydetectorclient) , který se ověřuje v Azure pomocí [ApiKeyServiceClientCredentials](/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.apikeyserviceclientcredentials), který obsahuje váš klíč. Klient může provádět detekci anomálií pro celou datovou sadu pomocí [EntireDetectAsync ()](/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.anomalydetectorclientextensions.entiredetectasync)nebo k nejnovějšímu datovému bodu pomocí [LastDetectAsync ()](/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.anomalydetectorclientextensions.lastdetectasync). Metoda [ChangePointDetectAsync](https://aka.ms/anomaly-detector-dotnet-ref) detekuje body, které označují změny trendu.

Data časové řady se odesílají jako řada [bodů](/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.models.request.series?view=azure-dotnet-preview#Microsoft_Azure_CognitiveServices_AnomalyDetector_Models_Request_Series) v objektu [Request](/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.models.request) . `Request`Objekt obsahuje vlastnosti pro popis dat (například[členitosti](/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.models.request.granularity) ) a parametrů pro detekci anomálií.

Odezva detektoru anomálií je objekt [EntireDetectResponse](/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.models.entiredetectresponse), [LastDetectResponse](/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.models.lastdetectresponse)nebo [changePointDetectResponse](https://aka.ms/anomaly-detector-dotnet-ref) v závislosti na použité metodě.

## <a name="code-examples"></a>Příklady kódu

Tyto fragmenty kódu ukazují, jak provést následující akce pomocí klientské knihovny anomálií detektoru pro .NET:

* [Ověření klienta](#authenticate-the-client)
* [Načtení datové sady časových řad ze souboru](#load-time-series-data-from-a-file)
* [Detekovat anomálie v celé sadě dat](#detect-anomalies-in-the-entire-data-set)
* [Zjistit stav anomálií nejnovějšího datového bodu](#detect-the-anomaly-status-of-the-latest-data-point)
* [Zjistit body změny v sadě dat](#detect-change-points-in-the-data-set)

## <a name="authenticate-the-client"></a>Ověření klienta

V nové metodě vytvořte instanci klienta s vaším koncovým bodem a klíčem. Vytvořte objekt [ApiKeyServiceClientCredentials](/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.apikeyserviceclientcredentials?view=azure-dotnet-preview) s klíčem a použijte ho u svého koncového bodu k vytvoření objektu [AnomalyDetectorClient](/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.anomalydetectorclient?view=azure-dotnet-preview) .

[!code-csharp[Client authentication function](~/samples-anomaly-detector/quickstarts/sdk/csharp-sdk-sample.cs?name=createClient)]

## <a name="load-time-series-data-from-a-file"></a>Načtení dat časových řad ze souboru

Stáhněte si ukázková data pro tento rychlý Start z [GitHubu](https://github.com/Azure-Samples/AnomalyDetector/blob/master/example-data/request-data.csv):
1. V prohlížeči klikněte pravým tlačítkem na **raw**.
2. Klikněte na **Uložit odkaz jako**.
3. Uložte soubor do adresáře aplikace jako soubor. csv.

Tato data časové řady jsou formátována jako soubor. csv a budou odeslána do rozhraní API detektoru anomálií.

Vytvořte novou metodu pro čtení v datech časové řady a přidejte ji do objektu [Request](/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.models.request?view=azure-dotnet-preview) . Zavolejte `File.ReadAllLines()` pomocí cesty k souboru a vytvořte seznam objektů [Point](/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.models.point?view=azure-dotnet-preview) a všechny znaky nového řádku. Extrahujte hodnoty a oddělte dateStamp od své číselné hodnoty a přidejte je do nového `Point` objektu.

Vytvořte `Request` objekt s řadou bodů a `Granularity.Daily` pro [členitost](/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.models.granularity?view=azure-dotnet-preview) (nebo periodicitu) datových bodů.

[!code-csharp[load the time series data file](~/samples-anomaly-detector/quickstarts/sdk/csharp-sdk-sample.cs?name=GetSeriesFromFile)]

## <a name="detect-anomalies-in-the-entire-data-set"></a>Detekovat anomálie v celé sadě dat

Vytvořte metodu, která zavolá metodu [EntireDetectAsync ()](/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.anomalydetectorclientextensions.entiredetectasync?view=azure-dotnet-preview#Microsoft_Azure_CognitiveServices_AnomalyDetector_AnomalyDetectorClientExtensions_EntireDetectAsync_Microsoft_Azure_CognitiveServices_AnomalyDetector_IAnomalyDetectorClient_Microsoft_Azure_CognitiveServices_AnomalyDetector_Models_Request_System_Threading_CancellationToken_) klienta s `Request` objektem a očekává odpověď jako objekt [EntireDetectResponse](/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.models.entiredetectresponse?view=azure-dotnet-preview) . Pokud časová řada obsahuje nějaké anomálie, Iterujte prostřednictvím hodnot [anomálií](/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.models.entiredetectresponse.isanomaly?view=azure-dotnet-preview) odpovědi a vytiskněte všechny, které jsou `true` . Tyto hodnoty odpovídají indexu datových bodů neobvyklé, pokud byly nalezeny.

[!code-csharp[EntireDetectSampleAsync() function](~/samples-anomaly-detector/quickstarts/sdk/csharp-sdk-sample.cs?name=entireDatasetExample)]

## <a name="detect-the-anomaly-status-of-the-latest-data-point"></a>Zjistit stav anomálií nejnovějšího datového bodu

Vytvořte metodu, která zavolá metodu [LastDetectAsync ()](/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.anomalydetectorclientextensions.lastdetectasync?view=azure-dotnet-preview#Microsoft_Azure_CognitiveServices_AnomalyDetector_AnomalyDetectorClientExtensions_LastDetectAsync_Microsoft_Azure_CognitiveServices_AnomalyDetector_IAnomalyDetectorClient_Microsoft_Azure_CognitiveServices_AnomalyDetector_Models_Request_System_Threading_CancellationToken_) klienta s `Request` objektem a očekává odpověď jako objekt [LastDetectResponse](/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.models.lastdetectresponse?view=azure-dotnet-preview) . Zkontrolujte atribut [anomálie](/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.models.lastdetectresponse.isanomaly?view=azure-dotnet-preview) odpovědi a určete, zda byl nejnovější odeslaný datový bod anomálií nebo nikoli.

[!code-csharp[LastDetectSampleAsync() function](~/samples-anomaly-detector/quickstarts/sdk/csharp-sdk-sample.cs?name=latestPointExample)]

## <a name="detect-change-points-in-the-data-set"></a>Zjištění bodů změny v sadě dat

Vytvořte metodu, která zavolá metodu [DetectChangePointAsync](https://aka.ms/anomaly-detector-dotnet-ref) klienta s `Request` objektem a počká na odpověď jako objekt [ChangePointDetectResponse](https://aka.ms/anomaly-detector-dotnet-ref) . Ověřte hodnoty IsChangePoint odpovědi a vytiskněte všechny, které jsou `true` . Tyto hodnoty odpovídají bodům změny trendu, pokud byly nalezeny.

[!code-csharp[DetectChangePoint() function](~/samples-anomaly-detector/quickstarts/sdk/csharp-sdk-sample.cs?name=changePointExample)]

## <a name="run-the-application"></a>Spuštění aplikace

Spusťte aplikaci pomocí `dotnet run` příkazu z adresáře aplikace.

```dotnetcli
dotnet run
```

[!INCLUDE [anomaly-detector-next-steps](../quickstart-cleanup-next-steps.md)]