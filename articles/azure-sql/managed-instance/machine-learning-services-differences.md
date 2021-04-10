---
title: Klíčové rozdíly pro Machine Learning Services
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
ms.date: 03/17/2021
ms.openlocfilehash: b5ad439a8e10fa9aa44e477ca35f45d65ae40803
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "104599540"
---
# <a name="key-differences-between-machine-learning-services-in-azure-sql-managed-instance-and-sql-server"></a>Klíčové rozdíly mezi Machine Learning Services ve službách Azure SQL Managed Instance a SQL Server

Tento článek popisuje několik důležitých rozdílů ve funkcích mezi [Machine Learning Services ve službě Azure SQL Managed instance](machine-learning-services-overview.md) a [SQL Server Machine Learning Services](https://docs.microsoft.com/sql/advanced-analytics/what-is-sql-server-machine-learning).

## <a name="language-support"></a>Podpora jazyků

Machine Learning Services v obou spravovaných instancích SQL i SQL Server podporují [architekturu rozšiřitelnosti](/sql/machine-learning/concepts/extensibility-framework)Pythonu a R. Hlavní rozdíly ve spravované instanci SQL jsou:

- Podporují se pouze jazyky Python a R. Externí jazyky, jako je Java, není možné přidat.

- Počáteční verze Pythonu a R se liší:

  | Platforma                   | Verze modulu runtime Pythonu           | Verze modulu R runtime                   |
  |----------------------------|----------------------------------|--------------------------------------|
  | Spravovaná instance Azure SQL | 3.7.2                            | bodu                                |
  | SQL Server 2019            | 3.7.1                            | bodu                                |
  | SQL Server 2017            | 3.5.2 a 3.7.2 (CU22 a novější) | 3.3.3 a 3.5.2 (CU22 a novější)     |
  | SQL Server 2016            | Není k dispozici                    | 3.2.2 a 3.5.2 (SP2 CU14 a novější) |

## <a name="python-and-r-packages"></a>Balíčky Pythonu a R

Pro balíčky, které závisejí na externích modulech runtime (jako Java) nebo potřebují přístup k rozhraním API operačního systému pro instalaci nebo použití, není podporovaná žádná podpora v SQL Managed instance.

Další informace o správě balíčků Python a R najdete v těchto tématech:

- [Získání informací o balíčku Pythonu](https://docs.microsoft.com/sql/machine-learning/package-management/python-package-information?context=/azure/azure-sql/managed-instance/context/ml-context&view=azuresqldb-mi-current&preserve-view=true)
- [Získání informací o balíčku jazyka R](https://docs.microsoft.com/sql/machine-learning/package-management/r-package-information?context=/azure/azure-sql/managed-instance/context/ml-context&view=azuresqldb-mi-current&preserve-view=true)

## <a name="resource-governance"></a>Zásady správného řízení prostředků

Ve spravované instanci SQL není možné omezit prostředky R prostřednictvím [Správce zdrojů](/sql/relational-databases/resource-governor/resource-governor?view=azuresqldb-mi-current&preserve-view=true)a externí fondy zdrojů nejsou podporované.

Ve výchozím nastavení jsou prostředky R nastavené na maximálně 20% dostupných prostředků spravované instance SQL, pokud je povolená rozšiřitelnost. Pokud chcete změnit toto výchozí procento, vytvořte lístek podpory Azure na adrese [https://azure.microsoft.com/support/create-ticket/](https://azure.microsoft.com/support/create-ticket/) .

Pro následující příkazy SQL se povoluje rozšiřitelnost (spravované instance SQL se restartuje a nebude dostupná po dobu několika sekund):

```sql
sp_configure 'external scripts enabled', 1;
RECONFIGURE WITH OVERRIDE;
```

K zakázání rozšiřitelnosti a obnovení 100% množství paměti a prostředků procesoru k SQL Server použijte následující příkazy:

```sql
sp_configure 'external scripts enabled', 0;
RECONFIGURE WITH OVERRIDE;
```

Celkový počet prostředků dostupných pro spravovanou instanci SQL závisí na zvolené úrovni služby. Další informace najdete v tématu [Azure SQL Database nákup modelů](/azure/sql-database/sql-database-service-tiers).

### <a name="insufficient-memory-error"></a>Nedostatek paměti – chyba

Využití paměti závisí na tom, kolik se jí používá ve skriptech R, a na počtu spuštěných paralelních dotazů. Pokud pro R není k dispozici dostatek paměti, zobrazí se chybová zpráva. Běžné chybové zprávy jsou:

- `Unable to communicate with the runtime for 'R' script for request id: *******. Please check the requirements of 'R' runtime`
- `'R' script error occurred during execution of 'sp_execute_external_script' with HRESULT 0x80004004. ...an external script error occurred: "..could not allocate memory (0 Mb) in C function 'R_AllocStringBuffer'"`
- `An external script error occurred: Error: cannot allocate vector of size.`

Pokud se zobrazí jedna z těchto chyb, můžete ji vyřešit škálováním databáze na vyšší úroveň služby.

## <a name="sql-managed-instance-pools"></a>Fondy spravované instance SQL

Machine Learning Services se v tuto chvíli nepodporují ve [fondech spravované instance Azure SQL (Preview)](instance-pools-overview.md).

## <a name="next-steps"></a>Další kroky

- Podívejte se na přehled [Machine Learning Services ve spravované instanci SQL Azure](machine-learning-services-overview.md).
- Informace o použití Pythonu v Machine Learning Services najdete v tématu [spuštění skriptů Pythonu](/sql/machine-learning/tutorials/quickstart-python-create-script?context=/azure/azure-sql/managed-instance/context/ml-context&view=azuresqldb-mi-current&preserve-view=true).
- Informace o použití R v Machine Learning Services najdete v tématu [spuštění skriptů r](/sql/machine-learning/tutorials/quickstart-r-create-script?context=/azure/azure-sql/managed-instance/context/ml-context&view=azuresqldb-mi-current&preserve-view=true).
