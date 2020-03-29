---
title: Práce s rozhraním API integrovaného dotazu JavaScriptu v uložené procedury a aktivační události Azure Cosmos DB
description: Tento článek představuje koncepty pro javascriptové jazykově integrované rozhraní API dotazu k vytvoření uložených procedur a aktivačních událostí v Azure Cosmos DB.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/01/2019
ms.author: mjbrown
ms.reviewer: sngun
ms.openlocfilehash: 7b7ad470b3330224e80a7160fc1a37bb5ee1cde8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76901840"
---
# <a name="javascript-query-api-in-azure-cosmos-db"></a>Rozhraní API dotazu JavaScriptu v DB Azure Cosmos

Kromě vydávání dotazů pomocí rozhraní SQL API v Azure Cosmos DB poskytuje [sada SDK na straně serveru Cosmos DB](https://azure.github.io/azure-cosmosdb-js-server/) rozhraní JavaScript pro provádění optimalizovaných dotazů v uložené procedury a aktivační události Cosmos DB. Abyste mohli používat toto rozhraní JavaScriptu, nemusíte si být vědomi jazyka SQL. JavaScript dotaz API umožňuje programově vytvářet dotazy předáním predikátu funkce do posloupnosti volání funkce, se syntaxí známé ecmascript5 pole vestavěných a populární javascriptové knihovny jako Lodash. Dotazy jsou analyzovány runtime JavaScript a efektivně spustit pomocí indexů Azure Cosmos DB.

## <a name="supported-javascript-functions"></a>Podporované funkce JavaScriptu

| **Funkce** | **Popis** |
|---------|---------|
|`chain() ... .value([callback] [, options])`|Spustí zřetězené volání, které musí být ukončeno s value().|
|`filter(predicateFunction [, options] [, callback])`|Filtruje vstup pomocí funkce predikátu, která vrací hodnotu true/false, aby bylo možné filtrovat vstupní dokumenty do výsledné sady. Tato funkce se chová podobně jako klauzule WHERE v SQL.|
|`flatten([isShallow] [, options] [, callback])`|Kombinuje a slaňuje pole z každé vstupní položky do jednoho pole. Tato funkce se chová podobně jako SelectMany v LINQ.|
|`map(transformationFunction [, options] [, callback])`|Aplikuje projekci danou transformační funkci, která mapuje každou vstupní položku na objekt nebo hodnotu JavaScriptu. Tato funkce se chová podobně jako klauzule SELECT v SQL.|
|`pluck([propertyName] [, options] [, callback])`|Tato funkce je zkratka pro mapu, která extrahuje hodnotu jedné vlastnosti z každé vstupní položky.|
|`sortBy([predicate] [, options] [, callback])`|Vytvoří novou sadu dokumentů seřazením dokumentů ve vstupním datovém proudu dokumentu ve vzestupném pořadí pomocí daného predikátu. Tato funkce se chová podobně jako klauzule ORDER BY v SQL.|
|`sortByDescending([predicate] [, options] [, callback])`|Vytvoří novou sadu dokumentů seřazením dokumentů ve vstupním datovém proudu dokumentu v sestupném pořadí pomocí daného predikátu. Tato funkce se chová podobně jako klauzule ORDER BY x DESC v SQL.|
|`unwind(collectionSelector, [resultSelector], [options], [callback])`|Provede vlastní spojení s vnitřní pole a přidá výsledky z obou stran jako n-tic do projekce výsledku. Například spojení osobního dokumentu s person.pets by produkovalo [osoba, domácí mazlíček] řazené kolekce členů. To je podobné SelectMany v .NET LINK.|

Pokud jsou zahrnuty do funkcí predikátu a/nebo voliče, následující konstrukce JavaScriptu se automaticky optimalizují tak, aby se spouštěli přímo na indexech Azure Cosmos DB:

- Jednoduché `=` `+` `-` `*` `/` `%` `|` operátory: `^` `&` `==` `!=` `===` `!===` `<` `>` `<=` `>=` `||` `&&` `<<` `>>` `>>>!``~`
- Literály, včetně literálu objektu:{}
- var, návrat

Následující konstrukce Jazyka JavaScript nejsou optimalizovány pro indexy Azure Cosmos DB:

- Řízení toku (například pokud, pro, while)
- Volání funkcí

Další informace naleznete v [dokumentaci javascriptu na straně serveru Cosmos DB .](https://azure.github.io/azure-cosmosdb-js-server/)

## <a name="sql-to-javascript-cheat-sheet"></a>Sql to JavaScript tahák

V následující tabulce jsou uvedeny různé dotazy SQL a odpovídající dotazy jazyka JavaScript. Stejně jako u dotazů SQL, vlastnosti (například item.id) jsou malá a velká písmena.

> [!NOTE]
> `__`(double-underscore) je alias `getContext().getCollection()` při použití javascriptového api dotazu.

|**SQL**|**Rozhraní API dotazu javascriptu**|**Popis**|
|---|---|---|
|VYBERTE *<br>From docs from docs FROM docs FROM doc| __.map(funkce(doc) { <br>&nbsp;&nbsp;&nbsp;&nbsp;návrat doc;<br>});|Výsledky ve všech dokumentech (stránkované tokenem pokračování) tak, jak jsou.|
|SELECT <br>&nbsp;&nbsp;&nbsp;docs.id,<br>&nbsp;&nbsp;&nbsp;docs.message AS msg,<br>&nbsp;&nbsp;&nbsp;docs.akce <br>From docs from docs FROM docs FROM doc|__.map(funkce(doc) {<br>&nbsp;&nbsp;&nbsp;&nbsp;vrátit {<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;id: doc.id,<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;msg: doc.message,<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;akce:doc.actions<br>&nbsp;&nbsp;&nbsp;&nbsp;};<br>});|Promítne id, zprávu (aliased to msg) a akci ze všech dokumentů.|
|VYBERTE *<br>From docs from docs FROM docs FROM doc<br>WHERE<br>&nbsp;&nbsp;&nbsp;docs.id="X998_Y998"|__.filter(function(doc) { __.filter(function(doc) { __.filter(function(doc) { __<br>&nbsp;&nbsp;&nbsp;&nbsp;vrátit doc.id ==="X998_Y998";<br>});|Dotazy na dokumenty s predikátem: id = "X998_Y998".|
|VYBERTE *<br>From docs from docs FROM docs FROM doc<br>WHERE<br>&nbsp;&nbsp;&nbsp;ARRAY_CONTAINS (docs. Štítky, 123)|__.filter(function(x) {<br>&nbsp;&nbsp;&nbsp;&nbsp;return x.Tags && x.Tags.indexOf(123) > -1;<br>});|Dotazy na dokumenty, které mají Vlastnost Tagy a Tagy je pole obsahující hodnotu 123.|
|SELECT<br>&nbsp;&nbsp;&nbsp;docs.id,<br>&nbsp;&nbsp;&nbsp;docs.message AS msg<br>From docs from docs FROM docs FROM doc<br>WHERE<br>&nbsp;&nbsp;&nbsp;docs.id="X998_Y998"|__.chain()<br>&nbsp;&nbsp;&nbsp;&nbsp;.filter(function(doc) {<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;vrátit doc.id ==="X998_Y998";<br>&nbsp;&nbsp;&nbsp;&nbsp;})<br>&nbsp;&nbsp;&nbsp;&nbsp;.map(function(doc) {<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;vrátit {<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;id: doc.id,<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;msg: doc.message<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;};<br>&nbsp;&nbsp;&nbsp;&nbsp;})<br>.value();|Dotazy na dokumenty s predikátem, id = "X998_Y998" a potom promítne id a zprávu (aliased to msg).|
|Značka SELECT VALUE<br>From docs from docs FROM docs FROM doc<br>Připojit značku v dokumentech. Tagy<br>POŘADÍ podle docs._ts|__.chain()<br>&nbsp;&nbsp;&nbsp;&nbsp;.filter(function(doc) {<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;návrat doc. Značky && Array.isArray(doc. Tagy);<br>&nbsp;&nbsp;&nbsp;&nbsp;})<br>&nbsp;&nbsp;&nbsp;&nbsp;.sortBy(function(doc) {<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;zpáteční doc._ts;<br>&nbsp;&nbsp;&nbsp;&nbsp;})<br>&nbsp;&nbsp;&nbsp;&nbsp;.pluck("Tagy")<br>&nbsp;&nbsp;&nbsp;&nbsp;.flatten()<br>&nbsp;&nbsp;&nbsp;&nbsp;.value()|Filtry pro dokumenty, které mají vlastnost pole, Tagy a seřadí výsledné dokumenty podle vlastnosti systému _ts časových razítek a potom projekty + sloučí pole Tagy.|

## <a name="next-steps"></a>Další kroky

Další koncepty a postupy pro zápis a používání uložených procedur, aktivačních událostí a uživatelem definovaných funkcí v Azure Cosmos DB:

- [Jak psát uložené procedury a aktivační události pomocí rozhraní Javascript Query API](how-to-write-javascript-query-api.md)
- [Práce s uloženými procedurami, aktivačními událostmi a uživateli definovanými funkcemi Azure Cosmos DB](stored-procedures-triggers-udfs.md)
- [Jak používat uložené procedury, aktivační události, uživatelem definované funkce v Azure Cosmos DB](how-to-use-stored-procedures-triggers-udfs.md)
- [Odkaz na rozhraní API na straně serveru Azure Cosmos DB JavaScript](https://azure.github.io/azure-cosmosdb-js-server)
- [JavaScript ES6 (ECMA 2015)](https://www.ecma-international.org/ecma-262/6.0/)
