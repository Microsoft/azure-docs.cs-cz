---
title: Nejčastější dotazy o Analýza textu rozhraní API - kognitivní služby Azure | Microsoft Docs
description: Získejte odpovědi na časté otázky týkající se Microsoft kognitivní Services Text Analytics API v Azure.
services: cognitive-services
author: HeidiSteen
manager: cgronlun
ms.service: cognitive-services
ms.component: text-analytics
ms.topic: conceptual
ms.date: 3/07/2018
ms.author: heidist
ms.openlocfilehash: bf82899b4317f0f5ce0f6ca5096dccef7cddd931
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2018
ms.locfileid: "35343628"
---
# <a name="frequently-asked-questions-faq-about-the-text-analytics-api"></a>Nejčastější dotazy (FAQ) o rozhraní API Analýza textu

 Odpovědi na nejčastější dotazy o konceptech, kód a scénáře související s rozhraní API Analytics Text pro kognitivní služby společnosti Microsoft na platformě Azure.

## <a name="can-text-analytics-identify-sarcasm"></a>Analýza textu můžete identifikovat sarcasm?

Analýza je kladné záporné postojích, nikoli vyznění detekce.

Je vždy určitý stupeň nepřesnosti analýzou postojích, ale modelu je nejužitečnější, pokud není skrytý význam nebo subtext k obsahu. Irony, sarcasm, humor a podobně nuanced obsah závisí na kulturního kontextu a norem pro vyjádření záměr. Tento typ obsahu je mezi nejnáročnější k analýze. Obvykle je největší nesoulad mezi danou skóre vytvářených nástroje analyzer a subjektivní assessment uživatelem pro obsah s nuanced význam.

## <a name="can-i-add-my-own-training-data-or-models"></a>Můžete přidat vlastní Cvičná data nebo modely?

Ne, jsou pretrained modely. Jsou k dispozici jenom operace na odeslaná data vyhodnocování, extrakce klíče frázi a zjišťování jazyka. Vlastní modely jsme nejsou hostiteli. Pokud chcete vytvořit a hostovat vlastní machine learning modely, zvažte [strojového učení možnosti v Microsoft R Server](https://docs.microsoft.com/r-server/r/concept-what-is-the-microsoftml-package).

## <a name="can-i-request-additional-languages"></a>Může požádat o další jazyky?

Jsou k dispozici pro postojích analýzy a rozbalení klíčů frázi [vyberte počet jazyků](text-analytics-supported-languages.md). Zpracování přirozeného jazyka je složitý a vyžaduje významné testování před nové funkce, se uvolní. Z tohoto důvodu jsme Vyhněte se předem uvedení podporu tak, aby měla žádná závislost na funkce, které vyžaduje více času pro dospělé. 

Z důvodu stanovení priorit jazyky, které mají pracovat na další, hlasování pro konkrétní jazyky o [User Voice](https://cognitive.uservoice.com/forums/555922-text-analytics). 

## <a name="why-does-key-phrase-extraction-return-some-words-but-not-others"></a>Proč klíče frázi extrakce vrátit některé slova a jiné ne?

Rozbalení klíče frázi eliminuje slova není nezbytné a přídavných samostatné jmen. Kombinace přídavných jmen-podstatné jméno, například "spectacular zobrazení" nebo "zamlženého počasí" jsou vráceny společně.

Obecně platí výstup se skládá z podstatná jména a objekty věty. Výstup je uvedena v pořadí podle důležitost, se první fráze se nejdůležitější. Důležitost se měří počet, který je uveden konkrétní koncept nebo vztah tohoto prvku do další prvky v textu.

## <a name="why-does-output-vary-given-identical-inputs"></a>Proč výstup liší, daný identické vstupy?

Vylepšení modely a algoritmy jsou oznámeno, pokud změny hlavní nebo tiše opravami do služby, pokud je aktualizace menší. V čase možná zjistíte, že stejné výsledky vstupní text v různých postojích skóre nebo klíče frázi výstup. To je normální a záměrné důsledků použití materiály spravovaného počítače v cloudu.

## <a name="next-steps"></a>Další postup

Je váš dotaz o chybějící funkce nebo funkce? Zvažte požaduje nebo volíte se naše [webu UserVoice](https://cognitive.uservoice.com/forums/555922-text-analytics).

## <a name="see-also"></a>Další informace najdete v tématech

 [StackOverflow: Analýza textu rozhraní API](https://stackoverflow.com/questions/tagged/text-analytics-api)   
 [StackOverflow: Kognitivní služby](http://stackoverflow.com/questions/tagged/microsoft-cognitive)
