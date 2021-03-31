---
title: Vytvoření funkcí pro převod definice uživatelského rozhraní
description: Popisuje funkce, které se mají použít při převodu hodnot mezi datovými typy a kódováními.
author: tfitzmac
ms.topic: conceptual
ms.date: 07/13/2020
ms.author: tomfitz
ms.openlocfilehash: b69cd35b27b343da08727b4c4ee9b4fd025e1df7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "87096961"
---
# <a name="createuidefinition-conversion-functions"></a>Funkce převodu CreateUiDefinition

Tyto funkce lze použít pro převod hodnot mezi datovými typy JSON a kódování.

## <a name="bool"></a>bool

Převede parametr na logickou hodnotu. Tato funkce podporuje parametry typu Number, String a Boolean. Podobně jako logické hodnoty v JavaScriptu, jakákoli hodnota s výjimkou `0` nebo `'false'` vrátí `true` .

Následující příklad vrátí `true` :

```json
"[bool(1)]"
```

Následující příklad vrátí `false` :

```json
"[bool(0)]"
```

Následující příklad vrátí `true` :

```json
"[bool(true)]"
```

Následující příklad vrátí `true` :

```json
"[bool('true')]"
```

## <a name="decodebase64"></a>decodeBase64

Dekóduje parametr ze kódovaného řetězce Base-64. Tato funkce podporuje pouze parametry typu String.

Následující příklad vrátí `"Contoso"` :

```json
"[decodeBase64('Q29udG9zbw==')]"
```

## <a name="decodeuricomponent"></a>decodeURIComponent –

Dekóduje parametr z řetězce kódovaného adresou URL. Tato funkce podporuje pouze parametry typu String.

Následující příklad vrátí `"https://portal.azure.com/"` :

```json
"[decodeUriComponent('https%3A%2F%2Fportal.azure.com%2F')]"
```

## <a name="encodebase64"></a>encodeBase64

Zakóduje parametr do řetězce kódovaného pomocí základního 64. Tato funkce podporuje pouze parametry typu String.

Následující příklad vrátí `"Q29udG9zbw=="` :

```json
"[encodeBase64('Contoso')]"
```

## <a name="encodeuricomponent"></a>encodeURIComponent –

Zakóduje parametr do řetězce kódovaného adresou URL. Tato funkce podporuje pouze parametry typu String.

Následující příklad vrátí `"https%3A%2F%2Fportal.azure.com%2F"` :

```json
"[encodeUriComponent('https://portal.azure.com/')]"
```

## <a name="float"></a>float

Převede parametr na plovoucí desetinnou čárku. Tato funkce podporuje parametry typu Number a String.

Následující příklad vrátí `1.0` :

```json
"[float('1.0')]"
```

Následující příklad vrátí `2.9` :

```json
"[float(2.9)]"
```

## <a name="int"></a>int

Převede parametr na celé číslo. Tato funkce podporuje parametry typu Number a String.

Následující příklad vrátí `1` :

```json
"[int('1')]"
```

Následující příklad vrátí `2` :

```json
"[int(2.9)]"
```

## <a name="parse"></a>parse

Převede parametr na nativní typ. Jinými slovy je tato funkce inverzní k `string()` . Tato funkce podporuje pouze parametry typu String.

Následující příklad vrátí `1` :

```json
"[parse('1')]"
```

Následující příklad vrátí `true` :

```json
"[parse('true')]"
```

Následující příklad vrátí `[1,2,3]` :

```json
"[parse('[1,2,3]')]"
```

Následující příklad vrátí `{"type":"webapp"}` :

```json
"[parse('{\"type\":\"webapp\"}')]"
```

## <a name="string"></a>řetězec

Převede parametr na řetězec. Tato funkce podporuje parametry všech datových typů JSON.

Následující příklad vrátí `"1"` :

```json
"[string(1)]"
```

Následující příklad vrátí `"2.9"` :

```json
"[string(2.9)]"
```

Následující příklad vrátí `"[1,2,3]"` :

```json
"[string([1,2,3])]"
```

Následující příklad vrátí `"{"type":"webapp"}"` :

```json
"[string({\"type\":\"webapp\"})]"
```

## <a name="next-steps"></a>Další kroky

* Úvod do Azure Resource Manager najdete v tématu [Azure Resource Manager Overview](../management/overview.md).
