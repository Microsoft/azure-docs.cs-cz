---
title: Rozdíly mezi funkcemi T-SQL v Azure Synapse SQL
description: Seznam funkcí Transact-SQL, které jsou k dispozici v Synapse SQL.
services: synapse analytics
author: jovanpop-msft
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: jovanpop
ms.reviewer: jrasnick
ms.openlocfilehash: b3e4d705bea7243966959038cf15373097c73ebc
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81424892"
---
# <a name="transact-sql-features-supported-in-azure-synapse-sql"></a>Funkce Transact-SQL podporované v Azure Synapse SQL

Azure Synapse SQL je analytická služba pro velké objemy dat, která umožňuje dotazovat a analyzovat data pomocí jazyka T-SQL. Pro analýzu dat můžete použít standardní dialekt jazyka SQL kompatibilní s ANSI používaný na SQL Serveru a Azure SQL Database. 

Transact-SQL jazyk se používá v Synapse SQL serverless a zřízeného modelu můžete odkazovat na různé objekty a má některé rozdíly v sadě podporovaných funkcí. Na této stránce naleznete rozdíly v jazycích Transact-SQL na vysoké úrovni mezi modely spotřeby Synapse SQL.

## <a name="database-objects"></a>Databázové objekty

Modely spotřeby v Synapse SQL umožňují používat různé databázové objekty. Porovnání podporovaných typů objektů je uvedeno v následující tabulce:

