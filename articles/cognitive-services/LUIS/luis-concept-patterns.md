---
title: Zjistěte, jak vzory zvyšte přesnost předpovědi | Microsoft Docs
titleSuffix: Azure
description: Naučte se o zvýšení skóre záměrné předpovědi a najít entity vzory návrhu.
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.technology: luis
ms.topic: article
ms.date: 06/08/2018
ms.author: v-geberr
ms.openlocfilehash: c3c0d12bbbeec85d2cbf0daead49ee16ca7728fb
ms.sourcegitcommit: 0c490934b5596204d175be89af6b45aafc7ff730
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/27/2018
ms.locfileid: "37046254"
---
# <a name="patterns-improve-prediction-accuracy"></a>Vzory zvyšte přesnost předpovědi
Vzory jsou navrženy a zvyšte tak přesnost při několik utterances jsou velmi podobné. Poskytnutím vzor pro utterance může obsahovat LEOŠ vysoce důvěryhodných předpovědi. 

## <a name="patterns-solve-low-intent-confidence"></a>Vzory vyřešit nízkou záměrné spolehlivosti
Vezměte v úvahu aplikaci lidských zdrojů, která hlásí organizační grafu ve vztahu k zaměstnanec. Zadaný název a vztah zaměstnance, LEOŠ vrátí zaměstnanci související se situací. Vezměte v úvahu zaměstnanec tní, s manažera název Alice a tým podřízené s názvem: Michael Milena a David.

![Obrázek organizačního diagramu](./media/luis-concept-patterns/org-chart.png)

|Projevy|Záměr předpovědět|Záměrné skóre|
|--|--|--|
|Kdo je na tní podřízená?|GetOrgChart|.30|
|Kdo je podřízená položka tní?|GetOrgChart|.30|

Pokud aplikace má mezi 10 a 20 utterances s různých délek věty, jiném pořadí aplikace word a i jiná slova (synonyma "podřízený", "manage", "zpráva"), může LEOŠ vracejí skóre nízkou spolehlivosti. Abyste pomohli pochopit význam pořadí slov LEOŠ, vytvoří se vzorem. 

Vzory pro řešení těchto situacích: 

* Když je záměrné skóre nízká
* Při správné záměr není nejvyšší skóre, ale na nejvyšší skóre příliš zavřít. 

## <a name="patterns-are-not-a-guarantee-of-intent"></a>Vzory nejsou záruku záměru
Vzory použít kombinaci technologie předpovědi. Nastavení pro šablony utterance záměrem ve tvaru není zárukou toho záměrné předpovědi, ale je silný signál. 

## <a name="patterns-do-not-improve-entity-detection"></a>Vzory pro zlepšení detekce entity
Zatímco vzory vyžadují entity, vzor nepomůže, zjišťovat entity. Vzor je určená jenom pomohou předpovědi pomocí tříd Intent a rolí.  

## <a name="patterns-use-entity-roles"></a>Vzory použít entity role
Pokud souvisejí kontextově dvě nebo více entit ve tvaru, použijte vzorce entity [role](luis-concept-roles.md) extrahovat kontextové informace o entitách. To je ekvivalentní podřízených objektů hierarchické entity, ale je **pouze** k dispozici v vzory. 

## <a name="prediction-scores-with-and-without-patterns"></a>Skóre předpovědi s i bez vzory
Zadané dostatek utterances příklad, budou moci zvýšit důvěru předpovědi bez vzory LEOŠ. Vzory zvýšit důvěru skóre bez nutnosti poskytnout tolik utterances.  

## <a name="pattern-matching"></a>Shoda vzoru
Vzor je nalezena shoda podle nejprve zjišťování entit uvnitř vzor, pak ověření zbytek slova a word pořadí vzoru. Ve vzoru pro vzor tak, aby odpovídaly se vyžadují entity. 

## <a name="pattern-syntax"></a>Vzor syntaxe
Vzor syntaxe je šablona pro utterance. Šablona musí obsahovat slova a entity, které chcete porovnat a také slova nebo interpunkční znaménka, které chcete ignorovat. Je **není** regulární výraz. 

Entity v vzory jsou obklopená složené závorky, `{}`. Vzory může obsahovat entity a entity role. Pattern.any je používáno pouze pro vzorce entity. Syntaxe je vysvětlené v následujících částech.

### <a name="syntax-to-add-an-entity-to-a-pattern-template"></a>Syntaxe pro přidání entity do šablony vzor
Pokud chcete přidat entitu do šablony vzor, uzavřete název entity s složené závorky, `Who does {Employee} manage?`. 

