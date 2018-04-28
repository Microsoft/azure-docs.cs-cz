---
title: Architektura připojení k Azure SQL Database | Microsoft Docs
description: Tento dokument popisuje architekturu připojení k Azure SQLDB z Azure nebo z mimo Azure.
services: sql-database
author: CarlRabeler
manager: craigg
ms.service: sql-database
ms.custom: DBs & servers
ms.topic: article
ms.date: 01/24/2018
ms.author: carlrab
ms.openlocfilehash: 76af1ead671a6d6b279202eaf7886463d4cd8bb7
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/19/2018
---
# <a name="azure-sql-database-connectivity-architecture"></a>Architektura připojení k databázi Azure SQL 

Tento článek popisuje architekturu připojení k databázi SQL Azure a vysvětluje, jak různé součásti funkci, aby se směrovat provoz na instanci databáze SQL Azure. Tyto Azure SQL Database připojení funkce komponent směrovat síťový provoz do databáze Azure s klienty připojení v rámci Azure a klienti připojení z mimo Azure. Tento článek také obsahuje skript ukázky změnit, jak dojde k připojení a důležité informace související s změny výchozího nastavení připojení. 

## <a name="connectivity-architecture"></a>Architektura připojení

Následující diagram představuje podrobný přehled architektury připojení k databázi SQL Azure.

![Přehled architektury](./media/sql-database-connectivity-architecture/architecture-overview.png)


Následující kroky popisují, jak se naváže připojení k databázi Azure SQL přes Azure SQL Database softwaru-Vyrovnávání zatížení (SLB) a bránu Azure SQL Database.

- Klienti v rámci Azure nebo mimo Azure připojit k SLB, která má veřejnou IP adresu a naslouchá na portu 1433.
- SLB přesměruje přenosy na bráně Azure SQL Database.
- Brána přesměruje provoz na správný proxy middleware.
- Proxy middleware přesměruje přenosy dat k příslušné databázi Azure SQL.

> [!IMPORTANT]
> Každou z těchto součástí je distribuovat útok na dostupnost služby (Denial) ochrany integrované v síti a vrstva aplikací.
>

## <a name="connectivity-from-within-azure"></a>Připojení z v rámci Azure

Pokud se připojujete ze v rámci Azure, vaše připojení mají zásady připojení **přesměrování** ve výchozím nastavení. Zásady **přesměrování** znamená, že připojení po TCP je vytvoření relace k databázi Azure SQL relaci klienta se pak přesměrují na proxy middleware s změnu cílové virtuální IP adresa od bránu Azure SQL Database a který middleware proxy serveru. Následně všechny následující pakety toku přímo prostřednictvím proxy serveru middlewaru, obcházení brány Azure SQL Database. Následující diagram znázorňuje tento tok přenosů.

![Přehled architektury](./media/sql-database-connectivity-architecture/connectivity-from-within-azure.png)

## <a name="connectivity-from-outside-of-azure"></a>Připojení z mimo Azure

Pokud se připojujete ze mimo Azure, vaše připojení mají zásady připojení **Proxy** ve výchozím nastavení. Zásady **Proxy** znamená, že je vytvoření relace TCP prostřednictvím brány Azure SQL Database a všechny následující pakety toku prostřednictvím brány. Následující diagram znázorňuje tento tok přenosů.

![Přehled architektury](./media/sql-database-connectivity-architecture/connectivity-from-outside-azure.png)

## <a name="azure-sql-database-gateway-ip-addresses"></a>Azure SQL Database brány IP adresy

Pro připojení k databázi Azure SQL z místních prostředků, musíte povolit odchozí přenosy sítě k bráně Azure SQL Database pro vaši oblast Azure. Připojení, jdou pouze prostřednictvím brány při připojení v režimu proxy serveru, který je výchozím nastavením, pokud se připojujete z místních prostředků.

Následující tabulka uvádí primární a sekundární IP adresy brány Azure SQL Database pro všechny datové oblasti. Pro některé oblasti jsou dvě IP adresy. V těchto oblastech primární IP adresa je aktuální IP adresu brány, a druhou IP adresu je IP adresa převzetí služeb při selhání. Převzetí služeb při selhání adresa je adresa, na kterou jsme může přesunout server, abyste mohli udržovat vysokou dostupnost služeb. Pro tyto oblasti doporučujeme povolit odchozí na IP adresy. Druhou IP adresu je vlastnictví společnosti Microsoft a nepřijímá požadavky na žádné služby, až po aktivaci Azure SQL Database tak, aby přijímal připojení.

| Název oblasti | Primární adresa IP | Sekundární adresa IP |
| --- | --- |--- |
| Austrálie – východ | 191.238.66.109 | 13.75.149.87 |
| Austrálie – jihovýchod | 191.239.192.109 | 13.73.109.251 |
| Brazílie – jih | 104.41.11.5 | |
| Střední Kanada | 40.85.224.249 | |
| Východní Kanada | 40.86.226.166 | |
| Střed USA | 23.99.160.139 | 13.67.215.62 |
| Východní Asie | 191.234.2.139 | 52.175.33.150 |
| Východní USA 1 | 191.238.6.43 | 40.121.158.30 |
| Východní USA 2 | 191.239.224.107 | 40.79.84.180 * |
| Indie – střed | 104.211.96.159  | |
| Indie – jih | 104.211.224.146  | |
| Indie – západ | 104.211.160.80 | |
| Japonsko – východ | 191.237.240.43 | 13.78.61.196 |
| Japonsko – západ | 191.238.68.11 | 104.214.148.156 |
| Korea – střed | 52.231.32.42 | |
| Korea – jih | 52.231.200.86 |  |
| Střed USA – sever | 23.98.55.75 | 23.96.178.199 |
| Severní Evropa | 191.235.193.75 | 40.113.93.91 |
| Střed USA – jih | 23.98.162.75 | 13.66.62.124 |
| Jihovýchodní Asie | 23.100.117.95 | 104.43.15.0 |
| Spojené království – sever | 13.87.97.210 | |
| Spojené království – jih 1 | 51.140.184.11 | |
| Spojené království – jih 2 | 13.87.34.7 | |
| Spojené království – západ | 51.141.8.11  | |
| Západní střed USA | 13.78.145.25 | |
| Západní Evropa | 191.237.232.75 | 40.68.37.158 |
| Západní USA 1 | 23.99.34.75 | 104.42.238.205 |
| Západní USA 2 | 13.66.226.202  | |
||||

