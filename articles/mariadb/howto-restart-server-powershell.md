---
title: Restart serveru – Azure PowerShell-Azure Database for MariaDB
description: Tento článek popisuje, jak můžete restartovat server Azure Database for MariaDB pomocí prostředí PowerShell.
author: savjani
ms.author: pariks
ms.service: jroth
ms.topic: how-to
ms.date: 5/26/2020
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 0311111285d7dc0d60bc63ce9cef2be3f0ddfb19
ms.sourcegitcommit: 52e3d220565c4059176742fcacc17e857c9cdd02
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/21/2021
ms.locfileid: "98664864"
---
# <a name="restart-azure-database-for-mariadb-server-using-powershell"></a>Restartování serveru Azure Database for MariaDB pomocí prostředí PowerShell

Toto téma popisuje, jak můžete restartovat server Azure Database for MariaDB. Možná budete muset restartovat server z důvodů údržby, což způsobí krátkou výpadky během operace.

Pokud je služba zaneprázdněná, je restartování serveru blokované. Například služba může zpracovávat dříve požadovanou operaci, jako je například škálování virtuální jádra.

Množství času potřebného k dokončení restartování závisí na procesu obnovení MariaDB. Chcete-li zkrátit dobu restartování, doporučujeme, abyste minimalizovali množství aktivity, ke kterým došlo na serveru před restartováním.

## <a name="prerequisites"></a>Požadavky

K dokončení tohoto průvodce budete potřebovat:

- [Modul AZ PowerShell](/powershell/azure/install-az-ps) nainstalovaný místně nebo [Azure Cloud Shell](https://shell.azure.com/) v prohlížeči
- [Server Azure Database for MariaDB](quickstart-create-mariadb-server-database-using-azure-powershell.md)

> [!IMPORTANT]
> I když je modul PowerShell AZ. MariaDb ve verzi Preview, musíte ho nainstalovat samostatně z modulu AZ PowerShellu pomocí následujícího příkazu: `Install-Module -Name Az.MariaDb -AllowPrerelease` .
> Jakmile je modul PowerShellu AZ. MariaDb všeobecně dostupný, bude součástí budoucna k tomu, aby vydaná vydání modulu PowerShellu a nativně dostupná v rámci Azure Cloud Shell.

Pokud se rozhodnete použít prostředí PowerShell místně, připojte se k účtu Azure pomocí rutiny [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) .

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="restart-the-server"></a>Restartování serveru

Restartujte server pomocí následujícího příkazu:

```azurepowershell-interactive
Restart-AzMariaDbServer -Name mydemoserver -ResourceGroupName myresourcegroup
```

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Vytvoření serveru Azure Database for MariaDB pomocí prostředí PowerShell](quickstart-create-mariadb-server-database-using-azure-powershell.md)