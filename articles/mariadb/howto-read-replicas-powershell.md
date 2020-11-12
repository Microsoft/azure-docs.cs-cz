---
title: Spravovat repliky čtení-Azure PowerShell-Azure Database for MariaDB
description: Naučte se, jak nastavit a spravovat repliky pro čtení v Azure Database for MariaDB pomocí PowerShellu.
author: savjani
ms.author: pariks
ms.service: mariadb
ms.topic: how-to
ms.date: 6/10/2020
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: a5921026c8f260683f091870850350fcc4619f6b
ms.sourcegitcommit: 6ab718e1be2767db2605eeebe974ee9e2c07022b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/12/2020
ms.locfileid: "94538610"
---
# <a name="how-to-create-and-manage-read-replicas-in-azure-database-for-mariadb-using-powershell"></a>Jak vytvářet a spravovat repliky pro čtení v Azure Database for MariaDB pomocí prostředí PowerShell

V tomto článku se naučíte vytvářet a spravovat repliky pro čtení ve službě Azure Database for MariaDB pomocí prostředí PowerShell. Další informace o replikách pro čtení najdete v tématu [Přehled](concepts-read-replicas.md).

## <a name="azure-powershell"></a>Azure PowerShell

Pomocí PowerShellu můžete vytvářet a spravovat repliky pro čtení.

## <a name="prerequisites"></a>Požadavky

K dokončení tohoto průvodce budete potřebovat:

- [Modul AZ PowerShell](/powershell/azure/install-az-ps) nainstalovaný místně nebo [Azure Cloud Shell](https://shell.azure.com/) v prohlížeči
- [Server Azure Database for MariaDB](quickstart-create-mariadb-server-database-using-azure-powershell.md)

> [!IMPORTANT]
> I když je modul PowerShell AZ. MariaDb ve verzi Preview, musíte ho nainstalovat samostatně z modulu AZ PowerShellu pomocí následujícího příkazu: `Install-Module -Name Az.MariaDb -AllowPrerelease` .
> Jakmile je modul PowerShellu AZ. MariaDb všeobecně dostupný, bude součástí budoucna k tomu, aby vydaná vydání modulu PowerShellu a nativně dostupná v rámci Azure Cloud Shell.

Pokud se rozhodnete použít prostředí PowerShell místně, připojte se k účtu Azure pomocí rutiny [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) .

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

> [!IMPORTANT]
> Funkce replika čtení je k dispozici pouze pro Azure Database for MariaDB servery v cenové úrovni optimalizované pro Pro obecné účely nebo paměť. Ujistěte se, že je zdrojový server v jedné z těchto cenových úrovní.

### <a name="create-a-read-replica"></a>Vytvoření repliky pro čtení

> [!IMPORTANT]
> Když vytvoříte repliku pro zdroj, který nemá žádné existující repliky, zdroj se nejdřív restartuje, aby se připravil pro replikaci. Vezměte v úvahu a udělejte tyto operace v době mimo špičku.

Server repliky pro čtení se dá vytvořit pomocí následujícího příkazu:

```azurepowershell-interactive
Get-AzMariaDbServer -Name mydemoserver -ResourceGroupName myresourcegroup |
  New-AzMariaDbServerReplica -Name mydemoreplicaserver -ResourceGroupName myresourcegroup
```

`New-AzMariaDbServerReplica`Příkaz vyžaduje následující parametry:

| Nastavení | Příklad hodnoty | Popis  |
| --- | --- | --- |
| ResourceGroupName |  myresourcegroup |  Skupina prostředků, ve které se vytvoří server repliky.  |
| Název | mydemoreplicaserver | Název nového serveru repliky, který se vytvoří. |

Pokud chcete vytvořit repliku čtení ve více oblastech, použijte parametr **Location (umístění** ). V následujícím příkladu se vytvoří replika v oblasti **západní USA** .

```azurepowershell-interactive
Get-AzMariaDbServer -Name mrdemoserver -ResourceGroupName myresourcegroup |
  New-AzMariaDServerReplica -Name mydemoreplicaserver -ResourceGroupName myresourcegroup -Location westus
```

Další informace o tom, které oblasti můžete vytvořit repliku v, najdete v [článku věnovaném konceptům pro čtení replik](concepts-read-replicas.md).

Ve výchozím nastavení se repliky čtení vytvoří se stejnou konfigurací serveru jako zdroj, pokud není zadaný parametr **SKU** .

> [!NOTE]
> Doporučuje se udržovat konfiguraci serveru repliky ve stejné nebo větší hodnotě než zdroj, aby bylo zajištěno, že je replika schopná s hlavní hodnotou.

### <a name="list-replicas-for-a-source-server"></a>Vypíše repliky pro zdrojový server.

Chcete-li zobrazit všechny repliky pro daný zdrojový server, spusťte následující příkaz:

```azurepowershell-interactive
Get-AzMariaDReplica -ResourceGroupName myresourcegroup -ServerName mydemoserver
```

`Get-AzMariaDReplica`Příkaz vyžaduje následující parametry:

| Nastavení | Příklad hodnoty | Popis  |
| --- | --- | --- |
| ResourceGroupName |  myresourcegroup |  Skupina prostředků, do které se vytvoří server repliky.  |
| ServerName | mydemoserver | Název nebo ID zdrojového serveru. |

### <a name="delete-a-replica-server"></a>Odstranění serveru repliky

Odstranění serveru repliky pro čtení se dá provést spuštěním `Remove-AzMariaDbServer` rutiny.

```azurepowershell-interactive
Remove-AzMariaDbServer -Name mydemoreplicaserver -ResourceGroupName myresourcegroup
```

### <a name="delete-a-source-server"></a>Odstranění zdrojového serveru

> [!IMPORTANT]
> Odstraněním zdrojového serveru se zastaví replikace na všechny servery replik a odstraní se samotný zdrojový server. Ze serverů replik se stanou samostatné servery, které teď podporují čtení i zápis.

Pokud chcete odstranit zdrojový server, můžete spustit `Remove-AzMariaDbServer` rutinu.

```azurepowershell-interactive
Remove-AzMariaDbServer -Name mydemoserver -ResourceGroupName myresourcegroup
```

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Restartování serveru Azure Database for MariaDB pomocí prostředí PowerShell](howto-restart-server-powershell.md)