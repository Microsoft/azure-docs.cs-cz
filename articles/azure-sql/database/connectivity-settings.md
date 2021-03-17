---
title: Nastavení připojení pro Azure SQL Database a Azure synapse Analytics
description: V tomto článku se dozvíte, jak zvolit verzi protokolu TLS (Transport Layer Security) a proxy server oproti přesměrování Azure SQL Database a Azure synapse Analytics.
services: sql-database
ms.service: sql-database
titleSuffix: Azure SQL Database and Azure Synapse Analytics
ms.topic: how-to
author: rohitnayakmsft
ms.author: rohitna
ms.reviewer: sstein, vanto
ms.date: 07/06/2020
ms.openlocfilehash: 84fdca96f2ce42c608e7def98f6a3400964cfe46
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/03/2021
ms.locfileid: "101691922"
---
# <a name="azure-sql-connectivity-settings"></a>Nastavení připojení Azure SQL
[!INCLUDE[appliesto-sqldb-asa](../includes/appliesto-sqldb-asa.md)]

V tomto článku se seznámíte s nastaveními, která řídí připojení k serveru pro Azure SQL Database a Azure synapse Analytics. Tato nastavení se vztahují na všechny databáze SQL Database a Azure synapse Analytics přidružené k tomuto serveru.

> [!IMPORTANT]
> Tento článek se nevztahuje na spravovanou instanci SQL Azure.

Nastavení připojení jsou přístupná z obrazovky **brány firewall a virtuální sítě** , jak je znázorněno na následujícím snímku obrazovky:

 ![Snímek obrazovky okna nastavení připojení][1]

> [!NOTE]
> Tato nastavení se projeví ihned po jejich použití. Pokud se vaši zákazníci neshodují s požadavky pro každé nastavení, může dojít ke ztrátě připojení.

## <a name="deny-public-network-access"></a>Odepření přístupu k veřejné síti

Když je možnost **Odepřít přístup k veřejné síti** nastavená na **Ano**, povolí se jenom připojení prostřednictvím privátních koncových bodů. Pokud toto nastavení není **(výchozí** ), můžou se zákazníci připojit pomocí veřejných koncových bodů (s pravidly brány firewall na základě IP adresy nebo pomocí pravidel brány firewall na základě virtuální sítě) nebo privátních koncových bodů (pomocí privátního odkazu Azure), jak je uvedeno v [přehledu přístupu k síti](network-access-controls-overview.md).

 ![Diagram znázorňující, že možnost Odepřít přístup k veřejné síti je nastavená na Ano, a když je odepřený přístup k veřejné síti nastavený na ne.][2]

Všechny pokusy o nastavení **Odepřít přístup k veřejné síti** na **Ano** , aniž by se žádné existující privátní koncové body na logickém serveru nezdařily, chybová zpráva podobná této:  

```output
Error 42102
Unable to set Deny Public Network Access to Yes since there is no private endpoint enabled to access the server.
Please set up private endpoints and retry the operation.
```

> [!NOTE]
> Pokud chcete definovat pravidla brány firewall virtuální sítě na logickém serveru, který už je nakonfigurovaný pomocí privátních koncových bodů, nastavte **Odepřít přístup k veřejné síti** na **ne**.

Když je možnost **Odepřít přístup k veřejné síti** nastavená na **Ano**, povolí se jenom připojení prostřednictvím privátních koncových bodů. Všechna připojení prostřednictvím veřejných koncových bodů budou odepřena s chybovou zprávou podobnou této:  

```output
Error 47073
An instance-specific error occurred while establishing a connection to SQL Server. 
The public network interface on this server is not accessible. 
To connect to this server, use the Private Endpoint from inside your virtual network.
```

Je-li **odepřen přístup k veřejné síti** nastaven na **hodnotu Ano**, všechny pokusy o přidání nebo aktualizaci pravidel brány firewall budou odepřeny s chybovou zprávou podobnou této:

```output
Error 42101
Unable to create or modify firewall rules when public network interface for the server is disabled. 
To manage server or database level firewall rules, please enable the public network interface.
```

## <a name="change-public-network-access-via-powershell"></a>Změna přístupu k veřejné síti přes PowerShell

