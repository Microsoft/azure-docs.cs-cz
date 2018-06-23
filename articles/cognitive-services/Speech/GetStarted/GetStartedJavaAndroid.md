---
title: Začínáme s rozhraním API rozpoznávání řeči Microsoft v jazyce Java v systému Android | Microsoft Docs
description: Použijte rozhraní API pro rozpoznávání řeči společnosti Microsoft pro vývoj aplikací pro Android, které mluvené zvuk převedeno na text.
services: cognitive-services
author: zhouwangzw
manager: wolfma
ms.service: cognitive-services
ms.component: bing-speech
ms.topic: article
ms.date: 09/29/2017
ms.author: zhouwang
ms.openlocfilehash: a10f7be1c36fb431016a9867f606e26be858069e
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2018
ms.locfileid: "35342653"
---
# <a name="get-started-with-speech-recognition-in-java-on-android"></a>Začínáme s rozpoznávání řeči v jazyce Java v systému Android

S rozhraním API rozpoznávání řeči můžete vyvíjet Android aplikace, které používají Cloudová služba rozpoznávání řeči mluvené zvuk převést na text. Rozhraní API podporuje streamování v reálném čase, tak, aby vaše aplikace může současně a asynchronní příjem výsledků částečné rozpoznávání ve stejnou dobu, kdy je odesílání zvuk ke službě.

Tento článek používá ukázkovou aplikaci k ukazují, jak používat řeči Klientská knihovna pro Android k vývoji aplikací řeči na text v jazyce Java pro zařízení s Androidem.

## <a name="prerequisites"></a>Požadavky

### <a name="platform-requirements"></a>Požadavky na platformu

