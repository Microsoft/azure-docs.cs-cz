---
title: Architektura připojení
description: Tento dokument popisuje architekturu připojení Azure SQL pro databázová připojení z Azure nebo mimo Azure.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: fasttrack-edit
titleSuffix: Azure SQL Database and SQL Data Warehouse
ms.devlang: ''
ms.topic: conceptual
author: rohitnayakmsft
ms.author: rohitna
ms.reviewer: carlrab, vanto
ms.date: 07/02/2019
ms.openlocfilehash: 0ac9247f5156eb1b766aec7403b2dc8473114659
ms.sourcegitcommit: 8cf199fbb3d7f36478a54700740eb2e9edb823e8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/25/2019
ms.locfileid: "74483717"
---
# <a name="azure-sql-connectivity-architecture"></a>Architektura připojení Azure SQL

Tento článek popisuje architekturu připojení Azure SQL Database a SQL Data Warehouse a také způsob, jakým funkce různé komponenty směrují provoz do vaší instance Azure SQL. Tyto součásti pro připojení umožňují směrovat síťový provoz do Azure SQL Database nebo SQL Data Warehouse pomocí klientů připojujících se z Azure a s klienty, kteří se připojují mimo Azure. Tento článek také poskytuje ukázky skriptů pro změnu způsobu, jakým dojde k připojení, a aspektů, které se týkají změny výchozího nastavení připojení.

## <a name="connectivity-architecture"></a>Architektura připojení

Následující diagram poskytuje podrobný přehled architektury Azure SQL Database pro připojení.

![Přehled architektury](./media/sql-database-connectivity-architecture/connectivity-overview.png)

Následující kroky popisují, jak se naváže připojení ke službě Azure SQL Database:

- Klienti se připojují k bráně, která má veřejnou IP adresu a naslouchá na portu 1433.
- Brána v závislosti na efektivních zásadách připojení přesměruje nebo proxy data do správného databázového clusteru.
- V rámci provozu databázového clusteru se předají do příslušné databáze SQL Azure.

## <a name="connection-policy"></a>Zásady připojení

Azure SQL Database podporuje pro nastavení zásad připojení serveru SQL Database následující tři možnosti:

- **Přesměrování (doporučeno):** Klienti navázali připojení přímo k uzlu, který je hostitelem databáze, což vede k nižší latenci a vylepšení v celém. Aby připojení používala klienti tohoto režimu, musí
   - Povolí příchozí a odchozí komunikaci z klienta se všemi IP adresami Azure v oblasti na portech v rozsahu 11000 11999.  
   - Povolí příchozí a odchozí komunikaci od klienta až po Azure SQL Database IP adresy brány na portu 1433.

- **Proxy server:** V tomto režimu jsou všechna připojení proxy přes Azure SQL Database bran, což vede k zvýšené latenci a zkrácení v celém. Aby připojení používala tento režim, klienti musí povolit příchozí a odchozí komunikaci od klienta až po Azure SQL Database IP adresy brány na portu 1433.

