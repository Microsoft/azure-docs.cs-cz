---
title: Začínáme s rozhraním API rozpoznávání řeči Microsoft v Objective-C v systému iOS | Microsoft Docs
description: Použití rozhraní API pro rozpoznávání řeči Microsoft k vývoji aplikací pro iOS, které mluvené zvuk převedeno na text.
services: cognitive-services
author: zhouwangzw
manager: wolfma
ms.service: cognitive-services
ms.component: bing-speech
ms.topic: article
ms.date: 09/29/2017
ms.author: zhouwang
ms.openlocfilehash: bbb8d3975cdab537135b97ca9bbf6e845aa3fa0e
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2018
ms.locfileid: "35342650"
---
# <a name="get-started-with-the-speech-recognition-api-in-objective-c-on-ios"></a>Začínáme s rozhraním API pro rozpoznávání řeči v Objective-C v systému iOS

S rozhraním API rozpoznávání řeči můžete vyvíjet aplikace iOS, které používají Cloudová služba rozpoznávání řeči mluvené zvuk převést na text. Rozhraní API podporuje streamování v reálném čase, tak, aby vaše aplikace může současně a asynchronní příjem výsledků částečné rozpoznávání ve stejnou dobu, kdy je odesílání zvuk ke službě.

Tento článek používá ukázkovou aplikaci k předvedení základní informace o tom, jak začít pracovat s rozhraním API pro rozpoznávání řeči vyvíjet aplikace pro iOS. Úplný referenční dokumentace rozhraní API, najdete v článku [referenční dokumentace knihoven klienta SDK řeči](https://cdn.rawgit.com/Microsoft/Cognitive-Speech-STT-iOS/master/com.Microsoft.SpeechSDK-1_0-for-iOS.docset/Contents/Resources/Documents/index.html).

## <a name="prerequisites"></a>Požadavky

### <a name="platform-requirements"></a>Požadavky na platformu

Ověřte, zda je nainstalován Mac XCode IDE.

### <a name="get-the-client-library-and-examples"></a>Nainstalujte klienta knihovny a příklady

Jsou k dispozici na rozpoznávání řeči klientské knihovny a příklady pro iOS [řeči klienta SDK pro iOS](https://github.com/microsoft/cognitive-speech-stt-ios).

### <a name="subscribe-to-the-speech-recognition-api-and-get-a-free-trial-subscription-key"></a>Přihlášení k odběru do rozhraní API pro rozpoznávání řeči a získat klíč bezplatné předplatné zkušební verze

Rozhraní API pro rozpoznávání řeči je součástí služby kognitivní (dříve Oxford projektu). Můžete získat zkušební předplatné klíče z [kognitivní služby předplatného](https://azure.microsoft.com/try/cognitive-services/) stránky. Po výběru rozhraní API pro rozpoznávání řeči, vyberte **získat klíč rozhraní API** k získání klíče. Vrátí primární a sekundární klíč. Oba klíče jsou svázané s stejné kvótu, takže můžete použít buď klíč.

Pokud chcete použít *rozpoznávání se záměrem*, musíte také zaregistrovat [jazyk Principy inteligentního služby (LEOŠ)](https://azure.microsoft.com/services/cognitive-services/language-understanding-intelligent-service/).

> [!IMPORTANT]
> * Získáte klíč předplatného. Než budete moci použít řeči klientské knihovny, musíte mít [klíč předplatného](https://azure.microsoft.com/try/cognitive-services/).
>
> * Použijte svůj klíč předplatného. Zadaná iOS ukázkovou aplikaci je nutné aktualizovat soubor Samples/SpeechRecognitionServerExample/settings.plist s svůj klíč předplatného. Další informace najdete v tématu [sestavení a spuštění ukázky](#build-and-run-samples).

## <a name="use-the-speech-client-library"></a>Použijte klientskou knihovnu řeči

Pokud chcete přidat do projektu XCode klientské knihovny, postupujte podle těchto [pokyny](https://github.com/Azure-Samples/Cognitive-Speech-STT-iOS#the-client-library).

Klient referenční příručka knihovny pro iOS, najdete v tématu to [webová stránka](https://cdn.rawgit.com/Microsoft/Cognitive-Speech-STT-iOS/master/com.Microsoft.SpeechSDK-1_0-for-iOS.docset/Contents/Resources/Documents/index.html).

## <a name="build-and-run-samples"></a>Sestavení a spuštění ukázky

Informace o tom, jak sestavení a spuštění ukázky najdete v tématu to [stránce README](https://github.com/Azure-Samples/Cognitive-Speech-STT-iOS#the-sample).

## <a name="samples-explained"></a>Ukázky vysvětlené

### <a name="create-recognition-clients"></a>Vytvořit rozpoznávání klienti

Následující kód v ukázce ukazuje postup vytvoření třídy klienta rozpoznávání podle scénáře uživatele:

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

Klientská knihovna poskytuje předem implementovaná rozpoznávání třídy klienta pro typické scénáře v rozpoznávání řeči:

* `DataRecognitionClient`: Rozpoznávání řeči daty PCM (například ze zdrojového souboru nebo zvuk). Data je rozdělena na vyrovnávací paměti a každý vyrovnávací paměti je odeslána do řeči služby. Bez úprav provádí do vyrovnávacích pamětí, takže uživatelé můžou použít vlastní interval detekce v případě potřeby. Není-li data ze souborů WAV, může odesílat data ze souboru práva k serveru. Pokud máte nezpracovaná data, například zvuk procházející přes Bluetooth, je nejprve posílat hlavičku formátu serveru následované data.
* `MicrophoneRecognitionClient`: Rozpoznávání řeči s zvuk pocházejících z mikrofonu. Zajistěte, aby mikrofon je zapnutý a že se ke službě rozpoznávání řeči neodesílají data z mikrofonu. Předdefinované "ticho detektor" se použije k datům mikrofon před odesláním do služby rozpoznávání.
* `DataRecognitionClientWithIntent` a `MicrophoneRecognitionClientWithIntent`: kromě rozpoznávání text, tito klienti vrácení strukturovaných informací o záměr lektora, které aplikace můžete použít k řízení další akce. Pokud chcete používat "Záměr", je potřeba nejdřív trénování modelu s použitím [LEOŠ](https://azure.microsoft.com/services/cognitive-services/language-understanding-intelligent-service/).

### <a name="recognition-language"></a>Jazyk rozpoznávání

Při použití `SpeechRecognitionServiceFactory` pro vytvoření klienta, je nutné vybrat jazyk. Úplný seznam jazyků podporovaných službou rozpoznávání řeči, najdete v části [podporované jazyky](../API-Reference-REST/supportedlanguages.md).

### <a name="speechrecognitionmode"></a>SpeechRecognitionMode

Budete taky muset zadat `SpeechRecognitionMode` při vytváření klienta se `SpeechRecognitionServiceFactory`:

* `SpeechRecognitionMode_ShortPhrase`: Utterance až 15 sekund dlouho. Podle data odeslání službu, klient obdrží několik částečné výsledky a jeden konečný výsledek s více možností n nejvhodnější.
* `SpeechRecognitionMode_LongDictation`: Utterance až dvou minut dlouho. Jak se data odesílá do služby, klient obdrží více částečné výsledky a více konečné výsledky podle kde serveru identifikuje pozastaví věty.

### <a name="attach-event-handlers"></a>Připojte obslužné rutiny událostí

Různé obslužné rutiny událostí můžete připojit ke klientovi jste vytvořili:

* **Částečné výsledky události**: Tato událost volala pokaždé, když služby řeči předpovídá, co vám může být informacemi o tom, ještě před dokončení hovořícího (Pokud používáte `MicrophoneRecognitionClient`) nebo dokončení odesílání dat (Pokud používáte `DataRecognitionClient`).
* **Chybové události**: volána, když se služba zjistí chybu.
* **Záměrné události**: volat na klientech "WithIntent" (pouze v režimu ShortPhrase) po posledním rozpoznávání výsledek analýzy do strukturovaných záměr JSON.
* **Způsobit události**:
  * V `SpeechRecognitionMode_ShortPhrase` režimu, tato událost je volána a vrátí n nejlepší výsledky, po dokončení hovořícího.
  * V `SpeechRecognitionMode_LongDictation` režimu, obslužné rutiny události je volat vícekrát, podle kde službu identifikuje pozastaví věty.
  * **Pro každou z těchto možností n nejvhodnější**, je vrácena hodnota spolehlivosti a několik různé formy rozpoznaný text. Další informace najdete v tématu [výstupní formát](../Concepts.md#output-format).

## <a name="related-topics"></a>Související témata

* [Referenční dokumentace knihoven klienta pro iOS](https://cdn.rawgit.com/Microsoft/Cognitive-Speech-STT-iOS/master/com.Microsoft.SpeechSDK-1_0-for-iOS.docset/Contents/Resources/Documents/index.html)
* [Začínáme s rozpoznávání řeči společnosti Microsoft nebo záměr v jazyce Java v systému Android](GetStartedJavaAndroid.md)
* [Začínáme s rozhraním API pro rozpoznávání řeči Microsoft v jazyce JavaScript](GetStartedJSWebsockets.md)
* [Začínáme s rozhraním API pro rozpoznávání řeči Microsoft přes REST](GetStartedREST.md)
