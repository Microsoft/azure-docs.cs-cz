---
title: Povolit transparentní šifrování dat pro funkce Stretch Database TSQL - Azure | Microsoft Docs
description: Povolit transparentní šifrování dat (šifrování TDE) pro SQL Server Stretch Database na Azure TSQL
services: sql-server-stretch-database
documentationcenter: ''
author: douglaslMS
manager: jhubbard
editor: ''
ms.assetid: 27753d91-9ca2-4d47-b34d-b5e2c2f029bb
ms.service: sql-server-stretch-database
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: anvang
ms.openlocfilehash: ed26c2b386e08b76f78b4a05e12c46d2b97c20f2
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/21/2017
ms.locfileid: "23872936"
---
# <a name="enable-transparent-data-encryption-tde-for-stretch-database-on-azure-transact-sql"></a>Povolit transparentní šifrování dat (šifrování TDE) pro funkce Stretch Database na Azure (Transact-SQL)
> [!div class="op_single_selector"]
> * [portál Azure Portal](sql-server-stretch-database-encryption-tde.md)
> * [TSQL](sql-server-stretch-database-tde-tsql.md)
>
>

Transparentní šifrování šifrování dat (TDE) pomáhá chránit před ohrožením škodlivých aktivit provedením v reálném čase šifrování a dešifrování databáze, přidružených záloh a souborů protokolů transakci bez nutnosti změny aplikace.

Šifrování TDE zašifruje úložiště celé databáze pomocí symetrický klíč s názvem šifrovací klíč databáze. Šifrovací klíč databáze je chráněn certifikát integrovaného serveru. Certifikát integrovaného serveru je jedinečný pro každý server Azure. Microsoft automaticky otočí tyto certifikáty alespoň jednou za 90 dní. Obecný popis TDE, najdete v části [transparentní šifrování šifrování dat (TDE)].

## <a name="enabling-encryption"></a>Povolení šifrování
Chcete-li povolit šifrování TDE Azure migrovat databázi, která ukládá data z databáze serveru SQL povolenou funkcí Stretch, provádět následující akce:

1. Připojení k *hlavní* databáze na serveru Azure, který je hostitelem databáze pomocí přihlášení, který je správcem nebo člen **dbmanager** role v hlavní databázi
2. Spusťte následující příkaz k šifrování databáze.

```sql
ALTER DATABASE [database_name] SET ENCRYPTION ON;
```

## <a name="disabling-encryption"></a>Zakázáním šifrování
Zakázat šifrování TDE Azure migrovat databázi, která ukládá data z databáze povolenou funkcí Stretch SQL Server, provádět následující akce:

1. Připojení k *hlavní* databáze pomocí přihlášení, který je správcem nebo člen **dbmanager** role v hlavní databázi
2. Spusťte následující příkaz k šifrování databáze.

```sql
ALTER DATABASE [database_name] SET ENCRYPTION OFF;
```

## <a name="verifying-encryption"></a>Ověření šifrování
Pokud chcete ověřit, že stav šifrování pro databázi Azure, která ukládá data migrována z databáze povolenou funkcí Stretch SQL serveru, proveďte následující akce:

1. Připojení k *hlavní* nebo instanci databáze pomocí přihlášení, který je správcem nebo člen **dbmanager** role v hlavní databázi
2. Spusťte následující příkaz k šifrování databáze.

```sql
SELECT
    [name],
    [is_encrypted]
FROM
    sys.databases;
```

Důsledkem ```1``` Určuje databázi chráněnou ```0``` Určuje databázi bez šifrování.

<!--Anchors-->
[transparentní šifrování šifrování dat (TDE)]: https://msdn.microsoft.com/library/bb934049.aspx


<!--Image references-->

<!--Link references-->
