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
ms.openlocfilehash: d632d078db55696dad7bb05e72368b13610b5978
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "87091415"
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
3. Vyberte snímek obrazovky s možností **transparentního šifrování dat** ![ Azure Portal a zobrazte okno nastavení. V části Obecné se zvýrazní transparentní šifrování dat.][1]
4. Vyberte nastavení **zapnuto** a pak vyberte **Uložit** 
    ![ snímek obrazovky Azure Portal, když se zobrazí okno transparentní šifrování dat. Šifrování dat je zapnuté a zvýrazní se tlačítko Uložit.][2]

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
