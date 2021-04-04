---
title: Konfigurace aplikací Azure REST API – revize klíč-hodnota
description: Referenční stránky pro práci s revizemi klíč-hodnota pomocí REST API konfigurace aplikace Azure
author: AlexandraKemperMS
ms.author: alkemper
ms.service: azure-app-configuration
ms.topic: reference
ms.date: 08/17/2020
ms.openlocfilehash: cfa117d1ed017170c279b7c4e0a146ae4edac108
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "96932468"
---
# <a name="key-value-revisions"></a>Revize klíč-hodnota

*Revize klíč-hodnota* definuje historické reprezentace prostředku klíč-hodnota. Revize vyprší za 7 dní pro úložiště úrovně Free nebo 30 dnů pro obchody úrovně Standard. Revize podporuje `List` operaci.

Pro všechny operace ``key`` je volitelný parametr. Pokud tento parametr vynecháte, znamená to, že je nějaký klíč.

Pro všechny operace ``label`` je volitelný parametr. Pokud tento parametr vynecháte, znamená to, že označuje libovolný popisek.

Tento článek se týká rozhraní API verze 1,0.

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-rest-api-prereqs.md)]

## <a name="list-revisions"></a>Seznam revizí

```http
GET /revisions?label=*&api-version={api-version} HTTP/1.1
```

**Požadavků**

```http
HTTP/1.1 200 OK
Content-Type: application/vnd.microsoft.appconfig.kvset+json; charset=utf-8"
Accept-Ranges: items
```

```json
{
    "items": [
        {
          "etag": "4f6dd610dd5e4deebc7fbaef685fb903",
          "key": "{key}",
          "label": "{label}",
          "content_type": null,
          "value": "example value",
          "last_modified": "2017-12-05T02:41:26.4874615+00:00",
          "tags": []
        },
        ...
    ],
    "@nextLink": "{relative uri}"
}
```

## <a name="pagination"></a>Stránkování

Výsledkem je stránkování, pokud počet vrácených položek překročí limit odezvy. Použijte volitelnou ``Link`` hlavičku odpovědi a použijte ji ``rel="next"`` pro navigaci. Další možností je, že obsah poskytuje další odkaz ve formě ``@nextLink`` Vlastnosti.

```http
GET /revisions?api-version={api-version} HTTP/1.1
```

**Základě**

```http
HTTP/1.1 OK
Content-Type: application/vnd.microsoft.appconfig.kvs+json; charset=utf-8
Accept-Ranges: items
Link: <{relative uri}>; rel="next"
```

```json
{
    "items": [
        ...
    ],
    "@nextLink": "{relative uri}"
}
```

## <a name="list-subset-of-revisions"></a>Seznam dílčích revizí

Použijte `Range` hlavičku požadavku. Odpověď obsahuje hlavičku `Content-Range`. Pokud server nedokáže splnit požadovaný rozsah, odpoví HTTP `416` ( `RangeNotSatisfiable` ).

```http
GET /revisions?api-version={api-version} HTTP/1.1
Range: items=0-2
```

**Response** (Odpověď)

```http
HTTP/1.1 206 Partial Content
Content-Type: application/vnd.microsoft.appconfig.revs+json; charset=utf-8
Content-Range: items 0-2/80
```

## <a name="filtering"></a>Filtrování

`key`Podporuje se kombinace a `label` filtrování.
Použijte volitelné `key` parametry a `label` parametr řetězce dotazu.

```http
GET /revisions?key={key}&label={label}&api-version={api-version}
```

### <a name="supported-filters"></a>Podporované filtry

|Filtr klíčů|Účinek|
|--|--|
|`key` je vynecháno nebo `key=*`|Odpovídá **jakémukoli** klíči|
|`key=abc`|Odpovídá klíči s názvem  **ABC**|
|`key=abc*`|Odpovídá názvům klíčů, které začínají na **ABC**|
|`key=*abc`|Odpovídá názvům klíčů, které končí na **ABC**|
|`key=*abc*`|Odpovídá názvům klíčů, které obsahují **ABC**|
|`key=abc,xyz`|Odpovídá názvům klíčů **ABC** nebo **XYZ** (omezeno na 5 CSV)|

|Filtr popisku|Účinek|
|--|--|
|`label` je vynecháno nebo `label=`|Odpovídá položce bez popisku|
|`label=*`|Odpovídá **jakémukoli** popisku|
|`label=prod`|Odpovídá popisku **kat** .|
|`label=prod*`|Odpovídá popiskům, které začínají na **prod**|
|`label=*prod`|Odpovídá popiskům, které končí na **prod**|
|`label=*prod*`|Odpovídá popiskům, které obsahují **prod** .|
|`label=prod,test`|Odpovídá popisku **na** oddělení nebo **test** (omezeno na 5 CSV)|

### <a name="reserved-characters"></a>Vyhrazené znaky

Vyhrazené znaky jsou:

`*`, `\`, `,`

Pokud je vyhrazený znak součástí hodnoty, musí být uvozen pomocí `\{Reserved Character}` . Nerezervované znaky mohou být také uvozeny řídicími znaky.

### <a name="filter-validation"></a>Ověření filtru

Pokud dojde k chybě ověřování filtru, odpověď je HTTP `400` s podrobnostmi o chybě:

```http
HTTP/1.1 400 Bad Request
Content-Type: application/problem+json; charset=utf-8
```

```json
{
  "type": "https://azconfig.io/errors/invalid-argument",
  "title": "Invalid request parameter '{filter}'",
  "name": "{filter}",
  "detail": "{filter}(2): Invalid character",
  "status": 400
}
```

### <a name="examples"></a>Příklady

- Všem

    ```http
    GET /revisions
    ```

- Položky, kde název klíče začíná na **ABC**:

    ```http
    GET /revisions?key=abc*&api-version={api-version}
    ```

- Položky, kde je název klíče **ABC** nebo **XYZ**, a popisky obsahují položku **prod**:

    ```http
    GET /revisions?key=abc,xyz&label=*prod*&api-version={api-version}
    ```

## <a name="request-specific-fields"></a>Konkrétní pole žádosti

Použijte volitelný `$select` parametr řetězce dotazu a zadejte čárkami oddělený seznam požadovaných polí. Pokud `$select` je parametr vynechán, odpověď obsahuje výchozí sadu.

```http
GET /revisions?$select=value,label,last_modified&api-version={api-version} HTTP/1.1
```

## <a name="time-based-access"></a>Přístup založený na čase

Získat reprezentace výsledku v čase, který byl v minulosti. Další informace najdete v tématu [rozhraní HTTP pro Time-Based přístupu ke stavům prostředků – soupisky](https://tools.ietf.org/html/rfc7089#section-2.1), oddíl 2.1.1.

```http
GET /revisions?api-version={api-version} HTTP/1.1
Accept-Datetime: Sat, 12 May 2018 02:10:00 GMT
```

**Základě**

```http
HTTP/1.1 200 OK
Content-Type: application/vnd.microsoft.appconfig.revs+json"
Memento-Datetime: Sat, 12 May 2018 02:10:00 GMT
Link: <{relative uri}>; rel="original"
```

```json
{
    "items": [
        ....
    ]
}
```
