---
title: 'Rychlý start: Zjišťovat anomálie v datech časových řad pomocí sady SDK detekce anomálií pro .NET'
titleSuffix: Azure Cognitive Services
description: Spusťte zjišťování anomálií ve vašich datech časových řad službou detekce anomálií.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: quickstart
ms.date: 07/01/2019
ms.author: aahi
ms.openlocfilehash: a75196e035585a7501cdd842fb5b80ceff424dcc
ms.sourcegitcommit: dad277fbcfe0ed532b555298c9d6bc01fcaa94e2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/10/2019
ms.locfileid: "67721569"
---
# <a name="quickstart-anomaly-detector-client-library-for-net"></a>Rychlý start: Anomálií detektor Klientská knihovna pro .NET

Začínáme s detekce anomálií Klientská knihovna pro .NET. Postupujte podle následujících kroků instalace balíčku a vyzkoušejte si ukázkový kód pro základní úlohy. Služba detekce anomálií vám umožňuje automaticky pomocí nejlepšího těsně modelů, bez ohledu na odvětví, scénáři nebo objem dat vyhledat anomálie v daty časových řad.

Použití detekce anomálií klientské knihovny pro .NET:

* Zjišťovat anomálie v dávce
* Zjištění anomálií stavu nejnovější datového bodu

