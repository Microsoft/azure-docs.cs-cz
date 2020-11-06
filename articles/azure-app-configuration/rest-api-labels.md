---
title: REST API – popisky konfigurace aplikace Azure
description: Referenční stránky pro práci s popisky pomocí konfigurace aplikace Azure REST API
author: lisaguthrie
ms.author: lcozzens
ms.service: azure-app-configuration
ms.topic: reference
ms.date: 08/17/2020
ms.openlocfilehash: e6fcc8399c1dbc36a5b2e915c726f1c2496ee2f9
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/06/2020
ms.locfileid: "93423991"
---
# <a name="labels"></a>Popisky

verze API-Version: 1,0

Prostředek **popisku** je definován následujícím způsobem:

```json
{
      "name": [string]             // Name of the label
}
```

Podporuje následující operace:

- Seznam

Pro všechny operace ``name`` je volitelný parametr filtru. Pokud tento parametr vynecháte, bude to označovat **libovolný** popisek.

## <a name="prerequisites"></a>Předpoklady

- Všechny požadavky HTTP musí být ověřeny. Viz část [ověřování](./rest-api-authentication-index.md) .
- Všechny požadavky HTTP musí poskytovat explicitní `api-version` . Viz část [Správa verzí](./rest-api-versioning.md) .

## <a name="list-labels"></a>Seznam popisků

```http
GET /labels?api-version={api-version} HTTP/1.1
```

**Požadavků**

```http
HTTP/1.1 200 OK
Content-Type: application/vnd.microsoft.appconfig.labelset+json; charset=utf-8"
```

```json
{
    "items": [
        {
          "name": "{label-name}"
        },
        ...
    ],
    "@nextLink": "{relative uri}"
}
```

## <a name="pagination"></a>Stránkování

Výsledkem je stránkování, pokud počet vrácených položek překročí limit odezvy. Postupujte podle volitelných `Link` hlaviček odpovědí a použijte `rel="next"` pro navigaci. Alternativně obsah poskytuje další odkaz ve formě `@nextLink` Vlastnosti. Další odkaz obsahuje `api-version` parametr.

```http
GET /labels?api-version={api-version} HTTP/1.1
```

**Základě**

```http
HTTP/1.1 OK
Content-Type: application/vnd.microsoft.appconfig.labelset+json; charset=utf-8
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

## <a name="filtering"></a>Filtrování

Filtrování podle `name` je podporováno.

```http
GET /labels?name={label-name}&api-version={api-version}
```

### <a name="supported-filters"></a>Podporované filtry

|Filtr klíčů|Účinek|
|--|--|
|`name` je vynecháno nebo `name=*`|Odpovídá **jakémukoli** popisku|
|`name=abc`|Odpovídá popisku s názvem  **ABC**|
|`name=abc*`|Odpovídá názvům popisků, které začínají na **ABC**|
|`name=abc,xyz`|Odpovídá názvům popisků **ABC** nebo **XYZ** (omezeno na 5 sdílených svazků clusteru).|

### <a name="reserved-characters"></a>Vyhrazené znaky

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
  "title": "Invalid request parameter 'name'",
  "name": "name",
  "detail": "name(2): Invalid character",
  "status": 400
}
```

### <a name="examples"></a>Příklady

- Vše

    ```http
    GET /labels?api-version={api-version}
    ```

- Název popisku začíná na **ABC**

    ```http
    GET  /labels?name=abc*&api-version={api-version}
    ```

- Název popisku je buď **ABC** , nebo **XYZ** .

    ```http
    GET /labels?name=abc,xyz&api-version={api-version}
    ```

## <a name="request-specific-fields"></a>Konkrétní pole žádosti

Použijte volitelný `$select` parametr řetězce dotazu a zadejte čárkami oddělený seznam požadovaných polí. Pokud `$select` je parametr vynechán, odpověď obsahuje výchozí sadu.

```http
GET /labels?$select=name&api-version={api-version} HTTP/1.1
```

## <a name="time-based-access"></a>Přístup k Time-Based

Získat reprezentace výsledku v čase, který byl v minulosti. Viz část [2.1.1](https://tools.ietf.org/html/rfc7089#section-2.1)

```http
GET /labels&api-version={api-version} HTTP/1.1
Accept-Datetime: Sat, 12 May 2018 02:10:00 GMT
```

**Základě**

```http
HTTP/1.1 200 OK
Content-Type: application/vnd.microsoft.appconfig.labelset+json"
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
