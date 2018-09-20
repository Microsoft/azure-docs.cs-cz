---
title: Začínáme s rozhraním API pro rozpoznávání řeči Microsoftu pomocí služby knihovny jazyka C# | Dokumentace Microsoftu
titlesuffix: Azure Cognitive Services
description: Převádějte mluvený jazyk na text pomocí knihovny služby rozpoznávání řeči Bingu.
services: cognitive-services
author: zhouwangzw
manager: wolfma
ms.service: cognitive-services
ms.component: bing-speech
ms.topic: article
ms.date: 09/18/2018
ms.author: zhouwang
ms.openlocfilehash: 275f32ebdddf802b4f6d4e5c03cd4a7291cd2e73
ms.sourcegitcommit: ce526d13cd826b6f3e2d80558ea2e289d034d48f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/19/2018
ms.locfileid: "46364308"
---
# <a name="quickstart-use-the-bing-speech-recognition-service-library-in-c35-for-net-windows"></a>Rychlý start: Použití knihovny služby rozpoznávání řeči Bingu v jazyce C&#35; pro Windows rozhraní .NET

Knihovna služby je pro vývojáře, kteří mají své vlastní cloudovou službu a chcete volat Speech Service z příslušnou službu. Pokud chcete volat službu rozpoznávání řeči z aplikace vázané na zařízení, nepoužívejte tuto sadu SDK. (Použít dalších knihoven klienta nebo rozhraní REST API pro daný.)

Použití služby knihovny jazyka C#, nainstalujte [balíček NuGet Microsoft.Bing.Speech](https://www.nuget.org/packages/Microsoft.Bing.Speech/). Knihovna reference k rozhraní API najdete v článku [knihovna služby Microsoft řeči C#](https://cdn.rawgit.com/Microsoft/Cognitive-Speech-STT-ServiceLibrary/master/docs/index.html).

Následující části popisují, jak nainstalovat, sestavení a spuštění ukázkové aplikace C# s použitím knihovny služby jazyka C#.

## <a name="prerequisites"></a>Požadavky

### <a name="platform-requirements"></a>Požadavky na platformu

