---
title: Povolit transparentní šifrování dat pro Stretch Database TSQL – Azure | Dokumentace Microsoftu
description: Povolit transparentní šifrování dat (TDE) pro SQL Server Stretch Database na Azure TSQL
services: sql-server-stretch-database
documentationcenter: ''
author: douglaslMS
manager: craigg
ms.assetid: 27753d91-9ca2-4d47-b34d-b5e2c2f029bb
ms.service: sql-server-stretch-database
ms.workload: data-management
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 01/23/2017
ms.author: douglasl
ms.openlocfilehash: 634e1b097f2ca16a8c52da02445b3f5f2af7ff3d
ms.sourcegitcommit: 21466e845ceab74aff3ebfd541e020e0313e43d9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/21/2018
ms.locfileid: "53744781"
---
# <a name="enable-transparent-data-encryption-tde-for-stretch-database-on-azure-transact-sql"></a>Povolit transparentní šifrování dat (TDE) pro Stretch Database v Azure (Transact-SQL)
> [!div class="op_single_selector"]
> * [Azure Portal](sql-server-stretch-database-encryption-tde.md)
> * [TSQL](sql-server-stretch-database-tde-tsql.md)
>
>

Transparentní šifrování dat (TDE) pomáhá chránit před hrozbou škodlivých aktivit pomocí provádí v reálném čase šifrování a dešifrování databáze, přidružené zálohy a soubory protokolů transakcí v klidovém stavu bez nutnosti změny aplikace.

Transparentní šifrování dat šifruje úložiště celou databázi pomocí symetrický klíč s názvem šifrovací klíč databáze. Šifrovací klíč databáze je chráněno certifikátem integrovaného serveru. Certifikát integrovaného serveru je jedinečný pro každý server Azure. Microsoft automaticky otočí tyto certifikáty nejméně každých 90 dní. Obecný popis transparentní šifrování dat, naleznete v tématu [transparentní šifrování dat (TDE)].

## <a name="enabling-encryption"></a>Povolení šifrování
Povolit transparentní šifrování dat pro Azure migrovat databázi, která ukládá data z databáze povolenou funkcí Stretch SQL serveru, proveďte následující akce:

1. Připojte se k *hlavní* databáze na serveru Azure hostujícím databázi pomocí přihlášení, které je správce nebo členem skupiny **dbmanager** role v hlavní databázi
2. Spusťte následující příkaz k šifrování databáze.

```sql
ALTER DATABASE [database_name] SET ENCRYPTION ON;
```

## <a name="disabling-encryption"></a>Zakázáním šifrování
Zakázat transparentní šifrování dat pro Azure migrovat databázi, která ukládá data z databáze povolenou funkcí Stretch SQL serveru, proveďte následující akce:

1. Připojení k *hlavní* databáze pomocí přihlášení, který je správce nebo člen **dbmanager** role v hlavní databázi
2. Spusťte následující příkaz k šifrování databáze.

```sql
ALTER DATABASE [database_name] SET ENCRYPTION OFF;
```

## <a name="verifying-encryption"></a>Ověření šifrování
Pokud chcete ověřit, že stav šifrování pro databázi Azure, který ukládá data migrovaná z databáze SQL serveru s povolenou funkcí Stretch, proveďte následující akce:

1. Připojení k *hlavní* nebo instanci databáze pomocí přihlášení, který je správce nebo člen **dbmanager** role v hlavní databázi
2. Spusťte následující příkaz k šifrování databáze.

```sql
SELECT
    [name],
    [is_encrypted]
FROM
    sys.databases;
```

Výsledkem ```1``` Určuje databázi šifrované ```0``` Určuje databázi bez šifrování.

<!--Anchors-->
[Transparentní šifrování dat (TDE)]: https://msdn.microsoft.com/library/bb934049.aspx


<!--Image references-->

<!--Link references-->
