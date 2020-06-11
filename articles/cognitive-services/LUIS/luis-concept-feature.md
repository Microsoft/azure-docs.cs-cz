---
title: Funkce – LUIS
description: Přidáním funkcí do jazykového modelu poskytněte nápovědu týkající se rozpoznávání vstupu, který chcete označit nebo klasifikovat.
ms.topic: conceptual
ms.date: 06/10/2020
ms.openlocfilehash: 823c51f0b58481e30ff54814dde03285ad094b9e
ms.sourcegitcommit: f01c2142af7e90679f4c6b60d03ea16b4abf1b97
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/10/2020
ms.locfileid: "84677587"
---
# <a name="machine-learning-ml-features"></a>Funkce strojového učení (ML)

Ve službě Machine Learning je **funkce**   odlišující se vlastností a atributů dat, která systém sleduje a se učí.

Funkce strojového učení poskytují LUIS důležité pomůcky pro hledání věcí, které budou rozlišovat koncept. Jsou to doporučení, která LUIS můžou používat, ale ne pevná pravidla.  Tyto pomocné parametry se používají ve spojení s popisky pro hledání dat.

## <a name="what-is-a-feature"></a>Co je funkce

Funkce je rozlišující vlastnosti, které lze popsat jako funkci: f (x) = y. Funkce se používá k zjištění, kde hledat, v příkladu utterance, pro odlišení vlastnosti. Co se při vytváření vašeho schématu dozvíte o příkladu utterance, který označuje vlastnost? Vaše odpověď je nejlepším osvědčeným průvodcem vytvářením funkcí.

## <a name="types-of-features"></a>Typy funkcí

 LUIS podporuje seznamy frází i modely jako funkce:
* Funkce seznamu frází
* Model (záměr nebo entita) jako funkce

Funkce by měly být považovány za nezbytnou součást návrhu schématu.

## <a name="how-you-find-features-in-your-example-utterances"></a>Jak najít funkce v příkladu projevy

Vzhledem k tomu, že LUIS je jazykově založená aplikace, budou mít funkce založené na textu. Vyberte text, který určuje vlastnost, kterou chcete odlišit. V případě LUIS je textovým nejmenším procesorem token. V anglickém jazyce je token souvislým rozsahem bez mezer nebo interpunkčních znamének, písmen a číslic. Prostor není token.

Vzhledem k tomu, že mezery a interpunkční znaménka nejsou tokeny, zaměřte se na text, který můžete použít jako funkce. Nezapomeňte zahrnout variace těchto slov:
* množné formuláře
* vhodné příkazů
* zkratka
* pravopis a pravopis

Má text jako rozlišující vlastnost tyto vlastnosti:
* Porovnat přesné slovo nebo frázi – zvažte přidání entity regulárního výrazu nebo seznamu entit jako funkce pro entitu nebo záměr.
* Porovnává známý pojem, jako jsou data, časy nebo jména uživatelů – jako funkci pro entitu nebo záměr použijte předem vytvořenou entitu.
* Naučte se nové příklady v čase – použijte seznam frází některých příkladů konceptu jako funkci pro entitu nebo záměr.

## <a name="combine-features"></a>Kombinování funkcí

Vzhledem k tomu, že existuje několik možností, jak je popsána vlastnost, můžete použít více než jednu funkci, která pomáhá popsat daný znak nebo koncept. Společné párování je použití funkce seznamu frází a jednoho z typů entit, které se běžně používají jako funkce: předem sestavená entita, entita regulárního výrazu nebo entita seznamu.

### <a name="ticket-booking-entity-example"></a>Příklad entity rezervace lístku

Jako první příklad si představte aplikaci pro rezervaci letu s záměrem rezervace letu a entitou rezervace lístku.

Entita rezervace lístku je entita získaná počítačem pro cílové umístění letu. Chcete-li získat informace o umístění, použijte dvě funkce, které vám pomůžou:
* Seznam frází relevantních slov, jako například, `plane` `flight` , `reservation` ,`ticket`
* Předem vytvořená `geographyV2` entita jako funkce pro entitu

