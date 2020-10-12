---
title: Restart serveru – Azure PowerShell-Azure Database for MariaDB
description: Tento článek popisuje, jak můžete restartovat server Azure Database for MariaDB pomocí prostředí PowerShell.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: how-to
ms.date: 5/26/2020
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 37fb724b83e80c1265755e6440f152143d419051
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "87503068"
---
# <a name="restart-azure-database-for-mariadb-server-using-powershell"></a>Restartování serveru Azure Database for MariaDB pomocí prostředí PowerShell

Toto téma popisuje, jak můžete restartovat server Azure Database for MariaDB. Možná budete muset restartovat server z důvodů údržby, což způsobí krátkou výpadky během operace.

Pokud je služba zaneprázdněná, je restartování serveru blokované. Například služba může zpracovávat dříve požadovanou operaci, jako je například škálování virtuální jádra.

Množství času potřebného k dokončení restartování závisí na procesu obnovení MariaDB. Chcete-li zkrátit dobu restartování, doporučujeme, abyste minimalizovali množství aktivity, ke kterým došlo na serveru před restartováním.

## <a name="prerequisites"></a>Požadavky

K dokončení tohoto průvodce budete potřebovat:

- [Modul AZ PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps) nainstalovaný místně nebo [Azure Cloud Shell](https://shell.azure.com/) v prohlížeči
- [Server Azure Database for MariaDB](quickstart-create-mariadb-server-database-using-azure-powershell.md)

> [!IMPORTANT]
> I když je modul PowerShell AZ. MariaDb ve verzi Preview, musíte ho nainstalovat samostatně z modulu AZ PowerShellu pomocí následujícího příkazu: `Install-Module -Name Az.MariaDb -AllowPrerelease` .
> Jakmile je modul PowerShellu AZ. MariaDb všeobecně dostupný, bude součástí budoucna k tomu, aby vydaná vydání modulu PowerShellu a nativně dostupná v rámci Azure Cloud Shell.

Pokud se rozhodnete použít prostředí PowerShell místně, připojte se k účtu Azure pomocí rutiny [Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount) .

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="restart-the-server"></a>Restartování serveru

Restartujte server pomocí následujícího příkazu:

```azurepowershell-interactive
Restart-AzMariaDbServer -Name mydemoserver -ResourceGroupName myresourcegroup
```

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Vytvoření serveru Azure Database for MariaDB pomocí prostředí PowerShell](quickstart-create-mariadb-server-database-using-azure-powershell.md)
