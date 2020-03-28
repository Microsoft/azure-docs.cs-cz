---
title: 'Úvodní příručka: Rozpoznání digitálního inkoustu pomocí rozhraní REST API pro rozpoznávání inkoustu a c #'
titleSuffix: Azure Cognitive Services
description: Tento rychlý start ukazuje, jak pomocí rozhraní API rozpoznávání rukopisu začít rozpoznávat tahy digitálních perků.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: ink-recognizer
ms.topic: quickstart
ms.date: 12/17/2019
ms.author: aahi
ms.openlocfilehash: c24d055f1904453d2f512a278f00e23c6fea1d9b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "80371381"
---
# <a name="quickstart-recognize-digital-ink-with-the-ink-recognizer-rest-api-and-c"></a>Úvodní příručka: Rozpoznání digitálního inkoustu pomocí rozhraní REST API pro rozpoznávání inkoustu a c #

Pomocí tohoto rychlého startu můžete začít odesílat digitální tahy perem do rozhraní API pro rozpoznávání rukopisu. Tato aplikace Jazyka C# odešle požadavek rozhraní API obsahující data tahu perem ve formátu JSON a získá odpověď.

Zatímco tato aplikace je napsána v jazyce C#, ROZHRANÍ API je restful webová služba kompatibilní s většinou programovacích jazyků.

Obvykle byste volat rozhraní API z digitální aplikace rukopisu. Tento rychlý start odesílá data tahu rukopisu pro následující ručně psaný vzorek ze souboru JSON.

![obrázek ručně psaného textu](../media/handwriting-sample.jpg)

Zdrojový kód pro tento rychlý start najdete na [GitHubu](https://go.microsoft.com/fwlink/?linkid=2089502).

## <a name="prerequisites"></a>Požadavky

- Libovolná edice sady [Visual Studio 2017](https://visualstudio.microsoft.com/downloads/)
- [Newtonsoft.Json](https://www.newtonsoft.com/json)
    - Instalace Newtonsoft.Json jako balíčku NuGet ve visual studiu:
        1. Klikněte pravým tlačítkem myši na **Správce řešení**
        2. Klikněte na **Spravovat balíčky NuGet...**
        3. Vyhledejte `Newtonsoft.Json` a nainstalujte balíček
- Pokud používáte Linux/MacOS, lze tuto aplikaci schovat pomocí [Mono](https://www.mono-project.com/).

- Příklad dat tahů perem pro tento rychlý start najdete na [GitHubu](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/dotnet/Vision/InkRecognition/quickstart/example-ink-strokes.json).

### <a name="create-an-ink-recognizer-resource"></a>Vytvoření prostředku nástroje pro rozpoznávání rukopisu

[!INCLUDE [creating-an-ink-recognizer-resource](../includes/setup-instructions.md)]

## <a name="create-a-new-application"></a>Vytvoření nové aplikace

1. V sadě Visual Studio vytvořte nové řešení konzoly a přidejte následující balíčky. 
    
    [!code-csharp[import statements](~/cognitive-services-rest-samples/dotnet/Vision/InkRecognition/quickstart/recognizeInk.cs?name=imports)]

2. Vytvořte proměnné pro klíč předplatného a koncový bod a příklad souboru JSON. Koncový bod bude později `inkRecognitionUrl` kombinovat s pro přístup k rozhraní API. 

    [!code-csharp[endpoint file and key variables](~/cognitive-services-rest-samples/dotnet/Vision/InkRecognition/quickstart/recognizeInk.cs?name=vars)]

## <a name="create-a-function-to-send-requests"></a>Vytvoření funkce pro odesílání požadavků

1. Vytvořte novou asynchronní funkci s názvem, `Request` která přebírá výše vytvořené proměnné.

2. Nastavte protokol zabezpečení klienta a informace `HttpClient` záhlaví pomocí objektu. Nezapomeňte do `Ocp-Apim-Subscription-Key` záhlaví přidat klíč předplatného. Pak vytvořte `StringContent` objekt pro požadavek.
 
3. Odeslat požadavek s . `PutAsync()` Pokud je požadavek úspěšný, vraťte odpověď.  
    
    [!code-csharp[request example method](~/cognitive-services-rest-samples/dotnet/Vision/InkRecognition/quickstart/recognizeInk.cs?name=request)]

## <a name="send-an-ink-recognition-request"></a>Odeslání žádosti o rozpoznání inkoustu

1. Vytvořte novou `recognizeInk()`funkci nazvanou . Vytvořte požadavek a odešlete jej voláním `Request()` funkce s koncovým bodem, klíčem předplatného, adresou URL rozhraní API a daty digitálního tahu perem.

2. Deserializujte objekt JSON a zapište jej do konzoly. 
    
    [!code-csharp[request to recognize ink data](~/cognitive-services-rest-samples/dotnet/Vision/InkRecognition/quickstart/recognizeInk.cs?name=recognize)]

## <a name="load-your-digital-ink-data"></a>Načtení digitálních dat inkoustu

Vytvořte funkci `LoadJson()` volanou k načtení souboru JSON o inkoustových datech. Použijte `StreamReader` a `JsonTextReader` a `JObject` vytvořit a vrátit.

[!code-csharp[load the JSON file](~/cognitive-services-rest-samples/dotnet/Vision/InkRecognition/quickstart/recognizeInk.cs?name=loadJson)]

## <a name="send-the-api-request"></a>Odeslat požadavek rozhraní API

1. V hlavní metodě aplikace načtěte data JSON pomocí výše vytvořené funkce. 

2. Volání `recognizeInk()` výše vytvořené funkce. Slouží `System.Console.ReadKey()` k zachování okna konzoly otevřené po spuštění aplikace.
    
    [!code-csharp[file main method](~/cognitive-services-rest-samples/dotnet/Vision/InkRecognition/quickstart/recognizeInk.cs?name=main)]


## <a name="run-the-application-and-view-the-response"></a>Spuštění aplikace a zobrazení odpovědi

Spusťte aplikaci. Úspěšná odpověď je vrácena ve formátu JSON. Odpověď JSON najdete také na [GitHubu](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/dotnet/Vision/InkRecognition/quickstart/example-response.json).


## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Odkaz na rozhraní REST API](https://go.microsoft.com/fwlink/?linkid=2089907)


Pokud se chcete podívat, jak rozhraní API pro rozpoznávání rukopisu funguje v digitální aplikaci rukopisu, podívejte se na následující ukázkové aplikace na GitHubu:
* [C# a Univerzální platforma Windows (UPW)](https://go.microsoft.com/fwlink/?linkid=2089803)  
* [C# a Windows Presentation Foundation (WPF)](https://go.microsoft.com/fwlink/?linkid=2089804)
* [Aplikace webového prohlížeče v Javascriptu](https://go.microsoft.com/fwlink/?linkid=2089908)       
* [Mobilní aplikace v Javě a Androidu](https://go.microsoft.com/fwlink/?linkid=2089906)
* [Mobilní aplikace ve Swiftu a iOS](https://go.microsoft.com/fwlink/?linkid=2089805)
