---
title: Projevy LUIS aplikací v Azure | Dokumentace Microsoftu
description: Přidání projevů v aplikacích Language Understanding Intelligent Service (LUIS).
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 02/13/2018
ms.author: v-geberr
ms.openlocfilehash: 4a3571acade7b7bd304c91eadc90b1a9f6e53860
ms.sourcegitcommit: 11321f26df5fb047dac5d15e0435fce6c4fde663
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/06/2018
ms.locfileid: "37886837"
---
# <a name="utterances-in-luis"></a>Projevy v LUIS

**Projevy** jsou vstupy od uživatele, který vaše aplikace potřebuje k interpretaci. K trénování LUIS k extrakci záměry a entity z nich, je potřeba zaznamenat celou řadu různých vstupů pro každý záměr. Aktivní učení nebo proces pokračuje tak moct trénovat na nové projevy, je nezbytné pro zjištěné počítače intelligence, která poskytuje služby LUIS.

Shromážděte frázích, které si myslíte, že budou uživatelé zadávat. Zahrňte projevy, které mají stejný význam, ale jsou vytvořeny jinak než délka slova a umístění aplikace word. 

## <a name="how-to-choose-varied-utterances"></a>Jak zvolit pohyblivá podle projevy
Pokud můžete nejprve začít tím, že [přidání projevů příklad](luis-how-to-add-example-utterances.md) do modelu LUIS, tady jsou některé zásady týkající se brát v úvahu.

### <a name="utterances-arent-always-well-formed"></a>Projevy nejsou vždy ve správném
To může být věty, jako je "Rezervovat z mě lístek do Paříže" nebo fragment věty, stejně jako "Rezervace" nebo "Paříž let."  Uživatelé často musíte dělat pravopisných chyb. Při plánování vaší aplikace, zvažte, zda je kontrola pravopisu vstupu uživatele před předáním LUIS. [API kontrola pravopisu Bingu] [ BingSpellCheck] integruje do služby LUIS. Můžete přidružit aplikace LUIS externí klíč pro rozhraní API Bingu pro kontrolu pravopisu zkontrolovat při jejím publikování. Pokud není pravopisu kontrola uživatele projevy, by měl trénování LUIS na projevy, které zahrnují překlepy nebo chybně napsaných slov.

### <a name="use-the-representative-language-of-the-user"></a>Použití reprezentativní jazyk uživatele
Při výběru projevy, mějte na paměti, co si myslíte je běžný pojem nebo fráze, nemusí být pro typické uživatele klientské aplikace. Nemají prostředí domény. Buďte proto opatrní při použití podmínky nebo slovních spojení, že uživatel by pouze Řekněme, že pokud se jednalo o odborníky.

### <a name="choose-varied-terminology-as-well-as-phrasing"></a>Zvolte pohyblivá podle terminologie, jakož i frázového
Zjistíte, že i v případě, že provedete snahy o vytvoření různých věty vzorů, bude stále zopakujete některé slovníku.

Využijte tyto projevy příkladu:
```
how do I get a computer?
Where do I get a computer?
I want to get a computer, how do I go about it?
When can I have a computer? 
```
Termín core tady není různorodé "počítač". Slibují může stolní počítač, přenosných počítačů, pracovní stanice nebo dokonce pouze počítače. Služba LUIS inteligentně odvodí synonyma z kontextu, ale při vytváření projevy pro vzdělávání se liší je stále lepší.

## <a name="example-utterances-in-each-intent"></a>Příklad projevy v každé záměr
Každý záměr musí mít příklad projevy, alespoň 10 až 15. Pokud máte záměru, který nemá jakékoli projevy příklad, nebudete moci natrénovat LUIS. Pokud máte záměru s velmi málo nebo jeden příklad projevy, nebude LUIS přesně předpovídat záměr. 

## <a name="add-small-groups-of-10-15-utterances-for-each-authoring-iteration"></a>Přidat malé skupiny 10 až 15 projevy pro každou iteraci pro tvorbu
V každé iteraci modelu nepřidávejte velké množství projevy. Přidání projevů množství desítky. [Trénování](luis-how-to-train.md), [publikovat](luis-how-to-publish-app.md), a [testování](interactive-test.md) znovu.  

Služba LUIS sestavení efektivní modely s projevy, které jsou vybrány pečlivě. Přidání příliš mnoho projevy není cenné, protože zavádí nejasnostem.  

Je lepší začít s několika projevy, pak [zkontrolujte koncový bod projevy](label-suggested-utterances.md) správné záměru extrakci předpovědi a entity.

## <a name="ignoring-words-and-punctuation"></a>Ignoruje slova a interpunkce
Pokud chcete ignorovat slova nebo interpunkčním znaménkem v příkladu utterance, použijte [vzor](luis-concept-patterns.md#pattern-syntax) s _Ignorovat_ syntaxe. 

## <a name="training-utterances"></a>Projevy školení
Školení je Nedeterministický: předpověď utterance může mírně lišit mezi verzemi nebo aplikace.

## <a name="testing-utterances"></a>Testování projevy 

Vývojáři by měl spustit testování jejich LUIS aplikace skutečný provozu odesláním projevy ke koncovému bodu. Tyto projevy, které se používají ke zlepšení výkonu záměry a entity, které [zkontrolujte projevy](label-suggested-utterances.md). Testy odeslané s webem služby LUIS testování podokně neodešlou prostřednictvím koncového bodu a proto se nepodílejí na aktivní učení. 

## <a name="review-utterances"></a>Zkontrolujte projevy
Po modelu trénovaného publikované a přijímající [koncový bod](luis-glossary.md#endpoint) dotazy, [zkontrolujte projevy](label-suggested-utterances.md) navrhovaná službou LUIS. Služba LUIS vybere koncový bod projevy, které mají nízké skóre, které se záměrem nebo entity. 

## <a name="best-practices"></a>Osvědčené postupy
Kontrola [osvědčené postupy](luis-concept-best-practices.md) Další informace.

## <a name="next-steps"></a>Další postup
Zobrazit [přidání projevů příklad](luis-how-to-add-example-utterances.md) informace o školení aplikace LUIS pochopit projevy uživatele.

[BingSpellCheck]: https://docs.microsoft.com/azure/cognitive-services/bing-spell-check/proof-text