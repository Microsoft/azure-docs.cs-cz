---
title: Architektura připojení k Azure SQL Database | Dokumentace Microsoftu
description: Tento dokument popisuje architekturu připojení k Azure SQLDB z v rámci Azure nebo z mimo Azure.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: DhruvMsft
ms.author: dhruv
ms.reviewer: carlrab
manager: craigg
ms.date: 01/24/2018
ms.openlocfilehash: 66f558db713ab951864fe694f27f2e60d52e875a
ms.sourcegitcommit: cc4fdd6f0f12b44c244abc7f6bc4b181a2d05302
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2018
ms.locfileid: "47064126"
---
# <a name="azure-sql-database-connectivity-architecture"></a>Architektura připojení k Azure SQL Database 

Tento článek popisuje architekturu připojení k Azure SQL Database a vysvětluje, jak funkce různých komponent pro směrování provozu do vaší instance služby Azure SQL Database. Tyto Azure SQL Database připojení komponenty funkce ke směrování síťového provozu směřujícího do databáze Azure s klientů připojujících se z v rámci Azure a klientů připojujících se z mimo Azure. Tento článek také obsahuje ukázkové skripty, chcete-li změnit, jak dojde k připojení a důležité informace související s Změna výchozího nastavení připojení. 

## <a name="connectivity-architecture"></a>Architektura připojení

Následující diagram představuje podrobný přehled architektury připojení k Azure SQL Database.

![Přehled architektury](./media/sql-database-connectivity-architecture/architecture-overview.png)


Následující kroky popisují, jak navázat připojení ke službě Azure SQL database prostřednictvím Azure SQL Database – pro vyrovnávání zatížení softwaru (SLB) a brány Azure SQL Database.

- Klienti v rámci Azure nebo mimo Azure připojovat k SLB, který má veřejnou IP adresu a naslouchá na portu 1433.
- Nástroj SLB směruje provoz do Azure SQL Database brány.
- Brána přesměruje provoz s middlewarem správný proxy server.
- Proxy middleware přesměruje provoz k příslušné databázi Azure SQL.

> [!IMPORTANT]
> Každá z těchto komponent distribuoval s cílem odepření služeb (DDoS) ochrany integrované v síti a vrstvu aplikace.
>

## <a name="connectivity-from-within-azure"></a>Připojení z v rámci Azure

Pokud se chcete připojit z v rámci Azure, vaše připojení mají zásady připojení **přesměrování** ve výchozím nastavení. Zásady **přesměrování** znamená, že připojení po relaci protokolu TCP do Azure SQL database, relace klienta se pak přesměrují na middlewaru serveru proxy a změní na virtuální IP adresu cílového od Azure Brána SQL Database, middleware proxy serveru. Po tomto datu všechny následné pakety tok přímo prostřednictvím proxy serveru middlewaru, obcházení brány Azure SQL Database. Následující diagram znázorňuje tento tok provozu.

![Přehled architektury](./media/sql-database-connectivity-architecture/connectivity-from-within-azure.png)

## <a name="connectivity-from-outside-of-azure"></a>Připojení z mimo Azure

Pokud se chcete připojit z mimo Azure, vaše připojení mají zásady připojení **Proxy** ve výchozím nastavení. Zásady **Proxy** znamená, že je vytvořena relace TCP přes bránu Azure SQL Database a všechny následné pakety flow přes bránu. Následující diagram znázorňuje tento tok provozu.

![Přehled architektury](./media/sql-database-connectivity-architecture/connectivity-from-outside-azure.png)

