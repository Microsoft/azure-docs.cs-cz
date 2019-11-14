---
title: Co jsou služby Speech?
titleSuffix: Azure Cognitive Services
description: Služba Speech Services představuje sjednocení převodu řeči na text, převod textu na řeč a rozpoznávání řeči do jednoho předplatného Azure. Pomocí sady Speech SDK, zařízení Speech SDK nebo rozhraní REST API můžete do svých aplikací, nástrojů a zařízení přidat řeč.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: overview
ms.date: 11/05/2019
ms.author: erhopf
ms.openlocfilehash: b1c32042fcb70ff16bb9d014a9f3546f4435e83f
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/14/2019
ms.locfileid: "74075777"
---
# <a name="what-are-the-speech-services"></a>Co jsou služby Speech?

Služba Speech Services představuje sjednocení převodu řeči na text, převod textu na řeč a rozpoznávání řeči do jednoho předplatného Azure. Rozpoznávání řeči umožňuje používat aplikace, nástroje a zařízení pomocí [sady Speech SDK](speech-sdk-reference.md), [zařízení Speech SDK](https://aka.ms/sdsdk-quickstart)nebo [rozhraní REST API](rest-apis.md).

> [!IMPORTANT]
> Služba Speech Services nahradila rozhraní API pro zpracování řeči Bingu, Translator Speech a Custom Speech. Pokyny k migraci najdete v tématu _Průvodce postupy > migrace_ .

Tyto funkce tvoří službu Azure Speech Services. Pomocí odkazů v této tabulce se dozvíte víc o běžných případech použití pro jednotlivé funkce nebo o Projděte si referenční informace k rozhraní API.

| Služba | Funkce | Popis | Sada SDK | REST |
| ------- | ------- | ----------- | --- | ---- |
| [Speech-to-Text](speech-to-text.md) | Převod řeči na text | Převod řeči na text transcribes zvukové streamy na text v reálném čase, které mohou aplikace, nástroje nebo zařízení spotřebovat nebo zobrazit. Použití převodu řeči na text s [Language Understanding (Luis)](https://docs.microsoft.com/azure/cognitive-services/luis/) k odvození záměrů uživatele z přepisu řeči a fungování hlasových příkazů. | [Ano](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) | [Ano](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis) |
|         | [Přepis Batch](batch-transcription.md) | Batch přepis umožňuje provádět přepis velkých objemů dat z převodu řeči na text. Jedná se o službu založenou na REST, která používá stejný koncový bod jako přizpůsobení a Správa modelů. | Ne | [Ano](https://westus.cris.ai/swagger/ui/index) |
|         | [Přepis konverzace](conversation-transcription-service.md) | Umožňuje rozpoznávání řeči v reálném čase, identifikaci mluvčího a diarization. Je ideální pro zdlouhavého přepisování schůzky na osobu s možností odlišit reproduktory. | Ano | Ne |
|         | [Vytváření modelů Custom Speech](#customize-your-speech-experience) | Pokud používáte převod řeči na text pro rozpoznávání a přepis v jedinečném prostředí, můžete vytvořit a vyškolit vlastní modely akustického, jazyka a výslovnosti pro řešení hluku nebo slovníku specifického pro konkrétní obor. | Ne | [Ano](https://westus.cris.ai/swagger/ui/index) |
| [Text-to-Speech](text-to-speech.md) | Převod textu na řeč | Převod textu na řeč převede vstupní text na syntetizované rozpoznávání řeči pomocí [jazyka SSML (Speech syntézy)](text-to-speech.md#speech-synthesis-markup-language-ssml). Vyberte si ze standardních hlasů a hlasy neuronové (viz [Podpora jazyků](language-support.md)). | [Ano](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) | [Ano](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis) |
|         | [Vytváření vlastních hlasů](#customize-your-speech-experience) | Vytvářejte vlastní hlasová písma, která jsou jedinečná pro vaši značku nebo produkt. | Ne | [Ano](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis) |
| [Překlad řeči](speech-translation.md) | Překlad řeči | Překlad řeči umožňuje překlady řeči v reálném čase pro aplikace, nástroje a zařízení v reálném čase. Použijte tuto službu pro překlad řeči a převod řeči na text. | [Ano](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) | Ne |
| [Asistenti hlasu](voice-assistants.md) | Hlasoví asistenti | Hlasové asistenti, kteří používají službu Azure Speech Services, umožňují vývojářům vytvářet v aplikacích a prostředích přirozené rozhraní pro konverzaci podobné člověku. Služba hlasového asistenta poskytuje rychlou a spolehlivou interakci mezi zařízením a implementací pomocníka, která používá kanál s přímým přístupem na platformě bot nebo integrované vlastní příkazy (Preview) pro dokončení úkolu. | [Ano](voice-assistants.md) | Ne |

## <a name="news-and-updates"></a>Novinky a aktualizace

Zjistěte, co je nového ve službě Azure Speech Services.

- Září 2019
  - Vydaná sada Speech SDK 1.7.0. Úplný seznam aktualizací, vylepšení a známých problémů najdete v [poznámkách k verzi](releasenotes.md).
- Srpen 2019
  - **Nový kurz**: [hlas povolte robotovi pomocí sady Speech SDK, C# ](tutorial-voice-enable-your-bot-speech-sdk.md)
  - Byl přidán nový styl mluveného slova [`chat`](speech-synthesis-markup.md#adjust-speaking-styles)pro `en-US-JessaNeural` hlas.
- Červen 2019
  - Vydaná sada Speech SDK 1.6.0. Úplný seznam aktualizací, vylepšení a známých problémů najdete v [poznámkách k verzi](releasenotes.md).
- Květen 2019 – dokumentace je nyní k dispozici pro [přepis konverzace](conversation-transcription-service.md), [přepisy centra volání](call-center-transcription.md)a [hlasové asistenty](voice-assistants.md).
- Květen 2019
  - Vydaná sada Speech SDK 1.5.1. Úplný seznam aktualizací, vylepšení a známých problémů najdete v [poznámkách k verzi](releasenotes.md).
  - Vydaná sada Speech SDK 1.5.0. Úplný seznam aktualizací, vylepšení a známých problémů najdete v [poznámkách k verzi](releasenotes.md).

## <a name="try-speech-services"></a>Vyzkoušejte hlasové služby

Nabízíme rychlé zprovoznění v nejoblíbenějších programovacích jazycích, z nichž každá je navržena tak, aby používala kód za méně než 10 minut. Tato tabulka obsahuje nejoblíbenější rychlé starty pro jednotlivé funkce. Pomocí levé navigační navigace můžete prozkoumat další jazyky a platformy.

| Převod řeči na text (SDK) | Převod textu na řeč (SDK) | Překlad (SDK) |
| -------------------- | -------------------- | ----------------- |
| [Rozpoznávání řeči ze zvukového souboru](quickstarts/speech-to-text-from-file.md) | [Vysyntetizovat řeč do zvukového souboru](quickstarts/text-to-speech-audio-file.md) | [Přeložit řeč na text](quickstarts/translate-speech-to-text.md) |
| [Rozpoznávání řeči pomocí mikrofonu](quickstarts/speech-to-text-from-microphone.md) | [Vysyntetizátorování řeči na mluvčím](quickstarts/text-to-speech.md) | [Převod řeči na více cílových jazyků](quickstarts/translate-speech-to-text-multiple-languages.md) |
| [Rozpoznávání řeči uložených v úložišti objektů BLOB](quickstarts/from-blob.md) | [Asynchronní syntéza pro dlouhý formát zvuku](quickstarts/text-to-speech/async-synthesis-long-form-audio.md) | [Převod řeči na řeč](quickstarts/translate-speech-to-speech.md) |

> [!NOTE]
> Převod řeči na text a převod textu na řeč má také koncové body REST a přidružené rychlé starty.

Až budete mít možnost používat služby pro rozpoznávání řeči, vyzkoušejte náš kurz, který vás učí, jak rozpoznávat záměry pomocí sady Speech SDK a LUIS.

- [Kurz: rozpoznávání záměrů z řeči pomocí sady Speech SDK a LUISC#](how-to-recognize-intents-from-speech-csharp.md)
- [Kurz: hlasový vstup pro robota pomocí sady Speech SDK,C#](tutorial-voice-enable-your-bot-speech-sdk.md)
- [Kurz: sestavení aplikace v baňce pro překlad textu, analýza mínění a syntetizace přeloženého textu na řeč, REST](https://docs.microsoft.com/azure/cognitive-services/translator/tutorial-build-flask-app-translation-synthesis?toc=%2fazure%2fcognitive-services%2fspeech-service%2ftoc.json&bc=%2fazure%2fcognitive-services%2fspeech-service%2fbreadcrumb%2ftoc.json&toc=%2Fen-us%2Fazure%2Fcognitive-services%2Fspeech-service%2Ftoc.json&bc=%2Fen-us%2Fazure%2Fbread%2Ftoc.json)

## <a name="get-sample-code"></a>Získání vzorového kódu

Vzorový kód je k dispozici na GitHubu pro každou službu Azure Speech Services. Tyto ukázky se týkají běžných scénářů, jako je čtení zvuku ze souboru nebo datového proudu, průběžné a jednorázové rozpoznávání a práce s vlastními modely. Pomocí těchto odkazů můžete zobrazit ukázky sady SDK a REST:

- [Ukázky převodu řeči na text, převod textu na řeč a rozpoznávání řeči (SDK)](https://github.com/Azure-Samples/cognitive-services-speech-sdk)
- [Ukázky pro dávková přepisy (REST)](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/batch)
- [Ukázky převodu textu na řeč (REST)](https://github.com/Azure-Samples/Cognitive-Speech-TTS)
- [Ukázky hlasového asistenta (SDK)](https://aka.ms/csspeech/samples)

## <a name="customize-your-speech-experience"></a>Přizpůsobení prostředí pro rozpoznávání řeči

Služba Azure Speech Services dobře funguje s integrovanými modely, ale možná budete chtít další přizpůsobení a vyladit prostředí pro svůj produkt nebo prostředí. Možnosti přizpůsobení jsou v rozsahu od ladění akustického modelu až po jedinečná hlasová písma pro vaši značku. Po vytvoření vlastního modelu ho můžete použít u libovolné služby Azure Speech Services.

| Speech Service | Platforma | Popis |
| -------------- | -------- | ----------- |
| Řeč na text | [Custom Speech](https://aka.ms/customspeech) | Modely rozpoznávání řeči můžete přizpůsobit vašim potřebám a dostupným datům. Překonání bariér při rozpoznání řeči, jako jsou mluvený styl, slovník a šum na pozadí |
| Převod textu na řeč | [Vlastní hlas](https://aka.ms/customvoice) | Vytvořte si rozpoznatelný jedinečný hlas vašich aplikací pro převod textu na řeč pomocí vašich dostupných dat o hlasu. Hlasové výstupy můžete ještě více ladit úpravou sady hlasových parametrů. |

## <a name="reference-docs"></a>Referenční dokumenty

- [Speech SDK](speech-sdk-reference.md)
- [Sada Speech Devices SDK](speech-devices-sdk.md)
- [REST API: Převod řeči na text](rest-speech-to-text.md)
- [REST API: převod textu na řeč](rest-text-to-speech.md)
- [REST API: přepis a přizpůsobení Batch](https://westus.cris.ai/swagger/ui/index)

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Získejte zdarma klíč předplatného služby Speech](get-started.md)
