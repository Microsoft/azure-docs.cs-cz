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
ms.date: 03/01/2019
ms.openlocfilehash: 1397f5d81ddf63740d733111b965a0517a2b917f
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/08/2019
ms.locfileid: "73827466"
---
# <a name="key-differences-between-machine-learning-services-in-azure-sql-database-preview-and-sql-server"></a>Klíčové rozdíly mezi Machine Learning Services v Azure SQL Database (Preview) a SQL Server

Funkce Azure SQL Database Machine Learning Services (s R) v (Preview) se podobá [SQL Server Machine Learning Services](https://docs.microsoft.com/sql/advanced-analytics/what-is-sql-server-machine-learning). Níže jsou uvedeny některé klíčové rozdíly.

> [!IMPORTANT]
> Azure SQL Database Machine Learning Services je aktuálně ve verzi Public Preview.
> Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro úlohy v produkčním prostředí. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti.
> Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="language-support"></a>Podpora jazyků

SQL Server podporuje jazyk R a Python prostřednictvím [rozhraní rozšiřitelnosti](https://docs.microsoft.com/sql/advanced-analytics/concepts/extensibility-framework). SQL Database nepodporuje oba jazyky. Hlavní rozdíly:

- Jazyk R je jediným podporovaným jazykem v SQL Database. Python se momentálně nepodporuje.
- Verze R je 3.4.4.
- `external scripts enabled` přes `sp_configure`není potřeba konfigurovat. Po [registraci](sql-database-machine-learning-services-overview.md#signup)bude pro vaši databázi SQL povolený strojové učení.

## <a name="package-management"></a>Správa balíčků

Správa balíčků R a instalace se liší od SQL Database a SQL Server. Jsou to tyto rozdíly:

- Balíčky R se instalují přes [sqlmlutils](https://github.com/Microsoft/sqlmlutils) nebo [vytvářejí externí knihovny](https://docs.microsoft.com/sql/t-sql/statements/create-external-library-transact-sql).
- Balíčky nemůžou provádět odchozí síťová volání. Toto omezení se podobá [výchozím pravidlům brány firewall pro Machine Learning Services](https://docs.microsoft.com//sql/advanced-analytics/security/firewall-configuration) v SQL Server, ale nedá se změnit v SQL Database.
- Neexistuje žádná podpora pro balíčky, které závisí na externích modulech runtime (jako Java), nebo při instalaci nebo použití nepotřebují přístup k rozhraním API operačního systému.

## <a name="writing-to-a-temporary-table"></a>Zápis do dočasné tabulky

Pokud používáte RODBC v Azure SQL Database, nemůžete zapisovat do dočasné tabulky, ať už je vytvořená v relaci `sp_execute_external_script` nebo mimo ni. Alternativním řešením je použití [RxOdbcData](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/rxodbcdata) a [rxDataStep](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/rxdatastep) (s přepsáním = false a Append = "Rows") k zápisu do globální dočasné tabulky vytvořené před dotazem `sp_execute_external_script`.

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
