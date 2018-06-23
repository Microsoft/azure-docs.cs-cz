---
title: Strukturovaná výrazy dotazů v rozhraní API služby zkoumání znalostní báze | Microsoft Docs
description: Další informace o použití výrazy strukturovaných dotazů v znalostní báze zkoumání služby (KES) rozhraní API v kognitivní služby.
services: cognitive-services
author: bojunehsu
manager: stesp
ms.service: cognitive-services
ms.component: knowledge-exploration
ms.topic: article
ms.date: 03/26/2016
ms.author: paulhsu
ms.openlocfilehash: 070ee311a1153bc9fb59870dce68f385a43b15f1
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2018
ms.locfileid: "35342474"
---
# <a name="structured-query-expression"></a>Výraz Structured Query
Výraz strukturovaných dotazů určuje sadu operací vyhodnotit proti index datových.  Obsahuje atribut – výrazy dotazů a vyšší úrovně funkce.  Použití [ *vyhodnotit* ](evaluateMethod.md) metodu za účelem výpočtu objekty odpovídající výraz.  Následuje příklad z domény academic publikace, která vrací publikace autorem Jaime Teevan od roku 2013.

`And(Composite(Author.Name=='jaime teevan'),Y>=2013)`

Výrazy strukturovaných dotazů je možné získat od [ *interpretovat* ](interpretMethod.md) požadavků, kde je sémantický výstup každé interpretace strukturovaných dotazů výraz, který vrátí index objektů, které odpovídají vstupní přirozeného jazyka dotazů.  Případně se může být ručně vytvořené pomocí syntaxe popsaných v této části.

## <a name="attribute-query-expression"></a>Atribut výrazu dotazu
Výraz dotazu atribut identifikuje sadu objektů na základě shody s určitým atributem.  Různé odpovídající operace jsou podporovány v závislosti na typu atributu a indexované operaci zadané v [schématu](SchemaFormat.md):

| Typ | Operace | Příklady |
|------|-------------|------------|
| Řetězec | rovná se | Title = "latentní sémantického analýzy" (kanonický + synonyma) |
| Řetězec | rovná se | Author.Name=='susan t dumais (kanonický pouze)|
| Řetězec | starts_with | Název = 'latentní s'... |
| Int32, Int64 nebo Double | rovná se | Rok = 2000 |
| Int32, Int64 nebo Double | starts_with | Rok = "20"... (všechny Desítková hodnota počínaje "20") |
| Int32, Int64 nebo Double | is_between | Rok&lt;2000 <br/> Rok&lt;= 2000 <br/> Rok&gt;2000 <br/> Rok&gt;= 2000 <br/> Year=[2010,2012) *(obsahuje hodnotu pouze levé hranic: 2010, 2011)* <br/> Rok = [2000,2012] *(zahrnuje obě hodnoty hranic: 2010, 2011, 2012)* |
| Datum | rovná se | Datum narození ='1984-05-14. |
| Datum | is_between | Datum narození&lt;= "2008/03/14' <br/> PublishDate = ['2000-01-01', ' 2009-12-31'] |
| Guid | rovná se | ID = "602DD052-CC47-4B23-A16A-26B52D30C05B. |


Například výraz "Title ="latentní s"..." odpovídá všechny academic publikace, jejichž název začíná řetězcem "latentní s".  Aby bylo možné vyhodnotit tento výraz, atribut název zadejte "starts_with" operaci ve schématu použit k vytvoření indexu.

Pro atributy s přidružené synonyma může výrazu dotazu určete objekty, jejichž kanonický hodnota odpovídá dané řetězce pomocí "==" operátor nebo objekty, kde některé z jeho kanonickém nebo synonymum hodnoty shodují pomocí operátoru "=".  Musí být zadaný v definici atribut operátorovi "je rovno".


## <a name="functions"></a>Functions
Není integrovanou sadu funkcí, které umožňuje vytváření sofistikovanější výrazy dotazu z základní atribut dotazů.

### <a name="and-function"></a>A funkce
`And(expr1, expr2)`

Vrací průnik dvou výrazů vstupní dotaz.

Následující příklad vrací academic publikace, které jsou publikovány v roce 2000 o načtení informací:

`And(Year=2000, Keyword=='information retrieval')`

### <a name="or-function"></a>Nebo – funkce
`Or(expr1, expr2)`

Vrátí sjednocení dvou výrazů vstupní dotaz.

Následující příklad vrací academic publikace, které jsou publikovány v roce 2000 o načtení informací nebo modelování uživatele:

`And(Year=2000, Or(Keyword='information retrieval', Keyword='user modeling'))`

### <a name="composite-function"></a>Složené – funkce
`Composite(expr)`

Vrací výraz, který zapouzdřuje vnitřní výrazu tvořený dotazy pro dílčí atributy společný atribut složené.  Zapouzdření vyžaduje atribut složené žádné odpovídající datový objekt do mají alespoň jednu hodnotu, která splňuje jednotlivě vnitřní výraz.  Všimněte si, že výrazu dotazu na dílčí atributy složené atributu musí být zapouzdřené pomocí funkce Composite() před kombinaci s jinými výrazy dotazu.

Například následující výraz vrací academic publikace podle "harry shum" při mu byl v "microsoft":

```
Composite(And(Author.Name="harry shum", 
              Author.Affiliation="microsoft"))
```

Následující výraz na druhé straně vrátí academic publikace, kde jeden autorů je "harry shum" a jeden názorům uživatele je "společnost microsoft":

```
And(Composite(Author.Name="harry shum"), 
    Composite(Author.Affiliation="microsoft"))
```
