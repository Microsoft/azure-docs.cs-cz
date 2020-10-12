---
title: Vytvoření funkcí řetězce definice uživatelského rozhraní
description: Popisuje funkce řetězců, které se mají použít při vytváření definic uživatelského rozhraní pro Azure Managed Applications
author: tfitzmac
ms.topic: conceptual
ms.date: 07/13/2020
ms.author: tomfitz
ms.openlocfilehash: c662948542c36cd93f889ca045ee245c15c7bb11
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "87096938"
---
# <a name="createuidefinition-string-functions"></a>Funkce řetězce CreateUiDefinition

Tyto funkce pro použití s řetězci JSON.

## <a name="concat"></a>concat

Zřetězí jeden nebo více řetězců.

Například pokud výstupní hodnota `element1` if `"Contoso"` , pak tento příklad vrátí řetězec `"Demo Contoso app"` :

```json
"[concat('Demo ', steps('step1').element1, ' app')]"
```

## <a name="endswith"></a>endsWith

Určuje, zda řetězec končí hodnotou.

Následující ukázka vrátí hodnotu true.

```json
"[endsWith('tuvwxyz', 'xyz')]"
```

## <a name="guid"></a>guid

Generuje globálně jedinečný řetězec (GUID).

Následující příklad vrací hodnotu jako `"c7bc8bdc-7252-4a82-ba53-7c468679a511"` :

```json
"[guid()]"
```

## <a name="indexof"></a>indexOf

Vrátí první pozici hodnoty v řetězci nebo hodnotu-1, pokud nebyla nalezena.

Následující ukázka vrátí 2.

```json
"[indexOf('abcdef', 'cd')]"
```

## <a name="lastindexof"></a>lastIndexOf

Vrátí poslední pozici hodnoty v řetězci nebo hodnotu-1, pokud nebyla nalezena.

Následující ukázka vrátí 3.

```json
"[lastIndexOf('test', 't')]"
```

## <a name="replace"></a>náhrady

Vrátí řetězec, ve kterém jsou všechny výskyty zadaného řetězce v aktuálním řetězci nahrazeny jiným řetězcem.

Následující příklad vrátí `"Contoso.com web app"` :

```json
"[replace('Contoso.net web app', '.net', '.com')]"
```

## <a name="startswith"></a>startsWith

Určuje, zda řetězec začíná hodnotou.

Následující ukázka vrátí hodnotu true.

```json
"[startsWith('abcdefg', 'ab')]"
```

## <a name="substring"></a>podřetězec

Vrátí podřetězec určeného řetězce. Podřetězec začíná zadaným indexem a má zadanou délku.

Následující příklad vrátí `"web"` :

```json
"[substring('Contoso.com web app', 12, 3)]"
```

## <a name="tolower"></a>toLower

Vrátí řetězec převedený na malá písmena.

Následující příklad vrátí `"contoso"` :

```json
"[toLower('CONTOSO')]"
```

## <a name="toupper"></a>toUpper

Vrátí řetězec převedený na velká písmena.

Následující příklad vrátí `"CONTOSO"` :

```json
"[toUpper('contoso')]"
```

## <a name="next-steps"></a>Další kroky

* Úvod do Azure Resource Manager najdete v tématu [Azure Resource Manager Overview](../management/overview.md).

