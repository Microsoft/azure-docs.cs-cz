---
title: 'Rychlý Start: zjištění anomálií v datech časových řad pomocí detektoru anomálií REST API a Java'
titleSuffix: Azure Cognitive Services
description: Rozhraní API pro detekci anomálií použijte k detekci anomálií v datové řadě buď jako dávku, nebo na streamovaná data.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: quickstart
ms.date: 07/26/2019
ms.author: aahi
ms.openlocfilehash: 6d54ec8df08e7c3d76a97c2531c21b1fd4d130b9
ms.sourcegitcommit: ae461c90cada1231f496bf442ee0c4dcdb6396bc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/17/2019
ms.locfileid: "72554749"
---
# <a name="quickstart-detect-anomalies-in-your-time-series-data-using-the-anomaly-detector-rest-api-and-java"></a>Rychlý Start: zjištění anomálií v datech časových řad pomocí detektoru anomálií REST API a Java

Tento rychlý Start vám umožní začít používat dva režimy zjišťování rozhraní API pro detekci anomálií ke zjištění anomálií v datech časových řad. Tato aplikace Java odesílá dvě požadavky rozhraní API obsahující data časových řad ve formátu JSON a získává odpovědi.

| Požadavek rozhraní API                                        | Výstup aplikace                                                                                                                         |
|----------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------|
| Zjištění anomálií jako dávky                        | Odpověď JSON obsahující stav anomálie (a další data) pro každý datový bod v datech časové řady a pozice všech zjištěných anomálií. |
| Zjistit stav anomálií nejnovějšího datového bodu | Odpověď JSON obsahující stav anomálie (a další data) pro poslední datový bod v datech časové řady.                                                                                                                                         |

 I když je tato aplikace napsaná v jazyce Java, rozhraní API je webová služba RESTful kompatibilní s většinou programovacích jazyků.

## <a name="prerequisites"></a>Předpoklady

- [Java &trade; Development Kit (JDK) 7](https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html) nebo novější.

