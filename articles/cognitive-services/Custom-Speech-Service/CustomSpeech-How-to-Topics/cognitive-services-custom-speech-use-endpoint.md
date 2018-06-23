---
title: Koncový bod vlastní řeči pomocí vlastní řeči služby v Azure | Microsoft Docs
description: Naučte se používat vlastní koncový bod řeči na text s vlastní službou řeči v kognitivní služby.
services: cognitive-services
author: PanosPeriorellis
manager: onano
ms.service: cognitive-services
ms.component: custom-speech
ms.topic: article
ms.date: 02/08/2017
ms.author: panosper
ms.openlocfilehash: d28065d7962ee660cafd4b3321abdd6a8f94abcb
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2018
ms.locfileid: "35342508"
---
# <a name="use-a-custom-speech-to-text-endpoint"></a>Použít vlastní koncový bod řeči na text
Požadavky na koncový bod Azure vlastní řeči Service řeči na text, můžete odeslat podobným způsobem jako do služby kognitivní řeči výchozí koncový bod. Tyto koncové body fungují stejně jako jsou výchozí koncové body rozhraní API pro rozpoznávání řeči. Stejné funkce, která je k dispozici prostřednictvím klientské knihovny nebo REST API pro rozpoznávání řeči rozhraní API je proto také k dispozici pro svůj vlastní koncový bod.

Koncové body, které vytvoříte pomocí této služby může zpracovat různý počet souběžných požadavků. Svazek závisí na cenovou úroveň spojené s vaším předplatným. Pokud jsou moc žádostí, dojde k chybě. Úroveň free má měsíčního limitu požadavků.

Služba se předpokládá, že jsou data přenášena v reálném čase. Pokud je odeslat rychleji, žádost považuje za spuštění, dokud jeho zvuk trvání v reálném čase byla úspěšná.

