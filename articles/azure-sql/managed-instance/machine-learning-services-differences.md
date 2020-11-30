---
title: Klíčové rozdíly pro Machine Learning Services (Preview)
description: Tento článek popisuje klíčové rozdíly mezi Machine Learning Services ve službě Azure SQL Managed instance a SQL Server Machine Learning Services.
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
ms.date: 10/26/2020
ms.openlocfilehash: c806c0a13f9f5f13588b780054d1f285beb44802
ms.sourcegitcommit: 4295037553d1e407edeb719a3699f0567ebf4293
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/30/2020
ms.locfileid: "96324529"
---
# <a name="key-differences-between-machine-learning-services-in-azure-sql-managed-instance-and-sql-server"></a>Klíčové rozdíly mezi Machine Learning Services ve službách Azure SQL Managed Instance a SQL Server

Funkce [Machine Learning Services ve službě Azure SQL Managed instance (Preview)](machine-learning-services-overview.md) je skoro stejná jako [SQL Server Machine Learning Services](/sql/advanced-analytics/what-is-sql-server-machine-learning). Níže jsou uvedeny některé klíčové rozdíly.

> [!IMPORTANT]
> Machine Learning Services ve službě Azure SQL Managed instance je aktuálně ve verzi Public Preview. Pokud se chcete zaregistrovat, přečtěte si téma [registrace verze Preview](machine-learning-services-overview.md#signup).

## <a name="preview-limitations"></a>Omezení verze Preview

Ve verzi Preview platí pro tuto službu následující omezení:

- Připojení zpětné smyčky nefungují (Další informace najdete v tématu [připojení ke zpětné smyčce SQL Server ze skriptu Python nebo R](/sql/machine-learning/connect/loopback-connection)).
- Externí fondy prostředků se nepodporují.
- Podporují se pouze jazyky Python a R. Externí jazyky, jako je Java, není možné přidat.
- Scénáře využívající rozhraní MPI (Message Passing [Interface](/message-passing-interface/microsoft-mpi) ) nejsou podporovány.

V případě aktualizace objektu na úrovni služby (SLO) aktualizujte objekt SLO a vyvolejte lístek podpory pro opětovné povolení omezení vyhrazených prostředků pro R/Python.

## <a name="language-support"></a>Podpora jazyků

Machine Learning Services ve spravované instanci SQL a SQL Server podporují [architekturu rozšiřitelnosti](/sql/advanced-analytics/concepts/extensibility-framework)Python i R. Hlavní rozdíly:

- Počáteční verze Pythonu a R se liší od Machine Learning Services spravované instance SQL a SQL Server:

  | Systém               | Python | R     |
  |----------------------|--------|-------|
  | Spravovaná instance SQL | 3.7.1  | bodu |
  | SQL Server           | bodu  | body |

- Není nutné konfigurovat `external scripts enabled` prostřednictvím `sp_configure` . Po [zaregistrování](machine-learning-services-overview.md#signup) verze Preview je Machine Learning povolený pro spravovanou instanci Azure SQL.

## <a name="packages"></a>Balíčky

Správa balíčku Pythonu a R funguje odlišně mezi spravovanými instancemi SQL a SQL Server. Jsou to tyto rozdíly:

- Neexistuje žádná podpora pro balíčky, které závisí na externích modulech runtime (jako Java), nebo při instalaci nebo použití nepotřebují přístup k rozhraním API operačního systému.
- Balíčky můžou provádět odchozí síťová volání (ve verzi Preview se změní z výše). Můžete nastavit správná odchozí pravidla zabezpečení na úrovni [skupiny zabezpečení sítě](../../virtual-network/network-security-groups-overview.md) a povolit odchozí síťová volání.

Další informace o správě balíčků Python a R najdete v těchto tématech:

- [Získání informací o balíčku Pythonu](/sql/machine-learning/package-management/python-package-information?context=/azure/azure-sql/managed-instance/context/ml-context&view=azuresqldb-mi-current&preserve-view=true)
- [Získání informací o balíčku jazyka R](/sql/machine-learning/package-management/r-package-information?context=/azure/azure-sql/managed-instance/context/ml-context&view=azuresqldb-mi-current&preserve-view=true)

## <a name="resource-governance"></a>Zásady správného řízení prostředků

Prostředky R není možné omezit prostřednictvím [Správce prostředků](/sql/relational-databases/resource-governor/resource-governor) a externích fondů zdrojů.

Ve verzi Public Preview jsou prostředky R nastavené na maximálně 20 % prostředků SQL Managed Instance a závisí na tom, jakou úroveň služby zvolíte. Další informace najdete v tématu [Azure SQL Database nákup modelů](../database/purchasing-models.md).

### <a name="insufficient-memory-error"></a>Nedostatek paměti – chyba

Pokud pro R není k dispozici dostatek paměti, zobrazí se chybová zpráva. Běžné chybové zprávy jsou:

- `Unable to communicate with the runtime for 'R' script for request id: *******. Please check the requirements of 'R' runtime`
- `'R' script error occurred during execution of 'sp_execute_external_script' with HRESULT 0x80004004. ...an external script error occurred: "..could not allocate memory (0 Mb) in C function 'R_AllocStringBuffer'"`
- `An external script error occurred: Error: cannot allocate vector of size.`

Využití paměti závisí na tom, kolik se jí používá ve skriptech R, a na počtu spuštěných paralelních dotazů. Pokud se zobrazí výše uvedené chyby, můžete je napravit škálováním databáze na vyšší úroveň služby.

## <a name="next-steps"></a>Další kroky

- Podívejte se na přehled [Machine Learning Services ve spravované instanci SQL Azure](machine-learning-services-overview.md).
- Informace o použití Pythonu v Machine Learning Services najdete v tématu [spuštění skriptů Pythonu](/sql/machine-learning/tutorials/quickstart-python-create-script?context=/azure/azure-sql/managed-instance/context/ml-context&view=azuresqldb-mi-current&preserve-view=true).
- Informace o použití R v Machine Learning Services najdete v tématu [spuštění skriptů r](/sql/machine-learning/tutorials/quickstart-r-create-script?context=/azure/azure-sql/managed-instance/context/ml-context&view=azuresqldb-mi-current&preserve-view=true).