> [!IMPORTANT]
> Azure SQL Database stále podporuje modul PowerShellu Azure Resource Manager, ale všechny budoucí vývojové prostředí jsou pro modul AZ. SQL. Tyto rutiny naleznete v tématu [AzureRM. SQL](/powershell/module/AzureRM.Sql/). Argumenty pro příkazy v modulech AZ a v modulech AzureRm jsou v podstatě identické. Následující skript vyžaduje [modul Azure PowerShell](/powershell/azure/install-az-ps).

Následující skript prostředí PowerShell ukazuje, jak `Get` a `Set` vlastnost **přístup k veřejné síti** na úrovni serveru:

```powershell
# Get the Public Network Access property
(Get-AzSqlServer -ServerName sql-server-name -ResourceGroupName sql-server-group).PublicNetworkAccess

# Update Public Network Access to Disabled
$SecureString = ConvertTo-SecureString "password" -AsPlainText -Force

Set-AzSqlServer -ServerName sql-server-name -ResourceGroupName sql-server-group -SqlAdministratorPassword $SecureString -PublicNetworkAccess "Disabled"
```

## <a name="change-public-network-access-via-cli"></a>Změna přístupu k veřejné síti přes rozhraní příkazového řádku

> [!IMPORTANT]
> Všechny skripty v této části vyžadují rozhraní příkazového [řádku Azure CLI](/cli/azure/install-azure-cli).

### <a name="azure-cli-in-a-bash-shell"></a>Rozhraní příkazového řádku Azure v prostředí bash

Následující skript rozhraní příkazového řádku ukazuje, jak změnit nastavení **přístupu k veřejné síti** v prostředí bash:

```azurecli-interactive

# Get current setting for Public Network Access
az sql server show -n sql-server-name -g sql-server-group --query "publicNetworkAccess"

# Update setting for Public Network Access
az sql server update -n sql-server-name -g sql-server-group --set publicNetworkAccess="Disabled"
```

## <a name="minimal-tls-version"></a>Minimální verze protokolu TLS 

