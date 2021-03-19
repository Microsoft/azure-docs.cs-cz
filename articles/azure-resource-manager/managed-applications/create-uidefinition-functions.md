---
title: Vytvoření funkcí definice uživatelského rozhraní
description: Popisuje funkce, které se mají použít při vytváření definic uživatelského rozhraní pro Azure Managed Applications
author: tfitzmac
ms.topic: conceptual
ms.date: 07/13/2020
ms.author: tomfitz
ms.openlocfilehash: e4255f0d42e28a72ad55d9b7f81d0dc49b2950cb
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "87041002"
---
# <a name="createuidefinition-functions"></a>Funkce CreateUiDefinition

Tento článek poskytuje přehled podporovaných funkcí pro CreateUiDefinition.

## <a name="function-syntax"></a>Syntaxe funkce

Chcete-li použít funkci, uzavřete vyvolání do hranatých závorek. Například:

```json
"[function()]"
```

Na řetězce a další funkce lze odkazovat jako na parametry funkce, ale řetězce musí být obklopeny v jednoduchých uvozovkách. Například:

```json
"[fn1(fn2(), 'demo text')]"
```

V případě potřeby můžete odkazovat na vlastnosti výstupu funkce pomocí operátoru tečka. Například:

```json
"[func().prop1]"
```

## <a name="collection-functions"></a>Funkce kolekcí

Tyto funkce lze použít s kolekcemi, jako jsou řetězce JSON, pole a objekty.

* [zobrazí](create-ui-definition-collection-functions.md#contains)
* [empty](create-ui-definition-collection-functions.md#empty)
* [filtrovací](create-ui-definition-collection-functions.md#filter)
* [první](create-ui-definition-collection-functions.md#first)
* [posledního](create-ui-definition-collection-functions.md#last)
* [length](create-ui-definition-collection-functions.md#length)
* [mapy](create-ui-definition-collection-functions.md#map)
* [přímo](create-ui-definition-collection-functions.md#skip)
* [rozdělení](create-ui-definition-collection-functions.md#split)
* [nezbytná](create-ui-definition-collection-functions.md#take)

## <a name="conversion-functions"></a>Převodní funkce

Tyto funkce lze použít pro převod hodnot mezi datovými typy JSON a kódování.

* [bool](create-ui-definition-conversion-functions.md#bool)
* [decodeBase64](create-ui-definition-conversion-functions.md#decodebase64)
* [decodeURIComponent –](create-ui-definition-conversion-functions.md#decodeuricomponent)
* [encodeBase64](create-ui-definition-conversion-functions.md#encodebase64)
* [encodeURIComponent –](create-ui-definition-conversion-functions.md#encodeuricomponent)
* [float](create-ui-definition-conversion-functions.md#float)
* [int](create-ui-definition-conversion-functions.md#int)
* [parse](create-ui-definition-conversion-functions.md#parse)
* [řetězec](create-ui-definition-conversion-functions.md#string)

## <a name="date-functions"></a>Datové funkce

* [addHours](create-ui-definition-date-functions.md#addhours)
* [addMinutes](create-ui-definition-date-functions.md#addminutes)
* [addSeconds](create-ui-definition-date-functions.md#addseconds)
* [utcNow](create-ui-definition-date-functions.md#utcnow)

## <a name="logical-functions"></a>Logické funkce

Tyto funkce lze použít v podmíněných podmínkách. Některé funkce nemusí podporovat všechny datové typy JSON.

* [and](create-ui-definition-logical-functions.md#and)
* [COALESCE](create-ui-definition-logical-functions.md#coalesce)
* [rovná](create-ui-definition-logical-functions.md#equals)
* [greater](create-ui-definition-logical-functions.md#greater)
* [greaterOrEquals](create-ui-definition-logical-functions.md#greaterorequals)
* [if](create-ui-definition-logical-functions.md#if)
* [tolik](create-ui-definition-logical-functions.md#less)
* [lessOrEquals](create-ui-definition-logical-functions.md#lessorequals)
* [mění](create-ui-definition-logical-functions.md#not)
* [nebo](create-ui-definition-logical-functions.md#or)

## <a name="math-functions"></a>Matematické funkce

* [add](create-ui-definition-math-functions.md#add)
* [ceil –](create-ui-definition-math-functions.md#ceil)
* [div](create-ui-definition-math-functions.md#div)
* [řízení](create-ui-definition-math-functions.md#floor)
* [počet](create-ui-definition-math-functions.md#max)
* [min](create-ui-definition-math-functions.md#min)
* [střední](create-ui-definition-math-functions.md#mod)
* [mul](create-ui-definition-math-functions.md#mul)
* [funkcí](create-ui-definition-math-functions.md#rand)
* [oblasti](create-ui-definition-math-functions.md#range)
* [jednotk](create-ui-definition-math-functions.md#sub)

## <a name="referencing-functions"></a>Odkazovací funkce

* [práce](create-ui-definition-referencing-functions.md#basics)
* [oblasti](create-ui-definition-referencing-functions.md#location)
* [resourceGroup](create-ui-definition-referencing-functions.md#resourcegroup)
* [uvedené](create-ui-definition-referencing-functions.md#steps)
* [formě](create-ui-definition-referencing-functions.md#subscription)

## <a name="string-functions"></a>Funkce řetězců

* [spojuje](create-ui-definition-string-functions.md#concat)
* [endsWith](create-ui-definition-string-functions.md#endswith)
* [guid](create-ui-definition-string-functions.md#guid)
* [indexOf](create-ui-definition-string-functions.md#indexof)
* [lastIndexOf](create-ui-definition-string-functions.md#lastindexof)
* [náhrady](create-ui-definition-string-functions.md#replace)
* [startsWith](create-ui-definition-string-functions.md#startswith)
* [podřetězec](create-ui-definition-string-functions.md#substring)
* [toLower](create-ui-definition-string-functions.md#tolower)
* [toUpper](create-ui-definition-string-functions.md#toupper)

## <a name="next-steps"></a>Další kroky

* Úvod do Azure Resource Manager najdete v tématu [Azure Resource Manager Overview](../management/overview.md).
