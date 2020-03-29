---
title: Záměry a entity – LUIS
titleSuffix: Azure Cognitive Services
description: Jeden záměr představuje úkol nebo akci, kterou chce uživatel provést. Je to účel nebo cíl vyjádřený výrokem uživatele. Definujte sadu záměrů, které odpovídají akcím, které uživatelé chtějí provést ve vaší aplikaci.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 10/10/2019
ms.author: diberry
ms.openlocfilehash: 309a2592dbac2918aeb532fbe91e33d296f4e5a5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79220971"
---
# <a name="intents-in-your-luis-app"></a>Záměry v aplikaci LUIS

Záměr představuje úkol nebo akci, kterou chce uživatel provést. Jedná se o účel nebo cíl vyjádřený v [promluvě](luis-concept-utterance.md)uživatele .

Definujte sadu záměrů, které odpovídají akcím, které uživatelé chtějí provést ve vaší aplikaci. Například cestovní aplikace definuje několik záměrů:

Záměry cestovatelské aplikace   |   Ukázkové promluvy   | 
------|------|
 BookFlight (Rezervovat let)     |   "Rezervujte mi let do Ria příští týden" <br/> "Odvez mě do Ria 24. <br/> "Potřebuji letenku příští neděli do Rio de Janeiro"    |
 Pozdrav     |   "Ahoj" <br/>"Hello" <br/>"Dobré ráno"  |
 Zkontrolovat Počasí | "Jaké je počasí v Bostonu?" <br/> "Ukažte mi předpověď na tento víkend" |
 Žádný         | "Dejte mi recept na sušenky"<br>"Vyhráli Lakers?" |

Všechny aplikace jsou dodávány s předdefinovaným záměrem "[Žádný](#none-intent)", což je záložní záměr. 

## <a name="prebuilt-domains-provide-intents"></a>Předem sestavené domény poskytují záměry
Kromě záměrů, které definujete, můžete použít předem vytvořené záměry z jedné z [předem vytvořených domén](luis-how-to-use-prebuilt-domains.md). 

## <a name="return-all-intents-scores"></a>Vrátit skóre všech záměrů
Přiřadíte utterance k jedinému záměru. Když luis obdrží utterance na koncovém bodu, ve výchozím nastavení vrátí nejvyšší záměr pro tento utterance. 

Pokud chcete skóre pro všechny záměry pro utterance, můžete zadat příznak na řetězec dotazu rozhraní API předpověď. 

|Předpověď API verze|Příznak|
|--|--|
|V2|`verbose=true`|
|V3|`show-all-intents=true`|

## <a name="intent-compared-to-entity"></a>Záměr ve srovnání s entitou
Záměr představuje akci, kterou by aplikace měla provést pro uživatele a je založena na celé utterance. Utterance může mít pouze jeden hlavní bodové záměr, ale může mít mnoho entit. 

<a name="how-do-intents-relate-to-entities"></a>

Vytvořte záměr, když _záměr_ uživatele by aktivovat akci ve vaší klientské aplikaci, jako je volání checkweather() funkce. Potom vytvořte entity představující parametry potřebné k provedení akce. 

|Záměr   | Entita | Ukázková promluva   | 
|------------------|------------------------------|------------------------------|
| Zkontrolovat Počasí | { "typ": "umístění", "entita": "seattle" }<br>{ "type": "builtin.datetimeV2.date","entity": "tomorrow","resolution":"2018-05-23" } | Jaké je `Seattle` `tomorrow`počasí? |
| Zkontrolovat Počasí | { "type": "date_range", "entity": "tento víkend" } | Zobrazit předpověď pro`this weekend` | 
||||

## <a name="prebuilt-domain-intents"></a>Předem sestavené záměry domény

[Předem sestavené domény](luis-how-to-use-prebuilt-domains.md) poskytují záměry s projevy. 

## <a name="none-intent"></a>Žádný záměr

**Žádný** záměr je vytvořen, ale ponechat prázdný na účel. Žádný **None** záměr je povinný záměr a nelze odstranit nebo přejmenovat. Vyplňte jej projevy, které jsou mimo vaši doménu.

