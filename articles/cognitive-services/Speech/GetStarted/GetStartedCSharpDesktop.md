---
title: Začínáme s rozhraním API pro rozpoznávání Zpracování řeči Bingu pomocí C# desktopové knihovny | Microsoft Docs
titlesuffix: Azure Cognitive Services
description: Vývoj základních aplikací pro Windows, které používají rozhraní API pro rozpoznávání Zpracování řeči Bingu k převedení mluveného zvuku na text.
services: cognitive-services
author: nitinme
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-speech
ms.topic: article
ms.date: 09/18/2018
ms.author: nitinme
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 3160ccc1c8741d87fcee94a6face48551a79052d
ms.sourcegitcommit: fbea2708aab06c19524583f7fbdf35e73274f657
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/13/2019
ms.locfileid: "70966906"
---
# <a name="quickstart-use-the-bing-speech-recognition-api-in-c35-for-net-on-windows"></a>Rychlý start: Použití rozhraní API pro rozpoznávání Zpracování řeči Bingu v&#35; jazyce C pro .NET v systému Windows

[!INCLUDE [Deprecation note](../../../../includes/cognitive-services-bing-speech-api-deprecation-note.md)]

Na této stránce se dozvíte, jak vyvíjet základní aplikaci pro Windows, která používá rozhraní API pro rozpoznávání řeči k převedení mluveného zvuku na text. Použití klientské knihovny umožňuje streamování v reálném čase, což znamená, že když klientská aplikace pošle zvuk do služby, současně a asynchronně obdrží výsledky částečného rozpoznávání zpět.

Vývojáři, kteří chtějí používat službu Speech z aplikací, které běží na jakémkoli zařízení, můžou C# používat desktopovou knihovnu. Pokud chcete použít knihovnu, nainstalujte [balíček NuGet Microsoft. ProjectOxford. SpeechRecognition-x86](https://www.nuget.org/packages/Microsoft.ProjectOxford.SpeechRecognition-x86/) pro 32 platformu a [balíček NuGet Microsoft. ProjectOxford. SpeechRecognition-x64](https://www.nuget.org/packages/Microsoft.ProjectOxford.SpeechRecognition-x64/) pro 64 bitovou platformu. Referenční informace k rozhraní API klientské knihovny najdete v tématu [Knihovna Microsoft Speech C# Desktop Library](https://cdn.rawgit.com/Microsoft/Cognitive-Speech-STT-Windows/master/docs/SpeechSDK/index.html).

Následující části popisují, jak nainstalovat, sestavit a spustit C# ukázkovou aplikaci pomocí C# desktopové knihovny.

## <a name="prerequisites"></a>Požadavky

### <a name="platform-requirements"></a>Požadavky platformy

