---
title: Podporované funkce FHIR v Azure – Azure API pro FHIR
description: Tento článek vysvětluje, které funkce specifikace FHIR jsou implementované v Azure API pro FHIR.
services: healthcare-apis
author: hansenms
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 02/07/2019
ms.author: mihansen
ms.openlocfilehash: 7f92395f19d84f904493af458d1334f8013fd263
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "85808025"
---
# <a name="features"></a>Funkce

Azure API for FHIR poskytuje plně spravované nasazení serveru Microsoft FHIR pro Azure. Server je implementací standardu [FHIR](https://hl7.org/fhir) . Tento dokument obsahuje seznam hlavních funkcí serveru FHIR.

## <a name="fhir-version"></a>Verze FHIR

Nejnovější verze je podporována:`4.0.1`

Předchozí verze, které jsou aktuálně podporované, zahrnují:`3.0.2`

## <a name="rest-api"></a>REST API

| Rozhraní API                            | Podporováno – PaaS | Podporováno – OSS (SQL) | Podporované – OSS (Cosmos DB) | Komentář                                             |
|--------------------------------|-----------|-----------|-----------|-----------------------------------------------------|
| read                           | Ano       | Ano       | Ano       |                                                     |
| vread                          | Ano       | Ano       | Ano       |                                                     |
| update                         | Ano       | Ano       | Ano       |                                                     |
| aktualizace s optimistické uzamykání | Ano       | Ano       | Ano       |                                                     |
| aktualizovat (podmíněné)           | Ano       | Ano       | Ano       |                                                     |
| použita                          | No        | No        | No        |                                                     |
| delete                         | Ano       | Ano       | Ano       |                                                     |
| Odstranit (podmíněné)           | No        | No        | No        |                                                     |
| vytvoření                         | Ano       | Ano       | Ano       | Podpora pro POST/PUT                               |
| vytvořit (podmíněné)           | Ano       | Ano       | Ano       |                                                     |
| search                         | Částečné   | Částečné   | Částečné   | Viz níže                                           |
| zřetězené hledání                 | No        | Yes       | No        |                                           |
| zpětné zřetězené hledání         | No        | No        | No        |                                            |
| možnosti                   | Ano       | Ano       | Ano       |                                                     |
| dávka                          | Ano       | Ano       | Ano       |                                                     |
| transakce                    | No        | Yes       | No        |                                                     |
| historie                        | Ano       | Ano       | Ano       |                                                     |
| přenosu                         | Částečné   | Částečné   | Částečné   | `self`a `next` jsou podporovány                     |
| prostředníci                 | No        | No        | No        |                                                     |

## <a name="search"></a>Search

Všechny typy parametrů vyhledávání jsou podporovány. 

| Typ parametru hledání | Podporováno – PaaS | Podporováno – OSS (SQL) | Podporované – OSS (Cosmos DB) | Komentář |
|-----------------------|-----------|-----------|-----------|---------|
| Číslo                | Ano       | Ano       | Ano       |         |
| Datum/datum a čas         | Ano       | Ano       | Ano       |         |
| Řetězec                | Ano       | Ano       | Ano       |         |
| Token                 | Ano       | Ano       | Ano       |         |
| Referenční informace             | Ano       | Ano       | Ano       |         |
| Složený             | Ano       | Ano       | Ano       |         |
| Množství              | Ano       | Ano       | Ano       |         |
| Identifikátor URI                   | Ano       | Ano       | Ano       |         |
| Speciální               | No        | No        | No        |         |


| Modifikátory             | Podporováno – PaaS | Podporováno – OSS (SQL) | Podporované – OSS (Cosmos DB) | Komentář |
|-----------------------|-----------|-----------|-----------|---------|
|`:missing`             | Ano       | Ano       | Ano       |         |
|`:exact`               | Ano       | Ano       | Ano       |         |
|`:contains`            | Ano       | Ano       | Ano       |         |
|`:text`                | Ano       | Ano       | Ano       |         |
|`:in`klíčové          | No        | No        | No        |         |
|`:below`klíčové       | No        | No        | No        |         |
|`:above`klíčové       | No        | No        | No        |         |
|`:not-in`klíčové      | No        | No        | No        |         |
|`:[type]`odkaz  | No        | No        | No        |         |
|`:below`identifikátor URI         | Ano       | Ano       | Ano       |         |
|`:not`                 | No        | No        | No        |         |
|`:above`identifikátor URI         | No        | No        | No        | Problém [#158](https://github.com/Microsoft/fhir-server/issues/158) |

| Společný parametr hledání | Podporováno – PaaS | Podporováno – OSS (SQL) | Podporované – OSS (Cosmos DB) | Komentář |
|-------------------------| ----------| ----------| ----------|---------|
| `_id`                   | Ano       | Ano       | Ano       |         |
| `_lastUpdated`          | Ano       | Ano       | Ano       |         |
| `_tag`                  | Ano       | Ano       | Ano       |         |
| `_profile`              | Ano       | Ano       | Ano       |         |
| `_security`             | Ano       | Ano       | Ano       |         |
| `_text`                 | No        | No        | No        |         |
| `_content`              | No        | No        | No        |         |
| `_list`                 | No        | Ano       | Ano       |         |
| `_has`                  | No        | No        | No        |         |
| `_type`                 | Ano       | Ano       | Ano       |         |
| `_query`                | No        | No        | No        |         |

| Operace hledání       | Podporováno – PaaS | Podporováno – OSS (SQL) | Podporované – OSS (Cosmos DB) | Komentář |
|-------------------------|-----------|-----------|-----------|---------|
| `_filter`               | No        | No        | No        |         |
| `_sort`                 | No        | No        | No        |         |
| `_score`                | No        | No        | No        |         |
| `_count`                | Ano       | Ano       | Ano       |         |
| `_summary`              | Částečné   | Částečné   | Částečné   | `_summary=count`je podporováno |
| `_include`              | No        | Yes       | No        |         |
| `_revinclude`           | No        | No        | No        |         |
| `_contained`            | No        | No        | No        |         |
| `_elements`             | No        | No        | No        |         |

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
