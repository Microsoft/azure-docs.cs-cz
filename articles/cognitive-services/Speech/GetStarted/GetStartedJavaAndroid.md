---
title: Začínáme s API pro rozpoznávání řeči Microsoft v jazyce Java v Androidu | Dokumentace Microsoftu
titlesuffix: Azure Cognitive Services
description: Pomocí rozhraní Speech API Microsoftu pro vývoj aplikací pro Android, které převést mluvené slovo na text.
services: cognitive-services
author: zhouwangzw
manager: wolfma
ms.service: cognitive-services
ms.component: bing-speech
ms.topic: article
ms.date: 09/18/2018
ms.author: zhouwang
ms.openlocfilehash: d62a1fd3a497ed87deb24fc053ec3315a7aba26b
ms.sourcegitcommit: ce526d13cd826b6f3e2d80558ea2e289d034d48f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/19/2018
ms.locfileid: "46367028"
---
# <a name="quickstart-use-the-bing-speech-recognition-api-in-java-on-android"></a>Rychlý start: Použití rozpoznávání řeči Bingu rozhraní API v jazyce Java v Androidu

API pro rozpoznávání řeči Bingu vám umožní vytvářet aplikace pro Android, které můžete převést mluvené slovo na text Speech Service Bingu založené na cloudu. Rozhraní API podporuje streamování v reálném čase, takže vaše aplikace může současně a asynchronně přijímat částečné výsledky rozpoznávání ve stejnou dobu, kterou je posílání zvuku ve službě.

Tento článek provádí ukázková aplikace předvádí způsob použití knihovny řečového klienta pro Android pro vývoj aplikací speech to text v jazyce Java pro zařízení s Androidem.

## <a name="prerequisites"></a>Požadavky

### <a name="platform-requirements"></a>Požadavky na platformu

