---
title: Typy entit
titleSuffix: Language Understanding - Azure Cognitive Services
description: Přidání entity (klíčových dat v doméně vaší aplikace) v aplikacích Language Understanding Intelligent Service (LUIS).
services: cognitive-services
author: diberry
manager: cgronlun
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 01/02/2019
ms.author: diberry
ms.openlocfilehash: a6dba36eadde9bc9bb1e6ca778a3bce07b561e54
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2019
ms.locfileid: "55207288"
---
# <a name="entity-types-and-their-purposes-in-luis"></a>Typy entit a jejich účely v LUIS

Entity jsou slova nebo fráze v projevy, které jsou data klíče v doméně vaší aplikace.

## <a name="entity-compared-to-intent"></a>Entity ve srovnání s cílem

Entita představuje slovo nebo frázi v utterance, který chcete, aby byl extrahován. Utterance může obsahovat mnoho entit nebo žádný vůbec. Entity představuje třídu kolekce podobných objektů (místa, věci, osoby, události nebo koncepty) včetně. Entity popisují informace související s cílem a někdy jsou nezbytné pro vaši aplikaci k provedení svých úkolů. Pro vyhledávání zpráv aplikace může například obsahovat entity, jako je například "tematické", "zdroj", "– klíčové slovo" a "datum publikování", které jsou klíčová data pro vyhledávání zpráv. Cestovní aplikaci rezervace, "umístění", "data", "letecká společnost" "cesty třída" a "lístků" jsou informace o klíči pro rezervace letu (relevantní k příslušnému záměru "Rezervovat let").

Naproti tomu představuje záměr předpovědi celý utterance. 

## <a name="entities-help-with-data-extraction-only"></a>Entity usnadní pouze extrakce dat

Označení nebo značka entity pro účely it pouze extrakce entity nepomůže s záměru předpovědi.

## <a name="entities-represent-data"></a>Entity představují data

Entity jsou data, která chcete načítat utterance. To může být název, datum, název produktu nebo jakákoli skupina slova. 

|Promluva|Entita|Data|
|--|--|--|
|Zakoupit 3 lístky pro New York|Předem připravené číslo<br>Location.Destination|3<br>New York|
|Nákup lístků z New Yorku do Londýna na 5. března|Location.Origin<br>Location.Destination<br>Předem připravené datetimeV2|New York<br>Londýn<br>5. března 2018|

## <a name="entities-are-optional-but-highly-recommended"></a>Entity jsou volitelné, ale důrazně doporučené

I když záměry povinné, entity jsou volitelné. Nemusíte vytvářet entity pro každý koncept ve vaší aplikaci, ale pouze pro potřebných ke klientské aplikaci provést akci. 

Pokud vaše projevy nemají podrobnosti, které váš robot je potřeba pokračovat, není potřeba je přidat. Během existence vaší aplikace, můžete je přidat později. 

Pokud si nejste jistí, jak můžete využít informace, přidat pár běžných předem připravených entit, jako [datetimeV2](luis-reference-prebuilt-datetimev2.md), [ordinální](luis-reference-prebuilt-ordinal.md), [e-mailu](luis-reference-prebuilt-email.md), a [telefonní číslo ](luis-reference-prebuilt-phonenumber.md).

## <a name="label-for-word-meaning"></a>Popisek pro význam slova

Pokud volba slovo nebo slovo uspořádání je stejný, ale není to samé, není popisek s entitou. 

Následující projevy, slovo `fair` je homograf. Je napsána stejný, ale má odlišný význam:

|Promluva|
|--|
|Jaký druh county veletrhů se dějí v Seattlu toto léto?|
|Je aktuální hodnocení ke kontrole Seattle přiměřená?|

Pokud byste chtěli entity událost najít všechna data událostí, popisek slovo `fair` v první utterance, ale nikoli do druhého.

## <a name="entities-are-shared-across-intents"></a>Entity jsou sdíleny napříč záměrů

Entity jsou sdíleny mezi záměry. Nepatří do žádné jeden záměr. Záměry a entity můžou být sémanticky přidružené, ale není výhradní vztah.

