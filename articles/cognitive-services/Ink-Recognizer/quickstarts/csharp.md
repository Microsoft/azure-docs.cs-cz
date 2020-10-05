---
title: 'Rychlý Start: rozpoznávání digitálního inkoustu pomocí nástroje pro rozpoznávání rukopisu REST API a C #'
titleSuffix: Azure Cognitive Services
description: V tomto rychlém startu se dozvíte, jak používat rozhraní API pro rozpoznávání rukopisu a jazyk C# ke spuštění rozpoznávání tahů digitálního inkoustu.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: ink-recognizer
ms.topic: quickstart
ms.date: 08/24/2020
ms.author: aahi
ms.custom: devx-track-csharp
ms.openlocfilehash: a4bb02f11c7a9a75ddc96e0ee8e9f4b868f8ade5
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2020
ms.locfileid: "89051589"
---
# <a name="quickstart-recognize-digital-ink-with-the-ink-recognizer-rest-api-and-c"></a>Rychlý Start: rozpoznávání digitálního inkoustu pomocí nástroje pro rozpoznávání rukopisu REST API a C #

[!INCLUDE [ink-recognizer-deprecation](../includes/deprecation-note.md)]

Pomocí tohoto rychlého startu můžete začít odesílat tahy digitálního inkoustu do rozhraní API pro rozpoznávání rukopisu. Tato aplikace v jazyce C# pošle požadavek rozhraní API obsahující data tahu ve formátu JSON a získá odpověď.

I když je tato aplikace napsaná v jazyce C#, rozhraní API je webová služba RESTful kompatibilní s většinou programovacích jazyků.

Obvykle byste volali rozhraní API z digitální aplikace pro psaní rukou. V tomto rychlém startu se v souboru JSON pošle data tahy perem pro následující psaný vzorek.

![Obrázek rukopisného textu](../media/handwriting-sample.jpg)

Zdrojový kód pro tento rychlý Start najdete na [GitHubu](https://go.microsoft.com/fwlink/?linkid=2089502).

## <a name="prerequisites"></a>Předpoklady

- Libovolná edice sady [Visual Studio 2017](https://visualstudio.microsoft.com/downloads/)
- [Newtonsoft.Json](https://www.newtonsoft.com/json)
    - Instalace Newtonsoft.Jsjako balíčku NuGet v aplikaci Visual Studio:
        1. Klikněte pravým tlačítkem na **správce řešení** .
        2. Klikněte na **Spravovat balíčky NuGet...**
        3. Vyhledat `Newtonsoft.Json` a nainstalovat balíček
- Pokud používáte Linux/MacOS, může být tato aplikace spuštěná pomocí [mono](https://www.mono-project.com/).

- Ukázková data tahu perem pro tento rychlý Start najdete na [GitHubu](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/dotnet/Vision/InkRecognition/quickstart/example-ink-strokes.json).

### <a name="create-an-ink-recognizer-resource"></a>Vytvoření prostředku pro rozpoznávání rukopisu

[!INCLUDE [creating-an-ink-recognizer-resource](../includes/setup-instructions.md)]

## <a name="create-a-new-application"></a>Vytvoření nové aplikace

1. V aplikaci Visual Studio vytvořte nové řešení konzoly a přidejte následující balíčky. 
    
    [!code-csharp[import statements](~/cognitive-services-rest-samples/dotnet/Vision/InkRecognition/quickstart/recognizeInk.cs?name=imports)]

2. Vytvořte proměnné pro klíč a koncový bod předplatného a ukázkový soubor JSON. Koncový bod bude později kombinován s `inkRecognitionUrl` pro přístup k rozhraní API. 

    [!code-csharp[endpoint file and key variables](~/cognitive-services-rest-samples/dotnet/Vision/InkRecognition/quickstart/recognizeInk.cs?name=vars)]

## <a name="create-a-function-to-send-requests"></a>Vytvoření funkce pro odesílání požadavků

1. Vytvořte novou asynchronní funkci s názvem `Request` , která přijímá proměnné vytvořené výše.

2. Pomocí objektu nastavte informace o protokolu zabezpečení a hlavičce klienta `HttpClient` . Nezapomeňte do hlavičky přidat svůj klíč předplatného `Ocp-Apim-Subscription-Key` . Pak vytvořte `StringContent` objekt pro požadavek.
 
3. Odešlete žádost pomocí `PutAsync()` . Pokud je požadavek úspěšný, vrátí odpověď.  
    
    [!code-csharp[request example method](~/cognitive-services-rest-samples/dotnet/Vision/InkRecognition/quickstart/recognizeInk.cs?name=request)]

## <a name="send-an-ink-recognition-request"></a>Poslat požadavek na rozpoznávání rukopisu

1. Vytvořte novou funkci s názvem `recognizeInk()` . Sestavte požadavek a odešlete ho voláním `Request()` funkce s vaším koncovým bodem, klíčem předplatného, adresou URL pro rozhraní API a datovým tahem digitálního inkoustu.

2. Deserializovat objekt JSON a zapsat ho do konzoly. 
    
    [!code-csharp[request to recognize ink data](~/cognitive-services-rest-samples/dotnet/Vision/InkRecognition/quickstart/recognizeInk.cs?name=recognize)]

## <a name="load-your-digital-ink-data"></a>Načtení dat digitálního inkoustu

Vytvořte funkci nazvanou `LoadJson()` , která načte soubor JSON pro tisk dat. Pomocí `StreamReader` a `JsonTextReader` vytvořte `JObject` a vraťte ho.

[!code-csharp[load the JSON file](~/cognitive-services-rest-samples/dotnet/Vision/InkRecognition/quickstart/recognizeInk.cs?name=loadJson)]

## <a name="send-the-api-request"></a>Odeslat požadavek rozhraní API

1. V metodě Main aplikace načtěte data JSON pomocí funkce vytvořené výše. 

2. Zavolejte `recognizeInk()` funkci vytvořenou výše. Slouží `System.Console.ReadKey()` k zachování otevřeného okna konzoly po spuštění aplikace.
    
    [!code-csharp[file main method](~/cognitive-services-rest-samples/dotnet/Vision/InkRecognition/quickstart/recognizeInk.cs?name=main)]


## <a name="run-the-application-and-view-the-response"></a>Spuštění aplikace a zobrazení odpovědi

Spusťte aplikaci. Ve formátu JSON se vrátí úspěšná odpověď. Můžete také najít odpověď JSON na [GitHubu](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/dotnet/Vision/InkRecognition/quickstart/example-response.json).


## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [REST API – referenční informace](https://go.microsoft.com/fwlink/?linkid=2089907)


Pokud chcete zjistit, jak funguje rozhraní API pro rozpoznávání rukopisu v digitální aplikaci pro rukopis, podívejte se na následující ukázkové aplikace na GitHubu:
* [C# a Univerzální platforma Windows (UPW)](https://go.microsoft.com/fwlink/?linkid=2089803)  
* [C# a Windows Presentation Foundation (WPF)](https://go.microsoft.com/fwlink/?linkid=2089804)
* [Aplikace webového prohlížeče v Javascriptu](https://go.microsoft.com/fwlink/?linkid=2089908)       
* [Mobilní aplikace v Javě a Androidu](https://go.microsoft.com/fwlink/?linkid=2089906)
* [Mobilní aplikace ve Swiftu a iOS](https://go.microsoft.com/fwlink/?linkid=2089805)
