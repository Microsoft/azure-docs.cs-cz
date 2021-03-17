---
title: REST API klíče konfigurace aplikace Azure
description: Referenční stránky pro práci s klíči pomocí konfigurace aplikace Azure REST API
author: AlexandraKemperMS
ms.author: alkemper
ms.service: azure-app-configuration
ms.topic: reference
ms.date: 08/17/2020
ms.openlocfilehash: 65ca190d7fbd6d8d4df473fbe2112eafbd031fde
ms.sourcegitcommit: 1756a8a1485c290c46cc40bc869702b8c8454016
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/09/2020
ms.locfileid: "96932536"
---
# <a name="keys"></a>Klíče

verze API-Version: 1,0

Následující syntaxe představuje prostředek klíče:

```http
{
    "name": [string]             // Name of the key
}
```

## <a name="operations"></a>Operace

Klíčové prostředky podporují následující operaci:

- Seznam

Pro všechny operace `name` je volitelný parametr filtru. Pokud tento parametr vynecháte, znamená to, že je *nějaký* klíč.

## <a name="prerequisites"></a>Předpoklady

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-rest-api-prereqs.md)]

## <a name="list-keys"></a>Zobrazit seznam klíčů

```http
GET /keys?api-version={api-version} HTTP/1.1
```

**Požadavků**

```http
HTTP/1.1 200 OK
Content-Type: application/vnd.microsoft.appconfig.keyset+json; charset=utf-8"
```

```json
{
    "items": [
        {
          "name": "{key-name}"
        },
        ...
    ],
    "@nextLink": "{relative uri}"
}
```

## <a name="pagination"></a>Stránkování

Výsledkem je stránkování, pokud počet vrácených položek překročí limit odezvy. Postupujte podle volitelných `Link` hlaviček odpovědí a použijte `rel="next"` pro navigaci. Alternativně obsah poskytuje další odkaz ve formě `@nextLink` Vlastnosti. Další odkaz obsahuje `api-version` parametr.

```http
GET /keys?api-version={api-version} HTTP/1.1
```

**Základě**

```http
HTTP/1.1 OK
Content-Type: application/vnd.microsoft.appconfig.keyset+json; charset=utf-8
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

Filtrování podle ```name``` je podporováno.

```http
GET /keys?name={key-name}&api-version={api-version}
```

Podporují se tyto filtry:

|Filtr klíčů|Účinek|
|--|--|
|`name` je vynecháno nebo `name=*`|Odpovídá **jakémukoli** klíči|
|`name=abc`|Odpovídá klíči s názvem  **ABC**|
|`name=abc*`|Odpovídá názvům klíčů, které začínají na **ABC**|
|`name=abc,xyz`|Odpovídá názvům klíčů **ABC** nebo **XYZ** (omezeno na 5 CSV)|

Následující znaky jsou vyhrazené: `*` , `\` , `,`

Pokud je vyhrazený znak součástí hodnoty, musí být uvozen pomocí `\{Reserved Character}` . Nerezervované znaky mohou být také uvozeny řídicími znaky.

## <a name="filter-validation"></a>Ověření filtru

V případě chyby ověření filtru je odpověď HTTP `400` s podrobnostmi o chybě:

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

## <a name="examples"></a>Příklady

- Vše

    ```http
    GET /keys?api-version={api-version}
    ```

- Název klíče začíná na **ABC**

    ```http
    GET  /keys?name=abc*&api-version={api-version}
    ```

- Název klíče je buď **ABC** , nebo **XYZ** .

    ```http
    GET /keys?name=abc,xyz&api-version={api-version}
    ```

## <a name="request-specific-fields"></a>Konkrétní pole žádosti

Použijte volitelný `$select` parametr řetězce dotazu a zadejte čárkami oddělený seznam požadovaných polí. Pokud `$select` je parametr vynechán, odpověď obsahuje výchozí sadu.

```http
GET /keys?$select=name&api-version={api-version} HTTP/1.1
```

## <a name="time-based-access"></a>Přístup k Time-Based

Získat reprezentace výsledku v čase, který byl v minulosti. Viz část [2.1.1](https://tools.ietf.org/html/rfc7089#section-2.1)

```http
GET /keys&api-version={api-version} HTTP/1.1
Accept-Datetime: Sat, 12 May 2018 02:10:00 GMT
```

**Základě**

```http
HTTP/1.1 200 OK
Content-Type: application/vnd.microsoft.appconfig.keyset+json"
Memento-Datetime: Sat, 12 May 2018 02:10:00 GMT
Link: <relative uri>; rel="original"
```

```json
{
    "items": [
        ....
    ]
}
```
