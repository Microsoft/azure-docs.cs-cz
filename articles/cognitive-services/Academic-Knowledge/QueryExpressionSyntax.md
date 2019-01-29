---
title: Syntaxe výrazu dotazu – Academic Knowledge API
titlesuffix: Azure Cognitive Services
description: Další informace o použití syntaxe výrazu dotazu v rozhraní Academic Knowledge API.
services: cognitive-services
author: alch-msft
manager: cgronlun
ms.service: cognitive-services
ms.subservice: academic-knowledge
ms.topic: conceptual
ms.date: 03/27/2017
ms.author: alch
ms.openlocfilehash: c130c6cd5fcb5191195712f570db66408734200a
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2019
ms.locfileid: "55150863"
---
# <a name="query-expression-syntax"></a>Syntaxe výrazu dotazu

Jsme viděli, která odpověď **interpretovat** žádost obsahuje výraz dotazu. Gramatiky, který interpretován dotaz uživatele vytvořit výraz dotazu pro každý výkladu. Výraz dotazu je pak možné použít k problému **vyhodnotit** požadavek na načtení výsledků vyhledávání entit.

Můžete také vytvořit vlastní výrazy dotazu a použít je v **vyhodnotit** požadavku. To může být užitečné, pokud vytváříte vlastní uživatelské rozhraní, které vytvoří výrazu dotazu v reakci na akce uživatele. Chcete-li to provést, je potřeba vědět syntaxe výrazů dotazů.  

Každý atribut entity, které mohou být součástí výrazu dotazu má zvláštní datový typ a sadu operátorů dotazu je to možné. Sada atributů entity a podporované operátory pro každý atribut je zadán v [atributů Entity](EntityAttributes.md). Dotaz na hodnotu single vyžaduje atribut pro podporu *rovná* operace. Předpona dotaz vyžaduje atribut pro podporu *StartsWith* operace. Dotazy na číselný rozsah vyžaduje atribut pro podporu *IsBetween* operace.

Některá entity data se ukládají jako složený atributy je určeno tečku "." v názvu atributu. Například informace o autorovi/přidružení je vyjádřena jako složený atribut. Obsahuje 4 komponenty: AuN, AuId AfN, AfId. Tyto součásti jsou samostatné části data, která tvoří hodnotu atributu jedné entity.


**Atribut řetězců: Jedna hodnota** (včetně shody proti synonym)  
Čas = "indexování službou latentní sémantické analýzy.  
Composite(AA.AuN='sue dumais')

**Atribut řetězců: Přesná jednu hodnotu** (odpovídá pouze kanonických hodnot)  
Ti == "indexování službou latentní sémantické analýzy.  
Složené (AA. AuN == "dumais susan t")
     
**Atribut řetězců: Hodnota předpony**   
Čas = 'indexování službou latentní seman'...  
Složené (AA. AuN =... "procesní rozlišované sjednocení typu")

**Číselné atribut: Jedinou hodnotu**  
Y=2010
 
**Číselné atribut: Hodnota rozsahu**  
Y>2005  
Y>=2005  
Y<2010  
Y<=2010  
Y =\[2010, 2012\) (obsahuje hodnotu pouze levé hranice: 2010, 2011)  
Y =\[2010, 2012\] (zahrnuje obě hodnoty hranic: 2010, 2011, 2012)
 
**Číselné atribut: Hodnota předpony**  
Y = "19"... (libovolná číselná hodnota, která začíná textem 19) 
 
**Atribut Datum: Jedinou hodnotu**  
D = "2010-02-04.

**Atribut Datum: Hodnota rozsahu**  
D &GT; "2010-02-03.  
D = ["2010-02-03", "2010-02-05"]

**A/nebo dotazy:**  
A (Y = 1985, čas = "disordered elektronických systémy")  
Nebo (čas = "disordered elektronických systémy", čas = "zásady odolnost proti chybám a postupem")  
And(OR(Y=1985,Y=2008), čas = "disordered elektronických systémy")
 
**Složený dotazy:**  
Dotaz součástí složeného atributu budete muset uzavřít součástí výrazu dotazu, který odkazuje na atribut složené ve funkci Composite(). 

Například dotaz na dokumenty Paper podle jméno autora, použijte následující dotaz:
```
Composite(AA.AuN='mike smith')
```
<br>Dotaz pro Paper konkrétním autorem, když byl autorem na konkrétní instituce, použijte následující dotaz:
```
Composite(And(AA.AuN='mike smith',AA.AfN='harvard university'))
```
<br>Funkce Composite() spojuje dvě části složené atributu. To znamená, že používáme pouze ty Paper kde jedním z autorů "Jan Macek" je při pracoval bruno v Harvard. 

Dotaz pro Paper podle konkrétního autora v umístění s (ostatní) tvůrci používající konkrétní instituce, použijte následující dotaz:
```
And(Composite(AA.AuN='mike smith'),Composite(AA.AfN='harvard university'))
```
<br>V této verzi protože Composite() se použije pro autora a přidružení jednotlivě před And(), získáme všech dokumentů, kde jedním z autorů je "Jan Macek" a jedním z autorů afilace je "Harvard". To zní podobně jako předchozí příklad dotazu, ale není totéž.

Obecně platí zvažte následující příklad: Máme složené atribut C, který má dvě součásti A a B. Entita může mít více hodnot pro C. Toto jsou naše entity:
```
E1: C={A=1, B=1}  C={A=1,B=2}  C={A=2,B=3}
E2: C={A=1, B=3}  C={A=3,B=2}
```

<br>Dotaz 
```
Composite(And(C.A=1, C.B=2))
```

<br>odpovídá pouze entity, které mají hodnotu pro jazyk C, kde je součást C.A 1 a 2 je součást C.B. Pouze E1 se shoduje s Tento dotaz.

Dotaz 
```
And(Composite(C.A=1), Composite(C.B=2))
```
<br>odpovídá entity, které mají hodnotu pro jazyk C, kde C.A je 1 a také obsahovat hodnotu pro jazyk C kde C.B je 2. E1 a E2 odpovídají tomuto dotazu.

Poznámka:  
- Součástí složeného atribut mimo funkci Composite() nelze odkazovat.
- Součástí dvou různých atributů složené uvnitř stejné funkce Composite() nelze odkazovat.
- Atribut, který není součástí složeného atribut uvnitř funkce Composite() nelze odkazovat.
