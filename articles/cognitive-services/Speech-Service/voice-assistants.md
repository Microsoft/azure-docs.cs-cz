---
title: Hlasová asistenti – služba pro rozpoznávání řeči
titleSuffix: Azure Cognitive Services
description: Přehled funkcí, možností a omezení pro hlasové asistenty pomocí sady Speech Software Development Kit (SDK).
services: cognitive-services
author: trrwilson
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 11/05/2019
ms.author: travisw
ms.openlocfilehash: 403768bc81b476dd144d5e4496f3501a3001da8a
ms.sourcegitcommit: 8e31a82c6da2ee8dafa58ea58ca4a7dd3ceb6132
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/19/2019
ms.locfileid: "74195942"
---
# <a name="about-voice-assistants"></a>O hlasových asistentcích

Hlasové asistenti, kteří používají službu Azure Speech Services, umožňují vývojářům vytvářet v aplikacích a prostředích přirozené rozhraní pro konverzaci podobné člověku.

Služba hlasového asistenta poskytuje rychlou a spolehlivou interakci mezi zařízením a implementací pomocníka, která používá (1) kanál pro přímý přenos řeči platformy bot nebo (2) službu Integrated Custom Command (Preview) Service pro dokončení úkolu.

Aplikace se připojují ke službě hlasového asistenta pomocí sady Speech Software Development Kit (SDK).

   ![Koncepční diagram toku služby orchestrace hlasového asistenta](media/voice-assistants/overview.png "Tok hlasového asistenta")

## <a name="choosing-an-assistant-solution"></a>Výběr řešení pomocníka

Prvním krokem k vytvoření hlasového asistenta je rozhodování o tom, co by mělo dělat. Služba Azure Speech Services poskytuje několik doplňujících řešení pro vytváření interakcí asistentů. Bez ohledu na to, jestli chcete mít flexibilitu a univerzálnost, které poskytuje kanál pro přímý přístup k [přímému line](direct-line-speech.md) rozhraní bot Framework, nebo jednoduché [vlastní příkazy (Preview)](custom-commands.md) pro jednoduché scénáře, můžete začít tím, že vyberete správné nástroje.

| Pokud chcete... | Pak zvažte... | Například... |
|-------------------|------------------|----------------|
|Nedokončená konverzace s robustními integrací dovedností a úplným řízením nasazení | Kanál s [přímým přístupem](direct-line-speech.md) k rozhraní robot Framework | <ul><li>"Potřebuji přejít na Seattle"</li><li>"Jaký druh pizza můžu objednat?"</li></ul>
|Příkazy a ovládací prvky nebo konverzace orientované na úlohy se zjednodušeným vytvářením a hostováním | [Vlastní příkazy (Preview)](custom-commands.md) | <ul><li>"Zapnout režijní světlo"</li><li>"Udělejte si 5 stupňů zahřívání"</ul>

