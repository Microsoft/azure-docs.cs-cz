---
title: Práce s JavaScript integrovaný jazyk dotazů rozhraní API ve službě Azure Cosmos DB
description: Tento článek představuje koncepty pro JavaScript integrovaný jazyk dotazů rozhraní API k vytvoření uložených procedur a aktivačních událostí ve službě Azure Cosmos DB.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/08/2018
ms.author: mjbrown
ms.reviewer: sngun
ms.openlocfilehash: 1fff32896ef794a26f223cae4ae491a2995d9acf
ms.sourcegitcommit: 63b996e9dc7cade181e83e13046a5006b275638d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/10/2019
ms.locfileid: "54191135"
---
# <a name="working-with-javascript-language-integrated-query-api-with-azure-cosmos-db"></a>Práce s dotazem integrovaný jazyk JavaScript API ve službě Azure Cosmos DB

Kromě vydávat dotazy pomocí rozhraní SQL API ve službě Azure Cosmos DB, [Cosmos DB SDK na straně serveru](https://azure.github.io/azure-cosmosdb-js-server/) umožňuje provádět optimalizované dotazy pomocí rozhraní JavaScript. Není nutné mít na paměti Toto rozhraní JavaScript jazyka SQL. Dotaz jazyka JavaScript, který rozhraní API můžete programově vytvářet dotazy předáním predikátu funkce do sekvence funkce volání s syntaxi na předdefinované pole a Oblíbené knihovny jazyka JavaScript, jako je Lodash ECMAScript5 společnosti. Dotazy jsou analyzovány pomocí modulu runtime jazyka JavaScript a efektivně proveden pomocí služby Azure Cosmos DB indexy.

## <a name="supported-javascript-functions"></a>Podporované funkce jazyka JavaScript

| **– funkce** | **Popis** |
|---------|---------|
|`chain() ... .value([callback] [, options])`|Začíná value() zřetězené volání, které musí být ukončen.|
|`filter(predicateFunction [, options] [, callback])`|Filtruje vstup pomocí funkce predikátu, který vrací hodnotu true nebo false, chcete-li filtrovat vstupních dokumentů vstup/výstup do výsledné sady. Tato funkce se chová podobně jako v klauzuli WHERE v SQL.|
|`flatten([isShallow] [, options] [, callback])`|Kombinuje a pole z každé vstupní položky se sloučí do jediného pole. Tato funkce se chová podobně jako operátor SelectMany v technologii LINQ.|
|`map(transformationFunction [, options] [, callback])`|Projekce zadané funkce transformace, která se mapuje na objekt jazyka JavaScript nebo hodnotu každé vstupní položky se vztahuje. Tato funkce se chová podobně jako v klauzuli SELECT v SQL.|
|`pluck([propertyName] [, options] [, callback])`|Tato funkce je zkratka pro mapu, která extrahuje hodnotu jedinou vlastnost z každé vstupní položky.|
|`sortBy([predicate] [, options] [, callback])`|Vytvoří novou sadu dokumentů, že je seřadíte dokumentů ve službě stream vstupní dokument ve vzestupném pořadí pomocí daného predikátu. Tato funkce se chová podobně jako klauzuli ORDER BY v SQL.|
|`sortByDescending([predicate] [, options] [, callback])`|Vytvoří novou sadu dokumentů, že je seřadíte dokumentů ve službě stream vstupní dokument v sestupném pořadí pomocí daného predikátu. Tato funkce se chová podobně jako klauzuli ORDER BY x DESC v SQL.|
|`unwind(collectionSelector, [resultSelector], [options], [callback])`|Provádí spojení sama s vnitřní pole a přidá výsledky z obou stran jako řazené kolekce členů výsledek projekce. Pro instanci připojení dokumentu osoba s person.pets vyprodukuje řazené kolekce členů [osoby, pet]. To se podobá operátor SelectMany v .NET odkazu.|

Pokud zahrnutý do predikátu a/nebo výběr funkcí, následující konstrukce jazyka JavaScript automaticky optimalizovat spouštět přímo na indexy Azure Cosmos DB:

- Jednoduché operátory: `=` `+` `-` `*` `/` `%` `|` `^` `&` `==` `!=` `===` `!===` `<` `>` `<=` `>=` `||` `&&` `<<` `>>` `>>>!` `~`
- Literály, včetně literálu objektu: {}
- var, vratky

Následující konstrukce jazyka JavaScript není optimalizovat pro indexy Azure Cosmos DB:

- Řízení toku (třeba pokud, pro, zatímco)
- Volání funkcí

Další informace najdete v tématu [dokumentaci JavaScript na straně serveru databáze Cosmos](https://azure.github.io/azure-cosmosdb-js-server/).

## <a name="sql-to-javascript-cheat-sheet"></a>SQL pro jazyk JavaScript tahák

Následující tabulka uvádí různé dotazy SQL a odpovídající dotazy jazyka JavaScript. Vlastnosti (například item.id) stejně jako u dotazů SQL, jsou malá a velká písmena.

> [!NOTE]
> `__` (dvojité podtržítko) je alias `getContext().getCollection()` při použití rozhraní API jazyka JavaScript dotazu.

|**SQL**|**Dotaz jazyka JavaScript API**|**Popis**|
|---|---|---|
|VYBERTE *<br>Z dokumentace| __.map(Function(DOC) { <br>&nbsp;&nbsp;&nbsp;&nbsp;Vrátí doc;<br>});|Výsledky ve všech dokumentech (stránkované se token pro pokračování), jako je.|
|SELECT <br>&nbsp;&nbsp;&nbsp;docs.ID,<br>&nbsp;&nbsp;&nbsp;docs.Message jako msg,<br>&nbsp;&nbsp;&nbsp;docs.Actions <br>Z dokumentace|__.map(Function(DOC) {<br>&nbsp;&nbsp;&nbsp;&nbsp;vrátit {<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;ID: doc.id,<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;msg –: doc.message,<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Actions:doc.Actions<br>&nbsp;&nbsp;&nbsp;&nbsp;};<br>});|Projekty id, zprávy (s aliasem se přiřadila msg) a akce ze všech dokumentů.|
|VYBERTE *<br>Z dokumentace<br>WHERE<br>&nbsp;&nbsp;&nbsp;docs.ID="X998_Y998"|__.Filter(Function(DOC) {<br>&nbsp;&nbsp;&nbsp;&nbsp;Vrátí doc.id === "X998_Y998";<br>});|Dotazy na dokumenty s predikát: id = "X998_Y998".|
|VYBERTE *<br>Z dokumentace<br>WHERE<br>&nbsp;&nbsp;&nbsp;ARRAY_CONTAINS (dokumentace. Značky, 123)|__.Filter(Function(x) {<br>&nbsp;&nbsp;&nbsp;&nbsp;Vrátí x.Tags & & x.Tags.indexOf(123) > -1;<br>});|Dotazy na dokumenty, které mají značky a vlastnost Tags je pole, který obsahuje hodnotu 123.|
|SELECT<br>&nbsp;&nbsp;&nbsp;docs.ID,<br>&nbsp;&nbsp;&nbsp;docs.Message jako zpráva<br>Z dokumentace<br>WHERE<br>&nbsp;&nbsp;&nbsp;docs.ID="X998_Y998"|__.chain()<br>&nbsp;&nbsp;&nbsp;&nbsp;.Filter(Function(DOC) {<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Vrátí doc.id === "X998_Y998";<br>&nbsp;&nbsp;&nbsp;&nbsp;})<br>&nbsp;&nbsp;&nbsp;&nbsp;.map(Function(DOC) {<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;vrátit {<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;ID: doc.id,<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;msg –: doc.message<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;};<br>&nbsp;&nbsp;&nbsp;&nbsp;})<br>.Value();|Dotazy na dokumenty s predikátem, id = "X998_Y998" a potom projekty id a zprávy (alias pro zprávy).|
|VYBRAT hodnotu značky<br>Z dokumentace<br>Připojte se k značky v docs. Značky<br>Klauzule ORDER BY docs._ts|__.chain()<br>&nbsp;&nbsp;&nbsp;&nbsp;.Filter(Function(DOC) {<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Vrátí dokumentu. Značky & & Array.IsArray – (doc. Značky);<br>&nbsp;&nbsp;&nbsp;&nbsp;})<br>&nbsp;&nbsp;&nbsp;&nbsp;.sortBy(function(doc) {<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Vrátí doc._ts;<br>&nbsp;&nbsp;&nbsp;&nbsp;})<br>&nbsp;&nbsp;&nbsp;&nbsp;.pluck("Tags")<br>&nbsp;&nbsp;&nbsp;&nbsp;.flatten()<br>&nbsp;&nbsp;&nbsp;&nbsp;.Value()|Filtry pro dokumenty, které mají vlastnost typu pole, značky a seřadí výsledný dokumenty podle _ts vlastnosti časového razítka systému a potom projekty + sloučí pole značky.|

## <a name="next-steps"></a>Další postup

Přečtěte si další koncepty a postupy: zápis a pomocí uložené procedury, triggery a uživatelem definovaných funkcí ve službě Azure Cosmos DB:

- [Jak napsat uložených procedur a aktivačních událostí pomocí rozhraní API pro dotazy jazyka Javascript](how-to-write-javascript-query-api.md)
- [Práce s Azure Cosmos DB uložené procedury, triggery a uživatelem definovaných funkcí](stored-procedures-triggers-udfs.md)
- [Jak používat uložené procedury, aktivační události, uživatelem definovaných funkcí ve službě Azure Cosmos DB](how-to-use-stored-procedures-triggers-udfs.md)
- [Azure Cosmos DB na straně serveru rozhraní API reference na JavaScript na](https://azure.github.io/azure-cosmosdb-js-server)
- [ES6 jazyka JavaScript (ECMA 2015)](https://www.ecma-international.org/ecma-262/6.0/)
