---
title: Utterances v aplikacích LEOŠ v Azure | Microsoft Docs
description: Přidejte utterances v aplikacích jazyka Principy inteligentního služby (LEOŠ).
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 02/13/2018
ms.author: v-geberr
ms.openlocfilehash: 66a23876eebe177c767b20f60f86891c35da3385
ms.sourcegitcommit: ea5193f0729e85e2ddb11bb6d4516958510fd14c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/21/2018
ms.locfileid: "36301858"
---
# <a name="utterances-in-luis"></a>Utterances v LEOŠ

**Utterances** se vstup od uživatele, který vaše aplikace musí interpretovat. Ke cvičení LEOŠ extrahovat tříd Intent a entity z nich, je potřeba zaznamenat celou řadu různých vstupy pro každý záměr. Aktivní learning nebo proces pokračovat ke cvičení na nové utterances, je nezbytné pro počítač naučili intelligence poskytující LEOŠ.

Shromažďujte frází, které si myslíte, že budou uživatelé zadávat. Jinak zahrnuty utterances, které mají stejný význam, ale se vytvářejí Délka slova a word umístění. 

## <a name="how-to-choose-varied-utterances"></a>Jak vybrat rozmanitých utterances
Pokud jste nejdřív začít [přidání příklad utterances] [ add-example-utterances] LEOŠ modelu, tady jsou některé zásady, které je mějte na paměti.

### <a name="utterances-arent-always-well-formed"></a>Utterances nejsou vždy správně vytvořen.
To může být věty, jako je "Se sešit mi lístek do Paříž" nebo fragment věty, jako je "Rezervace" nebo "Paříž let."  Uživatelé provádět často pravopisné chyby. Při plánování vaší aplikace, zvažte, zda jste kontroly pravopisu vstup uživatele před jeho odesláním LEOŠ. [API kontrola pravopisu Bing] [ BingSpellCheck] se integruje s LEOŠ. Můžete přidružit aplikace LEOŠ externí klíč pro API kontrola pravopisu Bing při jeho publikování. Pokud není pravopisu utterances uživatele zkontrolujte, by Trénink LEOŠ na utterances, které zahrnují překlepy a pravopisné chyby.

### <a name="use-the-representative-language-of-the-user"></a>Použití reprezentativní jazyka uživatele
Při výběru utterances, uvědomte si, co si myslíte je běžný termín nebo frázi nemusí být typické uživateli klientské aplikace. Nemusí mít prostředí domény. Buďte proto opatrní při použití podmínky či fráze, že by uživatel pouze vyslovení Pokud byly odborník.

### <a name="choose-varied-terminology-as-well-as-phrasing"></a>Zvolte rozmanitých terminologie a také frázového
Zjistíte, že i v případě, že provedete ve snaze o vytváření vzorků rozmanitých věty, bude stále opakovat, s některých termínů.

Proveďte tyto utterances příklad:
```
how do I get a computer?
Where do I get a computer?
I want to get a computer, how do I go about it?
When can I have a computer? 
```
Základní termín zde není nejrůznější "počítač". Může se, že stolní počítač, přenosných počítačů, pracovní stanice nebo i pouze počítače. LEOŠ inteligentně odvodí synonyma z kontextu, ale při vytváření utterances pro školení je stále lepší je lišit.

## <a name="example-utterances-in-each-intent"></a>Příklad utterances v každém záměru
Každý záměr musí mít příklad utterances, alespoň 10 až 15. Pokud je záměrem, který nemá žádné utterances příklad, nebudete moci cvičení LEOŠ. Pokud je záměrem s jednu nebo několik velmi utterances příklad, nebude LEOŠ přesně předpovědět záměr. 

## <a name="add-small-groups-of-10-15-utterances-for-each-authoring-iteration"></a>Přidat malé skupiny 10 až 15 utterances pro každou iteraci pro tvorbu
V každé iteraci modelu nepřidávejte velké množství utterances. Přidáte utterances množství desítkami. [Train](luis-how-to-train.md), [publikování](publishapp.md), a [testování](interactive-test.md) znovu.  

LEOŠ vytvoří efektivní modely utterances, které jsou vybrány pečlivě. Přidání příliš mnoho utterances není cenné, protože by to zavedlo nejasnostem.  

Je lepší začínat několik utterances, pak [zkontrolujte koncový bod utterances](label-suggested-utterances.md) pro správné záměrné předpovědi a entity extrakci.

## <a name="ignoring-words-and-punctuation"></a>Bez ohledu na slova a interpunkce
Pokud chcete ignorovat konkrétní slova nebo interpunkční znaménka v příkladu utterance, použijte [vzor](luis-concept-patterns.md#pattern-syntax) s _Ignorovat_ syntaxe. 

## <a name="training-utterances"></a>Školení utterances
Školení je Nedeterministický: utterance předpovědi může mírně lišit mezi verzemi nebo aplikace.

## <a name="testing-utterances"></a>Testování utterances 

Vývojáři by se měl spustit testování jejich LEOŠ aplikace s skutečné provoz odesláním utterances ke koncovému bodu. Tyto utterances se používají ke zlepšení výkonu tříd Intent a entity s [zkontrolujte utterances](label-suggested-utterances.md). Testy odeslána s webem LEOŠ testování podokně nejsou odesílány prostřednictvím koncového bodu a proto nepřispívají k active učení. 

## <a name="review-utterances"></a>Zkontrolujte utterances
Po modelu vyškolení, publikované a příjmu [koncový bod](luis-glossary.md#endpoint) dotazy, [zkontrolujte utterances](label-suggested-utterances.md) navrhovaná službou LEOŠ. LEOŠ vybere utterances koncový bod, které mají nízké skóre pro záměr nebo entity. 

## <a name="best-practices"></a>Osvědčené postupy
Zkontrolujte [osvědčené postupy](luis-concept-best-practices.md) Další informace.

## <a name="next-steps"></a>Další postup
V tématu [přidat příklad utterances] [ add-example-utterances] informace o školení LEOŠ aplikace porozumět utterances uživatele.

[add-example-utterances]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-how-to-add-example-utterances
[BingSpellCheck]: https://docs.microsoft.com/azure/cognitive-services/bing-spell-check/proof-text