V utterance "rezervovat mě lístek do Paříže", "Paříž" je entitě odkazující na umístění. Tím, že rozpoznává entity, které jsou uvedeny v utterance uživatele, pomáhá LUIS klientské aplikace zvolte konkrétní akce, abyste mohli splnit žádost uživatele.

## <a name="mark-entities-in-none-intent"></a>Označování entit v žádné záměru

Všechny příkazy, včetně **žádný** záměr, by měl mít označen jako entity, pokud je to možné. To pomáhá LUIS Další informace o kde entity, které jsou v projevy a slova jsou kolem entity. 

## <a name="entity-status-for-predictions"></a>Stav entity pro předpovědi

Na portálu služby LUIS zjistíte, když entitu v utterance příkladu je buď liší od označené entity nebo je příliš zavřete s jinou entitou a tedy jasné. Je toto označeno červené podtržení v příkladu utterance. 

Další informace najdete v tématu [stav Entity predikcí](luis-how-to-add-example-utterances.md#entity-status-predictions). 

## <a name="types-of-entities"></a>Typy entit

Služba LUIS nabízí mnoho typů entit. Zvolte entitu, se závislosti na tom, jak by měla být rozbalena data a jak ho by měly být zastoupeny po je extrahován.

Entity můžete extrahovat pomocí strojového učení, což umožňuje LUIS a pokračujte ve čtení o jak entita zobrazuje v utterance. Bez strojového učení, odpovídající přesný text nebo regulárního výrazu může být extrahována entity. Entity ve vzorech může být extrahována s hybridní implementace. 

Po extrahování entity entity data můžete vyjádřena jako jedna jednotka informace nebo v kombinaci s jinými entitami, aby jednotka informace, které můžete použít klientskou aplikaci formuláře.

|Machine-learned|Označit|Kurz|Příklad:<br>Odpověď|Typ entity|Účel|
|--|--|--|--|--|--|
|✔|✔|[✔](luis-tutorial-composite-entity.md)|[✔](luis-concept-data-extraction.md#composite-entity-data)|[**Složené**](#composite-entity)|Seskupení entit, bez ohledu na typ entity.|
|✔|✔|[✔](luis-quickstart-intent-and-hier-entity.md)|[✔](luis-concept-data-extraction.md#hierarchical-entity-data)|[**Hierarchické**](#hierarchical-entity)|Seskupení jednoduché entit.|
|||[✔](luis-quickstart-intent-and-list-entity.md)|[✔](luis-concept-data-extraction.md#list-entity-data)|[**Seznam**](#list-entity)|Seznam položek a jejich synonyma extrahuje text přesně shodovat.|
|Smíšený||[✔](luis-tutorial-pattern.md)|[✔](luis-concept-data-extraction.md#patternany-entity-data)|[**Pattern.any**](#patternany-entity)|Entita, ve kterém je obtížné určit koncové entity.|
|||[✔](luis-tutorial-prebuilt-intents-entities.md)|[✔](luis-concept-data-extraction.md#prebuilt-entity-data)|[**Předem připravené**](#prebuilt-entity)|Extrahovat různé druhy dat je už vytrénovaný.|
|||[✔](luis-quickstart-intents-regex-entity.md)|[✔](luis-concept-data-extraction.md#regular-expression-entity-data)|[**Regulární výraz**](#regular-expression-entity)|Používá regulární výraz pro porovnání textu.|
|✔|✔|[✔](luis-quickstart-primary-and-secondary-data.md)|[✔](luis-concept-data-extraction.md#simple-entity-data)|[**Jednoduché**](#simple-entity)|Obsahuje jeden koncept v slova nebo fráze.|

Pouze entity se naučili počítače potřeba označené v příkladu projevy pro každý záměr. Počítače zjištěné entity fungují lépe, když testován prostřednictvím [koncový bod dotazy](luis-concept-test.md#endpoint-testing) a [recenzování projevy koncový bod](luis-how-to-review-endoint-utt.md). 

Pattern.Any entity musí být označeny [vzor](luis-how-to-model-intent-pattern.md) Příklady šablon, ne příklady záměru uživatele. 

Smíšené entity pomocí kombinace metod zjišťování entit.

## <a name="composite-entity"></a>Složený entity

Složený entity je tvořena jinými entitami, jako je například předem připravených entit, jednoduché, regulární výraz, seznam a hierarchické entity. Samostatné entity tvoří celé entity. 

Tato entita je vhodná přizpůsobit, kdy data:

* Se vztahují k sobě navzájem. 
* Se k sobě v kontextu promluvy navzájem vztahují.
* Využijte celou řadu typů entit.
* Je třeba seskupeny a zpracovány klientské aplikaci jako celek informací.
* Máte různé uživatelské projevy, které vyžadují strojového učení.

![Složený entity](./media/luis-concept-entities/composite-entity.png)

[Kurz](luis-tutorial-composite-entity.md)<br>
[Příklad odpovědi JSON pro entitu](luis-concept-data-extraction.md#composite-entity-data)<br>

## <a name="hierarchical-entity"></a>Hierarchická entita

Hierarchické entity je kategorie kontextově zjištěná jednoduché entity nazývané podřízené položky.

Tato entita je vhodná přizpůsobit, kdy data:

* Jsou jednoduché entity.
* Se k sobě v kontextu promluvy navzájem vztahují.
* Volba určité slovo slouží k označení každé podřízené entity. Mezi příklady těchto slov patří from/to (z/do), leaving/headed to (odstěhovat/nastěhovat), away from/toward (z kanceláře/do kanceláře).
* Podřízené objekty jsou často ve stejné utterance. 
* Musí být seskupeny a zpracovány klientskou aplikací jako jediná informace.

Nepoužívejte pokud:

* Potřebujete entitu, která má přesný text shody pro podřízené položky bez ohledu na kontextu. Použití [seznam entit](#list-entity) místo. 
* Potřebujete entitu pro vztah nadřízenosti a podřízenosti s jinými typy entit. Použití [složený entity](#composite-entity).

![hierarchické entity](./media/luis-concept-entities/hierarchical-entity.png)

[Kurz](luis-quickstart-intent-and-hier-entity.md)<br>
[Příklad odpovědi JSON pro entitu](luis-concept-data-extraction.md#hierarchical-entity-data)<br>

### <a name="roles-versus-hierarchical-entities"></a>Role a hierarchické entity

[Role](luis-concept-roles.md#roles-versus-hierarchical-entities) vzoru řešení stejného problému jako hierarchické entity ale vztahují se na všechny typy entit. Role jsou aktuálně dostupné jenom ve vzorcích. Role nejsou k dispozici v projevy příklad záměry.  

## <a name="list-entity"></a>Entita seznamu

Seznam entit představují sadu související slova spolu s jejich synonyma pevné, uzavřené. Služba LUIS nevyhledává další hodnoty pro seznam entit. Použití **doporučujeme** funkce návrhy pro nové slova na základě aktuálního seznamu. Pokud existuje více než jednu entitu seznamu se stejnou hodnotou, je každá entita vrácené dotazem koncový bod. 

Entita je vhodná podle při textová data:

* Jsou známé sady.
* Tato sada nepřekračuje maximální [hranice](luis-boundaries.md) aplikace LUIS pro tento typ entity.
* Text promluvy se přesně shoduje se synonymem nebo názvem v kanonickém tvaru. Služba LUIS nepoužívá seznamu nad rámec textu přesné shody. Seznam entit nejsou vyřešil slovního rozboru, množné číslo a další varianty konfigurací. Chcete-li spravovat změny, zvažte použití [vzor](luis-concept-patterns.md#syntax-to-mark-optional-text-in-a-template-utterance) syntaxí volitelný text, který.

![seznam entit](./media/luis-concept-entities/list-entity.png)

[Kurz](luis-quickstart-intent-and-list-entity.md)<br>
[Příklad odpovědi JSON pro entitu](luis-concept-data-extraction.md#list-entity-data)

## <a name="patternany-entity"></a>Pattern.Any entity

Pattern.any je použít jenom v utterance vzor šablony k označení, ve kterém entita začíná a končí zástupným symbolem proměnné délky.  

Entita je vhodná podle při:

* Koncové entity můžou být zaměňován s zbývající text utterance. 
[Kurz](luis-tutorial-pattern.md)<br>
[Příklad odpovědi JSON pro entitu](luis-concept-data-extraction.md#patternany-entity-data)

**Příklad**  
Klientská aplikace, která hledá zadaný pro knihy podle názvu, extrahuje pattern.any úplný název. Šablona utterance pomocí pattern.any je toto vyhledávání v adresáři `Was {BookTitle} written by an American this year[?]`. 

Každý řádek v následující tabulce, má dvě verze utterance. Začátek utterance je jak LUIS se zpočátku zobrazí utterance, kde není jasné, s názvem adresáře začíná a končí. Dolní utterance je, jak služba LUIS, že název knihy při vzor se používají pro extrakci. 

|Promluva|
|--|
|The Man kdo Mistook jeho manželkou Hat a další klinické kontrolky zapsal American tento rok?<br>Byl **The Man kdo Mistook jeho manželkou Hat a další klinické kontrolky** zapsal American tento rok?|
|Byl poloviční režimu spánku v žába Pajamas zapsal American tento rok?<br>Byl **poloviční režimu spánku v žába Pajamas** zapsal American tento rok?|
|Byla konkrétní smutek Citronový koláč: Nové, zapisuje American tento rok?<br>Byl **konkrétní smutek Citronový koláč: Nové** zapsal American tento rok?|
|Bylo, že v mé kapesního zařízení Wocket! zapisuje American tento rok?<br>Byl **je Moje Pocket Wocket!** zapisuje American tento rok?|

## <a name="prebuilt-entity"></a>Předem připravených entit

Předem připravených entit jsou předdefinované typy, které představují běžné konceptů, jako jsou e-mailovou adresu URL a telefonní číslo. Názvy předem připravených entit, jsou vyhrazena. [Všechny předem připravených entit](luis-prebuilt-entities.md) , které jsou přidané do aplikace jsou vráceny v dotazu předpovědi koncový bod, pokud se nenachází v utterance. 

Entita je vhodná podle při:

* Data odpovídá běžným případem použití předem připravených entit pro vaši kulturu jazyk nepodporuje. 

Předem připravených entit můžete přidávat a odebírat kdykoli. Pokud zjistíte, že je v příkladu utterance nalezeny předem připravených entit, provádění nemožné, označení vlastní entity z aplikace odebrat předem připravených entit, označit vaší entity a pak přidejte předem připravených entit. 

![Číslo předem připravených entit](./media/luis-concept-entities/number-entity.png)

[Kurz](luis-tutorial-prebuilt-intents-entities.md)<br>
[Příklad odpovědi JSON pro entitu](luis-concept-data-extraction.md#prebuilt-entity-data)

Některé z těchto předem připravených entit jsou definovány v open-source [rozpoznávání textu](https://github.com/Microsoft/Recognizers-Text) projektu. Pokud konkrétní jazykovou verzi nebo entity se aktuálně nepodporuje, přispět k projektu. 

## <a name="regular-expression-entity"></a>Entiay regulárního výrazu 

Regulární výraz je nejvhodnější pro nezpracované utterance text. Ignoruje velikost písmen a ignoruje kulturní variant.  Porovnávání regulárních výrazů se použije po kontrolu pravopisu změny na úrovni znak není úroveň tokenu. Pokud regulární výraz je příliš složitý, jako je třeba použití mnoha závorky, nejste schopni přidat výraz do modelu. Využívá část, ale ne všechny [.Net regulární výraz](https://docs.microsoft.com/dotnet/standard/base-types/regular-expressions) knihovny. 

Entita je vhodná podle při:

* Data jsou s jakékoli změny, které je konzistentní konzistentním formátováním.
* Regulární výraz není potřeba více než 2 úrovní vnoření. 

![Entiay regulárního výrazu](./media/luis-concept-entities/regex-entity.png)

[Kurz](luis-quickstart-intents-regex-entity.md)<br>
[Příklad odpovědi JSON pro entitu](luis-concept-data-extraction.md#regular-expression-entity-data)<br>

## <a name="simple-entity"></a>Jednoduchá entita 

Jednoduché entita je obecná entita, která popisuje jeden koncept a získané z kontextu zjištěné počítače. Protože jednoduché entity jsou obecně názvy, například názvy společností, názvy produktů nebo jiných kategorií názvů, přidejte [seznam frází](luis-concept-feature.md) při použití jednoduchého entity a zvýšit tak signál názvů používaných. 

Entita je vhodná podle při:

* Data nejsou naformátovány konzistentně ale označují stejnou věc. 

![jednoduché entity](./media/luis-concept-entities/simple-entity.png)

[Kurz](luis-quickstart-primary-and-secondary-data.md)<br/>
[Příklad odpovědi pro entitu](luis-concept-data-extraction.md#simple-entity-data)<br/>

## <a name="entity-limits"></a>Omezení entity

Kontrola [omezení](luis-boundaries.md#model-boundaries) porozumět, kolik jednotlivých typů entit můžete přidat do modelu.

## <a name="composite-vs-hierarchical-entities"></a>Složený vs hierarchické entity

Složené a hierarchické entit jak mají vztahů nadřazenosti a podřízenosti a jsou počítače se naučili. Machine learning umožňuje LUIS vysvětlení konceptu entit podle různých kontextech (uspořádání slov). Složený entity jsou flexibilnější, protože umožňují typy různých entit jako podřízené objekty. Hierarchické entity podřízené objekty jsou pouze jednoduché entity. 

|Typ|Účel|Příklad:|
|--|--|--|
|Hierarchické|Nadřazený podřízený jednoduché entit|Location.Origin=New York<br>Location.Destination=London|
|Složené|Nadřazené podřízené entity: předem připravených, seznam, jednoduchý a hierarchické| číslo = 3<br>Seznam = první třídy<br>prebuilt.datetimeV2=March 5|

## <a name="if-you-need-more-than-the-maximum-number-of-entities"></a>Pokud potřebujete více než maximální počet entit 

Můžete potřebovat použít hierarchická a složené entit. Hierarchické entity odrážet relace mezi entitami, které mají vlastnosti nebo jsou členy skupiny. Podřízené entity jsou všechny členy své nadřazené kategorie. Například hierarchické entitu s názvem PlaneTicketClass pravděpodobně podřízené entity EconomyClass a FirstClass. Hierarchie obsahuje pouze jednu úroveň hloubky.  

Složený entity představují část celku. Například složenou entitu s názvem PlaneTicketOrder může mít podřízené entity letecká společnost, cíl, DepartureCity, datumOdjezdu a PlaneTicketClass. Vytváření složených entity z již existujících jednoduché entit, podřízené položky hierarchické entity nebo předem připravených entit.  

Služba LUIS také poskytuje seznam typ entity, která není zjištěné počítače, ale umožňuje aplikaci LUIS, chcete-li určit pevnou seznam hodnot. Zobrazit [LUIS hranice](luis-boundaries.md) odkazu ke kontrole omezení typu entity. 

Pokud jste za hierarchické, složený a seznamu entit a stále potřebovat vyšší limit, obraťte se na podporu. Uděláte to tak, získat podrobné informace o systému, přejděte [LUIS](luis-reference-regions.md#luis-website) webu a pak vyberte **podporu**. Pokud vaše předplatné Azure zahrnuje odbornou pomoc, obraťte se na [technické podpoře Azure](https://azure.microsoft.com/support/options/). 

## <a name="next-steps"></a>Další postup

Další koncepty o dobré [projevy](luis-concept-utterance.md). 

Zobrazit [přidat entity](luis-how-to-add-entities.md) získat další informace o přidání entity do aplikace LUIS.
