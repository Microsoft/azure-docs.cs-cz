---
title: Začínáme s API pro rozpoznávání řeči Bingu pomocí klasické pracovní plochy knihovny jazyka C# | Dokumentace Microsoftu
titlesuffix: Azure Cognitive Services
description: Vyvíjejte základní aplikace Windows, které převést mluvené slovo na text pomocí rozhraní API pro rozpoznávání řeči Bingu.
services: cognitive-services
author: zhouwangzw
manager: wolfma
ms.service: cognitive-services
ms.subservice: bing-speech
ms.topic: article
ms.date: 09/18/2018
ms.author: zhouwang
ms.openlocfilehash: 4d03ccfddab9a4aab4a1eacde02d68652bf5103a
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2019
ms.locfileid: "55219069"
---
# <a name="quickstart-use-the-bing-speech-recognition-api-in-c35-for-net-on-windows"></a>Rychlý start: Rozpoznávání řeči Bingu rozhraní API v jazyce C&#35; pro .NET pro Windows

[!INCLUDE [Deprecation note](../../../../includes/cognitive-services-bing-speech-api-deprecation-note.md)]

Na této stránce ukazuje, jak vytvořit základní aplikaci Windows, který používá rozhraní API pro rozpoznávání řeči do převést mluvené slovo na text. Pomocí klientské knihovny umožňují streamování v reálném čase, což znamená, že pokud klientská aplikace odešle zvuku ve službě, asynchronně a současně přijme částečné výsledky rozpoznávání zpět.

Vývojáři, kteří chtějí používat Speech Service z aplikací, které běží na libovolném zařízení můžete použít knihovnu klasické pracovní plochy jazyka C#. Použití knihovny, nainstalujte [balíček NuGet Microsoft.ProjectOxford.SpeechRecognition x86](https://www.nuget.org/packages/Microsoft.ProjectOxford.SpeechRecognition-x86/) pro 32bitové platformě a [balíček NuGet Microsoft.ProjectOxford.SpeechRecognition x64](https://www.nuget.org/packages/Microsoft.ProjectOxford.SpeechRecognition-x64/) pro 64bitové platformě. Klientská knihovna reference k rozhraní API najdete v části [klasické pracovní plochy knihovny Microsoft řeči C#](https://cdn.rawgit.com/Microsoft/Cognitive-Speech-STT-Windows/master/docs/SpeechSDK/index.html).

Následující části popisují, jak nainstalovat, sestavení a spuštění ukázkové aplikace C# s použitím knihovny klasické pracovní plochy jazyka C#.

## <a name="prerequisites"></a>Požadavky

### <a name="platform-requirements"></a>Požadavky platformy

