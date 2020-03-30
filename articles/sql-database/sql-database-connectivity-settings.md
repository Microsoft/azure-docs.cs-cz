---
title: Nastavení připojení pro Azure SQL Database a datový sklad
description: Tento dokument vysvětluje volbu verze TLS a nastavení proxy vs. přesměrování pro Azure SQL
services: sql-database
ms.service: sql-database
titleSuffix: Azure SQL Database and SQL Data Warehouse
ms.topic: conceptual
author: rohitnayakmsft
ms.author: rohitna
ms.reviewer: carlrab, vanto
ms.date: 03/09/2020
ms.openlocfilehash: d18fdee85bd0fbabe68fe9890c4a2dc74366041d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79366083"
---
# <a name="azure-sql-connectivity-settings"></a>Nastavení připojení Azure SQL
> [!NOTE]
> Tento článek se vztahuje na Server Azure SQL a databáze SQL Database a SQL Data Warehouse, které jsou vytvořené na serveru Azure SQL. Pro zjednodušení se SQL Database používá k označení SQL Database i SQL Data Warehouse.

> [!IMPORTANT]
> Tento článek se *nevztahuje* na **spravovanou instanci Azure SQL Database**

Tento článek zavádí nastavení, která řídí připojení k Azure SQL Database na úrovni serveru. Tato nastavení platí pro **všechny** databáze databáze SQL a sql data warehouse databáze přidružené k serveru.

> [!NOTE]
> Jakmile jsou tato nastavení použita, **projeví** se okamžitě a mohou způsobit ztrátu připojení pro klienty, pokud nesplňují požadavky pro každé nastavení.

Nastavení připojení je přístupné z brány **firewall a virtuálních sítí,** jak je znázorněno na následujícím snímku obrazovky:

 ![Snímek obrazovky s nastavením připojení][1]


## <a name="deny-public-network-access"></a>Odepřít přístup k veřejné síti
Na webu Azure Portal, když je nastavení **Odepřít přístup k veřejné síti** nastaveno na **Ano**, jsou povolena pouze připojení přes privátní koncové body. Pokud je toto nastavení nastaveno na **ne**, klienti se mohou připojit pomocí soukromého nebo veřejného koncového bodu.

Po nastavení **Odepřít přístup k veřejné síti** **ano**, pokusy o přihlášení od klientů používajících veřejný koncový bod se nezdaří s následující chybou:

```output
Error 47073
An instance-specific error occurred while establishing a connection to SQL Server. The public network interface on this server is not accessible. To connect to this server, use the Private Endpoint from inside your virtual network.
```

## <a name="change-public-network-access-via-powershell"></a>Změna přístupu k veřejné síti přes PowerShell
[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> Modul PowerShell Azure Resource Manager je stále podporovaný službou Azure SQL Database, ale veškerý budoucí vývoj je pro modul Az.Sql. Tyto rutiny naleznete v tématu [AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Argumenty pro příkazy v modulu Az a v modulech AzureRm jsou v podstatě identické. Následující skript vyžaduje [modul Azure PowerShell](/powershell/azure/install-az-ps).

Následující skript prostředí PowerShell `Get` `Set` ukazuje, jak a vlastnost **Public Network Access** na úrovni logického serveru:

```powershell
#Get the Public Network Access property
(Get-AzSqlServer -ServerName sql-server-name -ResourceGroupName sql-server-group).PublicNetworkAccess

# Update Public Network Access to Disabled
$SecureString = ConvertTo-SecureString "password" -AsPlainText -Force

Set-AzSqlServer -ServerName sql-server-name -ResourceGroupName sql-server-group -SqlAdministratorPassword $SecureString -PublicNetworkAccess "Enabled" 
```

## <a name="change-public-network-access-via-cli"></a>Změna přístupu k veřejné síti prostřednictvím funkce vynastavení spojení se zásadou
> [!IMPORTANT]
> Všechny skripty v této části vyžaduje [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli).

### <a name="azure-cli-in-a-bash-shell"></a>Azure CLI v bash shellu
Následující skript příkazového příkazového příkazu ukazuje, jak změnit **přístup k veřejné síti** v prostředí bash:

```azurecli-interactive

# Get current setting for Public Network Access
az sql server show -n sql-server-name -g sql-server-group --query "publicNetworkAccess"

# Update setting for Public Network Access
az sql server update -n sql-server-name -g sql-server-group --set publicNetworkAccess="Disabled"

```


## <a name="connection-policy"></a>Zásady připojení
[Zásady připojení](sql-database-connectivity-architecture.md#connection-policy) určují, jak se klienti připojují k Azure SQL Serveru. 

## <a name="change-connection-policy-via-powershell"></a>Změna zásad připojení přes PowerShell
[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> Modul PowerShell Azure Resource Manager je stále podporovaný službou Azure SQL Database, ale veškerý budoucí vývoj je pro modul Az.Sql. Tyto rutiny naleznete v tématu [AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Argumenty pro příkazy v modulu Az a v modulech AzureRm jsou v podstatě identické. Následující skript vyžaduje [modul Azure PowerShell](/powershell/azure/install-az-ps).

Následující skript Prostředí PowerShell ukazuje, jak změnit zásady připojení pomocí Prostředí PowerShell:

```powershell
# Get SQL Server ID
$sqlserverid=(Get-AzSqlServer -ServerName sql-server-name -ResourceGroupName sql-server-group).ResourceId

# Set URI
$id="$sqlserverid/connectionPolicies/Default"

# Get current connection policy
(Get-AzResource -ResourceId $id).Properties.connectionType

# Update connection policy
Set-AzResource -ResourceId $id -Properties @{"connectionType" = "Proxy"} -f
```

## <a name="change-connection-policy-via-azure-cli"></a>Změna zásad připojení pomocí azure cli
> [!IMPORTANT]
> Všechny skripty v této části vyžaduje [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli).

### <a name="azure-cli-in-a-bash-shell"></a>Azure CLI v bash shellu
Následující skript příkazového příkazového příkazu ukazuje, jak změnit zásady připojení v prostředí bash: 

```azurecli-interactive
# Get SQL Server ID
sqlserverid=$(az sql server show -n sql-server-name -g sql-server-group --query 'id' -o tsv)

# Set URI
ids="$sqlserverid/connectionPolicies/Default"

# Get current connection policy
az resource show --ids $ids

# Update connection policy
az resource update --ids $ids --set properties.connectionType=Proxy
```

### <a name="azure-cli-from-a-windows-command-prompt"></a>Azure CLI z příkazového řádku Windows
Následující skript příkazového řádku ukazuje, jak změnit zásady připojení z příkazového řádku systému Windows (s nainstalovaným příkazovým příkazovým příkazem Azure).

```azurecli
# Get SQL Server ID and set URI
FOR /F "tokens=*" %g IN ('az sql server show --resource-group myResourceGroup-571418053 --name server-538465606 --query "id" -o tsv') do (SET sqlserverid=%g/connectionPolicies/Default)

# Get current connection policy
az resource show --ids %sqlserverid%

# Update connection policy
az resource update --ids %sqlserverid% --set properties.connectionType=Proxy
```

## <a name="next-steps"></a>Další kroky
- Přehled fungování připojení v Azure SQL Database najdete v článku [Architektura připojení Azure SQL](sql-database-connectivity-architecture.md)
- Informace o tom, jak změnit zásady připojení databáze Azure SQL pro server Azure SQL Database, naleznete [v zásadách conn.](https://docs.microsoft.com/cli/azure/sql/server/conn-policy)

<!--Image references-->
[1]: ./media/sql-database-get-started-portal/manage-connectivity-settings.png