[Referenční dokumentace rozhraní API](https://docs.microsoft.com/dotnet/api/Microsoft.Azure.CognitiveServices.AnomalyDetector?view=azure-dotnet-preview) | [zdrojový kód knihovny](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/AnomalyDetector) | [balíčku (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.AnomalyDetector/) | [ukázky](https://github.com/Azure-Samples/anomalydetector)

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure – [vytvořit zdarma](https://azure.microsoft.com/free/)
* Aktuální verze [.NET Core](https://dotnet.microsoft.com/download/dotnet-core)

## <a name="setting-up"></a>Nastavení

### <a name="create-an-anomaly-detector-resource"></a>Vytvořte prostředek detekce anomálií

[!INCLUDE [anomaly-detector-resource-creation](../../../../includes/cognitive-services-anomaly-detector-resource-cli.md)]

### <a name="create-a-new-c-app"></a>Vytvořte nový C# aplikace

Vytvořte novou aplikaci .NET Core v preferovaného editoru nebo prostředí IDE. 

V okně konzoly (jako je cmd, PowerShell nebo Bash), použijte dotnet `new` příkaz pro vytvoření nových konzolovou aplikaci s názvem `anomaly-detector-quickstart`. Tento příkaz vytvoří jednoduchý "Hello World" C# projekt s jeden zdrojový soubor: **Program.cs**. 

```console
dotnet new console -n anomaly-detector-quickstart
```

Změňte váš adresář na složku nově vytvořené aplikace. Můžete vytvářet aplikace pomocí:

```console
dotnet build
```

Výstup sestavení by měl obsahovat žádná upozornění ani chyby. 

```console
...
Build succeeded.
 0 Warning(s)
 0 Error(s)
...
```

### <a name="install-the-client-library"></a>Nainstalujte klientské knihovny

V adresáři aplikace nainstalujte detekce anomálií Klientská knihovna pro .NET pomocí následujícího příkazu:

```console
dotnet add package Microsoft.Azure.CognitiveServices.AnomalyDetector --version 0.8.0-preview
```

Pokud používáte Visual Studio IDE, je k dispozici jako balíček NuGet klientské knihovny. 

## <a name="object-model"></a>Objektový model

Je klient detekce anomálií [AnomalyDetectorClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.anomalydetectorclient) objekt, který se ověřuje pomocí Azure [ApiKeyServiceClientCredentials](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.apikeyserviceclientcredentials), která obsahuje váš klíč. Klient poskytuje dvě metody pro detekci anomálií: Na objekt pro celou datovou sadu pomocí [EntireDetectAsync()](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.anomalydetectorclientextensions.entiredetectasync)a na nejnovější data bodu pomocí [LastDetectAsync()](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.anomalydetectorclientextensions.lastdetectasync). 

Data časových řad se odešle jako řadu [body](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.models.request.series?view=azure-dotnet-preview#Microsoft_Azure_CognitiveServices_AnomalyDetector_Models_Request_Series) v [žádosti](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.models.request) objektu. `Request` Objekt obsahuje vlastností, které popisují data ([Členitosti](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.models.request.granularity) například) a parametrů pro detekci anomálií. 

Detekce anomálií odpovědi je buď [EntireDetectResponse](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.models.entiredetectresponse) nebo [LastDetectResponse](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.models.lastdetectresponse) objekt, v závislosti na metodě použít. 

## <a name="code-examples"></a>Příklady kódu

Tyto fragmenty kódu ukazují, jak provést následující pomocí detekce anomálií klientské knihovny pro .NET:

* [Ověření klienta](#authenticate-the-client)
* [Načtení času řady datové sady ze souboru](#load-time-series-data-from-a-file)
* [Zjišťovat anomálie v celé datové sady](#detect-anomalies-in-the-entire-data-set) 
* [Zjištění anomálií stavu nejnovější datového bodu](#detect-the-anomaly-status-of-the-latest-data-point)

### <a name="add-the-main-method"></a>Přidat main – metoda

Z adresáře projektu:

1. Otevřete soubor Program.cs v preferovaného editoru nebo prostředí IDE
2. Přidejte následující `using` direktivy

[!code-csharp[using statements](~/samples-anomaly-detector/quickstarts/sdk/csharp-sdk-sample.cs?name=usingStatements)]

> [!NOTE]
> V tomto rychlém startu se předpokládá jste [vytvoří proměnnou prostředí](../../cognitive-services-apis-create-account.md#configure-an-environment-variable-for-authentication) klíč detekce anomálií s názvem `ANOMALY_DETECTOR_KEY`.

V aplikaci prvku `main()` metody vytváření proměnných pro váš prostředek Azure umístění a klíč jako proměnnou prostředí. Pokud vytvoříte proměnné prostředí po spuštění aplikace, editor, integrovaném vývojovém prostředí nebo prostředí, kterých je spuštěná muset zavřít a znovu načíst pro přístup k proměnné.

[!code-csharp[Main method](~/samples-anomaly-detector/quickstarts/sdk/csharp-sdk-sample.cs?name=mainMethod)]

### <a name="authenticate-the-client"></a>Ověření klienta

V nové metodě vytvoření instance klienta s koncového bodu a klíče. Vytvoření [ApiKeyServiceClientCredentials](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.apikeyserviceclientcredentials?view=azure-dotnet-preview) objekt pomocí klíče a jeho použití s váš koncový bod pro vytvoření [AnomalyDetectorClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.anomalydetectorclient?view=azure-dotnet-preview) objektu. 

[!code-csharp[Client authentication function](~/samples-anomaly-detector/quickstarts/sdk/csharp-sdk-sample.cs?name=createClient)]
    
### <a name="load-time-series-data-from-a-file"></a>Načtení dat časových řad ze souboru

Stáhněte si ukázková data pro účely tohoto rychlého startu z [Githubu](https://github.com/Azure-Samples/AnomalyDetector/blob/master/example-data/request-data.csv):
1. V prohlížeči, klikněte pravým tlačítkem na **Raw**
2. Klikněte na tlačítko **uložit odkaz jako**
3. Uložte soubor do adresáře aplikace jako soubor .csv.

Tato data časových řad formátována jako soubor .csv a se odešlou do rozhraní API detekce anomálií.

Vytvoření nové metody pro čtení v datech časové řady a přidejte ji tak [žádosti](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.models.request?view=azure-dotnet-preview) objektu. Volání `File.ReadAllLines()` s cestou k souboru a vytvoří seznam [bodu](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.models.point?view=azure-dotnet-preview) objekty a odstranit všechny nové řádek znaků. Extrahování hodnot a oddělit datestamp číselnou hodnotu a přidat je do nového `Point` objektu. 

Ujistěte se, `Request` objekt s řadu bodů, a `Granularity.Daily` pro [Členitosti](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.models.granularity?view=azure-dotnet-preview) (nebo periodicita) datových bodů.

[!code-csharp[load the time series data file](~/samples-anomaly-detector/quickstarts/sdk/csharp-sdk-sample.cs?name=GetSeriesFromFile)]

### <a name="detect-anomalies-in-the-entire-data-set"></a>Zjišťovat anomálie v celé datové sady 

Vytvořte metodu k volání klienta [EntireDetectAsync()](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.anomalydetectorclientextensions.entiredetectasync?view=azure-dotnet-preview#Microsoft_Azure_CognitiveServices_AnomalyDetector_AnomalyDetectorClientExtensions_EntireDetectAsync_Microsoft_Azure_CognitiveServices_AnomalyDetector_IAnomalyDetectorClient_Microsoft_Azure_CognitiveServices_AnomalyDetector_Models_Request_System_Threading_CancellationToken_) metodou `Request` objektu a operátoru await odpověď jako [EntireDetectResponse](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.models.entiredetectresponse?view=azure-dotnet-preview) objektu. Obsahuje-li časové řady anomálie, iteraci v rámci odpovědi na [IsAnomaly](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.models.entiredetectresponse.isanomaly?view=azure-dotnet-preview) hodnoty a tisknout, žádné, které jsou `true`. Tyto hodnoty odpovídat indexu neobvyklé datových bodů, pokud některá.

[!code-csharp[EntireDetectSampleAsync() function](~/samples-anomaly-detector/quickstarts/sdk/csharp-sdk-sample.cs?name=entireDatasetExample)]

### <a name="detect-the-anomaly-status-of-the-latest-data-point"></a>Zjištění anomálií stavu nejnovější datového bodu

Vytvořte metodu k volání klienta [LastDetectAsync()](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.anomalydetectorclientextensions.lastdetectasync?view=azure-dotnet-preview#Microsoft_Azure_CognitiveServices_AnomalyDetector_AnomalyDetectorClientExtensions_LastDetectAsync_Microsoft_Azure_CognitiveServices_AnomalyDetector_IAnomalyDetectorClient_Microsoft_Azure_CognitiveServices_AnomalyDetector_Models_Request_System_Threading_CancellationToken_) metodou `Request` objektu a operátoru await odpověď jako [LastDetectResponse](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.models.lastdetectresponse?view=azure-dotnet-preview) objektu. Zkontrolujte odpovědi [IsAnomaly](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.models.lastdetectresponse.isanomaly?view=azure-dotnet-preview) atribut k určení, pokud byla poslední datový bod odeslané anomálií nebo ne. 

[!code-csharp[LastDetectSampleAsync() function](~/samples-anomaly-detector/quickstarts/sdk/csharp-sdk-sample.cs?name=latestPointExample)]

## <a name="run-the-application"></a>Spuštění aplikace

Spustit aplikaci s dotnet `run` příkaz z adresáře aplikace.

```dotnet
dotnet run
```

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud chcete vyčistit a odebrat předplatné služeb Cognitive Services, můžete odstranit prostředek nebo skupinu prostředků. Odstraněním skupiny prostředků se odstraní také všechny další prostředky spojené s vybranou skupinou prostředků.

* [Azure Portal](../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

Můžete také spustit cloud shell příkaz k odebrání skupiny prostředků a její přidružené prostředky. To může trvat několik minut. 

```azurecli-interactive
az group delete --name example-anomaly-detector-resource-group
```

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
>[Streamování detekce anomálií pomocí služby Azure Databricks](../tutorials/anomaly-detection-streaming-databricks.md)

* Co je [detekce anomálií rozhraní API?](../overview.md)
* [Osvědčené postupy](../concepts/anomaly-detection-best-practices.md) při použití rozhraní API detekce anomálií.
* Zdrojový kód pro tuto ukázku můžete najít na [Githubu](https://github.com/Azure-Samples/AnomalyDetector/blob/master/quickstarts/sdk/csharp-sdk-sample.cs).
