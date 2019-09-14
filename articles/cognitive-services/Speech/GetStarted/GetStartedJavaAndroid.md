---
title: Začínáme s rozhraním API pro rozpoznávání řeči od Microsoftu v Java v Androidu | Microsoft Docs
titlesuffix: Azure Cognitive Services
description: Rozhraní API pro rozpoznávání řeči od Microsoftu slouží k vývoji aplikací pro Android, které převádějí mluvený zvuk na text.
services: cognitive-services
author: nitinme
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-speech
ms.topic: article
ms.date: 09/18/2018
ms.author: nitinme
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 77fee2ecee9cfabe3fad9c1df2c41c7803c3367e
ms.sourcegitcommit: fbea2708aab06c19524583f7fbdf35e73274f657
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/13/2019
ms.locfileid: "70966830"
---
# <a name="quickstart-use-the-bing-speech-recognition-api-in-java-on-android"></a>Rychlý start: Použití rozhraní API pro rozpoznávání Zpracování řeči Bingu v jazyce Java v Androidu

[!INCLUDE [Deprecation note](../../../../includes/cognitive-services-bing-speech-api-deprecation-note.md)]

Pomocí rozhraní API pro rozpoznávání Zpracování řeči Bingu můžete vyvíjet aplikace pro Android, které využívají cloudovou Zpracování řeči Bingu službu pro převod mluveného zvuku na text. Rozhraní API podporuje streamování v reálném čase, takže vaše aplikace může současně a asynchronně přijímat částečné výsledky rozpoznávání ve stejnou dobu, kdy posílá zvuk do služby.

Tento článek používá ukázkovou aplikaci, která předvádí, jak používat klientská knihovna služby Speech pro Android k vývoji aplikací využívajících řeč pro text v Java pro zařízení s Androidem.

## <a name="prerequisites"></a>Požadavky

### <a name="platform-requirements"></a>Požadavky platformy

