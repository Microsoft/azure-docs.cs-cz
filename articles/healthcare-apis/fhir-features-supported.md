---
title: Podporované funkce FHIR v Azure – Azure API pro FHIR
description: Tento článek vysvětluje, které funkce specifikace FHIR jsou implementované v Azure API pro FHIR.
services: healthcare-apis
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 02/07/2019
ms.author: matjazl
ms.openlocfilehash: afb4026a7865f2cc8f831d8d1d7b1d332014d310
ms.sourcegitcommit: 3c66bfd9c36cd204c299ed43b67de0ec08a7b968
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/10/2020
ms.locfileid: "90007566"
---
# <a name="features"></a>Funkce

Azure API for FHIR poskytuje plně spravované nasazení serveru Microsoft FHIR pro Azure. Server je implementací standardu [FHIR](https://hl7.org/fhir) . Tento dokument obsahuje seznam hlavních funkcí serveru FHIR.

## <a name="fhir-version"></a>Verze FHIR

Nejnovější verze je podporována: `4.0.1`

Předchozí verze, které jsou aktuálně podporované, zahrnují: `3.0.2`

## <a name="rest-api"></a>REST API

| Rozhraní API                            | Podporováno – PaaS | Podporováno – OSS (SQL) | Podporované – OSS (Cosmos DB) | Komentář                                             |
|--------------------------------|-----------|-----------|-----------|-----------------------------------------------------|
| read                           | Yes       | Yes       | Yes       |                                                     |
| vread                          | Yes       | Yes       | Yes       |                                                     |
| update                         | Yes       | Yes       | Yes       |                                                     |
| aktualizace s optimistické uzamykání | Yes       | Yes       | Yes       |                                                     |
| aktualizovat (podmíněné)           | Yes       | Yes       | Yes       |                                                     |
| použita                          | No        | No        | No        |                                                     |
| delete                         | Yes       | Yes       | Yes       |                                                     |
| Odstranit (podmíněné)           | No        | No        | No        |                                                     |
| vytvoření                         | Yes       | Yes       | Yes       | Podpora pro POST/PUT                               |
| vytvořit (podmíněné)           | Yes       | Yes       | Yes       |                                                     |
| search                         | Částečné   | Částečné   | Částečné   | Viz níže                                           |
| zřetězené hledání                 | No        | Yes       | No        |                                           |
| zpětné zřetězené hledání         | No        | No        | No        |                                            |
| možnosti                   | Yes       | Yes       | Yes       |                                                     |
| dávka                          | Yes       | Yes       | Yes       |                                                     |
| transakce                    | No        | Yes       | No        |                                                     |
| historie                        | Yes       | Yes       | Yes       |                                                     |
| přenosu                         | Částečné   | Částečné   | Částečné   | `self` a `next` jsou podporovány                     |
| prostředníci                 | No        | No        | No        |                                                     |

## <a name="search"></a>Search

Všechny typy parametrů vyhledávání jsou podporovány. 

| Typ parametru hledání | Podporováno – PaaS | Podporováno – OSS (SQL) | Podporované – OSS (Cosmos DB) | Komentář |
|-----------------------|-----------|-----------|-----------|---------|
| Číslo                | Yes       | Yes       | Yes       |         |
| Datum/datum a čas         | Yes       | Yes       | Ano       |         |
| Řetězec                | Yes       | Yes       | Yes       |         |
| Token                 | Yes       | Yes       | Yes       |         |
| Reference             | Yes       | Yes       | Yes       |         |
| Složený             | Yes       | Yes       | Yes       |         |
| Množství              | Yes       | Yes       | Yes       |         |
| Identifikátor URI                   | Yes       | Yes       | Yes       |         |
| Speciální               | No        | No        | No        |         |


| Modifikátory             | Podporováno – PaaS | Podporováno – OSS (SQL) | Podporované – OSS (Cosmos DB) | Komentář |
|-----------------------|-----------|-----------|-----------|---------|
|`:missing`             | Yes       | Yes       | Yes       |         |
|`:exact`               | Yes       | Yes       | Yes       |         |
|`:contains`            | Yes       | Yes       | Yes       |         |
|`:text`                | Yes       | Yes       | Yes       |         |
|`:in` klíčové          | No        | No        | No        |         |
|`:below` klíčové       | No        | No        | No        |         |
|`:above` klíčové       | No        | No        | No        |         |
|`:not-in` klíčové      | No        | No        | No        |         |
|`:[type]` odkaz  | No        | No        | No        |         |
|`:below` identifikátor URI         | Yes       | Yes       | Yes       |         |
|`:not`                 | No        | No        | No        |         |
|`:above` identifikátor URI         | No        | No        | No        | Problém [#158](https://github.com/Microsoft/fhir-server/issues/158) |

| Společný parametr hledání | Podporováno – PaaS | Podporováno – OSS (SQL) | Podporované – OSS (Cosmos DB) | Komentář |
|-------------------------| ----------| ----------| ----------|---------|
| `_id`                   | Yes       | Yes       | Yes       |         |
| `_lastUpdated`          | Yes       | Yes       | Yes       |         |
| `_tag`                  | Yes       | Yes       | Yes       |         |
| `_profile`              | Yes       | Yes       | Yes       |         |
| `_security`             | Yes       | Yes       | Yes       |         |
| `_text`                 | No        | No        | No        |         |
| `_content`              | No        | No        | No        |         |
| `_list`                 | Yes       | Yes       | Yes       |         |
| `_has`                  | No        | No        | No        |         |
| `_type`                 | Yes       | Yes       | Yes       |         |
| `_query`                | No        | No        | No        |         |

| Operace hledání       | Podporováno – PaaS | Podporováno – OSS (SQL) | Podporované – OSS (Cosmos DB) | Komentář |
|-------------------------|-----------|-----------|-----------|---------|
| `_filter`               | No        | No        | No        |         |
| `_sort`                 | Částečné        | Částečné   | Částečné        |   `_sort=_lastUpdated` je podporováno       |
| `_score`                | No        | No        | No        |         |
| `_count`                | Yes       | Yes       | Yes       |         |
| `_summary`              | Částečné   | Částečné   | Částečné   | `_summary=count` je podporováno |
| `_include`              | No        | Yes       | No        |         |
| `_revinclude`           | No        | Yes       | No        | Zahrnuté položky jsou omezeny na 100. |
| `_contained`            | No        | No        | No        |         |
| `_elements`             | Yes        | Yes        | Yes        |         |

## <a name="extended-operations"></a>Rozšířené operace

Všechny podporované operace, které rozšiřuje rozhraní RESTful API.

| Typ parametru hledání | Podporováno – PaaS | Podporováno – OSS (SQL) | Podporované – OSS (Cosmos DB) | Komentář |
|-----------------------|-----------|-----------|-----------|---------|
| $export (celý systém)                | Yes       | Yes       | Yes       |         |
| Pacient/$export         | Yes       | Yes       | Yes       |         |
| Skupina/$export               | Yes       | Yes       | Yes       |         |

## <a name="persistence"></a>Uchování

Server Microsoft FHIR má modul připojitelná Persistence (viz [`Microsoft.Health.Fhir.Core.Features.Persistence`](https://github.com/Microsoft/fhir-server/tree/master/src/Microsoft.Health.Fhir.Core/Features/Persistence) ).

V současné době je otevřený zdrojový kód FHIR serveru, který obsahuje implementaci [Azure Cosmos DB](../cosmos-db/index-overview.md) a [SQL Database](https://azure.microsoft.com/services/sql-database/).

Cosmos DB je globálně distribuovaná databáze s více modely (rozhraní API SQL, rozhraní MongoDB API atd.). Podporuje různé [úrovně konzistence](../cosmos-db/consistency-levels.md). Výchozí šablona nasazení konfiguruje server FHIR s `Strong` konzistencí, ale zásady konzistence je možné upravit (obecně zmírnit) na žádost pomocí `x-ms-consistency-level` hlavičky žádosti.

## <a name="role-based-access-control"></a>Řízení přístupu na základě role

Server FHIR používá pro řízení přístupu [Azure Active Directory](https://azure.microsoft.com/services/active-directory/) . Konkrétně se vynutila Access Control založená na rolích (RBAC), pokud je `FhirServer:Security:Enabled` parametr konfigurace nastavený na hodnotu `true` a všechny požadavky (kromě `/metadata` ) na server FHIR musí mít jako `Authorization` hlavičku Request nastavenou na `Bearer <TOKEN>` . Token musí obsahovat jednu nebo více rolí, jak je definováno v `roles` deklaraci identity. Požadavek bude povolen, pokud token obsahuje roli, která umožňuje zadanou akci u zadaného prostředku.

V současné době se povolené akce pro danou roli aplikují *globálně* na rozhraní API.

## <a name="next-steps"></a>Další kroky

V tomto článku jste si přečetli informace o podporovaných funkcích FHIR v rozhraní Azure API pro FHIR. Dále nasaďte rozhraní Azure API pro FHIR.
 
>[!div class="nextstepaction"]
>[Nasazení Azure API for FHIR](fhir-paas-portal-quickstart.md)
