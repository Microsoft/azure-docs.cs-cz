---
title: Co je služba Speech?
titleSuffix: Azure Cognitive Services
description: Služba rozpoznávání řeči je sjednocení převodu řeči na text, převod textu na řeč a rozpoznávání řeči do jednoho předplatného Azure. Pomocí sady Speech SDK, zařízení Speech SDK nebo rozhraní REST API můžete do svých aplikací, nástrojů a zařízení přidat řeč.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: overview
ms.date: 03/12/2020
ms.author: trbye
ms.openlocfilehash: 664b71c56ae63ea73478923171731e343dfa5cbe
ms.sourcegitcommit: 8e5b4e2207daee21a60e6581528401a96bfd3184
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/04/2020
ms.locfileid: "84417221"
---
# <a name="what-is-the-speech-service"></a>Co je služba Speech?

Služba rozpoznávání řeči je sjednocení převodu řeči na text, převod textu na řeč a rozpoznávání řeči do jednoho předplatného Azure. Rozpoznávání řeči umožňuje používat aplikace, nástroje a zařízení pomocí [sady Speech SDK](speech-sdk-reference.md), [zařízení Speech SDK](https://aka.ms/sdsdk-quickstart)nebo [rozhraní REST API](rest-apis.md).

> [!IMPORTANT]
> Služba rozpoznávání řeči nahradila rozhraní API pro zpracování řeči Bingu a Translator Speech. Pokyny k migraci najdete v tématu _Průvodce postupy > migrace_ .

Tyto funkce tvoří službu pro rozpoznávání řeči. Pomocí odkazů v této tabulce se dozvíte víc o běžných případech použití pro jednotlivé funkce nebo o Projděte si referenční informace k rozhraní API.

| Služba | Příznak | Description | Sada SDK | REST |
|---------|---------|-------------|-----|------|
| [Převod řeči na text](speech-to-text.md) | Převod řeči na text v reálném čase | Převod řeči na text transcribes nebo přeloží zvukové streamy nebo místní soubory na text v reálném čase, který mohou aplikace, nástroje nebo zařízení spotřebovat nebo zobrazit. Použití převodu řeči na text s [Language Understanding (Luis)](https://docs.microsoft.com/azure/cognitive-services/luis/) k odvození záměrů uživatele z přepisu řeči a fungování hlasových příkazů. | [Ano](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) | [Ano](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis) |
| | [Dávkové zpracování řeči na text](batch-transcription.md) | Batch Speech-to-text umožňuje asynchronní převod řeči na text s velkými objemy zvukového zvuku, který je uložený v Azure Blob Storage. Kromě převodu zvukového zvuku na text, Batch Speech-to-text také umožňuje diarization a mínění-Analysis. | Ne | [Ano](https://westus.cris.ai/swagger/ui/index) |
| | [Konverzace s více zařízeními](multi-device-conversation.md) | Připojení více zařízení nebo klientů v rámci konverzace pro odesílání hlasových a textových zpráv s jednoduchou podporou pro přepis a překlady| Ano | Ne |
| | [Přepis konverzace](conversation-transcription-service.md) | Umožňuje rozpoznávání řeči v reálném čase, identifikaci mluvčího a diarization. Je ideální pro zdlouhavého přepisování schůzky na osobu s možností odlišit reproduktory. | Ano | Ne |
| | [Vytváření modelů Custom Speech](#customize-your-speech-experience) | Pokud používáte převod řeči na text pro rozpoznávání a přepis v jedinečném prostředí, můžete vytvořit a vyškolit vlastní modely akustického, jazyka a výslovnosti pro řešení hluku nebo slovníku specifického pro konkrétní obor. | Ne | [Ano](https://westus.cris.ai/swagger/ui/index) |
| [Převod textu na řeč](text-to-speech.md) | Převod textu na řeč | Převod textu na řeč převede vstupní text na syntetizované rozpoznávání řeči pomocí [jazyka SSML (Speech syntézy)](speech-synthesis-markup.md). Vyberte si ze standardních hlasů a hlasy neuronové (viz [Podpora jazyků](language-support.md)). | [Ano](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) | [Ano](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis) |
| | [Vytváření vlastních hlasů](#customize-your-speech-experience) | Vytvářejte vlastní hlasová písma, která jsou jedinečná pro vaši značku nebo produkt. | Ne | [Ano](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis) |
| [Překlad řeči](speech-translation.md) | Překlad řeči | Překlad řeči umožňuje překlady řeči v reálném čase pro aplikace, nástroje a zařízení v reálném čase. Použijte tuto službu pro překlad řeči a převod řeči na text. | [Ano](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) | Ne |
| [Hlasoví asistenti](voice-assistants.md) | Hlasoví asistenti | Hlasové asistenti, kteří používají službu pro rozpoznávání řeči, umožňují vývojářům vytvářet pro své aplikace a prostředí přirozené rozhraní pro konverzaci podobné člověku. Služba hlasového asistenta poskytuje rychlou a spolehlivou interakci mezi zařízením a implementací pomocníka, která používá kanál s přímým přístupem na platformě bot nebo integrované vlastní příkazy (Preview) pro dokončení úkolu. | [Ano](voice-assistants.md) | Ne |
| [Rozpoznávání mluvčího](speaker-recognition-overview.md) | Identifikace & ověření mluvčího | Služba rozpoznávání mluvčího poskytuje algoritmy pro ověřování a identifikaci mluvčích podle jejich jedinečných vlastností hlasu. Rozpoznávání mluvčího slouží k zodpovězení otázky "kdo se mluví?". | Ano | [Ano](https://docs.microsoft.com/rest/api/speakerrecognition/) |


[!INCLUDE [TLS 1.2 enforcement](../../../includes/cognitive-services-tls-announcement.md)]

## <a name="try-the-speech-service"></a>Vyzkoušejte službu pro rozpoznávání řeči

Nabízíme rychlé zprovoznění v nejoblíbenějších programovacích jazycích, z nichž každá je navržena tak, aby používala kód za méně než 10 minut. Tato tabulka obsahuje nejoblíbenější rychlé starty pro jednotlivé funkce. Pomocí levé navigační navigace můžete prozkoumat další jazyky a platformy.

| Převod řeči na text (SDK) | Převod textu na řeč (SDK) | Překlad (SDK) |
|----------------------|----------------------|-------------------|
| [Rozpoznávání řeči ze zvukového souboru](quickstarts/speech-to-text-from-file.md) | [Syntéza řeči do zvukového souboru](quickstarts/text-to-speech-audio-file.md) | [Přeložit řeč na text](quickstarts/translate-speech-to-text.md) |
| [Rozpoznávání řeči pomocí mikrofonu](quickstarts/speech-to-text-from-microphone.md) | [Syntéza řeči do reproduktoru](quickstarts/text-to-speech.md) | [Překlad řeči do několika cílových jazyků](quickstarts/translate-speech-to-text-multiple-languages.md) |
| [Rozpoznávání řeči uložené v úložišti objektů blob](quickstarts/from-blob.md) | [Asynchronní syntéza řeči dlouhého formátu](quickstarts/text-to-speech/async-synthesis-long-form-audio.md) | [Překlad řeči na řeč](quickstarts/translate-speech-to-speech.md) |

> [!NOTE]
> Převod řeči na text a převod textu na řeč má také koncové body REST a přidružené rychlé starty.

Až budete mít možnost používat službu rozpoznávání řeči, vyzkoušejte náš kurz, který vás seznámí s postupem rozpoznávání řeči pomocí sady Speech SDK a LUIS.

- [Kurz: rozpoznávání záměrů z řeči pomocí sady Speech SDK a LUIS, C #](how-to-recognize-intents-from-speech-csharp.md)
- [Kurz: hlasový vstup pro robota pomocí sady Speech SDK, C #](tutorial-voice-enable-your-bot-speech-sdk.md)
- [Kurz: sestavení aplikace v baňce pro překlad textu, analýza mínění a syntetizace přeloženého textu na řeč, REST](https://docs.microsoft.com/azure/cognitive-services/translator/tutorial-build-flask-app-translation-synthesis?toc=%2fazure%2fcognitive-services%2fspeech-service%2ftoc.json&bc=%2fazure%2fcognitive-services%2fspeech-service%2fbreadcrumb%2ftoc.json&toc=%2Fen-us%2Fazure%2Fcognitive-services%2Fspeech-service%2Ftoc.json&bc=%2Fen-us%2Fazure%2Fbread%2Ftoc.json)

## <a name="get-sample-code"></a>Získání vzorového kódu

Vzorový kód je k dispozici na GitHubu pro službu rozpoznávání řeči. Tyto ukázky se týkají běžných scénářů, jako je čtení zvuku ze souboru nebo datového proudu, průběžné a jednorázové rozpoznávání a práce s vlastními modely. Pomocí těchto odkazů můžete zobrazit ukázky sady SDK a REST:

- [Ukázky převodu řeči na text, převod textu na řeč a rozpoznávání řeči (SDK)](https://github.com/Azure-Samples/cognitive-services-speech-sdk)
- [Ukázky pro dávková přepisy (REST)](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/batch)
- [Ukázky převodu textu na řeč (REST)](https://github.com/Azure-Samples/Cognitive-Speech-TTS)
- [Ukázky hlasového asistenta (SDK)](https://aka.ms/csspeech/samples)

## <a name="customize-your-speech-experience"></a>Přizpůsobení prostředí pro rozpoznávání řeči

Služba Speech funguje dobře s integrovanými modely, ale možná budete chtít další přizpůsobení a vyladit prostředí pro svůj produkt nebo prostředí. Možnosti přizpůsobení jsou v rozsahu od ladění akustického modelu až po jedinečná hlasová písma pro vaši značku.

| Speech Service | Platforma | Description |
| -------------- | -------- | ----------- |
| Řeč na text | [Custom Speech](https://aka.ms/customspeech) | Modely rozpoznávání řeči můžete přizpůsobit vašim potřebám a dostupným datům. Překonání překážek rozpoznávání řeči, například mluveného stylu, slovníku a hluku na pozadí. |
| Převod textu na řeč | [Vlastní hlas](https://aka.ms/customvoice) | Vytvořte si rozpoznatelný jedinečný hlas vašich aplikací pro převod textu na řeč pomocí vašich dostupných dat o hlasu. Hlasové výstupy můžete ještě více ladit úpravou sady hlasových parametrů. |

## <a name="reference-docs"></a>Referenční dokumenty

- [Speech SDK](speech-sdk-reference.md)
- [Speech Devices SDK](speech-devices-sdk.md)
- [REST API: Převod řeči na text](rest-speech-to-text.md)
- [REST API: převod textu na řeč](rest-text-to-speech.md)
- [REST API: přepis a přizpůsobení Batch](https://westus.cris.ai/swagger/ui/index)

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Získejte zdarma klíč předplatného služby Speech](get-started.md)
