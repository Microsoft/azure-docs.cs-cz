---
title: Transparentní šifrování dat (portál)
description: Transparentní šifrování dat (TDE) ve službě Azure synapse Analytics
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
ms.openlocfilehash: c121f9c16895a749922525d1ba85ee2c2e60cfb0
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/29/2020
ms.locfileid: "78195852"
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
Pokud chcete povolit TDE, postupujte podle následujících kroků:

1. Otevřete databázi v [Azure Portal](https://portal.azure.com)
2. V okně databáze klikněte na tlačítko **Nastavení** .
3. Vyberte možnost **transparentní šifrování dat** ![][1]
4. Vyberte nastavení **On** ![][2]
5. Vyberte **uložit**
   ![][3]  

## <a name="disabling-encryption"></a>Zakázání šifrování
Pokud chcete zakázat TDE, postupujte podle následujících kroků:

1. Otevřete databázi v [Azure Portal](https://portal.azure.com)
2. V okně databáze klikněte na tlačítko **Nastavení** .
3. Vyberte možnost **transparentní šifrování dat** ![][1]
4. Vyberte nastavení **Off** ![][4]
5. Vyberte **uložit**
   ![][5]  

## <a name="encryption-dmvs"></a>Zobrazení dynamické správy šifrování
Šifrování lze potvrdit pomocí následujících zobrazení dynamické správy:

* [sys. databases]
* [sys. dm_pdw_nodes_database_encryption_keys]

<!--MSDN references-->
[Transparent Data Encryption (TDE)]: https://msdn.microsoft.com/library/bb934049.aspx
[sys. databases]: https://msdn.microsoft.com/library/ms178534.aspx
[sys. dm_pdw_nodes_database_encryption_keys]: https://msdn.microsoft.com/library/mt203922.aspx

<!--Image references-->
[1]: ./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings.png
[2]: ./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings-on.png
[3]: ./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings-save.png
[4]: ./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings-off.png
[5]: ./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings-save2.png

<!--Link references-->
