---
title: Transparentní šifrování dat (portál)
description: Transparentní šifrování dat (TDE) v Azure Synapse Analytics
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
ms.openlocfilehash: 247691326e3aa2c8027dd0318b23a2cbfcba1efe
ms.sourcegitcommit: bd5fee5c56f2cbe74aa8569a1a5bce12a3b3efa6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "80745225"
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

Chcete-li povolit TDE, postupujte podle následujících kroků:

1. Otevření databáze na [webu Azure Portal](https://portal.azure.com)
2. V okně databáze klepněte na tlačítko **Nastavení.**
3. Výběr **Transparent data encryption** nastavení portálu ![možností Transparentní šifrování dat](./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings.png)
4. Vyberte nastavení ![ **portálu Na**](./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings-on.png)
5. Vyberte **Uložit**
   ![nastavení portálu uložit.](./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings-save.png)  

## <a name="disabling-encryption"></a>Zakázání šifrování

Chcete-li zakázat TDE, postupujte podle následujících kroků:

1. Otevření databáze na [webu Azure Portal](https://portal.azure.com)
2. V okně databáze klepněte na tlačítko **Nastavení.**
3. Výběr **Transparent data encryption** nastavení portálu ![možností Transparentní šifrování dat](./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings.png)
4. Vyberte vypnuto nastavení portálu **Off.** ![](./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings-off.png)
5. Vybrat **Uložit**
   ![nastavení portálu uložit 2](./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings-save2.png)  

## <a name="encryption-dmvs"></a>Šifrovací dmvs

Šifrování lze potvrdit pomocí následujících dmvs:

* [Sys.databases](/sql/relational-databases/system-catalog-views/sys-databases-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
* [sys.dm_pdw_nodes_database_encryption_keys](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-nodes-database-encryption-keys-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
