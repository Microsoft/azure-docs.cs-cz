---
title: Typy entit
titleSuffix: Language Understanding - Azure Cognitive Services
description: 'Entity extrahují data z utterance. Typy entit poskytují předvídatelné extrakci dat. Existují dva typy entit: počítač-se naučil a počítač se naučil. Je důležité znát, ve kterém typu entity pracujete v projevy.'
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 07/24/2019
ms.author: diberry
ms.openlocfilehash: a5a3ba8c25107317e7c47ee358f9a6ebe7d4556f
ms.sourcegitcommit: 75a56915dce1c538dc7a921beb4a5305e79d3c7a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/24/2019
ms.locfileid: "68479114"
---
# <a name="entity-types-and-their-purposes-in-luis"></a>Typy entit a jejich účely v LUIS

Entity extrahují data z utterance. Typy entit poskytují předvídatelné extrakci dat. Existují dva typy entit: počítač-se naučil a počítač se naučil. Je důležité znát, ve kterém typu entity pracujete v projevy. 

## <a name="entity-compared-to-intent"></a>Entity ve srovnání s cílem

Entita představuje slovo nebo frázi v utterance, který chcete, aby byl extrahován. Utterance může obsahovat mnoho entit nebo žádný vůbec. Klientská aplikace může potřebovat entitu k provedení její úlohy nebo ji použít jako vodítko pro zobrazení několika možností pro uživatele. 

Entita:

* Představuje třídu, včetně kolekce podobných objektů (míst, věcí, osob, událostí nebo konceptů). 
* Popisuje informace týkající se záměru.


