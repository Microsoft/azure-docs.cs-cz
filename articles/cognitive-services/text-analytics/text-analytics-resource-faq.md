---
title: Nejčastější dotazy k rozhraní Text Analytics API
titleSuffix: Azure Cognitive Services
description: Získejte odpovědi na běžné dotazy týkající se rozhraní API pro analýzu textu.
services: cognitive-services
author: HeidiSteen
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 09/12/2018
ms.author: heidist
ms.openlocfilehash: 6da9526c67b5c99bfd76dd881ebcceda2c769dfe
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/07/2019
ms.locfileid: "55874444"
---
# <a name="frequently-asked-questions-faq-about-the-text-analytics-cognitive-service"></a>O Text Analytics služby Cognitive Services – nejčastější dotazy (FAQ)

 Najděte odpovědi na nejčastější dotazy o konceptech, kód a scénáře týkající se rozhraní API pro analýzu textu služeb Microsoft Cognitive Services v Azure.

## <a name="can-text-analytics-identify-sarcasm"></a>Rozhraní Text Analytics můžete identifikovat jízlivosti?

Analýza je záporná pozitivní mínění, nikoli náladu zjišťování.

Je vždy určitý stupeň nepřesnost v analýzu mínění, ale model je nejužitečnější, pokud neexistuje žádný význam skrytý nebo získat jeho část obsahu. Irony, jízlivosti, si a podobně odlišování obsah závisí na kontextu jazykové verze a norem vyjádřit záměr. Tento typ obsahu je mezi nejnáročnějších způsobů na analyzovat. Obvykle je největší rozdíl mezi skóre vytvářené analyzátor a subjektivní hodnocení uživatelem pro obsah s odlišování význam.

## <a name="can-i-add-my-own-training-data-or-models"></a>Můžete přidat vlastní trénovacích dat nebo modely?

Ne, tyto modely jsou které je předem vytrénované. Pouze operace dostupné na nahraných dat jsou hodnocení, extrakce klíčových frází a rozpoznání jazyka. Společnost Microsoft není hostitelem vlastních modelů. Pokud chcete vytvářet a hostovat vlastní machine learning modely, vezměte v úvahu [strojového učení v Microsoft R Server](https://docs.microsoft.com/r-server/r/concept-what-is-the-microsoftml-package).

## <a name="can-i-request-additional-languages"></a>Můžete požádat o další jazyky?

Jsou k dispozici pro analýzu subjektivního hodnocení a extrakci klíčových frází [vyberte počet jazyků](text-analytics-supported-languages.md). Zpracování přirozeného jazyka je složitá a vyžaduje podstatné testování před nové funkce mohou být vydány. Z tohoto důvodu jsme vyhnout tak, aby nikdo je závislá na funkci, která vyžaduje více času na vyspělejší předem oznamujeme podpory. 

Pokud chcete nám pomohou určit prioritu jazyky, které mají pracovat na další, Hlasujte pro konkrétní jazyky [User Voice](https://cognitive.uservoice.com/forums/555922-text-analytics). 

## <a name="why-does-key-phrase-extraction-return-some-words-but-not-others"></a>Proč extrakce klíčových frází vrací některé slova, ale ne pro jiné?

Extrakce klíčových frází eliminuje nepodstatné slova a přídavných samostatné jmen. Kombinace přídavného jména-podstatné jméno, například "efektivními zobrazení" nebo "vám není jasné, počasí" jsou vráceny společně.

Obecně platí výstup se skládá z podstatných jmen a objekty věty. Výstup je uvedena v pořadí podle důležitosti, se první frázi je nejdůležitější. Důležitost se měří počet pokusů, které je uvedeno konkrétní koncept nebo vztah daného prvku s ostatními prvky v textu.

## <a name="why-does-output-vary-given-identical-inputs"></a>Proč výstup liší, daný identické vstupy?

Vylepšení moduly a algoritmy odesílány, pokud změna hlavní nebo tiše opravami do služby, pokud je dílčí aktualizace. V průběhu času můžete zjistit, která stejné výsledky vstupní text v různých zabarvení nebo klíčových frází výstup. To je normální a úmyslné důsledků používání spravovaného počítače studijní materiály v cloudu.

## <a name="next-steps"></a>Další postup

Je vaše otázky týkající se chybějící funkce nebo funkce? Vezměte v úvahu požádat nebo pro ni hlasovat o našich [webovou stránku UserVoice](https://cognitive.uservoice.com/forums/555922-text-analytics).

## <a name="see-also"></a>Další informace najdete v tématech

 [StackOverflow: Rozhraní API pro analýzu textu   
 [StackOverflow: Cognitive Services](http://stackoverflow.com/questions/tagged/microsoft-cognitive)