V následujícím příkladu byla vyvinuta pro Windows 8 + a .NET 4.5 + Framework pomocí [Visual Studio 2015, Community Edition](https://www.visualstudio.com/products/visual-studio-community-vs).

### <a name="get-the-sample-application"></a>Získat ukázkovou aplikaci

Naklonujte ukázku z [ukázka knihovny služby řeči C#](https://github.com/Microsoft/Cognitive-Speech-STT-ServiceLibrary) úložiště.

### <a name="subscribe-to-the-speech-recognition-api-and-get-a-free-trial-subscription-key"></a>Přihlaste se k rozhraní API pro rozpoznávání řeči odběru a získání klíče bezplatné předplatné zkušební verze

Rozhraní Speech API je součástí služeb Cognitive Services (dříve Project Oxford). Můžete získat bezplatné předplatné zkušební verze klíče z [předplatné služeb Cognitive Services](https://azure.microsoft.com/try/cognitive-services/) stránky. Po výběru rozhraní API pro rozpoznávání řeči, vyberte **získat klíč rozhraní API** získat klíč. Vrátí primární a sekundární klíč. Oba klíče jsou svázány se stejnou kvótu, abyste mohli používat ani jeden klíč.

> [!IMPORTANT]
> * Získáte klíč předplatného. Než budete moct použít klientské knihovny pro zpracování řeči, musíte mít [klíč předplatného](https://azure.microsoft.com/try/cognitive-services/).
>
> * Použijte váš klíč předplatného. U zadané služby knihovny ukázkové aplikace v C# je třeba jako jeden z parametrů příkazového řádku zadejte klíč předplatného. Další informace najdete v tématu [spuštění ukázkové aplikace](#step-3-run-the-sample-application).

## <a name="step-1-install-the-sample-application"></a>Krok 1: Instalace ukázkové aplikace

1. Spusťte Visual Studio 2015 a vyberte **souboru** > **otevřít** > **projekt či řešení**.

2. Dvojitým kliknutím otevřete soubor řešení sady Visual Studio 2015 (.sln) s názvem SpeechClient.sln. Řešení se otevře v sadě Visual Studio.

## <a name="step-2-build-the-sample-application"></a>Krok 2: Vytvoření ukázkové aplikace

Stiskněte kombinaci kláves Ctrl + Shift + B nebo vyberte **sestavení** na pásu karet. Potom vyberte **sestavit řešení**.

## <a name="step-3-run-the-sample-application"></a>Krok 3: Spuštění ukázkové aplikace

1. Po dokončení sestavení, stiskněte klávesu F5 nebo vyberte **Start** na pásu karet pro spuštění příkladu.

2. Otevřete výstupní adresář pro ukázky, například SpeechClientSample\bin\Debug. Stiskněte Shift + šipka vpravo klikněte a vyberte **tady Otevřete příkazové okno**.

3. Spustit `SpeechClientSample.exe` s následujícími argumenty:

   * Arg [0]: Zadejte vstupní zvukový soubor WAV.
   * Arg [1]: Určete zvuku národní prostředí.
   * Argument [2]: Zadejte rozpoznávání režimy: *krátký* pro `ShortPhrase` režimu a *dlouhé* pro `LongDictation` režimu.
   * Argument [3]: Zadejte klíč předplatného pro přístup ke službě rozpoznávání řeči.

## <a name="samples-explained"></a>Vysvětlení ukázky

### <a name="recognition-modes"></a>Rozpoznávání režimy

* `ShortPhrase` režim: utterance až do 15 sekund dlouho. Jak se odešlou na server, obdrží klient několik částečných výsledků a jeden konečný výsledek nejlepší.
* `LongDictation` režim: utterance až 10 minut dlouhé. Jak se odešlou na server, obdrží klient několik částečných výsledků a několik konečných výsledků podle označuje, kde server pozastaví věty.

### <a name="supported-audio-formats"></a>Podporované formáty zvuku

Rozhraní Speech API podporuje audio/WAV pomocí následující kodeky:

* PCM jeden kanál
* Siren
* SirenSR

### <a name="preferences"></a>Předvolby

K vytvoření SpeechClient, musíte nejprve vytvořit objekt předvolby. Objekt předvolby je sadu parametrů, které konfiguruje chování služby řeči. Skládá se z těchto polí:

* `SpeechLanguage`: Národní prostředí zvuku zaslané službě řeči.
* `ServiceUri`: Koncový bod používá k volání služby řeči.
* `AuthorizationProvider`: Implementace IAuthorizationProvider slouží k načtení tokeny pro přístup ke službě řeči. I když tento vzorový zprostředkovatele autorizace služby Cognitive Services, důrazně doporučujeme vytvořit vlastní implementaci pro zpracování, ukládání tokenu do mezipaměti.
* `EnableAudioBuffering`: Upřesňující možnosti. Zobrazit [správu připojení](#connection-management).

### <a name="speech-input"></a>Hlasový vstup

Objekt SpeechInput se skládá ze dvou polí:

* **Zvukový**: implementace datového proudu podle vašeho výběru, ze kterého stáhne sada SDK zvuku. Může být kterýkoli [stream](https://msdn.microsoft.com/library/system.io.stream(v=vs.110).aspx) , který podporuje čtení.
   > [!NOTE]
   > Sada SDK rozpozná konce datového proudu, pokud vrací datový proud **0** ve čtení.

* **RequestMetadata**: Metadata o požadavek řeči. Další informace najdete v tématu [odkaz](https://cdn.rawgit.com/Microsoft/Cognitive-Speech-STT-ServiceLibrary/master/docs/index.html).

### <a name="speech-request"></a>Žádost o rozpoznávání řeči

Po instance SpeechClient a SpeechInput objekty pomocí RecognizeAsync vytvořte žádost na Speech Service.

```cs
    var task = speechClient.RecognizeAsync(speechInput);
```

Po dokončení žádosti se dokončí úkol vrácený RecognizeAsync. Poslední RecognitionResult je konec uznání. Úkol může selhat, pokud služba nebo sady SDK dojde k neočekávanému selhání.

### <a name="speech-recognition-events"></a>Události rozpoznávání řeči

#### <a name="partial-results-event"></a>Událost částečné výsledky.

Tato událost volána pokaždé, když Speech Service předpovídá, co vám může být říká, ještě předtím, než dokončíte mluvený (Pokud používáte `MicrophoneRecognitionClient`) nebo dokončení odesílání dat (Pokud používáte `DataRecognitionClient`). Můžete se přihlásit k události pomocí `SpeechClient.SubscribeToPartialResult()`. Nebo můžete použít metodu předplatné obecné události `SpeechClient.SubscribeTo<RecognitionPartialResult>()`.

**Vrátí formát** | Popis |
------|------
**LexicalForm** | Tento formulář je ideální pro použití u aplikací, které je třeba výsledky rozpoznávání řeči a nezpracované.
**ZobrazenýText** | Rozpoznaný fráze s inverzní text normalizace, malá a velká písmena, interpunkce a maskování vulgárních výrazů, které jsou použity. Vulgárních výrazů je maskována hvězdičkami po počátečním znaku, například "d ***." Tento formulář je ideální pro použití u aplikací, které zobrazují výsledky rozpoznávání řeči pro uživatele.
**spolehlivosti** | Úroveň spolehlivosti rozpoznanou frázi představuje pro přidružené zvuk serverem rozpoznávání řeči.
**MediaTime** | Aktuální čas vzhledem k začátku zvukový datový proud (v časových jednotkách 100 nanosekund).
**MediaDuration** | Aktuální frázi doba trvání/délka vzhledem k zvuku segment (v časových jednotkách 100 nanosekund).

#### <a name="result-event"></a>Výsledek události
Po dokončení čtení (v `ShortPhrase` režimu), tato událost je volána. Možnosti pro n-best jste už zadali pro výsledek. V `LongDictation` režimu události může být volána více než jednou, podle označuje, kde server pozastaví věty. Můžete se přihlásit k události pomocí `SpeechClient.SubscribeToRecognitionResult()`. Nebo můžete použít metodu předplatné obecné události `SpeechClient.SubscribeTo<RecognitionResult>()`.

**Vrátí formát** | Popis |
------|------|
**RecognitionStatus** | Stav vytváření uznání. Například ho vznikla jako důsledek úspěšného rozpoznávání, nebo v důsledku zrušení připojení atd.
**Fráze** | Sada n-best rozpoznaný věty bez obav rozpoznávání.

Další informace o rozpoznávání výsledky, naleznete v tématu [výstupní formát](../Concepts.md#output-format).

### <a name="speech-recognition-response"></a>Odpověď rozpoznávání řeči

Příklad odpovědi řeči:
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

Rozhraní API využívá jedno připojení pomocí protokolu WebSocket každý požadavek. Sada SDK se pro zajištění optimálního uživatelského prostředí, pokusí znovu připojit ke službě řeči a rozpoznávání spusťte z poslední RecognitionResult přijatou. Například pokud zvuku žádost o dvou minut dlouhé, sady SDK přijme RecognitionEvent na minutu trvající značce a po pěti sekundách došlo k chybě sítě, sady SDK spustí nové připojení, který začíná na značku jedné minuty.

>[!NOTE]
>Sada SDK nepodporuje hledání zpět na značku jedné minuty vzhledem k tomu, že datový proud nepodporuje vyhledávání. Místo toho sada SDK sleduje vnitřní vyrovnávací paměti, že používá k přechodnému zvuk a vymaže vyrovnávací paměti, když přijímá události RecognitionResult.

## <a name="buffering-behavior"></a>Chování ukládání do vyrovnávací paměti

Ve výchozím nastavení sady SDK vyrovnávacích pamětí zvuk tak, aby ji můžete obnovit, když dojde k přerušení sítě. Ve scénáři, kde je vhodnější než zahodit zvuk přijdete při odpojení sítě a restartujte připojení, je nejvhodnější zakázat zvuku ukládání do vyrovnávací paměti tak, že nastavíte `EnableAudioBuffering` objektu Předvolby `false`.

## <a name="related-topics"></a>Související témata

[Referenční dokumentace knihoven služby Microsoft řeči C#](https://cdn.rawgit.com/Microsoft/Cognitive-Speech-STT-ServiceLibrary/master/docs/index.html)
