---
title: 'Rychlý Start: rozpoznávání digitálního inkoustu pomocí REST API pro rozpoznávání rukopisuC#'
titleSuffix: Azure Cognitive Services
description: Pomocí rozhraní API pro rozpoznávání rukopisu můžete začít rozpoznávat tahy digitálního inkoustu.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: ink-recognizer
ms.topic: quickstart
ms.date: 09/23/2019
ms.author: aahi
ms.openlocfilehash: 0c7d3ed7e2cbaee7d30f368efa004bbb3daaafdd
ms.sourcegitcommit: 9f330c3393a283faedaf9aa75b9fcfc06118b124
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/07/2019
ms.locfileid: "71996870"
---
# <a name="quickstart-recognize-digital-ink-with-the-ink-recognizer-rest-api-and-c"></a>Rychlý Start: rozpoznávání digitálního inkoustu pomocí REST API pro rozpoznávání rukopisuC#

Pomocí tohoto rychlého startu můžete začít odesílat tahy digitálního inkoustu do rozhraní API pro rozpoznávání rukopisu. Tato C# aplikace pošle požadavek rozhraní API obsahující data tahu ve formátu JSON a získá odpověď.

I když je tato aplikace napsaná v C#, rozhraní API je webová služba RESTful kompatibilní s většinou programovacích jazyků.

Obvykle byste volali rozhraní API z digitální aplikace pro psaní rukou. V tomto rychlém startu se v souboru JSON pošle data tahy perem pro následující psaný vzorek.

![Obrázek rukopisného textu](../media/handwriting-sample.jpg)

Zdrojový kód pro tento rychlý Start najdete na [GitHubu](https://go.microsoft.com/fwlink/?linkid=2089502).

## <a name="prerequisites"></a>Požadavky

- Všechny edice sady [Visual Studio 2017](https://visualstudio.microsoft.com/downloads/).
- [Newtonsoft. JSON](https://www.newtonsoft.com/json)
    - Postup instalace Newtonsoft. JSON jako balíčku NuGet v aplikaci Visual Studio:
        1. Klikněte pravým tlačítkem na **správce řešení** .
        2. Klikněte na **Spravovat balíčky NuGet...**
        3. Vyhledejte @no__t – 0 a nainstalujte balíček.
- Pokud používáte Linux/MacOS, může být tato aplikace spuštěná pomocí [mono](https://www.mono-project.com/).

- Ukázková data tahu perem pro tento rychlý Start najdete na [GitHubu](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/dotnet/InkRecognition/quickstart/example-ink-strokes.json).

### <a name="create-an-ink-recognizer-resource"></a>Vytvoření prostředku pro rozpoznávání rukopisu

[!INCLUDE [creating-an-ink-recognizer-resource](../includes/setup-instructions.md)]

## <a name="create-a-new-application"></a>Vytvoření nové aplikace

1. V aplikaci Visual Studio vytvořte nové řešení konzoly a přidejte následující balíčky. 
    
    [!code-csharp[imports](~/cognitive-services-rest-samples/dotnet/InkRecognition/quickstart/recognizeInk.cs?name=imports)]

2. Vytvořte proměnné pro klíč a koncový bod předplatného a ukázkový soubor JSON. Koncový bod bude později kombinován s `inkRecognitionUrl` pro přístup k rozhraní API. 

    [!code-csharp[endpoint file path and key variables](~/cognitive-services-rest-samples/dotnet/InkRecognition/quickstart/recognizeInk.cs?name=vars)]

## <a name="create-a-function-to-send-requests"></a>Vytvoření funkce pro odesílání požadavků

1. Vytvořte novou asynchronní funkci nazvanou `Request`, která přebírá proměnné vytvořené výše.

2. Nastavte informace o protokolu zabezpečení klienta a hlavičce pomocí objektu `HttpClient`. Nezapomeňte přidat klíč předplatného do hlavičky `Ocp-Apim-Subscription-Key`. Pak pro požadavek vytvořte objekt `StringContent`.
 
3. Odešlete požadavek s `PutAsync()`. Pokud je požadavek úspěšný, vrátí odpověď.  
    
    [!code-csharp[request example method](~/cognitive-services-rest-samples/dotnet/InkRecognition/quickstart/recognizeInk.cs?name=request)]

## <a name="send-an-ink-recognition-request"></a>Poslat požadavek na rozpoznávání rukopisu

1. Vytvořte novou funkci nazvanou `recognizeInk()`. Sestavte požadavek a odešlete ho voláním funkce `Request()` s vaším koncovým bodem, klíčem předplatného, adresou URL pro rozhraní API a daty tahu digitální barvy.

2. Deserializovat objekt JSON a zapsat ho do konzoly. 
    
    [!code-csharp[request to recognize ink data](~/cognitive-services-rest-samples/dotnet/InkRecognition/quickstart/recognizeInk.cs?name=recognize)]

## <a name="load-your-digital-ink-data"></a>Načtení dat digitálního inkoustu

Vytvořením funkce s názvem `LoadJson()` načtete soubor JSON pro tisk dat. K vytvoření `JObject` použijte `StreamReader` a `JsonTextReader` a vraťte ho.

[!code-csharp[load the JSON file](~/cognitive-services-rest-samples/dotnet/InkRecognition/quickstart/recognizeInk.cs?name=loadJson)]

## <a name="send-the-api-request"></a>Odeslat požadavek rozhraní API

1. V metodě Main aplikace načtěte data JSON pomocí funkce vytvořené výše. 

2. Zavolejte funkci `recognizeInk()` vytvořenou výše. Pomocí `System.Console.ReadKey()` nechejte okno konzoly otevřené po spuštění aplikace.
    
    [!code-csharp[file main method](~/cognitive-services-rest-samples/dotnet/InkRecognition/quickstart/recognizeInk.cs?name=main)]


## <a name="run-the-application-and-view-the-response"></a>Spuštění aplikace a zobrazení odpovědi

Spusťte aplikaci. Ve formátu JSON se vrátí úspěšná odpověď. Můžete také najít odpověď JSON na [GitHubu](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/dotnet/InkRecognition/quickstart/example-response.json).


## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Odkaz na REST API](https://go.microsoft.com/fwlink/?linkid=2089907)


Pokud chcete zjistit, jak funguje rozhraní API pro rozpoznávání rukopisu v digitální aplikaci pro rukopis, podívejte se na následující ukázkové aplikace na GitHubu:
* [C#a Univerzální platforma Windows (UWP)](https://go.microsoft.com/fwlink/?linkid=2089803)  
* [C#a Windows Presentation Foundation (WPF)](https://go.microsoft.com/fwlink/?linkid=2089804)
* [JavaScriptová aplikace pro web – prohlížeč](https://go.microsoft.com/fwlink/?linkid=2089908)       
* [Mobilní aplikace Java a Android](https://go.microsoft.com/fwlink/?linkid=2089906)
* [Mobilní aplikace SWIFT a iOS](https://go.microsoft.com/fwlink/?linkid=2089805)
