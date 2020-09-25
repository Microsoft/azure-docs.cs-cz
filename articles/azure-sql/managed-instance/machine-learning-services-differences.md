---
title: Klíčové rozdíly pro Machine Learning Services (Preview)
description: Toto téma popisuje klíčové rozdíly mezi Machine Learning Services ve službě Azure SQL Managed instance a SQL Server Machine Learning Services.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: machine-learning
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: garyericson
ms.author: garye
ms.reviewer: sstein, davidph
manager: cgronlun
ms.date: 05/27/2020
ms.openlocfilehash: 9ff2de18042c466bdd8fa6c71194fff4286c820d
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2020
ms.locfileid: "91325092"
---
# <a name="key-differences-between-machine-learning-services-in-azure-sql-managed-instance-and-sql-server"></a>Klíčové rozdíly mezi Machine Learning Services ve službách Azure SQL Managed Instance a SQL Server

Funkce [Machine Learning Services ve službě Azure SQL Managed instance (Preview)](machine-learning-services-overview.md) je skoro stejná jako [SQL Server Machine Learning Services](https://docs.microsoft.com/sql/advanced-analytics/what-is-sql-server-machine-learning). Níže jsou uvedeny některé klíčové rozdíly.

> [!IMPORTANT]
> Machine Learning Services ve službě Azure SQL Managed instance je aktuálně ve verzi Public Preview. Pokud se chcete zaregistrovat, přečtěte si téma [registrace verze Preview](machine-learning-services-overview.md#signup).

## <a name="preview-limitations"></a>Omezení verze Preview

Ve verzi Preview platí pro tuto službu následující omezení:

- Připojení zpětné smyčky nefungují (Další informace najdete v tématu [připojení ke zpětné smyčce SQL Server ze skriptu Python nebo R](/sql/machine-learning/connect/loopback-connection)).
- Externí fondy prostředků se nepodporují.
- Podporují se pouze jazyky Python a R. Externí jazyky, jako je Java, není možné přidat.
- Scénáře využívající rozhraní MPI (Message Passing [Interface](https://docs.microsoft.com/message-passing-interface/microsoft-mpi) ) nejsou podporovány.

V případě aktualizace cíle úrovně služby (SLO) aktualizujte prosím SLO a vytvořte lístek podpory pro opětovné povolení limitů vyhrazených prostředků pro R/Python.

## <a name="language-support"></a>Podpora jazyků

Machine Learning Services ve spravované instanci SQL a SQL Server podporují [architekturu rozšiřitelnosti](https://docs.microsoft.com/sql/advanced-analytics/concepts/extensibility-framework)Python i R. Hlavní rozdíly:

- Počáteční verze Pythonu a R se liší od Machine Learning Services spravované instance SQL a SQL Server:

  | Systém               | Python | R     |
  |----------------------|--------|-------|
  | Spravovaná instance SQL | 3.7.1  | bodu |
  | SQL Server           | bodu  | body |

- Není nutné konfigurovat `external scripts enabled` prostřednictvím `sp_configure` . Po [zaregistrování](machine-learning-services-overview.md#signup) verze Preview je Machine Learning povolený pro spravovanou instanci Azure SQL.

## <a name="packages"></a>Balíčky

Správa balíčku Pythonu a R funguje odlišně mezi spravovanými instancemi SQL a SQL Server. Jsou to tyto rozdíly:

- Balíčky nemůžou provádět odchozí síťová volání. Toto omezení se podobá [výchozím pravidlům brány firewall pro Machine Learning Services](https://docs.microsoft.com//sql/advanced-analytics/security/firewall-configuration) v SQL Server, ale nedá se změnit ve spravované instanci SQL.
- Neexistuje žádná podpora pro balíčky, které závisí na externích modulech runtime (jako Java), nebo při instalaci nebo použití nepotřebují přístup k rozhraním API operačního systému.

Další informace o správě balíčků Python a R najdete v těchto tématech:

- [Získání informací o balíčku Pythonu](https://docs.microsoft.com/sql/machine-learning/package-management/python-package-information?context=azure/sql-database/context/ml-context&view=sql-server-ver15)
- [Získání informací o balíčku jazyka R](https://docs.microsoft.com/sql/machine-learning/package-management/r-package-information?context=azure/sql-database/context/ml-context&view=sql-server-ver15)

## <a name="resource-governance"></a>Zásady správného řízení prostředků

Prostředky R není možné omezit prostřednictvím [Správce prostředků](https://docs.microsoft.com/sql/relational-databases/resource-governor/resource-governor) a externích fondů zdrojů.

Ve verzi Public Preview jsou prostředky R nastavené na maximálně 20 % prostředků SQL Managed Instance a závisí na tom, jakou úroveň služby zvolíte. Další informace najdete v tématu [Azure SQL Database nákup modelů](https://docs.microsoft.com/azure/sql-database/sql-database-service-tiers).

### <a name="insufficient-memory-error"></a>Nedostatek paměti – chyba

Pokud pro R není k dispozici dostatek paměti, zobrazí se chybová zpráva. Běžné chybové zprávy jsou:

- Nelze komunikovat s modulem runtime pro skript R pro ID požadavku: * * * * * * *. Zkontrolujte prosím požadavky modulu runtime R.
- Při spouštění procedury sp_execute_external_script došlo k chybě skriptu R. HRESULT = 0x80004004. ...došlo k chybě externího skriptu: „ ...nebylo možné přidělit paměť (0 Mb) ve funkci jazyka C R_AllocStringBuffer“
- Došlo k chybě externího skriptu: Chyba: Nelze přidělit vektor velikosti.

Využití paměti závisí na tom, kolik se jí používá ve skriptech R, a na počtu spuštěných paralelních dotazů. Pokud se zobrazí výše uvedené chyby, můžete je napravit škálováním databáze na vyšší úroveň služby.

## <a name="next-steps"></a>Další kroky

- Podívejte se na přehled [Machine Learning Services ve spravované instanci SQL Azure](machine-learning-services-overview.md).
- Informace o použití Pythonu v Machine Learning Services najdete v tématu [spuštění skriptů Pythonu](https://docs.microsoft.com/sql/machine-learning/tutorials/quickstart-python-create-script?context=/azure/azure-sql/managed-instance/context/ml-context&view=sql-server-ver15).
- Informace o použití R v Machine Learning Services najdete v tématu [spuštění skriptů r](https://docs.microsoft.com/sql/machine-learning/tutorials/quickstart-r-create-script?context=/azure/azure-sql/managed-instance/context/ml-context&view=sql-server-ver15).
