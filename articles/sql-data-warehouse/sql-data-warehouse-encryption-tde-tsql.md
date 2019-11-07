---
title: Transparentní šifrování dat (T-SQL)
description: Transparentní šifrování dat (TDE) v SQL Data Warehouse (T-SQL)
services: sql-data-warehouse
author: julieMSFT
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: security
ms.date: 04/30/2019
ms.author: jrasnick
ms.reviewer: rortloff
ms.custom: seo-lt-2019
ms.openlocfilehash: 98695aa7578e90be9ada2f86640e68b05a9812ed
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/06/2019
ms.locfileid: "73692741"
---
# <a name="get-started-with-transparent-data-encryption-tde"></a>Začínáme s transparentní šifrování dat (TDE)
> [!div class="op_single_selector"]
> * [Přehled zabezpečení](sql-data-warehouse-overview-manage-security.md)
> * [Ověřování](sql-data-warehouse-authentication.md)
> * [Šifrování (portál)](sql-data-warehouse-encryption-tde.md)
> * [Šifrování (T-SQL)](sql-data-warehouse-encryption-tde-tsql.md)
> 
> 

## <a name="required-permissions"></a>Požadovaná oprávnění
Pokud chcete povolit transparentní šifrování dat (TDE), musíte být správcem nebo členem role dbmanager.

## <a name="enabling-encryption"></a>Povolení šifrování
Pomocí těchto kroků povolíte TDE pro SQL Data Warehouse:

1. Připojte se k *Hlavní* databázi na serveru, který je hostitelem databáze, pomocí přihlašovacích údajů, které jsou správcem nebo členem role **dbmanager** v hlavní databázi.
2. Spusťte následující příkaz k zašifrování databáze.

```sql
ALTER DATABASE [AdventureWorks] SET ENCRYPTION ON;
```

## <a name="disabling-encryption"></a>Zakázání šifrování
Pomocí těchto kroků zakážete TDE pro SQL Data Warehouse:

1. Připojení k *Hlavní* databázi pomocí přihlášení, které je správcem nebo členem role **dbmanager** v hlavní databázi
2. Spusťte následující příkaz k zašifrování databáze.

```sql
ALTER DATABASE [AdventureWorks] SET ENCRYPTION OFF;
```

> [!NOTE]
> Před provedením změn v nastavení TDE je třeba obnovit pozastavený SQL Data Warehouse.
> 
> 

## <a name="verifying-encryption"></a>Ověřování šifrování
Pokud chcete ověřit stav šifrování pro SQL Data Warehouse, postupujte podle následujících kroků:

1. Připojte se k *Hlavní* databázi nebo databázi instance pomocí přihlášení, které je správcem nebo členem role **dbmanager** v hlavní databázi.
2. Spusťte následující příkaz k zašifrování databáze.

```sql
SELECT
    [name],
    [is_encrypted]
FROM
    sys.databases;
```

Výsledek ```1``` označuje šifrovanou databázi, ```0``` označuje nešifrovanou databázi.

## <a name="encryption-dmvs"></a>Zobrazení dynamické správy šifrování
* [sys. databases][sys.databases] 
* [sys. DM _pdw_nodes_database_encryption_keys][sys.dm_pdw_nodes_database_encryption_keys]

<!--Anchors-->
[Transparent Data Encryption (TDE)]: https://msdn.microsoft.com/library/bb934049.aspx
[sys.databases]: https://msdn.microsoft.com/library/ms178534.aspx  
[sys.dm_pdw_nodes_database_encryption_keys]: https://msdn.microsoft.com/library/mt203922.aspx  

<!--Image references-->

<!--Link references-->