### <a name="pizza-entity-example"></a>Příklad entity Pizza

Jako jiný příklad si představte aplikaci pro seřazení Pizza s cílem vytvořit objednávku Pizza a entitu Pizza.

Entita Pizza je entita získaná počítačem pro podrobnosti o Pizza. Chcete-li získat podrobné informace, použijte dvě funkce, které vám pomůžou:
* Seznam frází relevantních slov, jako například, `cheese` `crust` , `pepperoni` ,`pineapple`
* Předem vytvořená `number` entita jako funkce pro entitu

## <a name="a-phrase-list-for-a-particular-concept"></a>Seznam frází pro konkrétní koncept

Seznam frází je seznam slov nebo frází, které zapouzdřují konkrétní koncept a jsou aplikovány jako porovnávání bez rozlišení velkých a malých písmen na úrovni tokenu.

Když přidáváte seznam frází, můžete tuto funkci nastavit jako:
* **[Globální](#global-features)**. Globální funkce se vztahuje na celou aplikaci.

### <a name="when-to-use-a-phrase-list"></a>Kdy použít seznam frází

Pokud potřebujete, aby vaše aplikace LUIS dokázala zobecnit a identifikovat nové položky pro daný koncept, použijte seznam frází. Seznamy frází jsou podobně jako slovníky specifické pro doménu, které vám pomůžou se zvýšením kvality porozumění jejich záměrům a entitám.

### <a name="how-to-use-a-phrase-list"></a>Jak používat seznam frází

Seznam frází LUIS považuje za kontext a generalizy k identifikaci položek, které jsou podobné, ale nikoli přesného textu.

Postup použití seznamu frází:
* Začínáme s entitou strojového učení
    * Přidat ukázkové promluvy
    * Popisek s entitou strojového učení
* Přidat seznam frází
    * Přidat slova s podobným významem – **Nepřidávat všechna** možná slova nebo fráze. Místo toho přidejte několik slov nebo frází najednou a pak je přehlaste a publikujte.
    * Kontrola a přidání navrhovaných slov

### <a name="a-typical-scenario-for-a-phrase-list"></a>Typický scénář pro seznam frází

Typický scénář pro seznam frází vám umožní zvýšit slova související s konkrétním nápadem.

Příkladem slov, která můžou potřebovat seznam frází, jsou lékařské výrazy. Tyto výrazy mohou mít konkrétní fyzický, chemický, léčebný nebo abstraktní význam. LUIS neví, že tyto výrazy jsou důležité pro vaši doménu subjektu bez seznamu frází.

Pokud chcete extrahovat lékařské výrazy:
* Nejprve v rámci těchto projevy vytvořte příklad lékařského lékařství projevy a návěští.
* Pak vytvořte seznam frází s příklady podmínek v doméně předmětu. Seznam frází by měl zahrnovat skutečný termín, který jste popsali, a další podmínky, které popisují stejný pojem.
* Přidejte seznam frází k entitě nebo podentitě, která extrahuje koncept použitý v seznamu frází. Nejběžnějším scénářem je součást (podřízená) entita strojového učení. Pokud má být seznam frází použit ve všech záměrech nebo entitách, označte seznam frází jako globální seznam frází. `enabledForAllModels`Příznak řídí tento rozsah modelu v rozhraní API.

### <a name="token-matches-for-a-phrase-list"></a>Shody tokenu pro seznam frází

Seznam frází se vztahuje na úroveň tokenu bez ohledu na velikost písmen. Následující graf znázorňuje, jak se seznam frází obsahující slovo `Ann` aplikuje na variace stejných znaků v tomto pořadí.


| Variace tokenu`Ann` | Shoda v seznamu frází, když se najde token |
|--------------------------|---------------------------------------|
| ANN<br>aNN<br>           | Ano – token je`Ann`                  |
| Ann                    | Ano – token je`Ann`                  |
| Anne                     | No – token je`Anne`                  |


<a name="how-to-use-phrase-lists"></a>
<a name="how-to-use-a-phrase-lists"></a>
<a name="phrase-lists-help-identify-simple-exchangeable-entities"></a>

## <a name="a-model-as-a-feature-helps-another-model"></a>Model jako funkce pomáhá jinému modelu.

Model (záměr nebo entitu) můžete přidat jako funkci jinému modelu (záměr nebo entitu). Přidáním existujícího záměru nebo entity jako funkce přidejte dobře definovaný koncept s příklady s popisky.

Při přidávání modelu jako funkce můžete tuto funkci nastavit jako:
* **[Požadováno](#required-features)**. Aby byl model vrácen z koncového bodu předpovědi, je nutné najít požadovanou funkci.
* **[Globální](#global-features)**. Globální funkce se vztahuje na celou aplikaci.

### <a name="when-to-use-an-entity-as-a-feature-to-an-intent"></a>Kdy použít entitu jako funkci k záměru

Přidejte entitu jako funkci k záměru, když je zjišťování této entity pro záměr významné.

Například pokud je záměrem rezervace letu, `BookFlight` a entita je informace o lístkech (například počet míst, počátek a cíl), pak by se měla entita s informacemi o lístkech zvýšit důležitou váhou pro předpověď `BookFlight` záměru.

### <a name="when-to-use-an-entity-as-a-feature-to-another-entity"></a>Kdy použít entitu jako funkci pro jinou entitu

Entita (A) by měla být přidána jako funkce jiné entitě (B), pokud je zjišťování této entity (A) významné pro předpověď entity (B).

Pokud například entita adresa pro expedici obsahovala subentitu ulice, pak hledání podentity ulice přidá důležitou váhu předpovědi pro entitu dodací adresy.

* Adresa příjemce (entita počítač se naučila)
    * Číslo ulice (subentity)
    * Ulice (subentity)
    * Město (subentity)
    * Okres (subentity)
    * Země/oblast (subentita)
    * Poštovní směrovací číslo (subentity)

## <a name="nested-subentities-with-features"></a>Vnořené subentity s funkcemi

Počítač, na který se odkazuje subentita, indikuje, že nadřazená entita má k dispozici koncept, ať už je nadřazeným objektem jiná subentita nebo nejvyšší entita. Hodnota subentity funguje jako funkce nadřazeného objektu.

Podentita může mít jak seznam frází jako funkci, tak i jako model (jinou entitu) jako funkce.

Pokud má podentita seznam frází, bude se zvyšovat slovní slovo konceptu, ale nepřidá žádné informace do odpovědi JSON předpovědi.

Pokud má podentita funkci jiné entity, odpověď JSON zahrnuje extrahovaná data této jiné entity.

## <a name="required-features"></a>Požadované funkce

Aby byl model vrácen z koncového bodu předpovědi, je nutné najít požadovanou funkci. Požadovanou funkci použijte, když víte, že příchozí data se musí shodovat s funkcí.

Pokud text utterance neodpovídá požadované funkci, nebude extrahován.

**Požadovaná funkce používá nepočítačovou entitu, která se nejedná o počítač**:
* Entiay regulárního výrazu
* Entita seznamu
* Předem vytvořená entita

Jaké jsou dobré funkce k označení jako povinné? Pokud jste si jistí, že se model nachází v datech, nastavte funkci podle potřeby. Požadovaná funkce nevrátí žádnou hodnotu, pokud nebyla nalezena.

Pokračuje se v příkladu adresy pro expedici:
* Adresa příjemce (entita počítač se naučila)
    * Číslo ulice (subentity)
    * Ulice (subentity)
    * Název ulice (subentity)
    * Město (subentity)
    * Okres (subentity)
    * Země/oblast (subentita)
    * Poštovní směrovací číslo (subentity)

### <a name="required-feature-using-prebuilt-entities"></a>Požadovaná funkce s využitím předem připravených entit

Město, stát a země/oblast jsou všeobecně uzavřenou sadou seznamů, což znamená, že se v průběhu času nemění. Tyto entity mohou mít relevantní doporučené funkce a tyto funkce mohou být označeny jako povinné. To znamená, že se nevrátí celá dodací adresa, protože se nenašly entity s požadovanými funkcemi.

Co když se město, stát nebo země/oblast nacházejí v utterance, ale buď v umístění, nebo v slangem, které LUIS neočekává? Pokud chcete poskytnout nějakému následnému zpracování, aby bylo možné entitu vyřešit, z důvodu nízkého skóre spolehlivosti z LUIS, neoznačujte funkci jako povinnou.

Dalším příkladem požadované funkce pro doručovací adresu je vytvoření [předem připraveného](luis-reference-prebuilt-entities.md) čísla. Uživatel tak může zadat "1 Microsoft Way" nebo "One Microsoft". Obě budou přeloženy na číslo "1" pro subentity čísla ulice.

### <a name="required-feature-using-list-entities"></a>Požadovaná funkce využívající seznam entit

[Entita seznamu](reference-entity-list.md) se používá jako seznam kanonických názvů spolu s jejich synonymy. Pokud utterance neobsahuje kanonický název nebo synonymum, jako požadovaná funkce se entita nevrátí jako součást koncového bodu předpovědi.

S příkladem dodací adresy uvažujte o tom, že vaše společnost je dodávána pouze do omezené skupiny zemí nebo oblastí. Můžete vytvořit entitu seznamu, která zahrnuje několik způsobů, kterými se zákazník může na zemi odkázat. Pokud LUIS nenajde přesnou shodu v textu utterance, pak se v předpovědi nevrátí entita (která má požadovanou funkci entity seznam).

|Kanonický název|Synonyma|
|--|--|
|USA|USA:<br>U. S. A<br>USA<br>USA<br>0|

Klientská aplikace, jako je například robot, může požádat o následnou otázku, takže zákazník chápe, že výběr země nebo oblasti je omezený a je _povinný_.

### <a name="required-feature-using-regular-expression-entities"></a>Požadovaná funkce využívající entity regulárních výrazů

[Entita regulárního výrazu](reference-entity-regular-expression.md) použitá jako požadovaná funkce poskytuje funkce pro přizpůsobení textu.

V případě pokračování v dodací adrese můžete vytvořit regulární výraz, který zachycuje pravidla syntaxe poštovních směrovacích čísel země nebo oblasti.

## <a name="global-features"></a>Globální funkce

Obecně platí, že pokud chcete použít funkci pro konkrétní model, můžete tuto funkci nakonfigurovat jako **globální funkci** a použít ji pro celou aplikaci.

Nejběžnějším použitím globální funkce je přidání dalšího slovníku, jako je například slova z jiného jazyka, do aplikace. Pokud vaši zákazníci používají primární jazyk, ale očekává se, že bude moci používat jiný jazyk v rámci stejného utterance, můžete přidat funkci, která obsahuje slova ze sekundárního jazyka.

Vzhledem k tomu, že uživatel očekával použití druhého jazyka napříč jakýmkoli záměrem nebo entitou, měla by být přidána do seznamu frází se seznamem frází nakonfigurovaným jako globální funkce.

## <a name="best-practices"></a>Osvědčené postupy
Seznamte se s [osvědčenými postupy](luis-concept-best-practices.md).

## <a name="next-steps"></a>Další kroky

* [Rozšiřování](schema-change-prediction-runtime.md) modelů aplikací v prostředí předpovědi
* Další informace o tom, jak přidat funkce do aplikace LUIS, najdete v tématu věnovaném [Přidání funkcí](luis-how-to-add-features.md) .
