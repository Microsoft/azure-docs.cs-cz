---
title: Začínáme s rozhraním API pro rozpoznávání řeči od Microsoftu pomocí C# knihovny služeb | Microsoft Docs
titlesuffix: Azure Cognitive Services
description: Pomocí knihovny služby rozpoznávání Zpracování řeči Bingu můžete převést mluvený jazyk na text.
services: cognitive-services
author: nitinme
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-speech
ms.topic: article
ms.date: 09/18/2018
ms.author: nitinme
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 71c3e471a8844eb6c6b70921e40c94338a084a8b
ms.sourcegitcommit: fbea2708aab06c19524583f7fbdf35e73274f657
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/13/2019
ms.locfileid: "70965847"
---
# <a name="quickstart-use-the-bing-speech-recognition-service-library-in-c35-for-net-windows"></a>Rychlý start: Použití knihovny služeb rozpoznávání Zpracování řeči Bingu v jazyce C&#35; pro Windows .NET

[!INCLUDE [Deprecation note](../../../../includes/cognitive-services-bing-speech-api-deprecation-note.md)]

Knihovna služeb je určená pro vývojáře, kteří mají vlastní cloudovou službu a chtějí volat službu Speech Service ze své služby. Pokud chcete volat službu rozpoznávání řeči z aplikací vázaných na zařízení, nepoužívejte tuto sadu SDK. (Pro to použijte další klientské knihovny nebo rozhraní REST API.)

Pokud chcete použít C# knihovnu služeb, nainstalujte [balíček NuGet Microsoft. Bing. Speech](https://www.nuget.org/packages/Microsoft.Bing.Speech/). Referenční informace k rozhraní API knihovny najdete v [knihovně služby C# Microsoft Speech Service](https://cdn.rawgit.com/Microsoft/Cognitive-Speech-STT-ServiceLibrary/master/docs/index.html).

Následující části popisují, jak nainstalovat, sestavit a spustit C# ukázkovou aplikaci pomocí knihovny C# služby.

## <a name="prerequisites"></a>Požadavky

### <a name="platform-requirements"></a>Požadavky platformy

