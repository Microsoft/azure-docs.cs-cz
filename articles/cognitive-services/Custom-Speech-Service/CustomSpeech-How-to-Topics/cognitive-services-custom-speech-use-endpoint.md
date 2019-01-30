---
title: Koncový bod vlastní převod řeči pomocí Custom Speech Service v Azure | Dokumentace Microsoftu
description: Další informace o použití vlastního koncového bodu převodu řeči na text pomocí Custom Speech Service ve službě Cognitive Services.
services: cognitive-services
author: PanosPeriorellis
manager: onano
ms.service: cognitive-services
ms.subservice: custom-speech
ms.topic: article
ms.date: 02/08/2017
ms.author: panosper
ms.openlocfilehash: 3f99172f486c374cdbd0b6ebd50fdf7bfec98ec3
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2019
ms.locfileid: "55227144"
---
# <a name="use-a-custom-speech-to-text-endpoint"></a>Použití vlastního koncového bodu pro převod řeči na text

[!INCLUDE [Deprecation note](../../../../includes/cognitive-services-custom-speech-deprecation-note.md)]

Můžete odeslat požadavků do koncového bodu Azure Custom Speech Service speech to text, podobným způsobem jako do výchozí koncový bod služby Cognitive Services řeči. Tyto koncové body jsou funkčně stejný jako výchozí koncové body rozhraní API pro rozpoznávání řeči. Stejné funkce, která je dostupná přes klientskou knihovnu nebo rozhraní REST API pro rozhraní Speech API je tedy také k dispozici pro váš vlastní koncový bod.

Koncové body, které vytvoříte pomocí této služby může zpracovat různý počet souběžných požadavků. Svazek, závisí na cenové úrovni spojených s vaším předplatným. Pokud jsou příliš mnoho požadavků, dojde k chybě. Úroveň free má měsíčního limitu požadavků.

Služba se předpokládá, že jsou data přenášena v reálném čase. Pokud přijde rychlejší, požadavek považován za spuštění, dokud se úspěšně prošel jeho trvání zvuku v reálném čase.

