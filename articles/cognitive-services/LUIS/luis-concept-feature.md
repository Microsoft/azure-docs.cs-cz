---
title: Funkce strojového učení s LUIS
description: Přidáním funkcí do jazykového modelu poskytněte nápovědu týkající se rozpoznávání vstupu, který chcete označit nebo klasifikovat.
ms.topic: conceptual
ms.date: 06/10/2020
ms.openlocfilehash: 02a6fd27dbe22a40b29b47515edec5506d3b2075
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/23/2020
ms.locfileid: "87075172"
---
# <a name="machine-learning-features"></a>Funkce strojového učení

Ve službě Machine Learning je *funkce*   odlišující se vlastností a atributů dat, která systém sleduje a se učí.

Funkce strojového učení poskytují LUIS důležité pomůcky pro hledání věcí, které odlišují koncept. Jsou to pomocné parametry, které může LUIS použít, ale nejsou to pevná pravidla. LUIS používá při hledání dat tyto pomocné parametry ve spojení s popisky.

Funkci lze popsat jako funkci, jako je například f (x) = y. V příkladu utterance funkce oznamuje, kde hledat rozlišující vlastnosti. Tyto informace vám pomůžou vytvořit schéma.

## <a name="types-of-features"></a>Typy funkcí

LUIS podporuje seznamy frází i modely jako funkce:

* Funkce seznamu frází 
* Model (záměr nebo entita) jako funkce

Funkce by měly být považovány za nezbytnou součást návrhu schématu.

## <a name="find-features-in-your-example-utterances"></a>Hledání funkcí v příkladech projevy

Vzhledem k tomu, že LUIS je jazykově založená aplikace, jsou tyto funkce založené na textu. Vyberte text, který určuje vlastnost, kterou chcete odlišit. V případě LUIS je nejmenší jednotkou *token*. V anglickém jazyce je token souvislým rozsahem písmen a čísel, která nemají mezery ani interpunkční znaménka.

Vzhledem k tomu, že mezery a interpunkční znaménka nejsou tokeny, zaměřte se na text, který můžete použít jako funkce. Nezapomeňte zahrnout variace slov, například:

* množné formuláře
* časů příkazů
* zkratky
* pravopisné a chybné pravopisné chyby

Určete, zda text, protože rozlišuje vlastnosti, musí:

* Porovnat přesné slovo nebo frázi: zvažte přidání entity regulárního výrazu nebo entity seznamu jako funkce pro entitu nebo záměr.
* Porovnává dobře známý koncept, například data, časy nebo jména lidí: jako funkci pro entitu nebo záměr použijte předem vytvořenou entitu.
* Naučte se nové příklady v průběhu času: jako funkce pro entitu nebo záměr použijte seznam frází některých příkladů konceptu.

## <a name="combine-features"></a>Kombinování funkcí

K popisu vlastností nebo konceptu můžete použít více než jednu funkci. Společné párování je použití funkce seznamu frází a typu entity, který se často používá jako funkce:

 * předem vytvořená entita
 * entita regulárního výrazu
 * Seznam entit

### <a name="ticket-booking-entity-example"></a>Příklad entity rezervace lístku

Jako první příklad si představte aplikaci pro rezervaci letu s záměrem rezervace letu a entitou rezervace lístků.

Entita rezervace lístku je entita strojového učení pro cílové umístění letu. Chcete-li získat informace o umístění, použijte dvě funkce, které vám pomůžou:

* Seznam frází relevantních slov, jako je například **rovina**, **let**, **rezervace**nebo **lístek**
* Předem vytvořená **geographyV2** entita jako funkce pro entitu

### <a name="pizza-entity-example"></a>Příklad entity Pizza

Jako jiný příklad zvažte aplikaci pro řazení Pizza, která má záměr Create-Pizza-Order a entita Pizza.

Entita Pizza je entita strojového učení pro podrobnosti Pizza. Chcete-li získat podrobné informace, použijte dvě funkce, které vám pomůžou:

* Seznam frází relevantních slov, jako je například **sýr**, **crust**, **pepperoni**nebo **ananas**
* Předem vytvořená **Číselná** entita jako funkce pro entitu

## <a name="create-a-phrase-list-for-a-concept"></a>Vytvoření seznamu frází pro koncept

Seznam frází je seznam slov nebo frází, které popisují koncept. Seznam frází se používá jako porovnávání bez rozlišení velkých a malých písmen na úrovni tokenu.

