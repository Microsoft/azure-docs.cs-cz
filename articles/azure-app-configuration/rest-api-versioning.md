---
title: Konfigurace aplikace Azure REST API – Správa verzí
description: Referenční stránky pro správu verzí pomocí REST API konfigurace aplikací Azure
author: AlexandraKemperMS
ms.author: alkemper
ms.service: azure-app-configuration
ms.topic: reference
ms.date: 08/17/2020
ms.openlocfilehash: a869531860942e5a8b2b05212e778aca2170c89b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "96932417"
---
# <a name="versioning"></a>Správa verzí

Každý požadavek klienta musí jako parametr řetězce dotazu zadat explicitní verzi rozhraní API. Příklad: `https://{myconfig}.azconfig.io/kv?api-version=1.0`.

`api-version` je vyjádřen ve formátu SemVer (hlavní_verze. podverze). Vyjednávání rozsahu nebo verze se nepodporuje.

Tento článek se týká rozhraní API verze 1,0.

Následující text objednává souhrn možných odpovědí na chyby vrácených serverem, když nelze porovnat požadovanou verzi rozhraní API.

## <a name="api-version-unspecified"></a>Neurčená verze rozhraní API

K této chybě dojde, když klient odešle požadavek bez poskytnutí verze rozhraní API.

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

## <a name="unsupported-api-version"></a>Nepodporovaná verze rozhraní API

K této chybě dojde, když klient požadoval verzi rozhraní API, protože server neodpovídá žádné z podporovaných verzí rozhraní API.

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

## <a name="invalid-api-version"></a>Neplatná verze rozhraní API

K této chybě dochází, když klient vytvoří požadavek s verzí rozhraní API, ale hodnota je poškozená nebo nemůže být analyzována serverem.

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

## <a name="ambiguous-api-version"></a>Dvojznačná verze rozhraní API

K této chybě dojde, když klient požaduje verzi rozhraní API, která je nejednoznačná na serveru (například více různých hodnot).

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
