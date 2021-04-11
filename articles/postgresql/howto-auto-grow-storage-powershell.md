---
title: Automatické zvětšování úložiště – Azure PowerShell-Azure Database for PostgreSQL
description: Tento článek popisuje, jak můžete povolit automatické zvětšování úložiště pomocí prostředí PowerShell v Azure Database for PostgreSQL.
author: rothja
ms.author: jroth
ms.service: postgresql
ms.topic: how-to
ms.date: 06/08/2020
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 05333aa4a42b821366ea7ad0a564781422fda66a
ms.sourcegitcommit: b0557848d0ad9b74bf293217862525d08fe0fc1d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/07/2021
ms.locfileid: "106551046"
---
# <a name="auto-grow-storage-in-azure-database-for-postgresql-server-using-powershell"></a>Automatické zvětšení úložiště na serveru Azure Database for PostgreSQL pomocí prostředí PowerShell

Tento článek popisuje, jak můžete nakonfigurovat úložiště serveru Azure Database for PostgreSQL pro růst, aniž by to ovlivnilo zatížení.

Automatické zvětšení úložiště zabrání vašemu serveru [dosáhnout limitu úložiště](./concepts-pricing-tiers.md#reaching-the-storage-limit) a stane se jen pro čtení. U serverů s 100 GB nebo méně zřízeným úložištěm se velikost zvýší o 5 GB, pokud je volné místo menší než 10%. U serverů s více než 100 GB zřízeného úložiště se velikost zvýší o 5%, pokud je volné místo menší než 10 GB. V části úložiště [Azure Database for PostgreSQL cenové úrovně](./concepts-pricing-tiers.md#storage)platí omezení maximální velikosti úložiště.

> [!IMPORTANT]
> Mějte na paměti, že úložiště je možné škálovat pouze nahoru, ne dolů.

## <a name="prerequisites"></a>Požadavky

K dokončení tohoto průvodce budete potřebovat:

- [Modul AZ PowerShell](/powershell/azure/install-az-ps) nainstalovaný místně nebo [Azure Cloud Shell](https://shell.azure.com/) v prohlížeči
- [Server Azure Database for PostgreSQL](quickstart-create-postgresql-server-database-using-azure-powershell.md)

> [!IMPORTANT]
> I když je modul PowerShell AZ. PostgreSql ve verzi Preview, musíte ho nainstalovat samostatně z modulu AZ PowerShellu pomocí následujícího příkazu: `Install-Module -Name Az.PostgreSql -AllowPrerelease` .
> Jakmile je modul PowerShellu AZ. PostgreSql všeobecně dostupný, bude součástí budoucna k tomu, aby vydaná vydání modulu PowerShellu a nativně dostupná v rámci Azure Cloud Shell.

Pokud se rozhodnete použít prostředí PowerShell místně, připojte se k účtu Azure pomocí rutiny [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) .

## <a name="enable-postgresql-server-storage-auto-grow"></a>Povolit automatické zvětšování úložiště serveru PostgreSQL

Povolte automatické zvětšení úložiště na stávajícím serveru pomocí následujícího příkazu:

```azurepowershell-interactive
Update-AzPostgreSqlServer -Name mydemoserver -ResourceGroupName myresourcegroup -StorageAutogrow Enabled
```

Při vytváření nového serveru pomocí následujícího příkazu Povolte automatické zvětšení úložiště serveru:

```azurepowershell-interactive
$Password = Read-Host -Prompt 'Please enter your password' -AsSecureString
New-AzPostgreSqlServer -Name mydemoserver -ResourceGroupName myresourcegroup -Sku GP_Gen5_2 -StorageAutogrow Enabled -Location westus -AdministratorUsername myadmin -AdministratorLoginPassword $Password
```

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Jak vytvořit a spravovat repliky pro čtení v Azure Database for PostgreSQL pomocí prostředí PowerShell](howto-read-replicas-powershell.md).