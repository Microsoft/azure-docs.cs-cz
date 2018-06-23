---
title: Začínáme s rozhraním API pro rozpoznávání řeči Microsoft prostřednictvím plochy knihovny jazyka C# | Microsoft Docs
description: Vytvořte základní aplikace systému Windows, které používají rozhraní API pro rozpoznávání řeči Microsoft mluvené zvuk převést na text.
services: cognitive-services
author: zhouwangzw
manager: wolfma
ms.service: cognitive-services
ms.component: bing-speech
ms.topic: article
ms.date: 09/27/2017
ms.author: zhouwang
ms.openlocfilehash: e59b0e25401fb5182edd52f82985ffed9052286d
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2018
ms.locfileid: "35342652"
---
# <a name="get-started-with-the-speech-recognition-api-in-c35-for-net-on-windows"></a>Začínáme s rozhraním API pro rozpoznávání řeči v jazyce C&#35; pro .NET v systému Windows

Tato stránka ukazuje, jak vyvíjet základní aplikace Windows, která používá rozhraní API pro rozpoznávání řeči mluvené zvuk převést na text. Pomocí klientské knihovny umožňuje v reálném čase streamování, což znamená, že když klientské aplikace odesílá zvuk ke službě, současně a asynchronně přijímá částečné rozpoznávání výsledky zpět.

Vývojáři, kteří chtějí používat řeči služby z aplikací, které běžely na libovolném zařízení můžete použít knihovnu plochy C#. Na používání knihovny, nainstalujte [balíček NuGet Microsoft.ProjectOxford.SpeechRecognition x86](https://www.nuget.org/packages/Microsoft.ProjectOxford.SpeechRecognition-x86/) pro 32bitovou platformu a [balíček NuGet Microsoft.ProjectOxford.SpeechRecognition x64](https://www.nuget.org/packages/Microsoft.ProjectOxford.SpeechRecognition-x64/) pro 64bitovou platformu. Klientská knihovna referenční dokumentace rozhraní API najdete v části [Microsoft řeči C# plochy knihovny](https://cdn.rawgit.com/Microsoft/Cognitive-Speech-STT-Windows/master/docs/SpeechSDK/index.html).

Následující části popisují postup instalace, sestavení a spuštění ukázkové aplikace C# s použitím plochy knihovna jazyka C#.

## <a name="prerequisites"></a>Požadavky

### <a name="platform-requirements"></a>Požadavky na platformu

