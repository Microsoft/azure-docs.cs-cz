---
title: Práce s integrovaným rozhraním API dotazů v jazyce JavaScript v Azure Cosmos DB uložených procedurách a triggerech
description: V tomto článku se seznámíte s koncepty rozhraní API pro integrované dotazy jazyka JavaScript k vytváření uložených procedur a triggerů v Azure Cosmos DB.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/01/2019
ms.author: mjbrown
ms.reviewer: sngun
ms.openlocfilehash: 7b7ad470b3330224e80a7160fc1a37bb5ee1cde8
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/31/2020
ms.locfileid: "76901840"
---
# <a name="javascript-query-api-in-azure-cosmos-db"></a>Rozhraní API pro dotazování v jazyce JavaScript v Azure Cosmos DB

Kromě vydávání dotazů pomocí rozhraní SQL API v Azure Cosmos DB poskytuje [sada SDK na straně serveru Cosmos DB](https://azure.github.io/azure-cosmosdb-js-server/) rozhraní JavaScript pro provádění optimalizovaných dotazů v Cosmos DB uložených procedurách a triggerech. Pro použití tohoto rozhraní JavaScript nemusíte znát jazyk SQL. Rozhraní API pro dotazování v jazyce JavaScript umožňuje programově vytvářet dotazy tím, že předává funkce predikátů do sekvence volání funkce, se syntaxí ECMAScript5's předdefinované a oblíbené knihovny JavaScriptu, jako je Lodash. Dotazy jsou analyzovány modulem runtime jazyka JavaScript a efektivně spouštěny pomocí Azure Cosmos DB indexy.

## <a name="supported-javascript-functions"></a>Podporované funkce JavaScriptu

| **Slouží** | **Popis** |
|---------|---------|
|`chain() ... .value([callback] [, options])`|Začíná value() zřetězené volání, které musí být ukončen.|
|`filter(predicateFunction [, options] [, callback])`|Filtruje vstup pomocí funkce predikátu, který vrací hodnotu true nebo false, chcete-li filtrovat vstupních dokumentů vstup/výstup do výsledné sady. Tato funkce se chová podobně jako v klauzuli WHERE v SQL.|
|`flatten([isShallow] [, options] [, callback])`|Kombinuje a sloučí pole z každé vstupní položky do jednoho pole. Tato funkce se chová podobně jako operátor SelectMany v technologii LINQ.|
|`map(transformationFunction [, options] [, callback])`|Projekce zadané funkce transformace, která se mapuje na objekt jazyka JavaScript nebo hodnotu každé vstupní položky se vztahuje. Tato funkce se chová podobně jako v klauzuli SELECT v SQL.|
|`pluck([propertyName] [, options] [, callback])`|Tato funkce je zkratka pro mapu, která extrahuje hodnotu jedinou vlastnost z každé vstupní položky.|
|`sortBy([predicate] [, options] [, callback])`|Vytvoří novou sadu dokumentů seřazením dokumentů ve vstupním streamu dokumentů ve vzestupném pořadí pomocí daného predikátu. Tato funkce se chová podobně jako klauzuli ORDER BY v SQL.|
|`sortByDescending([predicate] [, options] [, callback])`|Vytvoří novou sadu dokumentů seřazením dokumentů v datovém proudu vstupního dokumentu v sestupném pořadí pomocí daného predikátu. Tato funkce se chová podobně jako klauzuli ORDER BY x DESC v SQL.|
|`unwind(collectionSelector, [resultSelector], [options], [callback])`|Provede samoobslužné spojení s vnitřním polem a přidá výsledky z obou stran jako řazené kolekce členů do výsledné projekce. Například připojení k dokumentu osoby pomocí Person. Pet vytvoří řazené kolekce členů [person, PET]. To se podobá operátor SelectMany v odkazu .NET.|

Pokud zahrnutý do predikátu a/nebo výběr funkcí, následující konstrukce jazyka JavaScript automaticky optimalizovat spouštět přímo na indexy Azure Cosmos DB:

- Jednoduché operátory: `=` `+` `-` `*` `/` `%` `|` `^` `&` `==` `!=` `===` `!===` `<` `>` `<=` `>=` `||` `&&` `<<` `>>` `>>>!` `~`
- Literály, včetně literálu objektu: {}
- var, vratky

Následující konstrukce jazyka JavaScript není optimalizovat pro indexy Azure Cosmos DB:

- Řízení toku (třeba pokud, pro, zatímco)
- Volání funkcí

Další informace najdete v dokumentaci k [JavaScriptu na straně serveru Cosmos DB](https://azure.github.io/azure-cosmosdb-js-server/).

## <a name="sql-to-javascript-cheat-sheet"></a>Tahák list SQL pro JavaScript

Následující tabulka uvádí různé dotazy SQL a odpovídající dotazy jazyka JavaScript. Stejně jako u dotazů SQL jsou u vlastností (například item.id) rozlišována malá a velká písmena.

> [!NOTE]
> `__` (dvojité podtržítko) je alias pro `getContext().getCollection()` při použití rozhraní API pro dotazy jazyka JavaScript.

|**SQL**|**Rozhraní API pro dotazy jazyka JavaScript**|**Popis**|
|---|---|---|
|VYBERTE *<br>Z dokumentace| __.map(Function(DOC) { <br>&nbsp;&nbsp;&nbsp;&nbsp;Vrátí doc;<br>});|Výsledky ve všech dokumentech (stránkované se token pro pokračování), jako je.|
|SELECT <br>&nbsp;&nbsp;&nbsp;docs.id,<br>&nbsp;&nbsp;&nbsp;docs. Message jako MSG,<br>&nbsp;&nbsp;&nbsp;docs. Actions <br>Z dokumentace|__.map(Function(DOC) {<br>&nbsp;&nbsp;&nbsp;&nbsp;vrátit {<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;ID: doc.id,<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;msg –: doc.message,<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Actions:doc.Actions<br>&nbsp;&nbsp;&nbsp;&nbsp;};<br>});|Projekty id, zprávy (s aliasem se přiřadila msg) a akce ze všech dokumentů.|
|VYBERTE *<br>Z dokumentace<br>WHERE<br>&nbsp;&nbsp;&nbsp;docs.id="X998_Y998"|__.Filter(Function(DOC) {<br>&nbsp;&nbsp;&nbsp;&nbsp;Vrátí doc.id === "X998_Y998";<br>});|Dotazy na dokumenty s predikát: id = "X998_Y998".|
|VYBERTE *<br>Z dokumentace<br>WHERE<br>&nbsp;&nbsp;&nbsp;ARRAY_CONTAINS (docs. Značky, 123)|__.Filter(Function(x) {<br>&nbsp;&nbsp;&nbsp;&nbsp;Vrátí x.Tags & & x.Tags.indexOf(123) > -1;<br>});|Dotazy na dokumenty, které mají značky a vlastnost Tags je pole, který obsahuje hodnotu 123.|
|SELECT<br>&nbsp;&nbsp;&nbsp;docs.id,<br>&nbsp;&nbsp;&nbsp;docs. Message jako MSG<br>Z dokumentace<br>WHERE<br>&nbsp;&nbsp;&nbsp;docs.id="X998_Y998"|__.chain()<br>&nbsp;&nbsp;&nbsp;&nbsp;.Filter(Function(DOC) {<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Vrátí doc.id === "X998_Y998";<br>&nbsp;&nbsp;&nbsp;&nbsp;})<br>&nbsp;&nbsp;&nbsp;&nbsp;.map(Function(DOC) {<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;vrátit {<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;ID: doc.id,<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;msg –: doc.message<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;};<br>&nbsp;&nbsp;&nbsp;&nbsp;})<br>.Value();|Dotazy na dokumenty s predikátem, id = "X998_Y998" a potom projekty id a zprávy (alias pro zprávy).|
|VYBRAT hodnotu značky<br>Z dokumentace<br>Připojte se k značky v docs. Značky<br>Klauzule ORDER BY docs._ts|__.chain()<br>&nbsp;&nbsp;&nbsp;&nbsp;.Filter(Function(DOC) {<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Vrátí dokumentu. Značky & & Array.IsArray – (doc. Značky);<br>&nbsp;&nbsp;&nbsp;&nbsp;})<br>&nbsp;&nbsp;&nbsp;&nbsp;.sortBy(function(doc) {<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Vrátí doc._ts;<br>&nbsp;&nbsp;&nbsp;&nbsp;})<br>&nbsp;&nbsp;&nbsp;&nbsp;.pluck("Tags")<br>&nbsp;&nbsp;&nbsp;&nbsp;.flatten()<br>&nbsp;&nbsp;&nbsp;&nbsp;.Value()|Filtry pro dokumenty, které mají vlastnost typu pole, značky a seřadí výsledný dokumenty podle _ts vlastnosti časového razítka systému a potom projekty + sloučí pole značky.|

## <a name="next-steps"></a>Další kroky

Přečtěte si další koncepty a postupy psaní a používání uložených procedur, triggerů a uživatelsky definovaných funkcí v Azure Cosmos DB:

- [Jak zapisovat uložené procedury a triggery pomocí rozhraní API pro dotazy jazyka JavaScript](how-to-write-javascript-query-api.md)
- [Práce s Azure Cosmos DB uloženými procedurami, triggery a uživatelsky definovanými funkcemi](stored-procedures-triggers-udfs.md)
- [Jak používat uložené procedury, triggery, uživatelsky definované funkce v Azure Cosmos DB](how-to-use-stored-procedures-triggers-udfs.md)
- [Azure Cosmos DB na straně serveru rozhraní API reference na JavaScript na](https://azure.github.io/azure-cosmosdb-js-server)
- [JavaScript ES6 (ECMA 2015)](https://www.ecma-international.org/ecma-262/6.0/)