|   | Zřízena | Bez serveru |
| --- | --- | --- |
| Tabulky | [Ano](/sql/t-sql/statements/create-table-azure-sql-data-warehouse?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) | Ne, model bez serveru může dotazovat jenom externí data umístěná ve [službě Azure Storage.](#storage-options) |
| Zobrazení | [Ano.](/sql/t-sql/statements/create-view-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) Zobrazení můžete použít [prvky jazyka dotazu,](#query-language) které jsou k dispozici v zřízeného modelu. | [Ano.](/sql/t-sql/statements/create-view-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) Zobrazení můžete použít [prvky jazyka dotazu,](#query-language) které jsou k dispozici v modelu bez serveru. |
| Schémata | [Ano](/sql/t-sql/statements/create-schema-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) | [Ano](/sql/t-sql/statements/create-schema-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) |
| Dočasné tabulky | [Ano](../sql-data-warehouse/sql-data-warehouse-tables-temporary.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) | Ne |
| Procedury | [Ano](/sql/t-sql/statements/create-procedure-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) | Ne |
| Functions | [Ano](/sql/t-sql/statements/create-function-sql-data-warehouse?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) | Ne |
| Aktivační události | Ne | Ne |
| Externí tabulky | [Ano.](/sql/t-sql/statements/create-external-table-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) Viz podporované [formáty dat](#data-formats). | [Ano.](/sql/t-sql/statements/create-external-table-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) Viz podporované [formáty dat](#data-formats). |
| Ukládání dotazů do mezipaměti | Ano, více formulářů (ukládání do mezipaměti na základě SSD, ukládání do mezipaměti v paměti, resultset). Kromě toho jsou podporovány materializované zobrazení | Ne |
| Proměnné tabulky | [Ne](/sql/t-sql/data-types/table-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), použijte dočasné tabulky | Ne |

## <a name="query-language"></a>Dotazovací jazyk

Dotazovací jazyky používané v Synapse SQL mohou mít různé podporované funkce v závislosti na modelu spotřeby. V následující tabulce jsou uvedeny nejdůležitější rozdíly v jazycích Transact-SQL:

|   | Zřízena | Bez serveru |
| --- | --- | --- |
| Příkaz SELECT | Ano. Klauzule dotazu Transact-SQL [PRO XML/FOR JSON](/sql/t-sql/queries/select-for-clause-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), [MATCH](/sql/t-sql/queries/match-sql-graph?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) a [PREDICT](/sql/t-sql/queries/predict-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) nejsou podporovány. | Ano. Klauzule dotazu Transact-SQL [pro xml](/sql/t-sql/queries/select-for-clause-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), [match](/sql/t-sql/queries/match-sql-graph?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), [predict](/sql/t-sql/queries/predict-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)a rady při psaní dotazu nejsou podporovány. [POSUN/FETCH](/sql/t-sql/queries/select-order-by-clause-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest#using-offset-and-fetch-to-limit-the-rows-returned) a [PIVOT/UNPIVOT](/sql/t-sql/queries/from-using-pivot-and-unpivot?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) lze použít pouze k dotazování dat v dočasných tabulkách (nikoli externídata). |
| Příkaz INSERT | Ano | Ne |
| Příkaz UPDATE | Ano | Ne |
| Příkaz DELETE | Ano | Ne |
| Příkaz MERGE | Ano | Ne |
| Načítání dat | Ano. Upřednostňovaný nástroj je [příkaz COPY,](/sql/t-sql/statements/copy-into-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) ale systém podporuje hromadné zatížení (BCP) a [CETAS](/sql/t-sql/statements/create-external-table-as-select-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) pro načítání dat. | Ne |
| Export dat | Ano. Pomocí [CETAS](/sql/t-sql/statements/create-external-table-as-select-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest). | Ano. Pomocí [CETAS](/sql/t-sql/statements/create-external-table-as-select-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest). |
| Typy | Ano, všechny typy Transact-SQL kromě [kurzoru](/sql/t-sql/data-types/cursor-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), [hierarchyid](/sql/t-sql/data-types/hierarchyid-data-type-method-reference?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), [ntext, text a obrázek](/sql/t-sql/data-types/ntext-text-and-image-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), [rowversion](/sql/t-sql/data-types/rowversion-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), [Spatial Types](/sql/t-sql/spatial-geometry/spatial-types-geometry-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), [SQL\_variant](/sql/t-sql/data-types/sql-variant-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)a [xml](/sql/t-sql/xml/xml-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) | Ano, všechny typy Transact-SQL kromě [kurzoru](/sql/t-sql/data-types/cursor-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), [hierarchyid](/sql/t-sql/data-types/hierarchyid-data-type-method-reference?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), [ntext, text a obrázek](/sql/t-sql/data-types/ntext-text-and-image-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), [rowversion](/sql/t-sql/data-types/rowversion-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), [Spatial Types](/sql/t-sql/spatial-geometry/spatial-types-geometry-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), [SQL\_variant](/sql/t-sql/data-types/sql-variant-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), [xml](/sql/t-sql/xml/xml-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)a table type |
| Mezidatabázové dotazy | Ne | Ano, včetně [příkazu USE.](/sql/t-sql/language-elements/use-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) |
| Vestavěné funkce (analýza) | Ano, všechny [analýzy](/sql/t-sql/functions/analytic-functions-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)Transact-SQL , konverze, [datum a čas](/sql/t-sql/functions/date-and-time-data-types-and-functions-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), logické, [matematické](/sql/t-sql/functions/mathematical-functions-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) funkce, s výjimkou [CHOOSE](/sql/t-sql/functions/logical-functions-choose-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), [IIF](/sql/t-sql/functions/logical-functions-iif-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)a [PARSE](/sql/t-sql/functions/parse-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) | Ano, všechny Transact-SQL [Analytic](/sql/t-sql/functions/analytic-functions-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), Konverze, [Datum a čas](/sql/t-sql/functions/date-and-time-data-types-and-functions-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), Logické, [Matematické](/sql/t-sql/functions/mathematical-functions-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) funkce. |
| Vestavěné funkce (text) | Ano. Všechny funkce Transact-SQL [String](/sql/t-sql/functions/string-functions-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), [JSON](/sql/t-sql/functions/json-functions-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)a Collation, s výjimkou [STRING_ESCAPE](/sql/t-sql/functions/string-escape-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) a [TRANSLATE](/sql/t-sql/functions/translate-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) | Ano. Všechny funkce Transact-SQL [String](/sql/t-sql/functions/string-functions-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), [JSON](/sql/t-sql/functions/json-functions-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)a Collation. |
| Vestavěné funkce s hodnotou stolu | Ano, [funkce Sady řádků Transact-SQL](/sql/t-sql/functions/functions?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest#rowset-functions), kromě [OPENXML](/sql/t-sql/functions/openxml-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), [OPENDATASOURCE](/sql/t-sql/functions/opendatasource-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), [OPENQUERY](/sql/t-sql/functions/openquery-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)a [OPENROWSET](/sql/t-sql/functions/openrowset-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) | Ano, [funkce Sady řádků Transact-SQL](/sql/t-sql/functions/functions?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest#rowset-functions), s výjimkou [OPENXML](/sql/t-sql/functions/openxml-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), [OPENDATASOURCE](/sql/t-sql/functions/opendatasource-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)a [OPENQUERY](/sql/t-sql/functions/openquery-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)  |
| Agregace |  Transact-SQL vestavěné agregáty s výjimkou, s výjimkou [CHECKSUM_AGG](/sql/t-sql/functions/checksum-agg-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) a [GROUPING_ID](/sql/t-sql/functions/grouping-id-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) | Transact-SQL vestavěné agregáty s výjimkou [\_STRING AGG](/sql/t-sql/functions/string-agg-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) |
| Operátory | Ano, všechny [operátory Transact-SQL](/sql/t-sql/language-elements/operators-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) kromě [!>](/sql/t-sql/language-elements/not-greater-than-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) a [!<](/sql/t-sql/language-elements/not-less-than-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) | Ano, všechny [operátory Transact-SQL](/sql/t-sql/language-elements/operators-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)  |
| Řízení průtoku | Ano. Všechny [příkazy Transact-SQL Control-of-flow](/sql/t-sql/language-elements/control-of-flow?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) kromě [continue](/sql/t-sql/language-elements/continue-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), [GOTO](/sql/t-sql/language-elements/goto-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), [RETURN](/sql/t-sql/language-elements/return-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), [USE](/sql/t-sql/language-elements/use-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)a [WAITFOR](/sql/t-sql/language-elements/waitfor-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) | Ano. Všechny [příkazy Transact-SQL Control-of-flow](/sql/t-sql/language-elements/control-of-flow?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) s `WHILE (...)` výjimkou dotazu [RETURN](/sql/t-sql/language-elements/return-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) a SELECT v podmínce |
| DDL příkazy (CREATE, ALTER, DROP) | Ano. Všechny Příkazy Transact-SQL DDL použitelné pro podporované typy objektů | Ano. Všechny Příkazy Transact-SQL DDL použitelné pro podporované typy objektů |

## <a name="security"></a>Zabezpečení

Synapse SQL umožňuje používat integrované funkce zabezpečení k zabezpečení dat a řízení přístupu. Následující tabulka porovnává rozdíly na vysoké úrovni mezi modely spotřeby SYNApse SQL.

|   | Zřízena | Bez serveru |
| --- | --- | --- |
| Přihlášení | Není k tomu k onomu (v databázích jsou podporováni pouze mitovaní uživatelé) | Ano |
| Uživatelé |  Není k tomu k onomu (v databázích jsou podporováni pouze mitovaní uživatelé) | Ano |
| [Uživatelé s omezením](/sql/relational-databases/security/contained-database-users-making-your-database-portable?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) | Ano. **Poznámka:** pouze jeden uživatel AAD může být neomezený admin | Ano |
| Ověřování služby Azure Active Directory (AAD) | Ano, uživatelé AAD | Ano, přihlášení a uživatelé a uživatelé a disponovat a |
| Ověření průchodu aad úložiště | Ano | Ano |
| Ověřování tokenů SAS úložiště | Ne | Ano, použití [pověření s oborem databáze](/sql/t-sql/statements/create-database-scoped-credential-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) v [externím zdroji dat](/sql/t-sql/statements/create-external-data-source-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) nebo [pověření](/sql/t-sql/statements/create-credential-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)na úrovni instance . |
| Ověřování přístupového klíče úložiště | Ano, použití [pověření oboru databáze](/sql/t-sql/statements/create-database-scoped-credential-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) v [externím zdroji dat](/sql/t-sql/statements/create-external-data-source-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) | Ne |
| Ověřování spravované identity úložiště | Ano, použití [pověření identity spravované služby](../../sql-database/sql-database-vnet-service-endpoint-rule-overview.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) | Ano, `Managed Identity` pomocí pověření. |
| Ověřování identity aplikace úložiště | [Ano](/sql/t-sql/statements/create-external-data-source-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) | Ne |
| Oprávnění – úroveň objektu | Ano, včetně možnosti udělit, odepřít a odvolat oprávnění pro uživatele | Ano, včetně možnosti udělit, odepřít a odvolat oprávnění uživatelům/přihlášení u podporovaných systémových objektů |
| Oprávnění – úroveň schématu | Ano, včetně možnosti udělit, odepřít a odvolat oprávnění pro uživatele/přihlášení ve schématu | Ano, včetně možnosti udělit, odepřít a odvolat oprávnění pro uživatele/přihlášení ve schématu |
| Oprávnění – [úroveň databáze](/sql/relational-databases/security/authentication-access/database-level-roles?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) | Ano | Ano |
| Oprávnění – [úroveň serveru](/sql/relational-databases/security/authentication-access/server-level-roles) | Ne | Ano, sysadmin a další role serveru jsou podporovány |
| Role/skupiny | Ano (s rozsahem databáze) | Ano (server i databáze s vymezenou souplní) |
| Funkce &amp; identity zabezpečení | Některé bezpečnostní funkce a operátory `CURRENT_USER` `HAS_DBACCESS`Transact-SQL: `SUSER_SNAME` `SYSTEM_USER`, `USER` `USER_NAME`, `EXECUTE AS` `IS_MEMBER` `IS_ROLEMEMBER`, `SESSION_USER`, `SUSER_NAME`, , , , , , , ,`OPEN/CLOSE MASTER KEY` | Některé `CURRENT_USER`funkce zabezpečení transact-SQL: `HAS_DBACCESS` `HAS_PERMS_BY_NAME`, `IS_MEMBER', 'IS_ROLEMEMBER` `IS_SRVROLEMEMBER`, `SESSION_USER` `SUSER_NAME`, `SUSER_SNAME` `SYSTEM_USER`, `USER` `USER_NAME`, `EXECUTE AS`, `REVERT`, , , , , , a . Funkce zabezpečení nelze použít k dotazování externích dat (uložte výsledek do proměnné, kterou lze použít v dotazu).  |
| Pověření oboru databáze | Ano | Ano |

Fond SQL a SQL na vyžádání používají k dotazování na data standardní jazyk Transact-SQL. Podrobné rozdíly naleznete v [referenční příručce jazyka Transact-SQL](/sql/t-sql/language-reference).

## <a name="tools"></a>nástroje

K dotazování dat můžete použít různé nástroje pro připojení k synapse SQL.

|   | Zřízena | Bez serveru |
| --- | --- | --- |
| Synapse Studio | Ano, skripty SQL | Ano, skripty SQL |
| Power BI | Ano | [Ano](tutorial-connect-power-bi-desktop.md) |
| Azure Analysis Service | Ano | Ano |
| Azure Data Studio | Ano | Ano, verze 1.14 nebo vyšší. Jsou podporovány skripty SQL a poznámkové bloky SQL. |
| SQL Server Management Studio | Ano | Ano, verze 18.4 nebo vyšší |

Většina aplikací používá standardní jazyk Transact-SQL, který může dotazovat jak zřízené, tak bezserverové modely spotřeby synapse SQL.

## <a name="storage-options"></a>Možnosti úložiště

Analyzovaná data mohou být uložena na různých typech úložišť. V následující tabulce jsou uvedeny všechny dostupné možnosti úložiště:

|   | Zřízena | Bez serveru |
| --- | --- | --- |
| Interní úložiště | Ano | Ne |
| Azure Data Lake v2 | Ano | Ano |
| Azure Blob Storage | Ano | Ano |

## <a name="data-formats"></a>Formáty dat

Analyzovaná data mohou být uložena v různých formátech úložiště. V následující tabulce jsou uvedeny všechny dostupné formáty dat, které lze analyzovat:

|   | Zřízena | Bez serveru |
| --- | --- | --- |
| Oddělený | [Ano](/sql/t-sql/statements/create-external-file-format-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) | [Ano](query-single-csv-file.md) |
| CSV | Ano (oddělovače s více znaky nejsou podporovány) | [Ano](query-single-csv-file.md) |
| Parketové | [Ano](/sql/t-sql/statements/create-external-file-format-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) | [Ano](query-parquet-files.md), včetně souborů s [vnořenými typy](query-parquet-nested-types.md) |
| Úl ORC | [Ano](/sql/t-sql/statements/create-external-file-format-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) | Ne |
| Úl RC | [Ano](/sql/t-sql/statements/create-external-file-format-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) | Ne |
| JSON | Ano | [Ano](query-json-files.md) |
| [Delta-jezero](https://delta.io/) | Ne | Ne |
| [Cdm](https://docs.microsoft.com/common-data-model/) | Ne | Ne |

## <a name="next-steps"></a>Další kroky
Další informace o doporučených postupech pro fond SQL a SQL na vyžádání naleznete v následujících článcích:

- [Doporučené postupy pro fond SQL](best-practices-sql-pool.md)
- [Doporučené postupy pro SQL na vyžádání](best-practices-sql-on-demand.md)