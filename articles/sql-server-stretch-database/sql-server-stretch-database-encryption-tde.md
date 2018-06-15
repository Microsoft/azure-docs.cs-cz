---
title: Povolit transparentní šifrování dat pro funkce Stretch Database - Azure | Microsoft Docs
description: Povolit transparentní šifrování dat (šifrování TDE) pro SQL Server Stretch Database na Azure
services: sql-server-stretch-database
documentationcenter: ''
author: douglaslMS
manager: barbkess
editor: ''
ms.assetid: a44ed8f5-b416-4c41-9b1e-b7271f10bdc3
ms.service: sql-server-stretch-database
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/14/2016
ms.author: douglasl
ms.openlocfilehash: ceb355d2ba872ed5d3886c6dc82ca75b1854db0a
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/21/2017
ms.locfileid: "23872922"
---
# <a name="enable-transparent-data-encryption-tde-for-stretch-database-on-azure"></a>Povolit transparentní šifrování dat (šifrování TDE) pro funkce Stretch Database na Azure
> [!div class="op_single_selector"]
> * [portál Azure Portal](sql-server-stretch-database-encryption-tde.md)
> * [TSQL](sql-server-stretch-database-tde-tsql.md)
>
>

Transparentní šifrování šifrování dat (TDE) pomáhá chránit před ohrožením škodlivých aktivit provedením v reálném čase šifrování a dešifrování databáze, přidružených záloh a souborů protokolů transakci bez nutnosti změny aplikace.

Šifrování TDE zašifruje úložiště celé databáze pomocí symetrický klíč s názvem šifrovací klíč databáze. Šifrovací klíč databáze je chráněn certifikát integrovaného serveru. Certifikát integrovaného serveru je jedinečný pro každý server Azure. Microsoft automaticky otočí tyto certifikáty alespoň jednou za 90 dní. Obecný popis TDE, najdete v části [transparentní šifrování šifrování dat (TDE)].

## <a name="enabling-encryption"></a>Povolení šifrování
Chcete-li povolit šifrování TDE Azure migrovat databázi, která ukládá data z databáze serveru SQL povolenou funkcí Stretch, provádět následující akce:

1. Otevřít v databázi [portálu Azure](https://portal.azure.com)
2. V okně databáze klikněte na **nastavení** tlačítko
3. Vyberte **transparentní šifrování dat** možnost![][1]
4. Vyberte **na** nastavení a potom vyberte **uložit**
   ![][2]

## <a name="disabling-encryption"></a>Zakázáním šifrování
Zakázat šifrování TDE Azure migrovat databázi, která ukládá data z databáze povolenou funkcí Stretch SQL Server, provádět následující akce:

1. Otevřít v databázi [portálu Azure](https://portal.azure.com)
2. V okně databáze klikněte na **nastavení** tlačítko
3. Vyberte **transparentní šifrování dat** možnost
4. Vyberte **vypnout** nastavení a potom vyberte **uložit**

<!--Anchors-->
[transparentní šifrování šifrování dat (TDE)]: https://msdn.microsoft.com/library/bb934049.aspx


<!--Image references-->
[1]: ./media/sql-server-stretch-database-encryption-tde/stretchtde1.png
[2]: ./media/sql-server-stretch-database-encryption-tde/stretchtde2.png


<!--Link references-->
