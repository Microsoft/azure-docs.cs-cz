---
title: Seznámení s typy entit v aplikacích LEOŠ v Azure | Microsoft Docs
description: Přidání entity (klíče data v doméně vaší aplikace) v aplikacích jazyka Principy inteligentního služby (LEOŠ).
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 05/22/2018
ms.author: v-geberr
ms.openlocfilehash: 918f5d9efa1163558e44c2c67028dbf802f479a5
ms.sourcegitcommit: 301855e018cfa1984198e045872539f04ce0e707
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/19/2018
ms.locfileid: "36266783"
---
# <a name="entities-in-luis"></a>Entity v LEOŠ

Entity jsou slova nebo fráze v utterances, které jsou klíčová data v doméně vaší aplikace.

## <a name="entity-compared-to-intent"></a>Ve srovnání s záměr entity
Entita představuje slovo nebo frázi uvnitř utterance, který chcete extrahované. Utterance může obsahovat mnoho entit nebo hodnotu none vůbec. Entita představuje třídu, včetně kolekce podobně jako objekty (míst, věcí, osoby, události nebo koncepty). Entity popisují informace, které jsou relevantní pro záměr a někdy jsou důležité pro vaši aplikaci k provedení úkolu. Vyhledávání zprávy aplikace může obsahovat třeba entity, jako je například "tématu", "zdroj", "– klíčové slovo" a "datu publikování", které jsou klíčová data pro vyhledávání zprávy. V aplikaci rezervace cesta, "místo", "datum", "letecká společnost" "cesta třída" a "lístky" jsou informace o klíči pro letu rezervace (relevantní pro záměr "Bookflight").

Pro srovnání představuje záměr předpovědi celý utterance. 

## <a name="entities-represent-data"></a>Entity představují data
Entity jsou data, která chcete načítat z utterance. To může být název, datum, název produktu nebo libovolnou skupinu slova. 

|Utterance|Entita|Data|
|--|--|--|
|Zakoupit 3 lístky pro Brno|Předkompilované číslo<br>Location.Destination|3<br>New York|
|Koupit lístek z New Yorku do Londýna na března 5|Location.Origin<br>Location.Destination<br>Předkompilované datetimeV2|New York<br>Londýn<br>5. března 2018|

## <a name="entities-are-optional-but-highly-recommended"></a>Entity jsou volitelné, ale důrazně doporučené
Při záměry jsou požadovány, jsou volitelné entity. Není nutné k vytváření entit pro každý koncept ve vaší aplikaci, ale jenom pro požadované aplikace provádět akci. 

Pokud vaše utterances nemají podrobnosti, které vaše robota musí pokračovat, není nutné je přidat. Během existence vaší aplikace, můžete je přidat později. 

Pokud si nejste jisti, jak byste použili informace, přidáte pár běžných předem entitami, jako je například datetimeV2 pořadí, e-mailu a telefonní číslo.

## <a name="label-for-word-meaning"></a>Popisek pro význam aplikace word
Pokud volba slovo nebo slovo uspořádání je stejný, ale neznamená samé, není popisku ho s entitou. 

Následující utterances, slovo `fair` je homograf. Je napsaný stejné, ale má jiný význam:

```
What kind of county fairs are happening in the Seattle area this summer?
Is the current rating for the Seattle review fair?
```

Pokud byste chtěli entitu událostí najít data všech událostí, označení slovo `fair` v první utterance, ale ne za sekundu.

## <a name="entities-are-shared-across-intents"></a>Entity jsou sdíleny záměry
Entity jsou sdílena mezi tříd Intent. Jejich nepatří do žádné jednoho záměr. Tříd Intent a entity lze sémanticky přidružit, ale není výhradní vztah.

V utterance "sešit mi lístek do Paříž", "Paříž" je entita typu umístění. Tím, že rozpoznává entitami, které jsou uvedené ve vstupu uživatele, LEOŠ vám pomůže vybrat konkrétní akce, které ke splnění záměrem.

## <a name="assign-entities-in-none-intent"></a>Přiřadit entity v žádné záměrné
Všechny záměry, včetně **žádné** záměr, by měly mít entity s názvem bez přípony. To pomáhá LEOŠ Další informace o kde entity, které jsou v utterances a slova jsou kolem entity. 

## <a name="types-of-entities"></a>Typy entit
LEOŠ nabízí mnoho typů entit; Předkompilované entity vlastní počítače se naučili a seznamu entit.

