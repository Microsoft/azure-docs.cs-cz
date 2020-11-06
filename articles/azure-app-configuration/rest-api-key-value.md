---
title: REST API konfigurace aplikace Azure – Key-Value
description: Referenční stránky pro práci s klíčovými hodnotami pomocí konfigurace aplikace Azure REST API
author: lisaguthrie
ms.author: lcozzens
ms.service: azure-app-configuration
ms.topic: reference
ms.date: 08/17/2020
ms.openlocfilehash: 50d97a330507e9361674776acf29d1007ee5bf58
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/06/2020
ms.locfileid: "93423995"
---
# <a name="key-values"></a>Key-Values

verze API-Version: 1,0

Klíč-hodnota je prostředek identifikovaný jedinečnou kombinací `key`  +  `label` . Parametr `label` je volitelný. Chcete-li explicitně odkazovat na klíčovou hodnotu bez popisku, použijte "\ 0" (adresa URL je zakódována jako ``%00`` ). Podívejte se na podrobnosti pro každou operaci.

## <a name="operations"></a>Operace

- Získat
- Seznam více
- Nastavit
- Odstranit

## <a name="prerequisites"></a>Předpoklady

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-rest-api-prereqs.md)]

## <a name="syntax"></a>Syntax

```json
{
  "etag": [string],
  "key": [string],
  "label": [string, optional],
  "content_type": [string, optional],
  "value": [string],
  "last_modified": [datetime ISO 8601],
  "locked": [boolean],
  "tags": [object with string properties, optional]
}
```

## <a name="get-key-value"></a>Získat Key-Value

**Požadováno:** ``{key}`` , ``{api-version}``  
*Volitelné:* ``label`` – Pokud je vynechaná, implikuje klíčovou hodnotu bez popisku.

```http
GET /kv/{key}?label={label}&api-version={api-version}
```

**Požadavků**

```http
HTTP/1.1 200 OK
Content-Type: application/vnd.microsoft.appconfig.kv+json; charset=utf-8;
Last-Modified: Tue, 05 Dec 2017 02:41:26 GMT
ETag: "4f6dd610dd5e4deebc7fbaef685fb903"
```

```json
{
  "etag": "4f6dd610dd5e4deebc7fbaef685fb903",
  "key": "{key}",
  "label": "{label}",
  "content_type": null,
  "value": "example value",
  "last_modified": "2017-12-05T02:41:26+00:00",
  "locked": "false",
  "tags": {
    "t1": "value1",
    "t2": "value2"
  }
}
```

Pokud klíč neexistuje, vrátí se následující odpověď:

```http
HTTP/1.1 404 Not Found
```

## <a name="get-conditionally"></a>Získat (podmíněně)

