---
title: Nejčastější dotazy týkající se rozhraní API pro analýzu textu
titleSuffix: Azure Cognitive Services
description: Najděte odpovědi na nejčastější dotazy týkající se konceptů, kódu a scénářů souvisejících s rozhraním API pro analýzu textu pro služby Azure Cognitive Services.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 02/13/2019
ms.author: aahi
ms.openlocfilehash: aca3b0bea65923cee1bae25020058666c973922c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "73837127"
---
# <a name="frequently-asked-questions-faq-about-the-text-analytics-cognitive-service"></a>Nejčastější dotazy (FAQ) týkající se služby Text Analytics Cognitive Service

 Najděte odpovědi na nejčastější dotazy týkající se konceptů, kódu a scénářů souvisejících s rozhraním API pro analýzu textu pro služby Microsoft Cognitive Services v Azure.

## <a name="can-text-analytics-identify-sarcasm"></a>Dokáže analýza textu identifikovat sarkasmus?

Analýza je pro pozitivní negativní sentiment spíše než detekci nálady.

V analýze mínění je vždy určitý stupeň nepřesnosti, ale model je nejužitečnější, pokud neexistuje žádný skrytý význam nebo podtext k obsahu. Ironie, sarkasmus, humor a podobně nuancí obsah spoléhají na kulturní kontext a normy, aby zprostředkovaly záměr. Tento typ obsahu patří mezi nejnáročnější analyzovat. Obvykle největší rozdíl mezi daným skóre vytvořeným analyzátorem a subjektivním hodnocením člověkem je pro obsah s jemným významem.

## <a name="can-i-add-my-own-training-data-or-models"></a>Mohu přidat vlastní trénovací data nebo modely?

Ne, modely jsou předem vycvičené. Jediné operace, které jsou k dispozici na odeslaných datech, jsou vyhodnocování, extrakce klíčových frází a detekce jazyka. Nehostujeme vlastní modely. Pokud chcete vytvořit a hostovat vlastní modely strojového učení, zvažte [možnosti strojového učení v Microsoft R Server](https://docs.microsoft.com/r-server/r/concept-what-is-the-microsoftml-package).

## <a name="can-i-request-additional-languages"></a>Mohu požádat o další jazyky?

Analýza mínění a extrakce [klíčových](text-analytics-supported-languages.md)frází jsou k dispozici pro vybraný počet jazyků . Zpracování přirozeného jazyka je složité a vyžaduje značné testování před vydáním nových funkcí. Z tohoto důvodu se vyhneme předběžnému oznámení podpory, aby nikdo nenapáchal závislost na funkcích, které potřebují více času na zrání. 

Chcete-li nám pomoci stanovit prioritu jazyků, na kterých se má pracovat příště, hlasujte pro konkrétní jazyky v [aplikaci User Voice](https://cognitive.uservoice.com/forums/555922-text-analytics). 

## <a name="why-does-key-phrase-extraction-return-some-words-but-not-others"></a>Proč extrakce klíčových frází vrací některá slova, ale jiná ne?

Extrakce klíčových frází eliminuje nepodstatná slova a samostatná přídavná jména. Kombinace přídavných jmen a podstaty, jako jsou "velkolepé výhledy" nebo "mlhavé počasí", jsou vráceny společně.

Obecně platí, že výstup se skládá z vlastních nebo duší a objektů věty. Výstup je uveden v pořadí podle důležitosti, přičemž nejdůležitější je první věta. Důležitost se měří počtem zmiňují určitý pojem nebo vztahem tohoto prvku k jiným prvkům v textu.

## <a name="why-does-output-vary-given-identical-inputs"></a>Proč se výstup liší, vzhledem k identické vstupy?

Vylepšení modelů a algoritmů jsou oznámeny, pokud je změna hlavní, nebo tiše slipstreamed do služby, pokud je aktualizace menší. V průběhu času můžete zjistit, že stejný vstup textu má za následek jiné skóre mínění nebo výstup klíčových frází. To je normální a záměrný důsledek použití spravovaných prostředků strojového učení v cloudu.

## <a name="next-steps"></a>Další kroky

Je vaše otázka týkající se chybějící funkce nebo funkce? Zvažte žádost nebo hlasování pro něj na našich [webových stránkách UserVoice](https://cognitive.uservoice.com/forums/555922-text-analytics).

## <a name="see-also"></a>Viz také

 [StackOverflow: Rozhraní API pro analýzu textu](https://stackoverflow.com/questions/tagged/text-analytics-api)   
 [StackOverflow: Kognitivní služby](https://stackoverflow.com/questions/tagged/microsoft-cognitive)
