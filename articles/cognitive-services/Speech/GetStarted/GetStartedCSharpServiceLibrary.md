---
title: Začínáme s rozhraním API pro rozpoznávání řeči Microsoft prostřednictvím služby knihovny jazyka C# | Microsoft Docs
description: V knihovně Microsoft rozpoznávání řeči služby mluvené jazyk převést na text.
services: cognitive-services
author: zhouwangzw
manager: wolfma
ms.service: cognitive-services
ms.component: bing-speech
ms.topic: article
ms.date: 09/17/2017
ms.author: zhouwang
ms.openlocfilehash: 0320f41658a7ac4d6bf9e88ed998c853b665d485
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2018
ms.locfileid: "35342663"
---
# <a name="get-started-with-the-speech-recognition-service-library-in-c35-for-net-windows"></a>Začínáme s knihovnou služby rozpoznávání řeči v jazyce C&#35; pro Windows rozhraní .NET

Knihovna služby je pro vývojáře, kteří mají své vlastní cloudové služby a chcete volání služby řeči ze své služby. Pokud chcete volání služby rozpoznávání řeči z aplikací vázané na zařízení, nepoužívejte tuto sadu SDK. (Použít jiné knihovny klienta nebo rozhraní REST API pro který.)

Pro použití služby knihovny jazyka C#, nainstalujte [balíček NuGet Microsoft.Bing.Speech](https://www.nuget.org/packages/Microsoft.Bing.Speech/). Knihovna referenční dokumentace rozhraní API, najdete v článku [knihovny služby Microsoft řeči C#](https://cdn.rawgit.com/Microsoft/Cognitive-Speech-STT-ServiceLibrary/master/docs/index.html).

Následující části popisují postup instalace, sestavení a spuštění ukázkové aplikace C# s použitím knihovny služby C#.

## <a name="prerequisites"></a>Požadavky

### <a name="platform-requirements"></a>Požadavky na platformu

