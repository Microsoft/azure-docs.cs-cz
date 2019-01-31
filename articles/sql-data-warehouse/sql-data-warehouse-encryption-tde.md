---
title: Transparentní šifrování dat ve službě SQL Data Warehouse (portál) | Dokumentace Microsoftu
description: Transparentní šifrování dat (TDE) ve službě SQL Data Warehouse
services: sql-data-warehouse
author: KavithaJonnakuti
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: implement
ms.date: 04/17/2018
ms.author: kavithaj
ms.reviewer: igorstan
ms.openlocfilehash: 6ca5ac013716db9712063e8efc0e2116570008a9
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/31/2019
ms.locfileid: "55455605"
---
# <a name="get-started-with-transparent-data-encryption-tde-in-sql-data-warehouse"></a>Začínáme s transparentní šifrování dat (TDE) ve službě SQL Data Warehouse
> [!div class="op_single_selector"]
> * [Přehled zabezpečení](sql-data-warehouse-overview-manage-security.md)
> * [Ověřování](sql-data-warehouse-authentication.md)
> * [Šifrování (portál)](sql-data-warehouse-encryption-tde.md)
> * [Šifrování (T-SQL)](sql-data-warehouse-encryption-tde-tsql.md)
> 
> 

## <a name="required-permssions"></a>Požadovaná oprávnění
Pokud chcete povolit transparentní šifrování dat (TDE), musíte být správce nebo členem dbmanager role.

## <a name="enabling-encryption"></a>Povolení šifrování
Pokud chcete povolit transparentní šifrování dat pro SQL Data Warehouse, postupujte podle následujících kroků:

1. Otevřít v databázi [webu Azure portal](https://portal.azure.com)
2. V okně databáze klikněte na tlačítko **nastavení** tlačítko
3. Vyberte **transparentní šifrování dat** možnost ![][1]
4. Vyberte **na** nastavení ![][2]
5. Vyberte **uložit**
   ![][3]  

## <a name="disabling-encryption"></a>Zakázáním šifrování
Chcete-li zakázat transparentní šifrování dat pro SQL Data Warehouse, použijte následující postup:

1. Otevřít v databázi [webu Azure portal](https://portal.azure.com)
2. V okně databáze klikněte na tlačítko **nastavení** tlačítko
3. Vyberte **transparentní šifrování dat** možnost ![][1]
4. Vyberte **vypnout** nastavení ![][4]
5. Vyberte **uložit**
   ![][5]  

## <a name="encryption-dmvs"></a>Šifrování zobrazení dynamické správy
Šifrování může být potvrzen pomocí následující zobrazení dynamické správy:

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
