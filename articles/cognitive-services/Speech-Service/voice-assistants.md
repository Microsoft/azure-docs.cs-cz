---
title: Hlasoví asistenti - Řečová služba
titleSuffix: Azure Cognitive Services
description: Přehled funkcí, funkcí a omezení pro hlasové asistenty pomocí sady Speech Software Development Kit (SDK).
services: cognitive-services
author: trrwilson
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: travisw
ms.openlocfilehash: 9219c9a72ce9e1cfba3504b0b8e16ade77f8a5e5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79369892"
---
# <a name="what-is-a-voice-assistant"></a>Co je hlasový asistent?

Hlasoví asistenti využívající službu Řeč umožňuje vývojářům vytvářet přirozené konverzační rozhraní podobné lidem pro jejich aplikace a prostředí.

Služba hlasového asistenta poskytuje rychlou a spolehlivou interakci mezi zařízením a implementací asistenta, která používá buď (1) kanál přímé řeči rozhraní Bot Framework, nebo (2) službu integrované vlastní příkazy (Preview) pro dokončení úkolu.

Aplikace se připojují ke službě hlasového asistenta pomocí sady Speech Software Development Kit (SDK).

   ![Koncepční diagram toku služby orchestrace hlasového asistenta](media/voice-assistants/overview.png "Tok hlasového asistenta")

## <a name="choosing-an-assistant-solution"></a>Výběr pomocného řešení

Prvním krokem k vytvoření hlasového asistenta je rozhodnout, co má dělat. Služba Řeč poskytuje několik doplňkových řešení pro vytváření interakcí asistenta. Ať už chcete flexibilitu a univerzálnost, kterou poskytuje kanál Přímé řeči rozhraní Bot [Framework,](direct-line-speech.md) nebo jednoduchost [vlastních příkazů (preview)](custom-commands.md) pro jednoduché scénáře, výběr správných nástrojů vám pomůže začít.

| Jestli chceš... | Pak zvažte... | Například... |
|-------------------|------------------|----------------|
|Otevřená konverzace s robustní integrací dovedností a plnou kontrolou nasazení | Kanál [přímé řeči](direct-line-speech.md) rozhraní Bot Framework | <ul><li>"Musím jet do Seattlu"</li><li>"Jakou pizzu si můžu objednat?"</li></ul>
|Řízení a řízení nebo konverzace zaměřená na úkoly se zjednodušeným vytvářením a hostováním | [Vlastní příkazy (náhled)](custom-commands.md) | <ul><li>"Rozsviňte stropní světlo"</li><li>"Ať je o 5 stupňů tepleji"</ul>

