---
title: Vlastní hlasová – první virtuální asistenti (Preview) – služba Speech
titleSuffix: Azure Cognitive Services
description: Přehled funkcí, možností a omezení pro vlastní virtuální asistenty hlasu pomocí kanálu Direct line Speech v rozhraní robot a sady Cognitive Services Speech Software Development Kit (SDK).
services: cognitive-services
author: trrwilson
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: travisw
ms.openlocfilehash: f77a95af1edcd37043ea1e067560577140a81a04
ms.sourcegitcommit: 5d6c8231eba03b78277328619b027d6852d57520
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/13/2019
ms.locfileid: "68967591"
---
# <a name="about-custom-voice-first-virtual-assistants-preview"></a>O vlastních hlasech pro virtuální asistenty ve verzi Preview

Vlastní virtuální asistenti využívající Azure Speech Services umožňují vývojářům vytvářet pro své aplikace a prostředí přirozené rozhraní pro konverzaci podobné člověku. Kanál pro přímý přenos řeči platformy robota tyto možnosti vylepšuje tím, že poskytuje koordinovaný a orchestrující vstupní bod na kompatibilním robotovi, který umožňuje hlas v, komunikaci hlasu s nízkou latencí a vysokou spolehlivostí. Tyto roboty můžou pro interakci v přirozeném jazyce použít Language Understanding (LUIS) od Microsoftu. K přímému line Speech přistupovala zařízení pomocí sady Speech Software Development Kit (SDK).

   ![Koncepční diagram toku služby orchestrace zpracování textu v přímém řádku](media/voice-first-virtual-assistants/overview.png "Tok kanálu řeči")


Přímý line Speech a jeho přidružená funkce pro vlastní virtuální asistenty hlasu jsou ideálním doplňkem k [řešení Virtual Assistant a šabloně organizace](https://docs.microsoft.com/azure/bot-service/bot-builder-enterprise-template-overview). I když je přímým line Speech možné pracovat s jakýmkoli kompatibilním robotem, poskytují tyto prostředky opakovaně použitelný směrný plán pro vysoce kvalitní konverzační prostředí a také společné podpůrné dovednosti a modely pro rychlé zahájení práce.


## <a name="core-features"></a>Základní funkce

| Kategorie | Funkce |
|----------|----------|
|[Vlastní slovo Wake](speech-devices-sdk-create-kws.md) | Uživatelům můžete povolit konverzaci pomocí roboty pomocí vlastního klíčového slova "Hey contoso". Tato úloha se provádí pomocí vlastního modulu Wordu Wake v sadě Speech SDK, který se dá nakonfigurovat pomocí vlastního slova Wake [, které tady můžete vygenerovat](speech-devices-sdk-create-kws.md). Přímý spojnicový řeč zahrnuje ověřování v případě probuzení na straně služby, které zlepšuje přesnost aktivace funkce Wake Word, oproti samotnému zařízení.
|[Převod řeči na text](speech-to-text.md) | Přímý spojnicový řeč zahrnuje přepis zvuku v reálném čase s rozpoznaným textem pomocí převodu [řeči na text](speech-to-text.md) z Azure Speech Services. Tento text je k dispozici pro robot i klientskou aplikaci, protože je přepisu.
|[Převod textu na řeč](text-to-speech.md) | Textové odpovědi z robota se budou syntetizovat pomocí převodu [textu na řeč](text-to-speech.md) z Azure Speech Services. Tato syntéza pak bude zpřístupněna klientské aplikaci jako zvukový datový proud. Microsoft nabízí možnost vytvářet vlastní a vysoce kvalitní hlas neuronové TTS, který poskytuje hlas k vaší značce, a dozví se, jak [nás kontaktovat](mailto:mstts@microsoft.com).
|[Přímý line – řeč](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-directlinespeech) | V rámci kanálu v rámci rozhraní bot je přímým vstupem na řeč umožněno hladké a bezproblémové propojení mezi klientskou aplikací, kompatibilní robotem a možnostmi služby Azure Speech Services. Další informace o konfiguraci robota pro použití kanálu přímým line Speech najdete na [stránce v dokumentaci k rozhraní bot Framework](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-directlinespeech).

## <a name="get-started-with-virtual-assistants"></a>Začínáme s virtuálními asistenty

Nabízíme rychlé starty, které jsou navržené tak, aby běžely kód za méně než 10 minut. Tato tabulka obsahuje seznam rychlých startů pro virtuální asistenty, které jsou uspořádány podle jazyka.

| Rychlý start | Platforma | API – referenční informace |
|------------|----------|---------------|
| C#, UWP | Windows | [Procházet](https://aka.ms/csspeech/csharpref) |
| Java | Windows, macOS, Linux | [Procházet](https://aka.ms/csspeech/javaref) |
| Java | Android | [Procházet](https://aka.ms/csspeech/javaref) |

## <a name="sample-code"></a>Ukázka kódu

Vzorový kód pro vytvoření virtuálního asistenta prvního hlasu je k dispozici na GitHubu. Tyto ukázky zahrnují klientskou aplikaci pro připojení k robotovi v několika oblíbených programovacích jazycích.

* [Ukázka hlasu prvního virtuálního pomocníka (SDK)](https://aka.ms/csspeech/samples)
* [Kurz: Hlasový vstup vám umožní pomocí sady Speech SDK,C#](tutorial-voice-enable-your-bot-speech-sdk.md)

## <a name="tutorial"></a>Kurz
V tomto kurzu se naučíte, jak pomocí hlasových sad pro [rozpoznávání řeči a přímého kanálu s přímým přístupem ke svým robotům zapnout hlas](tutorial-voice-enable-your-bot-speech-sdk.md).

## <a name="customization"></a>Přizpůsobení

Pomocí služby Azure Speech Services můžete využít celou řadu možností přizpůsobení, které jsou k dispozici pro [Převod řeči na text](speech-to-text.md), [Převod textu na řeč](text-to-speech.md)a [vlastní výběr vlastního klíčového slova](speech-devices-sdk-create-kws.md).

> [!NOTE]
> Možnosti přizpůsobení se liší podle jazyka nebo národního prostředí (viz [podporované jazyky](supported-languages.md)).

## <a name="reference-docs"></a>Referenční dokumenty

* [Speech SDK](speech-sdk-reference.md)
* [Azure Bot Service](https://docs.microsoft.com/azure/bot-service/?view=azure-bot-service-4.0)

## <a name="next-steps"></a>Další kroky

* [Získat klíč předplatného služby Speech Services zdarma](get-started.md)
* [Získat sadu Speech SDK](speech-sdk.md)
* [Vytvoření a nasazení základního chatbota](https://docs.microsoft.com/azure/bot-service/bot-builder-tutorial-basic-deploy?view=azure-bot-service-4.0)
* [Získat řešení a šablonu společnosti Virtual Assistant](https://github.com/Microsoft/AI)