Pro vyhledávání zpráv aplikace může například obsahovat entity, jako je například "tematické", "zdroj", "– klíčové slovo" a "datum publikování", které jsou klíčová data pro vyhledávání zpráv. V aplikaci pro rezervaci cest jsou klíčové informace pro rezervaci letu (důležité pro záměr "let", "datum", "letecká", "cestovní třída" a "lístky".

Naproti tomu představuje záměr předpovědi celý utterance. 

## <a name="entities-help-with-data-extraction-only"></a>Informace o entitách s extrakcí dat

Entity můžete označovat nebo označovat jenom pro účely extrakce entity, ale nemůžete s předpověďmi záměrů.

## <a name="entities-represent-data"></a>Entity představují data

Entity jsou data, která chcete načítat utterance. To může být název, datum, název produktu nebo jakákoli skupina slova. 

|Promluva|Entita|Data|
|--|--|--|
|Zakoupit 3 lístky pro New York|Předem připravené číslo<br>Location.Destination|3<br>New York|
|Nákup lístků z New Yorku do Londýna na 5. března|Location.Origin<br>Location.Destination<br>Předem připravené datetimeV2|New York<br>Londýn<br>5\. března 2018|

## <a name="entities-are-optional-but-highly-recommended"></a>Entity jsou volitelné, ale důrazně doporučené

I když záměry povinné, entity jsou volitelné. Nemusíte vytvářet entity pro každý koncept ve vaší aplikaci, ale jenom pro ty, které vyžaduje, aby klientská aplikace provedla akci. 

Pokud vaše projevy nemají podrobnosti, které váš robot je potřeba pokračovat, není potřeba je přidat. Během existence vaší aplikace, můžete je přidat později. 

Pokud si nejste jistí, jak byste tyto informace použili, přidejte několik běžných předem vytvořených entit, jako je [datetimeV2](luis-reference-prebuilt-datetimev2.md), [ordinální](luis-reference-prebuilt-ordinal.md)číslo, [e-mail](luis-reference-prebuilt-email.md)a [telefonní číslo](luis-reference-prebuilt-phonenumber.md).

## <a name="label-for-word-meaning"></a>Popisek pro význam slova

Pokud volba slovo nebo slovo uspořádání je stejný, ale není to samé, není popisek s entitou. 

Následující projevy, slovo `fair` je homograf. Je napsána stejný, ale má odlišný význam:

|Promluva|
|--|
|Jaký druh county veletrhů se dějí v Seattlu toto léto?|
|Je aktuální hodnocení ke kontrole Seattle přiměřená?|

Pokud byste chtěli entity událost najít všechna data událostí, popisek slovo `fair` v první utterance, ale nikoli do druhého.

## <a name="entities-are-shared-across-intents"></a>Entity jsou sdíleny napříč záměrů

Entity jsou sdíleny mezi záměry. Nepatří do žádné jeden záměr. Záměry a entity je možné sémanticky přidružit, ale nejedná se o výhradní vztah.

V adresáři utterance "lístek na dotaz na Paříž", "Paříž" je entita odkazující na umístění. Díky rozpoznávání entit, které jsou zmíněné v utterance uživatele, LUIS pomáhá klientské aplikaci zvolit konkrétní akce, které se mají provést při plnění požadavku uživatele.

## <a name="mark-entities-in-none-intent"></a>Označit entity v žádném záměru

Všechny záměry, včetně záměru **none** , by měly mít označené entity, pokud je to možné. To pomáhá LUIS Další informace o kde entity, které jsou v projevy a slova jsou kolem entity. 

## <a name="entity-status-for-predictions"></a>Stav entity pro předpovědi

Portál LUIS vás upozorní, když se entita v ukázkovém utterance liší od označené entity nebo je příliš blízko jiné entitě, a proto ji nejasný. To je v příkladu utterance označeno červeným podtržením. 

Další informace najdete v tématu [předpovědi stavu entity](luis-how-to-add-example-utterances.md#entity-status-predictions). 

## <a name="types-of-entities"></a>Typy entit

LUIS nabízí mnoho typů entit. Vyberte entitu na základě toho, jak by měla být data extrahována a jak by měla být reprezentována po extrakci.

Entity se dají extrahovat pomocí strojového učení, které LUIS umožňuje pokračovat ve získávání informací o tom, jak se entita zobrazuje v utterance. Entity je možné extrahovat bez strojového učení, a to tak, že odpovídají přesně textům nebo regulárnímu výrazu. Entity ve vzorcích mohou být extrahovány smíšenou implementací. 

Jakmile je entita extrahována, mohou být data entity reprezentována jako jediná jednotka informací nebo kombinována s jinými entitami, aby mohla tvořit jednotku informací, které může klientská aplikace používat.

|Strojové učení|Může označovat|Kurz|Příklad<br>Odpověď|Typ entity|Účel|
|--|--|--|--|--|--|
|✔|✔|[✔](luis-tutorial-composite-entity.md)|[✔](luis-concept-data-extraction.md#composite-entity-data)|[**Náhled**](#composite-entity)|Seskupení entit bez ohledu na typ entity.|
|||[✔](luis-quickstart-intent-and-list-entity.md)|[✔](luis-concept-data-extraction.md#list-entity-data)|[**Seznamu**](#list-entity)|Seznam položek a jejich synonym, které byly extrahovány s přesnou shodou textu|
|Smíšený||[✔](luis-tutorial-pattern.md)|[✔](luis-concept-data-extraction.md#patternany-entity-data)|[**Vzor. any**](#patternany-entity)|Entita, kde je obtížné určit konec entity|
|||[✔](luis-tutorial-prebuilt-intents-entities.md)|[✔](luis-concept-data-extraction.md#prebuilt-entity-data)|[**Předem připravených**](#prebuilt-entity)|Již jste vyškoleni k extrakci různých druhů dat.|
|||[✔](luis-quickstart-intents-regex-entity.md)|[✔](luis-concept-data-extraction.md#regular-expression-entity-data)|[**Regulární výraz**](#regular-expression-entity)|Používá regulární výraz pro spárování textu.|
|✔|✔|[✔](luis-quickstart-primary-and-secondary-data.md)|[✔](luis-concept-data-extraction.md#simple-entity-data)|[**Pouh**](#simple-entity)|Obsahuje jeden koncept ve slovech nebo frázi.|

V příkladu projevy je potřeba označit jenom ty entity, které se strojově naučily. Entity náročné na počítač fungují nejlépe při testování prostřednictvím [dotazů na koncový bod](luis-concept-test.md#endpoint-testing) a [kontrole projevy koncového bodu](luis-how-to-review-endoint-utt.md). 

Vzor. všechny entity musí být označeny v příkladech šablony [vzoru](luis-how-to-model-intent-pattern.md) , nikoli v příkladech uživatele záměr. 

Smíšené entity používají kombinaci metod detekce entit.

## <a name="machine-learned-entities-use-context"></a>Entity, které se naučily počítač, používají kontext

Entity, které se naučily počítačem, se seznámí z kontextu v utterance. Díky tomu je variace umístění v příkladu projevy významná. 

## <a name="non-machine-learned-entities-dont-use-context"></a>Nepoužité entity, které se naučily počítač, nepoužívají kontext

Následující entity nenáročné na počítač neberou utterance kontext do účtu, když se shodují entity: 

* [Předem připravených entit](#prebuilt-entity)
* [Entity regulárního výrazu](#regular-expression-entity)
* [Seznam entit](#list-entity) 

Tyto entity nevyžadují popisky ani školení modelu. Jakmile přidáte nebo nakonfigurujete entitu, budou se tyto entity extrahovat. Kompromisem je, že tyto entity se můžou přerovnávat, kde if byl kontext přidaný do účtu, shoda se neudělala. 

K tomu dochází často s entitami seznam pro nové modely. Svůj model sestavíte a otestujete pomocí entity seznam, ale když publikujete model a přijmete dotazy z koncového bodu, provedete si, že se model přechází z důvodu nedostatku kontextu. 

Pokud chcete rozlišovat slova nebo fráze a přihlédnout do kontextu, máte dvě možnosti. Prvním je použití jednoduché entity spárované se seznamem frází. Seznam frází se nebude používat pro porovnání, ale v takovém případě bude pomáhat signalizovat relativně podobná slova (seznam s rozevíracími změnami). Pokud potřebujete přesně vyhledat místo variací seznamu frází, použijte entitu seznam s rolí, která je popsaná níže.

### <a name="context-with-non-machine-learned-entities"></a>Kontext s entitami, které nezískal počítač

Pokud chcete, aby kontext utterance v souvislosti s entitami, které se nestrojově naučily, měli byste použít [role](luis-concept-roles.md).

Pokud máte entitu, která se nejedná o počítač, například [předem připravené entity](#prebuilt-entity), [regulární výrazy](#regular-expression-entity) nebo entity [seznamu](#list-entity) , které se shodují nad požadovanou instancí, zvažte vytvoření jedné entity se dvěma rolemi. Jedna role zachytí, co hledáte, a jedna role zachytí, co nehledáte. Obě verze budou muset být označeny jako projevy.  

## <a name="composite-entity"></a>Složený entity

[Složená entita](reference-entity-composite.md) je tvořena dalšími entitami, jako jsou předem připravené entity, jednoduché, regulární výrazy a seznam entit. Samostatné entity tvoří celé entity. 

## <a name="list-entity"></a>Entita seznamu

[Seznam entit](reference-entity-list.md) představuje pevně uzavřenou sadu příbuzných slov spolu s jejich synonymy. Služba LUIS nevyhledává další hodnoty pro seznam entit. Použití **doporučujeme** funkce návrhy pro nové slova na základě aktuálního seznamu. Pokud existuje více než jednu entitu seznamu se stejnou hodnotou, je každá entita vrácené dotazem koncový bod. 

## <a name="patternany-entity"></a>Pattern.Any entity

[Pattern. any](reference-entity-pattern-any.md) je zástupný symbol s proměnlivou délkou, který se používá jenom v šabloně vzoru utterance k označení, kde začíná a končí entita.  

## <a name="prebuilt-entity"></a>Předem připravených entit

Předem připravené entity jsou vestavěné typy, které představují běžné koncepty, jako je e-mail, adresa URL a telefonní číslo. Názvy předem připravených entit, jsou vyhrazena. [Všechny předem připravené entity](luis-prebuilt-entities.md) , které se přidají do aplikace, se vrátí v dotazu předpovědi koncového bodu, pokud se nacházejí v utterance. 

Entita je vhodná v případě, že:

* Data odpovídají běžnému případu použití, který je podporován předem vytvořenými entitami pro jazykovou verzi. 

Předem připravené entity lze kdykoli přidat a odebrat.

![Číslo předem připravených entit](./media/luis-concept-entities/number-entity.png)

[Kurz](luis-tutorial-prebuilt-intents-entities.md)<br>
[Příklad odpovědi JSON pro entitu](luis-concept-data-extraction.md#prebuilt-entity-data)

Některé z těchto předem vytvořených entit jsou definované v otevřeném zdrojovém projektu pro [rozpoznávání – textový](https://github.com/Microsoft/Recognizers-Text) projekt. Pokud konkrétní jazykovou verzi nebo entity se aktuálně nepodporuje, přispět k projektu. 

### <a name="troubleshooting-prebuilt-entities"></a>Řešení potíží s předem vytvořenými entitami

Pokud je v portálu LUIS k dispozici označení předem sestavené entity místo vlastní entity, máte několik možností, jak tento problém vyřešit.

Předem připravené entity přidané do aplikace se _vždycky_ vrátí, i když by utterance měl extrahovat vlastní entity pro stejný text. 

#### <a name="change-tagged-entity-in-example-utterance"></a>Změnit entitu s příznakem v příkladu utterance

Pokud je předem vytvořená entita stejný text nebo tokeny jako vlastní entita, vyberte text v příkladu utterance a změňte tagovaný utterance. 

Pokud je předem vytvořená entita označená s větším textem nebo tokeny než vaše vlastní entita, máte několik možností, jak tento problém vyřešit:

* [Remove – příklad metody utterance](#remove-example-utterance-to-fix-tagging)
* [Odebrat předem vytvořenou metodu entity](#remove-prebuilt-entity-to-fix-tagging)

#### <a name="remove-example-utterance-to-fix-tagging"></a>Odebrat příklad utterance pro opravu označování 

První volbou je odebrání ukázkového utteranceu. 

1. Odstraňte příklad utterance.
1. Přeučení aplikace 
1. Přidejte zpět pouze slovo nebo frázi, která je entitou, která je označena jako předem sestavená entita, jako kompletní příklad utterance. Pro slovo nebo frázi bude stále označena předdefinovaná entita. 
1. Vyberte entitu v příkladu utterance na stránce **záměr** a změňte ji na vlastní entitu a znovu spusťte vlak. To by mělo zabránit tomu, aby LUIS označit tento přesný text jako předem vytvořenou entitu v jakémkoli příkladu projevy, který tento text používá. 
1. Přidejte celý původní příklad utterance zpátky k záměru. Vlastní entita by měla být označena jako místo předem sestavené entity. Pokud vlastní entita není označená, musíte do projevy přidat další příklady tohoto textu.

#### <a name="remove-prebuilt-entity-to-fix-tagging"></a>Odebrat předem vytvořenou entitu pro opravu označování

1. Z aplikace odeberte předem vytvořenou entitu. 
1. Na stránce **záměr** označte vlastní entitu v příkladu utterance.
1. Trénujte aplikaci.
1. Přidejte předem vytvořenou entitu zpátky do aplikace a proveďte výuku aplikace. Tato oprava předpokládá, že předem vytvořená entita není součástí složené entity.

## <a name="regular-expression-entity"></a>Entiay regulárního výrazu 

[Entita regulárního výrazu](reference-entity-regular-expression.md) extrahuje entitu na základě vzoru regulárního výrazu, který zadáte.

## <a name="simple-entity"></a>Jednoduchá entita

A [jednoduchou entitu](reference-entity-simple.md) je hodnota zjištěné počítače. Může být slova nebo fráze.
## <a name="entity-limits"></a>Omezení entity

Kontrola [omezení](luis-boundaries.md#model-boundaries) porozumět, kolik jednotlivých typů entit můžete přidat do modelu.

## <a name="if-you-need-more-than-the-maximum-number-of-entities"></a>Pokud potřebujete více než maximální počet entit 

V kombinaci s rolemi entit může být nutné použít složené entity.

Složený entity představují část celku. Například složenou entitu s názvem PlaneTicketOrder může mít podřízené entity letecká společnost, cíl, DepartureCity, datumOdjezdu a PlaneTicketClass.

LUIS také poskytuje typ entity seznamu, který není zjištěn počítačem, ale umožňuje vaší aplikaci LUIS určit pevný seznam hodnot. Zobrazit [LUIS hranice](luis-boundaries.md) odkazu ke kontrole omezení typu entity. 

Pokud jste tyto entity posuzujete a stále potřebujete víc, než je limit, obraťte se na podporu. Uděláte to tak, získat podrobné informace o systému, přejděte [LUIS](luis-reference-regions.md#luis-website) webu a pak vyberte **podporu**. Pokud vaše předplatné Azure zahrnuje odbornou pomoc, obraťte se na [technické podpoře Azure](https://azure.microsoft.com/support/options/). 

## <a name="next-steps"></a>Další postup

Další koncepty o dobré [projevy](luis-concept-utterance.md). 

Zobrazit [přidat entity](luis-how-to-add-entities.md) získat další informace o přidání entity do aplikace LUIS.