```
Who does {Employee} manage?
```

### <a name="syntax-to-add-an-entity-and-role-to-a-pattern-template"></a>Syntaxe přidat do šablony vzor entity a role
O roli entity je označené jako `{entity:role}` názvem entity následovaný dvojtečkou a potom název role. Pokud chcete přidat entitu s rolí do šablony vzor, uzavřete název entity a název role s složené závorky, například `Book a ticket from {Location:Origin} to {Location:Destination}`. 

```
Book a ticket from {Location:Origin} to {Location:Destination}
```

### <a name="syntax-to-add-a-patternany-to-pattern-template"></a>Syntaxe pro přidání pattern.any do vzor šablony
Entita Pattern.any umožňuje přidat entitu různé délky se vzorem. Tak dlouho, dokud je následovaný vzor šablony, pattern.any může být jakékoli délky. 

Chcete-li přidat **Pattern.any** entity do šablony vzor obklopit Pattern.any entita s složené závorky, jako například `How much does {Booktitle} cost and what format is it available in?`.  

```
How much does {Booktitle} cost and what format is it available in?
```

|Názvů knih ve vzoru|
|--|
|Kolik **ukrást tato kniha** náklady a jaký formát je k dispozici v?|
|Kolik **požádejte** náklady a jaký formát je k dispozici v?|
|Kolik **zvědaví Incident pes včas noc** náklady a jaký formát je k dispozici v?| 

V těchto příkladech, název adresáře nejsou kontextové slova názvu adresáře pro LEOŠ matoucí. LEOŠ ví, kde název knihy skončí kvůli tomu je ve tvaru a označené jako Pattern.any entity.

### <a name="explicit-lists"></a>Explicitní seznamy
Pokud vaše vzor obsahuje Pattern.any a umožňuje vzor syntaxe pro možnost extrakci nesprávný entity podle utterance, vytvořte [explicitní seznam](https://aka.ms/ExplicitList) prostřednictvím rozhraní API pro vytváření obsahu povolit výjimku. 

Předpokládejme například, máte vzor obsahující obě volitelné syntaxe `[]`a syntaxi entity `{}`, kombinované způsobem nesprávně extrahovat data.

Vezměte v úvahu vzoru '[vyhledá] e-mailů o {subjektu} [{osoba}]'. V následující utterances **subjektu** a **osoba** entity se extrahují správně a nesprávně:

|utterance|Entita|Správné extrakce|
|--|--|:--:|
|e-mailu o PSI z Jan|předmět = PSI<br>osoba = Jan|✔|
|e-mailu o man z La Mancha|předmět = na man<br>osoba = La Mancha|X|

V předchozí tabulce, utterance `email about the man from La Mancha`, uvedený předmět by měl být `the man from La Mancha` (název adresáře), ale protože subjekt zahrnuje volitelné slovo `from`, název je nesprávně předpovědět. 

Chcete-li opravit tato výjimka se vzorem, přidejte `the man from la mancha` jako explicitní seznam v případě shody s využitím entity {subjektu} [vytváření rozhraní API pro explicitní seznam](https://aka.ms/ExplicitList).

### <a name="syntax-to-mark-optional-text-in-a-template-utterance"></a>Syntaxe označit volitelné textu v utterance šablony
Označit volitelné textu v utterance pomocí syntaxe regulárního výrazu hranatá závorka, `[]`. Volitelné text lze vnořit hranaté závorky až pouze dvě závorky.

```
[find] email about {subject} [from {person}]
```

Interpunkční znaménka, například `.`, `!`, a `?` můžete ignorovat pomocí hranaté závorky. Chcete-li ignorovat tyto značky, každý značky musí být v samostatné vzoru. Volitelné syntaxe aktuálně nepodporuje ignoruje položku v seznamu několik položek.

## <a name="patterns-only"></a>Vzory pouze
LEOŠ umožňuje aplikaci, bez jakékoli utterances příklad v záměr. Toto použití je povoleno pouze v případě, že se nepoužívají vzory. Vzory vyžadují minimálně jednu entitu v každé vzoru. Pro aplikaci jen vzor vzoru nesmí obsahovat naučili počítač entity, protože ty vyžadují utterances příklad. 

## <a name="best-practices"></a>Osvědčené postupy
Další informace [osvědčené postupy](luis-concept-best-practices.md).

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Postup implementace vzorů v tomto kurzu](luis-tutorial-pattern.md)

[LUIS]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions
