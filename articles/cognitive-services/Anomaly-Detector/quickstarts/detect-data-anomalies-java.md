---
title: 'Úvodní příručka: Detekce anomálií v datech časových řad pomocí rozhraní REST API a Javy detektoru anomálií'
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
ms.openlocfilehash: 4e7cc969ff2c36415b8fd1cebafa1873f9efd38c
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "80239054"
---
# <a name="quickstart-detect-anomalies-in-your-time-series-data-using-the-anomaly-detector-rest-api-and-java"></a>Úvodní příručka: Detekce anomálií v datech časových řad pomocí rozhraní REST API a Javy detektoru anomálií

Pomocí tohoto rychlého startu můžete začít používat dva režimy detekce rozhraní API detektoru anomálií k detekci anomálií v datech časových řad. Tato aplikace Java odešle dva požadavky rozhraní API obsahující data časových řad ve formátu JSON a získá odpovědi.

| Požadavek na rozhraní API                                        | Výstup aplikace                                                                                                                         |
|----------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------|
| Detekce anomálií jako dávky                        | Odpověď JSON obsahující stav anomálie (a další data) pro každý datový bod v datech časových řad a pozice všech zjištěných anomálií. |
| Zjištění stavu anomálií nejnovějšího datového bodu | Odpověď JSON obsahující stav anomálie (a další data) pro nejnovější datový bod v datech časové řady.                                                                                                                                         |

 Zatímco tato aplikace je napsána v Javě, API je RESTful webová služba kompatibilní s většinou programovacích jazyků. Zdrojový kód pro tento rychlý start najdete na [GitHubu](https://github.com/Azure-Samples/AnomalyDetector/blob/master/quickstarts/java-detect-anomalies.java).

## <a name="prerequisites"></a>Požadavky

- [Java&trade; Development Kit (JDK) 7](https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html) nebo novější.
- Klíč a koncový bod detektoru anomálií
- Import těchto knihoven z úložiště Maven
    - [JSON v balíčku Java](https://mvnrepository.com/artifact/org.json/json)
    - [Balíček Apache HttpClient](https://mvnrepository.com/artifact/org.apache.httpcomponents/httpclient)

- Soubor JSON obsahující datové body časových řad. Ukázková data pro tento rychlý start lze nalézt na [GitHubu](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/request-data.json).

### <a name="create-an-anomaly-detector-resource"></a>Vytvoření prostředku detektoru anomálií

[!INCLUDE [anomaly-detector-resource-creation](../../../../includes/cognitive-services-anomaly-detector-resource-cli.md)]

## <a name="create-a-new-application"></a>Vytvoření nové aplikace

1. Vytvořte nový projekt Java a importujte následující knihovny.
    
    [!code-java[Import statements](~/samples-anomaly-detector/quickstarts/java-detect-anomalies.java?name=imports)]

2. Vytvořte proměnné pro klíč předplatného a koncový bod. Níže jsou identifikátory URI, které můžete použít pro detekci anomálií. Ty budou později připojeny ke koncovému bodu služby, aby se vytvořily adresy URL požadavků rozhraní API.

    |Metoda detekce  |Identifikátor URI  |
    |---------|---------|
    |Detekce dávky    | `/anomalydetector/v1.0/timeseries/entire/detect`        |
    |Detekce na nejnovějším datovém bodu     | `/anomalydetector/v1.0/timeseries/last/detect`        |

    [!code-java[Initial key and endpoint variables](~/samples-anomaly-detector/quickstarts/java-detect-anomalies.java?name=vars)]

## <a name="create-a-function-to-send-requests"></a>Vytvoření funkce pro odesílání požadavků

1. Vytvořte novou `sendRequest()` funkci s názvem, která přebírá proměnné vytvořené výše. Poté proveďte následující kroky.

2. Vytvořte `CloseableHttpClient` objekt, který může odesílat požadavky do rozhraní API. Odešlete požadavek `HttpPost` na objekt požadavku kombinací koncového bodu a adresy URL detektoru anomálií.

3. Pomocí `setHeader()` funkce požadavku nastavte `Content-Type` záhlaví na `application/json`a přidejte klíč `Ocp-Apim-Subscription-Key` předplatného do záhlaví.

4. Použijte `setEntity()` funkci požadavku k datům, která mají být odeslána.

5. Pomocí funkce klienta `execute()` odešlete požadavek a `CloseableHttpResponse` uložte jej do objektu.

6. Vytvořte `HttpEntity` objekt pro uložení obsahu odpovědi. Získejte obsah `getEntity()`s . Pokud odpověď není prázdná, vraťte ji.

    [!code-java[API request method](~/samples-anomaly-detector/quickstarts/java-detect-anomalies.java?name=request)]

## <a name="detect-anomalies-as-a-batch"></a>Detekce anomálií jako dávky

1. Vytvořte metodu volanou `detectAnomaliesBatch()` ke zjištění anomálií v rámci dat jako dávka. Zavolejte `sendRequest()` výše vytvořenou metodu pomocí koncového bodu, adresy URL, klíče předplatného a dat json. Získejte výsledek a vytiskněte jej do konzole.

2. Pokud odpověď `code` obsahuje pole, vytiskněte kód chyby a chybovou zprávu.

3. V opačném případě najděte pozice anomálií v datové sadě. `isAnomaly` Pole odpovědi obsahuje logickou hodnotu vztahující se k tomu, zda je daný datový bod anomálií. Získejte pole JSON a iterate přes něj, `true` tisk indexu všechny hodnoty. Tyto hodnoty odpovídají indexu anomálních datových bodů, pokud byly nalezeny.

    [!code-java[Method for batch detection](~/samples-anomaly-detector/quickstarts/java-detect-anomalies.java?name=detectBatch)]

## <a name="detect-the-anomaly-status-of-the-latest-data-point"></a>Zjištění stavu anomálií nejnovějšího datového bodu

Vytvořte metodu volanou `detectAnomaliesLatest()` ke zjištění stavu anomálií posledního datového bodu v sadě dat. Zavolejte `sendRequest()` výše vytvořenou metodu pomocí koncového bodu, adresy URL, klíče předplatného a dat json. Získejte výsledek a vytiskněte jej do konzole.

[!code-java[Latest point detection method](~/samples-anomaly-detector/quickstarts/java-detect-anomalies.java?name=detectLatest)]

## <a name="load-your-time-series-data-and-send-the-request"></a>Načtěte data časových řad a odešlete požadavek

1. V hlavní metodě aplikace si přečtěte v souboru JSON obsahující data, která budou přidány do požadavků.

2. Volání dvou funkcí detekce anomálií vytvořených výše.

    [!code-java[Main method](~/samples-anomaly-detector/quickstarts/java-detect-anomalies.java?name=main)]

### <a name="example-response"></a>Příklad odpovědi

Úspěšná odpověď je vrácena ve formátu JSON. Kliknutím na níže uvedené odkazy zobrazíte odpověď JSON na GitHubu:
* [Příklad odpovědi detekce dávky](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/batch-response.json)
* [Příklad nejnovější odpovědi na detekci bodů](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/latest-point-response.json)

[!INCLUDE [anomaly-detector-next-steps](../includes/quickstart-cleanup-next-steps.md)]
