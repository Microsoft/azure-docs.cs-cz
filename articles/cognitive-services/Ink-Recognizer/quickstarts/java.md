---
title: 'Rychlý Start: rozpoznávání digitálního inkoustu pomocí nástroje pro rozpoznávání rukopisu REST API a Java'
titleSuffix: Azure Cognitive Services
description: Pomocí rozhraní API pro rozpoznávání rukopisu a Java začněte rozpoznávat tahy digitálního inkoustu v tomto rychlém startu.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: ink-recognizer
ms.topic: quickstart
ms.date: 08/24/2020
ms.author: aahi
ms.custom: devx-track-java
ms.openlocfilehash: dea46a07a7357be6079c52634be8ea2ff79cc8f3
ms.sourcegitcommit: 22da82c32accf97a82919bf50b9901668dc55c97
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/08/2020
ms.locfileid: "94369116"
---
# <a name="quickstart-recognize-digital-ink-with-the-ink-recognizer-rest-api-and-java"></a>Rychlý Start: rozpoznávání digitálního inkoustu pomocí nástroje pro rozpoznávání rukopisu REST API a Java

[!INCLUDE [ink-recognizer-deprecation](../includes/deprecation-note.md)]

Pomocí tohoto rychlého startu můžete začít používat rozhraní API pro rozpoznávání rukopisu na tahy digitálního pera. Tato aplikace Java pošle požadavek rozhraní API obsahující data tahu ve formátu JSON a získá odpověď.

I když je tato aplikace napsaná v jazyce Java, rozhraní API je webová služba RESTful kompatibilní s většinou programovacích jazyků.

Obvykle byste volali rozhraní API z digitální aplikace pro psaní rukou. V tomto rychlém startu se v souboru JSON pošle data tahy perem pro následující psaný vzorek.

![Obrázek rukopisného textu](../media/handwriting-sample.jpg)

Zdrojový kód pro tento rychlý Start najdete na [GitHubu](https://go.microsoft.com/fwlink/?linkid=2089904).

## <a name="prerequisites"></a>Požadavky

- [Java &trade; Development Kit (JDK) 7](https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html) nebo novější.

- Importovat tyto knihovny z úložiště Maven
    - [JSON v balíčku Java](https://mvnrepository.com/artifact/org.json/json)
    - Balíček [Apache HttpClient](https://mvnrepository.com/artifact/org.apache.httpcomponents/httpclient)

- Ukázková data tahu perem pro tento rychlý Start najdete na [GitHubu](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/java/InkRecognition/quickstart/example-ink-strokes.json).

### <a name="create-an-ink-recognizer-resource"></a>Vytvoření prostředku pro rozpoznávání rukopisu

[!INCLUDE [creating an ink recognizer resource](../includes/setup-instructions.md)]

## <a name="create-a-new-application"></a>Vytvoření nové aplikace

1. V oblíbeném integrovaném vývojovém prostředí nebo editoru vytvořte nový projekt Java a naimportujte následující knihovny.
    
    [!code-java[import statements](~/cognitive-services-rest-samples/java/InkRecognition/quickstart/RecognizeInk.java?name=imports)]

2. Vytvořte proměnné pro svůj klíč předplatného, koncový bod a soubor JSON. Koncový bod bude později připojený k identifikátoru URI nástroje pro rozpoznávání rukopisu.

    [!code-java[initial vars](~/cognitive-services-rest-samples/java/InkRecognition/quickstart/RecognizeInk.java?name=vars)]

## <a name="create-a-function-to-send-requests"></a>Vytvoření funkce pro odesílání požadavků

1. Vytvořte novou funkci s názvem `sendRequest()` , která přijímá proměnné vytvořené výše. Pak proveďte následující kroky.

2. Vytvořte `CloseableHttpClient` objekt, který může odesílat požadavky do rozhraní API. Odešlete požadavek do `HttpPut` objektu žádosti kombinací koncového bodu a adresy URL nástroje pro rozpoznávání rukopisu.

3. Pomocí `setHeader()` funkce Request nastavte `Content-Type` hlavičku na `application/json` a přidejte do hlavičky klíč předplatného `Ocp-Apim-Subscription-Key` .

4. Použijte `setEntity()` funkci žádosti na data, která chcete odeslat.   

5. Použijte `execute()` funkci klienta k odeslání žádosti a uložte ji do `CloseableHttpResponse` objektu. 

6. Vytvořte `HttpEntity` objekt pro uložení obsahu odpovědi. Získejte obsah pomocí `getEntity()` . Pokud odpověď není prázdná, vraťte ji.
    
    [!code-java[send a request](~/cognitive-services-rest-samples/java/InkRecognition/quickstart/RecognizeInk.java?name=sendRequest)]

## <a name="send-an-ink-recognition-request"></a>Poslat požadavek na rozpoznávání rukopisu

Vytvořte metodu volanou `recognizeInk()` pro rozpoznání dat tahu perem. Zavolejte `sendRequest()` metodu vytvořenou výše pomocí vašeho koncového bodu, adresy URL, klíče předplatného a dat JSON. Získejte výsledek a vytiskněte ho do konzoly.

[!code-java[recognizeInk](~/cognitive-services-rest-samples/java/InkRecognition/quickstart/RecognizeInk.java?name=recognizeInk)]

## <a name="load-your-digital-ink-data-and-send-the-request"></a>Načtení dat digitálního inkoustu a odeslání žádosti

1. V metodě Main aplikace si přečtěte v souboru JSON obsahujícím data, která se přidají do požadavků.

2. Zavolejte funkci rozpoznávání rukopisu vytvořenou výše.
    
    [!code-java[main method](~/cognitive-services-rest-samples/java/InkRecognition/quickstart/RecognizeInk.java?name=main)]


## <a name="run-the-application-and-view-the-response"></a>Spuštění aplikace a zobrazení odpovědi

Spusťte aplikaci. Ve formátu JSON se vrátí úspěšná odpověď. Můžete také najít odpověď JSON na [GitHubu](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/java/InkRecognition/quickstart/example-response.json).

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [REST API – referenční informace](/rest/api/cognitiveservices/inkrecognizer/inkrecognizer)


Pokud chcete zjistit, jak funguje rozhraní API pro rozpoznávání rukopisu v digitální aplikaci pro rukopis, podívejte se na následující ukázkové aplikace na GitHubu:
* [C# a Univerzální platforma Windows (UPW)](https://go.microsoft.com/fwlink/?linkid=2089803)  
* [C# a Windows Presentation Foundation (WPF)](https://go.microsoft.com/fwlink/?linkid=2089804)
* [Aplikace webového prohlížeče v Javascriptu](https://go.microsoft.com/fwlink/?linkid=2089908)       
* [Mobilní aplikace v Javě a Androidu](https://go.microsoft.com/fwlink/?linkid=2089906)
* [Mobilní aplikace ve Swiftu a iOS](https://go.microsoft.com/fwlink/?linkid=2089805)