---
title: Konfigurace parametrů serveru-Azure PowerShell-Azure Database for PostgreSQL
description: Tento článek popisuje, jak nakonfigurovat parametry služby v Azure Database for PostgreSQL pomocí prostředí PowerShell.
author: lfittl-msft
ms.author: lufittl
ms.service: postgresql
ms.devlang: azurepowershell
ms.topic: how-to
ms.date: 06/08/2020
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 0fa49714365c00060ef7f11f5c9646141f707f4b
ms.sourcegitcommit: 19dce034650c654b656f44aab44de0c7a8bd7efe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/04/2020
ms.locfileid: "91707808"
---
# <a name="customize-azure-database-for-postgresql-server-parameters-using-powershell"></a>Přizpůsobení parametrů Azure Database for PostgreSQL serveru pomocí PowerShellu

Pomocí prostředí PowerShell můžete vypsat, zobrazit a aktualizovat parametry konfigurace serveru Azure Database for PostgreSQL. Podmnožina konfigurací modulu se zveřejňuje na úrovni serveru a dá se upravit.

## <a name="prerequisites"></a>Požadované součásti

K dokončení tohoto průvodce budete potřebovat:

- [Modul AZ PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps) nainstalovaný místně nebo [Azure Cloud Shell](https://shell.azure.com/) v prohlížeči
- [Server Azure Database for PostgreSQL](quickstart-create-postgresql-server-database-using-azure-powershell.md)

> [!IMPORTANT]
> I když je modul PowerShell AZ. PostgreSql ve verzi Preview, musíte ho nainstalovat samostatně z modulu AZ PowerShellu pomocí následujícího příkazu: `Install-Module -Name Az.PostgreSql -AllowPrerelease` .
> Jakmile je modul PowerShellu AZ. PostgreSql všeobecně dostupný, bude součástí budoucna k tomu, aby vydaná vydání modulu PowerShellu a nativně dostupná v rámci Azure Cloud Shell.

Pokud se rozhodnete použít prostředí PowerShell místně, připojte se k účtu Azure pomocí rutiny [Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount) .

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="list-server-configuration-parameters-for-azure-database-for-postgresql-server"></a>Vypsat parametry konfigurace serveru pro Azure Database for PostgreSQL Server

Chcete-li zobrazit seznam všech parametrů s modifikátory na serveru a jejich hodnotách, spusťte `Get-AzPostgreSqlConfiguration` rutinu.

Následující příklad zobrazí seznam parametrů konfigurace serveru pro server **mydemoserver** ve skupině prostředků **myresourcegroup**.

```azurepowershell-interactive
Get-AzPostgreSqlConfiguration -ResourceGroupName myresourcegroup -ServerName mydemoserver
```

Definice všech uvedených parametrů naleznete v části Reference PostgreSQL v tématu [proměnné prostředí](https://www.postgresql.org/docs/12/libpq-envars.html).

## <a name="show-server-configuration-parameter-details"></a>Zobrazit podrobnosti parametru konfigurace serveru

Chcete-li zobrazit podrobnosti o konkrétním parametru konfigurace pro server, spusťte `Get-AzPostgreSqlConfiguration` rutinu a zadejte parametr **Name** .

Tento příklad ukazuje podrobnosti parametru konfigurace serveru **pomalého \_ dotazu \_ ** pro server **mydemoserver** v části Skupina prostředků **myresourcegroup**.

```azurepowershell-interactive
Get-AzPostgreSqlConfiguration -Name slow_query_log -ResourceGroupName myresourcegroup -ServerName mydemoserver
```

## <a name="modify-a-server-configuration-parameter-value"></a>Změna hodnoty parametru konfigurace serveru

Můžete také změnit hodnotu určitého parametru konfigurace serveru, který aktualizuje základní konfigurační hodnotu pro modul PostgreSQL serveru. Pokud chcete aktualizovat konfiguraci, použijte `Update-AzPostgreSqlConfiguration` rutinu.

Pokud chcete aktualizovat parametr konfigurace serveru s **pomalým \_ dotazem \_ ** na serveru **mydemoserver** v části Skupina prostředků **myresourcegroup**.

```azurepowershell-interactive
Update-AzPostgreSqlConfiguration -Name slow_query_log -ResourceGroupName myresourcegroup -ServerName mydemoserver -Value On
```

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Automatické zvětšování úložiště na serveru Azure Database for PostgreSQL pomocí prostředí PowerShell](howto-auto-grow-storage-powershell.md).