Následující ukázka byla vyvinuta pro systém Windows 8 + a rozhraní .NET Framework 4.5 + pomocí [Visual Studio 2015, Community Edition](https://www.visualstudio.com/products/visual-studio-community-vs).

### <a name="get-the-sample-application"></a>Získat ukázkovou aplikaci

Klonování ukázka z [ukázka plochy knihovny řeči C#](https://github.com/microsoft/cognitive-speech-stt-windows) úložiště.

### <a name="subscribe-to-the-speech-recognition-api-and-get-a-free-trial-subscription-key"></a>Přihlášení k odběru do rozhraní API pro rozpoznávání řeči a získat klíč bezplatné předplatné zkušební verze

Rozhraní API pro rozpoznávání řeči je součástí služby kognitivní (dříve Oxford projektu). Můžete získat zkušební předplatné klíče z [kognitivní služby předplatného](https://azure.microsoft.com/try/cognitive-services/) stránky. Po výběru rozhraní API pro rozpoznávání řeči, vyberte **získat klíč rozhraní API** k získání klíče. Vrátí primární a sekundární klíč. Oba klíče jsou svázané s stejné kvótu, takže můžete použít buď klíč.

> [!IMPORTANT]
> * Získáte klíč předplatného. Než použijete řeči klientské knihovny, musíte mít [klíč předplatného](https://azure.microsoft.com/try/cognitive-services/).
>
> * Použijte svůj klíč předplatného. Pomocí zadané C# plochy ukázkovou aplikaci vložte svůj klíč předplatného do textového pole, při spuštění ukázky. Další informace najdete v tématu [spuštění ukázkové aplikace](#step-3-run-the-sample-application).

## <a name="step-1-install-the-sample-application"></a>Krok 1: Instalace ukázkové aplikace

1. Spusťte Visual Studio 2015 a vybrat **soubor** > **otevřete** > **projekt nebo řešení**.

2. Přejděte do složky, kam jste uložili stažené soubory API pro rozpoznávání řeči. Vyberte **řeči** > **Windows**a potom vyberte složku WP ukázka.

3. Dvojitým kliknutím otevřete soubor Visual Studio 2015 řešení (.sln) s názvem SpeechToText. WPF Samples.sln. Otevře se řešení v sadě Visual Studio.

## <a name="step-2-build-the-sample-application"></a>Krok 2: Vytvoření ukázkové aplikace

1. Pokud chcete použít *rozpoznávání se záměrem*, je nutné nejprve zaregistrovat [jazyk Principy inteligentního služby (LEOŠ)](https://azure.microsoft.com/services/cognitive-services/language-understanding-intelligent-service/). Pomocí adresu URL koncového bodu aplikace LEOŠ nastavte hodnotu klíče `LuisEndpointUrl` v souboru app.config ve složce Ukázky/SpeechRecognitionServiceExample. Další informace na adresu URL koncového bodu aplikace LEOŠ najdete v tématu [publikování aplikace](../../luis/luis-get-started-create-app.md#publish-your-app).

   > [!TIP]
   > Nahraďte `&` znak v adrese URL koncového bodu LEOŠ s `&amp;` zajistit, že adresa URL je správně interpretována analyzátoru kódu XML.

2. Stiskněte kombinaci kláves Ctrl + Shift + B, nebo vyberte **sestavení** na pásu karet. Potom vyberte **sestavit řešení**.

## <a name="step-3-run-the-sample-application"></a>Krok 3: Spuštění ukázkové aplikace

1. Po dokončení sestavení stisknutím klávesy F5 nebo vyberte **spustit** na pásu karet ke spuštění ukázky.

2. Přejděte na **projektu Oxford převod řeči na Text ukázka** okno. Vložte svůj klíč předplatného do **vložte klíč předplatného zahájíte** textového pole, jak je vidět. Chcete-li zachovat svůj klíč předplatného na svůj počítač nebo přenosný počítač, vyberte **uložit klíč**. Chcete-li odstranit klíč předplatného ze systému, vyberte **odstranit klíč** jeho odebrání z počítače nebo přenosný počítač.

   ![Klíč vložení v rozpoznávání řeči](../Images/SpeechRecog_paste_key.PNG)

3. V části **zdroj rozpoznávání řeči**, vyberte jednu z šesti řeči zdroje, které lze rozdělit do dvou hlavních kategorií vstupní:

   * Použijte k zachycení řeči mikrofon počítače nebo připojené mikrofonu.
   * Přehrání zvukového souboru.

   Každá kategorie obsahuje tři režimy rozpoznávání:

    * **Režim ShortPhrase**: utterance až 15 sekund dlouho. Podle data odeslání na server, klient obdrží několik částečné výsledky a jeden konečný výsledek s více možností n nejvhodnější.
    * **Režim LongDictation**: utterance až dvou minut dlouhé. Podle data odeslání na server, klient obdrží několik částečné výsledky a více konečné výsledky, založené na Určuje, kde server větu pozastaví.
    * **Záměrné detekce**: server vrátí další strukturovaných informace o řeči vstup. Pokud chcete použít záměrné zjišťování, je potřeba nejdřív trénování modelu s použitím [LEOŠ](https://azure.microsoft.com/services/cognitive-services/language-understanding-intelligent-service/).

Ukázka zvukových souborů pomocí této ukázkové aplikaci. Najít soubory v úložišti, které jste stáhli pomocí této ukázky ve složce Ukázky/SpeechRecognitionServiceExample. Tyto ukázkové zvukové soubory spouštěny automaticky, pokud žádné další soubory jsou vybrali při výběru **použijte soubor wav pro režim Shortphrase** nebo **použijte soubor wav pro režim Longdictation** jako řeč vstup. V současné době se podporuje jenom WAV zvuk formát.

![Převod řeči na Text rozhraní](../Images/HelloJones.PNG)

## <a name="samples-explained"></a>Ukázky vysvětlené

### <a name="recognition-events"></a>Rozpoznávání události

* **Částečné výsledky události**: Tato událost volala pokaždé, když služba rozpoznávání řeči předpovídá, co vám může být informacemi o tom, ještě před dokončení hovořícího (Pokud používáte `MicrophoneRecognitionClient`) nebo dokončení odesílání dat (Pokud používáte `DataRecognitionClient`).
* **Chybové události**: volána, když se služba zjistí chybu.
* **Záměrné události**: volat na klientech "WithIntent" (pouze v režimu ShortPhrase) po posledním rozpoznávání výsledek analýzy do strukturovaných záměr JSON.
* **Způsobit události**:
  * V `ShortPhrase` režimu, tato událost je volána a vrátí n nejlepší výsledky, po dokončení hovořícího.
  * V `LongDictation` režimu, obslužné rutiny události je volat vícekrát, podle kde službu identifikuje pozastaví věty.
  * **Pro každou z těchto možností n nejvhodnější**, je vrácena hodnota spolehlivosti a několik různé formy rozpoznaný text. Další informace najdete v tématu [výstupní formát](../Concepts.md#output-format).

Obslužné rutiny událostí jsou již zdůraznit v kódu ve formě komentáře kódu.

## <a name="related-topics"></a>Související témata

* [Microsoft Speech plochy knihovna – referenční informace](https://cdn.rawgit.com/Microsoft/Cognitive-Speech-STT-Windows/master/docs/SpeechSDK/index.html)
* [Začínáme s rozhraním API rozpoznávání řeči Microsoft v jazyce Java v systému Android](GetStartedJavaAndroid.md)
* [Začínáme s rozhraním API rozpoznávání řeči Microsoft v Objective-C v systému iOS](Get-Started-ObjectiveC-iOS.md)
* [Začínáme s rozhraním API rozpoznávání řeči Microsoft v jazyce JavaScript](GetStartedJSWebsockets.md)
* [Začínáme s rozhraním API rozpoznávání řeči Microsoft přes REST](GetStartedREST.md)