Chcete-li zlepšit ukládání do mezipaměti klienta, použijte `If-Match` nebo `If-None-Match` hlavičku požadavku. `etag`Argument je součástí reprezentace klíče. Viz [část 14,24 a 14,26](https://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html).

Následující požadavek načte klíč-hodnota pouze v případě, že aktuální reprezentace neodpovídá zadané hodnotě `etag` :

```http
GET /kv/{key}?api-version={api-version} HTTP/1.1
Accept: application/vnd.microsoft.appconfig.kv+json;
If-None-Match: "{etag}"
```

**Požadavků**

```http
HTTP/1.1 304 NotModified
```

nebo

```http
HTTP/1.1 200 OK
```

## <a name="list-key-values"></a>Seznam Key-Values

Další možnosti najdete v tématu **filtrování** .

*Volitelné:* ``key`` – Pokud není zadaný, implikuje se **libovolný** klíč.
*Volitelné:* ``label`` – Pokud není zadaný, předpokládá se **libovolný** popisek.

```http
GET /kv?label=*&api-version={api-version} HTTP/1.1
```

**Základě**

```http
HTTP/1.1 200 OK
Content-Type: application/vnd.microsoft.appconfig.kvset+json; charset=utf-8
```

## <a name="pagination"></a>Stránkování

Výsledkem je stránkování, pokud počet vrácených položek překročí limit odezvy. Postupujte podle volitelných `Link` hlaviček odpovědí a použijte `rel="next"` pro navigaci.
Alternativně obsah poskytuje další odkaz ve formě `@nextLink` Vlastnosti. Propojený identifikátor URI obsahuje `api-version` argument.

```http
GET /kv?api-version={api-version} HTTP/1.1
```

**Základě**

```http
HTTP/1.1 200 OK
Content-Type: application/vnd.microsoft.appconfig.kvs+json; charset=utf-8
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

## <a name="filtering"></a>Filtrování

`key`Podporuje se kombinace a `label` filtrování.
Použijte volitelné `key` parametry a `label` parametr řetězce dotazu.

```http
GET /kv?key={key}&label={label}&api-version={api-version}
```

### <a name="supported-filters"></a>Podporované filtry

|Filtr klíčů|Účinek|
|--|--|
|`key` je vynecháno nebo `key=*`|Odpovídá **jakémukoli** klíči|
|`key=abc`|Odpovídá klíči s názvem **ABC**|
|`key=abc*`|Odpovídá názvům klíčů, které začínají na **ABC**|
|`key=abc,xyz`|Odpovídá názvům klíčů **ABC** nebo **XYZ** (omezeno na 5 CSV)|

|Filtr popisku|Účinek|
|--|--|
|`label` je vynecháno nebo `label=*`|Odpovídá **jakémukoli** popisku|
|`label=%00`|Odpovídá KV bez popisku|
|`label=prod`|Odpovídá popisku **kat** .|
|`label=prod*`|Odpovídá popiskům, které začínají na **prod**|
|`label=prod,test`|Odpovídá popisku **na** oddělení nebo **test** (omezeno na 5 CSV)|

**_Rezervované znaky_* _

`_`, `\`, `,`

Pokud je vyhrazený znak součástí hodnoty, musí být uvozen pomocí `\{Reserved Character}` . Nerezervované znaky mohou být také uvozeny řídicími znaky.

***Ověření filtru** _

V případě chyby ověření filtru je odpověď HTTP `400` s podrobnostmi o chybě:

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

_ – *Příklady**

- Vše

    ```http
    GET /kv?api-version={api-version}
    ```

- Název klíče začíná na **ABC** a zahrnuje všechny popisky.

    ```http
    GET /kv?key=abc*&label=*&api-version={api-version}
    ```

- Název klíče začíná na **ABC** a popisek se rovná **v1** nebo **v2** .

    ```http
    GET /kv?key=abc*&label=v1,v2&api-version={api-version}
    ```

## <a name="request-specific-fields"></a>Konkrétní pole žádosti

Použijte volitelný `$select` parametr řetězce dotazu a zadejte čárkami oddělený seznam požadovaných polí. Pokud `$select` je parametr vynechán, odpověď obsahuje výchozí sadu.

```http
GET /kv?$select=key,value&api-version={api-version} HTTP/1.1
```

## <a name="time-based-access"></a>Přístup k Time-Based

Získat reprezentace výsledku v čase, který byl v minulosti. Viz část [2.1.1](https://tools.ietf.org/html/rfc7089#section-2.1). Stránkování je stále podporováno, jak je definováno výše.

```http
GET /kv?api-version={api-version} HTTP/1.1
Accept-Datetime: Sat, 12 May 2018 02:10:00 GMT
```

**Základě**

```http
HTTP/1.1 200 OK
Content-Type: application/vnd.microsoft.appconfig.kvset+json"
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

## <a name="set-key"></a>Nastavit klíč

- **Požadováno:**``{key}``
- *Volitelné:* ``label`` – Pokud není zadaný nebo je popisek = %00, znamená to, že bez popisku je KV.

```http
PUT /kv/{key}?label={label}&api-version={api-version} HTTP/1.1
Content-Type: application/vnd.microsoft.appconfig.kv+json
```

```json
{
  "value": "example value",         // optional
  "content_type": "user defined",   // optional
  "tags": {                         // optional
    "tag1": "value1",
    "tag2": "value2",
  }
}
```

**Požadavků**

```http
HTTP/1.1 200 OK
Content-Type: application/vnd.microsoft.appconfig.kv+json; charset=utf-8
Last-Modified: Tue, 05 Dec 2017 02:41:26 GMT
ETag: "4f6dd610dd5e4deebc7fbaef685fb903"
```

```json
{
  "etag": "4f6dd610dd5e4deebc7fbaef685fb903",
  "key": "{key}",
  "label": "{label}",
  "content_type": "user defined",
  "value": "example value",
  "last_modified": "2017-12-05T02:41:26.4874615+00:00",
  "tags": {
    "tag1": "value1",
    "tag2": "value2",
  }
}
```

Je-li položka uzamčena, obdržíte následující odpověď:

```http
HTTP/1.1 409 Conflict
Content-Type: application/problem+json; charset="utf-8"
```

```json
{
    "type": "https://azconfig.io/errors/key-locked",
    "title": "Modifing key '{key}' is not allowed",
    "name": "{key}",
    "detail": "The key is read-only. To allow modification unlock it first.",
    "status": "409"
}
```

## <a name="set-key-conditionally"></a>Set – klíč (podmíněně)

Aby nedocházelo ke konfliktům časování, použijte `If-Match` nebo `If-None-Match` vyžádejte hlavičky. `etag`Argument je součástí reprezentace klíče.
Pokud `If-Match` `If-None-Match` jsou nebo vynechány, operace bude nepodmíněný.

Následující odpověď aktualizuje hodnotu pouze v případě, že aktuální reprezentace odpovídá zadané `etag`

```http
PUT /kv/{key}?label={label}&api-version={api-version} HTTP/1.1
Content-Type: application/vnd.microsoft.appconfig.kv+json
If-Match: "4f6dd610dd5e4deebc7fbaef685fb903"
```

Následující odpověď aktualizuje hodnotu pouze v případě, že *aktuální reprezentace neodpovídá zadané hodnotě.*`etag`

```http
PUT /kv/{key}?label={label}&api-version={api-version} HTTP/1.1
Content-Type: application/vnd.microsoft.appconfig.kv+json;
If-None-Match: "4f6dd610dd5e4deebc7fbaef685fb903"
```

Následující žádost přidá hodnotu pouze v případě, že reprezentace již existuje:

```http
PUT /kv/{key}?label={label}&api-version={api-version} HTTP/1.1
Content-Type: application/vnd.microsoft.appconfig.kv+json;
If-Match: "*"
```

Následující požadavek přidá hodnotu pouze v případě, *že reprezentace ještě neexistuje:*

```http
PUT /kv/{key}?label={label}&api-version={api-version} HTTP/1.1
Content-Type: application/vnd.microsoft.appconfig.kv+json
If-None-Match: "*"
```

**Odpovědi**

```http
HTTP/1.1 200 OK
Content-Type: application/vnd.microsoft.appconfig.kv+json; charset=utf-8
...
```

nebo

```http
HTTP/1.1 412 PreconditionFailed
```

## <a name="delete"></a>Odstranit

- **Požadováno:** `{key}` , `{api-version}`
- *Volitelné:* `{label}` – Pokud není zadaný nebo je popisek = %00, znamená to, že bez popisku je KV.

```http
DELETE /kv/{key}?label={label}&api-version={api-version} HTTP/1.1
```

**Odpověď:** Vrátí odstraněný klíč-hodnota nebo žádné, pokud klíč-hodnota neexistuje.

```http
HTTP/1.1 200 OK
Content-Type: application/vnd.microsoft.appconfig.kv+json; charset=utf-8
...
```

nebo

```http
HTTP/1.1 204 No Content
```

## <a name="delete-key-conditionally"></a>Odstranit klíč (podmíněně)

Podobný jako **klíč nastavení (podmíněně)**
