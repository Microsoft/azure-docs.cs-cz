---
title: Spravovat repliky čtení-Azure PowerShell-Azure Database for PostgreSQL
description: Naučte se, jak nastavit a spravovat repliky pro čtení v Azure Database for PostgreSQL pomocí PowerShellu.
author: sr-msft
ms.author: srranga
ms.service: postgresql
ms.topic: how-to
ms.date: 06/08/2020
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 8bacb7a434cfa04dbdfdaf39d9fd3a0baab5f11a
ms.sourcegitcommit: 3bcce2e26935f523226ea269f034e0d75aa6693a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/23/2020
ms.locfileid: "92489808"
---
# <a name="how-to-create-and-manage-read-replicas-in-azure-database-for-postgresql-using-powershell"></a>Jak vytvářet a spravovat repliky pro čtení v Azure Database for PostgreSQL pomocí prostředí PowerShell

V tomto článku se naučíte vytvářet a spravovat repliky pro čtení ve službě Azure Database for PostgreSQL pomocí prostředí PowerShell. Další informace o replikách pro čtení najdete v tématu [Přehled](concepts-read-replicas.md).

## <a name="azure-powershell"></a>Azure PowerShell

Pomocí PowerShellu můžete vytvářet a spravovat repliky pro čtení.

## <a name="prerequisites"></a>Předpoklady

K dokončení tohoto průvodce budete potřebovat:

- [Modul AZ PowerShell](/powershell/azure/install-az-ps) nainstalovaný místně nebo [Azure Cloud Shell](https://shell.azure.com/) v prohlížeči
- [Server Azure Database for PostgreSQL](quickstart-create-postgresql-server-database-using-azure-powershell.md)

> [!IMPORTANT]
> I když je modul PowerShell AZ. PostgreSql ve verzi Preview, musíte ho nainstalovat samostatně z modulu AZ PowerShellu pomocí následujícího příkazu: `Install-Module -Name Az.PostgreSql -AllowPrerelease` .
> Jakmile je modul PowerShellu AZ. PostgreSql všeobecně dostupný, bude součástí budoucna k tomu, aby vydaná vydání modulu PowerShellu a nativně dostupná v rámci Azure Cloud Shell.

Pokud se rozhodnete použít prostředí PowerShell místně, připojte se k účtu Azure pomocí rutiny [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) .

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

> [!IMPORTANT]
> Funkce replika čtení je k dispozici pouze pro Azure Database for PostgreSQL servery v cenové úrovni optimalizované pro Pro obecné účely nebo paměť. Ujistěte se, že je primární server v jedné z těchto cenových úrovní.

### <a name="create-a-read-replica"></a>Vytvoření repliky pro čtení

Server repliky pro čtení se dá vytvořit pomocí následujícího příkazu:

```azurepowershell-interactive
Get-AzPostgreSqlServer -Name mydemoserver -ResourceGroupName myresourcegroup |
  New-AzPostgreSqlServerReplica -Name mydemoreplicaserver -ResourceGroupName myresourcegroup
```

`New-AzPostgreSqlServerReplica`Příkaz vyžaduje následující parametry:

| Nastavení | Příklad hodnoty | Popis  |
| --- | --- | --- |
| ResourceGroupName |  myresourcegroup |  Skupina prostředků, ve které se vytvoří server repliky.  |
| Name | mydemoreplicaserver | Název nového serveru repliky, který se vytvoří. |

Pokud chcete vytvořit repliku čtení ve více oblastech, použijte parametr **Location (umístění** ). V následujícím příkladu se vytvoří replika v oblasti **západní USA** .

```azurepowershell-interactive
Get-AzPostgreSqlServer -Name mrdemoserver -ResourceGroupName myresourcegroup |
  New-AzMariaDServerReplica -Name mydemoreplicaserver -ResourceGroupName myresourcegroup -Location westus
```

Další informace o tom, které oblasti můžete vytvořit repliku v, najdete v [článku věnovaném konceptům pro čtení replik](concepts-read-replicas.md).

Ve výchozím nastavení se repliky čtení vytvoří se stejnou konfigurací serveru jako primární, pokud není zadaný parametr **SKU** .

> [!NOTE]
> Doporučuje se udržovat konfiguraci serveru repliky ve stejné nebo větší hodnotě než primární, aby bylo zajištěno, že je replika schopná s hlavní hodnotou.

### <a name="list-replicas-for-a-primary-server"></a>Vypíše repliky pro primární server.

Chcete-li zobrazit všechny repliky pro daný primární server, spusťte následující příkaz:

```azurepowershell-interactive
Get-AzMariaDReplica -ResourceGroupName myresourcegroup -ServerName mydemoserver
```

`Get-AzMariaDReplica`Příkaz vyžaduje následující parametry:

| Nastavení | Příklad hodnoty | Popis  |
| --- | --- | --- |
| ResourceGroupName |  myresourcegroup |  Skupina prostředků, do které se vytvoří server repliky.  |
| ServerName | mydemoserver | Název nebo ID primárního serveru. |

### <a name="delete-a-replica-server"></a>Odstranění serveru repliky

Odstranění serveru repliky pro čtení se dá provést spuštěním `Remove-AzPostgreSqlServer` rutiny.

```azurepowershell-interactive
Remove-AzPostgreSqlServer -Name mydemoreplicaserver -ResourceGroupName myresourcegroup
```

### <a name="delete-a-primary-server"></a>Odstranění primárního serveru

> [!IMPORTANT]
> Odstranění primárního serveru zastaví replikaci na všechny servery repliky a odstraní samotný primární server. Ze serverů replik se stanou samostatné servery, které teď podporují čtení i zápis.

Pokud chcete odstranit primární server, můžete spustit `Remove-AzPostgreSqlServer` rutinu.

```azurepowershell-interactive
Remove-AzPostgreSqlServer -Name mydemoserver -ResourceGroupName myresourcegroup
```

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Restartování serveru Azure Database for PostgreSQL pomocí prostředí PowerShell](howto-restart-server-powershell.md)