> [!IMPORTANT]
> Při používání koncových bodů služby s Azure SQL Database je vaše zásady **Proxy** ve výchozím nastavení. Pokud chcete povolit připojení z uvnitř virtuální sítě, musíte také povolit odchozí připojení k Azure SQL Database brány IP adresy určené v níže uvedeném seznamu. Při použití koncové body služby, důrazně doporučujeme změna zásad připojení k **přesměrování** umožňuje lepší příjem. Pokud změníte připojení zásadu **přesměrování** nebude stačit povolit odchozí na vaše skupiny zabezpečení sítě k bráně Azure SQLDB níže uvedených IP adres, musíte povolit odchozí ke všem IP adresám SQLDB Azure. To lze provést pomocí značky služeb skupiny zabezpečení sítě (skupiny zabezpečení sítě). Další informace najdete v tématu [značky služeb](https://docs.microsoft.com/azure/virtual-network/security-overview#service-tags).

## <a name="azure-sql-database-gateway-ip-addresses"></a>IP adresy brány Azure SQL Database

Pro připojení k databázi Azure SQL z místních prostředků, budete muset povolit odchozí síťový provoz do Azure SQL Database brány pro vaši oblast Azure. Připojení, jdou pouze přes bránu pro připojení v režimu proxy serveru, což je výchozí hodnota při připojování z místních prostředků.

V následující tabulce jsou uvedeny primárních a sekundárních IP adresy brány Azure SQL Database pro všechny datové oblasti. V některých oblastech existují dvě IP adresy. V těchto oblastech primární IP adresa je aktuální IP adresu brány a druhou IP adresu je IP adresa převzetí služeb při selhání. Převzetí služeb při selhání adresa je adresa, do kterého jsme může přesunout vašeho serveru, abyste měli vysokou dostupnost služeb. Pro tyto oblasti doporučujeme vám povolit odchozí IP adresy. Druhá IP adresa není ve vlastnictví společnosti Microsoft a není naslouchání žádné služby, dokud je aktivovaná služba Azure SQL Database tak, aby přijímal připojení.

> [!IMPORTANT]
> Pokud se chcete připojit z v rámci Azure zásad připojení bude **přesměrování** ve výchozím nastavení (s výjimkou případů, pokud použijete koncové body služby). Nebude stačit povolit následující IP adresy. Musíte také povolit všechny Azure SQL Database IP adresy. Pokud se chcete připojit z v rámci virtuální sítě, můžete to provést pomocí značky služeb skupiny zabezpečení sítě (skupiny zabezpečení sítě). Další informace najdete v tématu [značky služeb](https://docs.microsoft.com/azure/virtual-network/security-overview#service-tags).

| Název oblasti | Primární IP adresu | Sekundární adresa IP |
| --- | --- |--- |
| Austrálie – východ | 191.238.66.109 | 13.75.149.87 |
| Austrálie – jihovýchod | 191.239.192.109 | 13.73.109.251 |
| Brazílie – jih | 104.41.11.5 | |
| Kanada – střed | 40.85.224.249 | |
| Kanada – východ | 40.86.226.166 | |
| USA – střed | 23.99.160.139 | 13.67.215.62 |
| Východní Asie | 191.234.2.139 | 52.175.33.150 |
| USA – východ 1 | 191.238.6.43 | 40.121.158.30 |
| Východní USA 2 | 191.239.224.107 | 40.79.84.180 * |
| Indie – střed | 104.211.96.159  | |
| Indie – jih | 104.211.224.146  | |
| Indie – západ | 104.211.160.80 | |
| Japonsko – východ | 191.237.240.43 | 13.78.61.196 |
| Japonsko – západ | 191.238.68.11 | 104.214.148.156 |
| Jižní Korea – střed | 52.231.32.42 | |
| Jižní Korea – jih | 52.231.200.86 |  |
| Střed USA – sever | 23.98.55.75 | 23.96.178.199 |
| Severní Evropa | 191.235.193.75 | 40.113.93.91 |
| Střed USA – jih | 23.98.162.75 | 13.66.62.124 |
| Jihovýchodní Asie | 23.100.117.95 | 104.43.15.0 |
| Velká Británie – sever | 13.87.97.210 | |
| Velká Británie – jih 1 | 51.140.184.11 | |
| Velká Británie – jih 2 | 13.87.34.7 | |
| Spojené království – západ | 51.141.8.11  | |
| Západní střed USA | 13.78.145.25 | |
| Západní Evropa | 191.237.232.75 | 40.68.37.158 |
| USA – západ 1 | 23.99.34.75 | 104.42.238.205 |
| USA – západ 2 | 13.66.226.202  | |
||||

\* **Poznámka:** *USA – východ 2* má také terciární IP adresu z `52.167.104.0`.

## <a name="change-azure-sql-database-connection-policy"></a>Změna zásad připojení Azure SQL Database

Chcete-li změnit zásady připojení Azure SQL Database pro server Azure SQL Database, použijte [conn zásad](https://docs.microsoft.com/cli/azure/sql/server/conn-policy) příkazu.

- Pokud vaše zásady připojení nastavená na **Proxy**, všechny síťové pakety flow přes bránu Azure SQL Database. U tohoto nastavení musíte povolit odchozí pouze IP brány Azure SQL Database. Pomocí nastavení **Proxy** má další latenci než nastavení **přesměrování**.
- Pokud nastavení zásad připojení **přesměrování**, všechny síťové pakety tok přímo k proxy serveru middlewaru. U tohoto nastavení musíte povolit odchozí několik IP adres.

## <a name="script-to-change-connection-settings-via-powershell"></a>Skript, chcete-li změnit nastavení připojení prostřednictvím prostředí PowerShell

> [!IMPORTANT]
> Tento skript vyžaduje [modulu Azure PowerShell](/powershell/azure/install-azurerm-ps).
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

## <a name="script-to-change-connection-settings-via-azure-cli"></a>Skript, chcete-li změnit nastavení připojení prostřednictvím rozhraní příkazového řádku Azure

> [!IMPORTANT]
> Tento skript vyžaduje [rozhraní příkazového řádku Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).
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

- Informace o tom, jak změnit zásady připojení Azure SQL Database pro server Azure SQL Database najdete v tématu [conn zásad](https://docs.microsoft.com/cli/azure/sql/server/conn-policy).
- Informace o chování připojení Azure SQL Database pro klienty, kteří používají technologii ADO.NET 4.5 nebo novější verze, najdete v části [porty nad 1433 pro technologii ADO.NET 4.5](sql-database-develop-direct-route-ports-adonet-v12.md).
- Obecná aplikace vývoj přehled informace najdete v tématu [přehled vývoje databázových aplikací SQL Database](sql-database-develop-overview.md).