> [!NOTE]
> Podporujeme [nové webové sokety](https://docs.microsoft.com/azure/cognitive-services/speech/api-reference-rest/websocketprotocol) ještě. Pokud budete chtít použít objekty websocket pomocí vašeho koncového bodu vlastní převod řeči, postupujte podle zde uvedených pokynů.
>
> Nové [rozhraní REST API](https://docs.microsoft.com/azure/cognitive-services/speech/getstarted/getstartedrest) podporu Připravujeme. Pokud budete chtít volat koncový bod vlastní převod řeči přes protokol HTTP, postupujte podle zde uvedených pokynů.
>

## <a name="send-requests-by-using-the-speech-client-library"></a>Odeslání žádosti s použitím knihovny řečového klienta

K odesílání požadavků na vlastní koncový bod s použitím knihovny řečového klienta, spusťte klienta rozpoznávání. Umožňuje využít řeč klientské sady SDK z [NuGet](http://nuget.org/). Vyhledejte *rozpoznávání řeči*a vyberte balíček, rozpoznávání řeči od Microsoftu pro vaši platformu. Vzorový kód můžete najít na [Githubu](https://github.com/Microsoft/Cognitive-Speech-STT-Windows). Klientské sady SDK řeč poskytuje třídu objektů factory **SpeechRecognitionServiceFactory**, což poskytuje následující metody:

  *   ```CreateDataClient(...)```: Klient rozpoznávání data.
  *   ```CreateDataClientWithIntent(...)```: Klient rozpoznávání dat s cílem.
  *   ```CreateMicrophoneClient(...)```: Klient rozpoznávání mikrofon.
  *   ```CreateMicrophoneClientWithIntent(...)```: Klient rozpoznávání mikrofon s cílem.

Podrobnou dokumentaci najdete v článku [rozhraní API pro zpracování řeči Bingu](https://docs.microsoft.com/azure/cognitive-services/speech/home). Koncové body služby Custom Speech Service podporují stejnou sadu SDK.

Rozpoznávání klientských dat je vhodné pro rozpoznávání řeči z dat, jako je například souboru nebo jiného zdroje zvuku. Klient rozpoznávání mikrofonu je vhodné pro rozpoznávání řeči z mikrofon. Použití záměr v obou klientovi může vrátit strukturované záměru výsledky z [Language Understanding Intelligent Service](https://www.luis.ai/) (LUIS), pokud jste vytvořili aplikaci LUIS pro váš scénář.

Všechny čtyři typy klientů můžete vytvořit instanci dvěma způsoby. První způsob využívá standardní Speech API služeb Cognitive Services. Druhým způsobem umožňuje zadat adresu URL, která odpovídá na vlastní koncový bod vytvořili s Custom Speech Service.

Například můžete vytvořit **DataRecognitionClient** , který odesílá požadavky na vlastní koncový bod pomocí následující metody:

```csharp
public static DataRecognitionClient CreateDataClient(SpeeechRecognitionMode speechRecognitionMode, string language, string primaryOrSecondaryKey, **string url**);
```

**Your_subscriptionId** a **endpointURL** odkazovat na adresu URL webové sokety a klíč předplatného na kartách, **informace o nasazení** stránky.

**AuthenticationUri** slouží k získání tokenu z ověřovací služby. Tento identifikátor URI musí být nastavena samostatně, jak je znázorněno v následujícím ukázkovém kódu.

Tento ukázkový kód ukazuje, jak používat klientskou sadou SDK:

```csharp
var dataClient = SpeechRecognitionServiceFactory.CreateDataClient(
  SpeechRecognitionMode.LongDictation,
  "en-us",
  "your_subscriptionId",
  "your_subscriptionId",
  "endpointURL");
// set the authorization Uri
dataClient.AuthenticationUri = "https://westus.api.cognitive.microsoft.com/sts/v1.0/issueToken";
```

> [!NOTE]
> Při použití **vytvořit** metody v sadě SDK, je nutné zadat ID předplatného dvakrát z důvodu přetížení **vytvořit** metody.
>

Custom Speech Service používá dva různé adresy URL pro rozpoznávání krátkometrážní a dlouhá. Obě jsou uvedeny na **nasazení** stránky. Použijte správný koncový bod adresy URL pro konkrétní formulář, který chcete použít.

Další informace o volání různých klientů rozpoznávání s vlastní koncový bod, najdete v článku [SpeechRecognitionServiceFactory](https://www.microsoft.com/cognitive-services/Speech-api/documentation/GetStarted/GetStartedCSharpDesktop) třídy. V dokumentaci na této stránce odkazuje na přizpůsobení akustického modelu, ale platí pro všechny koncové body vytvořené pomocí služby Custom Speech Service.

## <a name="send-requests-by-using-the-speech-protocol"></a>Odeslání žádosti s použitím protokolu řeči

Koncové body pro [řeči protokol](https://docs.microsoft.com/azure/cognitive-services/speech/api-reference-rest/websocketprotocol) jsou koncové body protokolu otevřete zdrojový Web soketu řeči.

V současné době je implementace klienta pouze oficiální [JavaScript](https://github.com/Azure-Samples/SpeechToText-WebSockets-Javascript). Pokud chcete začít, poskytnutou ukázkou, proveďte následující změny kódu a znovu sestavte ukázku:

1. V _src\sdk\speech.browser\SpeechConnectionFactory.ts_, nahraďte název hostitele "wss://speech.platform.bing.com" s názvem hostitele, který je zobrazený na stránce Podrobnosti vašeho nasazení. Nevkládejte úplný identifikátor URI tady ale jenom *wss* protokol schématu a název hostitele. Příklad:

    ```JavaScript
    private get Host(): string {
        return Storage.Local.GetOrAdd("Host", "wss://<your_key_goes_here>.api.cris.ai");
    }
    ```

2. Nastavte _recognitionMode_ parametr _samples\browser\Samples.html_ podle vašich požadavků:
    * _RecognitionMode.Interactive_ podporuje požadavky až 15 sekund.
    * _RecognitionMode.Conversation_ a _RecognitionMode.Dictation_ (obojí jsou ekvivalentní ve službě Custom Speech Service) až 10 minut, než se žádostí o podporu.

3. Sestavte ukázku s použitím "gulp sestavení" před jejich použitím.

> [!NOTE]
> Ujistěte se, že používáte správný identifikátor URI pro tento protokol. Požadované schéma je *wss* (ne *http* jako klienta protokolu). 

Další informace najdete v tématu [rozhraní API pro zpracování řeči Bingu](https://docs.microsoft.com/azure/cognitive-services/speech/getstarted/getstartedclientlibraries) dokumentaci.

## <a name="send-requests-by-using-http"></a>Odesílání požadavků pomocí protokolu HTTP

Odesílání požadavku na vlastní koncový bod pomocí metody post protokolu HTTP je podobný jako poslání požadavek HTTP Bing Speech API služeb Cognitive Services. Změnit adresu URL tak, aby odrážela adresu vlastního nasazení.

Existují některá omezení požadavků odeslaných přes protokol HTTP pro koncový bod Cognitive Services řeči a vlastní koncové body vytvořené pomocí této služby. Požadavek HTTP nelze vracet částečné výsledky během procesu rozlišování. Kromě toho je omezené na 10 sekund, zvukový obsah a 14 sekund celkové doby trvání požadavků.

Pokud chcete vytvořit požadavek post, opakujte stejný postup, který používáte pro rozhraní Speech API služeb Cognitive Services.

1. Získat přístupový token s použitím ID vašeho předplatného. Tento token vyžaduje přístup k rozpoznávání koncový bod. Můžete použít opakovaně po dobu 10 minut.

    ```
    curl -X POST --header "Ocp-Apim-Subscription-Key:<subscriptionId>" --data "" "https://westus.api.cognitive.microsoft.com/sts/v1.0/issueToken"
    ```
      **SubscriptionId** by mělo být nastavené ID odběru pro toto nasazení použijete. Odpověď je prostý token, které potřebujete pro další požadavek.

2. Publikovat zvuk ke koncovému bodu pomocí příspěvku znovu.

    ```
    curl -X POST --data-binary @example.wav -H "Authorization: Bearer <token>" -H "Content-Type: application/octet-stream" "<https_endpoint>"
    ```

    **Token** je přístupový token, který jste obdrželi s portálem předchozího volání. **Https_endpoint** je úplná adresa vlastní převod řeči na text koncový bod, je vidět na **informace o nasazení** stránky.

Další informace o parametry post protokolu HTTP a formát odpovědi, najdete v článku [Microsoft Bing Speech HTTP API služeb Cognitive Services](https://www.microsoft.com/cognitive-services/speech-api/documentation/API-Reference-REST/BingVoiceRecognition#SampleImplementation).

## <a name="send-requests-by-using-the-service-library"></a>Odeslání žádosti s použitím knihovny služby
Knihovna služby umožňuje službě provádět pomocí cloudu Microsoftu Speech určené k transkripci převádějte mluvený jazyk na text v reálném čase, tak, aby klientská aplikace může posílat zvuk a přijímat asynchronně a současně po obnovení částečné výsledky rozpoznávání. Najdete podrobné informace o sadě SDK služby [zde](https://docs.microsoft.com/azure/cognitive-services/speech/getstarted/getstartedcsharpservicelibrary)

> [!NOTE]
> Při použití knihovny služby je nutné změnit identifikátor URI zprostředkovatele autorizace v implementaci **IAuthorizationProvider** k https://westus.api.cognitive.microsoft.com/sts/v1.0/issueToken.

## <a name="next-steps"></a>Další postup
* Zvyšte přesnost s vaší [vlastního akustického modelu](cognitive-services-custom-speech-create-acoustic-model.md).
* Zlepšit přesnost [vlastního jazykového modelu](cognitive-services-custom-speech-create-language-model.md).
