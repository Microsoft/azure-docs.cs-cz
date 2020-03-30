---
title: Povolit transparentní šifrování dat pro roztažnou databázi (T-SQL)
description: Povolení transparentního šifrování dat (TDE) pro databázi roztažení serveru SQL Server v Azure TSQL
services: sql-server-stretch-database
documentationcenter: ''
ms.assetid: 27753d91-9ca2-4d47-b34d-b5e2c2f029bb
ms.service: sql-server-stretch-database
ms.workload: data-management
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 01/23/2017
author: blazem-msft
ms.author: blazem
ms.reviewer: jroth
manager: jroth
ms.custom: seo-lt-2019
ms.openlocfilehash: 6f1f5f55348069dbfe11b4d5857d93f8ba8c9b19
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74033960"
---
# <a name="enable-transparent-data-encryption-tde-for-stretch-database-on-azure-transact-sql"></a>Povolení transparentního šifrování dat (TDE) pro roztažnou databázi v Azure (Transact-SQL)
> [!div class="op_single_selector"]
> * [Portál Azure](sql-server-stretch-database-encryption-tde.md)
> * [TSQL](sql-server-stretch-database-tde-tsql.md)
>
>

Transparentní šifrování dat (TDE) pomáhá chránit před hrozbou škodlivé aktivity prováděním šifrování a dešifrování databáze v reálném čase, přidružené zálohy a soubory protokolu transakcí v klidovém stavu bez nutnosti změny v aplikaci.

Transparentní šifrování dat šifruje úložiště celé databáze pomocí symetrického klíče nazývaného šifrovací klíč databáze. Šifrovací klíč databáze je chráněn integrovaným certifikátem serveru. Integrovaný certifikát serveru je jedinečný pro každý server Azure. Společnost Microsoft automaticky otočí tyto certifikáty alespoň každých 90 dní. Obecný popis TDE naleznete v tématu [Transparentní šifrování dat (TDE)].

## <a name="enabling-encryption"></a>Povolení šifrování
Chcete-li povolit TDE pro databázi Azure, která ukládá data migrovaná z databáze SQL Serveru s podporou stretch, proveďte následující kroky:

1. Připojení k *hlavní* databázi na serveru Azure hostujícím databázi pomocí přihlášení, které je správcem nebo členem role **dbmanager** v hlavní databázi
2. Spusťte následující příkaz k šifrování databáze.

```sql
ALTER DATABASE [database_name] SET ENCRYPTION ON;
```

## <a name="disabling-encryption"></a>Zakázání šifrování
Chcete-li zakázat TDE pro databázi Azure, která ukládá data migrovaná z databáze SQL Serveru s podporou stretch, proveďte následující kroky:

1. Připojení k *hlavní* databázi pomocí přihlášení, které je správcem nebo členem role **dbmanager** v hlavní databázi
2. Spusťte následující příkaz k šifrování databáze.

```sql
ALTER DATABASE [database_name] SET ENCRYPTION OFF;
```

## <a name="verifying-encryption"></a>Ověření šifrování
Chcete-li ověřit stav šifrování pro databázi Azure, která ukládá data migrovaná z databáze SQL Serveru s podporou stretch, proveďte následující kroky:

1. Připojení k hlavní *databázi* nebo databázi instancí pomocí přihlášení, které je správcem nebo členem role **dbmanager** v hlavní databázi
2. Spusťte následující příkaz k šifrování databáze.

```sql
SELECT
    [name],
    [is_encrypted]
FROM
    sys.databases;
```

Výsledek ```1``` označuje šifrovanou ```0``` databázi, označuje nešifrovanou databázi.

<!--Anchors-->
[Transparentní šifrování dat (TDE)]: https://msdn.microsoft.com/library/bb934049.aspx


<!--Image references-->

<!--Link references-->
