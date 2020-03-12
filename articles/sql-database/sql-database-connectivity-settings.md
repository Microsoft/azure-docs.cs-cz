---
title: Nastavení připojení pro Azure SQL Database a datový sklad
description: Tento dokument vysvětluje nastavení verze TLS a proxy server vs. Redirect pro Azure SQL.
services: sql-database
ms.service: sql-database
titleSuffix: Azure SQL Database and SQL Data Warehouse
ms.topic: conceptual
author: rohitnayakmsft
ms.author: rohitna
ms.reviewer: carlrab, vanto
ms.date: 03/09/2020
ms.openlocfilehash: d18fdee85bd0fbabe68fe9890c4a2dc74366041d
ms.sourcegitcommit: be53e74cd24bbabfd34597d0dcb5b31d5e7659de
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/11/2020
ms.locfileid: "79096679"
---
# <a name="azure-sql-connectivity-settings"></a>Nastavení připojení Azure SQL
> [!NOTE]
> Tento článek se týká Azure SQL serveru a databází SQL Database i SQL Data Warehouse, které jsou vytvořené na Azure SQL serveru. Pro zjednodušení se SQL Database používá k označení SQL Database i SQL Data Warehouse.

> [!IMPORTANT]
> Tento článek se *nevztahuje na* **Azure SQL Database spravovanou instanci** .

Tento článek představuje nastavení, které řídí připojení k Azure SQL Database na úrovni serveru. Tato nastavení platí pro **všechny** databáze SQL Database a SQL Data Warehouse přidružené k serveru.

> [!NOTE]
> Po použití těchto nastavení se projeví **okamžitě** a může dojít ke ztrátě připojení pro klienty, pokud nesplňují požadavky pro každé nastavení.

Nastavení připojení jsou přístupná z okna **brány firewall a virtuální sítě** , jak je znázorněno na následujícím snímku obrazovky:

 ![Snímek obrazovky s nastavením připojení][1]


## <a name="deny-public-network-access"></a>Odepřít přístup k veřejné síti
Pokud je v Azure Portal nastavení **Odepřít přístup k veřejné síti** nastavené na **Ano**, povolí se jenom připojení prostřednictvím privátních koncových bodů. Pokud je toto nastavení nastaveno na **ne**, klienti se mohou připojit pomocí privátního nebo veřejného koncového bodu.

Po nastavení **Odepřít přístup k veřejné síti** na **Ano**se pokusy o přihlášení od klientů, kteří používají veřejný koncový bod, nezdaří a zobrazí se následující chyba:

```output
Error 47073
An instance-specific error occurred while establishing a connection to SQL Server. The public network interface on this server is not accessible. To connect to this server, use the Private Endpoint from inside your virtual network.
```

## <a name="change-public-network-access-via-powershell"></a>Změna přístupu k veřejné síti přes PowerShell
[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> Modul PowerShell Azure Resource Manager je stále podporován Azure SQL Database, ale všechny budoucí vývojové prostředí jsou pro modul AZ. SQL. Tyto rutiny naleznete v tématu [AzureRM. SQL](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Argumenty pro příkazy v modulech AZ a v modulech AzureRm jsou v podstatě identické. Následující skript vyžaduje [modul Azure PowerShell](/powershell/azure/install-az-ps).

Následující skript prostředí PowerShell ukazuje, jak `Get` a `Set` vlastnost **přístup k veřejné síti** na úrovni logického serveru:

```powershell
#Get the Public Network Access property
(Get-AzSqlServer -ServerName sql-server-name -ResourceGroupName sql-server-group).PublicNetworkAccess

# Update Public Network Access to Disabled
$SecureString = ConvertTo-SecureString "password" -AsPlainText -Force

Set-AzSqlServer -ServerName sql-server-name -ResourceGroupName sql-server-group -SqlAdministratorPassword $SecureString -PublicNetworkAccess "Enabled" 
```

## <a name="change-public-network-access-via-cli"></a>Změna přístupu k veřejné síti přes rozhraní příkazového řádku
> [!IMPORTANT]
> Všechny skripty v této části vyžadují rozhraní příkazového [řádku Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli).

### <a name="azure-cli-in-a-bash-shell"></a>Rozhraní příkazového řádku Azure v prostředí bash
Následující skript rozhraní příkazového řádku ukazuje, jak změnit **přístup k veřejné síti** v prostředí bash:

```azurecli-interactive

# Get current setting for Public Network Access
az sql server show -n sql-server-name -g sql-server-group --query "publicNetworkAccess"

# Update setting for Public Network Access
az sql server update -n sql-server-name -g sql-server-group --set publicNetworkAccess="Disabled"

```


## <a name="connection-policy"></a>Zásady připojení
[Zásady připojení](sql-database-connectivity-architecture.md#connection-policy) určují, jak se klienti připojují k Azure SQL Server. 

## <a name="change-connection-policy-via-powershell"></a>Změna zásad připojení prostřednictvím PowerShellu
[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> Modul PowerShell Azure Resource Manager je stále podporován Azure SQL Database, ale všechny budoucí vývojové prostředí jsou pro modul AZ. SQL. Tyto rutiny naleznete v tématu [AzureRM. SQL](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Argumenty pro příkazy v modulech AZ a v modulech AzureRm jsou v podstatě identické. Následující skript vyžaduje [modul Azure PowerShell](/powershell/azure/install-az-ps).

Následující skript prostředí PowerShell ukazuje, jak změnit zásady připojení pomocí prostředí PowerShell:

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

## <a name="change-connection-policy-via-azure-cli"></a>Změna zásad připojení prostřednictvím rozhraní příkazového řádku Azure
> [!IMPORTANT]
> Všechny skripty v této části vyžadují rozhraní příkazového [řádku Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli).

### <a name="azure-cli-in-a-bash-shell"></a>Rozhraní příkazového řádku Azure v prostředí bash
Následující skript rozhraní příkazového řádku ukazuje, jak změnit zásady připojení v prostředí bash: 

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
Následující skript CLI ukazuje, jak změnit zásady připojení z příkazového řádku Windows (s nainstalovaným rozhraním Azure CLI).

```azurecli
# Get SQL Server ID and set URI
FOR /F "tokens=*" %g IN ('az sql server show --resource-group myResourceGroup-571418053 --name server-538465606 --query "id" -o tsv') do (SET sqlserverid=%g/connectionPolicies/Default)

# Get current connection policy
az resource show --ids %sqlserverid%

# Update connection policy
az resource update --ids %sqlserverid% --set properties.connectionType=Proxy
```

## <a name="next-steps"></a>Další kroky
- Přehled o tom, jak funguje konektivita v Azure SQL Database, najdete v tématu [Architektura připojení Azure SQL](sql-database-connectivity-architecture.md) .
- Informace o tom, jak změnit zásady Azure SQL Databaseho připojení pro Azure SQL Database Server, najdete v tématu Connection [-Policy](https://docs.microsoft.com/cli/azure/sql/server/conn-policy).

<!--Image references-->
[1]: ./media/sql-database-get-started-portal/manage-connectivity-settings.png