Následující ukázka byla vyvinuta pro systém Windows 8 + a .NET Framework 4.5 + pomocí sady [Visual Studio 2015, Community Edition](https://www.visualstudio.com/products/visual-studio-community-vs).

### <a name="get-the-sample-application"></a>Získat ukázkovou aplikaci

Naklonujte ukázku z [ukázkového úložiště knihovny C# řeč pro stolní počítače](https://github.com/microsoft/cognitive-speech-stt-windows) .

### <a name="subscribe-to-the-speech-recognition-api-and-get-a-free-trial-subscription-key"></a>Přihlaste se k odběru rozhraní API pro rozpoznávání řeči a získejte bezplatný zkušební klíč předplatného.

Rozhraní API pro rozpoznávání řeči je součástí Cognitive Services (dříve projekt Oxford). Můžete získat bezplatný zkušební odběr klíčů na stránce [Cognitive Services předplatné](https://azure.microsoft.com/try/cognitive-services/) . Po výběru rozhraní API pro rozpoznávání řeči vyberte **získat klíč rozhraní API** a získejte klíč. Vrátí primární a sekundární klíč. Oba klíče jsou vázané na stejnou kvótu, takže můžete použít kteroukoli z těchto klíčů.

> [!IMPORTANT]
> * Získejte klíč předplatného. Než použijete klientské knihovny pro rozpoznávání řeči, musíte mít [klíč předplatného](https://azure.microsoft.com/try/cognitive-services/).
>
> * Použijte svůj klíč předplatného. Pomocí poskytnuté C# ukázkové aplikace pro stolní počítače vložte klíč předplatného do textového pole při spuštění ukázky. Další informace najdete v tématu [spuštění ukázkové aplikace](#step-3-run-the-sample-application).

## <a name="step-1-install-the-sample-application"></a>Krok 1: Instalace ukázkové aplikace

1. Spusťte Visual Studio 2015 a vyberte **soubor** > **otevřít** > **projekt/řešení**.

2. Přejděte do složky, kam jste uložili stažené soubory rozhraní API pro rozpoznávání řeči. Vyberte možnost**okna**pro **rozpoznávání řeči** > a potom vyberte složku Sample-wp.

3. Dvojím kliknutím otevřete soubor řešení Visual Studio 2015 (. sln) s názvem SpeechToText-WPF-Samples. sln. Řešení se otevře v aplikaci Visual Studio.

## <a name="step-2-build-the-sample-application"></a>Krok 2: Sestavení ukázkové aplikace

1. Pokud chcete použít *rozpoznávání s záměrem*, nejprve se musíte zaregistrovat [Language Understanding Intelligent Service (Luis)](https://azure.microsoft.com/services/cognitive-services/language-understanding-intelligent-service/). Pak použijte adresu URL koncového bodu aplikace Luis a nastavte hodnotu klíče `LuisEndpointUrl` v souboru App. config ve složce Samples/SpeechRecognitionServiceExample. Další informace o adrese URL koncového bodu aplikace LUIS najdete v tématu [publikování aplikace](../../luis/luis-get-started-create-app.md#publish-your-app).

   > [!TIP]
   > Nahraďte `&amp;` znak v adrese URL koncového bodu Luis pomocí, abyste zajistili, že adresa URL je správně interpretována analyzátorem XML. `&`

2. Stiskněte kombinaci kláves CTRL + SHIFT + B nebo vyberte možnost **sestavit** v nabídce pásu karet. Pak vyberte **Sestavit řešení**.

## <a name="step-3-run-the-sample-application"></a>Krok 3: Spuštění ukázkové aplikace

1. Po dokončení sestavení stiskněte klávesu F5 nebo vyberte **Spustit** v nabídce pásu karet a spusťte ukázku.

2. Přejít do okna **Ukázka projektu Oxford řeč na text** . Vložte klíč předplatného do pole **Vložit klíč předplatného, abyste mohli začít** s textovým polem. Pokud chcete zachovat klíč předplatného na počítači nebo přenosném počítači, vyberte **Uložit klíč**. Pokud chcete odstranit klíč předplatného ze systému, vyberte **Odstranit klíč** a odeberte ho z počítače nebo přenosného počítače.

   ![Klíč pro vložení rozpoznávání řeči](../Images/SpeechRecog_paste_key.PNG)

3. V části **zdroj rozpoznávání řeči**vyberte jeden ze šesti zdrojů řeči, který patří do dvou hlavních kategorií vstupu:

   * K zachycení řeči použijte mikrofon počítače nebo připojeného mikrofonu.
   * Přehrání zvukového souboru.

   Každá kategorie má tři režimy rozpoznávání:

    * **ShortPhrase režim**: Utterance až 15 sekund dlouho. Když se data odesílají na server, klient obdrží několik částečných výsledků a jeden konečný výsledek s více než nejlepší možností.
    * **LongDictation režim**: Utterance může trvat až dvě minuty. Po odeslání dat na server klient obdrží více částečných výsledků a více konečných výsledků na základě toho, kde server indikuje pozastavení vět.
    * **Detekce záměru**: Server vrátí další strukturované informace o vstupu řeči. Chcete-li použít detekci záměrů, je třeba nejprve vytvořit model pomocí [Luis](https://azure.microsoft.com/services/cognitive-services/language-understanding-intelligent-service/).

Použijte ukázkové zvukové soubory s touto ukázkovou aplikací. Vyhledejte soubory v úložišti, které jste si stáhli pomocí této ukázky, do složky Samples/SpeechRecognitionServiceExample. Tyto ukázkové zvukové soubory jsou spouštěny automaticky, pokud nevyberete žádné jiné soubory, pokud vyberete možnost **použít soubor WAV pro režim Shortphrase** nebo **použít soubor WAV pro režim Longdictation** jako vstup řeči. V současné době je podporován pouze zvukový formát WAV.

![Speech to text Interface](../Images/HelloJones.PNG)

## <a name="samples-explained"></a>Vysvětlení ukázek

### <a name="recognition-events"></a>Události rozpoznávání

* **Události částečných výsledků**: Tato událost se volá pokaždé, když služba rozpoznávání řeči předpovídá, co můžete vyjádřit, dokonce i před tím, než dokončíte práci `MicrophoneRecognitionClient`nebo dokončete odesílání dat (Pokud `DataRecognitionClient`používáte).
* **Chybové události**: Volá se, když služba detekuje chybu.
* **Události záměru**: Volá se na klientech WithIntent (jenom v režimu ShortPhrase), až se výsledek výsledku rozpoznávání analyzuje do strukturovaného záměru JSON.
* **Události výsledku**:
  * V `ShortPhrase` režimu se tato událost volá a po dokončení mluvení vrátí nejlepší výsledky n.
  * V `LongDictation` režimu je obslužná rutina události volána vícekrát, na základě toho, kde služba identifikuje podobu pozastavení.
  * **Pro každou z těchto n-nejlepších možností**se vrátí hodnota spolehlivosti a několik různých forem rozpoznaného textu. Další informace naleznete v tématu [Output Format](../Concepts.md#output-format).

Obslužné rutiny událostí jsou již v kódu ukázány ve formě komentářů kódu.

## <a name="related-topics"></a>Související témata

* [Referenční dokumentace knihovny Microsoft Speech Desktop](https://cdn.rawgit.com/Microsoft/Cognitive-Speech-STT-Windows/master/docs/SpeechSDK/index.html)
* [Začínáme s rozhraním API pro rozpoznávání řeči od Microsoftu v Java v Androidu](GetStartedJavaAndroid.md)
* [Začínáme s rozhraním API pro rozpoznávání řeči od Microsoftu v cíli – C v iOS](Get-Started-ObjectiveC-iOS.md)
* [Začínáme s rozhraním API pro rozpoznávání řeči od Microsoftu v JavaScriptu](GetStartedJSWebsockets.md)
* [Začínáme s rozhraním API pro rozpoznávání řeči od Microsoftu přes REST](GetStartedREST.md)
