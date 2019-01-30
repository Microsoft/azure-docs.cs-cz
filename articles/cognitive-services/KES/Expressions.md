---
title: Výrazy strukturovaných dotazů – Knowledge Exploration Service API
titlesuffix: Azure Cognitive Services
description: Zjistěte, jak používat strukturované výrazy dotazu v znalostní báze zkoumání služby (KES) rozhraní API.
services: cognitive-services
author: bojunehsu
manager: cgronlun
ms.service: cognitive-services
ms.subservice: knowledge-exploration
ms.topic: conceptual
ms.date: 03/26/2016
ms.author: paulhsu
ms.openlocfilehash: 335bcc025d2f3e972a02234da89e35c90c91afeb
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2019
ms.locfileid: "55222690"
---
# <a name="structured-query-expression"></a>Výraz strukturovaných dotazů

Výraz strukturovaných dotazů určuje sadu operací s data indexu.  Zahrnuje atribut – výrazy dotazů a funkce vyšší úrovně.  Použití [ *vyhodnotit* ](evaluateMethod.md) metodu za účelem výpočtu objektů, které odpovídají výrazu.  Následuje příklad z akademického publikace domény, který vrací publikace autorem Jaime Teevan od roku 2013.

`And(Composite(Author.Name=='jaime teevan'),Y>=2013)`

Výrazy strukturovaných dotazů mohou pocházet od [ *interpretovat* ](interpretMethod.md) žádosti, kde sémantické výstup každé vyhodnocení je výraz strukturovaných dotazů, který vrací index objektů, které odpovídají přirozený jazyk dotazu.  Případně se může být ručně vytvořené pomocí syntaxe popsané v této části.

## <a name="attribute-query-expression"></a>Atribut výrazu dotazu

Výraz dotazu atribut identifikuje sadu objektů na základě shody s konkrétním atributem.  Podporovány jsou různé odpovídající operace v závislosti na typu atributu a indexované operaci zadané v [schématu](SchemaFormat.md):

| Type | Operace | Příklady |
|------|-------------|------------|
| Řetězec | rovná se | Název = "skryté sémantické analýzy" (canonical + synonym) |
| Řetězec | rovná se | Author.Name=='susan t dumais (canonical jenom)|
| Řetězec | starts_with | Název = 'skryté s'... |
| Datový typ Int32 nebo Int64/Double | rovná se | Rok = 2000 |
| Datový typ Int32 nebo Int64/Double | starts_with | Rok = "20"... (žádné Desítková hodnota začíná "20") |
| Datový typ Int32 nebo Int64/Double | is_between | Rok&lt;2000 <br/> Rok&lt;= 2000 <br/> Rok&gt;2000 <br/> Rok&gt;= 2000 <br/> Year=[2010,2012) *(obsahuje hodnotu pouze levé hranice: 2010, 2011)* <br/> Rok = [2000,2012] *(zahrnuje obě hodnoty hranic: 2010, 2011, 2012)* |
| Datum | rovná se | BirthDate='1984-05-14' |
| Datum | is_between | Datum narození&lt;= "2008/03/14' <br/> PublishDate = ["2000-01-01", "2009-12-31'] |
| Guid | rovná se | Id='602DD052-CC47-4B23-A16A-26B52D30C05B' |


Například výraz "Title = 'latentní s'..." odpovídá všechny academic publikace, jejichž název začíná řetězcem "latentní s".  Aby bylo možné vyhodnotit tento výraz, musíte zadat atribut název operace "starts_with" ve schématu použit k vytvoření indexu.

Pro atributy se přidružené synonyma výrazu dotazu může určit objekty, jejichž Kanonická hodnota odpovídá dané řetězec za použití "==" operátor nebo objekty, kde některé z jeho canonical synonymum/hodnoty shodují pomocí operátoru "=".  Oba vyžadují operátor "je rovno" pro zadaný v definici atributu.


## <a name="functions"></a>Functions

Je integrovaná sada funkce umožňující vytváření sofistikovanějších – výrazy dotazů z dotazů základní atribut.

### <a name="and-function"></a>A funkce

`And(expr1, expr2)`

Vrací průnik dvou výrazů vstupní dotaz.

Následující příklad vrátí academic publikace publikované v roce 2000 o načítání informací:

`And(Year=2000, Keyword=='information retrieval')`

### <a name="or-function"></a>Nebo – funkce

`Or(expr1, expr2)`

Vrátí sjednocení dvou výrazů vstupní dotaz.

Následující příklad vrátí academic publikace publikované v roce 2000 o načítání informací nebo uživatel modelování:

`And(Year=2000, Or(Keyword='information retrieval', Keyword='user modeling'))`

### <a name="composite-function"></a>Složený – funkce

`Composite(expr)`

Vrátí výraz, který zapouzdřuje vnitřní výraz se skládá z dotazy na dílčí atributy složené společný atribut.  Zapouzdření vyžaduje atribut složené žádné odpovídající datového objektu má alespoň jednu hodnotu, která splňuje jednotlivě vnitřní výraz.  Všimněte si, že výrazu dotazu na dílčí atributy složené atributu musí být zapouzdřeny pomocí funkce Composite() předtím, než se dá se kombinovat s jinými výrazy dotazu.

Například následující výraz vrátí academic publikace podle "harry shum" zatímco pracoval bruno v "microsoft":

```
Composite(And(Author.Name="harry shum", 
              Author.Affiliation="microsoft"))
```

Na druhé straně následující výraz vrací academic publikací, kde jedním z autorů je "harry shum" a jeden umístění je "microsoft":

```
And(Composite(Author.Name="harry shum"), 
    Composite(Author.Affiliation="microsoft"))
```