- **Výchozí:** Toto je zásada připojení platná pro všechny servery po vytvoření, pokud zásadu připojení explicitně neupravíte na buď `Proxy`, nebo `Redirect`. Výchozí zásady jsou`Redirect` pro všechna připojení klientů pocházející z Azure (např. z virtuálního počítače Azure) a `Proxy`pro všechna připojení klientů, která pocházejí uvnitř (např. připojení z místní pracovní stanice).

 Pro nejnižší latenci a nejvyšší propustnost doporučujeme, abyste zásady připojení `Redirect` k zásadám připojení `Proxy`. Budete ale muset splnit další požadavky na povolení síťového provozu, jak je uvedeno výše. Pokud se jedná o virtuální počítač Azure, můžete to udělat pomocí skupin zabezpečení sítě (NSG) s [visačkami služby](../virtual-network/security-overview.md#service-tags). Pokud se klient připojuje z místní pracovní stanice, možná budete muset spolupracovat se správcem sítě a zapnout síťový provoz přes bránu firewall vaší firmy.

## <a name="connectivity-from-within-azure"></a>Připojení v rámci Azure

Pokud se připojujete z Azure, mají připojení `Redirect` ve výchozím nastavení zásady připojení. Zásada `Redirect` znamená, že po navázání relace TCP na databázi SQL Azure se relace klienta přesměruje do správného databázového clusteru se změnou cílové virtuální IP adresy z této Azure SQL Database brány na služby. Následně se všechny následné pakety nasměrují přímo do clusteru a vycházejí z Azure SQL Database brány. Tento tok přenosů znázorňuje následující diagram.

![Přehled architektury](./media/sql-database-connectivity-architecture/connectivity-azure.png)

## <a name="connectivity-from-outside-of-azure"></a>Konektivita mimo Azure

Pokud se připojujete z oblasti mimo Azure, vaše připojení mají ve výchozím nastavení zásady připojení `Proxy`. Zásada `Proxy` znamená, že relace TCP je vytvořena prostřednictvím brány Azure SQL Database a všech následných paketů toku prostřednictvím brány. Tento tok přenosů znázorňuje následující diagram.

![Přehled architektury](./media/sql-database-connectivity-architecture/connectivity-onprem.png)

> [!IMPORTANT]
> Také otevřete porty 14000-14999, abyste umožnili [připojení pomocí DAC](https://docs.microsoft.com/sql/database-engine/configure-windows/diagnostic-connection-for-database-administrators?view=sql-server-2017#connecting-with-dac) .


## <a name="azure-sql-database-gateway-ip-addresses"></a>IP adresy brány Azure SQL Database

V následující tabulce jsou uvedené IP adresy bran podle oblasti. Pokud se chcete připojit k Azure SQL Database, je potřeba, abyste povolili & síťového provozu ze **všech** bran pro danou oblast.

Podrobnosti o tom, jak se bude provoz migrovat na nové brány v konkrétních oblastech, najdete v následujícím článku: [Azure SQL Database migrace provozu na novější brány](sql-database-gateway-migration.md)


| Název oblasti          | IP adresy brány |
| --- | --- |
| Austrálie – střed    | 20.36.105.0 |
| Central2 Austrálie   | 20.36.113.0 |
| Austrálie – východ       | 13.75.149.87, 40.79.161.1 |
| Austrálie – jihovýchod | 191.239.192.109, 13.73.109.251 |
| Brazílie – jih         | 104.41.11.5, 191.233.200.14 |
| Střední Kanada       | 40.85.224.249      |
| Východní Kanada          | 40.86.226.166      |
| Střední USA           | 13.67.215.62, 52.182.137.15, 23.99.160.139, 104.208.16.96, 104.208.21.1 | 
| Čína – východ           | 139.219.130.35     |
| Čína – východ 2         | 40.73.82.1         |
| Čína – sever          | 139.219.15.17      |
| Čína – sever 2        | 40.73.50.0         |
| Východní Asie            | 191.234.2.139, 52.175.33.150, 13.75.32.4 |
| Východní USA              | 40.121.158.30, 40.79.153.12, 191.238.6.43, 40.78.225.32 |
| Východ USA 2            | 40.79.84.180, 52.177.185.181, 52.167.104.0, 191.239.224.107, 104.208.150.3 | 
| Francie – střed       | 40.79.137.0, 40.79.129.1 |
| Německo – střed      | 51.4.144.100       |
| Německo – sever východ   | 51.5.144.179       |
| Indie – střed        | 104.211.96.159     |
| Indie – jih          | 104.211.224.146    |
| Indie – západ           | 104.211.160.80     |
| Japonsko – východ           | 13.78.61.196, 40.79.184.8, 13.78.106.224, 191.237.240.43, 40.79.192.5 | 
| Japonsko – západ           | 104.214.148.156, 40.74.100.192, 191.238.68.11, 40.74.97.10 | 
| Jižní Korea – střed        | 52.231.32.42       |
| Jižní Korea – jih          | 52.231.200.86      |
| Středoseverní USA     | 23.96.178.199, 23.98.55.75, 52.162.104.33 |
| Severní Evropa         | 40.113.93.91, 191.235.193.75, 52.138.224.1 | 
| Jižní Afrika – sever   | 102.133.152.0      |
| Jižní Afrika – západ    | 102.133.24.0       |
| Středojižní USA     | 13.66.62.124, 23.98.162.75, 104.214.16.32   | 
| Jihovýchodní Asie      | 104.43.15.0, 23.100.117.95, 40.78.232.3   | 
| Spojené arabské emiráty – střed          | 20.37.72.64        |
| Spojené arabské emiráty – sever            | 65.52.248.0        |
| Spojené království – jih             | 51.140.184.11      |
| Spojené království – západ              | 51.141.8.11        |
| Středozápadní USA      | 13.78.145.25       |
| Západní Evropa          | 40.68.37.158, 191.237.232.75, 104.40.168.105  |
| Západní USA              | 104.42.238.205, 23.99.34.75, 13.86.216.196   |
| Západní USA 2            | 13.66.226.202      |
|                      |                    |

## <a name="change-azure-sql-database-connection-policy"></a>Změna zásad Azure SQL Databaseho připojení

Chcete-li změnit zásady Azure SQL Database připojení pro Azure SQL Database Server, použijte příkaz Connection [-Policy](https://docs.microsoft.com/cli/azure/sql/server/conn-policy) .

- Pokud je vaše zásada připojení nastavená na `Proxy`, všechny síťové pakety se procházejí přes bránu Azure SQL Database. Pro toto nastavení je potřeba, abyste povolili odchozí jenom na IP adresu Azure SQL Database brány. Použití nastavení `Proxy` má větší latenci než nastavení `Redirect`.
- Pokud je vaše zásada připojení nastavená `Redirect`, všechny síťové pakety se budou nacházet přímo do databázového clusteru. Pro toto nastavení je potřeba, abyste povolili odchozí i víc IP adres.

## <a name="script-to-change-connection-settings-via-powershell"></a>Skript pro změnu nastavení připojení prostřednictvím PowerShellu

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> Modul PowerShell Azure Resource Manager je stále podporován Azure SQL Database, ale všechny budoucí vývojové prostředí jsou pro modul AZ. SQL. Tyto rutiny naleznete v tématu [AzureRM. SQL](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Argumenty pro příkazy v modulech AZ a v modulech AzureRm jsou v podstatě identické. Následující skript vyžaduje [modul Azure PowerShell](/powershell/azure/install-az-ps).

Následující skript prostředí PowerShell ukazuje, jak změnit zásady připojení.

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

## <a name="script-to-change-connection-settings-via-azure-cli"></a>Skript pro změnu nastavení připojení přes rozhraní příkazového řádku Azure

> [!IMPORTANT]
> Tento skript vyžaduje rozhraní příkazového [řádku Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli).

### <a name="azure-cli-in-a-bash-shell"></a>Rozhraní příkazového řádku Azure v prostředí bash

> [!IMPORTANT]
> Tento skript vyžaduje rozhraní příkazového [řádku Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli).

Následující skript rozhraní příkazového řádku ukazuje, jak změnit zásady připojení v prostředí bash.

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

> [!IMPORTANT]
> Tento skript vyžaduje rozhraní příkazového [řádku Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli).

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

- Informace o tom, jak změnit zásady Azure SQL Databaseho připojení pro Azure SQL Database Server, najdete v tématu Connection [-Policy](https://docs.microsoft.com/cli/azure/sql/server/conn-policy).
- Informace o chování připojení Azure SQL Database pro klienty, kteří používají verzi ADO.NET 4,5 nebo novější, najdete v části [porty kromě 1433 pro ADO.NET 4,5](sql-database-develop-direct-route-ports-adonet-v12.md).
- Obecné informace o vývoji aplikací naleznete v tématu [SQL Database Application Development Overview](sql-database-develop-overview.md).
