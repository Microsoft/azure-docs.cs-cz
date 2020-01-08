---
title: Syntaxe výrazu dotazu – Academic Knowledge API
titlesuffix: Azure Cognitive Services
description: Naučte se používat syntaxi výrazu dotazu v Academic Knowledge API.
services: cognitive-services
author: alch-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: academic-knowledge
ms.topic: conceptual
ms.date: 03/27/2017
ms.author: alch
ROBOTS: NOINDEX
ms.openlocfilehash: 4056ecba7ac80436952228da9e1b74dc7382448c
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/25/2019
ms.locfileid: "75448954"
---
# <a name="query-expression-syntax"></a>Syntaxe výrazu dotazu

Zjistili jsme, že odpověď na požadavek **interpreta** obsahuje výraz dotazu. Gramatika, která interpretuje dotaz uživatele, vytvořila výraz dotazu pro každou interpretaci. Výraz dotazu se pak dá použít k vystavení žádosti o **vyhodnocení** pro načtení výsledků hledání entit.

Můžete také vytvořit vlastní výrazy dotazu a použít je v žádosti o **vyhodnocení** . To může být užitečné, pokud vytváříte vlastní uživatelské rozhraní, které v reakci na akce uživatele vytvoří výraz dotazu. K tomu potřebujete znát syntaxi pro výrazy dotazů.  

Každý atribut entity, který lze zahrnout do výrazu dotazu, má určitý datový typ a sadu možných operátorů dotazů. Sada atributů entity a podporované operátory pro každý atribut jsou zadány v [atributech entity](EntityAttributes.md). Dotaz s jednou hodnotou vyžaduje, aby atribut podporoval operaci *EQUAL* . Dotaz předpony vyžaduje, aby atribut podporoval operaci *StartsWith* . Dotazy číselného rozsahu vyžadují, aby atribut podporoval operaci *mezi* .

Některá data entity jsou ukládána jako složené atributy, které jsou označeny tečkou "." v názvu atributu. Například informace o autorovi nebo afilaci jsou reprezentovány jako složený atribut. Obsahuje 4 součásti: AuN, AuId, AfN, AfId. Tyto součásti jsou oddělené části dat, které tvoří jednu hodnotu atributu entity.

Poznámka: všechny výrazy dotazu musí být malými písmeny a bez speciálních znaků.

**Řetězcový atribut: jedna hodnota** (včetně shody s synonymy)  
ČŘ = Indexing podle latentní sémantické analýzy  
Složené (AA. AuN = ' Sue Dumais ')

**Řetězcový atribut: přesná jediná hodnota** (odpovídá jenom kanonickým hodnotám)  
ČŘ = = ' indexování pomocí latentní sémantické analýzy '  
Složené (AA. AuN = = "Zuzana t Dumais")
     
**Řetězcový atribut: hodnota předpony**   
Ti = "indexování pomocí latentních Seman"...  
Složené (AA. AuN = ' Sue du '...)

**Číselný atribut: jedna hodnota**  
Y=2010
 
**Číselný atribut: hodnota rozsahu**  
Y>2005  
Y > = 2005  
Y<2010  
Y<=2010  
Y =\[2010, 2012\) (zahrnuje jenom levou hodnotu hranice: 2010, 2011)  
Y =\[2010, 2012\] (zahrnuje hodnoty mezních hodnot: 2010, 2011, 2012)
 
**Číselný atribut: hodnota předpony**  
Y = 19... (libovolná číselná hodnota, která začíná 19) 
 
**Atribut data: jedna hodnota**  
D = "2010-02-04"

**Atribut data: hodnota rozsahu**  
D > "2010-02-03"  
D = [' 2010-02-03 ', ' 2010-02-05 ']

**A/nebo dotazy:**  
A (Y = 1985, ČŘ = "neuspořádané elektronické systémy")  
Nebo (ti = "vyřazení elektronické systémy", ČŘ = "principy a cvičení odolnosti proti chybám")  
A (nebo (Y = 1985, Y = 2008), ti = "rozobjednávkované elektronické systémy")
 
**Složené dotazy:**  
Chcete-li zadat dotaz na součásti složeného atributu, je nutné uzavřít část výrazu dotazu, který odkazuje na složený atribut v složené () funkci. 

Chcete-li například zadat dotaz na doklady podle jména autora, použijte následující dotaz:
```
Composite(AA.AuN='mike smith')
```
<br>Pokud chcete zadat dotaz na doklady podle konkrétního autora, zatímco autor byl na konkrétní instituci, použijte následující dotaz:
```
Composite(And(AA.AuN='mike smith',AA.AfN='harvard university'))
```
<br>Funkce Composite () spojuje dvě části složeného atributu dohromady. To znamená, že budeme dostávat jenom dokumenty, ve kterých je jeden z autorů "Jan Novák", ale byl na Harvard. 

K dotazování na doklady podle konkrétního autora v afilacích s (jinými) autory z konkrétní instituce použijte následující dotaz:
```
And(Composite(AA.AuN='mike smith'),Composite(AA.AfN='harvard university'))
```
<br>Vzhledem k tomu, že je v této verzi použita možnost složené () pro autor a přidružení jednotlivě před a (), získáme všechny dokumenty, u nichž jeden z autorů je "Jan Novák" a jedna z afilací autoři je "Harvard". Tyto zvuky se podobají předchozímu příkladu dotazu, ale nejedná se o stejné věci.

Obecně platí, že je třeba vzít v úvahu následující příklad: máme složený atribut C, který má dvě komponenty A a B. Entita může mít více hodnot pro C. Jedná se o naše entity:
```
E1: C={A=1, B=1}  C={A=1,B=2}  C={A=2,B=3}
E2: C={A=1, B=3}  C={A=3,B=2}
```

<br>Dotaz 
```
Composite(And(C.A=1, C.B=2))
```

<br>odpovídá pouze entitám, které mají hodnotu pro C, kde komponenta C. A je 1 a komponenta C. B je 2. Tento dotaz odpovídá pouze E1.

Dotaz 
```
And(Composite(C.A=1), Composite(C.B=2))
```
<br>odpovídá entitám, které mají hodnotu pro C, kde C. A je 1 a má také hodnotu pro C, kde C. B je 2. Tento dotaz odpovídají hodnotám E1 a E2.

Poznámka:  
- Nejde odkazovat na část složeného atributu mimo funkci složeného ().
- V rámci stejné složené funkce () nelze odkazovat na části dvou různých složených atributů.
- Nejde odkazovat na atribut, který není součástí složeného atributu uvnitř složené funkce ().
