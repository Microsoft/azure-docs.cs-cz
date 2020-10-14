---
title: 'Rychlý Start: zjištění anomálií v datech časových řad pomocí detektoru anomálií REST API a Java'
titleSuffix: Azure Cognitive Services
description: Naučte se používat rozhraní API pro detekci anomálií ke zjištění anomálií v datové řadě buď jako dávku, nebo na streamovaná data.
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: quickstart
ms.date: 09/03/2020
ms.custom: devx-track-java
ms.author: mbullwin
ms.openlocfilehash: e23e9016b53a1d9f99809d792d710f865918a098
ms.sourcegitcommit: 2c586a0fbec6968205f3dc2af20e89e01f1b74b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/14/2020
ms.locfileid: "92019677"
---
# <a name="quickstart-detect-anomalies-in-your-time-series-data-using-the-anomaly-detector-rest-api-and-java"></a>Rychlý Start: zjištění anomálií v datech časových řad pomocí detektoru anomálií REST API a Java

Tento rychlý Start vám umožní začít používat dva režimy zjišťování rozhraní API pro detekci anomálií ke zjištění anomálií v datech časových řad. Tato aplikace Java odesílá požadavky rozhraní API obsahující data časových řad ve formátu JSON a získá odpovědi.

| Požadavek rozhraní API                                        | Výstup aplikace                                                                                                                         |
|----------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------|
| Zjištění anomálií jako dávky                        | Odpověď JSON obsahující stav anomálie (a další data) pro každý datový bod v datech časové řady a pozice všech zjištěných anomálií. |
| Zjistit stav anomálií nejnovějšího datového bodu | Odpověď JSON obsahující stav anomálie (a další data) pro poslední datový bod v datech časové řady.   |
| Zjištění bodů změn, které označují nové trendy dat | Odpověď JSON obsahující zjištěné body změny v datech časové řady |

