---
title: Konfigurace aplikace Azure REST API – Správa verzí
description: Referenční stránky pro správu verzí pomocí REST API konfigurace aplikací Azure
author: lisaguthrie
ms.author: lcozzens
ms.service: azure-app-configuration
ms.topic: reference
ms.date: 08/17/2020
ms.openlocfilehash: 90d131cdc7c496853f2520951c95b9903d69f8fb
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/06/2020
ms.locfileid: "93424062"
---
# <a name="versioning"></a>Správa verzí

verze API-Version: 1,0

Každý požadavek klienta musí jako parametr řetězce dotazu zadat explicitní verzi rozhraní API. Příklad: `https://{myconfig}.azconfig.io/kv?api-version=1.0`

`api-version` je vyjádřen ve formátu SemVer (hlavní_verze. podverze). Vyjednávání rozsahu nebo verze se nepodporuje.

## <a name="error-response"></a>Chybová odezva

Následující text objednává souhrn možných odpovědí na chyby vrácených serverem, když nelze porovnat požadovanou verzi rozhraní API.

### <a name="api-version-unspecified"></a>Neurčená verze rozhraní API

Vyvolá se v případě, že klient vytvoří požadavek bez poskytnutí verze rozhraní API.

```http
HTTP/1.1 400 Bad Request
Content-Type: application/problem+json; charset=utf-8
{
  "type": "https://azconfig.io/errors/invalid-argument",
  "title": "API version is not specified",
  "name": "api-version",
  "detail": "An API version is required, but was not specified.",
  "status": 400
}
```

### <a name="unsupported-api-version"></a>Nepodporovaná verze rozhraní API

Vyvolá se v případě, že požadovaná verze rozhraní API klienta neodpovídá žádné z podporovaných verzí rozhraní API serverem.

```http
HTTP/1.1 400 Bad Request
Content-Type: application/problem+json; charset=utf-8
{
  "type": "https://azconfig.io/errors/invalid-argument",
  "title": "Unsupported API version",
  "name": "api-version",
  "detail": "The HTTP resource that matches the request URI '{request uri}' does not support the API version '{api-version}'.",
  "status": 400
}
```

### <a name="invalid-api-version"></a>Neplatná verze rozhraní API

Vyvolá se v případě, že klient vytvoří požadavek s verzí rozhraní API, ale hodnota je poškozená nebo nemůže být analyzována serverem.

```http
HTTP/1.1 400 Bad Request
Content-Type: application/problem+json; charset=utf-8  
{
  "type": "https://azconfig.io/errors/invalid-argument",
  "title": "Invalid API version",
  "name": "api-version",
  "detail": "The HTTP resource that matches the request URI '{request uri}' does not support the API version '{api-version}'.",
  "status": 400
}
```

### <a name="ambiguous-api-version"></a>Dvojznačná verze rozhraní API

Vyvolá se v případě, že klient požaduje verzi rozhraní API, která je pro server nejednoznačná. Například více různých hodnot.

```http
HTTP/1.1 400 Bad Request
Content-Type: application/problem+json; charset=utf-8
{
  "type": "https://azconfig.io/errors/invalid-argument",
  "title": "Ambiguous API version",
  "name": "api-version",
  "detail": "The following API versions were requested: {comma separated api versions}. At most, only a single API version may be specified. Please update the intended API version and retry the request.",
  "status": 400
}
```
