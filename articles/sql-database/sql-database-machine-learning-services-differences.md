---
title: Klíčové rozdíly ve službách strojového učení (preview)
description: Toto téma popisuje klíčové rozdíly mezi službou Azure SQL Database Machine Learning Services (s R) a službou SQL Server Machine Learning Services.
services: sql-database
ms.service: sql-database
ms.subservice: machine-learning
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: dphansen
ms.author: davidph
ms.reviewer: carlrab
manager: cgronlun
ms.date: 11/20/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 34ba75b6126024c9cd43d6fe474f7c1b62dd990f
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81453145"
---
# <a name="key-differences-between-machine-learning-services-in-azure-sql-database-preview-and-sql-server"></a>Klíčové rozdíly mezi službami Machine Learning Services v Azure SQL Database (preview) a SQL Serveru

Funkce služby Azure SQL Database Machine Learning Services (s R) v (preview) je podobná [sql server machine learning services](https://docs.microsoft.com/sql/advanced-analytics/what-is-sql-server-machine-learning). Níže jsou uvedeny některé klíčové rozdíly.

[!INCLUDE[ml-preview-note](../../includes/sql-database-ml-preview-note.md)]

## <a name="language-support"></a>Podpora jazyků

SQL Server má podporu pro R a Python prostřednictvím [rozhraní rozšiřitelnosti](https://docs.microsoft.com/sql/advanced-analytics/concepts/extensibility-framework). SQL Database nepodporuje oba jazyky. Hlavní rozdíly jsou:

- R je jediný podporovaný jazyk v databázi SQL. Python se momentálně nepodporuje.
- Verze R je 3.4.4.
- Není třeba konfigurovat `external scripts enabled` přes `sp_configure`.

## <a name="package-management"></a>Správa balíčků

Správa balíčků R a instalace práce se liší mezi SQL Database a SQL Server. Tyto rozdíly jsou:

- Balíčky R jsou nainstalovány prostřednictvím [sqlmlutils](https://github.com/Microsoft/sqlmlutils) nebo [CREATE EXTERNAL LIBRARY](https://docs.microsoft.com/sql/t-sql/statements/create-external-library-transact-sql).
- Balíčky nemohou provádět odchozí síťová volání. Toto omezení je podobné [výchozím pravidlům brány firewall pro služby Machine Learning Services](https://docs.microsoft.com//sql/advanced-analytics/security/firewall-configuration) v serveru SQL Server, ale nelze je změnit v databázi SQL.
- Neexistuje žádná podpora pro balíčky, které závisí na externích runtimes (jako je Java) nebo potřebují přístup k os API pro instalaci nebo použití.

## <a name="writing-to-a-temporary-table"></a>Zápis do dočasné tabulky

Pokud používáte rod rod v Azure SQL Database, pak nelze zapisovat do dočasné tabulky, `sp_execute_external_script` ať už je vytvořen uvnitř nebo vně relace. Řešení je použít [RxOdbcData](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/rxodbcdata) a [rxDataStep](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/rxdatastep) (s overwrite=FALSE a append="rows") k zápisu do globální dočasné tabulky vytvořené před dotazem. `sp_execute_external_script`

## <a name="resource-governance"></a>Zásady správného řízení prostředků

Není možné omezit prostředky R prostřednictvím [správce prostředků](https://docs.microsoft.com/sql/relational-databases/resource-governor/resource-governor) a externífondy prostředků.

Během verze Public Preview jsou prostředky R nastaveny na maximálně 20 % prostředků databáze SQL a závisí na tom, kterou úroveň služby zvolíte. Další informace naleznete v [tématu Nákupní modely Azure SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-service-tiers).
### <a name="insufficient-memory-error"></a>Nedostatečná chyba paměti

Pokud není k dispozici dostatek paměti pro R, zobrazí se chybová zpráva. Běžné chybové zprávy jsou:

- Nelze komunikovat s runtime pro skript 'R' pro id požadavku: *******. Zkontrolujte prosím požadavky na runtime "R"
- Při provádění sp_execute_external_script s HRESULT 0x80004004 došlo k chybě skriptu R. ... došlo k chybě externího skriptu: ".. Nelze přidělit paměť (0 Mb) ve funkci C 'R_AllocStringBuffer'"
- Došlo k chybě externího skriptu: Chyba: nelze přidělit vektor velikosti.

Využití paměti závisí na tom, kolik se používá ve skriptech R a počet paralelních dotazů, které jsou prováděny. Pokud se zobrazí výše uvedené chyby, můžete škálovat databázi na vyšší úroveň služby k vyřešení tohoto problému.

## <a name="next-steps"></a>Další kroky

- Podívejte se na přehled [Služby strojového učení Azure SQL Database s R (preview).](sql-database-machine-learning-services-overview.md)
- Informace o použití jazyka R k dotazování služby Azure SQL Database Machine Learning Services (preview) najdete v [příručce K Rychlému startu](sql-database-connect-query-r.md).
- Pokud chcete začít s některými jednoduchými skripty R, přečtěte si část [Vytvoření a spuštění jednoduchých skriptů Jazyka R ve službě Azure SQL Database Machine Learning Services (preview).](sql-database-quickstart-r-create-script.md)
