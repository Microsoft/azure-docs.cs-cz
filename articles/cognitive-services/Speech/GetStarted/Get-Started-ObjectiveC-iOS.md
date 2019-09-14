---
title: Začínáme s rozhraním API pro rozpoznávání Zpracování řeči Bingu v cíli – C v iOS | Microsoft Docs
titlesuffix: Azure Cognitive Services
description: Rozhraní API pro rozpoznávání Zpracování řeči Bingu slouží k vývoji aplikací pro iOS, které převádějí mluvený zvuk na text.
services: cognitive-services
author: nitinme
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-speech
ms.topic: article
ms.date: 09/18/2018
ms.author: nitinme
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: e42784e6d2751f7e76aec8caf1d6e1f9f09a9fd1
ms.sourcegitcommit: fbea2708aab06c19524583f7fbdf35e73274f657
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/13/2019
ms.locfileid: "70965940"
---
# <a name="quickstart-use-the-bing-speech-recognition-api-in-objective-c-on-ios"></a>Rychlý start: Použití rozhraní API pro rozpoznávání Zpracování řeči Bingu v cíli – C v iOS

[!INCLUDE [Deprecation note](../../../../includes/cognitive-services-bing-speech-api-deprecation-note.md)]

Pomocí rozhraní API pro rozpoznávání řeči můžete vyvíjet aplikace pro iOS, které využívají cloudovou službu pro hlasové rozpoznávání řeči, a převést mluvený zvuk na text. Rozhraní API podporuje streamování v reálném čase, takže vaše aplikace může současně a asynchronně přijímat částečné výsledky rozpoznávání ve stejnou dobu, kdy posílá zvuk do služby.

