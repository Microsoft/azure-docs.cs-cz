---
title: Začínáme s API pro rozpoznávání řeči Bingu v Objective-C v iOS | Dokumentace Microsoftu
titlesuffix: Azure Cognitive Services
description: Vývoj aplikací pro iOS, které převést mluvené slovo na text pomocí rozhraní API pro rozpoznávání řeči Bingu.
services: cognitive-services
author: zhouwangzw
manager: wolfma
ms.service: cognitive-services
ms.subservice: bing-speech
ms.topic: article
ms.date: 09/18/2018
ms.author: zhouwang
ms.openlocfilehash: f8bc13aa2adad5c27b1754303ea30304c491f7ca
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2019
ms.locfileid: "55211793"
---
# <a name="quickstart-use-the-bing-speech-recognition-api-in-objective-c-on-ios"></a>Rychlý start: Použití rozhraní API pro rozpoznávání řeči Bingu v systému iOS v Objective-C

[!INCLUDE [Deprecation note](../../../../includes/cognitive-services-bing-speech-api-deprecation-note.md)]

Rozhraní API pro rozpoznávání řeči vám umožní vytvářet aplikace pro iOS, které Cloudová služba Speech umožňuje převést mluvené slovo na text. Rozhraní API podporuje streamování v reálném čase, takže vaše aplikace může současně a asynchronně přijímat částečné výsledky rozpoznávání ve stejnou dobu, kterou je posílání zvuku ve službě.