V následujícím příkladu byla vyvinuta pro systém Windows 8 + a rozhraní .NET 4.5 + Framework pomocí [Visual Studio 2015, Community Edition](https://www.visualstudio.com/products/visual-studio-community-vs).

### <a name="get-the-sample-application"></a>Získat ukázkovou aplikaci

Klonování ukázka z [ukázka knihovny služby řeči C#](https://github.com/Microsoft/Cognitive-Speech-STT-ServiceLibrary) úložiště.

### <a name="subscribe-to-the-speech-recognition-api-and-get-a-free-trial-subscription-key"></a>Přihlášení k odběru do rozhraní API pro rozpoznávání řeči a získat klíč bezplatné předplatné zkušební verze

Rozhraní API pro rozpoznávání řeči je součástí služby kognitivní (dříve Oxford projektu). Můžete získat zkušební předplatné klíče z [kognitivní služby předplatného](https://azure.microsoft.com/try/cognitive-services/) stránky. Po výběru rozhraní API pro rozpoznávání řeči, vyberte **získat klíč rozhraní API** k získání klíče. Vrátí primární a sekundární klíč. Oba klíče jsou svázané s stejné kvótu, takže můžete použít buď klíč.

> [!IMPORTANT]
> * Získáte klíč předplatného. Než budete moci použít knihovny klienta rozpoznávání řeči, musíte mít [klíč předplatného](https://azure.microsoft.com/try/cognitive-services/).
>
> * Použijte svůj klíč předplatného. Zadaná C# knihovny ukázkové aplikace služby je nutné zajistit svůj klíč předplatného jako jeden z parametrů příkazového řádku. Další informace najdete v tématu [spuštění ukázkové aplikace](#step-3-run-the-sample-application).

## <a name="step-1-install-the-sample-application"></a>Krok 1: Instalace ukázkové aplikace

1. Spusťte Visual Studio 2015 a vybrat **soubor** > **otevřete** > **projekt nebo řešení**.

2. Dvojitým kliknutím otevřete soubor Visual Studio 2015 řešení (.sln) s názvem SpeechClient.sln. Otevře se řešení v sadě Visual Studio.

## <a name="step-2-build-the-sample-application"></a>Krok 2: Vytvoření ukázkové aplikace

Stiskněte kombinaci kláves Ctrl + Shift + B, nebo vyberte **sestavení** na pásu karet. Potom vyberte **sestavit řešení**.

## <a name="step-3-run-the-sample-application"></a>Krok 3: Spuštění ukázkové aplikace

1. Po dokončení sestavení stisknutím klávesy F5 nebo vyberte **spustit** na pásu karet pro spuštění příkladu.

2. Otevřete výstupního adresáře pro ukázku, například SpeechClientSample\bin\Debug. Pomocí klávesy Shift + šipka vpravo kliknutím a **sem otevřete příkazové okno**.

3. Spustit `SpeechClientSample.exe` s těmito argumenty:

   * Arg [0]: Zadejte vstupní zvukový soubor WAV.
   * Arg [1]: Zadejte zvuk národní prostředí.
   * Arg [2]: Zadejte režimů rozpoznávání: *krátké* pro `ShortPhrase` režimu a *dlouho* pro `LongDictation` režimu.
   * Arg [3]: Zadejte klíč předplatného pro přístup ke službě rozpoznávání řeči.

## <a name="samples-explained"></a>Ukázky vysvětlené

### <a name="recognition-modes"></a>Režimy rozpoznávání

* `ShortPhrase` režim: utterance až 15 sekund dlouho. Podle data odeslání na server, klient obdrží několik částečné výsledky a jeden konečný výsledek nejlepší.
* `LongDictation` režim: utterance až 10 minut dlouhé. Podle data odeslání na server, klient obdrží několik částečné výsledky a více konečné výsledky, založené na Určuje, kde server větu pozastaví.

### <a name="supported-audio-formats"></a>Podporované formáty zvuk

Rozhraní API pro rozpoznávání řeči podporuje zvuk nebo WAV pomocí následující kodeky:

* Jeden kanál PCM
* Siren
* SirenSR

### <a name="preferences"></a>Předvolby

Pokud chcete vytvořit SpeechClient, musíte nejprve vytvořit objekt předvolby. Objekt předvolby je sadu parametrů, které konfiguruje chování nástroje řeči služby. Obsahuje následující pole:

* `SpeechLanguage`: Národní prostředí zaslané službě řeči zvukovém souboru.
* `ServiceUri`: Používá k volání služby řeči koncový bod.
* `AuthorizationProvider`: Implementace IAuthorizationProvider používá k načtení tokeny pro přístup ke službě řeči. I když ukázka poskytuje zprostředkovatele autorizace kognitivní služby, důrazně doporučujeme, abyste vytvořili vlastní implementace pro zpracování, ukládání tokenu do mezipaměti.
* `EnableAudioBuffering`: O upřesňující možnost. V tématu [správu připojení](#connection-management).

### <a name="speech-input"></a>Hlasový vstup

Objekt SpeechInput se skládá ze dvou polí:

* **Zvuk**: implementace datový proud podle vaší volby, ze kterého vrátí zvuk sady SDK. Může být libovolná [datového proudu](https://msdn.microsoft.com/library/system.io.stream(v=vs.110).aspx) , která podporuje čtení.
   > [!NOTE]
   > Sadu SDK zjistí konec datového proudu, když se vrátí do datového proudu **0** v pro čtení.

* **RequestMetadata**: Metadata o řeči požadavku. Další informace najdete v tématu [odkaz](https://cdn.rawgit.com/Microsoft/Cognitive-Speech-STT-ServiceLibrary/master/docs/index.html).

### <a name="speech-request"></a>Rozpoznávání řeči požadavku

Po vytváření instance objektů SpeechClient a SpeechInput použijte k vytvoření žádosti o službě řeči RecognizeAsync.

```cs
    var task = speechClient.RecognizeAsync(speechInput);
```

Po dokončení požadavku na vrácený RecognizeAsync dokončení úlohy. Poslední RecognitionResult je konec rozpoznávání. Úkol může selhat, pokud služba nebo sady SDK neočekávaně selže.

### <a name="speech-recognition-events"></a>Události rozpoznávání řeči

#### <a name="partial-results-event"></a>Událost částečné výsledky.

Tato událost volala pokaždé, když služba rozpoznávání řeči předpovídá, co vám může být informacemi o tom, ještě před dokončení hovořícího (Pokud používáte `MicrophoneRecognitionClient`) nebo dokončení odesílání dat (Pokud používáte `DataRecognitionClient`). Můžete se přihlásit k události pomocí `SpeechClient.SubscribeToPartialResult()`. Nebo můžete použít metodu předplatné obecné události `SpeechClient.SubscribeTo<RecognitionPartialResult>()`.

**Vrátí formát** | Popis |
------|------
**LexicalForm** | Tento formulář je optimální pro použití aplikacemi, které je třeba výsledky rozpoznávání řeči raw, nezpracované.
**ZobrazenýText** | Rozpoznaný frázi normalizaci inverzní text, malá a velká písmena, interpunkce a vulgárnost maskování použít. Vulgárnost je maskována hvězdičkami po počáteční znak, například "d ***." Tento formulář je optimální pro použití aplikacemi, které zobrazují výsledky rozpoznávání řeči na uživatele.
**Spolehlivosti** | Úroveň spolehlivosti rozpoznaný frázi představuje přidružené Audio podle definice serveru rozpoznávání řeči.
**MediaTime** | Aktuální čas relativně ke spuštění zvuk datového proudu (v časových jednotek 100 nanosekund).
**MediaDuration** | Aktuální frázi doba trvání nebo délka relativně k audio segmentu (v časových jednotek 100 nanosekund).

#### <a name="result-event"></a>Výsledek událostí
Po dokončení hovořícího (v `ShortPhrase` režim), tato událost je volána. N nejvhodnější volby se součástí pro výsledek. V `LongDictation` režimu události lze volat vícekrát, podle Určuje, kde server větu pozastaví. Můžete se přihlásit k události pomocí `SpeechClient.SubscribeToRecognitionResult()`. Nebo můžete použít metodu předplatné obecné události `SpeechClient.SubscribeTo<RecognitionResult>()`.

**Vrátí formát** | Popis |
------|------|
**RecognitionStatus** | Stav na tom, jak bylo vytvořeno rozpoznávání. Například se nevytvořil v důsledku úspěšné rozpoznávání nebo v důsledku zrušení připojení atd.
**Fráze** | Sada rozpoznaný frází n nejlépe s jistotou rozpoznávání.

Další informace o výsledcích rozpoznávání najdete v tématu [výstupní formát](../Concepts.md#output-format).

### <a name="speech-recognition-response"></a>Odpověď rozpoznávání řeči

Příklad řeči odpovědi:
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

Rozhraní API využívá jednoho připojení protokolu WebSocket každý požadavek. Pro optimální uživatelské prostředí sadu SDK pokusí znovu připojit ke službě řeči a spustit rozpoznávání z poslední RecognitionResult, který obdržel. Například pokud zvuk žádost dvě minuty., sada SDK přijme RecognitionEvent na značce jedné minuty a sítě došlo k chybě po pět sekund, sady SDK se začne nové připojení, která se spouští z označit jedné minuty.

>[!NOTE]
>Sada SDK není hledat zpět do jedné minuty označit, protože datový proud nepodporuje vyhledávání. Místo toho sady SDK udržuje vnitřní vyrovnávací paměť, používá k přechodnému ukládání zvuk a vymaže vyrovnávací paměti jako obdrží RecognitionResult události.

## <a name="buffering-behavior"></a>Chování ukládání do vyrovnávací paměti

Ve výchozím nastavení sadu SDK vyrovnávacích pamětí zvuk tak, aby ho obnovit, pokud dojde k přerušení sítě. Ve scénáři, kde je vhodnější zrušíte zvuk přijdete při odpojení sítě a restartování připojení, je nejvhodnější zakázat zvuk ukládání do vyrovnávací paměti nastavením `EnableAudioBuffering` v předvolby objekt, který má `false`.

## <a name="related-topics"></a>Související témata

[Referenční dokumentace knihoven služby Microsoft řeči C#](https://cdn.rawgit.com/Microsoft/Cognitive-Speech-STT-ServiceLibrary/master/docs/index.html)
