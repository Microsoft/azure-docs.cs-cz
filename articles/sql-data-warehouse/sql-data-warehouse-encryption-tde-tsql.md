---
title: Transparentní šifrování dat v datovém skladu SQL (T-SQL) | Microsoft Docs
description: Transparentní šifrování dat (šifrování TDE) v datovém skladu SQL (T-SQL)
services: sql-data-warehouse
author: kavithaj
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: implement
ms.date: 04/17/2018
ms.author: kavithaj
ms.reviewer: igorstan
ms.openlocfilehash: d10b8f751d782f00cbc58274e4b48c501cea6f70
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/18/2018
ms.locfileid: "31526008"
---
# <a name="get-started-with-transparent-data-encryption-tde"></a>Začínáme s transparentní šifrování dat (TDE)
> [!div class="op_single_selector"]
> * [Přehled zabezpečení](sql-data-warehouse-overview-manage-security.md)
> * [Ověřování](sql-data-warehouse-authentication.md)
> * [Šifrování (portál)](sql-data-warehouse-encryption-tde.md)
> * [Šifrování (T-SQL)](sql-data-warehouse-encryption-tde-tsql.md)
> 
> 

## <a name="required-permssions"></a>Požadovaná oprávnění
Pokud chcete povolit šifrování transparentní dat (TDE), musíte být správce nebo člen dbmanager role.

## <a name="enabling-encryption"></a>Povolení šifrování
Postupujte podle těchto kroků, které chcete povolit šifrování TDE pro SQL Data Warehouse:

1. Připojení k *hlavní* databáze na serveru, který hostuje databázi pomocí přihlášení, které je správce nebo člen **dbmanager** role v hlavní databázi
2. Spusťte následující příkaz k šifrování databáze.

```sql
ALTER DATABASE [AdventureWorks] SET ENCRYPTION ON;
```

## <a name="disabling-encryption"></a>Zakázáním šifrování
Postupujte podle těchto kroků zakázat šifrování TDE pro SQL Data Warehouse:

1. Připojení k *hlavní* databáze pomocí přihlášení, který je správcem nebo člen **dbmanager** role v hlavní databázi
2. Spusťte následující příkaz k šifrování databáze.

```sql
ALTER DATABASE [AdventureWorks] SET ENCRYPTION OFF;
```

> [!NOTE]
> Pozastavený SQL Data Warehouse musí být obnoven, před prováděním změn nastavení šifrování TDE.
> 
> 

## <a name="verifying-encryption"></a>Ověření šifrování
Pokud chcete ověřit stav šifrování pro SQL Data Warehouse, postupujte podle následujících kroků:

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

## <a name="encryption-dmvs"></a>Šifrování zobrazení dynamické správy
* [zobrazení Sys.Databases][sys.databases] 
* [Sys.dm_pdw_nodes_database_encryption_keys][sys.dm_pdw_nodes_database_encryption_keys]

<!--Anchors-->
[Transparent Data Encryption (TDE)]: https://msdn.microsoft.com/library/bb934049.aspx
[sys.databases]: http://msdn.microsoft.com/library/ms178534.aspx  
[sys.dm_pdw_nodes_database_encryption_keys]: https://msdn.microsoft.com/library/mt203922.aspx  

<!--Image references-->

<!--Link references-->
