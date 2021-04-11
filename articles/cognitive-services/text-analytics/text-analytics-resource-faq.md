---
title: Nejčastější dotazy týkající se rozhraní API pro analýzu textu
titleSuffix: Azure Cognitive Services
description: Získejte odpovědi na nejčastější dotazy týkající se konceptů, kódu a scénářů týkajících se rozhraní API pro analýzu textu pro Azure Cognitive Services.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 03/29/2021
ms.author: aahi
ms.openlocfilehash: c38b7c33cfe787ba933ca1fc4961080eaa4ada61
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/03/2021
ms.locfileid: "106276055"
---
# <a name="frequently-asked-questions-faq-about-the-text-analytics-api"></a>Nejčastější dotazy týkající se rozhraní API pro analýzu textu

 Získejte odpovědi na nejčastější dotazy týkající se konceptů, kódu a scénářů souvisejících s rozhraní API pro analýzu textu v Azure Cognitive Services.

## <a name="what-is-the-maximum-size-and-number-of-requests-i-can-make-to-the-api"></a>Jaká je maximální velikost a počet požadavků, které můžu v rozhraní API udělat?

Informace o velikosti a počtu požadavků, které můžete odeslat za minutu a sekundy, najdete v článku [omezení dat](concepts/data-limits.md) .

## <a name="can-text-analytics-identify-sarcasm"></a>Může Analýza textu identifikovat jízlivosti?

Analýza je pro pozitivní negativní míněníi, nikoli pro detekci nálady.

V analýze mínění je vždy určitý stupeň nepřesnosti, ale model je nejužitečnější, pokud k obsahu neexistují žádné skryté významy nebo podtexty. Obsah železa, jízlivosti, humor a podobně odlišit se spoléhá na kulturní kontext a normy pro vyjádření záměru. Tento typ obsahu je z nejvyšší náročnosti na analýzu. Největší nesoulad mezi daným skóre vytvořeným analyzátorem a subjektivní hodnocením člověkem je obvykle pro obsah s odlišit významem.

## <a name="can-i-add-my-own-training-data-or-models"></a>Můžu přidat vlastní školicí data a modely?

Ne, modely jsou předvlakované. Jediné operace, které jsou k dispozici pro nahraná data, jsou bodování, extrakce klíčových frází a rozpoznání jazyka. Nehostují se vlastní modely. Pokud chcete vytvořit a hostovat vlastní modely strojového učení, zvažte [Možnosti strojového učení v Microsoft R Server](/r-server/r/concept-what-is-the-microsoftml-package).

## <a name="can-i-request-additional-languages"></a>Můžu si vyžádat další jazyky?

Analýza mínění a extrakce klíčových frází jsou k dispozici pro [vybraný počet jazyků](./language-support.md). Zpracování přirozeného jazyka je složité a vyžaduje zásadní testování, než bude možné uvolnit nové funkce. Z tohoto důvodu se vyhnete předem ohlášení podpory, takže nikdo neuplatní závislost na funkcích, které potřebují víc času na vyspělý čas. 

Abychom vám pomohli určit, které jazyky se mají dále pracovat, Hlasujte pro konkrétní jazyky v [uživatelském hlasu](https://cognitive.uservoice.com/forums/555922-text-analytics). 

## <a name="why-does-key-phrase-extraction-return-some-words-but-not-others"></a>Proč extrakce klíčových frází vrátí některá slova, ale ne jiné?

Extrakce klíčových frází eliminuje nepostradatelná slova a samostatné přídavné jména. Přídavná jména a kombinace podstatného jména, například "zobrazení Spectacular" nebo "Foggy počasí", jsou vráceny společně.

Výstup se obecně skládá z podstatných jmen a objektů věty. Výstup je uveden v pořadí podle důležitosti, přičemž první fráze je nejdůležitější. Důležitost se měří podle počtu uvedení konkrétního konceptu nebo vztahu tohoto prvku na jiné prvky v textu.

## <a name="why-does-output-vary-given-identical-inputs"></a>Proč se výstup liší, má stejné vstupy?

Vylepšení modelů a algoritmů se oznamují, pokud je změna hlavní, nebo v tichém zahrnutí do služby, pokud je aktualizace nepatrná. V průběhu času se můžete setkat s tím, že stejný textový vstup má za následek jiné mínění skóre nebo výstup klíčových frází. Jedná se o běžné a úmyslné důsledky používání prostředků spravovaného strojového učení v cloudu.

## <a name="service-availability-and-redundancy"></a>Dostupnost služby a redundance

### <a name="is-text-analytics-service-zone-resilient"></a>Je Analýza textu zóny služby odolné?

Ano. Služba Analýza textu je ve výchozím nastavení odolná proti zónám.

### <a name="how-do-i-configure-the-text-analytics-service-to-be-zone-resilient"></a>Návody nakonfigurovat službu Analýza textu, aby byla odolná proti zóně?

Pro povolení odolnosti zóny není nutná žádná konfigurace zákazníka. V zóně – odolnost pro Analýza textu prostředky je ve výchozím nastavení dostupná a spravovaná samotnými službami.

## <a name="next-steps"></a>Další kroky

Máte dotaz ohledně chybějící funkce nebo funkce? Na našem webu [UserVoice](https://cognitive.uservoice.com/forums/555922-text-analytics)si můžete vyžádat nebo hlasovat.

## <a name="see-also"></a>Viz také

 * [StackOverflow: rozhraní API pro analýzu textu](https://stackoverflow.com/questions/tagged/text-analytics-api)   
 * [StackOverflow: Cognitive Services](https://stackoverflow.com/questions/tagged/microsoft-cognitive)
