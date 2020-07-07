---
title: Povolit transparentní šifrování dat pro Stretch Database
description: Povolit transparentní šifrování dat (TDE) pro SQL Server Stretch Database v Azure
services: sql-server-stretch-database
documentationcenter: ''
ms.assetid: a44ed8f5-b416-4c41-9b1e-b7271f10bdc3
ms.service: sql-server-stretch-database
ms.workload: data-management
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 06/14/2016
author: blazem-msft
ms.author: blazem
ms.reviewer: jroth
manager: jroth
ms.custom: seo-lt-2019
ms.openlocfilehash: 465338bb793ad7b4fc406e9d1c073b9dc0dc06c8
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "74034013"
---
# <a name="enable-transparent-data-encryption-tde-for-stretch-database-on-azure"></a>Povolit transparentní šifrování dat (TDE) pro Stretch Database v Azure
> [!div class="op_single_selector"]
> * [Azure Portal](sql-server-stretch-database-encryption-tde.md)
> * [TSQL](sql-server-stretch-database-tde-tsql.md)
>
>

Transparentní šifrování dat (TDE) pomáhá chránit před hrozbou škodlivých aktivit tím, že provádí šifrování a dešifrování databáze, přidružených záloh a souborů protokolu transakcí v reálném čase bez nutnosti změny aplikace.

Transparentní šifrování dat šifruje úložiště celé databáze pomocí symetrického klíče nazývaného šifrovací klíč databáze. Šifrovací klíč databáze je chráněn integrovaným certifikátem serveru. Integrovaný certifikát serveru je pro každý server Azure jedinečný. Microsoft tyto certifikáty automaticky přetočí nejméně každých 90 dnů. Obecný popis TDE naleznete v tématu [transparentní šifrování dat (TDE)].

## <a name="enabling-encryption"></a>Povolení šifrování
Pokud chcete povolit TDE pro databázi Azure, která ukládá data migrovaná z databáze SQL Server s povolenou funkcí Stretch, proveďte následující akce:

1. Otevřete databázi v [Azure Portal](https://portal.azure.com)
2. V okně databáze klikněte na tlačítko **Nastavení** .
3. Vybrat možnost **transparentní šifrování dat**![][1]
4. Vyberte nastavení **zapnuto** a pak vyberte **Uložit** .
   ![][2]

## <a name="disabling-encryption"></a>Zakázání šifrování
Pokud chcete zakázat TDE pro databázi Azure, která ukládá data migrovaná z databáze SQL Server s povolenou funkcí Stretch, proveďte následující akce:

1. Otevřete databázi v [Azure Portal](https://portal.azure.com)
2. V okně databáze klikněte na tlačítko **Nastavení** .
3. Vybrat možnost **transparentní šifrování dat**
4. Vyberte nastavení **vypnuto** a pak vyberte **Uložit** .

<!--Anchors-->
[Transparentní šifrování dat]: https://msdn.microsoft.com/library/bb934049.aspx


<!--Image references-->
[1]: ./media/sql-server-stretch-database-encryption-tde/stretchtde1.png
[2]: ./media/sql-server-stretch-database-encryption-tde/stretchtde2.png


<!--Link references-->