**Žádný** záměr je záložní záměr, důležité v každé aplikaci a by měl mít 10 % celkových projevy. Používá se k výuce luis projevy, které nejsou důležité v doméně aplikace (oblast předmětu). Pokud nepřidáte žádné projevy pro **žádný** záměr, LUIS vynutí utterance, který je mimo doménu do jednoho z záměrů domény. To bude zkreslit skóre předpověď výukou LUIS nesprávný záměr pro utterance. 

Při utterance je předpovězeno jako žádný záměr, klientská aplikace můžete klást další otázky nebo poskytnout nabídku nasměrovat uživatele na platné volby. 

## <a name="negative-intentions"></a>Negativní záměry 
Pokud chcete určit negativní a pozitivní záměry, jako například **"Chci** auto" a "Nechci auto", můžete vytvořit dva záměry (jeden pozitivní a jeden negativní) a přidat odpovídající projevy pro každého. **don't** Nebo můžete vytvořit jeden záměr a označit dva různé kladné a záporné termíny jako entitu.  

## <a name="intents-and-patterns"></a>Záměry a vzory

Pokud máte příklad projevy, které mohou být definovány v části nebo celku jako regulární výraz, zvažte použití [entity regulárního výrazu](luis-concept-entity-types.md#regular-expression-entity) spárované se [vzorkem](luis-concept-patterns.md). 

Použití entity regulárního výrazu zaručuje extrakci dat tak, aby byl vzorek spárován. Porovnávání vzorů zaručuje, že je vrácen přesný záměr. 

## <a name="intent-balance"></a>Rovnováha záměru
Záměry domény aplikace by mělmít rovnováhu projevy v rámci každého záměru. Nemají jeden záměr s 10 projevy a jiný záměr s 500 projevy. To není vyvážené. Pokud máte tuto situaci, zkontrolujte záměr s 500 projevy a zjistěte, zda mnoho záměrů lze reorganizovat do [vzoru](luis-concept-patterns.md). 

**Žádný** záměr není zahrnuta v zůstatku. Tento záměr by měl obsahovat 10 % celkových promluv v aplikaci.

## <a name="intent-limits"></a>Omezení záměru
Zkontrolujte [limity,](luis-boundaries.md#model-boundaries) abyste pochopili, kolik záměrů můžete přidat do modelu. 

### <a name="if-you-need-more-than-the-maximum-number-of-intents"></a>Pokud potřebujete více než maximální počet záměrů 
Nejprve zvažte, zda váš systém používá příliš mnoho záměrů. 

### <a name="can-multiple-intents-be-combined-into-single-intent-with-entities"></a>Může být více záměrů sloučeno do jednoho záměru s entitami. 
Záměry, které jsou příliš podobné může ztížit LUIS rozlišovat mezi nimi. Záměry by měly být dostatečně rozmanité, aby zachytily hlavní úkoly, které uživatel požaduje, ale nemusí zachytit každou cestu, kterou váš kód provede. Například BookFlight a FlightCustomerService může být samostatné záměry v cestovní aplikaci, ale BookInternationalFlight a BookDomesticFlight jsou příliš podobné. Pokud váš systém potřebuje je odlišit, použijte entity nebo jiné logiky, nikoli záměry. 

### <a name="dispatcher-model"></a>Dispečermodel
Další informace o kombinování aplikací LUIS a QnA maker s [modelem odeslání](luis-concept-enterprise.md#when-you-need-to-combine-several-luis-and-qna-maker-apps). 

### <a name="request-help-for-apps-with-significant-number-of-intents"></a>Žádost o pomoc pro aplikace s významným počtem záměrů
Pokud snížení počtu záměrů nebo rozdělení záměrů do více aplikací pro vás nefunguje, obraťte se na podporu. Pokud vaše předplatné Azure zahrnuje služby podpory, obraťte se na [technickou podporu Azure](https://azure.microsoft.com/support/options/). 

## <a name="next-steps"></a>Další kroky

* Další informace o [entitách](luis-concept-entity-types.md), což jsou důležitá slova relevantní pro záměry
* Přečtěte si, jak [přidávat a spravovat záměry](luis-how-to-add-intents.md) v aplikaci LUIS.
* Zkontrolujte [doporučené postupy záměru](luis-concept-best-practices.md)
