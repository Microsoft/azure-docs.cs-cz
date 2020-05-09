---
title: Konfigurace parametrů serveru-Azure PowerShell-Azure Database for MySQL
description: Tento článek popisuje, jak nakonfigurovat parametry služby v Azure Database for MySQL pomocí prostředí PowerShell.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.devlang: azurepowershell
ms.topic: conceptual
ms.date: 4/29/2020
ms.openlocfilehash: 0de816d25bbc1563885413d8dbd52dc7bda7d538
ms.sourcegitcommit: 50ef5c2798da04cf746181fbfa3253fca366feaa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/30/2020
ms.locfileid: "82615081"
---
# <a name="customize-azure-database-for-mysql-server-parameters-using-powershell"></a>Přizpůsobení parametrů Azure Database for MySQL serveru pomocí PowerShellu

Pomocí prostředí PowerShell můžete vypsat, zobrazit a aktualizovat parametry konfigurace serveru Azure Database for MySQL. Podmnožina konfigurací modulu se zveřejňuje na úrovni serveru a dá se upravit.

## <a name="prerequisites"></a>Požadavky

K dokončení tohoto průvodce budete potřebovat:

- [Modul AZ PowerShell](/powershell/azure/install-az-ps) nainstalovaný místně nebo [Azure Cloud Shell](https://shell.azure.com/) v prohlížeči
- [Server Azure Database for MySQL](quickstart-create-mysql-server-database-using-azure-powershell.md)

> [!IMPORTANT]
> I když je modul PowerShell AZ. MySql ve verzi Preview, musíte ho nainstalovat samostatně z modulu AZ PowerShellu pomocí následujícího příkazu: `Install-Module -Name Az.MySql -AllowPrerelease`.
> Jakmile je modul PowerShellu AZ. MySql všeobecně dostupný, bude součástí budoucna k budoucímu AZ PowerShell Release releases a k dispozici nativně z Azure Cloud Shell.

Pokud se rozhodnete použít prostředí PowerShell místně, připojte se k účtu Azure pomocí rutiny [Connect-AzAccount](/powershell/module/az.accounts/Connect-AzAccount) .

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="list-server-configuration-parameters-for-azure-database-for-mysql-server"></a>Vypsat parametry konfigurace serveru pro Azure Database for MySQL server

Chcete-li zobrazit seznam všech parametrů s modifikátory na serveru a jejich hodnotách, spusťte `Get-AzMySqlConfiguration` rutinu.

Následující příklad zobrazí seznam parametrů konfigurace serveru pro server **mydemoserver** ve skupině prostředků **myresourcegroup**.

```azurepowershell-interactive
Get-AzMySqlConfiguration -ResourceGroupName myresourcegroup -ServerName mydemoserver
```

Definice všech uvedených parametrů naleznete v části referenční dokumentace MySQL na [systémových proměnných serveru](https://dev.mysql.com/doc/refman/5.7/en/server-system-variables.html).

## <a name="show-server-configuration-parameter-details"></a>Zobrazit podrobnosti parametru konfigurace serveru

Chcete-li zobrazit podrobnosti o konkrétním parametru konfigurace pro server, spusťte `Get-AzMySqlConfiguration` rutinu a zadejte parametr **Name** .

Tento příklad ukazuje podrobnosti parametru konfigurace **serveru\_pomalého dotazu\_** pro server **mydemoserver** v části Skupina prostředků **myresourcegroup**.

```azurepowershell-interactive
Get-AzMySqlConfiguration -Name slow_query_log -ResourceGroupName myresourcegroup -ServerName mydemoserver
```

## <a name="modify-a-server-configuration-parameter-value"></a>Změna hodnoty parametru konfigurace serveru

Můžete také změnit hodnotu určitého parametru konfigurace serveru, který aktualizuje základní konfigurační hodnotu pro modul serveru MySQL. Pokud chcete aktualizovat konfiguraci, použijte `Update-AzMySqlConfiguration` rutinu.

Pokud chcete aktualizovat parametr konfigurace serveru s **pomalým\_\_dotazem** na serveru **mydemoserver** v části Skupina prostředků **myresourcegroup**.

```azurepowershell-interactive
Update-AzMySqlConfiguration -Name slow_query_log -ResourceGroupName myresourcegroup -ServerName mydemoserver -Value On
```

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Automatické zvětšování úložiště na serveru Azure Database for MySQL pomocí prostředí PowerShell](howto-auto-grow-storage-powershell.md).