- Importovat tyto knihovny z úložiště Maven
    - [JSON v balíčku Java](https://mvnrepository.com/artifact/org.json/json)
    - Balíček [Apache HttpClient](https://mvnrepository.com/artifact/org.apache.httpcomponents/httpclient)

- Soubor JSON, který obsahuje datové body časové řady. Ukázková data pro tento rychlý Start najdete na [GitHubu](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/request-data.json).

### <a name="create-an-anomaly-detector-resource"></a>Vytvoření prostředku detektoru anomálií

[!INCLUDE [anomaly-detector-resource-creation](../../../../includes/cognitive-services-anomaly-detector-resource-cli.md)]

## <a name="create-a-new-application"></a>Vytvoření nové aplikace

1. Vytvořte nový projekt Java a importujte následující knihovny.
    
    [!code-java[Import statements](~/samples-anomaly-detector/quickstarts/java-detect-anomalies.java?name=imports)]

2. Vytvořte proměnné pro svůj klíč předplatného a koncový bod. Níže jsou uvedeny identifikátory URI, které lze použít pro detekci anomálií. Ty se připojí ke koncovému bodu služby později a vytvoří adresy URL žádostí o rozhraní API.

    |Metoda detekce  |IDENTIFIKÁTOR URI  |
    |---------|---------|
    |Zjišťování dávky    | `/anomalydetector/v1.0/timeseries/entire/detect`        |
    |Zjišťování nejnovějšího datového bodu     | `/anomalydetector/v1.0/timeseries/last/detect`        |

    [!code-java[Initial key and endpoint variables](~/samples-anomaly-detector/quickstarts/java-detect-anomalies.java?name=vars)]

## <a name="create-a-function-to-send-requests"></a>Vytvoření funkce pro odesílání požadavků

1. Vytvořte novou funkci nazvanou `sendRequest()`, která přebírá proměnné vytvořené výše. Pak proveďte následující kroky.

2. Vytvoří objekt `CloseableHttpClient`, který může odesílat požadavky do rozhraní API. Odešlete žádost do objektu žádosti o `HttpPost` kombinováním koncového bodu a adresy URL detektoru anomálií.

3. Pomocí funkce `setHeader()` žádosti nastavte hlavičku `Content-Type` na `application/json` a přidejte svůj klíč předplatného do hlavičky `Ocp-Apim-Subscription-Key`.

4. K odeslání dat použijte funkci `setEntity()` žádosti.

5. K odeslání žádosti použijte funkci klienta `execute()` a uložte ji do objektu `CloseableHttpResponse`.

6. Vytvořte objekt `HttpEntity` pro uložení obsahu odpovědi. Získejte obsah pomocí `getEntity()`. Pokud odpověď není prázdná, vraťte ji.

[!code-java[API request method](~/samples-anomaly-detector/quickstarts/java-detect-anomalies.java?name=request)]

## <a name="detect-anomalies-as-a-batch"></a>Zjištění anomálií jako dávky

1. Vytvořte metodu nazvanou `detectAnomaliesBatch()` pro detekci anomálií v datech jako dávku. Zavolejte metodu `sendRequest()` vytvořenou výše s vaším koncovým bodem, adresou URL, klíčem předplatného a data JSON. Získejte výsledek a vytiskněte ho do konzoly.

2. Pokud odpověď obsahuje `code` pole, vytiskněte kód chyby a chybovou zprávu.

3. V opačném případě najděte pozice anomálií v datové sadě. Pole `isAnomaly` odpovědi obsahuje logickou hodnotu týkající se toho, zda je daný datový bod anomálií. Získá pole JSON a projde ho a vytiskne index všech `true`ch hodnot. Tyto hodnoty odpovídají indexu datových bodů neobvyklé, pokud byly nalezeny.

[!code-java[Method for batch detection](~/samples-anomaly-detector/quickstarts/java-detect-anomalies.java?name=detectBatch)]

## <a name="detect-the-anomaly-status-of-the-latest-data-point"></a>Zjistit stav anomálií nejnovějšího datového bodu

* Vytvořte metodu nazvanou `detectAnomaliesLatest()` k detekci stavu anomálií posledního datového bodu v datové sadě. Zavolejte metodu `sendRequest()` vytvořenou výše s vaším koncovým bodem, adresou URL, klíčem předplatného a data JSON. Získejte výsledek a vytiskněte ho do konzoly.

[!code-java[Latest point detection method](~/samples-anomaly-detector/quickstarts/java-detect-anomalies.java?name=detectLatest)]

## <a name="load-your-time-series-data-and-send-the-request"></a>Načtěte data časové řady a odešlete žádost.

1. V metodě Main aplikace si přečtěte v souboru JSON obsahujícím data, která se přidají do požadavků.

2. Zavolejte dvě funkce detekce anomálií vytvořené výše.

[!code-java[Main method](~/samples-anomaly-detector/quickstarts/java-detect-anomalies.java?name=main)]

### <a name="example-response"></a>Příklad odpovědi

Ve formátu JSON se vrátí úspěšná odpověď. Kliknutím na následující odkazy zobrazíte odpověď JSON na GitHubu:
* [Příklad odpovědi na zjišťování dávky](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/batch-response.json)
* [Příklad odpovědi na nejnovější zjištění bodu](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/latest-point-response.json)

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
>[Detekce anomálií streamování pomocí Azure Databricks](../tutorials/anomaly-detection-streaming-databricks.md)

* Co je rozhraní API pro detekci [anomálií?](../overview.md)
* [Osvědčené postupy](../concepts/anomaly-detection-best-practices.md) při použití rozhraní API detektoru anomálií
* Zdrojový kód pro tuto ukázku najdete na [GitHubu](https://github.com/Azure-Samples/AnomalyDetector/blob/master/quickstarts/sdk/csharp-sdk-sample.cs).
