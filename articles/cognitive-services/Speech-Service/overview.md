---
title: Co je služba Speech?
titleSuffix: Azure Cognitive Services
description: Služba Řeč je sjednocení převodu řeči na text, převod textu na řeč a překladu řeči do jednoho předplatného Azure. Přidejte řeč do svých aplikací, nástrojů a zařízení pomocí sad Speech SDK, sad SDK pro řečová zařízení nebo rest API.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: overview
ms.date: 03/12/2020
ms.author: trbye
ms.openlocfilehash: 464e21caf3105b12f0fbf44dc2aa3674a252d3c6
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81401024"
---
# <a name="what-is-the-speech-service"></a>Co je služba Speech?

Služba Řeč je sjednocení řeči na text, převod textu na řeč a překlad řeči do jednoho předplatného Azure. Je snadné řeči povolit vaše aplikace, nástroje a zařízení s [řeči SDK](speech-sdk-reference.md), [řeči zařízení SDK](https://aka.ms/sdsdk-quickstart)nebo [REST API](rest-apis.md).

> [!IMPORTANT]
> Služba Řeč nahradila rozhraní API pro řeč Bingu a překladač řeči. Pokyny pro migraci najdete _v tématu Návody > migrace._

Tyto funkce tvoří službu Řeči. Pomocí odkazů v této tabulce se dozvíte více o běžných případech použití pro jednotlivé funkce nebo procházet odkaz na rozhraní API.

| Služba | Funkce | Popis | Sada SDK | REST |
|---------|---------|-------------|-----|------|
| [Převod řeči na text](speech-to-text.md) | Převod řeči na text v reálném čase | Převod řeči na text přepisuje nebo překládá zvukové proudy nebo místní soubory na text v reálném čase, který mohou vaše aplikace, nástroje nebo zařízení využívat nebo zobrazovat. Pomocí funkce převod řeči na text s [jazykovou úpravou (LUIS)](https://docs.microsoft.com/azure/cognitive-services/luis/) můžete odvodit záměry uživatelů z přepisované řeči a jednat s hlasovými příkazy. | [Ano](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) | [Ano](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis) |
| | [Dávková řeč na text](batch-transcription.md) | Dávková funkce Převod řeči na text umožňuje asynchronní přepis řeči na text velkých objemů zvukových dat řeči uložených ve službě Azure Blob Storage. Kromě převodu zvuku řeči na text umožňuje dávková řeč na text také diarizaci a analýzu mínění. | Ne | [Ano](https://westus.cris.ai/swagger/ui/index) |
| | [Konverzace na více zařízeních](multi-device-conversation.md) | Připojte více zařízení nebo klientů v konverzaci k odesílání zpráv založených na řeči nebo textu se snadnou podporou přepisu a překladu| Ano | Ne |
| | [Přepis konverzace](conversation-transcription-service.md) | Umožňuje rozpoznávání řeči v reálném čase, identifikaci mluvčího a diarizaci. Je ideální pro přepis osobních schůzek se schopností rozlišovat řečníky. | Ano | Ne |
| | [Vytvořit vlastní modely řeči](#customize-your-speech-experience) | Pokud používáte převod řeči na text pro rozpoznávání a přepis v jedinečném prostředí, můžete vytvořit a trénovat vlastní modely akustické, jazykové a výslovnosti, které řeší okolní hluk nebo slovní zásobu specifickou pro dané odvětví. | Ne | [Ano](https://westus.cris.ai/swagger/ui/index) |
| [Text-to-speech](text-to-speech.md) | Převod textu na řeč | Převod textu na řeč převádí vstupní text na syntetizovanou řeč podobné člověku pomocí [jazyka SSML (Speech Synthesis Markup Language).](speech-synthesis-markup.md) Vyberte si ze standardních a neurálních hlasů (viz [Jazyková podpora).](language-support.md) | [Ano](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) | [Ano](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis) |
| | [Vytváření vlastních zvuků](#customize-your-speech-experience) | Vytvořte vlastní hlasová písma jedinečná pro vaši značku nebo produkt. | Ne | [Ano](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis) |
| [Překlad řeči](speech-translation.md) | Překlad řeči | Překlad řeči umožňuje vícejazyčný překlad řeči do aplikací, nástrojů a zařízení v reálném čase. Tuto službu použijte pro překlad řeči na řeč a převod řeči na text. | [Ano](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) | Ne |
| [Hlasoví asistenti](voice-assistants.md) | Hlasoví asistenti | Hlasoví asistenti využívající službu Řeč umožňuje vývojářům vytvářet přirozené konverzační rozhraní podobné člověku pro jejich aplikace a prostředí. Služba hlasového asistenta poskytuje rychlou a spolehlivou interakci mezi zařízením a implementací asistenta, která používá kanál přímé řeči rozhraní Bot Framework nebo integrovanou službu vlastních příkazů (Preview) pro dokončení úkolu. | [Ano](voice-assistants.md) | Ne |


[!INCLUDE [TLS 1.2 enforcement](../../../includes/cognitive-services-tls-announcement.md)]

## <a name="try-the-speech-service"></a>Vyzkoušejte službu Řeč

Nabízíme rychlé starty v nejpopulárnějších programovacích jazycích, z nichž každý je navržen tak, aby vám spouštěl kód za méně než 10 minut. Tato tabulka obsahuje nejoblíbenější rychlé starty pro každou funkci. Pomocí navigace na levé straně můžete prozkoumat další jazyky a platformy.

| Převod řeči na text (SDK) | Převod textu na řeč (SDK) | Překlad (SDK) |
|----------------------|----------------------|-------------------|
| [Rozpoznávání řeči ze zvukového souboru](quickstarts/speech-to-text-from-file.md) | [Syntéza řeči do zvukového souboru](quickstarts/text-to-speech-audio-file.md) | [Překlad řeči na text](quickstarts/translate-speech-to-text.md) |
| [Rozpoznávání řeči pomocí mikrofonu](quickstarts/speech-to-text-from-microphone.md) | [Syntéza řeči do reproduktoru](quickstarts/text-to-speech.md) | [Překlad řeči do několika cílových jazyků](quickstarts/translate-speech-to-text-multiple-languages.md) |
| [Rozpoznávání řeči uložené v úložišti objektů blob](quickstarts/from-blob.md) | [Asynchronní syntéza řeči dlouhého formátu](quickstarts/text-to-speech/async-synthesis-long-form-audio.md) | [Překlad řeči na řeč](quickstarts/translate-speech-to-speech.md) |

> [!NOTE]
> Řeč na text a převod textu na řeč mají také koncové body REST a přidružené rychlé starty.

Poté, co jste měli možnost používat službu řeči, zkuste náš kurz, který vás naučí, jak rozpoznat záměry z řeči pomocí sady Speech SDK a LUIS.

- [Kurz: Rozpoznání záměrů z řeči pomocí sady Speech SDK a LUIS, C #](how-to-recognize-intents-from-speech-csharp.md)
- [Výuka: Hlas povolit bot s řeči SDK, C #](tutorial-voice-enable-your-bot-speech-sdk.md)
- [Kurz: Sestavte aplikaci Flask pro překlad textu, analýzu sentimentu a syntézu přeloženého textu do řeči, REST](https://docs.microsoft.com/azure/cognitive-services/translator/tutorial-build-flask-app-translation-synthesis?toc=%2fazure%2fcognitive-services%2fspeech-service%2ftoc.json&bc=%2fazure%2fcognitive-services%2fspeech-service%2fbreadcrumb%2ftoc.json&toc=%2Fen-us%2Fazure%2Fcognitive-services%2Fspeech-service%2Ftoc.json&bc=%2Fen-us%2Fazure%2Fbread%2Ftoc.json)

## <a name="get-sample-code"></a>Získání vzorového kódu

Ukázkový kód je k dispozici na GitHubu pro službu Řeč. Tyto ukázky zahrnují běžné scénáře, jako je čtení zvuku ze souboru nebo datového proudu, nepřetržité rozpoznávání a rozpoznávání jedním výstřelem a práce s vlastními modely. Tyto odkazy slouží k zobrazení ukázek sady SDK a REST:

- [Ukázky převodu řeči na text, převod textu na řeč a překladřeči (SDK)](https://github.com/Azure-Samples/cognitive-services-speech-sdk)
- [Vzorky přepisu dávky (REST)](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/batch)
- [Ukázky převodu textu na řeč (REST)](https://github.com/Azure-Samples/Cognitive-Speech-TTS)
- [Ukázky hlasového asistenta (SDK)](https://aka.ms/csspeech/samples)

## <a name="customize-your-speech-experience"></a>Přizpůsobení prostředí pro řeč

Služba Řeč funguje dobře s vestavěnými modely, ale můžete chtít dále přizpůsobit a vyladit prostředí pro váš produkt nebo prostředí. Možnosti přizpůsobení sahají od akustického ladění modelů až po jedinečná hlasová písma pro vaši značku.

| Speech Service | Platforma | Popis |
| -------------- | -------- | ----------- |
| Řeč na text | [Vlastní řeč](https://aka.ms/customspeech) | Přizpůsobte modely rozpoznávání řeči svým potřebám a dostupným datům. Překonávejte překážky rozpoznávání řeči, jako je styl mluvení, slovní zásoba a hluk na pozadí. |
| Převod textu na řeč | [Vlastní hlas](https://aka.ms/customvoice) | Vytvořte si rozpoznatelný jedinečný hlas vašich aplikací pro převod textu na řeč pomocí vašich dostupných dat o hlasu. Hlasové výstupy můžete dále doladit úpravou sady hlasových parametrů. |

## <a name="reference-docs"></a>Referenční dokumenty

- [Speech SDK](speech-sdk-reference.md)
- [Speech Devices SDK](speech-devices-sdk.md)
- [ROZHRANÍ REST API: Převod řeči na text](rest-speech-to-text.md)
- [ROZHRANÍ REST API: Převod textu na řeč](rest-text-to-speech.md)
- [ROZHRANÍ REST API: Dávkový přepis a přizpůsobení](https://westus.cris.ai/swagger/ui/index)

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Získejte zdarma klíč předplatného služby Speech](get-started.md)
