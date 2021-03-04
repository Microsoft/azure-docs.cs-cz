---
title: Podporované funkce FHIR v Azure – Azure API pro FHIR
description: Tento článek vysvětluje, které funkce specifikace FHIR jsou implementované v Azure API pro FHIR.
services: healthcare-apis
author: caitlinv39
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 1/30/2021
ms.author: cavoeg
ms.openlocfilehash: 19f051320aaa675ebe5ff148fb6580c2a5d8770c
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/03/2021
ms.locfileid: "101719130"
---
# <a name="features"></a>Funkce

Azure API for FHIR poskytuje plně spravované nasazení serveru Microsoft FHIR pro Azure. Server je implementací standardu [FHIR](https://hl7.org/fhir) . Tento dokument obsahuje seznam hlavních funkcí serveru FHIR.

## <a name="fhir-version"></a>Verze FHIR

Nejnovější verze je podporována: `4.0.1`

Předchozí verze, které jsou aktuálně podporované, zahrnují: `3.0.2`

## <a name="rest-api"></a>REST API

| Rozhraní API                            | Podporováno – PaaS | Podporováno – OSS (SQL) | Podporované – OSS (Cosmos DB) | Komentář                                             |
|--------------------------------|-----------|-----------|-----------|-----------------------------------------------------|
| čtení                           | Ano       | Ano       | Ano       |                                                     |
| vread                          | Ano       | Ano       | Ano       |                                                     |
| update                         | Ano       | Ano       | Ano       |                                                     |
| aktualizace s optimistické uzamykání | Ano       | Ano       | Ano       |                                                     |
| aktualizovat (podmíněné)           | Ano       | Ano       | Ano       |                                                     |
| použita                          | Ne        | Ne        | Ne        |                                                     |
| delete                         | Ano       | Ano       | Ano       |  Viz poznámka níže.                                                   |
| Odstranit (podmíněné)           | Ne        | Ne        | Ne        |                                                     |
| historie                        | Ano       | Ano       | Ano       |                                                     |
| vytvoření                         | Ano       | Ano       | Ano       | Podpora pro POST/PUT                               |
| vytvořit (podmíněné)           | Ano       | Ano       | Ano       | Problém [#1382](https://github.com/microsoft/fhir-server/issues/1382) |
| search                         | Částečné   | Částečné   | Částečné   | Viz níže                                           |
| zřetězené hledání                 | Ne        | Ano       | Ne        |                                                     |
| zpětné zřetězené hledání         | Ne        | Ano       | Ne        |                                                     |
| možnosti                   | Ano       | Ano       | Ano       |                                                     |
| dávka                          | Ano       | Ano       | Ano       |                                                     |
| transakce                    | Ne        | Ano       | Ne        |                                                     |
| přenosu                         | Částečné   | Částečné   | Částečné   | `self` a `next` jsou podporovány                     |
| prostředníci                 | Ne        | Ne        | Ne        |                                                     |

> [!Note]
> Odstranění definované specifikací FHIR vyžaduje, aby po odstranění následující nepoužívané čtení prostředků vrátilo stavový kód HTTP 410 a prostředek se už nenalezne prostřednictvím vyhledávání. Rozhraní Azure API pro FHIR vám také umožňuje úplně odstranit (včetně veškeré historie) prostředku. K úplnému odstranění prostředku můžete předat nastavení parametru `hardDelete` na hodnotu true ( `DELETE {server}/{resource}/{id}?hardDelete=true` ). Pokud tento parametr nepředáte nebo nastavíte `hardDelete` na hodnotu false, budou historické verze prostředku stále k dispozici.

## <a name="search"></a>Search

Všechny typy parametrů vyhledávání jsou podporovány. 

| Typ parametru hledání | Podporováno – PaaS | Podporováno – OSS (SQL) | Podporované – OSS (Cosmos DB) | Komentář |
|-----------------------|-----------|-----------|-----------|---------|
| Číslo                | Ano       | Ano       | Ano       |         |
| Datum/datum a čas         | Ano       | Ano       | Ano       |         |
| Řetězec                | Ano       | Ano       | Ano       |         |
| Token                 | Ano       | Ano       | Ano       |         |
| Reference             | Ano       | Ano       | Ano       |         |
| Složený             | Ano       | Ano       | Ano       |         |
| Množství              | Ano       | Ano       | Ano       |         |
| Identifikátor URI                   | Ano       | Ano       | Ano       |         |
| Speciální               | Ne        | Ne        | Ne        |         |


| Modifikátory             | Podporováno – PaaS | Podporováno – OSS (SQL) | Podporované – OSS (Cosmos DB) | Komentář |
|-----------------------|-----------|-----------|-----------|---------|
|`:missing`             | Ano       | Ano       | Ano       |         |
|`:exact`               | Ano       | Ano       | Ano       |         |
|`:contains`            | Ano       | Ano       | Ano       |         |
|`:text`                | Ano       | Ano       | Ano       |         |
|`:[type]` odkaz  | Ano       | Ano       | Ano       |         |
|`:not`                 | Ano       | Ano       | Ano       |         |
|`:below` identifikátor URI         | Ano       | Ano       | Ano       |         |
|`:above` identifikátor URI         | Ne        | Ne        | Ne        | Problém [#158](https://github.com/Microsoft/fhir-server/issues/158) |
|`:in` klíčové          | Ne        | Ne        | Ne        |         |
|`:below` klíčové       | Ne        | Ne        | Ne        |         |
|`:above` klíčové       | Ne        | Ne        | Ne        |         |
|`:not-in` klíčové      | Ne        | Ne        | Ne        |         |

| Společný parametr hledání | Podporováno – PaaS | Podporováno – OSS (SQL) | Podporované – OSS (Cosmos DB) | Komentář |
|-------------------------| ----------| ----------| ----------|---------|
| `_id`                   | Ano       | Ano       | Ano       |         |
| `_lastUpdated`          | Ano       | Ano       | Ano       |         |
| `_tag`                  | Ano       | Ano       | Ano       |         |
| `_list`                 | Ano       | Ano       | Ano       |         |
| `_type`                 | Ano       | Ano       | Ano       | Problém [#1562](https://github.com/microsoft/fhir-server/issues/1562)        |
| `_security`             | Ano       | Ano       | Ano       |         |
| `_profile`              | Částečné   | Částečné   | Částečné   | Podporováno v STU3. Pokud jste databázi vytvořili **po** 20. únoru 2021, budete mít také podporu v R4. Pracujeme na povolení _profile databází vytvořených před 20. února 2021. |
| `_text`                 | Ne        | Ne        | Ne        |         |
| `_content`              | Ne        | Ne        | Ne        |         |
| `_has`                  | Ne        | Ne        | Ne        |         |
| `_query`                | Ne        | Ne        | Ne        |         |
| `_filter`               | Ne        | Ne        | Ne        |         |

| Parametry výsledků hledání | Podporováno – PaaS | Podporováno – OSS (SQL) | Podporované – OSS (Cosmos DB) | Komentář |
|-------------------------|-----------|-----------|-----------|---------|
| `_elements`             | Ano       | Ano       | Ano       | Problém [#1256](https://github.com/microsoft/fhir-server/issues/1256)        |
| `_count`                | Ano       | Ano       | Ano       | `_count` je omezen na 1000 znaků. Pokud je nastavena na hodnotu vyšší než 1000, bude vrácena pouze 1000 a v balíčku bude vráceno upozornění. |
| `_include`              | Ano       | Ano       | Ano       |Zahrnuté položky jsou omezeny na 100. Zahrnutí do PaaS a OSS v Cosmos DB nezahrnuje: iterace podpora.|
| `_revinclude`           | Ano       | Ano       | Ano       | Zahrnuté položky jsou omezeny na 100. Zahrnutí do PaaS a OSS v Cosmos DB [nezahrnuje: iterace podpora](https://github.com/microsoft/fhir-server/issues/1313). Problém [#1319](https://github.com/microsoft/fhir-server/issues/1319)|
| `_summary`              | Částečné   | Částečné   | Částečné   | `_summary=count` je podporováno |
| `_total`                | Částečné   | Částečné   | Částečné   | `_total=none` a `_total=accurate`      |
| `_sort`                 | Částečné   | Částečné   | Částečné   |   `_sort=_lastUpdated` je podporováno       |
| `_contained`            | Ne        | Ne        | Ne        |         |
| `containedType`         | Ne        | Ne        | Ne        |         |
| `_score`                | Ne        | Ne        | Ne        |         |

## <a name="extended-operations"></a>Rozšířené operace

Všechny podporované operace, které rozšiřuje rozhraní RESTful API.

| Typ parametru hledání | Podporováno – PaaS | Podporováno – OSS (SQL) | Podporované – OSS (Cosmos DB) | Komentář |
|------------------------|-----------|-----------|-----------|---------|
| $export (celý systém) | Ano       | Ano       | Ano       |         |
| Pacient/$export        | Ano       | Ano       | Ano       |         |
| Skupina/$export          | Ano       | Ano       | Ano       |         |
| $convert – data          | Ano       | Ano       | Ano       |         |


## <a name="persistence"></a>Dočasné

Server Microsoft FHIR má modul připojitelná Persistence (viz [`Microsoft.Health.Fhir.Core.Features.Persistence`](https://github.com/Microsoft/fhir-server/tree/master/src/Microsoft.Health.Fhir.Core/Features/Persistence) ).

V současné době je otevřený zdrojový kód FHIR serveru, který obsahuje implementaci [Azure Cosmos DB](../cosmos-db/index-overview.md) a [SQL Database](https://azure.microsoft.com/services/sql-database/).

Cosmos DB je globálně distribuovaná databáze s více modely (rozhraní API SQL, rozhraní MongoDB API atd.). Podporuje různé [úrovně konzistence](../cosmos-db/consistency-levels.md). Výchozí šablona nasazení konfiguruje server FHIR s `Strong` konzistencí, ale zásady konzistence je možné upravit (obecně zmírnit) na žádost pomocí `x-ms-consistency-level` hlavičky žádosti.

## <a name="role-based-access-control"></a>Řízení přístupu na základě role

Server FHIR používá pro řízení přístupu [Azure Active Directory](https://azure.microsoft.com/services/active-directory/) . Konkrétně se vynutilo řízení přístupu na základě role (RBAC), pokud je `FhirServer:Security:Enabled` parametr konfigurace nastavený na hodnotu `true` a všechny požadavky (s výjimkou `/metadata` ) serveru FHIR musí mít `Authorization` nastavenou hlavičku Request nastavenou na `Bearer <TOKEN>` . Token musí obsahovat jednu nebo více rolí, jak je definováno v `roles` deklaraci identity. Požadavek bude povolen, pokud token obsahuje roli, která umožňuje zadanou akci u zadaného prostředku.

V současné době se povolené akce pro danou roli aplikují *globálně* na rozhraní API.

## <a name="service-limits"></a>Omezení služby

* [**Jednotky žádosti (ru)**](../cosmos-db/concepts-limits.md) – na portálu Azure API pro FHIR můžete nakonfigurovat až 10 000 ru. Budete potřebovat minimálně 400 ru nebo 10 ru/GB, podle toho, co je větší. Pokud potřebujete více než 10 000 ru, můžete umístit do lístku podpory, aby se to zvýšilo. Maximální dostupná hodnota je 1 000 000.

* **Souběžná připojení** a **instance** – ve výchozím nastavení máte pět souběžných připojení ke dvěma instancím v clusteru (celkem 10 souběžných požadavků). Pokud se domníváte, že potřebujete víc souběžných žádostí, otevřete lístek podpory s podrobnostmi o vašich potřebách.

* **Velikost sady prostředků** – jednotlivé sady jsou omezené na 500 položek.

* **Velikost dat** – data/dokumenty musí být mírně méně než 2 MB.

## <a name="performance-expectations"></a>Očekávání výkonu

Výkon systému závisí na počtu ru, souběžných připojeních a typu operací, které provádíte (Put, post atd.). Níže jsou uvedeny některé obecné rozsahy, co můžete očekávat na základě nakonfigurovaných ru. Obecně platí, že se výkon škáluje lineárně s nárůstem ru:

| počet ru | Prostředky/s |    Maximální velikost úložiště (GB) *    |
|----------|---------------|--------|                 
| 400      | 5-10          |     40   |
| 1 000    | 100-150       |      100  |
| 10 000   | 225-400       |      1 000  |
| 100 000  | 2 500 – 4000   |      10 000  |

Poznámka: požadavek na Cosmos DB vyžaduje minimální propustnost 10 RU/s za GB úložiště. Další informace najdete v [Cosmos DB kvótách služeb](../cosmos-db/concepts-limits.md).

## <a name="next-steps"></a>Další kroky

V tomto článku jste si přečetli informace o podporovaných funkcích FHIR v rozhraní Azure API pro FHIR. Dále nasaďte rozhraní Azure API pro FHIR.
 
>[!div class="nextstepaction"]
>[Nasazení Azure API for FHIR](fhir-paas-portal-quickstart.md)
