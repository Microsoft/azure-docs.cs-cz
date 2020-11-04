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
ms.openlocfilehash: fe30a2a0885e1a579eb32ad84ef467f7162febe4
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/04/2020
ms.locfileid: "93310326"
---
# <a name="transact-sql-features-supported-in-azure-synapse-sql"></a>Funkce jazyka Transact-SQL podporované v Azure synapse SQL

Azure synapse SQL je analytická služba pro velké objemy dat, která umožňuje dotazování a analýzu dat pomocí jazyka T-SQL. Můžete použít standardní dialekt jazyka SQL kompatibilní se standardem ANSI, který se používá na SQL Server a Azure SQL Database pro analýzu dat. 

Jazyk Transact-SQL se používá v Synapsem a zřízeném modelu s SQL serverem se může odkazovat na různé objekty a v sadě podporovaných funkcí je několik rozdílů. Na této stránce můžete najít rozdíly v jazyce Transact-SQL ve velkém rozsahu mezi modely spotřeby synapse SQL.

## <a name="database-objects"></a>Objekty databáze

Modely spotřeby v synapse SQL umožňují použití různých databázových objektů. Porovnání podporovaných typů objektů je uvedeno v následující tabulce:

|   | Zřízené | Bez serveru |
| --- | --- | --- |
| **Tabulky** | [Ano](/sql/t-sql/statements/create-table-azure-sql-data-warehouse?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) | Ne, model bez serveru se může dotazovat jenom na externí data umístěná na [Azure Storage](#storage-options) |
| **Zobrazení** | [Ano](/sql/t-sql/statements/create-view-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true). Zobrazení mohou používat [prvky dotazovacího jazyka](#query-language) , které jsou k dispozici v zřízeném modelu. | [Ano](/sql/t-sql/statements/create-view-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true). Zobrazení mohou používat [prvky dotazovacího jazyka](#query-language) , které jsou k dispozici v modelu bez serveru. |
| **Schémata** | [Ano](/sql/t-sql/statements/create-schema-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) | [Ano](/sql/t-sql/statements/create-schema-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) |
| **Dočasné tabulky** | [Ano](../sql-data-warehouse/sql-data-warehouse-tables-temporary.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) | Ne |
| **Procedury** | [Ano](/sql/t-sql/statements/create-procedure-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) | Ne |
| **Functions** | [Ano](/sql/t-sql/statements/create-function-sql-data-warehouse?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) | Ano, vložené funkce vracející tabulku. |
| **Triggery** | Ne | Ne |
| **Externí tabulky** | [Ano](/sql/t-sql/statements/create-external-table-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true). Viz Podporované [formáty dat](#data-formats). | [Ano](/sql/t-sql/statements/create-external-table-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true). Viz Podporované [formáty dat](#data-formats). |
| **Ukládání dotazů do mezipaměti** | Ano, více forem (ukládání do mezipaměti založené na SSD, ukládání do mezipaměti sady výsledků v paměti). Kromě toho se podporují materializované zobrazení. | Ne |
| **Proměnné tabulek** | [Ne](/sql/t-sql/data-types/table-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true), použít dočasné tabulky | Ne |
| **[Distribuce tabulky](../sql-data-warehouse/sql-data-warehouse-tables-distribute.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)**               | Ano | Ne |
| **[Indexy tabulek](../sql-data-warehouse/sql-data-warehouse-tables-index.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)**                           | Ano | Ne |
| **[Oddíly tabulky](../sql-data-warehouse/sql-data-warehouse-tables-partition.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)**                     | Ano | Ne |
| **[Statistika](develop-tables-statistics.md)**            | Ano | Ano |
| **[Správa úloh, třídy prostředků a řízení souběžnosti](../sql-data-warehouse/resource-classes-for-workload-management.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)** | Ano    | Ne |

## <a name="query-language"></a>Dotazovací jazyk

Jazyky dotazů používané v synapse SQL mohou mít různé podporované funkce v závislosti na modelu spotřeby. Následující tabulka popisuje nejdůležitější rozdíly v dotazovacím jazyku v dialektech jazyka Transact-SQL:

|   | Zřízené | Bez serveru |
| --- | --- | --- |
| **Příkaz SELECT** | Ano. Klauzule dotazu Transact-SQL [pro XML/for JSON](/sql/t-sql/queries/select-for-clause-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)a [porovnávání](/sql/t-sql/queries/match-sql-graph?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) nejsou podporovány. | Ano. Klauzule dotazu Transact-SQL [pro příkazy jazyka XML](/sql/t-sql/queries/select-for-clause-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true), [porovnávání](/sql/t-sql/queries/match-sql-graph?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true), [předpovědi](/sql/t-sql/queries/predict-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)a dotazování nejsou podporovány. [Posunutí/načtení](/sql/t-sql/queries/select-order-by-clause-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest#using-offset-and-fetch-to-limit-the-rows-returned) a [Pivot/Unpivot](/sql/t-sql/queries/from-using-pivot-and-unpivot?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) lze použít k dotazování systémových objektů (ne externích dat). |
| **Příkaz INSERT** | Ano | Ne |
| **Příkaz UPDATE** | Ano | Ne |
| **Příkaz DELETE** | Ano | Ne |
| **Příkaz MERGE** | Ne | Ne |
| **[Transakce](develop-transactions.md)** | Ano | Ne |
| **[Popisky](develop-label.md)** | Ano | Ne |
| **Načtení dat** | Ano. Preferovaný nástroj je příkaz [copy](/sql/t-sql/statements/copy-into-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) , ale systém podporuje hromadnou zátěž (BCP) i [CETAS](/sql/t-sql/statements/create-external-table-as-select-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) pro načítání dat. | Ne |
| **Export dat** | Ano. Pomocí [CETAS](/sql/t-sql/statements/create-external-table-as-select-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true). | Ano. Pomocí [CETAS](/sql/t-sql/statements/create-external-table-as-select-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true). |
| **Typy** | Ano, všechny typy Transact-SQL s výjimkou [Cursor](/sql/t-sql/data-types/cursor-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true), [hierarchyid](/sql/t-sql/data-types/hierarchyid-data-type-method-reference?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true), [ntext, text a image](/sql/t-sql/data-types/ntext-text-and-image-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true), [rowversion](/sql/t-sql/data-types/rowversion-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true), [prostorových typů](/sql/t-sql/spatial-geometry/spatial-types-geometry-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true), [SQL \_ variant](/sql/t-sql/data-types/sql-variant-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)a [XML](/sql/t-sql/xml/xml-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) | Ano, všechny typy Transact-SQL s výjimkou [Cursor](/sql/t-sql/data-types/cursor-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true), [hierarchyid](/sql/t-sql/data-types/hierarchyid-data-type-method-reference?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true), [ntext, text a image](/sql/t-sql/data-types/ntext-text-and-image-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true), [rowversion](/sql/t-sql/data-types/rowversion-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true), [prostorových typů](/sql/t-sql/spatial-geometry/spatial-types-geometry-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true), [SQL \_ variant](/sql/t-sql/data-types/sql-variant-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true), [XML](/sql/t-sql/xml/xml-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)a typu tabulky |
| **Mezidatabázové dotazy** | Ne | Ano, včetně příkazu [Use](/sql/t-sql/language-elements/use-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) . |
| **Předdefinované funkce (analýza)** | Ano, všechny [analytické](/sql/t-sql/functions/analytic-functions-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)operace Transact-SQL, převod, [Datum a čas](/sql/t-sql/functions/date-and-time-data-types-and-functions-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true), logické, [matematické](/sql/t-sql/functions/mathematical-functions-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) funkce, s výjimkou [příkazu Choose](/sql/t-sql/functions/logical-functions-choose-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true), [IIf](/sql/t-sql/functions/logical-functions-iif-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)a [Parse](/sql/t-sql/functions/parse-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) | Ano, všechny [analytické](/sql/t-sql/functions/analytic-functions-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true), převodní, [Datum a čas](/sql/t-sql/functions/date-and-time-data-types-and-functions-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)v jazyce Transact-SQL, logické a [matematické](/sql/t-sql/functions/mathematical-functions-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) funkce. |
| **Předdefinované funkce (text)** | Ano. Všechny funkce jazyka Transact-SQL [String](/sql/t-sql/functions/string-functions-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true), [JSON](/sql/t-sql/functions/json-functions-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)a kolace s výjimkou [STRING_ESCAPE](/sql/t-sql/functions/string-escape-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) a [překladu](/sql/t-sql/functions/translate-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) | Ano. Všechny funkce Transact-SQL [String](/sql/t-sql/functions/string-functions-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true), [JSON](/sql/t-sql/functions/json-functions-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)a kolace. |
| **Předdefinované funkce tabulky – hodnota** | Ano, [funkce sady řádků Transact-SQL](/sql/t-sql/functions/functions?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest#rowset-functions), s výjimkou formátu [OPENXML](/sql/t-sql/functions/openxml-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true), [OpenDataSource](/sql/t-sql/functions/opendatasource-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true), [OtevřítDotaz](/sql/t-sql/functions/openquery-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)a [OpenRowset](/sql/t-sql/functions/openrowset-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) | Ano, [funkce sady řádků Transact-SQL](/sql/t-sql/functions/functions?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest#rowset-functions), s výjimkou formátu [OPENXML](/sql/t-sql/functions/openxml-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true), [OpenDataSource](/sql/t-sql/functions/opendatasource-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)a [OtevřítDotaz](/sql/t-sql/functions/openquery-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)  |
| **Agregace** |  Předdefinované agregované hodnoty jazyka Transact-SQL s výjimkou [CHECKSUM_AGG](/sql/t-sql/functions/checksum-agg-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) a [GROUPING_ID](/sql/t-sql/functions/grouping-id-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) | Předdefinované agregace jazyka Transact-SQL s výjimkou [řetězce \_ AGG](/sql/t-sql/functions/string-agg-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) |
| **Operátory** | Ano, všechny [operátory jazyka Transact-SQL](/sql/t-sql/language-elements/operators-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) s výjimkou [! >](/sql/t-sql/language-elements/not-greater-than-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) a [! <](/sql/t-sql/language-elements/not-less-than-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) | Ano, všechny [operátory jazyka Transact-SQL](/sql/t-sql/language-elements/operators-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)  |
| **Řízení toku** | Ano. Všechny [příkazy Transact-SQL Control-of-Flow](/sql/t-sql/language-elements/control-of-flow?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) s výjimkou [pokračování](/sql/t-sql/language-elements/continue-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true), [goto](/sql/t-sql/language-elements/goto-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true), [return](/sql/t-sql/language-elements/return-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true), [Use](/sql/t-sql/language-elements/use-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)a [waitfor](/sql/t-sql/language-elements/waitfor-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) | Ano. Všechny [příkazy Transact-SQL Control-of-Flow](/sql/t-sql/language-elements/control-of-flow?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) s výjimkou [NÁVRATového](/sql/t-sql/language-elements/return-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) a výběrového dotazu v `WHILE (...)` podmínce |
| **Příkazy DDL (vytvoření, změna, vyřazení)** | Ano. Všechny příkazy jazyka Transact-SQL DDL použitelné pro podporované typy objektů | Ano. Všechny příkazy jazyka Transact-SQL DDL použitelné pro podporované typy objektů |

## <a name="security"></a>Zabezpečení

Synapse SQL vám umožní používat integrované funkce zabezpečení k zabezpečení dat a řízení přístupu. V následující tabulce jsou porovnávány rozdíly vysoké úrovně mezi synapsemi modely spotřeby SQL.

|   | Zřízené | Bez serveru |
| --- | --- | --- |
| **Přihlášení** | Není k dispozici (databáze podporuje pouze obsažené uživatele) | Ano |
| **Uživatelé** |  Není k dispozici (databáze podporuje pouze obsažené uživatele) | Ano |
| **[Uživatelé s omezením](/sql/relational-databases/security/contained-database-users-making-your-database-portable?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)** | Ano. **Poznámka:** správce může být bez omezení jenom u jednoho uživatele Azure AD. | Ano |
| **Ověřování uživatelského jména a hesla SQL**| Ano | Ano |
| **Ověřování Azure Active Directory (Azure AD)**| Ano, uživatelé Azure AD | Ano, přihlášení a uživatelé Azure AD |
| **Předávací ověřování pro službu Storage Azure Active Directory (Azure AD)** | Ano | Ano |
| **Ověřování tokenu SAS úložiště** | Ne | Ano, použití [přihlašovacích údajů s rozsahem databáze](/sql/t-sql/statements/create-database-scoped-credential-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) v [externím zdroji dat](/sql/t-sql/statements/create-external-data-source-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) nebo [přihlašovacích údajích](/sql/t-sql/statements/create-credential-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)na úrovni instance. |
| **Ověřování přístupového klíče k úložišti** | Ano, použití [přihlašovacích údajů v oboru databáze](/sql/t-sql/statements/create-database-scoped-credential-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) v [EXTERNÍm zdroji dat](/sql/t-sql/statements/create-external-data-source-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) | Ne |
| **Ověřování [identity spravovaného](../security/synapse-workspace-managed-identity.md) úložištěm** | Ano, pomocí [Identita spravované služby přihlašovací údaje](../../sql-database/sql-database-vnet-service-endpoint-rule-overview.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) | Ano, pomocí `Managed Identity` přihlašovacích údajů. |
| **Ověřování identity aplikace úložiště** | [Ano](/sql/t-sql/statements/create-external-data-source-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) | Ne |
| **Oprávnění – úroveň objektu** | Ano, včetně možnosti udělit, odepřít a odvolat oprávnění uživatelům | Ano, včetně možnosti udělit, odepřít a odvolat oprávnění pro uživatele nebo přihlašovací údaje u systémových objektů, které jsou podporovány |
| **Oprávnění – úroveň schématu** | Ano, včetně možnosti udělit, odepřít a odvolat oprávnění uživatelům nebo přihlášením ve schématu | Ano, včetně možnosti udělit, odepřít a odvolat oprávnění uživatelům nebo přihlášením ve schématu |
| **Oprávnění – na [úrovni databáze](/sql/relational-databases/security/authentication-access/database-level-roles?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)** | Ano | Ano |
| **Oprávnění – na [úrovni serveru](/sql/relational-databases/security/authentication-access/server-level-roles)** | Ne | Ano, sysadmin a další serverové role jsou podporovány |
| **Oprávnění – [zabezpečení na úrovni sloupců](/azure/synapse-analytics/sql-data-warehouse/column-level-security?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)** | Ano | Ano |
| **Role/skupiny** | Ano (databáze s rozsahem) | Ano (u serveru i z oboru databáze) |
| **&amp;Funkce identity zabezpečení** | Některé funkce a operátory zabezpečení jazyka Transact-SQL:  `CURRENT_USER` , `HAS_DBACCESS` , `IS_MEMBER` , `IS_ROLEMEMBER` , `SESSION_USER` , `SUSER_NAME` , `SUSER_SNAME` , `SYSTEM_USER` , `USER` , `USER_NAME` , `EXECUTE AS` , `OPEN/CLOSE MASTER KEY` | Některé funkce a operátory zabezpečení jazyka Transact-SQL:  `CURRENT_USER` , `HAS_DBACCESS` , `HAS_PERMS_BY_NAME` , `IS_MEMBER', 'IS_ROLEMEMBER` , `IS_SRVROLEMEMBER` , `SESSION_USER` , `SUSER_NAME` , `SUSER_SNAME` , `SYSTEM_USER` , `USER` , `USER_NAME` , `EXECUTE AS` a `REVERT` . Funkce zabezpečení nelze použít k dotazování na externí data (uložení výsledku do proměnné, kterou lze použít v dotazu).  |
| **POVĚŘENÍ V OBORU DATABÁZE** | Ano | Ano |
| **PŘIHLAŠOVACÍ ÚDAJE V OBORU SERVERU** | Ne | Ano |
| **Zabezpečení na úrovni řádku** | [Ano](https://docs.microsoft.com/sql/relational-databases/security/row-level-security?toc=%2Fazure%2Fsynapse-analytics%2Ftoc.json&bc=%2Fazure%2Fsynapse-analytics%2Fbreadcrumb%2Ftoc.json&view=sql-server-ver15) | Ne |
| **Transparentní šifrování dat** | [Ano](/azure/sql-database/transparent-data-encryption-azure-sql?toc=%2Fazure%2Fsynapse-analytics%2Ftoc.json&bc=%2Fazure%2Fsynapse-analytics%2Fbreadcrumb%2Ftoc.json&tabs=azure-portal) | Ne | 
| **Zjišťování a klasifikace dat** | [Ano](/azure/sql-database/sql-database-data-discovery-and-classification?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) | Ne |
| **Posouzení ohrožení zabezpečení** | [Ano](/azure/sql-database/sql-vulnerability-assessment?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) | Ne |
| **Advanced Threat Protection** | [Ano](/azure/sql-database/sql-database-threat-detection-overview?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
| **Auditování** | [Ano](/azure/sql-database/sql-database-auditing?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) | Ne |
| **[Pravidla brány firewall](../security/synapse-workspace-ip-firewall.md)**| Ano | Ano |
| **[Privátní koncový bod](../security/synapse-workspace-managed-private-endpoints.md)**| Ano | Ano |

Vyhrazený fond SQL a SQL Server bez serveru používají standardní jazyk Transact-SQL k dotazování dat. Podrobné rozdíly najdete v [referenčních informacích k jazyku Transact-SQL](/sql/t-sql/language-reference).

## <a name="tools"></a>nástroje

Můžete použít různé nástroje pro připojení k synapse SQL k dotazování dat.

|   | Zřízené | Bez serveru |
| --- | --- | --- |
| **Synapse Studio** | Ano, skripty SQL | Ano, skripty SQL |
| **Power BI** | Ano | [Ano](tutorial-connect-power-bi-desktop.md) |
| **Služba Azure Analysis** | Ano | Ano |
| **Azure Data Studio** | Ano | Ano, verze 1.18.0 nebo vyšší. Podporují se skripty SQL a notebooky SQL. |
| **SQL Server Management Studio** | Ano | Ano, verze 18,5 nebo vyšší |

> [!NOTE]
> Pomocí SSMS se můžete připojit k fondu SQL bez serveru (Preview) a dotazům. Od verze 18,5 se tato podpora částečně podporuje a můžete ji použít jenom pro připojení a dotazování.

Většina aplikací používá standardní jazyk Transact-SQL, který může dotazovat jak zřízené, tak i neserverové modely spotřeby synapse SQL.

## <a name="storage-options"></a>Možnosti úložiště

Data, která se analyzují, se dají ukládat do různých typů úložiště. V následující tabulce jsou uvedeny všechny dostupné možnosti úložiště:

|   | Zřízené | Bez serveru |
| --- | --- | --- |
| **Interní úložiště** | Ano | Ne |
| **Azure Data Lake v2** | Ano | Ano |
| **Azure Blob Storage** | Ano | Ano |
| **Analytické úložiště Azure CosmosDB** | Ne | Ano, pomocí [odkazu synapse](/azure/cosmos-db/synapse-link?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) (v části [gatedd Preview](/azure/cosmos-db/synapse-link?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json#limitations)) |

## <a name="data-formats"></a>Formáty dat

Data, která se analyzují, se dají ukládat v různých formátech úložiště. V následující tabulce jsou uvedeny všechny dostupné formáty dat, které lze analyzovat:

|   | Zřízené | Bez serveru |
| --- | --- | --- |
| **Oddělených** | [Ano](/sql/t-sql/statements/create-external-file-format-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) | [Ano](query-single-csv-file.md) |
| **CSV** | Ano (oddělovače s více znaky se nepodporují.) | [Ano](query-single-csv-file.md) |
| **Parquet** | [Ano](/sql/t-sql/statements/create-external-file-format-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) | [Ano](query-parquet-files.md), včetně souborů s [vnořenými typy](query-parquet-nested-types.md) |
| **ORC podregistru** | [Ano](/sql/t-sql/statements/create-external-file-format-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) | Ne |
| **Podregistr RC** | [Ano](/sql/t-sql/statements/create-external-file-format-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) | Ne |
| **JSON** | Ano | [Ano](query-json-files.md) |
| **Avro** | Ne | Ne |
| **[Rozdíl – Lake](https://delta.io/)** | Ne | Ne |
| **[CDM](https://docs.microsoft.com/common-data-model/)** | Ne | No |

## <a name="next-steps"></a>Další kroky
Další informace o osvědčených postupech pro vyhrazený fond SQL a SQL Server bez serveru najdete v následujících článcích:

- [Osvědčené postupy pro vyhrazený fond SQL](best-practices-sql-pool.md)
- [Osvědčené postupy pro fond SQL bez serveru](best-practices-sql-on-demand.md)