Ukázka je vyvinutý [Android Studio](http://developer.android.com/sdk/index.html) pro Windows v jazyce Java.

### <a name="get-the-client-library-and-sample-application"></a>Získání klienta knihovny a ukázkové aplikace

Jsou k dispozici v knihovny řečového klienta a ukázky pro Android [řeči Klientská sada SDK pro Android](https://github.com/microsoft/cognitive-speech-stt-android). Můžete najít sestavitelnou vzorku samples/SpeechRecoExample adresáři. Můžete vyhledat dvě knihovny, budete muset použít ve vlastních aplikacích v SpeechSDK/knihovny pod armeabi a x86 složky. Velikost souboru libandroid_platform.so je 22 MB, ale je omezená na 4 MB v době nasazení.

#### <a name="subscribe-to-the-speech-api-and-get-a-free-trial-subscription-key"></a>Přihlaste se k rozhraní API pro rozpoznávání řeči odběru a získejte klíč bezplatné předplatné zkušební verze

Rozhraní Speech API je součástí služeb Cognitive Services (dříve Project Oxford). Můžete získat bezplatné předplatné zkušební verze klíče z [předplatné služeb Cognitive Services](https://azure.microsoft.com/try/cognitive-services/) stránky. Po výběru rozhraní API pro rozpoznávání řeči, vyberte **získat klíč rozhraní API** získat klíč. Vrátí primární a sekundární klíč. Oba klíče jsou svázány se stejnou kvótu, abyste mohli používat ani jeden klíč.

Pokud chcete použít *využijte záměr*, budete také muset zaregistrovat [Language Understanding Intelligent Service (LUIS)](https://azure.microsoft.com/services/cognitive-services/language-understanding-intelligent-service/).

> [!IMPORTANT]
>* Získáte klíč předplatného. Než budete moct použít klientské knihovny pro zpracování řeči, musíte mít [klíč předplatného](https://azure.microsoft.com/try/cognitive-services/).
>
>* Použijte váš klíč předplatného. Pomocí poskytnutý Androidem ukázkové aplikace aktualizujte soubor samples/SpeechRecoExample/res/values/strings.xml klíče předplatného. Další informace najdete v tématu [sestavení a spuštění ukázky](#build-and-run-samples).

## <a name="use-the-speech-client-library"></a>Použití knihovny řečového klienta

Chcete-li použít klientskou knihovnu pro ve vaší aplikaci, postupujte [pokyny](https://github.com/microsoft/cognitive-speech-stt-android#the-client-library).

Najdete klienta knihovna – referenční informace pro Android ve složce dokumentace [řeči Klientská sada SDK pro Android](https://github.com/microsoft/cognitive-speech-stt-android).

## <a name="build-and-run-samples"></a>Sestavení a spuštění ukázek

Informace o sestavení a spuštění ukázek, najdete v tomto [stránce README](https://github.com/microsoft/cognitive-speech-stt-android#the-sample).

## <a name="samples-explained"></a>Vysvětlení ukázky

### <a name="create-recognition-clients"></a>Vytvoření klientů rozpoznávání

Kód v následujícím příkladu ukazuje, jak vytvořit třídy klienta rozpoznávání podle uživatelských scénářů:

```java
void initializeRecoClient()
    {
        String language = "en-us";

        String subscriptionKey = this.getString(R.string.subscription_key);
        String luisAppID = this.getString(R.string.luisAppID);
        String luisSubscriptionID = this.getString(R.string.luisSubscriptionID);

        if (m_isMicrophoneReco && null == m_micClient) {
            if (!m_isIntent) {
                m_micClient = SpeechRecognitionServiceFactory.createMicrophoneClient(this,
                                                                                     m_recoMode,
                                                                                     language,
                                                                                     this,
                                                                                     subscriptionKey);
            }
            else {
                MicrophoneRecognitionClientWithIntent intentMicClient;
                intentMicClient = SpeechRecognitionServiceFactory.createMicrophoneClientWithIntent(this,
                                                                                                   language,
                                                                                                   this,
                                                                                                   subscriptionKey,
                                                                                                   luisAppID,
                                                                                                   luisSubscriptionID);
                m_micClient = intentMicClient;

            }
        }
        else if (!m_isMicrophoneReco && null == m_dataClient) {
            if (!m_isIntent) {
                m_dataClient = SpeechRecognitionServiceFactory.createDataClient(this,
                                                                                m_recoMode,
                                                                                language,
                                                                                this,
                                                                                subscriptionKey);
            }
            else {
                DataRecognitionClientWithIntent intentDataClient;
                intentDataClient = SpeechRecognitionServiceFactory.createDataClientWithIntent(this,
                                                                                              language,
                                                                                              this,
                                                                                              subscriptionKey,
                                                                                              luisAppID,
                                                                                              luisSubscriptionID);
                m_dataClient = intentDataClient;
            }
        }
    }

```

Klientská knihovna poskytuje předem implementované rozpoznávání třídy klienta pro typické scénáře v rozpoznávání řeči:

* `DataRecognitionClient`: Rozpoznávání řeči PCM daty (například ze zdrojového souboru nebo ve zvukovém souboru). Data je rozdělený do vyrovnávací paměti a každý vyrovnávací paměti se odesílají službě řeči. Žádné úpravy se provádí do vyrovnávací paměti, tak v případě potřeby může uživatel použít vlastní detekce nečinnosti. Pokud data ze souborů WAV můžete odeslat data ze souboru přímo na Speech Service. Pokud máte nezpracovaná data, například zvukový procházející přes Bluetooth, nejdřív odešlete hlavičku formátu Speech Service, za nímž následuje data.
* `MicrophoneRecognitionClient`: Rozpoznávání řeči s zvuk přicházející z mikrofon. Ujistěte se, že je zapnutý mikrofon a mikrofon se odešlou do služby rozpoznávání řeči. Integrované "nečinnosti detektoru s" se použije na mikrofon data před odesláním do služby rozpoznávání.
* `DataRecognitionClientWithIntent` a `MicrophoneRecognitionClientWithIntent`: tito klienti vrátit, kromě rozpoznávání textu, strukturované informace o záměr mluvčího, který slouží k řízení další akce vaší aplikace. Pokud chcete používat "Záměr", budete muset nejprve trénování modelu s použitím [LUIS](https://azure.microsoft.com/services/cognitive-services/language-understanding-intelligent-service/).

### <a name="recognition-language"></a>Rozpoznávání jazyka

Při použití `SpeechRecognitionServiceFactory` pro vytvoření klienta, musíte vybrat jazyk. Úplný seznam jazyků podporovaných službou Speech Service, najdete v části [podporované jazyky](../API-Reference-REST/supportedlanguages.md).

### `SpeechRecognitionMode`

Budete taky muset zadat `SpeechRecognitionMode` při vytváření klienta se `SpeechRecognitionServiceFactory`:

* `ShortPhrase`: Celé utterance až do 15 sekund dlouho. Odeslání dat službě, obdrží klient několik částečných výsledků a jeden konečný výsledek s několika volbami n-best.
* `LongDictation`: Celé utterance až dvě minuty dlouho. Odeslání dat službě, obdrží klient několik částečných výsledků a několik konečných výsledků podle kde službu identifikuje pozastaví věty.

### <a name="attach-event-handlers"></a>Připojte obslužné rutiny událostí

Různé obslužných rutin událostí můžete připojit ke klientovi jste vytvořili:

* **Částečné výsledky události**: Tato událost volána pokaždé, když Speech Service předpovídá, co vám může být říká, ještě předtím, než dokončíte mluvený (Pokud používáte `MicrophoneRecognitionClient`) nebo dokončení odesílání dat (Pokud používáte `DataRecognitionClient`).
* **Události chyb**: volá se, když služba detekuje chybu.
* **Záměru události**: volá se na klientech "WithIntent" (pouze v `ShortPhrase` režimu) po rozpoznávání konečný výsledek je analyzován do strukturovaného formátu JSON záměr.
* **Výsledek události**:
  * V `ShortPhrase` režimu, tato událost se nazývá a vrátí výsledky, n-best po dokončení čtení.
  * V `LongDictation` režimu, obslužná rutina události je volána více než jednou, podle kde službu identifikuje pozastaví věty.
  * **Pro každou z možností n-best**, je vrácena hodnota spolehlivosti a několik různých forem rozpoznaný text. Další informace najdete v tématu [výstupní formát](../Concepts.md#output-format).

## <a name="related-topics"></a>Související témata

* [Klientská knihovna – referenční informace pro Android](https://github.com/Azure-Samples/Cognitive-Speech-STT-Android/tree/master/docs)
* [Začínáme s Microsoft Speech API v jazyce C# pro Windows v prostředí .NET](GetStartedCSharpDesktop.md)
* [Začínáme s rozhraním API pro rozpoznávání řeči Microsoft v Objective-C v iOS](Get-Started-ObjectiveC-iOS.md)
* [Začínáme s Microsoft Speech API v jazyce JavaScript](GetStartedJSWebsockets.md)
* [Začínáme s Microsoft Speech API prostřednictvím REST](GetStartedREST.md)
