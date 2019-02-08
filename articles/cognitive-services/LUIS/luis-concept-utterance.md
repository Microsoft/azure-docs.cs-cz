---
title: Dobrým příkladem projevy
titleSuffix: Language Understanding - Azure Cognitive Services
description: Projevy jsou vstupy od uživatele, který vaše aplikace potřebuje k interpretaci. Shromážděte frázích, které si myslíte, že budou uživatelé zadávat. Zahrňte projevy, které mají stejný význam, ale jsou vytvořeny jinak než délka slova a umístění aplikace word.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 01/02/2019
ms.author: diberry
ms.openlocfilehash: 63334c861cc85c7119ccd3111429dee47ada3162
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/07/2019
ms.locfileid: "55862934"
---
# <a name="understand-what-good-utterances-are-for-your-luis-app"></a>Porozumění projevy vhodné pro aplikace LUIS

**Projevy** jsou vstupy od uživatele, který vaše aplikace potřebuje k interpretaci. K trénování LUIS k extrakci záměry a entity z nich, je potřeba zaznamenat širokou škálu projevy jiném příkladu pro každý záměr. Aktivní učení nebo proces pokračuje tak moct trénovat na nové projevy, je nezbytné pro zjištěné počítače intelligence, která poskytuje služby LUIS.

Shromážděte projevy, které si myslíte, že budou uživatelé zadávat. Zahrnout projevy, které mají stejný význam, ale jsou vytvořeny v mnoha různými způsoby:

* Délka utterance – krátký, střední a dlouhou pro vaši klientskou aplikaci
* Délka slova a slovní spojení 
* Umístění aplikace Word – entity na začátku, střední a konec utterance
* Gramatika 
* Pluralizace
* Slovního rozboru
* Volba podstatné jméno a operace
* Interpunkční znaménka - dobré různých pomocí správné, správné a žádná gramatika

## <a name="how-to-choose-varied-utterances"></a>Jak zvolit pohyblivá podle projevy

Pokud můžete nejprve začít tím, že [přidání projevů příklad](luis-how-to-add-example-utterances.md) do modelu LUIS, tady jsou některé zásady týkající se brát v úvahu.

### <a name="utterances-arent-always-well-formed"></a>Projevy nejsou vždy ve správném

