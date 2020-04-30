---
title: Automatické zvětšování úložiště – Azure PowerShell-Azure Database for MySQL
description: Tento článek popisuje, jak můžete povolit automatické zvětšování úložiště pomocí prostředí PowerShell v Azure Database for MySQL.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 4/28/2020
ms.openlocfilehash: 09431a6690be751324bb796415d308d3b7727dad
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "82254093"
---
# <a name="auto-grow-storage-in-azure-database-for-mysql-storage-using-powershell"></a>Automatické zvětšování úložiště v Azure Database for MySQLovém úložišti pomocí PowerShellu

Tento článek popisuje, jak můžete nakonfigurovat úložiště serveru Azure Database for MySQL pro růst, aniž by to ovlivnilo zatížení.

Automatické zvětšení úložiště zabrání vašemu serveru [dosáhnout limitu úložiště](/azure/mysql/concepts-pricing-tiers#reaching-the-storage-limit) a stane se jen pro čtení. U serverů s 100 GB nebo méně zřízeným úložištěm se velikost zvýší o 5 GB, pokud je volné místo menší než 10%. U serverů s více než 100 GB zřízeného úložiště se velikost zvýší o 5%, pokud je volné místo menší než 10 GB. V části úložiště [Azure Database for MySQL cenové úrovně](/azure/mysql/concepts-pricing-tiers#storage)platí omezení maximální velikosti úložiště.

> [!IMPORTANT]
> Mějte na paměti, že úložiště je možné škálovat pouze nahoru, ne dolů.

## <a name="prerequisites"></a>Požadavky

K dokončení tohoto průvodce budete potřebovat:

- [Modul AZ PowerShell](/powershell/azure/install-az-ps) nainstalovaný místně nebo [Azure Cloud Shell](https://shell.azure.com/) v prohlížeči
- [Server Azure Database for MySQL](quickstart-create-mysql-server-database-using-azure-powershell.md)

> [!IMPORTANT]
> I když je modul PowerShell AZ. MySql ve verzi Preview, musíte ho nainstalovat samostatně z modulu AZ PowerShellu pomocí následujícího příkazu: `Install-Module -Name Az.MySql -AllowPrerelease`.
> Jakmile je modul PowerShellu AZ. MySql všeobecně dostupný, bude součástí budoucna k budoucímu AZ PowerShell Release releases a k dispozici nativně z Azure Cloud Shell.

Pokud se rozhodnete použít prostředí PowerShell místně, připojte se k účtu Azure pomocí rutiny [Connect-AzAccount](/powershell/module/az.accounts/Connect-AzAccount) .

## <a name="enable-mysql-server-storage-auto-grow"></a>Povolit automatické zvětšování úložiště serveru MySQL

Povolte automatické zvětšení úložiště na stávajícím serveru pomocí následujícího příkazu:

```azurepowershell-interactive
Update-AzMySqlServer -Name mydemoserver -ResourceGroupName myresourcegroup -StorageAutogrow Enabled
```

Při vytváření nového serveru pomocí následujícího příkazu Povolte automatické zvětšení úložiště serveru:

```azurepowershell-interactive
$Password = Read-Host -Prompt 'Please enter your password' -AsSecureString
New-AzMySqlServer -Name mydemoserver -ResourceGroupName myresourcegroup -Sku GP_Gen5_2 -StorageAutogrow Enabled -Location westus -AdministratorUsername myadmin -AdministratorLoginPassword $Password
```

## <a name="next-steps"></a>Další kroky

Přečtěte si informace o [tom, jak vytvářet výstrahy na metrikách](howto-alert-on-metric.md).