I když je tato aplikace napsaná v jazyce Java, rozhraní API je webová služba RESTful kompatibilní s většinou programovacích jazyků. Zdrojový kód pro tento rychlý Start najdete na [GitHubu](https://github.com/Azure-Samples/AnomalyDetector/blob/master/quickstarts/java-detect-anomalies.java).

## <a name="prerequisites"></a>Požadavky

- Předplatné Azure – [Vytvořte si ho zdarma](https://azure.microsoft.com/free/cognitive-services) .
- Jakmile budete mít předplatné Azure, <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAnomalyDetector"  title=" vytvořte prostředek pro detekci anomálií "  target="_blank"> vytvořením prostředku detektoru anomálií <span class="docon docon-navigate-external x-hidden-focus"></span> </a> v Azure Portal, abyste získali svůj klíč a koncový bod. Počkejte na nasazení a klikněte na tlačítko **Přejít k prostředku** .
    - K připojení aplikace k rozhraní API detektoru anomálií budete potřebovat klíč a koncový bod z prostředku, který vytvoříte. Svůj klíč a koncový bod vložíte do níže uvedeného kódu později v rychlém startu.
    K vyzkoušení služby můžete použít bezplatnou cenovou úroveň ( `F0` ) a upgradovat ji později na placenou úroveň pro produkční prostředí.
- [Java &trade; Development Kit (JDK) 7](https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html) nebo novější.
- Importovat tyto knihovny z úložiště Maven
    - [JSON v balíčku Java](https://mvnrepository.com/artifact/org.json/json)
    - Balíček [Apache HttpClient](https://mvnrepository.com/artifact/org.apache.httpcomponents/httpclient)

- Soubor JSON, který obsahuje datové body časové řady. Ukázková data pro tento rychlý Start najdete na [GitHubu](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/request-data.json).

[!INCLUDE [anomaly-detector-environment-variables](../includes/environment-variables.md)]

## <a name="create-a-new-application"></a>Vytvoření nové aplikace

1. Vytvořte nový projekt Java a importujte následující knihovny.

    [!code-java[Import statements](~/samples-anomaly-detector/quickstarts/java-detect-anomalies.java?name=imports)]

2. Vytvořte proměnné pro svůj klíč předplatného a koncový bod. Níže jsou uvedeny identifikátory URI, které lze použít pro detekci anomálií. Ty se připojí ke koncovému bodu služby později a vytvoří adresy URL žádostí o rozhraní API.

    |Metoda detekce  |Identifikátor URI  |
    |---------|---------|
    |Zjišťování dávky    | `/anomalydetector/v1.0/timeseries/entire/detect`        |
    |Zjišťování nejnovějšího datového bodu     | `/anomalydetector/v1.0/timeseries/last/detect`        |
    | Detekce bodu změny | `/anomalydetector/v1.0/timeseries/changepoint/detect`   |

    [!code-java[Initial key and endpoint variables](~/samples-anomaly-detector/quickstarts/java-detect-anomalies.java?name=vars)]

## <a name="create-a-function-to-send-requests"></a>Vytvoření funkce pro odesílání požadavků

1. Vytvořte novou funkci s názvem `sendRequest()` , která přijímá proměnné vytvořené výše. Pak proveďte následující kroky.

2. Vytvořte `CloseableHttpClient` objekt, který může odesílat požadavky do rozhraní API. Odešlete požadavek do `HttpPost` objektu žádosti kombinací koncového bodu a adresy URL detektoru anomálií.

3. Pomocí `setHeader()` funkce Request nastavte `Content-Type` hlavičku na `application/json` a přidejte do hlavičky klíč předplatného `Ocp-Apim-Subscription-Key` .

4. Použijte `setEntity()` funkci žádosti na data, která chcete odeslat.

5. Použijte `execute()` funkci klienta k odeslání žádosti a uložte ji do `CloseableHttpResponse` objektu.

6. Vytvořte `HttpEntity` objekt pro uložení obsahu odpovědi. Získejte obsah pomocí `getEntity()` . Pokud odpověď není prázdná, vraťte ji.

    [!code-java[API request method](~/samples-anomaly-detector/quickstarts/java-detect-anomalies.java?name=request)]

## <a name="detect-anomalies-as-a-batch"></a>Zjištění anomálií jako dávky

1. Vytvořte metodu volanou `detectAnomaliesBatch()` pro detekci anomálií v rámci dat jako dávku. Zavolejte `sendRequest()` metodu vytvořenou výše pomocí vašeho koncového bodu, adresy URL, klíče předplatného a dat JSON. Získejte výsledek a vytiskněte ho do konzoly.

2. Pokud odpověď obsahuje `code` pole, vytiskněte kód chyby a chybovou zprávu.

3. V opačném případě najděte pozice anomálií v datové sadě. `isAnomaly`Pole odpovědi obsahuje logickou hodnotu, která se vztahuje na to, zda je daný datový bod anomálií. Získá pole JSON a projde ho a vytiskne index všech `true` hodnot. Tyto hodnoty odpovídají indexu datových bodů neobvyklé, pokud byly nalezeny.

    [!code-java[Method for batch detection](~/samples-anomaly-detector/quickstarts/java-detect-anomalies.java?name=detectBatch)]

## <a name="detect-the-anomaly-status-of-the-latest-data-point"></a>Zjistit stav anomálií nejnovějšího datového bodu

Vytvořte metodu volanou `detectAnomaliesLatest()` k detekci stavu anomálií posledního datového bodu v datové sadě. Zavolejte `sendRequest()` metodu vytvořenou výše pomocí vašeho koncového bodu, adresy URL, klíče předplatného a dat JSON. Získejte výsledek a vytiskněte ho do konzoly.

[!code-java[Latest point detection method](~/samples-anomaly-detector/quickstarts/java-detect-anomalies.java?name=detectLatest)]


## <a name="detect-change-points-in-the-data"></a>Zjištění bodů změny v datech

1. Vytvořte metodu volanou `detectChangePoints()` pro detekci anomálií v rámci dat jako dávku. Zavolejte `sendRequest()` metodu vytvořenou výše pomocí vašeho koncového bodu, adresy URL, klíče předplatného a dat JSON. Získejte výsledek a vytiskněte ho do konzoly.

2. Pokud odpověď obsahuje `code` pole, vytiskněte kód chyby a chybovou zprávu.

3. V opačném případě vyhledejte pozice bodů změn v datové sadě. `isChangePoint`Pole odpovědi obsahuje logickou hodnotu označující, zda je daný datový bod bodem změny trendu. Získejte pole JSON a Iterujte ho a vytiskněte index všech `true` hodnot. Tyto hodnoty odpovídají indexům bodů změny trendů, pokud byly nalezeny.

    [!code-java[detect change points](~/samples-anomaly-detector/quickstarts/java-detect-anomalies.java?name=detectChangePoint)]

## <a name="load-your-time-series-data-and-send-the-request"></a>Načtěte data časové řady a odešlete žádost.

1. V metodě Main aplikace si přečtěte v souboru JSON obsahujícím data, která se přidají do požadavků.

2. Zavolejte dvě funkce detekce anomálií vytvořené výše.

    [!code-java[Main method](~/samples-anomaly-detector/quickstarts/java-detect-anomalies.java?name=main)]

### <a name="example-response"></a>Příklad odpovědi

Ve formátu JSON se vrátí úspěšná odpověď. Kliknutím na následující odkazy zobrazíte odpověď JSON na GitHubu:
* [Příklad odpovědi na zjišťování dávky](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/batch-response.json)
* [Příklad odpovědi na nejnovější zjištění bodu](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/latest-point-response.json)
* [Ukázka odpovědi na detekci bodu změny](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/change-point-sample.json)

[!INCLUDE [anomaly-detector-next-steps](../includes/quickstart-cleanup-next-steps.md)]
