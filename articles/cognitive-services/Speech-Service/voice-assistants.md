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
ms.date: 03/11/2020
ms.author: travisw
ms.openlocfilehash: fc85eec008ef099d63d538e4871a1a84573f5a18
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "98790441"
---
# <a name="what-is-a-voice-assistant"></a>Co je hlasový asistent?

Hlasový asistent využívající službu Speech umožňuje vývojářům vytvářet pro své aplikace a prostředí přirozené rozhraní pro konverzaci podobné člověku.

Služba hlasového asistenta poskytuje rychlou a spolehlivou interakci mezi zařízením a implementací pomocníka, která používá buď (1) [přímý hlasový hlas](direct-line-speech.md) (prostřednictvím Azure bot Service) pro přidání možností hlasu vašim robotyám, nebo (2) vlastních příkazů pro scénáře hlasových příkazů.

## <a name="choosing-an-assistant-solution"></a>Výběr řešení pomocníka

Prvním krokem k vytvoření hlasového asistenta je rozhodování o tom, co by mělo dělat. Služba rozpoznávání řeči poskytuje více a doplňková řešení pro vytváření interakcí pomocníka. Do flexibilního a flexibilního robota vytvořeného pomocí Azure Bot Service s kanálem [Direct line Speech](direct-line-speech.md) můžete přidat hlas a možnosti hlasu a využít jednoduchost vytváření [vlastních příkazů](custom-commands.md) pro jednoduché scénáře hlasových příkazů.