Následující příklad byl vyvinut pro systémy Windows 8 + a .NET 4.5 + Framework pomocí sady [Visual Studio 2015, Community Edition](https://www.visualstudio.com/products/visual-studio-community-vs).

### <a name="get-the-sample-application"></a>Získat ukázkovou aplikaci

Naklonujte ukázku z [ukázkového úložiště knihovny služby pro rozpoznávání řeči C# ](https://github.com/Microsoft/Cognitive-Speech-STT-ServiceLibrary) .

### <a name="subscribe-to-the-speech-recognition-api-and-get-a-free-trial-subscription-key"></a>Přihlaste se k odběru rozhraní API pro rozpoznávání řeči a získejte bezplatný zkušební klíč předplatného.

Rozhraní API pro rozpoznávání řeči je součástí Cognitive Services (dříve projekt Oxford). Můžete získat bezplatný zkušební odběr klíčů na stránce [Cognitive Services předplatné](https://azure.microsoft.com/try/cognitive-services/) . Po výběru rozhraní API pro rozpoznávání řeči vyberte **získat klíč rozhraní API** a získejte klíč. Vrátí primární a sekundární klíč. Oba klíče jsou vázané na stejnou kvótu, takže můžete použít kteroukoli z těchto klíčů.

> [!IMPORTANT]
> * Získejte klíč předplatného. Předtím, než budete moci použít klientské knihovny pro rozpoznávání řeči, je nutné mít [klíč předplatného](https://azure.microsoft.com/try/cognitive-services/).
>
> * Použijte svůj klíč předplatného. Pomocí zadané C# ukázkové aplikace knihovny služeb musíte zadat klíč předplatného jako jeden z parametrů příkazového řádku. Další informace najdete v tématu [spuštění ukázkové aplikace](#step-3-run-the-sample-application).

## <a name="step-1-install-the-sample-application"></a>Krok 1: Instalace ukázkové aplikace

1. Spusťte Visual Studio 2015 a vyberte **soubor** > **otevřít** > **projekt/řešení**.

2. Dvojím kliknutím otevřete soubor řešení Visual Studio 2015 (. sln) s názvem SpeechClient. sln. Řešení se otevře v aplikaci Visual Studio.

## <a name="step-2-build-the-sample-application"></a>Krok 2: Sestavení ukázkové aplikace

Stiskněte kombinaci kláves CTRL + SHIFT + B nebo vyberte možnost **sestavit** v nabídce pásu karet. Pak vyberte **Sestavit řešení**.

## <a name="step-3-run-the-sample-application"></a>Krok 3: Spuštění ukázkové aplikace

1. Po dokončení sestavení stiskněte klávesu F5 nebo vyberte **Spustit** v nabídce pásu karet a spusťte příklad.

2. Otevřete výstupní adresář pro ukázku, například SpeechClientSample\bin\Debug. Stiskněte Shift + klikněte pravým tlačítkem myši a vyberte **otevřít příkazové okno**.

3. Spusťte `SpeechClientSample.exe` s následujícími argumenty:

   * ARG [0]: Zadejte vstupní zvukový soubor WAV.
   * ARG [1]: Určete národní prostředí zvuku.
   * ARG [2]: Určete režimy rozpoznávání: *Krátké* pro `ShortPhrase` režim *a Long* pro režim.`LongDictation`
   * ARG [3]: Zadejte klíč předplatného pro přístup ke službě rozpoznávání řeči.

## <a name="samples-explained"></a>Vysvětlení ukázek

### <a name="recognition-modes"></a>Režimy rozpoznávání

* `ShortPhrase`Mode Utterance až 15 sekund dlouho. Když se data odesílají na server, klient obdrží několik částečných výsledků a jeden konečný nejlepší výsledek.
* `LongDictation`Mode Utterance až po dobu 10 minut. Po odeslání dat na server klient obdrží více částečných výsledků a více konečných výsledků na základě toho, kde server indikuje pozastavení vět.

### <a name="supported-audio-formats"></a>Podporované formáty zvuku

Rozhraní API pro rozpoznávání řeči podporuje zvuk/WAV pomocí následujících kodeků:

* Jeden kanál PCM
* Siren
* SirenSR

### <a name="preferences"></a>Předvolby

Chcete-li vytvořit SpeechClient, musíte nejprve vytvořit objekt Preferences. Objekt Preferences je sada parametrů, která konfiguruje chování služby Speech. Skládá se z následujících polí:

* `SpeechLanguage`: Národní prostředí zvukového přenosu odeslaného službě Speech Service.
* `ServiceUri`: Koncový bod, který se používá k volání služby řeči.
* `AuthorizationProvider`: Implementace IAuthorizationProvider, která se používá k načtení tokenů za účelem přístupu ke službě Speech. I když ukázka poskytuje zprostředkovatele autorizace Cognitive Services, důrazně doporučujeme vytvořit vlastní implementaci pro zpracování mezipaměti tokenů.
* `EnableAudioBuffering`: Pokročilá možnost. Viz téma [Správa připojení](#connection-management).

### <a name="speech-input"></a>Vstup řeči

Objekt SpeechInput se skládá ze dvou polí:

* **Zvuk**: Implementace datového proudu podle vašeho výběru, ze které sada SDK vyžádá zvuk. Může to být libovolný [datový proud](https://msdn.microsoft.com/library/system.io.stream(v=vs.110).aspx) , který podporuje čtení.
   > [!NOTE]
   > Sada SDK detekuje konec streamu, když datový proud vrátí hodnotu **0** v režimu čtení.

* **RequestMetadata**: Metadata žádosti o řeč Další informace najdete v [referenčních](https://cdn.rawgit.com/Microsoft/Cognitive-Speech-STT-ServiceLibrary/master/docs/index.html)informacích.

### <a name="speech-request"></a>Požadavek na řeč

Po vytvoření instance objektů SpeechClient a SpeechInput použijte RecognizeAsync k vytvoření žádosti o službu Speech.

```cs
    var task = speechClient.RecognizeAsync(speechInput);
```

Po dokončení žádosti se úloha vrácená nástrojem RecognizeAsync dokončí. Poslední RecognitionResult je koncem rozpoznávání. Úkol může selhat, pokud se služba nebo sada SDK neočekávaně nezdaří.

### <a name="speech-recognition-events"></a>Události rozpoznávání řeči

#### <a name="partial-results-event"></a>Událost částečných výsledků

Tato událost se volá pokaždé, když služba rozpoznávání řeči předpovídá, co můžete vyjádřit, dokonce i před tím, než dokončíte práci `MicrophoneRecognitionClient`nebo dokončete odesílání dat (Pokud `DataRecognitionClient`používáte). K odběru události se můžete přihlásit pomocí `SpeechClient.SubscribeToPartialResult()`. Nebo můžete použít metodu `SpeechClient.SubscribeTo<RecognitionPartialResult>()`předplatného pro obecné události.

**Návratový formát** | Popis |
------|------
**LexicalForm** | Tento formulář je optimální pro použití aplikacemi, které potřebují nezpracované výsledky rozpoznávání řeči.
**DisplayText** | Rozpoznaná fráze, která má použitu normalizaci textu, Velká písmena, interpunkční znaménko a vulgární výrazy Vulgární výrazy jsou maskovány hvězdičkami za úvodní znak, například "d * * *". Tento formulář je optimální pro použití aplikacemi, které zobrazují výsledky rozpoznávání řeči uživateli.
**Jistotou** | Úroveň jistoty, kterou rozpoznaná fráze představuje pro přidružený zvuk definovaný serverem pro rozpoznávání řeči.
**MediaTime** | Aktuální čas relativní ke spuštění zvukového datového proudu (v 100 jednotek času).
**MediaDuration** | Doba trvání a délka aktuální fráze vzhledem ke segmentu zvuku (v 100 – nanosekund jednotek času).

#### <a name="result-event"></a>Událost výsledku
Po dokončení mluvení (v `ShortPhrase` režimu) se tato událost zavolá. K dispozici máte s n-nejlepší možností pro výsledek. V `LongDictation` režimu je možné událost volat víckrát, na základě toho, kde server indikuje pozastavení vět. K odběru události se můžete přihlásit pomocí `SpeechClient.SubscribeToRecognitionResult()`. Nebo můžete použít metodu `SpeechClient.SubscribeTo<RecognitionResult>()`předplatného pro obecné události.

**Návratový formát** | Popis |
------|------|
**RecognitionStatus** | Stav způsobu, jakým bylo rozpoznávání vyrobeno. Například byl vytvořen jako výsledek úspěšného rozpoznávání nebo v důsledku zrušení připojení atd.
**Větách** | Sada n-nejlepších rozpoznaných frází s jistotou pro rozpoznávání.

Další informace o výsledcích rozpoznávání naleznete v tématu [Output Format](../Concepts.md#output-format).

### <a name="speech-recognition-response"></a>Odpověď na rozpoznávání řeči

Příklad odpovědi na řeč:
```
--- Partial result received by OnPartialResult  
---what  
--- Partial result received by OnPartialResult  
--what's  
--- Partial result received by OnPartialResult  
---what's the web  
--- Partial result received by OnPartialResult   
---what's the weather like  
---***** Phrase Recognition Status = [Success]   
***What's the weather like? (Confidence:High)  
What's the weather like? (Confidence:High)
```

## <a name="connection-management"></a>Správa připojení

Rozhraní API využívá jedno připojení WebSocket na jednu žádost. Pro zajištění optimálního uživatelského prostředí se sada SDK pokusí znovu připojit ke službě Speech a zahájit rozpoznávání z poslední RecognitionResult, kterou obdržela. Například pokud je požadavek na zvuk 2 minuty, sada SDK obdržela RecognitionEvent na jednosměrnou značku a po pěti sekundách dojde k selhání sítě, sada SDK spustí nové připojení, které začíná od první minuty.

>[!NOTE]
>Sada SDK se nevrátí zpět na jednosměrovou značku, protože datový proud nemusí podporovat hledání. Místo toho sada SDK uchovává vnitřní vyrovnávací paměť, kterou používá k ukládání zvuku do vyrovnávací paměti a vymazání vyrovnávací paměti při přijímání RecognitionResultch událostí.

## <a name="buffering-behavior"></a>Chování při ukládání do vyrovnávací paměti

Sada SDK ve výchozím nastavení ukládá zvuk do vyrovnávací paměti tak, aby se mohla zotavit, když dojde k síťovému přerušení. Ve scénáři, ve kterém je vhodnější zrušit zvuk během odpojení sítě a restartovat připojení, je nejlepší zakázat ukládání zvuku do vyrovnávací paměti `EnableAudioBuffering` nastavením v objektu Preferences na. `false`

## <a name="related-topics"></a>Související témata

[Referenční informace C# ke knihovně služby Microsoft Speech Service](https://cdn.rawgit.com/Microsoft/Cognitive-Speech-STT-ServiceLibrary/master/docs/index.html)
