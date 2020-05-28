---
title: Klíčové rozdíly pro Machine Learning Services (Preview)
description: Toto téma popisuje klíčové rozdíly mezi Machine Learning Services ve službě Azure SQL Managed instance a SQL Server Machine Learning Services.
services: sql-database
ms.service: sql-database
ms.subservice: machine-learning
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: garyericson
ms.author: garye
ms.reviewer: carlrab, davidph
manager: cgronlun
ms.date: 05/27/2020
ms.openlocfilehash: 02211a09df2d2744119ea8fbe6f0ecaa63dba020
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/27/2020
ms.locfileid: "84045106"
---
# <a name="key-differences-between-machine-learning-services-in-azure-sql-managed-instance-and-sql-server"></a>Klíčové rozdíly mezi Machine Learning Services ve službě Azure SQL Managed instance a SQL Server

Funkce [Machine Learning Services ve službě Azure SQL Managed instance (Preview)](machine-learning-services-overview.md) je skoro stejná jako [SQL Server Machine Learning Services](https://docs.microsoft.com/sql/advanced-analytics/what-is-sql-server-machine-learning). Níže jsou uvedeny některé klíčové rozdíly.

> [!IMPORTANT]
> Machine Learning Services ve službě Azure SQL Managed instance je aktuálně ve verzi Public Preview. Pokud se chcete zaregistrovat, přečtěte si téma [registrace verze Preview](machine-learning-services-overview.md#signup).

## <a name="preview-limitations"></a>Omezení verze Preview

Ve verzi Preview má služba tato omezení:

- Připojení zpětné smyčky nefungují (Další informace najdete v tématu [připojení ke zpětné smyčce SQL Server ze skriptu Python nebo R](/sql/machine-learning/connect/loopback-connection)).
- Externí fondy zdrojů se nepodporují.
- Podporují se jenom Python a R. Externí jazyky, jako je Java, se nedají přidat.
- Scénáře využívající rozhraní MPI (Message Passing [Interface](https://docs.microsoft.com/message-passing-interface/microsoft-mpi) ) nejsou podporovány.

V případě aktualizace pro objekt na úrovni služby (SLO) aktualizujte objekt SLO a vyvolejte lístek podpory pro opětovné povolení omezení vyhrazených prostředků pro R/Python.

## <a name="language-support"></a>Podpora jazyků

Machine Learning Services ve spravované instanci SQL a SQL Server podporují [architekturu rozšiřitelnosti](https://docs.microsoft.com/sql/advanced-analytics/concepts/extensibility-framework)Python i R. Hlavní rozdíly:

- Počáteční verze Pythonu a R se liší od Machine Learning Services spravované instance SQL a SQL Server:

  |                      | Python | R     |
  |----------------------|--------|-------|
  | Spravovaná instance SQL | 3.7.1  | bodu |
  | SQL Server           | bodu  | body |

- Není nutné konfigurovat `external scripts enabled` prostřednictvím `sp_configure` . Po [zaregistrování](machine-learning-services-overview.md#signup) verze Preview je pro vaši databázi SQL povolené Machine Learning.

## <a name="packages"></a>Balíčky

Správa balíčku Pythonu a R funguje odlišně mezi spravovanými instancemi SQL a SQL Server. Jsou to tyto rozdíly:

- Balíčky nemůžou provádět odchozí síťová volání. Toto omezení se podobá [výchozím pravidlům brány firewall pro Machine Learning Services](https://docs.microsoft.com//sql/advanced-analytics/security/firewall-configuration) v SQL Server, ale nedá se změnit ve spravované instanci SQL.
- Neexistuje žádná podpora pro balíčky, které závisí na externích modulech runtime (jako Java), nebo při instalaci nebo použití nepotřebují přístup k rozhraním API operačního systému.

Další informace o správě balíčků Python a R najdete v těchto tématech:

- [Získat informace o balíčku Pythonu](https://docs.microsoft.com/sql/machine-learning/package-management/python-package-information?context=azure/sql-database/context/ml-context&view=sql-server-ver15)
- [Získat informace o balíčku R](https://docs.microsoft.com/sql/machine-learning/package-management/r-package-information?context=azure/sql-database/context/ml-context&view=sql-server-ver15)

## <a name="resource-governance"></a>Zásady správného řízení prostředků

Prostředky R není možné omezit prostřednictvím [Správce prostředků](https://docs.microsoft.com/sql/relational-databases/resource-governor/resource-governor) a externích fondů zdrojů.

V rámci verze Public Preview jsou prostředky R nastavené na maximálně 20% prostředků spravované instance SQL a závisí na zvolené úrovni služby. Další informace najdete v tématu [Azure SQL Database nákup modelů](https://docs.microsoft.com/azure/sql-database/sql-database-service-tiers).

### <a name="insufficient-memory-error"></a>Nedostatek paměti – chyba

Pokud pro R není k dispozici dostatek paměti, zobrazí se chybová zpráva. Běžné chybové zprávy jsou:

- Nelze komunikovat s modulem runtime pro skript R pro ID požadavku: * * * * * * *. Zkontrolujte prosím požadavky modulu runtime R.
- Při provádění sp_execute_external_script s HRESULT 0x80004004 došlo k chybě skriptu R. ... došlo k chybě externího skriptu: ".. Nelze přidělit paměť (0 MB) ve funkci C R_AllocStringBuffer.
- Došlo k chybě externího skriptu: Chyba: Nelze přidělit vektor velikosti.

Využití paměti závisí na tom, kolik je použito ve skriptech jazyka R a počet paralelně spuštěných paralelních dotazů. Pokud obdržíte výše uvedené chyby, můžete škálovat databázi na vyšší úroveň služby a vyřešit ji.

## <a name="next-steps"></a>Další kroky

- Podívejte se na přehled [Machine Learning Services ve spravované instanci SQL Azure](machine-learning-services-overview.md).
- Informace o použití Pythonu v Machine Learning Services najdete v tématu [spuštění skriptů Pythonu](https://docs.microsoft.com/sql/machine-learning/tutorials/quickstart-python-create-script?context=/azure/azure-sql/managed-instance/context/ml-context&view=sql-server-ver15).
- Informace o použití R v Machine Learning Services najdete v tématu [spuštění skriptů r](https://docs.microsoft.com/sql/machine-learning/tutorials/quickstart-r-create-script?context=/azure/azure-sql/managed-instance/context/ml-context&view=sql-server-ver15).
