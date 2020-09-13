---
title: Rozdíly ve funkcích T-SQL v synapse SQL
description: Seznam funkcí jazyka Transact-SQL, které jsou k dispozici v synapse SQL.
services: synapse analytics
author: jovanpop-msft
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: sql
ms.date: 04/15/2020
ms.author: jovanpop
ms.reviewer: jrasnick
ms.openlocfilehash: 54910aae3667d42ea5a6996d70d8989328711605
ms.sourcegitcommit: 3fc3457b5a6d5773323237f6a06ccfb6955bfb2d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/11/2020
ms.locfileid: "90033417"
---
# <a name="transact-sql-features-supported-in-azure-synapse-sql"></a>Funkce jazyka Transact-SQL podporované v Azure synapse SQL

Azure synapse SQL je analytická služba pro velké objemy dat, která umožňuje dotazování a analýzu dat pomocí jazyka T-SQL. Můžete použít standardní dialekt jazyka SQL kompatibilní se standardem ANSI, který se používá na SQL Server a Azure SQL Database pro analýzu dat. 

Jazyk Transact-SQL se používá v Synapsem a zřízeném modelu s SQL serverem se může odkazovat na různé objekty a v sadě podporovaných funkcí je několik rozdílů. Na této stránce můžete najít rozdíly v jazyce Transact-SQL ve velkém rozsahu mezi modely spotřeby synapse SQL.

## <a name="database-objects"></a>Objekty databáze

Modely spotřeby v synapse SQL umožňují použití různých databázových objektů. Porovnání podporovaných typů objektů je uvedeno v následující tabulce:

|   | Zřízené | Bez serveru |
| --- | --- | --- |
| **Tabulky** | [Ano](/sql/t-sql/statements/create-table-azure-sql-data-warehouse?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) | Ne, model bez serveru se může dotazovat jenom na externí data umístěná na [Azure Storage](#storage-options) |
| **Zobrazení** | [Ano](/sql/t-sql/statements/create-view-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest). Zobrazení mohou používat [prvky dotazovacího jazyka](#query-language) , které jsou k dispozici v zřízeném modelu. | [Ano](/sql/t-sql/statements/create-view-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest). Zobrazení mohou používat [prvky dotazovacího jazyka](#query-language) , které jsou k dispozici v modelu bez serveru. |
| **Schémata** | [Ano](/sql/t-sql/statements/create-schema-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) | [Ano](/sql/t-sql/statements/create-schema-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) |
| **Dočasné tabulky** | [Ano](../sql-data-warehouse/sql-data-warehouse-tables-temporary.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) | No |
| **Procedury** | [Ano](/sql/t-sql/statements/create-procedure-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) | No |
| **Functions** | [Ano](/sql/t-sql/statements/create-function-sql-data-warehouse?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) | Ano, vložené funkce vracející tabulku. |
| **Triggery** | No | No |
| **Externí tabulky** | [Ano](/sql/t-sql/statements/create-external-table-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest). Viz Podporované [formáty dat](#data-formats). | [Ano](/sql/t-sql/statements/create-external-table-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest). Viz Podporované [formáty dat](#data-formats). |
| **Ukládání dotazů do mezipaměti** | Ano, více forem (ukládání do mezipaměti založené na SSD, ukládání do mezipaměti sady výsledků v paměti). Kromě toho se podporují materializované zobrazení. | No |
| **Proměnné tabulky** | [Ne](/sql/t-sql/data-types/table-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), použít dočasné tabulky | No |
| **[Distribuce tabulky](../sql-data-warehouse/sql-data-warehouse-tables-distribute.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)**               | Yes | No |
| **[Indexy tabulek](../sql-data-warehouse/sql-data-warehouse-tables-index.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)**                           | Yes | No |
| **[Oddíly tabulky](../sql-data-warehouse/sql-data-warehouse-tables-partition.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)**                     | Yes | No |
| **[Statistika](develop-tables-statistics.md)**            | Yes | Yes |
| **[Správa úloh, třídy prostředků a řízení souběžnosti](../sql-data-warehouse/resource-classes-for-workload-management.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)** | Yes    | No |

## <a name="query-language"></a>Dotazovací jazyk

Jazyky dotazů používané v synapse SQL mohou mít různé podporované funkce v závislosti na modelu spotřeby. Následující tabulka popisuje nejdůležitější rozdíly v dotazovacím jazyku v dialektech jazyka Transact-SQL:

|   | Zřízené | Bez serveru |
| --- | --- | --- |
| **Příkaz SELECT** | Yes. Klauzule dotazu Transact-SQL [pro XML/for JSON](/sql/t-sql/queries/select-for-clause-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)a [porovnávání](/sql/t-sql/queries/match-sql-graph?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) nejsou podporovány. | Yes. Klauzule dotazu Transact-SQL [pro příkazy jazyka XML](/sql/t-sql/queries/select-for-clause-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), [porovnávání](/sql/t-sql/queries/match-sql-graph?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), [předpovědi](/sql/t-sql/queries/predict-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)a dotazování nejsou podporovány. [Posunutí/načtení](/sql/t-sql/queries/select-order-by-clause-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest#using-offset-and-fetch-to-limit-the-rows-returned) a [Pivot/Unpivot](/sql/t-sql/queries/from-using-pivot-and-unpivot?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) lze použít k dotazování systémových objektů (ne externích dat). |
| **Příkaz INSERT** | Yes | No |
| **Příkaz UPDATE** | Yes | No |
| **Příkaz DELETE** | Yes | No |
| **Příkaz MERGE** | No | No |
| **[Transakce](develop-transactions.md)** | Yes | No |
| **[Popisky](develop-label.md)** | Yes | No |
| **Načtení dat** | Yes. Preferovaný nástroj je příkaz [copy](/sql/t-sql/statements/copy-into-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) , ale systém podporuje hromadnou zátěž (BCP) i [CETAS](/sql/t-sql/statements/create-external-table-as-select-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) pro načítání dat. | No |
| **Export dat** | Yes. Pomocí [CETAS](/sql/t-sql/statements/create-external-table-as-select-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest). | Yes. Pomocí [CETAS](/sql/t-sql/statements/create-external-table-as-select-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest). |
| **Typy** | Ano, všechny typy Transact-SQL s výjimkou [Cursor](/sql/t-sql/data-types/cursor-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), [hierarchyid](/sql/t-sql/data-types/hierarchyid-data-type-method-reference?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), [ntext, text a image](/sql/t-sql/data-types/ntext-text-and-image-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), [rowversion](/sql/t-sql/data-types/rowversion-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), [prostorových typů](/sql/t-sql/spatial-geometry/spatial-types-geometry-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), [SQL \_ variant](/sql/t-sql/data-types/sql-variant-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)a [XML](/sql/t-sql/xml/xml-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) | Ano, všechny typy Transact-SQL s výjimkou [Cursor](/sql/t-sql/data-types/cursor-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), [hierarchyid](/sql/t-sql/data-types/hierarchyid-data-type-method-reference?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), [ntext, text a image](/sql/t-sql/data-types/ntext-text-and-image-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), [rowversion](/sql/t-sql/data-types/rowversion-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), [prostorových typů](/sql/t-sql/spatial-geometry/spatial-types-geometry-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), [SQL \_ variant](/sql/t-sql/data-types/sql-variant-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), [XML](/sql/t-sql/xml/xml-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)a typu tabulky |
| **Mezidatabázové dotazy** | No | Ano, včetně příkazu [Use](/sql/t-sql/language-elements/use-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) . |
| **Předdefinované funkce (analýza)** | Ano, všechny [analytické](/sql/t-sql/functions/analytic-functions-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)operace Transact-SQL, převod, [Datum a čas](/sql/t-sql/functions/date-and-time-data-types-and-functions-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), logické, [matematické](/sql/t-sql/functions/mathematical-functions-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) funkce, s výjimkou [příkazu Choose](/sql/t-sql/functions/logical-functions-choose-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), [IIf](/sql/t-sql/functions/logical-functions-iif-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)a [Parse](/sql/t-sql/functions/parse-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) | Ano, všechny [analytické](/sql/t-sql/functions/analytic-functions-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), převodní, [Datum a čas](/sql/t-sql/functions/date-and-time-data-types-and-functions-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)v jazyce Transact-SQL, logické a [matematické](/sql/t-sql/functions/mathematical-functions-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) funkce. |
| **Předdefinované funkce (text)** | Yes. Všechny funkce jazyka Transact-SQL [String](/sql/t-sql/functions/string-functions-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), [JSON](/sql/t-sql/functions/json-functions-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)a kolace s výjimkou [STRING_ESCAPE](/sql/t-sql/functions/string-escape-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) a [překladu](/sql/t-sql/functions/translate-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) | Yes. Všechny funkce Transact-SQL [String](/sql/t-sql/functions/string-functions-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), [JSON](/sql/t-sql/functions/json-functions-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)a kolace. |
| **Předdefinované funkce tabulky – hodnota** | Ano, [funkce sady řádků Transact-SQL](/sql/t-sql/functions/functions?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest#rowset-functions), s výjimkou formátu [OPENXML](/sql/t-sql/functions/openxml-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), [OpenDataSource](/sql/t-sql/functions/opendatasource-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), [OtevřítDotaz](/sql/t-sql/functions/openquery-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)a [OpenRowset](/sql/t-sql/functions/openrowset-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) | Ano, [funkce sady řádků Transact-SQL](/sql/t-sql/functions/functions?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest#rowset-functions), s výjimkou formátu [OPENXML](/sql/t-sql/functions/openxml-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), [OpenDataSource](/sql/t-sql/functions/opendatasource-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)a [OtevřítDotaz](/sql/t-sql/functions/openquery-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)  |
| **Agregace** |  Předdefinované agregované hodnoty jazyka Transact-SQL s výjimkou [CHECKSUM_AGG](/sql/t-sql/functions/checksum-agg-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) a [GROUPING_ID](/sql/t-sql/functions/grouping-id-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) | Předdefinované agregace jazyka Transact-SQL s výjimkou [řetězce \_ AGG](/sql/t-sql/functions/string-agg-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) |
| **Operátory** | Ano, všechny [operátory jazyka Transact-SQL](/sql/t-sql/language-elements/operators-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) s výjimkou [! >](/sql/t-sql/language-elements/not-greater-than-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) a [! <](/sql/t-sql/language-elements/not-less-than-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) | Ano, všechny [operátory jazyka Transact-SQL](/sql/t-sql/language-elements/operators-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)  |
| **Řízení toku** | Yes. Všechny [příkazy Transact-SQL Control-of-Flow](/sql/t-sql/language-elements/control-of-flow?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) s výjimkou [pokračování](/sql/t-sql/language-elements/continue-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), [goto](/sql/t-sql/language-elements/goto-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), [return](/sql/t-sql/language-elements/return-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), [Use](/sql/t-sql/language-elements/use-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)a [waitfor](/sql/t-sql/language-elements/waitfor-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) | Yes. Všechny [příkazy Transact-SQL Control-of-Flow](/sql/t-sql/language-elements/control-of-flow?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) s výjimkou [NÁVRATového](/sql/t-sql/language-elements/return-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) a výběrového dotazu v `WHILE (...)` podmínce |
| **Příkazy DDL (vytvoření, změna, vyřazení)** | Yes. Všechny příkazy jazyka Transact-SQL DDL použitelné pro podporované typy objektů | Yes. Všechny příkazy jazyka Transact-SQL DDL použitelné pro podporované typy objektů |

## <a name="security"></a>Zabezpečení

Synapse SQL vám umožní používat integrované funkce zabezpečení k zabezpečení dat a řízení přístupu. V následující tabulce jsou porovnávány rozdíly vysoké úrovně mezi synapsemi modely spotřeby SQL.

|   | Zřízené | Bez serveru |
| --- | --- | --- |
| **Přihlášení** | Není k dispozici (databáze podporuje pouze obsažené uživatele) | Yes |
| **Uživatelé** |  Není k dispozici (databáze podporuje pouze obsažené uživatele) | Yes |
| **[Uživatelé s omezením](/sql/relational-databases/security/contained-database-users-making-your-database-portable?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)** | Yes. **Poznámka:** správce může být bez omezení jenom u jednoho uživatele Azure AD. | Yes |
| **Ověřování uživatelského jména a hesla SQL**| Yes | Yes |
| **Ověřování Azure Active Directory (AAD)**| Ano, uživatelé Azure AD | Ano, přihlášení a uživatelé Azure AD |
| **Předávací ověřování pro úložiště Azure Active Directory (AAD)** | Yes | Yes |
| **Ověřování tokenu SAS úložiště** | No | Ano, použití [přihlašovacích údajů s rozsahem databáze](/sql/t-sql/statements/create-database-scoped-credential-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) v [externím zdroji dat](/sql/t-sql/statements/create-external-data-source-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) nebo [přihlašovacích údajích](/sql/t-sql/statements/create-credential-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)na úrovni instance. |
| **Ověřování přístupového klíče k úložišti** | Ano, použití [přihlašovacích údajů v oboru databáze](/sql/t-sql/statements/create-database-scoped-credential-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) v [EXTERNÍm zdroji dat](/sql/t-sql/statements/create-external-data-source-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) | No |
| **Ověřování [identity spravovaného](../security/synapse-workspace-managed-identity.md) úložištěm** | Ano, pomocí [Identita spravované služby přihlašovací údaje](../../sql-database/sql-database-vnet-service-endpoint-rule-overview.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) | Ano, pomocí `Managed Identity` přihlašovacích údajů. |
| **Ověřování identity aplikace úložiště** | [Ano](/sql/t-sql/statements/create-external-data-source-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) | No |
| **Oprávnění – úroveň objektu** | Ano, včetně možnosti udělit, odepřít a odvolat oprávnění uživatelům | Ano, včetně možnosti udělit, odepřít a odvolat oprávnění pro uživatele nebo přihlašovací údaje u systémových objektů, které jsou podporovány |
| **Oprávnění – úroveň schématu** | Ano, včetně možnosti udělit, odepřít a odvolat oprávnění uživatelům nebo přihlášením ve schématu | Ano, včetně možnosti udělit, odepřít a odvolat oprávnění uživatelům nebo přihlášením ve schématu |
| **Oprávnění – na [úrovni databáze](/sql/relational-databases/security/authentication-access/database-level-roles?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)** | Yes | Yes |
| **Oprávnění – na [úrovni serveru](/sql/relational-databases/security/authentication-access/server-level-roles)** | No | Ano, sysadmin a další serverové role jsou podporovány |
| **Oprávnění – [zabezpečení na úrovni sloupců](/azure/synapse-analytics/sql-data-warehouse/column-level-security?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)** | Yes | Yes |
| **Role/skupiny** | Ano (databáze s rozsahem) | Ano (u serveru i z oboru databáze) |
| **&amp;Funkce identity zabezpečení** | Některé funkce a operátory zabezpečení jazyka Transact-SQL:  `CURRENT_USER` , `HAS_DBACCESS` , `IS_MEMBER` , `IS_ROLEMEMBER` , `SESSION_USER` , `SUSER_NAME` , `SUSER_SNAME` , `SYSTEM_USER` , `USER` , `USER_NAME` , `EXECUTE AS` , `OPEN/CLOSE MASTER KEY` | Některé funkce a operátory zabezpečení jazyka Transact-SQL:  `CURRENT_USER` , `HAS_DBACCESS` , `HAS_PERMS_BY_NAME` , `IS_MEMBER', 'IS_ROLEMEMBER` , `IS_SRVROLEMEMBER` , `SESSION_USER` , `SUSER_NAME` , `SUSER_SNAME` , `SYSTEM_USER` , `USER` , `USER_NAME` , `EXECUTE AS` a `REVERT` . Funkce zabezpečení nelze použít k dotazování na externí data (uložení výsledku do proměnné, kterou lze použít v dotazu).  |
| **POVĚŘENÍ V OBORU DATABÁZE** | Yes | Yes |
| **PŘIHLAŠOVACÍ ÚDAJE V OBORU SERVERU** | No | Yes |
| **Zabezpečení na úrovni řádku** | [Ano](https://docs.microsoft.com/sql/relational-databases/security/row-level-security?toc=%2Fazure%2Fsynapse-analytics%2Ftoc.json&bc=%2Fazure%2Fsynapse-analytics%2Fbreadcrumb%2Ftoc.json&view=sql-server-ver15) | No |
| **Transparentní šifrování dat** | [Ano](/azure/sql-database/transparent-data-encryption-azure-sql?toc=%2Fazure%2Fsynapse-analytics%2Ftoc.json&bc=%2Fazure%2Fsynapse-analytics%2Fbreadcrumb%2Ftoc.json&tabs=azure-portal) | No | 
| **Zjišťování a klasifikace dat** | [Ano](/azure/sql-database/sql-database-data-discovery-and-classification?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) | No |
| **Posouzení ohrožení zabezpečení** | [Ano](/azure/sql-database/sql-vulnerability-assessment?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) | No |
| **Advanced Threat Protection** | [Ano](/azure/sql-database/sql-database-threat-detection-overview?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
| **Auditování** | [Ano](/azure/sql-database/sql-database-auditing?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) | No |
| **[Pravidla brány firewall](../security/synapse-workspace-ip-firewall.md)**| Yes | Yes |
| **[Soukromý koncový bod](../security/synapse-workspace-managed-private-endpoints.md)**| Yes | Yes |

SQL fond a SQL na vyžádání používají standardní jazyk Transact-SQL k dotazování dat. Podrobné rozdíly najdete v [referenčních informacích k jazyku Transact-SQL](/sql/t-sql/language-reference).

## <a name="tools"></a>Nástroje

Můžete použít různé nástroje pro připojení k synapse SQL k dotazování dat.

|   | Zřízené | Bez serveru |
| --- | --- | --- |
| **Synapse Studio** | Ano, skripty SQL | Ano, skripty SQL |
| **Power BI** | Yes | [Ano](tutorial-connect-power-bi-desktop.md) |
| **Služba Azure Analysis** | Yes | Yes |
| **Azure Data Studio** | Yes | Ano, verze 1.18.0 nebo vyšší. Podporují se skripty SQL a notebooky SQL. |
| **SQL Server Management Studio** | Yes | Ano, verze 18,5 nebo vyšší |

> [!NOTE]
> SSMS můžete použít pro připojení k SQL na vyžádání (Preview) a dotazování. Od verze 18,5 se tato podpora částečně podporuje a můžete ji použít jenom pro připojení a dotazování.

Většina aplikací používá standardní jazyk Transact-SQL, který může dotazovat jak zřízené, tak i neserverové modely spotřeby synapse SQL.

## <a name="storage-options"></a>Možnosti úložiště

Data, která se analyzují, se dají ukládat do různých typů úložiště. V následující tabulce jsou uvedeny všechny dostupné možnosti úložiště:

|   | Zřízené | Bez serveru |
| --- | --- | --- |
| **Interní úložiště** | Yes | No |
| **Azure Data Lake v2** | Yes | Yes |
| **Azure Blob Storage** | Yes | Yes |
| **Analytické úložiště Azure CosmosDB** | No | Ano, pomocí [odkazu synapse](/azure/cosmos-db/synapse-link?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) (v části [gatedd Preview](/azure/cosmos-db/synapse-link?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json#limitations)) |

## <a name="data-formats"></a>Formáty dat

Data, která se analyzují, se dají ukládat v různých formátech úložiště. V následující tabulce jsou uvedeny všechny dostupné formáty dat, které lze analyzovat:

|   | Zřízené | Bez serveru |
| --- | --- | --- |
| **Oddělených** | [Ano](/sql/t-sql/statements/create-external-file-format-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) | [Ano](query-single-csv-file.md) |
| **CSV** | Ano (oddělovače s více znaky se nepodporují.) | [Ano](query-single-csv-file.md) |
| **Parquet** | [Ano](/sql/t-sql/statements/create-external-file-format-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) | [Ano](query-parquet-files.md), včetně souborů s [vnořenými typy](query-parquet-nested-types.md) |
| **ORC podregistru** | [Ano](/sql/t-sql/statements/create-external-file-format-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) | No |
| **Podregistr RC** | [Ano](/sql/t-sql/statements/create-external-file-format-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) | No |
| **JSON** | Yes | [Ano](query-json-files.md) |
| **Avro** | No | No |
| **[Rozdíl – Lake](https://delta.io/)** | No | No |
| **[CDM](https://docs.microsoft.com/common-data-model/)** | No | No |

## <a name="next-steps"></a>Další kroky
Další informace o osvědčených postupech pro fond SQL a SQL na vyžádání najdete v následujících článcích:

- [Osvědčené postupy pro fond SQL](best-practices-sql-pool.md)
- [Osvědčené postupy pro SQL na vyžádání](best-practices-sql-on-demand.md)