To může být věty, jako je "Rezervovat lístek do Paříže pro mě" nebo fragment věty, stejně jako "Rezervace" nebo "Paříž let."  Uživatelé často musíte dělat pravopisných chyb. Při plánování vaší aplikace, zvažte, jestli používáte [Check][(luis-tutorial-bing-spellcheck) pravopisu Bingu opravit před předáním LUIS vstup uživatele. 

Pokud není pravopisu kontrola uživatele projevy, by měl trénování LUIS na projevy, které zahrnují překlepy nebo chybně napsaných slov.

### <a name="use-the-representative-language-of-the-user"></a>Použití reprezentativní jazyk uživatele

Při výběru projevy, mějte na paměti, co si myslíte, je běžné termín nebo frázi nemusí být správná pro běžné uživatelské klientské aplikace. Nemají prostředí domény. Buďte opatrní při použití podmínky nebo slovních spojení, že uživatel by pouze Řekněme, že pokud se jednalo o odborníky.

### <a name="choose-varied-terminology-as-well-as-phrasing"></a>Zvolte pohyblivá podle terminologie, jakož i frázového

Zjistíte, že i v případě, že provedete snahy o vytvoření různých věty vzorů, bude stále zopakujete některé slovníku.

Využijte tyto projevy příkladu:

|Ukázkové promluvy|
|--|
|Jak získám počítače?|
|Kde můžu získat počítače?|
|Chci získat do počítače, jak se mám obrátit o něm?|
|Pokud mají počítače?| 

Termín core tady není různorodé "počítač". Použijte alternativy, třeba stolní počítač, přenosných počítačů, pracovní stanice nebo dokonce pouze počítače. Služba LUIS inteligentně odvodí synonyma z kontextu, ale při vytváření projevy pro vzdělávání se liší je stále lepší.

## <a name="example-utterances-in-each-intent"></a>Příklad projevy v každé záměr

Každý záměr musí mít příklad projevy, alespoň 15. Pokud máte záměru, který nemá jakékoli projevy příklad, nebudete moci natrénovat LUIS. Pokud máte záměru s velmi málo nebo jeden příklad projevy, nebude LUIS přesně předpovídat záměr. 

## <a name="add-small-groups-of-15-utterances-for-each-authoring-iteration"></a>Přidat malé skupiny 15 projevy pro každou iteraci pro tvorbu

V každé iteraci modelu nepřidávejte velké množství projevy. Přidání projevů množství 15. [Trénování](luis-how-to-train.md), [publikovat](luis-how-to-publish-app.md), a [testování](luis-interactive-test.md) znovu.  

Služba LUIS sestavení efektivní modely s projevy, které jsou vybrány pečlivě autorem modelů služby LUIS. Přidání příliš mnoho projevy není cenné, protože zavádí nejasnostem.  

Je lepší začít s několika projevy, pak [zkontrolujte koncový bod projevy](luis-how-to-review-endoint-utt.md) správné záměru extrakci předpovědi a entity.

## <a name="punctuation-marks"></a>Interpunkční znaménka

Služba LUIS nebude ignorovat interpunkčních znamének, ve výchozím nastavení, protože některé klientské aplikace mohou klást na těchto známky význam. Zajistěte, aby že vaše příklad projevy pomocí interpunkce a bez interpunkce v pořadí pro obě styly vrátit stejné relativní skóre. Pokud interpunkční znaménka nemá žádný zvláštní význam v klientské aplikace, zvažte [je ignorována interpunkce](#ignoring-words-and-punctuation) pomocí vzorce. 

## <a name="ignoring-words-and-punctuation"></a>Ignoruje slova a interpunkce

Pokud chcete ignorovat slova nebo interpunkčním znaménkem v příkladu utterance, použijte [vzor](luis-concept-patterns.md#pattern-syntax) s _Ignorovat_ syntaxe. 

## <a name="training-utterances"></a>Projevy školení

Školení je obecně Nedeterministický: předpověď utterance může mírně lišit mezi verzemi nebo aplikace. Můžete odebrat Nedeterministický školení aktualizací [nastavení verze](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/versions-update-application-version-settings) rozhraní API s `UseAllTrainingData` název/hodnota dvojici použít všechny trénovací data.

## <a name="testing-utterances"></a>Testování projevy 

Vývojáři by měla začínat testování jejich LUIS aplikace skutečný provozu odesláním projevy do [koncový bod předpovědi](luis-how-to-azure-subscription.md) adresy URL. Tyto projevy, které se používají ke zlepšení výkonu záměry a entity, které [zkontrolujte projevy](luis-how-to-review-endoint-utt.md). Testy odeslané s webem služby LUIS testování podokně neodešlou prostřednictvím koncového bodu a proto se nepodílejí na aktivní učení. 

## <a name="review-utterances"></a>Zkontrolujte projevy

Po modelu trénovaného publikované a přijímající [koncový bod](luis-glossary.md#endpoint) dotazy, [zkontrolujte projevy](luis-how-to-review-endoint-utt.md) navrhovaná službou LUIS. Služba LUIS vybere koncový bod projevy, které mají nízké skóre, které se záměrem nebo entity. 

## <a name="best-practices"></a>Osvědčené postupy

Kontrola [osvědčené postupy](luis-concept-best-practices.md) a použít je jako součást vašeho regulární vývojového cyklu.

## <a name="next-steps"></a>Další postup
Zobrazit [přidání projevů příklad](luis-how-to-add-example-utterances.md) informace o školení aplikace LUIS pochopit projevy uživatele.

