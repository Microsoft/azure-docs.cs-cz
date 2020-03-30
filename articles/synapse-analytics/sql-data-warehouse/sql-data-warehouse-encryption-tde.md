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
ms.openlocfilehash: 669a02bb45ef61d1e66d719850defcfaa19b838d
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "80350529"
---
# <a name="get-started-with-transparent-data-encryption-tde"></a>Začínáme s transparentním šifrováním dat (TDE)
> [!div class="op_single_selector"]
> * [Přehled zabezpečení](sql-data-warehouse-overview-manage-security.md)
> * [Ověřování](sql-data-warehouse-authentication.md)
> * [Šifrování (portál)](sql-data-warehouse-encryption-tde.md)
> * [Šifrování (T-SQL)](sql-data-warehouse-encryption-tde-tsql.md)
> 
> 

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

* [Sys.databases]
* [sys.dm_pdw_nodes_database_encryption_keys]

<!--MSDN references-->
[Transparent Data Encryption (TDE)]: https://msdn.microsoft.com/library/bb934049.aspx
[Sys.databases]: https://msdn.microsoft.com/library/ms178534.aspx
[sys.dm_pdw_nodes_database_encryption_keys]: https://msdn.microsoft.com/library/mt203922.aspx

<!--Image references-->
[1]: ./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings.png
[2]: ./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings-on.png
[3]: ./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings-save.png
[4]: ./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings-off.png
[5]: ./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings-save2.png

<!--Link references-->
