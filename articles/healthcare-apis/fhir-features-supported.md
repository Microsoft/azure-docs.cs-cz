---
title: Podporované funkce FHIR v Azure – rozhraní API služby Azure pro FHIR
description: Tento článek vysvětluje, jaké funkce FHIR specifikace, které jsou implementovány v rozhraní API služby Azure pro FHIR
services: healthcare-apis
author: hansenms
ms.service: healthcare-apis
ms.topic: reference
ms.date: 02/07/2019
ms.author: mihansen
ms.openlocfilehash: a2851272d15ed2ce2c7f940cc3e429ef9f8b4446
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/07/2019
ms.locfileid: "55824008"
---
# <a name="features"></a>Funkce

Rozhraní API služby Azure pro FHIR poskytuje plně spravovanou nasazení Microsoft serveru FHIR pro Azure. Server je implementace [FHIR](https://hl7.org/fhir) standard. Tento dokument obsahuje seznam hlavní funkce FHIR serveru.

## <a name="fhir-version"></a>Verze FHIR

aktuální verze: `3.0.1`

## <a name="rest-api"></a>REST API

| Rozhraní API                            | Podporováno | Poznámka |
|--------------------------------|-----------|---------|
| číst                           | Ano       |         |
| vread                          | Ano       |         |
| update                         | Ano       |         |
| aktualizace pomocí optimistického zamykání | Ano       |         |
| aktualizace (podmíněné)           | Ne        |         |
| Oprava                          | Ne        |         |
| delete                         | Ano       |         |
| Odstranit (podmíněné)           | Ne        |         |
| vytvoření                         | Ano       | Podporovat obě POST a PUT |
| vytvořit (podmíněné)           | Ne        |         |
| hledat                         | Částečné   | Viz níže |
| Možnosti                   | Ano       |         |
| dávka                          | Ne        |         |
| Transakce                    | Ne        |         |
| Historie                        | Ano       |         |
| Stránkování                         | Částečné   | `self` a `next` jsou podporovány |
| Zprostředkovatelé                 | Ne        |         |

## <a name="search"></a>Search

Všechny typy parametrů hledání se nepodporuje. Zřetězené parametry a reverzní šifer jsou *není* podporována.

| Typ parametru hledání | Podporováno | Poznámka |
|-----------------------|-----------|---------|
| Číslo                | Ano       |         |
| Datem nebo datem a časem         | Ano       |         |
| String                | Ano       |         |
| Podpisový                 | Ano       |         |
| Referenční informace             | Ano       |         |
| Složené             | Ano       |         |
| Množství              | Ano       | Problém [#103](https://github.com/Microsoft/fhir-server/issues/103) |
| Identifikátor URI                   | Ano       |         |


| Modifikátory             | Podporováno | Poznámka |
|-----------------------|-----------|---------|
|`:missing`             | Ano       |         |
|`:exact`               | Ano       |         |
|`:contains`            | Ano       |         |
|`:text`                | Ano       |         |
|`:in` (token)          | Ne        |         |
|`:below` (token)       | Ne        |         |
|`:above` (token)       | Ne        |         |
|`:not-in` (token)      | Ne        |         |
|`:[type]` (referenční dokumentace)  | Ne        |         |
|`:below` (uri)         | Ano       |         |
|`:above` (uri)         | Ne        | Problém [#158](https://github.com/Microsoft/fhir-server/issues/158) |

| Společný parametr hledání | Podporováno | Poznámka |
|-------------------------| ----------|---------|
| `_id`                   | Ano       |         |
| `_lastUpdated`          | Ano       |         |
| `_tag`                  | Ano       |         |
| `_profile`              | Ano       |         |
| `_security`             | Ano       |         |
| `_text`                 | Ne        |         |
| `_content`              | Ne        |         |
| `_list`                 | Ne        |         |
| `_has`                  | Ne        |         |
| `_type`                 | Ano       |         |
| `_query`                | Ne        |         |

| Operace hledání       | Podporováno | Poznámka |
|-------------------------|-----------|---------|
| `_filter`               | Ne        |         |
| `_sort`                 | Ne        |         |
| `_score`                | Ne        |         |
| `_count`                | Ano       |         |
| `_summary`              | Částečné   | `_summary=count` je podporováno |
| `_include`              | Ne        |         |
| `_revinclude`           | Ne        |         |
| `_contained`            | Ne        |         |
| `_elements`             | Ne        |         |

## <a name="persistence"></a>Trvalost

Microsoft FHIR Server má modulární trvalého modulu (viz [ `Microsoft.Health.Fhir.Core.Features.Persistence` ](https://github.com/Microsoft/fhir-server/src/Microsoft.Health.Fhir.Core/Features/Persistence)).

Aktuálně otevřený zdrojový kód FHIR Server obsahuje implementaci pro [služby Azure Cosmos DB](../cosmos-db/index-overview.md).

Cosmos DB je globálně distribuovaná více modelů (rozhraní SQL API, rozhraní API MongoDB atd.) databáze. Podporuje různé [úrovně konzistence](../cosmos-db/consistency-levels.md). Výchozí šablona nasazení nakonfiguruje Server FHIR pomocí `Strong` konzistence, ale zásady konzistence může být upraveno (obecně volný) v žádosti o žádosti pomocí `x-ms-consistency-level` hlavičky žádosti.

## <a name="role-based-access-control"></a>Řízení přístupu na základě rolí

Používá FHIR Server [Azure Active Directory](https://azure.microsoft.com/services/active-directory/) pro řízení přístupu. Konkrétně se vynucuje na základě řízení přístupu Role (RBAC), pokud `FhirServer:Security:Enabled` konfigurační parametr je nastaven na `true`a všechny požadavky (s výjimkou `/metadata`) musí mít server FHIR `Authorization` hlavičku požadavku nastavte na `Bearer <TOKEN>`. Token musí obsahovat jednu nebo víc rolí, jak jsou definovány v `roles` deklarací identity. Požadavek bude povoleno, pokud token, který obsahuje roli, která umožňuje na zadanou akci pro zadaný prostředek.

V současné době se použijí povolené akce pro danou roli *globálně* na rozhraní API.

## <a name="next-steps"></a>Další postup

V tomto článku jste si přečetli o podporovaných funkcích FHIR v rozhraní API služby Azure pro FHIR. Nasaďte další rozhraní API FHIR v Azure.
 
>[!div class="nextstepaction"]
>[Nasazení Open FHIR zdrojového serveru](fhir-oss-powershell-quickstart.md)