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
ms.openlocfilehash: b22f2ed2704f752ff2ca7a5bc21e856b076f0f70
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "104584087"
---
# <a name="transact-sql-features-supported-in-azure-synapse-sql"></a>Funkce jazyka Transact-SQL podporované v Azure synapse SQL

Azure synapse SQL je analytická služba pro velké objemy dat, která umožňuje dotazování a analýzu dat pomocí jazyka T-SQL. Můžete použít standardní dialekt jazyka SQL kompatibilní se standardem ANSI, který se používá na SQL Server a Azure SQL Database pro analýzu dat. 

Jazyk Transact-SQL se používá v neserverovém fondu SQL a vyhrazený model může odkazovat na různé objekty a obsahuje několik rozdílů v sadě podporovaných funkcí. Na této stránce můžete najít rozdíly v jazyce Transact-SQL ve velkém rozsahu mezi modely spotřeby synapse SQL.

## <a name="database-objects"></a>Objekty databáze

Modely spotřeby v synapse SQL umožňují použití různých databázových objektů. Porovnání podporovaných typů objektů je uvedeno v následující tabulce:

|   | Vyhrazená | Bez serveru |
| --- | --- | --- |
| **Tabulky** | [Ano](/sql/t-sql/statements/create-table-azure-sql-data-warehouse?view=azure-sqldw-latest&preserve-view=true) | Ne, model bez serveru se může dotazovat jenom na externí data umístěná na [Azure Storage](#storage-options) |
| **Zobrazení** | [Ano](/sql/t-sql/statements/create-view-transact-sql?view=azure-sqldw-latest&preserve-view=true). Zobrazení mohou používat [prvky dotazovacího jazyka](#query-language) , které jsou k dispozici ve vyhrazeném modelu. | [Ano](/sql/t-sql/statements/create-view-transact-sql?view=azure-sqldw-latest&preserve-view=true). Zobrazení mohou používat [prvky dotazovacího jazyka](#query-language) , které jsou k dispozici v modelu bez serveru. |
| **Schémata** | [Ano](/sql/t-sql/statements/create-schema-transact-sql?view=azure-sqldw-latest&preserve-view=true) | [Ano](/sql/t-sql/statements/create-schema-transact-sql?view=azure-sqldw-latest&preserve-view=true) |
| **Dočasné tabulky** | [Ano](../sql-data-warehouse/sql-data-warehouse-tables-temporary.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) | No |
| **Procedury** | [Ano](/sql/t-sql/statements/create-procedure-transact-sql?view=azure-sqldw-latest&preserve-view=true) | Yes |
| **Functions** | [Ano](/sql/t-sql/statements/create-function-sql-data-warehouse?view=azure-sqldw-latest&preserve-view=true) | Ano, vložené funkce vracející tabulku. |
| **Triggery** | No | No |
| **Externí tabulky** | [Ano](/sql/t-sql/statements/create-external-table-transact-sql?view=azure-sqldw-latest&preserve-view=true). Viz Podporované [formáty dat](#data-formats). | [Ano](/sql/t-sql/statements/create-external-table-transact-sql?view=azure-sqldw-latest&preserve-view=true). Viz Podporované [formáty dat](#data-formats). |
| **Ukládání dotazů do mezipaměti** | Ano, více forem (ukládání do mezipaměti založené na SSD, ukládání do mezipaměti sady výsledků v paměti). Kromě toho se podporují materializované zobrazení. | No |
| **Proměnné tabulek** | [Ne](/sql/t-sql/data-types/table-transact-sql?view=azure-sqldw-latest&preserve-view=true), použít dočasné tabulky | No |
| **[Distribuce tabulky](../sql-data-warehouse/sql-data-warehouse-tables-distribute.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)**               | Yes | No |
| **[Indexy tabulek](../sql-data-warehouse/sql-data-warehouse-tables-index.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)**                           | Yes | No |
| **[Oddíly tabulky](../sql-data-warehouse/sql-data-warehouse-tables-partition.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)**                     | Yes | No |
| **[Statistika](develop-tables-statistics.md)**            | Yes | Yes |
| **[Správa úloh, třídy prostředků a řízení souběžnosti](../sql-data-warehouse/resource-classes-for-workload-management.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)** | Yes    | No |
| **Řízení nákladů** | Ano, pomocí akcí horizontálního navýšení kapacity a horizontálního navýšení kapacity. | Ano, pomocí [procedury Azure Portal nebo T-SQL](./data-processed.md#cost-control). |

## <a name="query-language"></a>Dotazovací jazyk

Jazyky dotazů používané v synapse SQL mohou mít různé podporované funkce v závislosti na modelu spotřeby. Následující tabulka popisuje nejdůležitější rozdíly v dotazovacím jazyku v dialektech jazyka Transact-SQL:

|   | Vyhrazená | Bez serveru |
| --- | --- | --- |
| **Příkaz SELECT** | Ano. Klauzule dotazu Transact-SQL [pro XML/pro JSON](/sql/t-sql/queries/select-for-clause-transact-sql?view=azure-sqldw-latest&preserve-view=true), [Match](/sql/t-sql/queries/match-sql-graph?view=azure-sqldw-latest&preserve-view=true), offset/Fetch nejsou podporovány. | Ano. Klauzule dotazu Transact-SQL [pro XML](/sql/t-sql/queries/select-for-clause-transact-sql?view=azure-sqldw-latest&preserve-view=true), [Match](/sql/t-sql/queries/match-sql-graph?view=azure-sqldw-latest&preserve-view=true), [předpověď](/sql/t-sql/queries/predict-transact-sql?view=azure-sqldw-latest&preserve-view=true), GROUPNG sady a pomocný parametr dotazu nejsou podporovány. |
| **Příkaz INSERT** | Yes | No |
| **Příkaz UPDATE** | Yes | No |
| **Příkaz DELETE** | Yes | No |
| **Příkaz MERGE** | Ano ([Preview](/sql/t-sql/statements/merge-transact-sql?view=azure-sqldw-latest&preserve-view=true)) | No |
| **[Transakce](develop-transactions.md)** | Yes | Ano, platí pro objekty meta-data. |
| **[Popisky](develop-label.md)** | Yes | No |
| **Načtení dat** | Ano. Preferovaný nástroj je příkaz [copy](/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest&preserve-view=true) , ale systém podporuje hromadnou zátěž (BCP) i [CETAS](/sql/t-sql/statements/create-external-table-as-select-transact-sql?view=azure-sqldw-latest&preserve-view=true) pro načítání dat. | No |
| **Export dat** | Ano. Pomocí [CETAS](/sql/t-sql/statements/create-external-table-as-select-transact-sql?view=azure-sqldw-latest&preserve-view=true). | Ano. Pomocí [CETAS](/sql/t-sql/statements/create-external-table-as-select-transact-sql?view=azure-sqldw-latest&preserve-view=true). |
| **Typy** | Ano, všechny typy Transact-SQL s výjimkou [Cursor](/sql/t-sql/data-types/cursor-transact-sql?view=azure-sqldw-latest&preserve-view=true), [hierarchyid](/sql/t-sql/data-types/hierarchyid-data-type-method-reference?view=azure-sqldw-latest&preserve-view=true), [ntext, text a image](/sql/t-sql/data-types/ntext-text-and-image-transact-sql?view=azure-sqldw-latest&preserve-view=true), [rowversion](/sql/t-sql/data-types/rowversion-transact-sql?view=azure-sqldw-latest&preserve-view=true), [prostorových typů](/sql/t-sql/spatial-geometry/spatial-types-geometry-transact-sql?view=azure-sqldw-latest&preserve-view=true), [SQL \_ variant](/sql/t-sql/data-types/sql-variant-transact-sql?view=azure-sqldw-latest&preserve-view=true)a [XML](/sql/t-sql/xml/xml-transact-sql?view=azure-sqldw-latest&preserve-view=true) | Ano, všechny typy Transact-SQL s výjimkou [Cursor](/sql/t-sql/data-types/cursor-transact-sql?view=azure-sqldw-latest&preserve-view=true), [hierarchyid](/sql/t-sql/data-types/hierarchyid-data-type-method-reference?view=azure-sqldw-latest&preserve-view=true), [ntext, text a image](/sql/t-sql/data-types/ntext-text-and-image-transact-sql?view=azure-sqldw-latest&preserve-view=true), [rowversion](/sql/t-sql/data-types/rowversion-transact-sql?view=azure-sqldw-latest&preserve-view=true), [prostorových typů](/sql/t-sql/spatial-geometry/spatial-types-geometry-transact-sql?view=azure-sqldw-latest&preserve-view=true), [SQL \_ variant](/sql/t-sql/data-types/sql-variant-transact-sql?view=azure-sqldw-latest&preserve-view=true), [XML](/sql/t-sql/xml/xml-transact-sql?view=azure-sqldw-latest&preserve-view=true)a typu tabulky |
| **Mezidatabázové dotazy** | No | Ano, včetně příkazu [Use](/sql/t-sql/language-elements/use-transact-sql?view=azure-sqldw-latest&preserve-view=true) . |
| **Předdefinované funkce (analýza)** | Ano, všechny [analytické](/sql/t-sql/functions/analytic-functions-transact-sql?view=azure-sqldw-latest&preserve-view=true)operace Transact-SQL, převod, [Datum a čas](/sql/t-sql/functions/date-and-time-data-types-and-functions-transact-sql?view=azure-sqldw-latest&preserve-view=true), logické, [matematické](/sql/t-sql/functions/mathematical-functions-transact-sql?view=azure-sqldw-latest&preserve-view=true) funkce, s výjimkou [příkazu Choose](/sql/t-sql/functions/logical-functions-choose-transact-sql?view=azure-sqldw-latest&preserve-view=true), [IIf](/sql/t-sql/functions/logical-functions-iif-transact-sql?view=azure-sqldw-latest&preserve-view=true)a [Parse](/sql/t-sql/functions/parse-transact-sql?view=azure-sqldw-latest&preserve-view=true) | Ano, všechny [analytické](/sql/t-sql/functions/analytic-functions-transact-sql?view=azure-sqldw-latest&preserve-view=true), převodní, [Datum a čas](/sql/t-sql/functions/date-and-time-data-types-and-functions-transact-sql?view=azure-sqldw-latest&preserve-view=true)v jazyce Transact-SQL, logické a [matematické](/sql/t-sql/functions/mathematical-functions-transact-sql?view=azure-sqldw-latest&preserve-view=true) funkce. |
| **Předdefinované funkce (text)** | Ano. Všechny funkce jazyka Transact-SQL [String](/sql/t-sql/functions/string-functions-transact-sql?view=azure-sqldw-latest&preserve-view=true), [JSON](/sql/t-sql/functions/json-functions-transact-sql?view=azure-sqldw-latest&preserve-view=true)a kolace s výjimkou [STRING_ESCAPE](/sql/t-sql/functions/string-escape-transact-sql?view=azure-sqldw-latest&preserve-view=true) a [překladu](/sql/t-sql/functions/translate-transact-sql?view=azure-sqldw-latest&preserve-view=true) | Ano. Všechny funkce Transact-SQL [String](/sql/t-sql/functions/string-functions-transact-sql?view=azure-sqldw-latest&preserve-view=true), [JSON](/sql/t-sql/functions/json-functions-transact-sql?view=azure-sqldw-latest&preserve-view=true)a kolace. |
| **Předdefinované funkce tabulky – hodnota** | Ano, [funkce sady řádků Transact-SQL](/sql/t-sql/functions/functions?view=azure-sqldw-latest&preserve-view=true#rowset-functions), s výjimkou formátu [OPENXML](/sql/t-sql/functions/openxml-transact-sql?view=azure-sqldw-latest&preserve-view=true), [OpenDataSource](/sql/t-sql/functions/opendatasource-transact-sql?view=azure-sqldw-latest&preserve-view=true), [OtevřítDotaz](/sql/t-sql/functions/openquery-transact-sql?view=azure-sqldw-latest&preserve-view=true)a [OpenRowset](/sql/t-sql/functions/openrowset-transact-sql?view=azure-sqldw-latest&preserve-view=true) | Ano, [funkce sady řádků Transact-SQL](/sql/t-sql/functions/functions?view=azure-sqldw-latest&preserve-view=true#rowset-functions), s výjimkou formátu [OPENXML](/sql/t-sql/functions/openxml-transact-sql?view=azure-sqldw-latest&preserve-view=true), [OpenDataSource](/sql/t-sql/functions/opendatasource-transact-sql?view=azure-sqldw-latest&preserve-view=true)a [OtevřítDotaz](/sql/t-sql/functions/openquery-transact-sql?view=azure-sqldw-latest&preserve-view=true)  |
| **Agregace** |  Předdefinované agregované hodnoty jazyka Transact-SQL s výjimkou [CHECKSUM_AGG](/sql/t-sql/functions/checksum-agg-transact-sql?view=azure-sqldw-latest&preserve-view=true) a [GROUPING_ID](/sql/t-sql/functions/grouping-id-transact-sql?view=azure-sqldw-latest&preserve-view=true) | Předdefinované agregace jazyka Transact-SQL. |
| **Operátory** | Ano, všechny [operátory jazyka Transact-SQL](/sql/t-sql/language-elements/operators-transact-sql?view=azure-sqldw-latest&preserve-view=true) s výjimkou [! >](/sql/t-sql/language-elements/not-greater-than-transact-sql?view=azure-sqldw-latest&preserve-view=true) a [! <](/sql/t-sql/language-elements/not-less-than-transact-sql?view=azure-sqldw-latest&preserve-view=true) | Ano, všechny [operátory jazyka Transact-SQL](/sql/t-sql/language-elements/operators-transact-sql?view=azure-sqldw-latest&preserve-view=true)  |
| **Řízení toku** | Ano. Všechny [příkazy Transact-SQL Control-of-Flow](/sql/t-sql/language-elements/control-of-flow?view=azure-sqldw-latest&preserve-view=true) s výjimkou [pokračování](/sql/t-sql/language-elements/continue-transact-sql?view=azure-sqldw-latest&preserve-view=true), [goto](/sql/t-sql/language-elements/goto-transact-sql?view=azure-sqldw-latest&preserve-view=true), [return](/sql/t-sql/language-elements/return-transact-sql?view=azure-sqldw-latest&preserve-view=true), [Use](/sql/t-sql/language-elements/use-transact-sql?view=azure-sqldw-latest&preserve-view=true)a [waitfor](/sql/t-sql/language-elements/waitfor-transact-sql?view=azure-sqldw-latest&preserve-view=true) | Ano. Všechny [příkazy Transact-SQL Control-of-Flow](/sql/t-sql/language-elements/control-of-flow?view=azure-sqldw-latest&preserve-view=true) – výběr dotazu v `WHILE (...)` podmínce |
| **Příkazy DDL (vytvoření, změna, vyřazení)** | Ano. Všechny příkazy jazyka Transact-SQL DDL použitelné pro podporované typy objektů | Ano. Všechny příkazy jazyka Transact-SQL DDL použitelné pro podporované typy objektů |

## <a name="security"></a>Zabezpečení

Synapse SQL vám umožní používat integrované funkce zabezpečení k zabezpečení dat a řízení přístupu. V následující tabulce jsou porovnávány rozdíly vysoké úrovně mezi synapsemi modely spotřeby SQL.

|   | Vyhrazená | Bez serveru |
| --- | --- | --- |
| **Přihlášení** | Není k dispozici (databáze podporuje pouze obsažené uživatele) | Yes |
| **Uživatelé** |  Není k dispozici (databáze podporuje pouze obsažené uživatele) | Yes |
| **[Uživatelé s omezením](/sql/relational-databases/security/contained-database-users-making-your-database-portable?view=azure-sqldw-latest&preserve-view=true)** | Ano. **Poznámka:** správce může být bez omezení jenom u jednoho uživatele Azure AD. | No |
| **Ověřování uživatelského jména a hesla SQL**| Yes | Yes |
| **Ověřování Azure Active Directory (Azure AD)**| Ano, uživatelé Azure AD | Ano, přihlášení a uživatelé Azure AD |
| **Předávací ověřování pro službu Storage Azure Active Directory (Azure AD)** | Yes | Yes |
| **Ověřování tokenu SAS úložiště** | No | Ano, použití [přihlašovacích údajů s rozsahem databáze](/sql/t-sql/statements/create-database-scoped-credential-transact-sql?view=azure-sqldw-latest&preserve-view=true) v [externím zdroji dat](/sql/t-sql/statements/create-external-data-source-transact-sql?view=azure-sqldw-latest&preserve-view=true) nebo [přihlašovacích údajích](/sql/t-sql/statements/create-credential-transact-sql?view=azure-sqldw-latest&preserve-view=true)na úrovni instance. |
| **Ověřování přístupového klíče k úložišti** | Ano, použití [přihlašovacích údajů v oboru databáze](/sql/t-sql/statements/create-database-scoped-credential-transact-sql?view=azure-sqldw-latest&preserve-view=true) v [EXTERNÍm zdroji dat](/sql/t-sql/statements/create-external-data-source-transact-sql?view=azure-sqldw-latest&preserve-view=true) | No |
| **Ověřování [identity spravovaného](../security/synapse-workspace-managed-identity.md) úložištěm** | Ano, pomocí [Identita spravované služby přihlašovací údaje](../../azure-sql/database/vnet-service-endpoint-rule-overview.md?bc=%2fazure%2fsynapse-analytics%2fbreadcrumb%2ftoc.json&preserve-view=true&toc=%2fazure%2fsynapse-analytics%2ftoc.json&view=azure-sqldw-latest&preserve-view=true) | Ano, pomocí `Managed Identity` přihlašovacích údajů. |
| **Ověřování identity aplikace úložiště** | [Ano](/sql/t-sql/statements/create-external-data-source-transact-sql?view=azure-sqldw-latest&preserve-view=true) | No |
| **Oprávnění – úroveň objektu** | Ano, včetně možnosti udělit, odepřít a odvolat oprávnění uživatelům | Ano, včetně možnosti udělit, odepřít a odvolat oprávnění pro uživatele nebo přihlašovací údaje u systémových objektů, které jsou podporovány |
| **Oprávnění – úroveň schématu** | Ano, včetně možnosti udělit, odepřít a odvolat oprávnění uživatelům nebo přihlášením ve schématu | Ano, včetně možnosti udělit, odepřít a odvolat oprávnění uživatelům nebo přihlášením ve schématu |
| **Oprávnění – na [úrovni databáze](/sql/relational-databases/security/authentication-access/database-level-roles?view=azure-sqldw-latest&preserve-view=true)** | Yes | Yes |
| **Oprávnění – na [úrovni serveru](/sql/relational-databases/security/authentication-access/server-level-roles)** | No | Ano, sysadmin a další serverové role jsou podporovány |
| **Oprávnění – [zabezpečení na úrovni sloupců](../sql-data-warehouse/column-level-security.md?bc=%2fazure%2fsynapse-analytics%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fsynapse-analytics%2ftoc.json)** | Yes | Yes |
| **Role/skupiny** | Ano (databáze s rozsahem) | Ano (u serveru i z oboru databáze) |
| **&amp;Funkce identity zabezpečení** | Některé funkce a operátory zabezpečení jazyka Transact-SQL:  `CURRENT_USER` , `HAS_DBACCESS` , `IS_MEMBER` , `IS_ROLEMEMBER` , `SESSION_USER` , `SUSER_NAME` , `SUSER_SNAME` , `SYSTEM_USER` , `USER` , `USER_NAME` , `EXECUTE AS` , `OPEN/CLOSE MASTER KEY` | Některé funkce a operátory zabezpečení jazyka Transact-SQL:  `CURRENT_USER` , `HAS_DBACCESS` , `HAS_PERMS_BY_NAME` , `IS_MEMBER', 'IS_ROLEMEMBER` , `IS_SRVROLEMEMBER` , `SESSION_USER` , `SESSION_CONTEXT` , `SUSER_NAME` , `SUSER_SNAME` , `SYSTEM_USER` , `USER` ,, a `USER_NAME` `EXECUTE AS` `REVERT` . Funkce zabezpečení nelze použít k dotazování na externí data (uložení výsledku do proměnné, kterou lze použít v dotazu).  |
| **POVĚŘENÍ V OBORU DATABÁZE** | Yes | Yes |
| **PŘIHLAŠOVACÍ ÚDAJE V OBORU SERVERU** | No | Yes |
| **Zabezpečení na úrovni řádků** | [Ano](/sql/relational-databases/security/row-level-security?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) | No |
| **Transparentní šifrování dat** | [Ano](../../azure-sql/database/transparent-data-encryption-tde-overview.md) | No | 
| **Zjišťování a klasifikace dat** | [Ano](../../azure-sql/database/data-discovery-and-classification-overview.md) | No |
| **Posouzení ohrožení zabezpečení** | [Ano](../../azure-sql/database/sql-vulnerability-assessment.md) | No |
| **Advanced Threat Protection** | [Ano](../../azure-sql/database/threat-detection-overview.md)
| **Auditování** | [Ano](../../azure-sql/database/auditing-overview.md) | No |
| **[Pravidla brány firewall](../security/synapse-workspace-ip-firewall.md)**| Yes | Yes |
| **[Soukromý koncový bod](../security/synapse-workspace-managed-private-endpoints.md)**| Yes | Yes |

Vyhrazený fond SQL a SQL Server bez serveru používají standardní jazyk Transact-SQL k dotazování dat. Podrobné rozdíly najdete v [referenčních informacích k jazyku Transact-SQL](/sql/t-sql/language-reference).

## <a name="tools"></a>nástroje

Můžete použít různé nástroje pro připojení k synapse SQL k dotazování dat.

|   | Vyhrazená | Bez serveru |
| --- | --- | --- |
| **Synapse Studio** | Ano, skripty SQL | Ano, skripty SQL |
| **Power BI** | Yes | [Ano](tutorial-connect-power-bi-desktop.md) |
| **Služba Azure Analysis** | Yes | Yes |
| **Azure Data Studio** | Yes | Ano, verze 1.18.0 nebo vyšší. Podporují se skripty SQL a notebooky SQL. |
| **SQL Server Management Studio** | Yes | Ano, verze 18,5 nebo vyšší |

> [!NOTE]
> SSMS se dá použít pro připojení k fondu SQL bez serveru a dotazům. Od verze 18,5 se tato podpora částečně podporuje a můžete ji použít jenom pro připojení a dotazování.

Většina aplikací používá standardní jazyk Transact-SQL, který se může dotazovat na vyhrazené a neserverové modely spotřeby synapse SQL.

## <a name="storage-options"></a>Možnosti úložiště

Data, která se analyzují, se dají ukládat do různých typů úložiště. V následující tabulce jsou uvedeny všechny dostupné možnosti úložiště:

|   | Vyhrazená | Bez serveru |
| --- | --- | --- |
| **Interní úložiště** | Yes | No |
| **Azure Data Lake v2** | Yes | Yes |
| **Azure Blob Storage** | Yes | Yes |
| **Azure SQL (vzdálené)** | No | No |
| **Transakční úložiště Azure CosmosDB** | No | No |
| **Analytické úložiště Azure CosmosDB** | No | Ano, pomocí [odkazu synapse (verze Preview)](../../cosmos-db/synapse-link.md?bc=%2fazure%2fsynapse-analytics%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fsynapse-analytics%2ftoc.json) ([Public Preview](../../cosmos-db/synapse-link.md?bc=%2fazure%2fsynapse-analytics%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fsynapse-analytics%2ftoc.json#limitations)) |
| **Apache Spark tabulky (v pracovním prostoru)** | No | PARQUET tabulky pouze pomocí [synchronizace metadat](develop-storage-files-spark-tables.md) |
| **Apache Spark tabulky (vzdálené)** | No | No |
| **Tabulky datacihly (vzdálené)** | No | No |

## <a name="data-formats"></a>Formáty dat

Data, která se analyzují, se dají ukládat v různých formátech úložiště. V následující tabulce jsou uvedeny všechny dostupné formáty dat, které lze analyzovat:

|   | Vyhrazená | Bez serveru |
| --- | --- | --- |
| **Oddělených** | [Ano](/sql/t-sql/statements/create-external-file-format-transact-sql?view=azure-sqldw-latest&preserve-view=true) | [Ano](query-single-csv-file.md) |
| **Formát** | Ano (oddělovače s více znaky se nepodporují.) | [Ano](query-single-csv-file.md) |
| **Parquet** | [Ano](/sql/t-sql/statements/create-external-file-format-transact-sql?view=azure-sqldw-latest&preserve-view=true) | [Ano](query-parquet-files.md), včetně souborů s [vnořenými typy](query-parquet-nested-types.md) |
| **ORC podregistru** | [Ano](/sql/t-sql/statements/create-external-file-format-transact-sql?view=azure-sqldw-latest&preserve-view=true) | No |
| **Podregistr RC** | [Ano](/sql/t-sql/statements/create-external-file-format-transact-sql?view=azure-sqldw-latest&preserve-view=true) | No |
| **JSON** | Yes | [Ano](query-json-files.md) |
| **Avro** | No | No |
| **[Rozdíl – Lake](https://delta.io/)** | No | No |
| **[CDM](/common-data-model/)** | No | No |

## <a name="next-steps"></a>Další kroky
Další informace o osvědčených postupech pro vyhrazený fond SQL a SQL Server bez serveru najdete v následujících článcích:

- [Osvědčené postupy pro vyhrazený fond SQL](best-practices-dedicated-sql-pool.md)
- [Osvědčené postupy pro fond SQL bez serveru](best-practices-serverless-sql-pool.md)
