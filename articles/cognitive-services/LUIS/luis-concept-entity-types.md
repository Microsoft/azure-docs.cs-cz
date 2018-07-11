---
title: Principy typů entit v aplikacích LUIS v Azure | Dokumentace Microsoftu
description: Přidání entity (klíčových dat v doméně vaší aplikace) v aplikacích Language Understanding Intelligent Service (LUIS).
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 06/28/2018
ms.author: v-geberr
ms.openlocfilehash: 7dc3d0ce70ca0df2c5cf951ae53ee0566451801f
ms.sourcegitcommit: a1e1b5c15cfd7a38192d63ab8ee3c2c55a42f59c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/10/2018
ms.locfileid: "37952803"
---
# <a name="entities-in-luis"></a>Entity v LUIS

Entity jsou slova nebo fráze v projevy, které jsou data klíče v doméně vaší aplikace.

## <a name="entity-compared-to-intent"></a>Entity ve srovnání s cílem
Entita představuje slovo nebo frázi v utterance, který chcete, aby byl extrahován. Utterance může obsahovat mnoho entit nebo žádný vůbec. Entity představuje třídu kolekce podobných objektů (místa, věci, osoby, události nebo koncepty) včetně. Entity popisují informace související s cílem a někdy jsou nezbytné pro vaši aplikaci k provedení svých úkolů. Pro vyhledávání zpráv aplikace může například obsahovat entity, jako je například "tematické", "zdroj", "– klíčové slovo" a "datum publikování", které jsou klíčová data pro vyhledávání zpráv. Cestovní aplikaci rezervace, "umístění", "data", "letecká společnost" "cesty třída" a "lístků" jsou informace o klíči pro rezervace letu (relevantní k příslušnému záměru "Bookflight").

Naproti tomu představuje záměr předpovědi celý utterance. 

## <a name="entities-represent-data"></a>Entity představují data
Entity jsou data, která chcete načítat utterance. To může být název, datum, název produktu nebo jakákoli skupina slova. 

|Promluva|Entita|Data|
|--|--|--|
|Zakoupit 3 lístky pro New York|Předem připravené číslo<br>Location.Destination|3<br>New York|
|Nákup lístků z New Yorku do Londýna na 5. března|Location.Origin<br>Location.Destination<br>Předem připravené datetimeV2|New York<br>Londýn<br>5. března 2018|

## <a name="entities-are-optional-but-highly-recommended"></a>Entity jsou volitelné, ale důrazně doporučené
I když záměry povinné, entity jsou volitelné. Nemusíte vytvářet entity pro každý koncept ve vaší aplikaci, ale pouze pro tyto aplikace vyžaduje k akci. 

Pokud vaše projevy nemají podrobnosti, které váš robot je potřeba pokračovat, není potřeba je přidat. Během existence vaší aplikace, můžete je přidat později. 

Pokud si nejste jisti, jak můžete využít informace, přidáte pár běžných předem připravených entit, jako je například datetimeV2 pořadovém místě, e-mailu a telefonní číslo.

## <a name="label-for-word-meaning"></a>Popisek pro význam slova
Pokud volba slovo nebo slovo uspořádání je stejný, ale není to samé, není popisek s entitou. 

Následující projevy, slovo `fair` je homograf. Je napsána stejný, ale má odlišný význam:

```
What kind of county fairs are happening in the Seattle area this summer?
Is the current rating for the Seattle review fair?
```

Pokud byste chtěli entity událost najít všechna data událostí, popisek slovo `fair` v první utterance, ale nikoli do druhého.

## <a name="entities-are-shared-across-intents"></a>Entity jsou sdíleny napříč záměrů
Entity jsou sdíleny mezi záměry. Nepatří do žádné jeden záměr. Záměry a entity můžou být sémanticky přidružené ale není výhradní vztah.

V utterance "rezervovat mě lístek do Paříže", "Paříž" je entita typu umístění. Služba LUIS umožňuje tím, že rozpoznává entity, které jsou uvedené ve vstupu uživatele, zvolte konkrétní akce ke splnění záměru.

