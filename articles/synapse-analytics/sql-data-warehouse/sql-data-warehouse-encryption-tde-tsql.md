---
title: Transparentní šifrování dat (T-SQL)
description: Transparentní šifrování dat (TDE) ve službě Azure synapse Analytics (T-SQL)
services: synapse-analytics
author: julieMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 04/30/2019
ms.author: jrasnick
ms.reviewer: rortloff
ms.custom: seo-lt-2019
ms.openlocfilehash: d9a80a9001d1d989495a246bbbc8fa703f58e08a
ms.sourcegitcommit: fec60094b829270387c104cc6c21257826fccc54
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/09/2020
ms.locfileid: "96919932"
---
# <a name="get-started-with-transparent-data-encryption-tde"></a>Začínáme s transparentní šifrování dat (TDE)

> [!div class="op_single_selector"]
>
> * [Přehled zabezpečení](sql-data-warehouse-overview-manage-security.md)
> * [Authentication](sql-data-warehouse-authentication.md)
> * [Šifrování (portál)](sql-data-warehouse-encryption-tde.md)
> * [Šifrování (T-SQL)](sql-data-warehouse-encryption-tde-tsql.md)

## <a name="required-permissions"></a>Požadovaná oprávnění

Pokud chcete povolit transparentní šifrování dat (TDE), musíte být správcem nebo členem role dbmanager.

## <a name="enabling-encryption"></a>Povolení šifrování

Pomocí těchto kroků povolíte TDE:

1. Připojte se k *Hlavní* databázi na serveru, který je hostitelem databáze, pomocí přihlašovacích údajů, které jsou správcem nebo členem role **dbmanager** v hlavní databázi.
2. Spusťte následující příkaz k zašifrování databáze.

```sql
ALTER DATABASE [AdventureWorks] SET ENCRYPTION ON;
```

## <a name="disabling-encryption"></a>Zakázání šifrování

Pomocí těchto kroků zakážete TDE:

1. Připojení k *Hlavní* databázi pomocí přihlášení, které je správcem nebo členem role **dbmanager** v hlavní databázi
2. Spusťte následující příkaz k zašifrování databáze.

```sql
ALTER DATABASE [AdventureWorks] SET ENCRYPTION OFF;
```

> [!NOTE]
> Před provedením změn v nastavení TDE je třeba obnovit pozastavený vyhrazený fond SQL.

## <a name="verifying-encryption"></a>Ověřování šifrování

Chcete-li ověřit stav šifrování, postupujte podle následujících kroků:

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

## <a name="encryption-dmvs"></a>Zobrazení dynamické správy šifrování

* [sys.databases](/sql/relational-databases/system-catalog-views/sys-databases-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
* [sys.dm_pdw_nodes_database_encryption_keys](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-nodes-database-encryption-keys-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