| Jestli chceš... | Pak zvažte... | Příklad: |
|-------------------|------------------|----------------|
|Nedokončená konverzace s robustními integrací dovedností a úplným řízením nasazení | Azure Bot Service robot s [přímým line Speech](direct-line-speech.md) Channel | <ul><li>"Potřebuji přejít na Seattle"</li><li>"Jaký druh pizza můžu objednat?"</li></ul>
|Hlasové příkazy nebo jednoduché konverzace orientované na úlohy se zjednodušeným vytvářením a hostováním | [Vlastní příkazy](custom-commands.md) | <ul><li>"Zapnout režijní světlo"</li><li>"Udělejte si 5 stupňů zahřívání"</li><li>Další ukázky [k dispozici](https://speech.microsoft.com/customcommands)</li></ul>

Pokud si zatím nejste jistí, co byste chtěli zvládnout, doporučujeme vám jako nejlepší výchozí volbu použít [přímý vstup hlasu](direct-line-speech.md) . Nabízí integraci s bohatou sadou nástrojů a pomůcek pro vytváření obsahu, jako je [řešení Virtual Assistant a podniková šablona](/azure/bot-service/bot-builder-enterprise-template-overview) a [Služba QnA maker](../qnamaker/overview/overview.md) pro vytváření běžných vzorů a používání stávajících zdrojů znalostí.

[Vlastní příkazy](custom-commands.md) usnadňují vytváření bohatě bohatých aplikací pro hlasové příkazy optimalizovaných pro prostředí s prvními interakcemi hlasu. Poskytuje jednotné prostředí pro vytváření, automatický hostující model a poměrně nižší složitost, což vám pomůže soustředit se na vytváření nejlepšího řešení pro scénáře hlasových příkazů.

   ![Porovnání řešení pomocníka](media/voice-assistants/assistant-solution-comparison.png "Porovnání řešení pomocníka")


## <a name="reference-architecture-for-building-a-voice-assistant-using-the-speech-sdk"></a>Referenční architektura pro sestavování hlasového asistenta pomocí sady Speech SDK

   ![Koncepční diagram toku služby orchestrace hlasového asistenta](media/voice-assistants/overview.png "Tok hlasového asistenta")

## <a name="core-features"></a>Základní funkce

Bez ohledu na to, jestli pro vytváření pomocných asistentů zvolíte [přímé line řeč](direct-line-speech.md) nebo [vlastní příkazy](custom-commands.md) , můžete použít bohatou sadu funkcí přizpůsobení a přizpůsobit svůj asistent na svou značku, produkt a osobnost.

| Kategorie | Funkce |
|----------|----------|
|[Vlastní klíčové slovo](./custom-keyword-basics.md) | Uživatelé můžou začít konverzace s asistenty pomocí vlastního klíčového slova "Hey contoso". V aplikaci se jedná o vlastní modul klíčových slov v sadě Speech SDK, který lze konfigurovat pomocí klíčového slova vlastní [, které zde můžete vygenerovat](./custom-keyword-basics.md). Hlasové asistenti mohou použít ověřování pomocí klíčového slova na straně služby ke zlepšení přesnosti aktivace klíčového slova (oproti samotnému zařízení).
|[Převod řeči na text](speech-to-text.md) | Hlasový asistent převádí zvuk v reálném čase na rozpoznaný text pomocí převodu [řeči na text](speech-to-text.md) ze služby Speech. Tento text je jako přepisu k dispozici jak pro vaši pomoc, tak pro klientské aplikace.
|[Převod textu na řeč](text-to-speech.md) | Textové odpovědi od vašeho asistenta se syntetizují pomocí převodu [textu na řeč](text-to-speech.md) od služby Speech. Tato syntéza je pak zpřístupněna klientské aplikaci jako zvukový datový proud. Microsoft nabízí možnost vytvářet vlastní a vysoce kvalitní hlas neuronové TTS, který poskytuje hlas k vaší značce. Pokud se chcete dozvědět víc, [kontaktujte nás](mailto:mstts@microsoft.com).

## <a name="getting-started-with-voice-assistants"></a>Začínáme se hlasovými asistenty

Nabízíme rychlé starty, které jsou navržené tak, aby běžely kód za méně než 10 minut. Tato tabulka obsahuje seznam rychlých startů hlasového asistenta uspořádaných podle jazyka.

* [Rychlý Start: Vytvoření vlastního hlasového asistenta pomocí přímého Lineového rozpoznávání](quickstarts/voice-assistants.md)
* [Rychlý Start: sestavení aplikace hlasových příkazů pomocí vlastních příkazů](quickstart-custom-commands-application.md)

## <a name="sample-code-and-tutorials"></a>Ukázka kódu a kurzů

Vzorový kód pro vytvoření hlasového asistenta je k dispozici na GitHubu. Tyto ukázky zahrnují klientskou aplikaci pro připojení k vašemu asistentovi v několika oblíbených programovacích jazycích.

* [Ukázky hlasového asistenta na GitHubu](https://github.com/Azure-Samples/Cognitive-Services-Voice-Assistant)
* [Kurz: hlasový asistent, který vychází z Azure Bot Service pomocí sady C# Speech SDK](tutorial-voice-enable-your-bot-speech-sdk.md)
* [Kurz: Vytvoření vlastní aplikace s příkazy pomocí jednoduchých hlasových příkazů](./how-to-develop-custom-commands-application.md)

## <a name="customization"></a>Přizpůsobení

Hlasové asistenti sestavené pomocí služby Azure Speech Services můžou využívat celou řadu možností přizpůsobení.

* [Custom Speech](./custom-speech-overview.md)
* [Vlastní hlas](how-to-custom-voice.md)
* [Vlastní klíčové slovo](custom-keyword-overview.md)

> [!NOTE]
> Možnosti přizpůsobení se liší podle jazyka nebo národního prostředí (viz [podporované jazyky](language-support.md)).

## <a name="next-steps"></a>Další kroky

* [Získejte zdarma klíč předplatného služby Speech](overview.md#try-the-speech-service-for-free)
* [Další informace o vlastních příkazech](custom-commands.md)
* [Další informace o funkci Direct line Speech](direct-line-speech.md)
* [Získat sadu Speech SDK](speech-sdk.md)