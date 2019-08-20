---
title: Co jsou služby Speech?
titleSuffix: Azure Cognitive Services
description: Služba Speech Services představuje sjednocení převodu řeči na text, převod textu na řeč a rozpoznávání řeči do jednoho předplatného Azure. Pomocí sady Speech SDK, zařízení Speech SDK nebo rozhraní REST API můžete snadno přidat řeč aplikací, nástrojů a zařízení. Přidejte funkci rozpoznávání řeči do existujícího robota chatu, převeďte převod textu na řeč v aplikaci pro překlad nebo přepisovat velké objemy dat z centra volání.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: overview
ms.date: 07/05/2019
ms.author: erhopf
ms.openlocfilehash: da88c6d0195236a6f93f81d3296e4624228125f2
ms.sourcegitcommit: 55e0c33b84f2579b7aad48a420a21141854bc9e3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/19/2019
ms.locfileid: "69624259"
---
# <a name="what-are-the-speech-services"></a>Co jsou služby Speech?

Služba Speech Services představuje sjednocení převodu řeči na text, převod textu na řeč a rozpoznávání řeči do jednoho předplatného Azure. Rozpoznávání řeči umožňuje používat aplikace, nástroje a zařízení pomocí [sady Speech SDK](speech-sdk-reference.md), [zařízení Speech SDK](https://aka.ms/sdsdk-quickstart)nebo [rozhraní REST API](rest-apis.md).

> [!IMPORTANT]
> Služba Speech Services nahradila rozhraní API pro zpracování řeči Bingu, Translator Speech a Custom Speech. Pokyny k migraci najdete v tématu *Průvodce postupy > migrace* .

Tyto funkce tvoří službu Azure Speech Services. Pomocí odkazů v této tabulce se dozvíte víc o běžných případech použití pro jednotlivé funkce nebo o Projděte si referenční informace k rozhraní API.

| Služba | Funkce | Popis | Sada SDK | REST |
|---------|---------|-------------|-----|------|
| [Speech-to-Text](speech-to-text.md) | Převod řeči na text | Převod řeči na text transcribes zvukové streamy na text v reálném čase, které mohou aplikace, nástroje nebo zařízení spotřebovat nebo zobrazit. Použití převodu řeči na text s [Language Understanding (Luis)](https://docs.microsoft.com/azure/cognitive-services/luis/) k odvození záměrů uživatele z přepisu řeči a fungování hlasových příkazů. | [Ano](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) | [Ano](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis) |
| | [Přepis Batch](batch-transcription.md) | Batch přepis umožňuje provádět přepis velkých objemů dat z převodu řeči na text. Jedná se o službu založenou na REST, která používá stejný koncový bod jako přizpůsobení a Správa modelů. | Ne | [Ano](https://westus.cris.ai/swagger/ui/index) |
| | [Přepis konverzace](conversation-transcription-service.md) | Umožňuje rozpoznávání řeči v reálném čase, identifikaci mluvčího a diarization. Je ideální pro zdlouhavého přepisování schůzky na osobu s možností odlišit reproduktory. | Ano | Ne |
| | [Vytváření modelů Custom Speech](#customize-your-speech-experience) | Pokud používáte převod řeči na text pro rozpoznávání a přepis v jedinečném prostředí, můžete vytvořit a vyškolit vlastní modely akustického, jazyka a výslovnosti pro řešení hluku nebo slovníku specifického pro konkrétní obor. | Ne | [Ano](https://westus.cris.ai/swagger/ui/index) |
| [Text-to-Speech](text-to-speech.md) | Převod textu na řeč | Převod textu na řeč převede vstupní text na syntetizované rozpoznávání řeči pomocí [jazyka SSML (Speech syntézy)](text-to-speech.md#speech-synthesis-markup-language-ssml). Vyberte si ze standardních hlasů a hlasy neuronové (viz [Podpora jazyků](language-support.md)). | [Ano](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) | [Ano](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis) |
| | [Vytváření vlastních hlasů](#customize-your-speech-experience) | Vytvářejte vlastní hlasová písma, která jsou jedinečná pro vaši značku nebo produkt. | Ne | [Ano](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis) |
| [Překlad řeči](speech-translation.md) | Překlad řeči | Překlad řeči umožňuje překlady řeči v reálném čase pro aplikace, nástroje a zařízení v reálném čase. Použijte tuto službu pro překlad řeči a převod řeči na text. | [Ano](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) | Ne |
| [Virtuální asistenti prvního hlasu](voice-first-virtual-assistants.md) | Virtuální asistenti prvního hlasu | Vlastní virtuální asistenti využívající Azure Speech Services umožňují vývojářům vytvářet pro své aplikace a prostředí přirozené rozhraní pro konverzaci podobné člověku. Kanál pro přímý přenos řeči platformy robota tyto možnosti vylepšuje tím, že poskytuje koordinovaný a orchestrující vstupní bod na kompatibilním robotovi, který umožňuje hlas v, komunikaci hlasu s nízkou latencí a vysokou spolehlivostí. | [Ano](voice-first-virtual-assistants.md) | Ne |

## <a name="news-and-updates"></a>Novinky a aktualizace

Zjistěte, co je nového ve službě Azure Speech Services.

* Srpen 2019
  * **Nový kurz**: [Hlasová podpora robota s využitím Speech SDK, C#](tutorial-voice-enable-your-bot-speech-sdk.md)
  * Byl přidán nový styl [`chat`](speech-synthesis-markup.md#adjust-speaking-styles)mluveného slova `en-US-JessaNeural` pro hlasový vstup. 
* Červeně 2019
  * Vydaná sada Speech SDK 1.6.0. Úplný seznam aktualizací, vylepšení a známých problémů najdete v poznámkách k [verzi](releasenotes.md).
* Květen 2019 – dokumentace je nyní k dispozici pro [přepis konverzace](conversation-transcription-service.md), [přepisy centra volání](call-center-transcription.md)a pro [virtuální asistenty hlasu First](voice-first-virtual-assistants.md).
* Květen 2019
  * Vydaná sada Speech SDK 1.5.1. Úplný seznam aktualizací, vylepšení a známých problémů najdete v poznámkách k [verzi](releasenotes.md).
  * Vydaná sada Speech SDK 1.5.0. Úplný seznam aktualizací, vylepšení a známých problémů najdete v poznámkách k [verzi](releasenotes.md).

## <a name="try-speech-services"></a>Vyzkoušejte hlasové služby

Nabízíme rychlé zprovoznění v nejoblíbenějších programovacích jazycích, z nichž každá je navržena tak, aby používala kód za méně než 10 minut. Tato tabulka obsahuje nejoblíbenější rychlé starty pro jednotlivé funkce. Pomocí levé navigační navigace můžete prozkoumat další jazyky a platformy.

| Převod řeči na text (SDK) | Převod textu na řeč (SDK) | Překlad (SDK) |
|----------------------|----------------------|-------------------|
| [C#, .NET Core (Windows)](quickstart-csharp-dotnet-windows.md) | [C#, .NET Framework (Windows)](quickstart-text-to-speech-dotnet-windows.md) | [Java (Windows, Linux)](quickstart-translate-speech-java-jre.md) |
| [JavaScript (prohlížeč)](quickstart-js-browser.md) | [C++Systému](quickstart-text-to-speech-cpp-windows.md) | [C#, .NET Core (Windows)](quickstart-translate-speech-dotnetcore-windows.md) |
| [Python (Windows, Linux, macOS)](quickstart-python.md) | [C++ (Linux)](quickstart-text-to-speech-cpp-linux.md) | [C#, .NET Framework (Windows)](quickstart-translate-speech-dotnetframework-windows.md) |
| [Java (Windows, Linux)](quickstart-java-jre.md) | | [C++Systému](quickstart-translate-speech-cpp-windows.md) |

> [!NOTE]
> Převod řeči na text a převod textu na řeč má také koncové body REST a přidružené rychlé starty.

Až budete mít možnost používat služby pro rozpoznávání řeči, vyzkoušejte náš kurz, který vás učí, jak rozpoznávat záměry pomocí sady Speech SDK a LUIS.

* [Kurz: Rozpoznávání záměrů od řeči pomocí sady Speech SDK a LUIS,C#](how-to-recognize-intents-from-speech-csharp.md)
* [Kurz: Hlasový vstup vám umožní pomocí sady Speech SDK,C#](tutorial-voice-enable-your-bot-speech-sdk.md)
* [Kurz: Sestavte aplikaci v baňce pro překlad textu, analyzujte mínění a syntetizujte přeložený text na řeč, REST](https://docs.microsoft.com/azure/cognitive-services/translator/tutorial-build-flask-app-translation-synthesis?toc=%2fazure%2fcognitive-services%2fspeech-service%2ftoc.json&bc=%2fazure%2fcognitive-services%2fspeech-service%2fbreadcrumb%2ftoc.json&toc=%2Fen-us%2Fazure%2Fcognitive-services%2Fspeech-service%2Ftoc.json&bc=%2Fen-us%2Fazure%2Fbread%2Ftoc.json)

## <a name="get-sample-code"></a>Získání vzorového kódu

Vzorový kód je k dispozici na GitHubu pro každou službu Azure Speech Services. Tyto ukázky se týkají běžných scénářů, jako je čtení zvuku ze souboru nebo datového proudu, průběžné a jednorázové rozpoznávání a práce s vlastními modely. Pomocí těchto odkazů můžete zobrazit ukázky sady SDK a REST:

* [Ukázky převodu řeči na text, převod textu na řeč a rozpoznávání řeči (SDK)](https://github.com/Azure-Samples/cognitive-services-speech-sdk)
* [Ukázky pro dávková přepisy (REST)](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/batch)
* [Ukázky převodu textu na řeč (REST)](https://github.com/Azure-Samples/Cognitive-Speech-TTS)
* [Ukázka hlasu prvního virtuálního pomocníka (SDK)](https://aka.ms/csspeech/samples)

## <a name="customize-your-speech-experience"></a>Přizpůsobení prostředí pro rozpoznávání řeči

Služba Azure Speech Services dobře funguje s integrovanými modely, ale možná budete chtít další přizpůsobení a vyladit prostředí pro svůj produkt nebo prostředí. Možnosti přizpůsobení jsou v rozsahu od ladění akustického modelu až po jedinečná hlasová písma pro vaši značku. Po vytvoření vlastního modelu ho můžete použít u libovolné služby Azure Speech Services.

| Speech Service | Model | Popis |
|----------------|-------|-------------|
| Řeč na text | [Akustický model](how-to-customize-acoustic-models.md) | Vytvořte si vlastní akustický model pro aplikace, nástroje nebo zařízení, které se používají v různých prostředích, jako je třeba v kleci nebo v továrně, z nichž každá má konkrétní podmínky záznamu. Mezi příklady patří zvýrazněné rozpoznávání řeči, konkrétní šum na pozadí nebo použití konkrétního mikrofonu pro záznam. |
| | [Jazykový model](how-to-customize-language-model.md) | Vytvořte vlastní jazykový model pro zlepšení přepisu slovníku a gramatiky specifické pro pole, jako je lékařské terminologie nebo žargonu. |
| | [Model výslovnosti](how-to-customize-pronunciation.md) | Pomocí vlastního modelu výslovnosti můžete definovat fonetický tvar a zobrazení slova nebo termínu. Je vhodný pro zpracování vlastní podmínky, jako jsou názvy produktů nebo zkratky. Vše, co potřebujete, abyste mohli začít se výslovnost soubor – soubor .txt jednoduché. |
| Text na řeč | [Hlasové písmo](how-to-customize-voice-font.md) | Vlastní hlasová písma umožňují vytvořit rozpoznatelný hlasový hlas pro vaši značku. Pro začátek zabere pouze malé množství dat. Větší údaje, které poskytnete, budou zvukové písmo větší, než je vaše hlasová. |

## <a name="reference-docs"></a>Referenční dokumenty

* [Speech SDK](speech-sdk-reference.md)
* [Sada Speech Devices SDK](speech-devices-sdk.md)
* [REST API: Převod řeči na text](rest-speech-to-text.md)
* [REST API: Převod textu na řeč](rest-text-to-speech.md)
* [REST API: Přepis a přizpůsobení Batch](https://westus.cris.ai/swagger/ui/index)

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Získat klíč předplatného služby Speech Services zdarma](get-started.md)
