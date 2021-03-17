---
title: Práce s integrovaným rozhraním API dotazů v jazyce JavaScript v Azure Cosmos DB uložených procedurách a triggerech
description: V tomto článku se seznámíte s koncepty rozhraní API pro integrované dotazy jazyka JavaScript k vytváření uložených procedur a triggerů v Azure Cosmos DB.
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 05/07/2020
ms.author: tisande
ms.reviewer: sngun
ms.custom: devx-track-js
ms.openlocfilehash: b2563a9af0e0ca6943059698e29d139143780d93
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/04/2020
ms.locfileid: "93340970"
---
# <a name="javascript-query-api-in-azure-cosmos-db"></a>Rozhraní API pro dotazování v jazyce JavaScript v Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Kromě vydávání dotazů pomocí rozhraní SQL API v Azure Cosmos DB poskytuje [sada SDK na straně serveru Cosmos DB](https://github.com/Azure/azure-cosmosdb-js-server/) rozhraní JavaScript pro provádění optimalizovaných dotazů v Cosmos DB uložených procedurách a triggerech. Pro použití tohoto rozhraní JavaScript nemusíte znát jazyk SQL. Rozhraní API pro dotazování v jazyce JavaScript umožňuje programově vytvářet dotazy tím, že předává funkce predikátů do sekvence volání funkce, se syntaxí ECMAScript5's předdefinované a oblíbené knihovny JavaScriptu, jako je Lodash. Dotazy jsou analyzovány modulem runtime jazyka JavaScript a efektivně spouštěny pomocí Azure Cosmos DB indexy.

## <a name="supported-javascript-functions"></a>Podporované funkce JavaScriptu

| **Funkce** | **Popis** |
|---------|---------|
|`chain() ... .value([callback] [, options])`|Spustí zřetězené volání, které musí být ukončeno hodnotou ().|
|`filter(predicateFunction [, options] [, callback])`|Filtruje vstup pomocí funkce predikátu, která vrací hodnotu true nebo false, aby bylo možné filtrovat vstupní a výstupní dokumenty do výsledné sady. Tato funkce se chová podobně jako klauzule WHERE v SQL.|
|`flatten([isShallow] [, options] [, callback])`|Kombinuje a sloučí pole z každé vstupní položky do jednoho pole. Tato funkce se chová podobně jako operátor SelectMany v LINQ.|
|`map(transformationFunction [, options] [, callback])`|Aplikuje projekci na danou funkci transformace, která mapuje každou vstupní položku na objekt nebo hodnotu JavaScriptu. Tato funkce se chová podobně jako klauzule SELECT v SQL.|
|`pluck([propertyName] [, options] [, callback])`|Tato funkce je zástupce pro mapu, která extrahuje hodnotu jedné vlastnosti z každé vstupní položky.|
|`sortBy([predicate] [, options] [, callback])`|Vytvoří novou sadu dokumentů seřazením dokumentů ve vstupním streamu dokumentů ve vzestupném pořadí pomocí daného predikátu. Tato funkce se chová podobně jako klauzule ORDER BY v SQL.|
|`sortByDescending([predicate] [, options] [, callback])`|Vytvoří novou sadu dokumentů seřazením dokumentů v datovém proudu vstupního dokumentu v sestupném pořadí pomocí daného predikátu. Tato funkce se chová podobně jako klauzule ORDER BY x DESC v jazyce SQL.|
|`unwind(collectionSelector, [resultSelector], [options], [callback])`|Provede samoobslužné spojení s vnitřním polem a přidá výsledky z obou stran jako řazené kolekce členů do výsledné projekce. Například připojení k dokumentu osoby pomocí Person. Pet vytvoří řazené kolekce členů [person, PET]. To se podobá operátor SelectMany v odkazu .NET.|

Při zahrnutí do predikátu a/nebo funkce selektoru jsou následující konstrukce JavaScriptu automaticky optimalizované pro spuštění přímo na Azure Cosmos DB indexy:

- Jednoduché operátory `=` `+` `-` `*` : `/` `%` `|` `^` `&` `==` `!=` `===` `!===` `<` `>` `<=` `>=` `||` `&&` `<<` `>>` `>>>!``~`
- Literály, včetně literálu objektu: {}
- var, Return

Následující konstrukce jazyka JavaScript nejsou optimalizované pro Azure Cosmos DB indexy:

- Tok řízení (například if, for, while)
- Volání funkcí