## <a name="assign-entities-in-none-intent"></a>Přiřadit entity v žádné záměru
Všechny příkazy, včetně **žádný** záměr, by měly mít entity s popiskem. To pomáhá LUIS Další informace o kde entity, které jsou v projevy a slova jsou kolem entity. 

## <a name="types-of-entities"></a>Typy entit
Služba LUIS nabízí mnoho typů entit; předem připravených entit, vlastního počítače se naučili a seznam entit.

| Název | Lze označit | Popis |
| -- |--|--|
| **Předem připravené** <br/>[Vlastní](#prebuilt)| |  **Definice**<br>Vestavěné typy, které představují běžné koncepty. <br><br>**Seznam**<br/>počet klíčových frází, pořadí, teploty, dimenze, peníze, věk, procento, e-mailu, adresa URL, telefonní číslo a klíčových frází. <br><br>Názvy předem připravených entit, jsou vyhrazena. <br><br>Všechny předem připravených entit, které jsou přidané do aplikace jsou vráceny v [koncový bod](luis-glossary.md#endpoint) dotazu. Další informace najdete v tématu [předem připravených entit](./luis-prebuilt-entities.md). <br/><br/>[Příklad odpovědi pro entitu](luis-concept-data-extraction.md#prebuilt-entity-data)|
|<!-- added week of 3/21/08 --> **Regulární výraz**<br/>[Regulární výraz](#regex)||**Definice**<br>Vlastní regulárního výrazu pro text formátovaný nezpracovaná utterance. Ignoruje velikost písmen a ignoruje kulturní variant.  <br><br>Tato entita je vhodný pro slova nebo fráze, které jsou s jakékoli změny, které je konzistentní s konzistentním formátováním.<br><br>Porovnávání regulárních výrazů se použije po změny pro kontrolu pravopisu. <br><br>Pokud regulární výraz je příliš složitý, jako je třeba použití mnoha závorky, nejste schopni přidat výraz do modelu. <br><br>**Příklad**<br>`kb[0-9]{6,}` odpovídá kb123456.<br/><br/>[Rychlý start](luis-quickstart-intents-regex-entity.md)<br>[Příklad odpovědi pro entitu](luis-concept-data-extraction.md)|
| **Jednoduché** <br/>[Zjištěné počítače](#machine-learned) | ✔ | **Definice**<br>Jednoduché entita je obecné entity, která popisuje jeden koncept a zjistili díky spolupráci se naučili počítač kontextu. Kontext obsahovat výběr aplikace word, slovo umístění a utterance délka.<br/><br/>To je dobrý entita slov nebo slovních spojení, které nejsou naformátovány konzistentně, ale označují stejnou věc. <br/><br/>[Rychlý start](luis-quickstart-primary-and-secondary-data.md)<br/>[Příklad odpovědi pro entitu](luis-concept-data-extraction.md#simple-entity-data)|  
| **Seznam** <br/>[Přesná shoda](#exact-match)|| **Definice**<br>Seznam entit představují sadu související slova spolu s jejich synoymns pevné, uzavřené v systému. <br><br>Každá entita seznamu může mít jeden nebo více formulářů. Nejvhodnější pro známé sady změn v způsoby, jak reprezentaci stejný koncept.<br/><br/>Služba LUIS nevyhledává další hodnoty pro seznam entit. Použití zobrazíte [sémantické slovníku](luis-glossary.md#semantic-dictionary) najít návrhy nových slov na základě aktuálního seznamu.<br/><br>Pokud existuje více než jednu entitu seznamu se stejnou hodnotou, je každá entita vrácené dotazem koncový bod. <br/><br/>[Rychlý start](luis-quickstart-intent-and-list-entity.md)<br>[Příklad odpovědi pro entitu](luis-concept-data-extraction.md#list-entity-data)| 
| **Pattern.Any** <br/>[Smíšené](#mixed) | ✔|**Definice**<br>Patterns.any je použít jenom v utterance vzor šablony k označení, ve kterém entita začíná a končí zástupným symbolem proměnné délky.  <br><br>**Příklad**<br>Zadaný utterance hledat podle názvu knihy, extrahuje pattern.any úplný název. Je šablona utterance pomocí pattern.any `Who wrote {BookTitle}[?]`.<br/><br/>[Kurz](luis-tutorial-pattern.md)<br>[Příklad odpovědi pro entitu](luis-concept-data-extraction.md#composite-entity-data)|  
| **Složené** <br/>[Zjištěné počítače](#machine-learned) | ✔|**Definice**<br>Složený entity je tvořena jinými entitami, jako je například předem připravených entit a jednoduché. Samostatné entity tvoří celé entity. Seznam entit nejsou povoleny v složený entity. <br><br>**Příklad**<br>Složený entita s názvem PlaneTicketOrder může obsahovat podřízené entity předem připravených `number` a `ToLocation`. <br/><br/>[Kurz](luis-tutorial-composite-entity.md)<br>[Příklad odpovědi pro entitu](luis-concept-data-extraction.md#composite-entity-data)|  
| **Hierarchické** <br/>[Zjištěné počítače](#machine-learned) |✔ | **Definice**<br>Hierarchické entita je kategorie kontextově zjištěná entit.<br><br>**Příklad**<br>Zadaný hierarchické entitu `Location` s podřízenými `ToLocation` a `FromLocation`, jednotlivých podřízených můžete určit, na základě **kontextu** v rámci utterance. V utterance `Book 2 tickets from Seattle to New York`, `ToLocation` a `FromLocation` kontextově liší na základě slova, co s nimi souvisí. <br/><br/>**Nepoužívejte, pokud**<br>Pokud hledáte entitu, která má přesný text shody pro podřízené položky bez ohledu na kontextu, měli byste použít seznam entit. Pokud hledáte vztah nadřízenosti a podřízenosti s jinými typy entit, měli byste použít složené entity.<br/><br/>[Rychlý start](luis-quickstart-intent-and-hier-entity.md)<br>[Příklad odpovědi pro entitu](luis-concept-data-extraction.md#hierarchical-entity-data)|

<a name="prebuilt"></a>
**Předem připravené** entity jsou poskytované služby LUIS vlastní entity. Některé z těchto entit jsou definovány v open-source [rozpoznávání textu](https://github.com/Microsoft/Recognizers-Text) projektu. Existuje několik instancí [příklady](https://github.com/Microsoft/Recognizers-Text/tree/master/Specs) v adresáři /Specs pro podporované jazykové verze. Pokud konkrétní jazykovou verzi nebo entity se aktuálně nepodporuje, přispět k projektu. 

<a name="machine-learned"></a>
**Zjištěné počítače** entity fungují lépe, když testován prostřednictvím [koncový bod dotazy](luis-concept-test.md#endpoint-testing) a [recenzování projevy koncový bod](luis-how-to-review-endoint-utt.md). 

<a name="regex"></a>
**Regulární výraz entity** jsou definovány pomocí regulárního výrazu, který uživatel zadá jako součást definice entity. 

<a name="exact-match"></a>
**Přesnou shodu** entity použití textu poskytnutého v entitě aby text na přesně shodovat.

<a name="mixed"></a>
**Smíšené** entity pomocí kombinace metod zjišťování entit.

## <a name="entity-limits"></a>Omezení entity
Kontrola [omezení](luis-boundaries.md#model-boundaries) porozumět, kolik jednotlivých typů entit můžete přidat do modelu.

## <a name="entity-roles"></a>Entita role
Entity [role](luis-concept-roles.md) se používají ve vzorcích pouze. 

## <a name="composite-vs-hierarchical-entities"></a>Složený vs hierarchické entity
Složené a hierarchické entit jak mají vztahů nadřazenosti a podřízenosti a jsou počítače se naučili. Machine learning umožňuje LUIS vysvětlení konceptu entit podle různých kontextech (uspořádání slov). Složený entity jsou flexibilnější, protože umožňují typy různých entit jako podřízené objekty. Hierarchické entity podřízené objekty jsou pouze jednoduché entity. 

|Typ|Účel|Příklad:|
|--|--|--|
|Hierarchické|Nadřazený podřízený jednoduché entit|Location.Origin=New York<br>Location.Destination=London|
|Složené|Nadřazené podřízené entity: předem připravených, seznam, jednoduchý a hierarchické| číslo = 3<br>Seznam = první třídy<br>prebuilt.datetimeV2=March 5|

## <a name="data-matching-multiple-entities"></a>Data odpovídající více entit
Pokud slovo nebo frázi, odpovídá více než jednu entitu, koncový bod dotaz vrací jednotlivé entity. Pokud přidáte číslo předem připravených entit a události prebuild datetimeV2 a mít utterance `create meeting on 2018/03/12 for lunch with wayne`, LUIS, rozpozná všechny entity a vrátí pole entit jako součást koncového bodu odpověď JSON: 

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

## <a name="data-matching-multiple-list-entities"></a>Data odpovídající více seznam entit
Pokud slovo nebo frázi, odpovídá více než jednu entitu seznamu, koncový bod dotaz vrátí Každá entita seznamu.

Pro dotaz `when is the best time to go to red rock?`, a aplikace obsahuje slovo `red` ve více než jeden seznam, LUIS, rozpozná všechny entity a vrátí pole entit jako součást koncového bodu odpověď JSON: 

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

Můžete potřebovat použít hierarchická a složené entit. Hierarchické entity odrážet relace mezi entitami, které mají vlastnosti nebo jsou členy skupiny. Podřízené entity jsou všechny členy své nadřazené kategorie. Například hierarchické entitu s názvem PlaneTicketClass pravděpodobně podřízené entity EconomyClass a FirstClass. Hierarchie obsahuje pouze jednu úroveň hloubky.  

Složený entity představují část celku. Například složenou entitu s názvem PlaneTicketOrder může mít podřízené entity letecká společnost, cíl, DepartureCity, datumOdjezdu a PlaneTicketClass. Vytváření složených entity z již existujících jednoduché entit, podřízené položky hierarchické entity nebo předem připravených entit.  

Služba LUIS také poskytuje seznam typ entity, která není se naučili počítače, ale umožňuje aplikaci LUIS, chcete-li určit pevnou seznam hodnot. Zobrazit [LUIS hranice](luis-boundaries.md) odkazu ke kontrole omezení typu entity. 

Pokud jste za hierarchické, složený a seznamu entit a stále potřebovat vyšší limit, obraťte se na podporu. Uděláte to tak, získat podrobné informace o systému, přejděte [LUIS](luis-reference-regions.md#luis-website) webu a pak vyberte **podporu**. Pokud vaše předplatné Azure zahrnuje odbornou pomoc, obraťte se na [technické podpoře Azure](https://azure.microsoft.com/support/options/). 

## <a name="best-practices"></a>Osvědčené postupy

Vytvoření [entity](luis-concept-entity-types.md) když volající aplikaci nebo robotovi potřebuje některé parametry nebo data z utterance potřebných k provedení akce. Entita je slovo nebo frázi v utterance, které potřebujete extrahovat – možná jako parametr pro funkci. 

Abyste mohli vybrat správný typ entitu, kterou chcete přidat do svojí aplikace, budete muset vědět, jak tato data je zadané uživateli. Každý typ entity se našlo se pomocí jiným způsobem, jako je machine learning, uzavřené seznamu nebo regulární výraz. Pokud si nejste jistí, začněte s jednoduchou entitu a označovat slovo nebo frázi, která představuje data v všechny projevy, přes všechny záměry včetně žádný záměru.  

Projděte si projevy koncový bod v pravidelných intervalech se najít běžné použití, ve kterém můžete entity identifikované jako regulární výraz nebo najít s shodou přesný text.  

V rámci kontroly zvažte možnost Přidat frázi seznamu se přidá signál k LUIS slov nebo slovních spojení, které jsou důležité pro vaši doménu, ale nejsou přesné shody, a pro které služba LUIS nemá vysokou spolehlivostí.  

Zobrazit [osvědčené postupy](luis-concept-best-practices.md) Další informace.

## <a name="next-steps"></a>Další postup

Další koncepty o dobré [projevy](luis-concept-utterance.md). 

Zobrazit [přidat entity](luis-how-to-add-entities.md) získat další informace o přidání entity do aplikace LUIS.