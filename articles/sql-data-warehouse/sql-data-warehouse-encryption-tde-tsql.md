---
title: Transparentní šifrování dat v datovém skladu SQL (T-SQL) | Dokumentace Microsoftu
description: Transparentní šifrování dat (TDE) ve službě SQL Data Warehouse (T-SQL)
services: sql-data-warehouse
author: KavithaJonnakuti
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: security
ms.date: 04/17/2018
ms.author: kavithaj
ms.reviewer: igorstan
ms.openlocfilehash: 7908e6e0927357446ea45e16b7c44adb83ec1fd3
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/18/2019
ms.locfileid: "57892434"
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
Pokud chcete povolit transparentní šifrování dat (TDE), musíte být správce nebo členem dbmanager role.

## <a name="enabling-encryption"></a>Povolení šifrování
Použijte následující postup povolit transparentní šifrování dat pro SQL Data Warehouse:

1. Připojte se k *hlavní* databáze na serveru, který je hostitelem databáze pomocí přihlášení, který je správcem nebo členem skupiny **dbmanager** role v hlavní databázi
2. Spusťte následující příkaz k šifrování databáze.

```sql
ALTER DATABASE [AdventureWorks] SET ENCRYPTION ON;
```

## <a name="disabling-encryption"></a>Zakázáním šifrování
Postupujte podle těchto kroků zakázat transparentní šifrování dat pro SQL Data Warehouse:

1. Připojení k *hlavní* databáze pomocí přihlášení, který je správce nebo člen **dbmanager** role v hlavní databázi
2. Spusťte následující příkaz k šifrování databáze.

```sql
ALTER DATABASE [AdventureWorks] SET ENCRYPTION OFF;
```

> [!NOTE]
> Před prováděním změn nastavení transparentní šifrování dat musí obnovit pozastavený SQL Data Warehouse.
> 
> 

## <a name="verifying-encryption"></a>Ověření šifrování
Pokud chcete ověřit stav šifrování pro SQL Data Warehouse, postupujte podle následujících kroků:

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

## <a name="encryption-dmvs"></a>Šifrování zobrazení dynamické správy
* [sys.databases][sys.databases] 
* [sys.dm_pdw_nodes_database_encryption_keys][sys.dm_pdw_nodes_database_encryption_keys]

<!--Anchors-->
[Transparent Data Encryption (TDE)]: https://msdn.microsoft.com/library/bb934049.aspx
[sys.databases]: https://msdn.microsoft.com/library/ms178534.aspx  
[sys.dm_pdw_nodes_database_encryption_keys]: https://msdn.microsoft.com/library/mt203922.aspx  

<!--Image references-->

<!--Link references-->
