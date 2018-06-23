---
title: Syntaxe výrazu v Academic Knowledge API dotazu | Microsoft Docs
description: Další informace o použití syntaxe výrazu dotazu v Academic Knowledge API kognitivní služeb společnosti Microsoft.
services: cognitive-services
author: alch-msft
manager: kuansanw
ms.service: cognitive-services
ms.component: academic-knowledge
ms.topic: article
ms.date: 03/27/2017
ms.author: alch
ms.openlocfilehash: 6ec338fff09954e2f14066ce2b83bc1228794af8
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2018
ms.locfileid: "35342382"
---
# <a name="query-expression-syntax"></a>Syntaxe výrazu dotazu

Jsme viděli, který odpověď na **interpretovat** žádost obsahuje výrazu dotazu. Gramatika, která interpretovat dotazu uživatele vytvořit výraz dotazu pro každý interpretace. Výrazu dotazu je pak možné použít k problému **vyhodnotit** žádosti o načtení entity výsledky hledání.

Můžete také vytvořit vlastní výrazy dotazů a použít je **vyhodnotit** požadavku. To může být užitečné, pokud vytváříte vlastní uživatelské rozhraní, které vytvoří výrazu dotazu v odpovědi na akce uživatele. Chcete-li to provést, musíte znát syntaxe pro výrazy dotazů.  

Každý atribut entity, který může být součástí výrazu dotazu má určitý datový typ a sadu operátory možné dotazu. Sadu atributů entity a podporovaných operátory pro každý atribut je uveden v [atributům Entity](EntityAttributes.md). Dotaz jednou hodnotou vyžaduje atribut pro podporu *rovná* operaci. Předpona dotazu vyžaduje atribut pro podporu *StartsWith* operaci. Číselný rozsah dotazy vyžaduje atribut pro podporu *IsBetween* operaci.

Některé dat entity jsou uložené jako složený atributy, které je uvedené tečkou '.' v názvu atributu. Například informace autora nebo přidružení je reprezentována jako složený atribut. Obsahuje součásti 4: AuN, AuId, AfN, AfId. Tyto součásti jsou samostatné části data, která tvoří hodnotu atributu jedné entity.


**Atribut řetězce: Jedna hodnota** (zahrnuje odpovídá proti synonyma)  
Ti = 'indexování latentní sémantického analýza.  
Složené (AA. AuN = 'procesní dumais.)

**Atribut řetězce: Přesnou jednu hodnotu** (odpovídá pouze kanonických hodnot)  
Ti == "indexování latentní sémantického analýza.  
Složené (AA. AuN == 'susan t dumais.)
     
**Řetězec atributu: Hodnota předpony**   
Ti = 'indexování podle latentní seman'...  
Složené (AA. AuN = 'procesní du,...)

**Číselného atribut: Jednu hodnotu**  
Y = 2010
 
**Číselné atribut: Hodnota rozsahu**  
Y &GT; 2005  
Y &GT; = 2005  
Y &LT; 2010  
Y &LT; = 2010  
Y =\[2010, 2012\) (obsahuje hodnotu pouze levé hranic: 2010, 2011)  
Y =\[2010, 2012\] (zahrnuje obě hodnoty hranic: 2010, 2011, 2012)
 
**Číselné atribut: Hodnota předpony**  
Y = '19'... (všechny číselnou hodnotu, která začíná 19) 
 
**Atribut data: Jednu hodnotu**  
D ='2010-02-04.

**Datum atribut: Hodnota rozsahu**  
D &GT;'2010-02-03.  
D = ['2010-02-03', ' 2010-02-05']

**Nebo dotazy:**  
A (Y = 1985, Ti = "disordered elektronické systémy")  
Nebo (Ti = "disordered elektronické systémy", Ti = "zásady odolnost proti chybám a postup")  
And(OR(Y=1985,Y=2008), Ti = "disordered elektronické systémy")
 
**Složené dotazy:**  
Dotaz součástí složeného atributu budete muset uzavřete část výrazu dotazu, který odkazuje na atribut složené ve funkci Composite(). 

Například dotazování pro dokumenty Paper podle jméno autora, použijte následující dotaz:
```
Composite(AA.AuN='mike smith')
```
<br>Dotaz pro dokumenty Paper určitého autora při Autor na konkrétní instituce, použijte následující dotaz:
```
Composite(And(AA.AuN='mike smith',AA.AfN='harvard university'))
```
<br>Funkce Composite() sváže dvě části složené atributu společně. To znamená, že nám pouze získat dokumenty Paper kde jeden autorů je "Jan Smith" při mu byl v Harvard. 

Dotaz pro dokumenty Paper určitého autora v názorům uživatele s (Další) autoři z konkrétní instituce, použijte následující dotaz:
```
And(Composite(AA.AuN='mike smith'),Composite(AA.AfN='harvard university'))
```
<br>V této verzi protože Composite() se používá k vytváření a přidružení jednotlivě před And(), se nám získat všech dokumentů, kde jeden autorů je "Jan Smith" a jeden z autorů afilace je "Harvard". To vyznívá podobně jako v předchozím příkladu dotazu, ale není totéž.

Obecně platí, podívejte se na následující příklad: máme složené atribut C, který má dvě součásti A a B. Entita může mít více hodnot pro C. Toto jsou naše entity:
```
E1: C={A=1, B=1}  C={A=1,B=2}  C={A=2,B=3}
E2: C={A=1, B=3}  C={A=3,B=2}
```

<br>Dotaz 
```
Composite(And(C.A=1, C.B=2))
```

<br>odpovídá pouze entity, které mají hodnotu pro C kde komponentu C.A je 1 a 2 je nainstalována součást C.B. Pouze E1 odpovídá tento dotaz.

Dotaz 
```
And(Composite(C.A=1), Composite(C.B=2))
```
<br>odpovídá entit, které mají hodnotu pro C, kde C.A je 1 a také mít hodnotu pro C kde C.B je 2. E1 a E2 odpovídat tento dotaz.

Poznámka:  
- Součástí složeného atribut mimo funkci Composite() nelze odkazovat.
- Součástí dvou různých atributů složené uvnitř stejné funkce Composite() nelze odkazovat.
- Nemůže odkazovat na atribut, který není součástí složeného atribut uvnitř funkce Composite().
