---
title: Transparentní šifrování dat (T-SQL)
description: Transparentní šifrování dat (TDE) v Azure Synapse Analytics (T-SQL)
services: synapse-analytics
author: julieMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/30/2019
ms.author: jrasnick
ms.reviewer: rortloff
ms.custom: seo-lt-2019
ms.openlocfilehash: ae751cc5b8e3ab67f3e65757724d0ebae1c45e02
ms.sourcegitcommit: bd5fee5c56f2cbe74aa8569a1a5bce12a3b3efa6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "80745252"
---
# <a name="get-started-with-transparent-data-encryption-tde"></a>Začínáme s transparentním šifrováním dat (TDE)

> [!div class="op_single_selector"]
>
> * [Přehled zabezpečení](sql-data-warehouse-overview-manage-security.md)
> * [Authentication](sql-data-warehouse-authentication.md)
> * [Šifrování (portál)](sql-data-warehouse-encryption-tde.md)
> * [Šifrování (T-SQL)](sql-data-warehouse-encryption-tde-tsql.md)

## <a name="required-permissions"></a>Požadovaná oprávnění

Chcete-li povolit transparentní šifrování dat (TDE), musíte být správcenebo člen role dbmanager.

## <a name="enabling-encryption"></a>Povolení šifrování

Povolte TDE následujícím postupem:

1. Připojení k *hlavní* databázi na serveru hostujícím databázi pomocí přihlášení, které je správcem nebo členem role **dbmanager** v hlavní databázi
2. Spusťte následující příkaz k šifrování databáze.

```sql
ALTER DATABASE [AdventureWorks] SET ENCRYPTION ON;
```

## <a name="disabling-encryption"></a>Zakázání šifrování

Chcete-li zakázat službu TDE, postupujte takto:

1. Připojení k *hlavní* databázi pomocí přihlášení, které je správcem nebo členem role **dbmanager** v hlavní databázi
2. Spusťte následující příkaz k šifrování databáze.

```sql
ALTER DATABASE [AdventureWorks] SET ENCRYPTION OFF;
```

> [!NOTE]
> Pozastavený fond SQL musí být obnoven před provedením změn nastavení TDE.

## <a name="verifying-encryption"></a>Ověření šifrování

Chcete-li ověřit stav šifrování, postupujte podle následujících kroků:

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

## <a name="encryption-dmvs"></a>Šifrovací dmvs

* [Sys.databases](/sql/relational-databases/system-catalog-views/sys-databases-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
* [sys.dm_pdw_nodes_database_encryption_keys](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-nodes-database-encryption-keys-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
