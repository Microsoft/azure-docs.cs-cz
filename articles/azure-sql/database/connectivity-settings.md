---
title: Nastavení připojení pro Azure SQL Database a datový sklad
description: Tento dokument popisuje nastavení verze TLS (Transport Layer Security) a proxy server vs. Redirect pro Azure SQL Database a Azure synapse Analytics.
services: sql-database
ms.service: sql-database
titleSuffix: Azure SQL Database and Azure Synapse Analytics (formerly SQL Data Warehouse)
ms.topic: conceptual
author: rohitnayakmsft
ms.author: rohitna
ms.reviewer: carlrab, vanto
ms.date: 07/06/2020
ms.openlocfilehash: f664ffbfc9aa38dcf8eb7736b28613efb95bde63
ms.sourcegitcommit: bf1340bb706cf31bb002128e272b8322f37d53dd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/03/2020
ms.locfileid: "89438173"
---
# <a name="azure-sql-connectivity-settings"></a>Nastavení připojení k Azure SQL
[!INCLUDE[appliesto-sqldb-asa](../includes/appliesto-sqldb-asa.md)]

V tomto článku se seznámíte s nastaveními, která řídí připojení k serveru pro Azure SQL Database a Azure synapse Analytics. Tato nastavení se vztahují na **všechny** databáze SQL Database a Azure synapse přidružené k tomuto serveru.

> [!IMPORTANT]
> Tento článek se *nevztahuje na* **SPRAVOVANOU instanci Azure SQL** .

Nastavení připojení jsou přístupná z obrazovky **brány firewall a virtuální sítě** , jak je znázorněno na následujícím snímku obrazovky:

 ![Snímek obrazovky s nastavením připojení][1]

> [!NOTE]
> Po použití těchto nastavení se projeví **okamžitě** a může dojít ke ztrátě připojení pro klienty, pokud nesplňují požadavky pro každé nastavení.

## <a name="deny-public-network-access"></a>Odepřít přístup k veřejné síti

Když je nastavení **Odepřít přístup k veřejné síti** nastavené na **Ano**, povolí se jenom připojení prostřednictvím privátních koncových bodů. Pokud je toto nastavení nastaveno na hodnotu **ne** (výchozí), klienti se mohou připojit pomocí veřejných koncových bodů (pravidla brány firewall založené na protokolu IP, pravidel brány firewall založené na virtuální síti) nebo soukromých koncových bodů (pomocí privátního propojení), jak je uvedeno v části [Přehled přístupu k síti](network-access-controls-overview.md). 

 ![Snímek obrazovky s přístupem odepřít přístup k veřejné síti][2]

Jakékoli pokusy o nastavení nastavení **Odepřít přístup k veřejné síti** na **Ano** , aniž by se žádné z existujících privátních koncových bodů na logickém serveru nezdařily, chybová zpráva podobná této:  

```output
Error 42102
Unable to set Deny Public Network Access to Yes since there is no private endpoint enabled to access the server. 
Please set up private endpoints and retry the operation. 
```

Je-li nastavení **Odepřít přístup k veřejné síti** nastaveno na **hodnotu Ano**, jsou povolena pouze připojení prostřednictvím privátních koncových bodů a všechna připojení prostřednictvím veřejných koncových bodů budou odepřena s chybovou zprávou podobnou této:  

```output
Error 47073
An instance-specific error occurred while establishing a connection to SQL Server. 
The public network interface on this server is not accessible. 
To connect to this server, use the Private Endpoint from inside your virtual network.
```

Je-li nastavení **Odepřít přístup k veřejné síti** nastavené na **hodnotu Ano**, všechny pokusy o přidání nebo aktualizaci pravidel brány firewall budou odepřeny s chybovou zprávou podobnou této:

```output
Error 42101
Unable to create or modify firewall rules when public network interface for the server is disabled. 
To manage server or database level firewall rules, please enable the public network interface.
```

## <a name="change-public-network-access-via-powershell"></a>Změna přístupu k veřejné síti přes PowerShell