Následující ukázka byla vyvinuta pro Windows 8 + a rozhraní .NET Framework 4.5 + pomocí [Visual Studio 2015, Community Edition](https://www.visualstudio.com/products/visual-studio-community-vs).

### <a name="get-the-sample-application"></a>Získat ukázkovou aplikaci

Naklonujte ukázku z [ukázka klasické pracovní plochy knihovny řeči C#](https://github.com/microsoft/cognitive-speech-stt-windows) úložiště.

### <a name="subscribe-to-the-speech-recognition-api-and-get-a-free-trial-subscription-key"></a>Přihlaste se k rozhraní API pro rozpoznávání řeči odběru a získání klíče bezplatné předplatné zkušební verze

Rozhraní Speech API je součástí služeb Cognitive Services (dříve Project Oxford). Můžete získat bezplatné předplatné zkušební verze klíče z [předplatné služeb Cognitive Services](https://azure.microsoft.com/try/cognitive-services/) stránky. Po výběru rozhraní API pro rozpoznávání řeči, vyberte **získat klíč rozhraní API** získat klíč. Vrátí primární a sekundární klíč. Oba klíče jsou svázány se stejnou kvótu, abyste mohli používat ani jeden klíč.

> [!IMPORTANT]
> * Získáte klíč předplatného. Než použijete klientské knihovny pro zpracování řeči, musíte mít [klíč předplatného](https://azure.microsoft.com/try/cognitive-services/).
>
> * Použijte váš klíč předplatného. Pomocí zadaného jazyka C# klasické pracovní plochy ukázkové aplikace vložte váš klíč předplatného do textového pole při spuštění ukázky. Další informace najdete v tématu [spuštění ukázkové aplikace](#step-3-run-the-sample-application).

## <a name="step-1-install-the-sample-application"></a>Krok 1: Nainstalovat ukázkovou aplikaci

1. Spusťte Visual Studio 2015 a vyberte **souboru** > **otevřít** > **projekt či řešení**.

2. Přejděte do složky, kam jste uložili stažené soubory rozhraní API pro rozpoznávání řeči. Vyberte **řeči** > **Windows**a potom vyberte složku ukázkové webové části.

3. Dvojitým kliknutím otevřete soubor řešení sady Visual Studio 2015 (.sln) s názvem SpeechToText. WPF Samples.sln. Řešení se otevře v sadě Visual Studio.

## <a name="step-2-build-the-sample-application"></a>Krok 2: Vytvoření ukázkové aplikace

1. Pokud chcete použít *využijte záměr*, musíte nejprve zaregistrovat [Language Understanding Intelligent Service (LUIS)](https://azure.microsoft.com/services/cognitive-services/language-understanding-intelligent-service/). Pak nastavte hodnotu klíče pomocí adresu URL koncového bodu aplikace LUIS `LuisEndpointUrl` v souboru app.config ve složce samples/SpeechRecognitionServiceExample. Další informace o adresu URL koncového bodu aplikace LUIS, naleznete v tématu [svou aplikaci můžete publikovat](../../luis/luis-get-started-create-app.md#publish-your-app).

   > [!TIP]
   > Nahradit `&` znak adresu URL koncového bodu služby LUIS s `&amp;` zajistit, že adresa URL je správně interpretována analyzátoru XML.

2. Stiskněte kombinaci kláves Ctrl + Shift + B nebo vyberte **sestavení** na pásu karet. Potom vyberte **sestavit řešení**.

## <a name="step-3-run-the-sample-application"></a>Krok 3: Spuštění ukázkové aplikace

1. Po dokončení sestavení, stiskněte klávesu F5 nebo vyberte **Start** na pásu karet ke spuštění ukázky.

2. Přejděte **Project Oxford převod řeči na Text ukázka** okna. Vložte váš klíč předplatného do **vložte klíč předplatného. Spusťte** textového pole, jak je znázorněno. Chcete-li zachovat klíč předplatného. na počítači nebo přenosném počítači, vyberte **uložit klíč**. Odstranit klíč předplatného ze systému, vyberte **odstranit klíč** ho neodeberete z počítače nebo přenosném počítači.

   ![Klíč vložení v rozpoznávání řeči](../Images/SpeechRecog_paste_key.PNG)

3. V části **zdroj rozpoznávání řeči**, zvolte jednu z šesti řeči zdrojů, které spadají do dvou hlavních kategorií vstupu:

   * K zachycení řeči použijte mikrofon počítače nebo připojené mikrofon.
   * Přehrání zvukového souboru.

   Každá kategorie má tři režimy rozpoznávání:

    * **Režim ShortPhrase**: Utterance až do 15 sekund. Jak se odešlou na server, obdrží klient několik částečných výsledků a jeden konečný výsledek s několika volbami n-best.
    * **Režim LongDictation**: Utterance až dvě minuty dlouho. Jak se odešlou na server, obdrží klient několik částečných výsledků a několik konečných výsledků podle označuje, kde server pozastaví věty.
    * **Rozpoznávání záměru**: Server vrací další strukturovaných informace o řeči vstup. Používání záměru detekce, budete muset nejprve trénování modelu s použitím [LUIS](https://azure.microsoft.com/services/cognitive-services/language-understanding-intelligent-service/).

Ukázka zvukové soubory pomocí této ukázkové aplikaci. Najdete soubory v úložišti, které jste stáhli s touto ukázkou ve složce samples/SpeechRecognitionServiceExample. Tyto ukázkové zvukové soubory spouštěny automaticky, pokud vyberete nebyly vybrány žádné soubory **použít souborů wav Shortphrase režimu** nebo **použít souborů wav pro režim Longdictation** jako řeč vstup. V současné době je podporována pouze WAV zvukový formát.

![Převod řeči na Text rozhraní](../Images/HelloJones.PNG)

## <a name="samples-explained"></a>Vysvětlení ukázky

### <a name="recognition-events"></a>Rozpoznávání události

* **Částečné výsledky události**: Tato událost volána pokaždé, když Speech Service předpovídá, co vám může být říká, ještě předtím, než dokončíte mluvený (Pokud používáte `MicrophoneRecognitionClient`) nebo dokončení odesílání dat (Pokud používáte `DataRecognitionClient`).
* **Události chyb**: Volá se, když služba zjistí chybu.
* **Záměru události**: Volá se na klientech "WithIntent" (pouze v režimu ShortPhrase) po posledním rozpoznávání výsledek je analyzován do strukturovaného formátu JSON záměr.
* **Výsledek události**:
  * V `ShortPhrase` režimu, tato událost se nazývá a vrátí výsledky n-best po dokončení čtení.
  * V `LongDictation` režimu, obslužná rutina události je volána více než jednou, podle kde službu identifikuje pozastaví věty.
  * **Pro každou z možností n-best**, je vrácena hodnota spolehlivosti a několik různých forem rozpoznaný text. Další informace najdete v tématu [výstupní formát](../Concepts.md#output-format).

Obslužné rutiny událostí jsou již uvedli v kódu ve formě komentářů ke kódu.

## <a name="related-topics"></a>Související témata

* [Microsoft Speech klasické pracovní plochy knihovna – referenční informace](https://cdn.rawgit.com/Microsoft/Cognitive-Speech-STT-Windows/master/docs/SpeechSDK/index.html)
* [Začínáme s API pro rozpoznávání řeči Microsoft v jazyce Java v Androidu](GetStartedJavaAndroid.md)
* [Začínáme s API pro rozpoznávání řeči Microsoft v Objective-C v iOS](Get-Started-ObjectiveC-iOS.md)
* [Začínáme s API pro rozpoznávání řeči Microsoft v jazyce JavaScript](GetStartedJSWebsockets.md)
* [Začínáme s API pro rozpoznávání řeči Microsoft přes REST](GetStartedREST.md)
