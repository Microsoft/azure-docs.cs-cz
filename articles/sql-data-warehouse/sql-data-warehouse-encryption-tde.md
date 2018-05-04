---
title: Transparentní šifrování dat v SQL Data Warehouse (portál) | Microsoft Docs
description: Transparentní šifrování dat (šifrování TDE) v SQL Data Warehouse
services: sql-data-warehouse
author: kavithaj
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: implement
ms.date: 04/17/2018
ms.author: kavithaj
ms.reviewer: igorstan
ms.openlocfilehash: 9c4abb0416acc656a4cfae332377c398260191de
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/18/2018
---
# <a name="get-started-with-transparent-data-encryption-tde-in-sql-data-warehouse"></a>Začínáme s transparentní dat šifrování (TDE) v SQL Data Warehouse
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
Pokud chcete povolit šifrování TDE pro SQL Data Warehouse, postupujte podle následujících kroků:

1. Otevřít v databázi [portálu Azure](https://portal.azure.com)
2. V okně databáze klikněte na **nastavení** tlačítko
3. Vyberte **transparentní šifrování dat** možnost ![][1]
4. Vyberte **na** nastavení ![][2]
5. Vyberte **uložit**
   ![][3]  

## <a name="disabling-encryption"></a>Zakázáním šifrování
Zakázat šifrování TDE pro SQL Data Warehouse, postupujte podle následujících kroků:

1. Otevřít v databázi [portálu Azure](https://portal.azure.com)
2. V okně databáze klikněte na **nastavení** tlačítko
3. Vyberte **transparentní šifrování dat** možnost ![][1]
4. Vyberte **vypnout** nastavení ![][4]
5. Vyberte **uložit**
   ![][5]  

## <a name="encryption-dmvs"></a>Šifrování zobrazení dynamické správy
Šifrování lze potvrdit s následující zobrazení dynamické správy:

* [sys.databases]
* [sys.dm_pdw_nodes_database_encryption_keys]

<!--MSDN references-->
[Transparent Data Encryption (TDE)]: https://msdn.microsoft.com/library/bb934049.aspx
[sys.databases]: http://msdn.microsoft.com/library/ms178534.aspx
[sys.dm_pdw_nodes_database_encryption_keys]: https://msdn.microsoft.com/library/mt203922.aspx

<!--Image references-->
[1]: ./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings.png
[2]: ./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings-on.png
[3]: ./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings-save.png
[4]: ./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings-off.png
[5]: ./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings-save2.png

<!--Link references-->