Tento článek používá ukázkovou aplikaci k předvedení základních informací o tom, jak začít s rozhraním API pro rozpoznávání řeči při vývoji aplikace pro iOS. Úplný referenční informace k rozhraní API najdete v [referenčních informacích k klientské knihovně pro sadu Speech SDK](https://cdn.rawgit.com/Microsoft/Cognitive-Speech-STT-iOS/master/com.Microsoft.SpeechSDK-1_0-for-iOS.docset/Contents/Resources/Documents/index.html).

## <a name="prerequisites"></a>Požadavky

### <a name="platform-requirements"></a>Požadavky platformy

Ujistěte se, že je nainstalované rozhraní IDE pro Mac XCode.

### <a name="get-the-client-library-and-examples"></a>Získat klientskou knihovnu a příklady

Klientská knihovna služby Speech a příklady pro iOS jsou k dispozici v [sadě Speech Client SDK pro iOS](https://github.com/microsoft/cognitive-speech-stt-ios).

### <a name="subscribe-to-the-speech-recognition-api-and-get-a-free-trial-subscription-key"></a>Přihlaste se k odběru rozhraní API pro rozpoznávání řeči a získejte bezplatný zkušební klíč předplatného.

Rozhraní API pro rozpoznávání řeči je součástí Cognitive Services (dříve projekt Oxford). Můžete získat bezplatný zkušební odběr klíčů na stránce [Cognitive Services předplatné](https://azure.microsoft.com/try/cognitive-services/) . Po výběru rozhraní API pro rozpoznávání řeči vyberte **získat klíč rozhraní API** a získejte klíč. Vrátí primární a sekundární klíč. Oba klíče jsou vázané na stejnou kvótu, takže můžete použít kteroukoli z těchto klíčů.

Pokud chcete použít *rozpoznávání s záměrem*, musíte si také zaregistrovat [Language Understanding Intelligent Service (Luis)](https://azure.microsoft.com/services/cognitive-services/language-understanding-intelligent-service/).

> [!IMPORTANT]
> * Získejte klíč předplatného. Předtím, než budete moci použít klientské knihovny řeči, je nutné mít [klíč předplatného](https://azure.microsoft.com/try/cognitive-services/).
>
> * Použijte svůj klíč předplatného. S poskytnutou ukázkovou aplikací pro iOS musíte aktualizovat soubor Samples/SpeechRecognitionServerExample/Settings. plist pomocí vašeho klíče předplatného. Další informace najdete v tématu [sestavení a spuštění ukázek](#build-and-run-samples).

## <a name="use-the-speech-client-library"></a>Použití klientská knihovna služby Speech

Chcete-li přidat knihovnu klienta do projektu XCode, postupujte podle těchto [pokynů](https://github.com/Azure-Samples/Cognitive-Speech-STT-iOS#the-client-library).

Informace o klientské knihovně pro iOS najdete na této [webové stránce](https://cdn.rawgit.com/Microsoft/Cognitive-Speech-STT-iOS/master/com.Microsoft.SpeechSDK-1_0-for-iOS.docset/Contents/Resources/Documents/index.html).

## <a name="build-and-run-samples"></a>Sestavení a spuštění ukázek

Informace o tom, jak sestavit a spustit ukázky, najdete na této [stránce Readme](https://github.com/Azure-Samples/Cognitive-Speech-STT-iOS#the-sample).

## <a name="samples-explained"></a>Vysvětlení ukázek

### <a name="create-recognition-clients"></a>Vytváření klientů pro rozpoznávání

Následující kód v ukázce ukazuje, jak vytvořit třídy klienta pro rozpoznávání na základě scénářů uživatele:

```
{
    NSString* language = @"en-us";

    NSString* path = [[NSBundle mainBundle] pathForResource:@"settings" ofType:@"plist"];
    NSDictionary* settings = [[NSDictionary alloc] initWithContentsOfFile:path];

    NSString* primaryOrSecondaryKey = [settings objectForKey:(@"primaryKey")];
    NSString* luisAppID = [settings objectForKey:(@"luisAppID")];
    NSString* luisSubscriptionID = [settings objectForKey:(@"luisSubscriptionID")];

    if (isMicrophoneReco) {
        if (!isIntent) {
            micClient = [SpeechRecognitionServiceFactory createMicrophoneClient:(recoMode)
                                                                   withLanguage:(language)
                                                                        withKey:(primaryOrSecondaryKey)
                                                                   withProtocol:(self)];
        }
        else {
            MicrophoneRecognitionClientWithIntent* micIntentClient;
            micIntentClient = [SpeechRecognitionServiceFactory createMicrophoneClientWithIntent:(language)
                                                                                        withKey:(primaryOrSecondaryKey)
                                                                                  withLUISAppID:(luisAppID)
                                                                                 withLUISSecret:(luisSubscriptionID)
                                                                                   withProtocol:(self)];
            micClient = micIntentClient;
        }
    }
    else {
        if (!isIntent) {
            dataClient = [SpeechRecognitionServiceFactory createDataClient:(recoMode)
                                                              withLanguage:(language)
                                                                   withKey:(primaryOrSecondaryKey)
                                                              withProtocol:(self)];
        }
        else {
            DataRecognitionClientWithIntent* dataIntentClient;
            dataIntentClient = [SpeechRecognitionServiceFactory createDataClientWithIntent:(language)
                                                                                   withKey:(primaryOrSecondaryKey)
                                                                             withLUISAppID:(luisAppID)
                                                                            withLUISSecret:(luisSubscriptionID)
                                                                              withProtocol:(self)];
            dataClient = dataIntentClient;
        }
    }
}

```

Klientská knihovna poskytuje pro typické scénáře rozpoznávání řeči předem implementované klientské třídy pro rozpoznávání:

* `DataRecognitionClient`: Rozpoznávání řeči s daty PCM (například ze zdroje souboru nebo zvuku). Data jsou rozdělena do vyrovnávacích pamětí a každá vyrovnávací paměť je odeslána službě Speech. Vyrovnávací paměti se neprovádí bez úprav, takže uživatelé můžou v případě potřeby použít vlastní detekci ticha. Pokud jsou data poskytována ze souborů WAV, můžete odeslat data ze souboru přímo na server. Pokud máte nezpracovaná data, například zvuk přidaný přes Bluetooth, nejprve na server odešlete hlavičku formátu a potom data.
* `MicrophoneRecognitionClient`: Rozpoznávání řeči pomocí zvukového přenosu, který přichází z mikrofonu. Ujistěte se, že je mikrofon zapnutý a že se data z mikrofonu odesílají službě rozpoznávání řeči. Pro data mikrofonu se před odesláním do služby pro rozpoznávání použije vestavěný "detektor tichého běhu".
* `DataRecognitionClientWithIntent`a `MicrophoneRecognitionClientWithIntent`: Kromě textu rozpoznávání vrátí tito klienti strukturované informace o záměru mluvčího, který vaše aplikace může použít k řízení dalších akcí. Chcete-li použít "záměr", musíte nejprve vytvořit model pomocí [Luis](https://azure.microsoft.com/services/cognitive-services/language-understanding-intelligent-service/).

### <a name="recognition-language"></a>Jazyk rozpoznávání

Když použijete `SpeechRecognitionServiceFactory` k vytvoření klienta, musíte vybrat jazyk. Úplný seznam jazyků podporovaných službou Speech Service najdete v části [podporované jazyky](../API-Reference-REST/supportedlanguages.md).

### <a name="speechrecognitionmode"></a>SpeechRecognitionMode

Musíte také určit `SpeechRecognitionMode` , kdy se má `SpeechRecognitionServiceFactory`klient vytvořit:

* `SpeechRecognitionMode_ShortPhrase`: Utterance až 15 sekund dlouho. Jakmile se data odešlou do služby, klient obdrží několik částečných výsledků a jeden konečný výsledek s více než nejlepší možností.
* `SpeechRecognitionMode_LongDictation`: Utterance může trvat až dvě minuty. Po odeslání dat službě klient obdrží více částečných výsledků a více konečných výsledků na základě toho, kde Server identifikuje větu pozastavena.

### <a name="attach-event-handlers"></a>Připojit obslužné rutiny událostí

K vytvořenému klientovi můžete připojit různé obslužné rutiny událostí:

* **Události částečných výsledků**: Tato událost se volá pokaždé, když služba rozpoznávání řeči předpovídá, co můžete vyjádřit, dokonce i před tím, než dokončíte práci `MicrophoneRecognitionClient`nebo dokončete odesílání dat (Pokud `DataRecognitionClient`používáte).
* **Chybové události**: Volá se, když služba detekuje chybu.
* **Události záměru**: Volá se na klientech WithIntent (jenom v režimu ShortPhrase), až se výsledek výsledku rozpoznávání analyzuje do strukturovaného záměru JSON.
* **Události výsledku**:
  * V `SpeechRecognitionMode_ShortPhrase` režimu se tato událost volá a po dokončení mluvení vrátí n nejlepších výsledků.
  * V `SpeechRecognitionMode_LongDictation` režimu je obslužná rutina události volána vícekrát, na základě toho, kde služba identifikuje podobu pozastavení.
  * **Pro každou z těchto n-nejlepších možností**se vrátí hodnota spolehlivosti a několik různých forem rozpoznaného textu. Další informace naleznete v tématu [Output Format](../Concepts.md#output-format).

## <a name="related-topics"></a>Související témata

* [Reference klientské knihovny pro iOS](https://cdn.rawgit.com/Microsoft/Cognitive-Speech-STT-iOS/master/com.Microsoft.SpeechSDK-1_0-for-iOS.docset/Contents/Resources/Documents/index.html)
* [Začínáme s rozpoznáváním řeči od Microsoftu nebo záměrem v jazyce Java v Androidu](GetStartedJavaAndroid.md)
* [Začínáme s rozhraním API pro rozpoznávání řeči Microsoftu v JavaScriptu](GetStartedJSWebsockets.md)
* [Začínáme s rozhraním Speech API od Microsoftu přes REST](GetStartedREST.md)