> [!IMPORTANT]
> Modul PowerShell Azure Resource Manager je stále podporován Azure SQL Database, ale všechny budoucí vývojové prostředí jsou pro modul AZ. SQL. Tyto rutiny naleznete v tématu [AzureRM. SQL](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Argumenty pro příkazy v modulech AZ a v modulech AzureRm jsou v podstatě identické. Následující skript vyžaduje [modul Azure PowerShell](/powershell/azure/install-az-ps).

Následující skript prostředí PowerShell ukazuje, jak `Get` a `Set` vlastnost **přístup k veřejné síti** na úrovni serveru:

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

## <a name="minimal-tls-version"></a>Minimální verze protokolu TLS 

Nastavení verze [protokolu TLS (minimální přenosná vrstva zabezpečení)](https://support.microsoft.com/help/3135244/tls-1-2-support-for-microsoft-sql-server) umožňuje zákazníkům řídit verzi TLS, kterou používá jejich Azure SQL Database.

V současnosti podporujeme TLS 1,0, 1,1 a 1,2. Nastavení minimální verze protokolu TLS zajistí, že budou podporovány následné novější verze TLS. Například vyberte verzi TLS vyšší než 1,1. znamená, že jsou přijímána pouze připojení k TLS 1,1 a 1,2 a TLS 1,0 je odmítnuta. Po otestování, jestli je vaše aplikace podporuje, doporučujeme nastavit minimální verzi protokolu TLS na 1,2, protože zahrnuje opravy chyb zabezpečení nalezené v předchozích verzích a je nejvyšší verzí TLS podporovaná v Azure SQL Database.

> [!IMPORTANT]
> Výchozí hodnota pro minimální verzi TLS je povolení všech verzí. Po vykonání verze TLS ale nemůžete obnovit výchozí nastavení.

Pro zákazníky s aplikacemi, které spoléhají na starší verze TLS, doporučujeme nastavit minimální verzi TLS podle požadavků vašich aplikací. Pro zákazníky, kteří spoléhají na aplikace, aby se připojili pomocí nešifrovaného připojení, doporučujeme nenastavit žádnou minimální verzi TLS.

Další informace najdete v tématu [požadavky na TLS pro SQL Database připojení](connect-query-content-reference-guide.md#tls-considerations-for-database-connectivity).

Po nastavení minimální verze protokolu TLS se pokusy o přihlášení od klientů, kteří používají verzi protokolu TLS nižší než minimální verze protokolu TLS serveru, nezdaří s následující chybou:

```output
Error 47072
Login failed with invalid TLS version
```

## <a name="set-minimal-tls-version-via-powershell"></a>Nastavení minimální verze protokolu TLS prostřednictvím PowerShellu

> [!IMPORTANT]
> Modul PowerShell Azure Resource Manager je stále podporován Azure SQL Database, ale všechny budoucí vývojové prostředí jsou pro modul AZ. SQL. Tyto rutiny naleznete v tématu [AzureRM. SQL](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Argumenty pro příkazy v modulech AZ a v modulech AzureRm jsou v podstatě identické. Následující skript vyžaduje [modul Azure PowerShell](/powershell/azure/install-az-ps).

Následující skript prostředí PowerShell ukazuje, jak `Get` a `Set` vlastnost **Minimální verze protokolu TLS** na úrovni logického serveru:

```powershell
#Get the Minimal TLS Version property
(Get-AzSqlServer -ServerName sql-server-name -ResourceGroupName sql-server-group).MinimalTlsVersion

# # Update Minimal TLS Version to 1.2
$SecureString = ConvertTo-SecureString "password" -AsPlainText -Force

Set-AzSqlServer -ServerName sql-server-name -ResourceGroupName sql-server-group -SqlAdministratorPassword $SecureString  -MinimalTlsVersion "1.2"
```

## <a name="set-minimal-tls-version-via-azure-cli"></a>Nastavení minimální verze protokolu TLS prostřednictvím rozhraní příkazového řádku Azure

> [!IMPORTANT]
> Všechny skripty v této části vyžadují rozhraní příkazového [řádku Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli).

### <a name="azure-cli-in-a-bash-shell"></a>Rozhraní příkazového řádku Azure v prostředí bash

Následující skript rozhraní příkazového řádku ukazuje, jak změnit nastavení **Minimální verze protokolu TLS** v prostředí bash:

```azurecli-interactive
# Get current setting for Minimal TLS Version
az sql server show -n sql-server-name -g sql-server-group --query "minimalTlsVersion"

# Update setting for Minimal TLS Version
az sql server update -n sql-server-name -g sql-server-group --set minimalTlsVersion="1.2"
```


## <a name="change-connection-policy"></a>Změna zásad připojení

[Zásady připojení](connectivity-architecture.md#connection-policy) určují, jak se klienti připojují k Azure SQL Database.


## <a name="change-connection-policy-via-powershell"></a>Změna zásad připojení prostřednictvím PowerShellu

> [!IMPORTANT]
> Modul PowerShell Azure Resource Manager je stále podporován Azure SQL Database, ale všechny budoucí vývojové prostředí jsou pro modul AZ. SQL. Tyto rutiny naleznete v tématu [AzureRM. SQL](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Argumenty pro příkazy v modulech AZ a v modulech AzureRm jsou v podstatě identické. Následující skript vyžaduje [modul Azure PowerShell](/powershell/azure/install-az-ps).

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

- Přehled o tom, jak funguje konektivita v Azure SQL Database, najdete v tématu [Architektura připojení](connectivity-architecture.md) .
- Informace o tom, jak změnit zásady připojení pro server, najdete v tématu Connection [-Policy](https://docs.microsoft.com/cli/azure/sql/server/conn-policy).

<!--Image references-->
[1]: media/single-database-create-quickstart/manage-connectivity-settings.png
[2]: media/single-database-create-quickstart/manage-connectivity-flowchart.png
