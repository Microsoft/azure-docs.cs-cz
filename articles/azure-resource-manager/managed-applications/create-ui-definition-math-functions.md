---
title: Vytvoření matematických funkcí definice uživatelského rozhraní
description: Popisuje funkce, které se mají použít při provádění matematických operací.
author: tfitzmac
ms.topic: conceptual
ms.date: 07/13/2020
ms.author: tomfitz
ms.openlocfilehash: 022e59d847a4d89b4243223637fc6fd1e73255a9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "87096944"
---
# <a name="createuidefinition-math-functions"></a>CreateUiDefinition matematické funkce

Funkce umožňují provádět matematické operace.

## <a name="add"></a>add

Přidá dvě čísla a vrátí výsledek.

Následující příklad vrátí `3` :

```json
"[add(1, 2)]"
```

## <a name="ceil"></a>ceil –

Vrátí největší celé číslo větší než nebo rovno zadanému číslu.

Následující příklad vrátí `4` :

```json
"[ceil(3.14)]"
```

## <a name="div"></a>div

Vydělí první číslo druhým číslem a vrátí výsledek. Výsledkem je vždy celé číslo.

Následující příklad vrátí `2` :

```json
"[div(6, 3)]"
```

## <a name="floor"></a>řízení

Vrací největší celé číslo menší nebo rovné zadanému číslu.

Následující příklad vrátí `3` :

```json
"[floor(3.14)]"
```

## <a name="max"></a>max

Vrátí větší z těchto dvou čísel.

Následující příklad vrátí `2` :

```json
"[max(1, 2)]"
```

## <a name="min"></a>min

Vrátí menší ze dvou čísel.

Následující příklad vrátí `1` :

```json
"[min(1, 2)]"
```

## <a name="mod"></a>střední

Vydělí první číslo druhým číslem a vrátí zbytek.

Následující příklad vrátí `0` :

```json
"[mod(6, 3)]"
```

Následující příklad vrátí `2` :

```json
"[mod(6, 4)]"
```

## <a name="mul"></a>mul

Vynásobí dvě čísla a vrátí výsledek.

Následující příklad vrátí `6` :

```json
"[mul(2, 3)]"
```

## <a name="rand"></a>rand

Vrátí náhodné integrální číslo v zadaném rozsahu. Tato funkce negeneruje kryptograficky zabezpečená náhodná čísla.

Následující příklad může vracet `42` :

```json
"[rand(-100, 100)]"
```

## <a name="range"></a>range

Vygeneruje posloupnost integrálních čísel v zadaném rozsahu.

Následující příklad vrátí `[1,2,3]` :

```json
"[range(1, 3)]"
```

## <a name="sub"></a>jednotk

Odečte druhé číslo od prvního čísla a vrátí výsledek.

Následující příklad vrátí `1` :

```json
"[sub(3, 2)]"
```

## <a name="next-steps"></a>Další kroky

* Úvod do Azure Resource Manager najdete v tématu [Azure Resource Manager Overview](../management/overview.md).
