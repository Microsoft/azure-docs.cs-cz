---
title: Povolit transparentní šifrování dat pro roztažnou databázi
description: Povolení transparentního šifrování dat (TDE) pro databázi roztažení serveru SQL Server v Azure
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74034013"
---
# <a name="enable-transparent-data-encryption-tde-for-stretch-database-on-azure"></a>Povolení transparentního šifrování dat (TDE) pro roztažnou databázi v Azure
> [!div class="op_single_selector"]
> * [Portál Azure](sql-server-stretch-database-encryption-tde.md)
> * [TSQL](sql-server-stretch-database-tde-tsql.md)
>
>

Transparentní šifrování dat (TDE) pomáhá chránit před hrozbou škodlivé aktivity prováděním šifrování a dešifrování databáze v reálném čase, přidružené zálohy a soubory protokolu transakcí v klidovém stavu bez nutnosti změny v aplikaci.

Transparentní šifrování dat šifruje úložiště celé databáze pomocí symetrického klíče nazývaného šifrovací klíč databáze. Šifrovací klíč databáze je chráněn integrovaným certifikátem serveru. Integrovaný certifikát serveru je jedinečný pro každý server Azure. Společnost Microsoft automaticky otočí tyto certifikáty alespoň každých 90 dní. Obecný popis TDE naleznete v tématu [Transparentní šifrování dat (TDE)].

## <a name="enabling-encryption"></a>Povolení šifrování
Chcete-li povolit TDE pro databázi Azure, která ukládá data migrovaná z databáze SQL Serveru s podporou stretch, proveďte následující kroky:

1. Otevření databáze na [webu Azure Portal](https://portal.azure.com)
2. V okně databáze klepněte na tlačítko **Nastavení.**
3. Výběr možnosti **Transparentní šifrování dat**![][1]
4. Vyberte nastavení **Zapnuto** a pak vyberte **Uložit**
   ![][2]

## <a name="disabling-encryption"></a>Zakázání šifrování
Chcete-li zakázat TDE pro databázi Azure, která ukládá data migrovaná z databáze SQL Serveru s podporou stretch, proveďte následující kroky:

1. Otevření databáze na [webu Azure Portal](https://portal.azure.com)
2. V okně databáze klepněte na tlačítko **Nastavení.**
3. Výběr možnosti **Transparentní šifrování dat**
4. Vyberte nastavení **Vypnuto** a pak vyberte **Uložit**

<!--Anchors-->
[Transparentní šifrování dat (TDE)]: https://msdn.microsoft.com/library/bb934049.aspx


<!--Image references-->
[1]: ./media/sql-server-stretch-database-encryption-tde/stretchtde1.png
[2]: ./media/sql-server-stretch-database-encryption-tde/stretchtde2.png


<!--Link references-->
