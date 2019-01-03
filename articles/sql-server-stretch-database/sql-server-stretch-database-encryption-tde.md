---
title: Povolit transparentní šifrování dat pro Stretch Database – Azure | Dokumentace Microsoftu
description: Povolit transparentní šifrování dat (TDE) pro SQL Server Stretch Database v Azure
services: sql-server-stretch-database
documentationcenter: ''
author: douglaslMS
manager: craigg
ms.assetid: a44ed8f5-b416-4c41-9b1e-b7271f10bdc3
ms.service: sql-server-stretch-database
ms.workload: data-management
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 06/14/2016
ms.author: douglasl
ms.openlocfilehash: 1e40e3d9eb1231666acda89c752ebc8f517e8fc6
ms.sourcegitcommit: 21466e845ceab74aff3ebfd541e020e0313e43d9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/21/2018
ms.locfileid: "53741534"
---
# <a name="enable-transparent-data-encryption-tde-for-stretch-database-on-azure"></a>Povolit transparentní šifrování dat (TDE) pro Stretch Database v Azure
> [!div class="op_single_selector"]
> * [Azure Portal](sql-server-stretch-database-encryption-tde.md)
> * [TSQL](sql-server-stretch-database-tde-tsql.md)
>
>

Transparentní šifrování dat (TDE) pomáhá chránit před hrozbou škodlivých aktivit pomocí provádí v reálném čase šifrování a dešifrování databáze, přidružené zálohy a soubory protokolů transakcí v klidovém stavu bez nutnosti změny aplikace.

Transparentní šifrování dat šifruje úložiště celou databázi pomocí symetrický klíč s názvem šifrovací klíč databáze. Šifrovací klíč databáze je chráněno certifikátem integrovaného serveru. Certifikát integrovaného serveru je jedinečný pro každý server Azure. Microsoft automaticky otočí tyto certifikáty nejméně každých 90 dní. Obecný popis transparentní šifrování dat, naleznete v tématu [transparentní šifrování dat (TDE)].

## <a name="enabling-encryption"></a>Povolení šifrování
Povolit transparentní šifrování dat pro Azure migrovat databázi, která ukládá data z databáze povolenou funkcí Stretch SQL serveru, proveďte následující akce:

1. Otevřít v databázi [webu Azure portal](https://portal.azure.com)
2. V okně databáze klikněte na tlačítko **nastavení** tlačítko
3. Vyberte **transparentní šifrování dat** možnost ![][1]
4. Vyberte **na** nastavení a pak vyberte **uložit**
   ![][2]

## <a name="disabling-encryption"></a>Zakázáním šifrování
Zakázat transparentní šifrování dat pro Azure migrovat databázi, která ukládá data z databáze povolenou funkcí Stretch SQL serveru, proveďte následující akce:

1. Otevřít v databázi [webu Azure portal](https://portal.azure.com)
2. V okně databáze klikněte na tlačítko **nastavení** tlačítko
3. Vyberte **transparentní šifrování dat** možnost
4. Vyberte **vypnout** nastavení a pak vyberte **uložit**

<!--Anchors-->
[Transparentní šifrování dat (TDE)]: https://msdn.microsoft.com/library/bb934049.aspx


<!--Image references-->
[1]: ./media/sql-server-stretch-database-encryption-tde/stretchtde1.png
[2]: ./media/sql-server-stretch-database-encryption-tde/stretchtde2.png


<!--Link references-->