Ukázka vyvinutý [Android Studio](http://developer.android.com/sdk/index.html) pro Windows v jazyce Java.

### <a name="get-the-client-library-and-sample-application"></a>Nainstalujte klienta knihovny a ukázkové aplikace

Jsou k dispozici v řeči klientské knihovny a ukázky pro Android [řeči klienta SDK pro Android](https://github.com/microsoft/cognitive-speech-stt-android). Můžete najít sestavitelná ukázka v adresáři ukázky/SpeechRecoExample. Můžete najít dvě knihovny, je nutné použít ve své vlastní aplikace v SpeechSDK/knihovny pod armeabi a x86 složky. Velikost souboru libandroid_platform.so je 22 MB, ale je snížen na 4 MB volného místa v době nasazení.

#### <a name="subscribe-to-the-speech-api-and-get-a-free-trial-subscription-key"></a>Přihlášení k odběru do rozhraní API pro rozpoznávání řeči a získat klíč bezplatné předplatné zkušební verze

Rozhraní API pro rozpoznávání řeči je součástí služby kognitivní (dříve Oxford projektu). Můžete získat zkušební předplatné klíče z [kognitivní služby předplatného](https://azure.microsoft.com/try/cognitive-services/) stránky. Po výběru rozhraní API pro rozpoznávání řeči, vyberte **získat klíč rozhraní API** k získání klíče. Vrátí primární a sekundární klíč. Oba klíče jsou svázané s stejné kvótu, takže můžete použít buď klíč.

Pokud chcete použít *rozpoznávání se záměrem*, musíte také zaregistrovat [jazyk Principy inteligentního služby (LEOŠ)](https://azure.microsoft.com/services/cognitive-services/language-understanding-intelligent-service/).

> [!IMPORTANT]
>* Získáte klíč předplatného. Než budete moci použít řeči klientské knihovny, musíte mít [klíč předplatného](https://azure.microsoft.com/try/cognitive-services/).
>
>* Použijte svůj klíč předplatného. Pomocí zadané Android ukázkovou aplikaci Aktualizujte samples/SpeechRecoExample/res/values/strings.xml soubor s klíči vašeho předplatného. Další informace najdete v tématu [sestavení a spuštění ukázky](#build-and-run-samples).

## <a name="use-the-speech-client-library"></a>Použijte klientskou knihovnu řeči

Klientská knihovna v aplikaci, postupujte podle [pokyny](https://github.com/microsoft/cognitive-speech-stt-android#the-client-library).

Odkaz lze nalézt klienta knihovna pro Android ve složce Dokumenty [řeči klienta SDK pro Android](https://github.com/microsoft/cognitive-speech-stt-android).

## <a name="build-and-run-samples"></a>Sestavení a spuštění ukázky

Informace o sestavení a spuštění ukázky najdete v tématu to [stránce README](https://github.com/microsoft/cognitive-speech-stt-android#the-sample).

## <a name="samples-explained"></a>Ukázky vysvětlené

### <a name="create-recognition-clients"></a>Vytvořit rozpoznávání klienti

Kód na následující příklad ukazuje postup vytvoření třídy klienta rozpoznávání podle scénáře uživatele:

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

Klientská knihovna poskytuje předem implementovaná rozpoznávání třídy klienta pro typické scénáře v rozpoznávání řeči:

* `DataRecognitionClient`: Rozpoznávání řeči daty PCM (například ze zdrojového souboru nebo zvuk). Data je rozdělena na vyrovnávací paměti a každý vyrovnávací paměti je odeslána do řeči služby. Bez úprav provádí do vyrovnávacích pamětí, tak v případě potřeby může uživatel použít vlastní interval detekce. Není-li data ze souborů WAV, může odesílat data ze souboru právo řeči služby. Pokud máte nezpracovaná data, například zvuk procházející přes Bluetooth, budete nejprve odeslání hlavičku formátu službě řeči následuje data.
* `MicrophoneRecognitionClient`: Rozpoznávání řeči s zvuk pocházejících z mikrofonu. Zajistěte, aby mikrofon je zapnutý a data z mikrofon se odesílají do služby rozpoznávání řeči. Předdefinované "ticho detektor" se použije k datům mikrofon před odesláním do služby rozpoznávání.
* `DataRecognitionClientWithIntent` a `MicrophoneRecognitionClientWithIntent`: tito klienti vrátit, kromě rozpoznávání text strukturovaná informace o záměr lektora, který můžete použít k řízení další akce vašich aplikací. Pokud chcete používat "Záměr", je potřeba nejdřív trénování modelu s použitím [LEOŠ](https://azure.microsoft.com/services/cognitive-services/language-understanding-intelligent-service/).

### <a name="recognition-language"></a>Jazyk rozpoznávání

Při použití `SpeechRecognitionServiceFactory` pro vytvoření klienta, je nutné vybrat jazyk. Úplný seznam jazyků podporovaných službou rozpoznávání řeči, najdete v části [podporované jazyky](../API-Reference-REST/supportedlanguages.md).

### `SpeechRecognitionMode`

Budete taky muset zadat `SpeechRecognitionMode` při vytváření klienta se `SpeechRecognitionServiceFactory`:

* `ShortPhrase`: Utterance až 15 sekund dlouho. Podle data odeslání službu, klient obdrží několik částečné výsledky a jeden konečný výsledek s více možností n nejvhodnější.
* `LongDictation`: Utterance až dvou minut dlouho. Jak se data odesílá do služby, klient obdrží více částečné výsledky a více konečné výsledky podle kde službu identifikuje pozastaví věty.

### <a name="attach-event-handlers"></a>Připojte obslužné rutiny událostí

Různé obslužné rutiny událostí můžete připojit ke klientovi jste vytvořili:

* **Částečné výsledky události**: Tato událost volala pokaždé, když služba rozpoznávání řeči předpovídá, co vám může být informacemi o tom, ještě před dokončení hovořícího (Pokud používáte `MicrophoneRecognitionClient`) nebo dokončení odesílání dat (Pokud používáte `DataRecognitionClient`).
* **Chybové události**: volána, když se služba zjistí chybu.
* **Záměrné události**: volat na klientech "WithIntent" (pouze v `ShortPhrase` režimu) po posledním rozpoznávání výsledek je analyzován do strukturovaných záměr JSON.
* **Způsobit události**:
  * V `ShortPhrase` režimu, tato událost je volána a vrátí n nejlepší výsledky, po dokončení hovořícího.
  * V `LongDictation` režimu, obslužné rutiny události je volat vícekrát, podle kde službu identifikuje pozastaví věty.
  * **Pro každou z těchto možností n nejvhodnější**, je vrácena hodnota spolehlivosti a několik různé formy rozpoznaný text. Další informace najdete v tématu [výstupní formát](../Concepts.md#output-format).

## <a name="related-topics"></a>Související témata

* [Referenční dokumentace knihoven klienta pro Android](https://github.com/Azure-Samples/Cognitive-Speech-STT-Android/tree/master/docs)
* [Začínáme s rozhraním API pro rozpoznávání řeči Microsoft v jazyce C# pro systém Windows v rozhraní .NET](GetStartedCSharpDesktop.md)
* [Začínáme s rozhraním API pro rozpoznávání řeči Microsoft v Objective-C v systému iOS](Get-Started-ObjectiveC-iOS.md)
* [Začínáme s rozhraním API pro rozpoznávání řeči Microsoft v jazyce JavaScript](GetStartedJSWebsockets.md)
* [Začínáme s rozhraním API pro rozpoznávání řeči Microsoft přes REST](GetStartedREST.md)