Když přidáváte seznam frází, můžete tuto funkci nastavit jako **[globální](#global-features)**. Globální funkce se vztahuje na celou aplikaci.

### <a name="when-to-use-a-phrase-list"></a>Kdy použít seznam frází

Seznam frází použijte v případě, že aplikaci LUIS potřebujete zobecnit a identifikovat nové položky pro daný koncept. Seznamy frází jsou jako slovníky specifické pro doménu. Zvyšují kvalitu porozumění pro záměry a entity.

### <a name="how-to-use-a-phrase-list"></a>Jak používat seznam frází

Seznam frází LUIS považuje za kontext a generalizy k identifikaci položek, které jsou podobné, ale neodpovídají přesnému textu. Použijte následující postup, chcete-li použít seznam frází:

1. Začněte s entitou strojového učení:
    1. Přidejte příklad projevy.
    1. Popisek s entitou strojového učení.
1. Přidat seznam frází:
    1. Přidejte slova s podobným významem. Nepřidávat všechna možná slova nebo fráze. Místo toho přidejte několik slov nebo frází současně. Pak převlakujte a publikujte.
    1. Zkontrolujte a přidejte navrhovaná slova.

### <a name="a-typical-scenario-for-a-phrase-list"></a>Typický scénář pro seznam frází

Typický scénář pro seznam frází vám umožní zvýšit slova související s konkrétním nápadem.

Lékařské výrazy jsou dobrým příkladem slov, která mohou potřebovat seznam frází, aby bylo možné zvýšit svůj význam. Tyto výrazy mohou mít konkrétní fyzický, chemický, léčebný nebo abstraktní význam. LUIS neví, že tyto výrazy jsou důležité pro vaši doménu subjektu bez seznamu frází.

Chcete-li extrahovat lékařské výrazy:

1. Vytvořte v těchto projevych příklad lékařské výrazy projevy a Label.
2. Vytvořte seznam frází s příklady podmínek v doméně předmětu. Seznam frází by měl zahrnovat skutečný termín, který jste popsali, a další podmínky, které popisují stejný pojem.
3. Přidejte seznam frází k entitě nebo podentitě, která extrahuje koncept použitý v seznamu frází. Nejběžnějším scénářem je součást (podřízená) entita strojového učení. Pokud má být seznam frází použit ve všech záměrech nebo entitách, označte seznam frází jako seznam globálních frází. Příznak **enabledForAllModels** řídí tento obor modelu v rozhraní API.

### <a name="token-matches-for-a-phrase-list"></a>Shody tokenu pro seznam frází

Seznam frází vždy platí na úrovni tokenu. Následující tabulka ukazuje, jak se seznam frází, který má slovo **Ann** , vztahuje na variace stejných znaků v tomto pořadí.


| Variace tokenu pro **Ann** | Seznam frází při nalezení tokenu souhlasí |
|--------------------------|---------------------------------------|
| **ANN**<br>**aNN**<br>           | Ano – token je **Ann**                  |
| **Ann**                    | Ano – token je **Ann**                  |
| **Anne**                     | No – token je **Anne**                  |

<a name="how-to-use-phrase-lists"></a>
<a name="how-to-use-a-phrase-lists"></a>
<a name="phrase-lists-help-identify-simple-exchangeable-entities"></a>

## <a name="a-model-as-a-feature-helps-another-model"></a>Model jako funkce pomáhá jinému modelu.

Model (záměr nebo entitu) můžete přidat jako funkci jinému modelu (záměr nebo entitu). Přidáním existujícího záměru nebo entity jako funkce přidáváte dobře definovaný koncept, který obsahuje příklady s popisky.

Při přidávání modelu jako funkce můžete tuto funkci nastavit jako:
* **[Požadováno](#required-features)**. Aby byl model vrácen z koncového bodu předpovědi, je nutné najít požadovanou funkci.
* **[Globální](#global-features)**. Globální funkce se vztahuje na celou aplikaci.

### <a name="when-to-use-an-entity-as-a-feature-to-an-intent"></a>Kdy použít entitu jako funkci k záměru

Přidejte entitu jako funkci k záměru, když je zjišťování této entity pro záměr významné.

Například pokud je záměrem rezervace letu, jako je **BookFlight**, a entita má informace o lístkech (jako je počet míst, počátek a cíl), pak by se měla entita s informacemi o lístkech přidat do předpovědi záměru **BookFlight** značnou váhu.

### <a name="when-to-use-an-entity-as-a-feature-to-another-entity"></a>Kdy použít entitu jako funkci pro jinou entitu

Entita (A) by měla být přidána jako funkce jiné entitě (B), pokud je zjišťování této entity (A) významné pro předpověď entity (B).

Pokud je například entita dodací adresy obsažena v subentitě adresy ulice, pak hledání podentity s adresou pro ulici přičítá k předpovědi pro danou entitu dodací adresy důležitou váhu.

* Adresa příjemce (entita strojového učení):

    * Číslo ulice (subentity)
    * Ulice (subentity)
    * Město (subentity)
    * Okres (subentity)
    * Země/oblast (subentita)
    * Poštovní směrovací číslo (subentity)

## <a name="nested-subentities-with-features"></a>Vnořené subentity s funkcemi

Podentita Machine-Learning indikuje, že nadřazená entita má k dispozici koncept. Nadřazená položka může být jiná subentita nebo nejvyšší entita. Hodnota subentity funguje jako funkce nadřazeného objektu.

Dílčí entita může mít jako funkci seznam frází i model (jinou entitu).

Pokud má podentita seznam frází, porovná slovní slovo konceptu, ale nepřidá žádné informace do odpovědi JSON předpovědi.

Pokud má podentita funkci jiné entity, odpověď JSON zahrnuje extrahovaná data této jiné entity.


## <a name="required-features"></a>Požadované funkce

Aby byl model vrácen z koncového bodu předpovědi, je nutné najít požadovanou funkci. Požadovanou funkci použijte, když víte, že příchozí data se musí shodovat s funkcí.

Pokud text utterance neodpovídá požadované funkci, nebude extrahován.

Požadovaná funkce používá entitu bez strojového učení:

* Entita regulárního výrazu
* Entita seznamu
* Předem vytvořená entita

Pokud máte jistotu, že se váš model nachází v datech, nastavte funkci podle potřeby. Požadovaná funkce nevrátí žádnou hodnotu, pokud nebyla nalezena.

Pokračuje se v příkladu adresy pro expedici:

Adresa příjemce (entita počítač se naučila)

 * Číslo ulice (subentity) 
 * Ulice (subentity) 
 * Název ulice (subentity) 
 * Město (subentity) 
 * Okres (subentity) 
 * Země/oblast (subentita) 
 * Poštovní směrovací číslo (subentity)

### <a name="required-feature-using-prebuilt-entities"></a>Požadovaná funkce s využitím předem připravených entit

Město, stát a země/oblast jsou všeobecně uzavřenou sadou seznamů, což znamená, že se v průběhu času nemění. Tyto entity mohou mít relevantní doporučené funkce a tyto funkce mohou být označeny jako povinné. To znamená, že pokud se entity, které mají požadované funkce, nenaleznou, celá dodací adresa se nevrátí.

Co když se město, stát nebo země/oblast nacházejí v utterance, ale nacházejí se v umístění nebo jsou slangem, že LUIS neočekává? Pokud chcete poskytnout nějaké následné zpracování, které vám pomůžou tuto entitu vyřešit, protože skóre s nízkou mírou spolehlivosti z LUIS, nepoužívejte tuto funkci podle potřeby.

Dalším příkladem požadované funkce pro doručovací adresu je zadání požadovaného a [předem připraveného](luis-reference-prebuilt-entities.md) čísla. Uživatel tak může zadat "1 Microsoft Way" nebo "One Microsoft". Oba se překládají na číslice "1" pro subentity s číslem ulice.

### <a name="required-feature-using-list-entities"></a>Požadovaná funkce využívající seznam entit

[Entita seznamu](reference-entity-list.md) se používá jako seznam kanonických názvů spolu s jejich synonymy. Pokud utterance neobsahuje kanonický název nebo synonymum, jako požadovaná funkce se entita nevrátí jako součást koncového bodu předpovědi.

Předpokládejme, že vaše společnost je dodávána pouze do omezené skupiny zemí nebo oblastí. Můžete vytvořit entitu seznamu, která bude obsahovat několik způsobů, jak zákazníkovi odkazovat na zemi nebo oblast. Pokud LUIS nenajde přesnou shodu v textu utterance, pak se v předpovědi nevrátí entita (která má požadovanou funkci entity seznam).

|Kanonický název|Synonyma|
|--|--|
|USA|USA:<br>U. S. A<br>USA<br>USA<br>0|

Klientská aplikace, jako je například robotka chatu, může požádat o pomocnou otázku. To pomáhá zákazníkovi pochopit, že výběr země nebo oblasti je omezený a je *povinný*.

### <a name="required-feature-using-regular-expression-entities"></a>Požadovaná funkce využívající entity regulárních výrazů

[Entita regulárního výrazu](reference-entity-regular-expression.md) , která se používá jako požadovaná funkce, poskytuje funkce pro přizpůsobení textu.

V příkladu adresy pro expedici můžete vytvořit regulární výraz, který zachycuje pravidla syntaxe poštovních směrovacích čísel země nebo oblasti.

## <a name="global-features"></a>Globální funkce

Obecně platí, že pokud chcete použít funkci pro konkrétní model, můžete tuto funkci nakonfigurovat jako **globální funkci** a použít ji pro celou aplikaci.

Nejběžnějším použitím globální funkce je přidání dalších slovníků do aplikace. Pokud například vaši zákazníci používají primární jazyk, ale očekává se, že bude moci používat jiný jazyk v rámci stejného utterance, můžete přidat funkci, která obsahuje slova ze sekundárního jazyka.

Vzhledem k tomu, že uživatel očekává použití sekundárního jazyka napříč jakýmkoli záměrem nebo entitou, přidejte do seznamu frázi slova ze sekundárního jazyka. Nakonfigurujte seznam frází jako globální funkci.

## <a name="best-practices"></a>Osvědčené postupy

Seznamte se s [osvědčenými postupy](luis-concept-best-practices.md).

## <a name="next-steps"></a>Další kroky

* [Rozšíříte](schema-change-prediction-runtime.md) své modely aplikací v prostředí předpovědi.
* Další informace o tom, jak přidat funkce do aplikace LUIS, najdete v tématu věnovaném [Přidání funkcí](luis-how-to-add-features.md) .