> [!NOTE]
> Podporujeme [nové webové sokety](https://docs.microsoft.com/azure/cognitive-services/speech/api-reference-rest/websocketprotocol) ještě. Pokud budete chtít použít objekty websocket s vlastní řeči koncový bod, postupujte podle pokynů v tomto poli.
>
> Nové [REST API](https://docs.microsoft.com/azure/cognitive-services/speech/getstarted/getstartedrest) podpora bude brzo. Pokud budete chtít volání váš koncový bod vlastní řeči prostřednictvím protokolu HTTP, postupujte podle pokynů v tomto poli.
>

## <a name="send-requests-by-using-the-speech-client-library"></a>Odesílání žádostí pomocí klientské knihovny řeči

K odesílání požadavků na svůj vlastní koncový bod pomocí klientské knihovny rozpoznávání řeči, spusťte rozpoznávání klienta. Použít řeči klienta SDK z [NuGet](http://nuget.org/). Vyhledejte *rozpoznávání řeči*a vyberte balíček rozpoznávání řeči od společnosti Microsoft pro vaši platformu. Ukázkový kód naleznete na [Githubu](https://github.com/Microsoft/Cognitive-Speech-STT-Windows). Sada SDK řeči klienta poskytuje třídu objektů factory **SpeechRecognitionServiceFactory**, který nabízí následující metody:

  *   ```CreateDataClient(...)```: Data rozpoznávání klienta.
  *   ```CreateDataClientWithIntent(...)```Rozpoznávání: data klienta se záměrem.
  *   ```CreateMicrophoneClient(...)```: Mikrofon rozpoznávání klienta.
  *   ```CreateMicrophoneClientWithIntent(...)```Klient: mikrofon rozpoznávání se záměrem.

Podrobnou dokumentaci najdete v tématu [rozhraní API pro rozpoznávání řeči Bing](https://docs.microsoft.com/azure/cognitive-services/speech/home). Vlastní rozpoznávání řeči služby na koncové body podporují stejné sady SDK.

Klient rozpoznávání data je vhodné pro rozpoznávání řeči z dat, například na soubor nebo jiný zvuk zdroj. Klient pro rozpoznávání mikrofon je vhodné pro rozpoznávání řeči z mikrofonu. Použití záměr buď klient může vrátit strukturovaných záměrné výsledky z [Služba inteligentního znalosti jazyka](https://www.luis.ai/) (LEOŠ), pokud jste vytvořili aplikaci LEOŠ pro váš scénář.

Všechny čtyři typy klientů se dá vytvořit instance dvěma způsoby. První způsob využívá standardní API pro rozpoznávání řeči kognitivní služby. Druhý způsob umožňuje zadat adresu URL, která odpovídá svůj vlastní koncový bod vytvořen s vlastní službou řeči.

Například můžete vytvořit **DataRecognitionClient** , zasílá požadavky na koncový bod vlastní pomocí následující metody:

```csharp
public static DataRecognitionClient CreateDataClient(SpeeechRecognitionMode speechRecognitionMode, string language, string primaryOrSecondaryKey, **string url**);
```

**Your_subscriptionId** a **endpointURL** odkazovat klíč předplatného a adresa URL webové sokety v uvedeném pořadí, v **informace o nasazení** stránky.

**AuthenticationUri** se používá k přijetí token pomocí ověřovací služby. Tento identifikátor URI musí být nastavena samostatně, jak je znázorněno v následujícím ukázkovém kódu.

Tento ukázkový kód ukazuje, jak použít klienta SDK:

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
> Při použití **vytvořit** metody v sadě SDK, je nutné zadat ID předplatného dvakrát z důvodu přetížení z **vytvořit** metody.
>

Vlastní rozpoznávání řeči služba používá dva různé adresy URL pro rozpoznávání zkrácené a dlouhá. Jak jsou uvedeny na **nasazení** stránky. Použijte adresu URL koncového bodu správné pro konkrétní formulář, který chcete použít.

Další informace o vyvolání různých klientů rozpoznávání s svůj vlastní koncový bod, najdete v článku [SpeechRecognitionServiceFactory](https://www.microsoft.com/cognitive-services/Speech-api/documentation/GetStarted/GetStartedCSharpDesktop) třídy. Dokumentace na této stránce odkazuje na přizpůsobení akustickými modelu, ale platí pro všechny koncové body vytvořené pomocí služby řeči vlastní.

## <a name="send-requests-by-using-the-speech-protocol"></a>Odeslání žádosti s využitím protokolu řeči

Koncové body pro zobrazen [řeči protokol](https://docs.microsoft.com/azure/cognitive-services/speech/api-reference-rest/websocketprotocol) jsou koncové body pro otevřete soketu řeči protokol webové zdroje.

V současné době je implementace pouze oficiální klienta pro [JavaScript](https://github.com/Azure-Samples/SpeechToText-WebSockets-Javascript). Pokud chcete spustit s ukázka poskytuje existuje, proveďte následující změny kódu a znovu sestavit ukázku:

1. V _src\sdk\speech.browser\SpeechConnectionFactory.ts_, nahraďte název hostitele "wss://speech.platform.bing.com" s název hostitele, který je zobrazený na stránce Podrobnosti vašeho nasazení. Nevkládejte o celý identifikátor URI zde ale právě *wss* protokolu schématu a název hostitele. Příklad:

    ```JavaScript
    private get Host(): string {
        return Storage.Local.GetOrAdd("Host", "wss://<your_key_goes_here>.api.cris.ai");
    }
    ```

2. Nastavte _recognitionMode_ parametr v _samples\browser\Samples.html_ podle vašich požadavků:
    * _RecognitionMode.Interactive_ podporuje požadavky až 15 sekund.
    * _RecognitionMode.Conversation_ a _RecognitionMode.Dictation_ (obě jsou ekvivalentní ve službě vlastní řeči) podporu požadavků až 10 minut.

3. Sestavte ukázku pomocí "gulp sestavení" jej před použitím.

> [!NOTE]
> Ujistěte se, že používáte správný identifikátor URI pro tento protokol. Požadované schéma je *wss* (ne *http* jako protokol klienta). 

Další informace najdete v tématu [rozhraní API pro rozpoznávání řeči Bing](https://docs.microsoft.com/azure/cognitive-services/speech/getstarted/getstartedclientlibraries) dokumentaci.

## <a name="send-requests-by-using-http"></a>Odesílat požadavky pomocí protokolu HTTP

Odesílání požadavku do svůj vlastní koncový bod pomocí HTTP post je podobný jako poslání kognitivní rozhraní API služby Bing řeči na požadavek HTTP. Upravte adresu URL, aby odrážela adresu vlastní nasazení.

Existují některá omezení na požadavky zaslané prostřednictvím protokolu HTTP pro koncový bod kognitivní řeči služby i vlastní koncové body vytvořené pomocí této služby. Požadavek HTTP nelze vrátit částečné výsledky během procesu rozpoznávání. Kromě toho je omezený na 10 sekund pro obsah, zvuk a 14 sekundy celkové trvání žádosti.

Chcete-li vytvořit požadavek post, postupujte podle stejný proces, který používáte pro API pro rozpoznávání řeči kognitivní služby.

1. Získejte token přístupu pomocí ID vašeho předplatného. Tento token je nutné pro přístup k rozpoznávání koncový bod. Může být znovu použity, po dobu 10 minut.

    ```
    curl -X POST --header "Ocp-Apim-Subscription-Key:<subscriptionId>" --data "" "https://westus.api.cognitive.microsoft.com/sts/v1.0/issueToken"
    ```
      **SubscriptionId** musí být nastavené na ID odběru pro toto nasazení použijete. Odpověď je prostý token, které potřebujete pro další požadavek.

2. POST zvuk ke koncovému bodu pomocí POST znovu.

    ```
    curl -X POST --data-binary @example.wav -H "Authorization: Bearer <token>" -H "Content-Type: application/octet-stream" "<https_endpoint>"
    ```

    **Tokenu** se přístupový token, který jste obdrželi s předchozího volání. **Https_endpoint** je úplná adresa vlastní endpoint řeči na text zobrazený na **informace o nasazení** stránky.

Další informace o parametry post protokolu HTTP a formát odpovědi, najdete v článku [Microsoft kognitivní služby Bing řeči HTTP API](https://www.microsoft.com/cognitive-services/speech-api/documentation/API-Reference-REST/BingVoiceRecognition#SampleImplementation).

## <a name="send-requests-by-using-the-service-library"></a>Posílat požadavky prostřednictvím knihovny služby
Knihovna Service umožňuje vaší služby, chcete-li použít přepis cloudu Microsoft Speech mluvené jazyk převést na text v reálném čase, tak, aby vaší klientské aplikace může odesílat zvuk a přijímat výsledky částečné rozpoznávání zálohovat současně a asynchronně. Můžete najít podrobnosti sady SDK služby [sem](https://docs.microsoft.com/azure/cognitive-services/speech/getstarted/getstartedcsharpservicelibrary)

> [!NOTE]
> Při použití knihovny služby budete muset změnit identifikátor URI zprostředkovatele autorizace implementace **IAuthorizationProvider** k https://westus.api.cognitive.microsoft.com/sts/v1.0/issueToken.

## <a name="next-steps"></a>Další postup
* Zvyšte přesnost s vaší [vlastní akustickými modelu](cognitive-services-custom-speech-create-acoustic-model.md).
* Zvyšte přesnost s [vlastní jazyk modelu](cognitive-services-custom-speech-create-language-model.md).