Tento článek používá ukázkovou aplikaci k předvedení základní informace o tom, jak začít pracovat s rozhraním API pro rozpoznávání řeči pro vývoj aplikací pro iOS. Kompletní reference k rozhraní API najdete v článku [sadou SDK pro řeč Klientská knihovna – referenční informace](https://cdn.rawgit.com/Microsoft/Cognitive-Speech-STT-iOS/master/com.Microsoft.SpeechSDK-1_0-for-iOS.docset/Contents/Resources/Documents/index.html).

## <a name="prerequisites"></a>Požadavky

### <a name="platform-requirements"></a>Požadavky platformy

Ujistěte se, že je nainstalované integrované vývojové prostředí XCode Mac.

### <a name="get-the-client-library-and-examples"></a>Získat klienta knihovny a příklady

Klientská knihovna pro zpracování řeči a příklady pro iOS jsou k dispozici na [řeči Klientská sada SDK pro iOS](https://github.com/microsoft/cognitive-speech-stt-ios).

### <a name="subscribe-to-the-speech-recognition-api-and-get-a-free-trial-subscription-key"></a>Přihlaste se k rozhraní API pro rozpoznávání řeči odběru a získání klíče bezplatné předplatné zkušební verze

Rozhraní Speech API je součástí služeb Cognitive Services (dříve Project Oxford). Můžete získat bezplatné předplatné zkušební verze klíče z [předplatné služeb Cognitive Services](https://azure.microsoft.com/try/cognitive-services/) stránky. Po výběru rozhraní API pro rozpoznávání řeči, vyberte **získat klíč rozhraní API** získat klíč. Vrátí primární a sekundární klíč. Oba klíče jsou svázány se stejnou kvótu, abyste mohli používat ani jeden klíč.

Pokud chcete použít *využijte záměr*, budete také muset zaregistrovat [Language Understanding Intelligent Service (LUIS)](https://azure.microsoft.com/services/cognitive-services/language-understanding-intelligent-service/).

> [!IMPORTANT]
> * Získáte klíč předplatného. Než budete moct použít klientské knihovny pro zpracování řeči, musíte mít [klíč předplatného](https://azure.microsoft.com/try/cognitive-services/).
>
> * Použijte váš klíč předplatného. Pomocí ukázkové aplikace pro iOS zadaný budete muset aktualizovat soubor Samples/SpeechRecognitionServerExample/settings.plist váš klíč předplatného. Další informace najdete v tématu [sestavení a spuštění ukázky](#build-and-run-samples).

## <a name="use-the-speech-client-library"></a>Použití knihovny řečového klienta

Chcete-li přidat klientské knihovny do projektu XCode, postupujte podle těchto [pokyny](https://github.com/Azure-Samples/Cognitive-Speech-STT-iOS#the-client-library).

Klient knihovna – referenční informace pro iOS, najdete v tématu to [webová stránka](https://cdn.rawgit.com/Microsoft/Cognitive-Speech-STT-iOS/master/com.Microsoft.SpeechSDK-1_0-for-iOS.docset/Contents/Resources/Documents/index.html).

## <a name="build-and-run-samples"></a>Sestavení a spuštění ukázek

Informace o tom, jak sestavení a spuštění ukázky, naleznete v tomto [stránce README](https://github.com/Azure-Samples/Cognitive-Speech-STT-iOS#the-sample).

## <a name="samples-explained"></a>Vysvětlení ukázky

### <a name="create-recognition-clients"></a>Vytvoření klientů rozpoznávání

Následující kód v ukázce ukazuje, jak vytvořit třídy klienta rozpoznávání podle uživatelských scénářů:

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

Klientská knihovna poskytuje předem implementované rozpoznávání třídy klienta pro typické scénáře v rozpoznávání řeči:

* `DataRecognitionClient`: Rozpoznávání řeči pomocí PCM data (například ze zdrojového souboru nebo ve zvukovém souboru). Data je rozdělený do vyrovnávací paměti a každý vyrovnávací paměti se odesílají službě řeči. Žádné úpravy se provádí do vyrovnávací paměti, takže v případě potřeby můžou uživatelé používat vlastní detekce nečinnosti. Pokud data ze souborů WAV můžete odeslat data ze souboru přímo na serveru. Pokud máte nezpracovaná data, například zvukový procházející přes Bluetooth, nejdřív odešlete formát hlavičky serveru, za nímž následuje data.
* `MicrophoneRecognitionClient`: Rozpoznávání řeči pomocí zvuk přicházející z mikrofon. Ujistěte se, že mikrofonu je zapnuté a mikrofon tato data se odesílají službě rozpoznávání řeči. Integrované "nečinnosti detektoru s" se použije na mikrofon data před odesláním do služby rozpoznávání.
* `DataRecognitionClientWithIntent` a `MicrophoneRecognitionClientWithIntent`: Kromě rozpoznávání textu vrátí tyto klienty strukturovaných informací o záměr mluvčího, které vaše aplikace můžete použít k řízení další akce. Pokud chcete používat "Záměr", budete muset nejprve trénování modelu s použitím [LUIS](https://azure.microsoft.com/services/cognitive-services/language-understanding-intelligent-service/).

### <a name="recognition-language"></a>Rozpoznávání jazyka

Při použití `SpeechRecognitionServiceFactory` pro vytvoření klienta, musíte vybrat jazyk. Úplný seznam jazyků podporovaných službou Speech Service, najdete v části [podporované jazyky](../API-Reference-REST/supportedlanguages.md).

### <a name="speechrecognitionmode"></a>SpeechRecognitionMode

Budete taky muset zadat `SpeechRecognitionMode` při vytváření klienta se `SpeechRecognitionServiceFactory`:

* `SpeechRecognitionMode_ShortPhrase`: Utterance až do 15 sekund. Odeslání dat službě, obdrží klient několik částečných výsledků a jeden konečný výsledek s několika volbami n-best.
* `SpeechRecognitionMode_LongDictation`: Utterance až dvě minuty dlouho. Odeslání dat službě, obdrží klient několik částečných výsledků a několik konečných výsledků podle kde serveru identifikuje pozastaví věty.

### <a name="attach-event-handlers"></a>Připojte obslužné rutiny událostí

Různé obslužných rutin událostí můžete připojit ke klientovi jste vytvořili:

* **Částečné výsledky události**: Tato událost volána pokaždé, když se tento Speech Service predicts vám může být názory, ještě než dokončíte mluvený (Pokud používáte `MicrophoneRecognitionClient`) nebo dokončení odesílání dat (Pokud používáte `DataRecognitionClient`).
* **Události chyb**: Volá se, když služba zjistí chybu.
* **Záměru události**: Volá se na klientech "WithIntent" (pouze v režimu ShortPhrase) po posledním rozpoznávání výsledek je analyzován do strukturovaného formátu JSON záměr.
* **Výsledek události**:
  * V `SpeechRecognitionMode_ShortPhrase` režimu, tato událost se nazývá a vrátí výsledky, n-best po dokončení čtení.
  * V `SpeechRecognitionMode_LongDictation` režimu, obslužná rutina události je volána více než jednou, podle kde službu identifikuje pozastaví věty.
  * **Pro každou z možností n-best**, je vrácena hodnota spolehlivosti a několik různých forem rozpoznaný text. Další informace najdete v tématu [výstupní formát](../Concepts.md#output-format).

## <a name="related-topics"></a>Související témata

* [Klientská knihovna – referenční informace pro iOS](https://cdn.rawgit.com/Microsoft/Cognitive-Speech-STT-iOS/master/com.Microsoft.SpeechSDK-1_0-for-iOS.docset/Contents/Resources/Documents/index.html)
* [Začínáme s Microsoft rozpoznávání řeči a/nebo záměr v jazyce Java v Androidu](GetStartedJavaAndroid.md)
* [Začínáme s Microsoft Speech API v jazyce JavaScript](GetStartedJSWebsockets.md)
* [Začínáme s Microsoft Speech API prostřednictvím REST](GetStartedREST.md)
