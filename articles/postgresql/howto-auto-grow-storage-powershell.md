---
title: Automatické zvětšování úložiště – Azure PowerShell-Azure Database for PostgreSQL
description: Tento článek popisuje, jak můžete povolit automatické zvětšování úložiště pomocí prostředí PowerShell v Azure Database for PostgreSQL.
author: ambhatna
ms.author: ambhatna
ms.service: postgresql
ms.topic: how-to
ms.date: 06/08/2020
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: a186c6e176cde20474cdf4772b8724ad3f356a3e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "87493614"
---
# <a name="auto-grow-storage-in-azure-database-for-postgresql-server-using-powershell"></a>Automatické zvětšení úložiště na serveru Azure Database for PostgreSQL pomocí prostředí PowerShell

Tento článek popisuje, jak můžete nakonfigurovat úložiště serveru Azure Database for PostgreSQL pro růst, aniž by to ovlivnilo zatížení.

Automatické zvětšení úložiště zabrání vašemu serveru [dosáhnout limitu úložiště](/azure/postgresql/concepts-pricing-tiers#reaching-the-storage-limit) a stane se jen pro čtení. U serverů s 100 GB nebo méně zřízeným úložištěm se velikost zvýší o 5 GB, pokud je volné místo menší než 10%. U serverů s více než 100 GB zřízeného úložiště se velikost zvýší o 5%, pokud je volné místo menší než 10 GB. V části úložiště [Azure Database for PostgreSQL cenové úrovně](/azure/postgresql/concepts-pricing-tiers#storage)platí omezení maximální velikosti úložiště.

> [!IMPORTANT]
> Mějte na paměti, že úložiště je možné škálovat pouze nahoru, ne dolů.

## <a name="prerequisites"></a>Požadavky

K dokončení tohoto průvodce budete potřebovat:

- [Modul AZ PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps) nainstalovaný místně nebo [Azure Cloud Shell](https://shell.azure.com/) v prohlížeči
- [Server Azure Database for PostgreSQL](quickstart-create-postgresql-server-database-using-azure-powershell.md)

> [!IMPORTANT]
> I když je modul PowerShell AZ. PostgreSql ve verzi Preview, musíte ho nainstalovat samostatně z modulu AZ PowerShellu pomocí následujícího příkazu: `Install-Module -Name Az.PostgreSql -AllowPrerelease` .
> Jakmile je modul PowerShellu AZ. PostgreSql všeobecně dostupný, bude součástí budoucna k tomu, aby vydaná vydání modulu PowerShellu a nativně dostupná v rámci Azure Cloud Shell.

Pokud se rozhodnete použít prostředí PowerShell místně, připojte se k účtu Azure pomocí rutiny [Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount) .

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
