---
title: Klíčové rozdíly pro Machine Learning Services (Preview)
description: Toto téma popisuje klíčové rozdíly mezi Azure SQL Database Machine Learning Services (s R) a SQL Server Machine Learning Services.
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "81453145"
---
# <a name="key-differences-between-machine-learning-services-in-azure-sql-database-preview-and-sql-server"></a>Klíčové rozdíly mezi Machine Learning Services v Azure SQL Database (Preview) a SQL Server

Funkce Azure SQL Database Machine Learning Services (s R) v (Preview) se podobá [SQL Server Machine Learning Services](https://docs.microsoft.com/sql/advanced-analytics/what-is-sql-server-machine-learning). Níže jsou uvedeny některé klíčové rozdíly.

[!INCLUDE[ml-preview-note](../../includes/sql-database-ml-preview-note.md)]

## <a name="language-support"></a>Podpora jazyků

SQL Server podporuje jazyk R a Python prostřednictvím [rozhraní rozšiřitelnosti](https://docs.microsoft.com/sql/advanced-analytics/concepts/extensibility-framework). SQL Database nepodporuje oba jazyky. Hlavní rozdíly:

- Jazyk R je jediným podporovaným jazykem v SQL Database. Python se momentálně nepodporuje.
- Verze R je 3.4.4.
- Není nutné konfigurovat `external scripts enabled` prostřednictvím `sp_configure`.

## <a name="package-management"></a>Správa balíčků

Správa balíčků R a instalace se liší od SQL Database a SQL Server. Jsou to tyto rozdíly:

- Balíčky R se instalují přes [sqlmlutils](https://github.com/Microsoft/sqlmlutils) nebo [vytvářejí externí knihovny](https://docs.microsoft.com/sql/t-sql/statements/create-external-library-transact-sql).
- Balíčky nemůžou provádět odchozí síťová volání. Toto omezení se podobá [výchozím pravidlům brány firewall pro Machine Learning Services](https://docs.microsoft.com//sql/advanced-analytics/security/firewall-configuration) v SQL Server, ale nedá se změnit v SQL Database.
- Neexistuje žádná podpora pro balíčky, které závisí na externích modulech runtime (jako Java), nebo při instalaci nebo použití nepotřebují přístup k rozhraním API operačního systému.

## <a name="writing-to-a-temporary-table"></a>Zápis do dočasné tabulky

Pokud používáte RODBC v Azure SQL Database, nemůžete zapisovat do dočasné tabulky, ať už je vytvořená uvnitř nebo mimo `sp_execute_external_script` relaci. Alternativním řešením je použití [RxOdbcData](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/rxodbcdata) a [rxDataStep](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/rxdatastep) (s přepsáním = false a Append = "Rows") k zápisu do globální dočasné tabulky vytvořené před `sp_execute_external_script` dotazem.

## <a name="resource-governance"></a>Zásady správného řízení prostředků

Prostředky R není možné omezit prostřednictvím [Správce prostředků](https://docs.microsoft.com/sql/relational-databases/resource-governor/resource-governor) a externích fondů zdrojů.

V rámci verze Public Preview jsou prostředky R nastavené na maximálně 20% SQL Databasech prostředků a závisí na zvolené úrovni služby. Další informace najdete v tématu [Azure SQL Database nákup modelů](https://docs.microsoft.com/azure/sql-database/sql-database-service-tiers).
### <a name="insufficient-memory-error"></a>Nedostatek paměti – chyba

Pokud pro R není k dispozici dostatek paměti, zobrazí se chybová zpráva. Běžné chybové zprávy jsou:

- Nelze komunikovat s modulem runtime pro skript R pro ID požadavku: * * * * * * *. Zkontrolujte prosím požadavky modulu runtime R.
- Při provádění sp_execute_external_script s HRESULT 0x80004004 došlo k chybě skriptu R. ... došlo k chybě externího skriptu: ".. Nelze přidělit paměť (0 MB) ve funkci C R_AllocStringBuffer.
- Došlo k chybě externího skriptu: Chyba: Nelze přidělit vektor velikosti.

Využití paměti závisí na tom, kolik je použito ve skriptech jazyka R a počet paralelně spuštěných paralelních dotazů. Pokud obdržíte výše uvedené chyby, můžete škálovat databázi na vyšší úroveň služby a vyřešit ji.

## <a name="next-steps"></a>Další kroky

- Podívejte se na přehled [Azure SQL Database Machine Learning Services s R (Preview)](sql-database-machine-learning-services-overview.md).
- Pokud se chcete dozvědět, jak použít R k dotazování Azure SQL Database Machine Learning Services (Preview), přečtěte si [příručku pro rychlý Start](sql-database-connect-query-r.md).
- Pokud chcete začít s některými jednoduchými skripty jazyka R, přečtěte si téma [Vytvoření a spuštění jednoduchých skriptů r v Azure SQL Database Machine Learning Services (Preview)](sql-database-quickstart-r-create-script.md).