Další informace najdete v dokumentaci k [JavaScriptu na straně serveru Cosmos DB](https://github.com/Azure/azure-cosmosdb-js-server/).

## <a name="sql-to-javascript-cheat-sheet"></a>Tahák list SQL pro JavaScript

Následující tabulka uvádí různé dotazy SQL a odpovídající dotazy JavaScriptu. Stejně jako u dotazů SQL jsou u vlastností (například item.id) rozlišována malá a velká písmena.

> [!NOTE]
> `__` (dvojité podtržítko) je alias pro `getContext().getCollection()` při použití rozhraní API pro dotazování jazyka JavaScript.

|**SQL**|**Rozhraní API pro dotazy jazyka JavaScript**|**Popis**|
|---|---|---|
|VYBRALI<br>Z dokumentů| __. map (funkce (doc) { <br>&nbsp;&nbsp;&nbsp;&nbsp;vrátit doc;<br>});|Výsledkem jsou všechny dokumenty (stránkované s tokenem pro pokračování) tak, jak jsou.|
|SELECT <br>&nbsp;&nbsp;&nbsp;docs.id,<br>&nbsp;&nbsp;&nbsp;docs. Message jako MSG,<br>&nbsp;&nbsp;&nbsp;dokumenty. Actions <br>Z dokumentů|__. map (funkce (doc) {<br>&nbsp;&nbsp;&nbsp;&nbsp;vrátit<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;ID: doc.id,<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Msg: doc. Message,<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;akce: doc. Actions<br>&nbsp;&nbsp;&nbsp;&nbsp;};<br>});|Projekty ID, zprávy (aliasy) a akce ze všech dokumentů.|
|VYBRALI<br>Z dokumentů<br>WHERE<br>&nbsp;&nbsp;&nbsp;docs. ID = "X998_Y998"|__. Filter (Function (doc) {<br>&nbsp;&nbsp;&nbsp;&nbsp;Return doc.id = = = "X998_Y998";<br>});|Dotazy na dokumenty s predikátem: ID = "X998_Y998".|
|VYBRALI<br>Z dokumentů<br>WHERE<br>&nbsp;&nbsp;&nbsp;ARRAY_CONTAINS (dokumentace Značky, 123)|__. Filter (Function (x) {<br>&nbsp;&nbsp;&nbsp;&nbsp;Vrátí x. Tags && x. Tags. indexOf (123) >-1;<br>});|Dotazy na dokumenty, které mají vlastnost a značky značek, jsou pole obsahující hodnotu 123.|
|SELECT<br>&nbsp;&nbsp;&nbsp;docs.id,<br>&nbsp;&nbsp;&nbsp;docs. Message jako MSG<br>Z dokumentů<br>WHERE<br>&nbsp;&nbsp;&nbsp;docs. ID = "X998_Y998"|__. Chain ()<br>&nbsp;&nbsp;&nbsp;&nbsp;. Filter (funkce (doc) {<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Return doc.id = = = "X998_Y998";<br>&nbsp;&nbsp;&nbsp;&nbsp;})<br>&nbsp;&nbsp;&nbsp;&nbsp;. map (funkce (doc) {<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;vrátit<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;ID: doc.id,<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Msg: doc. Message<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;};<br>&nbsp;&nbsp;&nbsp;&nbsp;})<br>. Value ();|Dotaz na dokumenty s predikátem, ID = "X998_Y998" a následně projektuje ID a zprávu (aliasy do zprávy).|
|VYBRAT značku hodnoty<br>Z dokumentů<br>Značka JOIN v docs Značky<br>Seřadit podle docs._ts|__. Chain ()<br>&nbsp;&nbsp;&nbsp;&nbsp;. Filter (funkce (doc) {<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;vrátit dokument. Značky && Array. IsArray (doc. Značky);<br>&nbsp;&nbsp;&nbsp;&nbsp;})<br>&nbsp;&nbsp;&nbsp;&nbsp;. sortBy (funkce (doc) {<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;vrátit doc._ts;<br>&nbsp;&nbsp;&nbsp;&nbsp;})<br>&nbsp;&nbsp;&nbsp;&nbsp;. Pluck ("značky")<br>&nbsp;&nbsp;&nbsp;&nbsp;. plochý ()<br>&nbsp;&nbsp;&nbsp;&nbsp;. Value ()|Filtry pro dokumenty, které mají vlastnost pole, značky a seřadí výsledné dokumenty pomocí vlastnosti System _ts timestamp a pak projekty + sloučí pole značek.|

## <a name="next-steps"></a>Další kroky

Přečtěte si další koncepty a postupy psaní a používání uložených procedur, triggerů a uživatelsky definovaných funkcí v Azure Cosmos DB:

- [Jak zapisovat uložené procedury a triggery pomocí rozhraní API pro dotazy jazyka JavaScript](how-to-write-javascript-query-api.md)
- [Práce s Azure Cosmos DB uloženými procedurami, triggery a uživatelsky definovanými funkcemi](stored-procedures-triggers-udfs.md)
- [Jak používat uložené procedury, triggery, uživatelsky definované funkce v Azure Cosmos DB](how-to-use-stored-procedures-triggers-udfs.md)
- [Reference k rozhraní API na straně serveru Azure Cosmos DB JavaScript](https://azure.github.io/azure-cosmosdb-js-server)
- [JavaScript ES6 (ECMA 2015)](https://www.ecma-international.org/ecma-262/6.0/)
