---
title: 'Úvodní příručka: Rozpoznávání digitálního inkoustu pomocí rozhraní REST API a Javy pro rozpoznávání rukopisu'
titleSuffix: Azure Cognitive Services
description: Pomocí rozhraní API pro rozpoznávání rukopisu začněte rozpoznávat tahy digitálních inkoustů na tomto rychlém startu.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: ink-recognizer
ms.topic: quickstart
ms.date: 12/17/2019
ms.author: aahi
ms.openlocfilehash: d2cd4e56477ea39587ce318538c9ddd84c51b03b
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75448121"
---
# <a name="quickstart-recognize-digital-ink-with-the-ink-recognizer-rest-api-and-java"></a>Úvodní příručka: Rozpoznávání digitálního inkoustu pomocí rozhraní REST API a Javy pro rozpoznávání rukopisu

Pomocí tohoto rychlého startu můžete začít používat rozhraní API rozpoznávání rukopisu u digitálních tahů perem. Tato aplikace Java odešle požadavek rozhraní API obsahující data tahu inkoustu ve formátu JSON a získá odpověď.

Zatímco tato aplikace je napsána v Javě, API je RESTful webová služba kompatibilní s většinou programovacích jazyků.

Obvykle byste volat rozhraní API z digitální aplikace rukopisu. Tento rychlý start odesílá data tahu rukopisu pro následující ručně psaný vzorek ze souboru JSON.

![obrázek ručně psaného textu](../media/handwriting-sample.jpg)

Zdrojový kód pro tento rychlý start najdete na [GitHubu](https://go.microsoft.com/fwlink/?linkid=2089904).

## <a name="prerequisites"></a>Požadavky

- [Java&trade; Development Kit (JDK) 7](https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html) nebo novější.

- Import těchto knihoven z úložiště Maven
    - [JSON v balíčku Java](https://mvnrepository.com/artifact/org.json/json)
    - [Balíček Apache HttpClient](https://mvnrepository.com/artifact/org.apache.httpcomponents/httpclient)

- Příklad dat tahů perem pro tento rychlý start najdete na [GitHubu](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/java/InkRecognition/quickstart/example-ink-strokes.json).

### <a name="create-an-ink-recognizer-resource"></a>Vytvoření prostředku nástroje pro rozpoznávání rukopisu

[!INCLUDE [creating an ink recognizer resource](../includes/setup-instructions.md)]

## <a name="create-a-new-application"></a>Vytvoření nové aplikace

1. V oblíbeném integrovaném vývojovém prostředí nebo editoru vytvořte nový projekt Java a naimportujte následující knihovny.
    
    [!code-java[import statements](~/cognitive-services-rest-samples/java/InkRecognition/quickstart/RecognizeInk.java?name=imports)]

2. Vytvořte proměnné pro klíč předplatného, koncový bod a soubor JSON. Koncový bod bude později připojen k identifikátoru URI nástroje pro rozpoznávání rukopisu.

    [!code-java[initial vars](~/cognitive-services-rest-samples/java/InkRecognition/quickstart/RecognizeInk.java?name=vars)]

## <a name="create-a-function-to-send-requests"></a>Vytvoření funkce pro odesílání požadavků

1. Vytvořte novou `sendRequest()` funkci s názvem, která přebírá proměnné vytvořené výše. Poté proveďte následující kroky.

2. Vytvořte `CloseableHttpClient` objekt, který může odesílat požadavky do rozhraní API. Odešlete požadavek `HttpPut` na objekt požadavku kombinací koncového bodu a adresy URL rozpoznávání rukopisu.

3. Pomocí `setHeader()` funkce požadavku nastavte `Content-Type` záhlaví na `application/json`a přidejte klíč `Ocp-Apim-Subscription-Key` předplatného do záhlaví.

4. Použijte `setEntity()` funkci požadavku k datům, která mají být odeslána.   

5. Pomocí funkce klienta `execute()` odešlete požadavek a `CloseableHttpResponse` uložte jej do objektu. 

6. Vytvořte `HttpEntity` objekt pro uložení obsahu odpovědi. Získejte obsah `getEntity()`s . Pokud odpověď není prázdná, vraťte ji.
    
    [!code-java[send a request](~/cognitive-services-rest-samples/java/InkRecognition/quickstart/RecognizeInk.java?name=sendRequest)]

## <a name="send-an-ink-recognition-request"></a>Odeslání žádosti o rozpoznání inkoustu

Vytvořte metodu volanou `recognizeInk()` k rozpoznání dat tahu rukopisu. Zavolejte `sendRequest()` výše vytvořenou metodu pomocí koncového bodu, adresy URL, klíče předplatného a dat json. Získejte výsledek a vytiskněte jej do konzole.

[!code-java[recognizeInk](~/cognitive-services-rest-samples/java/InkRecognition/quickstart/RecognizeInk.java?name=recognizeInk)]

## <a name="load-your-digital-ink-data-and-send-the-request"></a>Načtení digitálních inkoustových dat a odeslání požadavku

1. V hlavní metodě aplikace si přečtěte v souboru JSON obsahující data, která budou přidány do požadavků.

2. Zavolejte výše vytvořenou funkci rozpoznávání inkoustu.
    
    [!code-java[main method](~/cognitive-services-rest-samples/java/InkRecognition/quickstart/RecognizeInk.java?name=main)]


## <a name="run-the-application-and-view-the-response"></a>Spuštění aplikace a zobrazení odpovědi

Spusťte aplikaci. Úspěšná odpověď je vrácena ve formátu JSON. Odpověď JSON najdete také na [GitHubu](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/java/InkRecognition/quickstart/example-response.json).

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Odkaz na rozhraní REST API](https://go.microsoft.com/fwlink/?linkid=2089907)


Pokud se chcete podívat, jak rozhraní API pro rozpoznávání rukopisu funguje v digitální aplikaci rukopisu, podívejte se na následující ukázkové aplikace na GitHubu:
* [C# a Univerzální platforma Windows (UPW)](https://go.microsoft.com/fwlink/?linkid=2089803)  
* [C# a Windows Presentation Foundation (WPF)](https://go.microsoft.com/fwlink/?linkid=2089804)
* [Aplikace webového prohlížeče v Javascriptu](https://go.microsoft.com/fwlink/?linkid=2089908)       
* [Mobilní aplikace v Javě a Androidu](https://go.microsoft.com/fwlink/?linkid=2089906)
* [Mobilní aplikace ve Swiftu a iOS](https://go.microsoft.com/fwlink/?linkid=2089805)