Jako nejlepší výchozí volbu doporučujeme [přímou řeč,](direct-line-speech.md) pokud si ještě nejste jisti, co má asistent zpracovat. Nabízí integraci s bohatou sadou nástrojů a pomůcek pro vytváření, jako je [řešení virtuálníasistentky a podniková šablona](https://docs.microsoft.com/azure/bot-service/bot-builder-enterprise-template-overview) a [služba QnA Maker,](https://docs.microsoft.com/azure/cognitive-services/QnAMaker/Overview/overview) aby bylo schopno stavět na běžných vzorcích a využívat stávající zdroje znalostí.

[Vlastní příkazy (Preview)](custom-commands.md) poskytuje zjednodušené vytváření a hostování speciálně přizpůsobené pro scénáře příkazů a řízení přirozeného jazyka.

   ![Porovnání pomocných řešení](media/voice-assistants/assistant-solution-comparison.png "Porovnání pomocných řešení")

## <a name="core-features"></a>Základní funkce

Ať už pro vytvoření interakcí asistenta zvolíte [přímé přímé řeči](direct-line-speech.md) nebo vlastní [příkazy (náhled),](custom-commands.md) můžete pomocí bohaté sady funkcí přizpůsobení přizpůsobit asistenta značce, produktu a osobnosti.

| Kategorie | Funkce |
|----------|----------|
|[Vlastní klíčové slovo](speech-devices-sdk-create-kws.md) | Uživatelé mohou zahájit konverzaci s asistenty pomocí vlastního klíčového slova, jako je "Hey Contoso". Aplikace to provede pomocí vlastního modulu klíčových slov v sadě Speech SDK, který lze nakonfigurovat pomocí vlastního klíčového [slova, které můžete vygenerovat zde](speech-devices-sdk-create-kws.md). Hlasoví asistenti mohou pomocí ověření klíčových slov na straně služby zlepšit přesnost aktivace klíčového slova (oproti samotnému zařízení).
|[Řeč na text](speech-to-text.md) | Hlasoví asistenti převádějí zvuk v reálném čase na rozpoznaný text pomocí [funkce Převod řeči na text](speech-to-text.md) ze služby Řeč. Tento text je k dispozici, jak je přepisován, jak pro implementaci asistenta a klientské aplikace.
|[Převod textu na řeč](text-to-speech.md) | Textové odpovědi od asistenta jsou syntetizovány pomocí [převodu textu na řeč](text-to-speech.md) ze služby Řeč. Tato syntéza je pak k dispozici pro klientskou aplikaci jako zvukový datový proud. Společnost Microsoft nabízí možnost vytvořit si vlastní, vysoce kvalitní hlas TTS, který dává hlas vaší značce. Chcete-li se dozvědět více, [kontaktujte nás](mailto:mstts@microsoft.com).

## <a name="getting-started-with-voice-assistants"></a>Začínáme s hlasovými asistenty

Nabízíme rychlé starty navržené tak, abyste mohli spustit kód za méně než 10 minut. Tato tabulka obsahuje seznam rychlých startů hlasového asistenta uspořádaných podle jazyka.

| Rychlý start | Platforma | referenční dokumentace k rozhraní API |
|------------|----------|---------------|
| C#, UWP | Windows | [Procházet](https://aka.ms/csspeech/csharpref) |
| Java | Windows, macOS, Linux | [Procházet](https://aka.ms/csspeech/javaref) |
| Java | Android | [Procházet](https://aka.ms/csspeech/javaref) |

## <a name="sample-code"></a>Ukázka kódu

Ukázkový kód pro vytvoření hlasového asistenta je k dispozici na GitHubu. Tyto ukázky pokrývají klientskou aplikaci pro připojení k asistentovi v několika oblíbených programovacích jazycích.

* [Ukázky hlasového asistenta (SDK)](https://aka.ms/csspeech/samples)
* [Kurz: Hlasové povolení asistenta pomocí sady Speech SDK, C #](tutorial-voice-enable-your-bot-speech-sdk.md)

## <a name="tutorial"></a>Kurz

Návod, jak [hlasové povolení asistenta pomocí kanálu Speech SDK a Direct Line Speech](tutorial-voice-enable-your-bot-speech-sdk.md).

## <a name="customization"></a>Přizpůsobení

Hlasoví asistenti vytvoření pomocí služby Řeč mohou používat celou řadu možností přizpůsobení, které jsou k dispozici pro [převod řeči na text](speech-to-text.md), převod textu na [řeč](text-to-speech.md)a vlastní [výběr klíčových slov](speech-devices-sdk-create-kws.md).

> [!NOTE]
> Možnosti vlastního nastavení se liší podle jazyka/národního prostředí (viz [Podporované jazyky](supported-languages.md)).

## <a name="reference-docs"></a>Referenční dokumenty

* [Speech SDK](speech-sdk-reference.md)
* [Azure Bot Service](https://docs.microsoft.com/azure/bot-service/?view=azure-bot-service-4.0)

## <a name="next-steps"></a>Další kroky

* [Získejte zdarma klíč předplatného služby Speech](get-started.md)
* [Získání sady SDK pro rozpoznávání řeči](speech-sdk.md)
* [Další informace o vlastních příkazech (náhled)](custom-commands.md)
* [Další informace o přímé řeči linky](direct-line-speech.md)
