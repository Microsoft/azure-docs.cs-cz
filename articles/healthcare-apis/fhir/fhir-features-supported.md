---
title: Podporované funkce FHIR v Azure – Azure API pro FHIR
description: Tento článek vysvětluje, které funkce specifikace FHIR jsou implementované v Azure API pro FHIR.
services: healthcare-apis
author: caitlinv39
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 4/15/2021
ms.author: cavoeg
ms.openlocfilehash: 56e3ba46ffb43aec907d729a2e74cdf6f7a62c32
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2021
ms.locfileid: "107530630"
---
# <a name="features"></a>Funkce

Azure API for FHIR poskytuje plně spravované nasazení serveru Microsoft FHIR pro Azure. Server je implementací standardu [FHIR](https://hl7.org/fhir) . Tento dokument obsahuje seznam hlavních funkcí serveru FHIR.

## <a name="fhir-version"></a>Verze FHIR

Nejnovější verze je podporována: `4.0.1`

Předchozí verze, které jsou aktuálně podporované, zahrnují: `3.0.2`

## <a name="rest-api"></a>Rozhraní REST API

| Rozhraní API                            | Podporováno – PaaS | Podporováno – OSS (SQL) | Podporované – OSS (Cosmos DB) | Komentář                                             |
|--------------------------------|-----------|-----------|-----------|-----------------------------------------------------|
| čtení                           | Yes       | Yes       | Yes       |                                                     |
| vread                          | Yes       | Yes       | Yes       |                                                     |
| update                         | Yes       | Yes       | Yes       |                                                     |
| aktualizace s optimistické uzamykání | Yes       | Yes       | Yes       |                                                     |
| aktualizovat (podmíněné)           | Yes       | Yes       | Yes       |                                                     |
| použita                          | No        | No        | No        |                                                     |
| delete                         | Yes       | Yes       | Yes       |  Viz poznámka níže.                                   |
| Odstranit (podmíněné)           | No        | No        | No        |                                                     |
| historie                        | Yes       | Yes       | Yes       |                                                     |
| vytvoření                         | Yes       | Yes       | Yes       | Podpora pro POST/PUT                               |
| vytvořit (podmíněné)           | Yes       | Yes       | Yes       | Problém [#1382](https://github.com/microsoft/fhir-server/issues/1382) |
| search                         | Částečné   | Částečné   | Částečné   | Viz část Hledat níže.                           |
| zřetězené hledání                 | Částečné       | Yes       | Částečné   | Viz Poznámka 2 níže.                                   |
| zpětné zřetězené hledání         | Částečné       | Yes       | Částečné   | Viz Poznámka 2 níže.                                   |
| možnosti                   | Yes       | Yes       | Yes       |                                                     |
| dávka                          | Yes       | Yes       | Yes       |                                                     |
| transakce                    | No        | Yes       | No        |                                                     |
| přenosu                         | Částečné   | Částečné   | Částečné   | `self` a `next` jsou podporovány                     |
| prostředníci                 | No        | No        | No        |                                                     |

> [!Note]
> Odstranění definované specifikací FHIR vyžaduje, aby po odstranění následující nepoužívané čtení prostředků vrátilo stavový kód HTTP 410 a prostředek se už nenalezne prostřednictvím vyhledávání. Rozhraní Azure API pro FHIR vám také umožňuje úplně odstranit (včetně veškeré historie) prostředku. K úplnému odstranění prostředku můžete předat nastavení parametru `hardDelete` na hodnotu true ( `DELETE {server}/{resource}/{id}?hardDelete=true` ). Pokud tento parametr nepředáte nebo nastavíte `hardDelete` na hodnotu false, budou historické verze prostředku stále k dispozici.


 **Poznámka 2**
* Přidává podporu MVP pro zřetězené a reverzní FHIR vyhledávání v CosmosDB. 

  V rozhraní Azure API pro FHIR a Open Source Server FHIR, který je založený na Cosmos, je hledání pomocí zřetězeného a zpětného zřetězeného vyhledávání v rámci implementace MVP. Chcete-li provést zřetězené hledání na Cosmos DB, implementace provede dotaz na hledání a vystaví poddotazy k vyřešení odpovídajících prostředků. To se provádí pro každou úroveň výrazu. Pokud libovolný dotaz vrátí více než 100 výsledků, bude vyvolána chyba. Ve výchozím nastavení je zřetězené hledání za příznakem funkce. Chcete-li použít zřetězené hledání na Cosmos DB, použijte záhlaví `x-ms-enable-chained-search: true` . Další podrobnosti najdete v tématu [PR 1695](https://github.com/microsoft/fhir-server/pull/1695).

## <a name="search"></a>Hledat

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
|`:[type]` odkaz  | Yes       | Yes       | Yes       |         |
|`:not`                 | Yes       | Yes       | Yes       |         |
|`:below` identifikátor URI         | Yes       | Yes       | Yes       |         |
|`:above` identifikátor URI         | No        | No        | No        | Problém [#158](https://github.com/Microsoft/fhir-server/issues/158) |
|`:in` klíčové          | No        | No        | No        |         |
|`:below` klíčové       | No        | No        | No        |         |
|`:above` klíčové       | No        | No        | No        |         |
|`:not-in` klíčové      | No        | No        | No        |         |

| Společný parametr hledání | Podporováno – PaaS | Podporováno – OSS (SQL) | Podporované – OSS (Cosmos DB) | Komentář |
|-------------------------| ----------| ----------| ----------|---------|
| `_id`                   | Yes       | Yes       | Yes       |         |
| `_lastUpdated`          | Yes       | Yes       | Yes       |         |
| `_tag`                  | Yes       | Yes       | Yes       |         |
| `_list`                 | Yes       | Yes       | Yes       |         |
| `_type`                 | Yes       | Yes       | Yes       | Problém [#1562](https://github.com/microsoft/fhir-server/issues/1562)        |
| `_security`             | Yes       | Yes       | Yes       |         |
| `_profile`              | Částečné   | Částečné   | Částečné   | Podporováno v STU3. Pokud jste databázi vytvořili **po** 20. únoru 2021, budete mít také podporu v R4. Pracujeme na povolení _profile databází vytvořených před 20. února 2021. |
| `_text`                 | No        | No        | No        |         |
| `_content`              | No        | No        | No        |         |
| `_has`                  | No        | No        | No        |         |
| `_query`                | No        | No        | No        |         |
| `_filter`               | No        | No        | No        |         |

| Parametry výsledků hledání | Podporováno – PaaS | Podporováno – OSS (SQL) | Podporované – OSS (Cosmos DB) | Komentář |
|-------------------------|-----------|-----------|-----------|---------|
| `_elements`             | Yes       | Yes       | Yes       | Problém [#1256](https://github.com/microsoft/fhir-server/issues/1256)        |
| `_count`                | Yes       | Yes       | Yes       | `_count` je omezen na 1000 znaků. Pokud je nastavena na hodnotu vyšší než 1000, bude vrácena pouze 1000 a v balíčku bude vráceno upozornění. |
| `_include`              | Yes       | Yes       | Yes       |Zahrnuté položky jsou omezeny na 100. Zahrnutí do PaaS a OSS v Cosmos DB nezahrnuje: iterace podpora.|
| `_revinclude`           | Yes       | Yes       | Yes       | Zahrnuté položky jsou omezeny na 100. Zahrnutí do PaaS a OSS v Cosmos DB [nezahrnuje: iterace podpora](https://github.com/microsoft/fhir-server/issues/1313). Problém [#1319](https://github.com/microsoft/fhir-server/issues/1319)|
| `_summary`              | Částečné   | Částečné   | Částečné   | `_summary=count` je podporováno |
| `_total`                | Částečné   | Částečné   | Částečné   | `_total=none` a `_total=accurate`      |
| `_sort`                 | Částečné   | Částečné   | Částečné   |   `_sort=_lastUpdated` je podporováno       |
| `_contained`            | No        | No        | No        |         |
| `containedType`         | No        | No        | No        |         |
| `_score`                | No        | No        | No        |         |

## <a name="extended-operations"></a>Rozšířené operace

Všechny podporované operace, které rozšiřuje rozhraní RESTful API.

| Typ parametru hledání | Podporováno – PaaS | Podporováno – OSS (SQL) | Podporované – OSS (Cosmos DB) | Komentář |
|------------------------|-----------|-----------|-----------|---------|
| $export (celý systém) | Yes       | Yes       | Yes       |         |
| Pacient/$export        | Yes       | Yes       | Yes       |         |
| Skupina/$export          | Yes       | Yes       | Yes       |         |
| $convert – data          | Yes       | Yes       | Yes       |         |


## <a name="persistence"></a>Dočasné

Server Microsoft FHIR má modul připojitelná Persistence (viz [`Microsoft.Health.Fhir.Core.Features.Persistence`](https://github.com/Microsoft/fhir-server/tree/master/src/Microsoft.Health.Fhir.Core/Features/Persistence) ).

V současné době je otevřený zdrojový kód FHIR serveru, který obsahuje implementaci [Azure Cosmos DB](../../cosmos-db/index-overview.md) a [SQL Database](https://azure.microsoft.com/services/sql-database/).

Cosmos DB je globálně distribuovaná databáze s více modely (rozhraní API SQL, rozhraní MongoDB API atd.). Podporuje různé [úrovně konzistence](../../cosmos-db/consistency-levels.md). Výchozí šablona nasazení konfiguruje server FHIR s `Strong` konzistencí, ale zásady konzistence je možné upravit (obecně zmírnit) na žádost pomocí `x-ms-consistency-level` hlavičky žádosti.

## <a name="role-based-access-control"></a>Řízení přístupu na základě role

Server FHIR používá pro řízení přístupu [Azure Active Directory](https://azure.microsoft.com/services/active-directory/) . Konkrétně se vynutilo řízení přístupu na základě role (RBAC), pokud je `FhirServer:Security:Enabled` parametr konfigurace nastavený na hodnotu `true` a všechny požadavky (s výjimkou `/metadata` ) serveru FHIR musí mít `Authorization` nastavenou hlavičku Request nastavenou na `Bearer <TOKEN>` . Token musí obsahovat jednu nebo více rolí, jak je definováno v `roles` deklaraci identity. Požadavek bude povolen, pokud token obsahuje roli, která umožňuje zadanou akci u zadaného prostředku.

V současné době se povolené akce pro danou roli aplikují *globálně* na rozhraní API.

## <a name="service-limits"></a>Omezení služby

* [**Jednotky žádosti (ru)**](../../cosmos-db/concepts-limits.md) – na portálu Azure API pro FHIR můžete nakonfigurovat až 10 000 ru. Budete potřebovat minimálně 400 ru nebo 40 ru/GB, podle toho, co je větší. Pokud potřebujete více než 10 000 ru, můžete umístit do lístku podpory, aby se to zvýšilo. Maximální dostupná hodnota je 1 000 000.

* **Souběžná připojení** a **instance** – ve výchozím nastavení máte pět souběžných připojení ke dvěma instancím v clusteru (celkem 10 souběžných požadavků). Pokud se domníváte, že potřebujete víc souběžných žádostí, otevřete lístek podpory s podrobnostmi o vašich potřebách.

* **Velikost sady prostředků** – jednotlivé sady jsou omezené na 500 položek.

* **Velikost dat** – data/dokumenty musí být mírně méně než 2 MB.

## <a name="performance-expectations"></a>Očekávání výkonu

Výkon systému závisí na počtu ru, souběžných připojeních a typu operací, které provádíte (Put, post atd.). Níže jsou uvedeny některé obecné rozsahy, co můžete očekávat na základě nakonfigurovaných ru. Obecně platí, že se výkon škáluje lineárně s nárůstem ru:

| počet ru | Prostředky/s |    Maximální velikost úložiště (GB) *    |
|----------|---------------|--------|                 
| 400      | 5-10          |     10   |
| 1 000    | 100-150       |      25  |
| 10 000   | 225-400       |      250  |
| 100 000  | 2 500 – 4000   |      2,500  |

Poznámka: požadavek na Cosmos DB vyžaduje minimální propustnost 40 RU/s za GB úložiště. 

## <a name="next-steps"></a>Další kroky

V tomto článku jste si přečetli informace o podporovaných funkcích FHIR v rozhraní Azure API pro FHIR. Dále nasaďte rozhraní Azure API pro FHIR.
 
>[!div class="nextstepaction"]
>[Nasazení Azure API for FHIR](fhir-paas-portal-quickstart.md)