\* **Poznámka:** *východní USA 2* má také terciární IP adresu z `52.167.104.0`.

## <a name="change-azure-sql-database-connection-policy"></a>Změnit zásady připojení databáze SQL Azure

Můžete změnit zásady připojení databáze SQL Azure pro server Azure SQL Database [připojení objeví zásad](https://docs.microsoft.com/cli/azure/sql/server/conn-policy) příkaz.

- Pokud vaše zásady připojení je nastavený na **Proxy**, všechny síťové pakety toku prostřednictvím brány Azure SQL Database. U tohoto nastavení musíte povolit odchozí pouze IP brány Azure SQL Database. Pomocí nastavení **Proxy** má více latenci než nastavení **přesměrování**.
- Pokud je nastavení zásady vaší připojení **přesměrování**, všechny síťové pakety toku přímo k proxy serveru middleware. U tohoto nastavení musíte povolit odchozí do víc IP adres.

## <a name="script-to-change-connection-settings-via-powershell"></a>Skript, chcete-li změnit nastavení připojení pomocí prostředí PowerShell

> [!IMPORTANT]
> Tento skript vyžaduje [modul Azure PowerShell](/powershell/azure/install-azurerm-ps).
>

Následující skript prostředí PowerShell ukazuje, jak změnit zásady připojení.

```powershell
Connect-AzureRmAccount
Select-AzureRmSubscription -SubscriptionName <Subscription Name>

# Azure Active Directory ID
$tenantId = "<Azure Active Directory GUID>"
$authUrl = "https://login.microsoftonline.com/$tenantId"

# Subscription ID
$subscriptionId = "<Subscription GUID>"

# Create an App Registration in Azure Active Directory.  Ensure the application type is set to NATIVE
# Under Required Permissions, add the API:  Windows Azure Service Management API

# Specify the redirect URL for the app registration
$uri = "<NATIVE APP - REDIRECT URI>"

# Specify the application id for the app registration
$clientId = "<NATIVE APP - APPLICATION ID>"

# Logical SQL Server Name
$serverName = "<LOGICAL DATABASE SERVER - NAME>"

# Resource Group where the SQL Server is located
$resourceGroupName= "<LOGICAL DATABASE SERVER - RESOURCE GROUP NAME>"


# Login and acquire a bearer token
$AuthContext = [Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationContext]$authUrl
$result = $AuthContext.AcquireToken(
"https://management.core.windows.net/",
$clientId,
[Uri]$uri,
[Microsoft.IdentityModel.Clients.ActiveDirectory.PromptBehavior]::Auto
)

$authHeader = @{
'Content-Type'='application\json; '
'Authorization'=$result.CreateAuthorizationHeader()
}

#Get current connection Policy
Invoke-RestMethod -Uri "https://management.azure.com/subscriptions/$subscriptionId/resourceGroups/$resourceGroupName/providers/Microsoft.Sql/servers/$serverName/connectionPolicies/Default?api-version=2014-04-01-preview" -Method GET -Headers $authHeader

#Set connection policy to Proxy
$connectionType="Proxy" <#Redirect / Default are other options#>
$body = @{properties=@{connectionType=$connectionType}} | ConvertTo-Json

# Apply Changes
Invoke-RestMethod -Uri "https://management.azure.com/subscriptions/$subscriptionId/resourceGroups/$resourceGroupName/providers/Microsoft.Sql/servers/$serverName/connectionPolicies/Default?api-version=2014-04-01-preview" -Method PUT -Headers $authHeader -Body $body -ContentType "application/json"
```

## <a name="script-to-change-connection-settings-via-azure-cli-20"></a>Chcete-li změnit nastavení připojení prostřednictvím Azure CLI 2.0 skriptu

> [!IMPORTANT]
> Tento skript vyžaduje [Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).
>

Následující skript rozhraní příkazového řádku ukazuje, jak změnit zásady připojení.

<pre>
# Get SQL Server ID
sqlserverid=$(az sql server show -n <b>sql-server-name</b> -g <b>sql-server-group</b> --query 'id' -o tsv)

# Set URI
id="$sqlserverid/connectionPolicies/Default"

# Get current connection policy 
az resource show --ids $id

# Update connection policy 
az resource update --ids $id --set properties.connectionType=Proxy

</pre>

## <a name="next-steps"></a>Další postup

- Informace o tom, jak změnit zásady připojení Azure SQL Database pro databázi Azure SQL server najdete v tématu [připojení objeví zásad](https://docs.microsoft.com/cli/azure/sql/server/conn-policy).
- Informace o chování Azure SQL Database připojení pro klienty, kteří používají ADO.NET 4.5 nebo novější verze najdete v tématu [porty nad rámec 1433 pro technologii ADO.NET 4.5](sql-database-develop-direct-route-ports-adonet-v12.md).
- Obecná aplikace vývoj přehled informace najdete v tématu [přehled vývoje aplikace databáze SQL](sql-database-develop-overview.md).