| Název | Může označovat | Popis |
| -- |--|--|
| **Předkompilované** <br/>[Vlastní](#prebuilt)| |  **Definice**<br>Vestavěné typy, které představují běžné koncepty. <br><br>**seznam**<br/>číslo klíče frázi, pořadí, teploty, dimenze, peníze, stáří, procento, e-mailu, adresa URL, telefonní číslo a klíče frázi. <br><br>Předkompilované entity názvy jsou vyhrazené. <br><br>Všechny předem entity, které jsou přidány do aplikace jsou vrácena jako [koncový bod](luis-glossary.md#endpoint) dotazu. Další informace najdete v tématu [předem entity](./Pre-builtEntities.md). <br/><br/>[Příklad odpovědi pro entitu](luis-concept-data-extraction.md#prebuilt-entity-data)|
|<!-- added week of 3/21/08 --> **Regulární výraz**<br/>[Regulární výraz](#regex)||**Definice**<br>Vlastní regulární výraz formátovaný text. Se ignoruje velikost písmen a ignoruje kulturního variant.  <br><br>Tato entita je vhodný pro hesla či fráze konzistentně formátovaných pomocí jakékoli změny, která je konzistentní.<br><br>Odpovídající regulární výraz se použije po změnách kontrolu pravopisu v podniku. <br><br>Pokud regulární výraz je příliš složitý, jako je třeba použití mnoha závorky, nejsou budete moct přidat výraz do modelu. <br><br>**Příklad**<br>`kb[0-9]{6,}` odpovídá kb123456.<br/><br/>[Rychlý start](luis-quickstart-intents-regex-entity.md)<br>[Příklad odpovědi pro entitu](luis-concept-data-extraction.md)|
| **Jednoduché** <br/>[Naučili počítače](#machine-learned) | ✔ | **Definice**<br>Jednoduché entity je obecný entita, která popisuje jeden koncept a získané z počítače naučili kontextu. Kontext zahrnují volba word, word umístění a utterance délka.<br/><br/>Toto je dobré entity slova nebo fráze, které nejsou konzistentní formátovány ale indikovat samé. <br/><br/>[Rychlý start](luis-quickstart-primary-and-secondary-data.md)<br/>[Příklad odpovědi pro entitu](luis-concept-data-extraction.md#simple-entity-data)|  
| **seznam** <br/>[Přesná shoda](#exact-match)|| **Definice**<br>Seznam entity představují sadu související slova spolu s jejich synoymns pevné, uzavřené v systému. <br><br>Každá entita seznamu může mít jeden nebo více formulářů. Nejvhodnější pro sadu varianty způsoby, jak představují stejný koncept známý.<br/><br/>LEOŠ nevyhledává další hodnoty pro seznam entity. Použití zobrazíte [sémantického slovník](luis-glossary.md#semantic-dictionary) najít návrhy nových slov podle aktuálního seznamu.<br/><br>Pokud existuje více než jedna entita seznamu se stejnou hodnotou, vrátí se v dotazu koncový bod každé entity. <br/><br/>[Rychlý start](luis-quickstart-intent-and-list-entity.md)<br>[Příklad odpovědi pro entitu](luis-concept-data-extraction.md#list-entity-data)| 
| **Pattern.Any** <br/>[Ve smíšeném](#mixed) | ✔|**Definice**<br>Patterns.Any představuje proměnnou délkou použít pouze v utterance vzor šablony k označení, kde entity zahájení a ukončení.  <br><br>**Příklad**<br>Vzhledem utterance hledat podle názvu knihy, pattern.any extrahuje úplný název. Je utterance šablony pomocí pattern.any `Who wrote {BookTitle}[?]`.<br/><br/>[Kurz](luis-tutorial-pattern.md)<br>[Příklad odpovědi pro entitu](luis-concept-data-extraction.md#composite-entity-data)|  
| **Složené** <br/>[Naučili počítače](#machine-learned) | ✔|**Definice**<br>Složené entity se skládá z jinými entitami, jako je například předem entity a jednoduché. Samostatné entity formuláři celou entity. Seznam entity nejsou povoleny v složené entity. <br><br>**Příklad**<br>Složené entity s názvem PlaneTicketOrder může mít podřízených entit předem `number` a `ToLocation`. <br/><br/>[Kurz](luis-tutorial-composite-entity.md)<br>[Příklad odpovědi pro entitu](luis-concept-data-extraction.md#composite-entity-data)|  
| **Hierarchická** <br/>[Naučili počítače](#machine-learned) |✔ | **Definice**<br>Hierarchická entita je kategorii kontextově zjištěné entity.<br><br>**Příklad**<br>Zadané hierarchické entity `Location` s podřízenými položkami `ToLocation` a `FromLocation`, jednotlivých podřízených lze určit podle **kontextu** v rámci utterance. V utterance `Book 2 tickets from Seattle to New York`, `ToLocation` a `FromLocation` kontextově liší na základě slova je obcházet. <br/><br/>**Nepoužívejte Pokud**<br>Pokud hledáte entitu, která má odpovídá přesný text pro děti bez ohledu na kontext, měli byste použít seznam entity. Pokud hledáte relaci nadřazený podřízený s jinými typy entit, měli byste použít složené entity.<br/><br/>[Rychlý start](luis-quickstart-intent-and-hier-entity.md)<br>[Příklad odpovědi pro entitu](luis-concept-data-extraction.md#hierarchical-entity-data)|

<a name="prebuilt"></a>
**Předkompilované** entity jsou poskytované LEOŠ vlastní entity. Některé z těchto entitách jsou definovány v open-source [rozpoznávání Text](https://github.com/Microsoft/Recognizers-Text) projektu. Existuje mnoho [příklady](https://github.com/Microsoft/Recognizers-Text/tree/master/Specs) v adresáři /Specs pro podporované jazykové verze. Pokud konkrétní jazykové verze nebo entity není aktuálně podporován, přispívat do projektu. 

<a name="machine-learned"></a>
**Naučili počítač** entity se nejlépe fungovat při testování prostřednictvím [koncový bod dotazy](luis-concept-test.md#endpoint-testing) a [kontrola utterances koncový bod](label-suggested-utterances.md). 

<a name="regex"></a>
**Regulární výraz entity** jsou definovány jako regulární výraz uživatele poskytuje jako součást definice entity. 

<a name="exact-match"></a>
**Přesnou shodu** entity použijte text zadaný v entitě aby text na přesně shodovat.

<a name="mixed"></a>
**Smíšený** entity použít kombinaci metod detekce entity.

## <a name="entity-limits"></a>Omezení entity
Zkontrolujte [omezení](luis-boundaries.md#model-boundaries) zjistit, kolik každý typ entity, můžete přidat k modelu.

## <a name="composite-vs-hierarchical-entities"></a>Hierarchická entity složené vs
Složené a hierarchické entit mít vztahů nadřazenosti a podřízenosti i se naučili počítače. Strojové učení umožňuje LEOŠ pochopit entity podle různých kontextů (uspořádání slova). Složené entity jsou flexibilnější, protože jiné entity typy jako podřízené objekty. Hierarchická entity podřízené objekty jsou pouze jednoduché entity. 

|Typ|Účel|Příklad:|
|--|--|--|
|Hierarchická|Nadřazený podřízený jednoduché entit|Location.Origin=New York<br>Location.Destination=London|
|Složené|Nadřazený podřízený entity: předem, seznamu jednoduchý, hierarchické| počet = 3<br>Seznam = první – třída<br>prebuilt.datetimeV2=March 5|

## <a name="data-matching-multiple-entities"></a>Data odpovídající více entit
Pokud slovo nebo frázi odpovídá více než jedna entita, koncový bod dotaz vrátí každé entity. Pokud přidáte předem číslo entity a události prebuild datetimeV2 a mít utterance `create meeting on 2018/03/12 for lunch with wayne`, LEOŠ rozpozná všechny entity a vrátí pole entit v rámci odpovědi JSON koncového bodu: 

```JSON
{
  "query": "create meeting on 2018/03/12 for lunch with wayne",
  "topScoringIntent": {
    "intent": "Calendar.Add",
    "score": 0.9333419
  },
  "entities": [
    {
      "entity": "2018/03/12",
      "type": "builtin.datetimeV2.date",
      "startIndex": 18,
      "endIndex": 27,
      "resolution": {
        "values": [
          {
            "timex": "2018-03-12",
            "type": "date",
            "value": "2018-03-12"
          }
        ]
      }
    },
    {
      "entity": "2018",
      "type": "builtin.number",
      "startIndex": 18,
      "endIndex": 21,
      "resolution": {
        "value": "2018"
      }
    },
    {
      "entity": "03/12",
      "type": "builtin.number",
      "startIndex": 23,
      "endIndex": 27,
      "resolution": {
        "value": "0.25"
      }
    }
  ]
}
```

## <a name="data-matching-multiple-list-entities"></a>Data odpovídající více seznamu entit
Pokud slovo nebo frázi odpovídá více než jedna entita seznamu, koncový bod dotaz vrátí každé entity seznamu.

Pro dotaz `when is the best time to go to red rock?`, a aplikace má slovo `red` ve více než jeden seznam LEOŠ rozpozná všechny entity a vrátí pole entit v rámci odpovědi JSON koncového bodu: 

```JSON
{
  "query": "when is the best time to go to red rock?",
  "topScoringIntent": {
    "intent": "Calendar.Find",
    "score": 0.06701678
  },
  "entities": [
    {
      "entity": "red",
      "type": "Colors",
      "startIndex": 31,
      "endIndex": 33,
      "resolution": {
        "values": [
          "Red"
        ]
      }
    },
    {
      "entity": "red rock",
      "type": "Cities",
      "startIndex": 31,
      "endIndex": 38,
      "resolution": {
        "values": [
          "Destinations"
        ]
      }
    }
  ]
}
```

## <a name="if-you-need-more-than-the-maximum-number-of-entities"></a>Pokud potřebujete více než maximální počet entit 

Budete se muset použít hierarchické a složené entity. Hierarchická entity projeví vztah mezi entitami, které sdílí charakteristiky nebo jsou členy kategorie. Podřízených entit jsou všichni členové jejich nadřazené kategorie. Například hierarchické entity s názvem PlaneTicketClass pravděpodobně podřízených entit EconomyClass a FirstClass. Pouze jedna úroveň hloubky rozdělena na hierarchii.  

Složené entity představují část celek. Například složenou entitu s názvem PlaneTicketOrder pravděpodobně podřízených entit letecká společnost, cílový, DepartureCity, datumOdjezdu a PlaneTicketClass. Sestavování složené entity z existující jednoduché entit, děti hierarchické entit nebo předem entity.  

LEOŠ taky poskytuje seznam typ entity, který naučili počítač není ale umožníte své aplikaci LEOŠ zadejte pevný seznam hodnot. V tématu [LEOŠ hranice](luis-boundaries.md) odkaz na zkontrolujte omezení typu entity. 

Pokud jste považována za hierarchické, složený a seznamu entit a stále je třeba víc než limit, kontaktujte podporu. Uděláte to tak shromáždit podrobné informace o systému, přejděte na [LEOŠ] [ LUIS] webu a pak vyberte **podporu**. Pokud vaše předplatné zahrnuje služby podpory, obraťte se na [Azure technickou podporu](https://azure.microsoft.com/support/options/). 

## <a name="best-practices"></a>Osvědčené postupy

Vytvoření [entity](luis-concept-entity-types.md) když volající aplikace nebo robota potřebuje některé parametry nebo data z utterance požadované k provedení akce. Entita je slovo nebo frázi v utterance, které potřebujete – případně extrahovat jako parametr pro funkci. 

Chcete-li vybrat správný typ entity, které chcete přidat do vaší aplikace, je třeba vědět, jak se tato data zadané uživateli. Každému typu entity, se zjistí pomocí různých mechanismu, jako je například seznam strojové učení, uzavřené nebo regulární výraz. Pokud si nejste jistí, začínat jednoduché entity a označovat slovo nebo frázi, představuje těmto datům ve všech utterances napříč všech tříd Intent včetně žádný záměrné.  

Zkontrolujte utterances koncového bodu v pravidelných intervalech najít běžné využití, kde můžete identifikovat jako regulární výraz nebo najít s shodou přesný text entity.  

Jako součást kontrola zvažte přidání frázi seznamu pro přidání signál LEOŠ slova nebo fráze, které jsou důležité pro vaši doménu, ale nejsou přesné shody, a pro které LEOŠ nemá vysokou spolehlivostí.  

V tématu [osvědčené postupy](luis-concept-best-practices.md) Další informace.

## <a name="next-steps"></a>Další postup

Další informace o dobré koncepty [utterances](luis-concept-utterance.md). 

V tématu [přidat entity](luis-how-to-add-entities.md) Další informace o tom, jak přidat do aplikace LEOŠ entity.

[LUIS]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions#luis-website