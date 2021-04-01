---
title: Povolit transparentní šifrování dat pro Stretch Database (T-SQL)
description: Povolení transparentní šifrování dat (TDE) pro SQL Server Stretch Database v Azure TSQL
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
ms.openlocfilehash: 772341d046186e46b79ad7b11170e1bad23a3a6f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "95024190"
---
# <a name="enable-transparent-data-encryption-tde-for-stretch-database-on-azure-transact-sql"></a>Povolení transparentní šifrování dat (TDE) pro Stretch Database v Azure (Transact-SQL)
> [!div class="op_single_selector"]
> * [Azure Portal](sql-server-stretch-database-encryption-tde.md)
> * [TSQL](sql-server-stretch-database-tde-tsql.md)
>
>

Transparentní šifrování dat (TDE) pomáhá chránit před hrozbou škodlivých aktivit tím, že provádí šifrování a dešifrování databáze, přidružených záloh a souborů protokolu transakcí v reálném čase bez nutnosti změny aplikace.

Transparentní šifrování dat šifruje úložiště celé databáze pomocí symetrického klíče nazývaného šifrovací klíč databáze. Šifrovací klíč databáze je chráněn integrovaným certifikátem serveru. Integrovaný certifikát serveru je pro každý server Azure jedinečný. Microsoft tyto certifikáty automaticky přetočí nejméně každých 90 dnů. Obecný popis TDE naleznete v tématu [transparentní šifrování dat (TDE)].

## <a name="enabling-encryption"></a>Povolení šifrování
Pokud chcete povolit TDE pro databázi Azure, která ukládá data migrovaná z databáze SQL Server s povolenou funkcí Stretch, proveďte následující akce:

1. Připojte se k *Hlavní* databázi na serveru Azure, který je hostitelem databáze, pomocí přihlašovacích údajů, které jsou správcem nebo členem role **dbmanager** v hlavní databázi.
2. Spusťte následující příkaz k zašifrování databáze.

```sql
ALTER DATABASE [database_name] SET ENCRYPTION ON;
```

## <a name="disabling-encryption"></a>Zakázání šifrování
Pokud chcete zakázat TDE pro databázi Azure, která ukládá data migrovaná z databáze SQL Server s povolenou funkcí Stretch, proveďte následující akce:

1. Připojení k *Hlavní* databázi pomocí přihlášení, které je správcem nebo členem role **dbmanager** v hlavní databázi
2. Spusťte následující příkaz k zašifrování databáze.

```sql
ALTER DATABASE [database_name] SET ENCRYPTION OFF;
```

## <a name="verifying-encryption"></a>Ověřování šifrování
Pokud chcete ověřit stav šifrování databáze Azure, která ukládá data migrovaná z databáze SQL Server s povolenou funkcí Stretch, proveďte následující akce:

1. Připojte se k *Hlavní* databázi nebo databázi instance pomocí přihlášení, které je správcem nebo členem role **dbmanager** v hlavní databázi.
2. Spusťte následující příkaz k zašifrování databáze.

```sql
SELECT
    [name],
    [is_encrypted]
FROM
    sys.databases;
```

Výsledek ```1``` označuje šifrovanou databázi, která ```0``` označuje nešifrovanou databázi.

<!--Anchors-->
[Transparentní šifrování dat]: /sql/relational-databases/security/encryption/transparent-data-encryption


<!--Image references-->

<!--Link references-->