Pokud si zatím nejste jistí, co byste chtěli zvládnout, doporučujeme vám jako nejlepší výchozí volbu použít [přímý vstup hlasu](direct-line-speech.md) . Nabízí integraci s bohatou sadou nástrojů a pomůcek pro vytváření obsahu, jako je [řešení Virtual Assistant a podniková šablona](https://docs.microsoft.com/azure/bot-service/bot-builder-enterprise-template-overview) a [Služba QnA maker](https://docs.microsoft.com/azure/cognitive-services/QnAMaker/Overview/overview) pro vytváření běžných vzorů a používání stávajících zdrojů znalostí.

[Vlastní příkazy (Preview)](custom-commands.md) poskytují zjednodušené vývojové a hostující prostředí, které je specifické pro scénáře příkazů a ovládacích prvků v přirozeném jazyce.

   ![Porovnání řešení pomocníka](media/voice-assistants/assistant-solution-comparison.png "Porovnání řešení pomocníka")

## <a name="core-features"></a>Základní funkce

Bez ohledu na to, jestli pro vytvoření interakce asistenta zvolíte možnost [přímé line Speech](direct-line-speech.md) nebo [vlastní příkazy (Preview)](custom-commands.md) , můžete použít bohatou sadu funkcí přizpůsobení a přizpůsobit svůj asistent na svou značku, produkt a osobnost.

| Kategorie | Funkce |
|----------|----------|
|[Vlastní klíčové slovo](speech-devices-sdk-create-kws.md) | Uživatelé můžou začít konverzace s asistenty pomocí vlastního klíčového slova "Hey contoso". V aplikaci se jedná o vlastní modul klíčových slov v sadě Speech SDK, který lze konfigurovat pomocí klíčového slova vlastní [, které zde můžete vygenerovat](speech-devices-sdk-create-kws.md). Hlasové asistenti mohou použít ověřování pomocí klíčového slova na straně služby ke zlepšení přesnosti aktivace klíčového slova (oproti samotnému zařízení).
|[Převod řeči na text](speech-to-text.md) | Hlasový asistent převádí zvuk v reálném čase na rozpoznaný text pomocí převodu [řeči na text](speech-to-text.md) z Azure Speech Services. Tento text je jako přepisu k dispozici jak pro vaši pomoc, tak pro klientské aplikace.
|[Převod textu na řeč](text-to-speech.md) | Textové odpovědi od vašeho pomocníka jsou syntetizované pomocí převodu [textu na řeč](text-to-speech.md) z Azure Speech Services. Tato syntéza je pak zpřístupněna klientské aplikaci jako zvukový datový proud. Microsoft nabízí možnost vytvářet vlastní a vysoce kvalitní hlas neuronové TTS, který poskytuje hlas k vaší značce. Pokud se chcete dozvědět víc, [kontaktujte nás](mailto:mstts@microsoft.com).

## <a name="getting-started-with-voice-assistants"></a>Začínáme se hlasovými asistenty

Nabízíme rychlé starty, které jsou navržené tak, aby běžely kód za méně než 10 minut. Tato tabulka obsahuje seznam rychlých startů hlasového asistenta uspořádaných podle jazyka.

| Rychlý start | Platforma | API – referenční informace |
|------------|----------|---------------|
| C#, UWP | Windows | [Hlíží](https://aka.ms/csspeech/csharpref) |
| Java | Windows, macOS, Linux | [Hlíží](https://aka.ms/csspeech/javaref) |
| Java | Android | [Hlíží](https://aka.ms/csspeech/javaref) |

## <a name="sample-code"></a>Ukázka kódu

Vzorový kód pro vytvoření hlasového asistenta je k dispozici na GitHubu. Tyto ukázky zahrnují klientskou aplikaci pro připojení k vašemu asistentovi v několika oblíbených programovacích jazycích.

* [Ukázky hlasového asistenta (SDK)](https://aka.ms/csspeech/samples)
* [Kurz: hlasový asistent pro sadu Speech SDK,C#](tutorial-voice-enable-your-bot-speech-sdk.md)

## <a name="tutorial"></a>Kurz

Kurz o tom, jak [pomocí hlasu Povolit asistenta pomocí sady Speech SDK a přímého kanálu pro rozpoznávání řeči](tutorial-voice-enable-your-bot-speech-sdk.md).

## <a name="customization"></a>Přizpůsobení

Hlasové asistenti sestavené pomocí Azure Speech Services můžou používat celou škálu možností přizpůsobení, které jsou k dispozici pro [Převod řeči na text](speech-to-text.md), [Převod textu na řeč](text-to-speech.md)a [vlastní výběr klíčového slova](speech-devices-sdk-create-kws.md).

> [!NOTE]
> Možnosti přizpůsobení se liší podle jazyka nebo národního prostředí (viz [podporované jazyky](supported-languages.md)).

## <a name="reference-docs"></a>Referenční dokumenty

* [Speech SDK](speech-sdk-reference.md)
* [Azure Bot Service](https://docs.microsoft.com/azure/bot-service/?view=azure-bot-service-4.0)

## <a name="next-steps"></a>Další kroky

* [Získat klíč předplatného služby Speech Services zdarma](get-started.md)
* [Získat sadu Speech SDK](speech-sdk.md)
* [Další informace o vlastních příkazech (Preview)](custom-commands.md)
* [Další informace o funkci Direct line Speech](direct-line-speech.md)