Minimální verze [protokolu TLS (Transport Layer Security)](https://support.microsoft.com/help/3135244/tls-1-2-support-for-microsoft-sql-server) umožňuje zákazníkům zvolit, jakou verzi TLS SQL Database používá.

V současné době podporujeme protokoly TLS 1.0, 1.1 a 1.2. Nastavení minimální verze protokolu TLS zajišťuje podporu novějších verzí protokolu TLS. Například zvolíte-li TLS verze 1,1, budou přijímána pouze připojení k TLS 1,1 a 1,2 a připojení k TLS 1,0 budou odmítnuta. Po otestování, že vaše aplikace podporují protokol TLS 1.2, doporučujeme nastavit minimální verzi protokolu TLS na tuto verzi. Tato verze obsahuje opravy ohrožení zabezpečení v předchozích verzích a představuje nejvyšší podporovanou verzi protokolu TLS ve službě Azure SQL Database.

> [!IMPORTANT]
> Výchozí hodnotou pro minimální verzi TLS je povolení všech verzí. Po vykonání verze TLS se nemůžete vrátit k výchozímu nastavení.

Zákazníkům s aplikacemi, které se spoléhají na starší verze protokolu TLS, doporučujeme nastavit minimální verzi protokolu TLS podle požadavků konkrétních aplikací. Zákazníkům využívajícím aplikace, které se připojují pomocí nešifrovaného připojení, doporučujeme nenastavovat žádnou minimální verzi protokolu TLS.

Další informace najdete v tématu [požadavky na TLS pro SQL Database připojení](connect-query-content-reference-guide.md#tls-considerations-for-database-connectivity).

Po nastavení minimální verze protokolu TLS se přihlašovací pokusy od zákazníků, kteří používají verzi protokolu TLS nižší než minimální verze protokolu TLS serveru, nezdaří s následující chybou:

```output
Error 47072
Login failed with invalid TLS version
```

## <a name="set-the-minimal-tls-version-via-powershell"></a>Nastavení minimální verze protokolu TLS prostřednictvím PowerShellu

> [!IMPORTANT]
> Azure SQL Database stále podporuje modul PowerShellu Azure Resource Manager, ale všechny budoucí vývojové prostředí jsou pro modul AZ. SQL. Tyto rutiny naleznete v tématu [AzureRM. SQL](/powershell/module/AzureRM.Sql/). Argumenty pro příkazy v modulech AZ a v modulech AzureRm jsou v podstatě identické. Následující skript vyžaduje [modul Azure PowerShell](/powershell/azure/install-az-ps).

Následující skript prostředí PowerShell ukazuje, jak `Get` a `Set` vlastnost **Minimální verze protokolu TLS** na úrovni logického serveru:

```powershell
# Get the Minimal TLS Version property
(Get-AzSqlServer -ServerName sql-server-name -ResourceGroupName sql-server-group).MinimalTlsVersion

# Update Minimal TLS Version to 1.2
$SecureString = ConvertTo-SecureString "password" -AsPlainText -Force

Set-AzSqlServer -ServerName sql-server-name -ResourceGroupName sql-server-group -SqlAdministratorPassword $SecureString  -MinimalTlsVersion "1.2"
```

## <a name="set-the-minimal-tls-version-via-the-azure-cli"></a>Nastavení minimální verze protokolu TLS prostřednictvím rozhraní příkazového řádku Azure

> [!IMPORTANT]
> Všechny skripty v této části vyžadují rozhraní příkazového [řádku Azure CLI](/cli/azure/install-azure-cli).

### <a name="azure-cli-in-a-bash-shell"></a>Rozhraní příkazového řádku Azure v prostředí bash

Následující skript rozhraní příkazového řádku ukazuje, jak změnit nastavení **Minimální verze protokolu TLS** v prostředí bash:

```azurecli-interactive
# Get current setting for Minimal TLS Version
az sql server show -n sql-server-name -g sql-server-group --query "minimalTlsVersion"

# Update setting for Minimal TLS Version
az sql server update -n sql-server-name -g sql-server-group --set minimalTlsVersion="1.2"
```

## <a name="change-the-connection-policy"></a>Změna zásad připojení

[Zásady připojení](connectivity-architecture.md#connection-policy) určují, jak se zákazníci připojí k Azure SQL Database.

## <a name="change-the-connection-policy-via-powershell"></a>Změna zásad připojení prostřednictvím PowerShellu

> [!IMPORTANT]
> Azure SQL Database stále podporuje modul PowerShellu Azure Resource Manager, ale všechny budoucí vývojové prostředí jsou pro modul AZ. SQL. Tyto rutiny naleznete v tématu [AzureRM. SQL](/powershell/module/AzureRM.Sql/). Argumenty pro příkazy v modulech AZ a v modulech AzureRm jsou v podstatě identické. Následující skript vyžaduje [modul Azure PowerShell](/powershell/azure/install-az-ps).

Následující skript prostředí PowerShell ukazuje, jak změnit zásady připojení pomocí prostředí PowerShell:

```powershell
# Get SQL Server ID
$sqlserverid=(Get-AzSqlServer -ServerName sql-server-name -ResourceGroupName sql-server-group).ResourceId

# Set URI
$id="$sqlserverid/connectionPolicies/Default"

# Get current connection policy
(Get-AzResource -ResourceId $id -ApiVersion 2014-04-01 -Verbose).Properties.ConnectionType

# Update connection policy
Set-AzResource -ResourceId $id -Properties @{"connectionType" = "Proxy"} -f
```

## <a name="change-the-connection-policy-via-the-azure-cli"></a>Změna zásad připojení prostřednictvím rozhraní příkazového řádku Azure

> [!IMPORTANT]
> Všechny skripty v této části vyžadují rozhraní příkazového [řádku Azure CLI](/cli/azure/install-azure-cli).

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

Následující skript CLI ukazuje, jak změnit zásady připojení z příkazového řádku Windows (s nainstalovaným rozhraním Azure CLI):

```azurecli
# Get SQL Server ID and set URI
FOR /F "tokens=*" %g IN ('az sql server show --resource-group myResourceGroup-571418053 --name server-538465606 --query "id" -o tsv') do (SET sqlserverid=%g/connectionPolicies/Default)

# Get current connection policy
az resource show --ids %sqlserverid%

# Update connection policy
az resource update --ids %sqlserverid% --set properties.connectionType=Proxy
```

## <a name="next-steps"></a>Další kroky

- Přehled o tom, jak funguje konektivita v Azure SQL Database, najdete v tématu [Architektura připojení](connectivity-architecture.md).
- Informace o tom, jak změnit zásady připojení pro server, najdete v tématu Connection [-Policy](/cli/azure/sql/server/conn-policy).

<!--Image references-->
[1]: media/single-database-create-quickstart/manage-connectivity-settings.png
[2]: media/single-database-create-quickstart/manage-connectivity-flowchart.png