Ukázka je vyvíjena [Android Studio](https://developer.android.com/sdk/index.html) pro systém Windows v jazyce Java.

### <a name="get-the-client-library-and-sample-application"></a>Získat klientskou knihovnu a ukázkovou aplikaci

Klientská knihovna služby Speech a ukázky pro Android jsou dostupné v [sadě Speech Client SDK pro Android](https://github.com/microsoft/cognitive-speech-stt-android). Ukázku, kterou lze sestavit, najdete v adresáři Samples/SpeechRecoExample. Dvě knihovny, které ve svých aplikacích potřebujete použít, můžete najít ve složce SpeechSDK/knihovny pod složkou armeabi a x86. Velikost souboru libandroid_platform. proto je 22 MB, ale v době nasazení se zkracuje na 4 MB.

#### <a name="subscribe-to-the-speech-api-and-get-a-free-trial-subscription-key"></a>Přihlaste se k odběru rozhraní API pro rozpoznávání řeči a Stáhněte si bezplatný zkušební klíč předplatného.

Rozhraní API pro rozpoznávání řeči je součástí Cognitive Services (dříve projekt Oxford). Můžete získat bezplatný zkušební odběr klíčů na stránce [Cognitive Services předplatné](https://azure.microsoft.com/try/cognitive-services/) . Po výběru rozhraní API pro rozpoznávání řeči vyberte **získat klíč rozhraní API** a získejte klíč. Vrátí primární a sekundární klíč. Oba klíče jsou vázané na stejnou kvótu, takže můžete použít kteroukoli z těchto klíčů.

Pokud chcete použít *rozpoznávání s záměrem*, musíte si také zaregistrovat [Language Understanding Intelligent Service (Luis)](https://azure.microsoft.com/services/cognitive-services/language-understanding-intelligent-service/).

> [!IMPORTANT]
>* Získejte klíč předplatného. Předtím, než budete moci použít klientské knihovny řeči, je nutné mít [klíč předplatného](https://azure.microsoft.com/try/cognitive-services/).
>
>* Použijte svůj klíč předplatného. S poskytnutou ukázkovou aplikací pro Android aktualizujte soubory Samples/SpeechRecoExample/res/Values. XML pomocí klíčů předplatného. Další informace najdete v tématu [sestavení a spuštění ukázek](#build-and-run-samples).

## <a name="use-the-speech-client-library"></a>Použití klientská knihovna služby Speech

Chcete-li použít knihovnu klienta v aplikaci, postupujte podle [pokynů](https://github.com/microsoft/cognitive-speech-stt-android#the-client-library).

Odkaz na knihovnu klienta pro Android najdete ve složce Docs sady [SDK pro sadu Speech klienta pro Android](https://github.com/microsoft/cognitive-speech-stt-android).

## <a name="build-and-run-samples"></a>Sestavení a spuštění ukázek

Informace o tom, jak sestavit a spustit ukázky, najdete na této [stránce Readme](https://github.com/microsoft/cognitive-speech-stt-android#the-sample).

## <a name="samples-explained"></a>Vysvětlení ukázek

### <a name="create-recognition-clients"></a>Vytváření klientů pro rozpoznávání

Kód v následující ukázce ukazuje, jak vytvořit třídy klienta pro rozpoznávání na základě scénářů uživatele:

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

Klientská knihovna poskytuje pro typické scénáře rozpoznávání řeči předem implementované klientské třídy pro rozpoznávání:

* `DataRecognitionClient`: Rozpoznávání řeči s daty PCM (například ze zdroje souboru nebo zvuku). Data jsou rozdělena do vyrovnávacích pamětí a každá vyrovnávací paměť je odeslána službě Speech. Vyrovnávací paměti se neprovádí bez úprav, takže uživatel může v případě potřeby použít vlastní detekci tichého nečinnosti. Pokud jsou data poskytována ze souborů WAV, můžete odesílat data ze souboru přímo do služby Speech. Pokud máte nezpracovaná data, například zvuk přidaný přes Bluetooth, nejdřív odešlete hlavičku formátu ke službě Speech následovanými daty.
* `MicrophoneRecognitionClient`: Rozpoznávání řeči pomocí zvukového přenosu, který přichází z mikrofonu. Ujistěte se, že je mikrofon zapnutý a že se data z mikrofonu odesílají službě rozpoznávání řeči. Pro data mikrofonu se před odesláním do služby pro rozpoznávání použije vestavěný "detektor tichého běhu".
* `DataRecognitionClientWithIntent`a `MicrophoneRecognitionClientWithIntent`: Tito klienti vrátí kromě textu rozpoznávání i strukturované informace o záměru mluvčího, které lze použít k řízení dalších akcí v aplikacích. Chcete-li použít "záměr", musíte nejprve vytvořit model pomocí [Luis](https://azure.microsoft.com/services/cognitive-services/language-understanding-intelligent-service/).

### <a name="recognition-language"></a>Jazyk rozpoznávání

Když použijete `SpeechRecognitionServiceFactory` k vytvoření klienta, musíte vybrat jazyk. Úplný seznam jazyků podporovaných službou Speech Service najdete v části [podporované jazyky](../API-Reference-REST/supportedlanguages.md).

### `SpeechRecognitionMode`

Musíte také určit `SpeechRecognitionMode` , kdy se má `SpeechRecognitionServiceFactory`klient vytvořit:

* `ShortPhrase`: Utterance až 15 sekund dlouho. Jakmile se data odešlou do služby, klient obdrží několik částečných výsledků a jeden konečný výsledek s více než nejlepší možností.
* `LongDictation`: Utterance může trvat až dvě minuty. Jakmile se data odešlou do služby, klient obdrží několik částečných výsledků a několik konečných výsledků na základě toho, kde služba identifikuje větu pozastavena.

### <a name="attach-event-handlers"></a>Připojit obslužné rutiny událostí

K vytvořenému klientovi můžete připojit různé obslužné rutiny událostí:

* **Události částečných výsledků**: Tato událost se volá pokaždé, když služba rozpoznávání řeči předpovídá, co můžete vyjádřit, dokonce i před tím, než dokončíte práci `MicrophoneRecognitionClient`nebo dokončete odesílání dat (Pokud `DataRecognitionClient`používáte).
* **Chybové události**: Volá se, když služba detekuje chybu.
* **Události záměru**: Volá se na klientech "WithIntent" ( `ShortPhrase` pouze v režimu) po tom, co konečný výsledek rozpoznávání se analyzuje do strukturovaného záměru JSON.
* **Události výsledku**:
  * V `ShortPhrase` režimu se tato událost volá a po dokončení mluvení vrátí n nejlepších výsledků.
  * V `LongDictation` režimu je obslužná rutina události volána vícekrát, na základě toho, kde služba identifikuje podobu pozastavení.
  * **Pro každou z těchto n-nejlepších možností**se vrátí hodnota spolehlivosti a několik různých forem rozpoznaného textu. Další informace naleznete v tématu [Output Format](../Concepts.md#output-format).

## <a name="related-topics"></a>Související témata

* [Reference klientské knihovny pro Android](https://github.com/Azure-Samples/Cognitive-Speech-STT-Android/tree/master/docs)
* [Začínáme s rozhraním API pro rozpoznávání řeči C# Microsoftu v pro Windows v .NET](GetStartedCSharpDesktop.md)
* [Začínáme s rozhraním API pro rozpoznávání řeči od Microsoftu v cíli – C v iOS](Get-Started-ObjectiveC-iOS.md)
* [Začínáme s rozhraním API pro rozpoznávání řeči Microsoftu v JavaScriptu](GetStartedJSWebsockets.md)
* [Začínáme s rozhraním Speech API od Microsoftu přes REST](GetStartedREST.md)
