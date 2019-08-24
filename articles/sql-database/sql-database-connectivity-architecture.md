---
title: Architektura připojení Azure SQL Database a SQL Data Warehouse | Microsoft Docs
description: Tento dokument popisuje architekturu připojení Azure SQL pro databázová připojení z Azure nebo mimo Azure.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: rohitnayakmsft
ms.author: rohitna
ms.reviewer: carlrab, vanto
ms.date: 07/02/2019
ms.openlocfilehash: 0e9bdb22baed74ef948f369f9259784900486860
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/26/2019
ms.locfileid: "68569082"
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

- **Přesměrování (doporučeno):** Klienti navážou připojení přímo k uzlu, který je hostitelem databáze. Aby bylo možné připojení povolit, musí klienti povolit odchozí pravidla brány firewall ke všem IP adresám Azure v oblasti pomocí skupin zabezpečení sítě (NSG) a označením [služby](../virtual-network/security-overview.md#service-tags)pro porty 11000-11999, nikoli jenom IP adresy Azure SQL Database brány na portu 1433. Vzhledem k tomu, že pakety přecházejí přímo do databáze, latence a propustnost mají vyšší výkon.
- **Soubory** V tomto režimu jsou všechna připojení proxy prostřednictvím bran Azure SQL Database. Aby bylo možné připojení povolit, musí mít klient odchozí pravidla brány firewall, která povolují pouze IP adresy brány Azure SQL Database (obvykle dvě IP adresy v každé oblasti). Výběr tohoto režimu může mít za následek vyšší latenci a nižší propustnost, a to v závislosti na povaze úlohy. Pro nejnižší latenci `Redirect` a nejvyšší propustnost důrazně doporučujeme zásady připojení `Proxy` oproti zásadám připojení.
- **Výchozí** Toto je zásada připojení platná pro všechny servery po vytvoření, pokud zásadu připojení explicitně neupravíte na buď `Proxy` nebo. `Redirect` Efektivní zásady závisí na tom, jestli připojení pocházejí z Azure (`Redirect`) nebo mimo Azure (`Proxy`).

## <a name="connectivity-from-within-azure"></a>Připojení v rámci Azure

Pokud se připojujete z v rámci Azure, mají připojení `Redirect` ve výchozím nastavení zásady připojení. Zásada `Redirect` znamená, že po navázání relace TCP na databázi SQL Azure se relace klienta přesměruje do správného databázového clusteru se změnou cílové virtuální IP adresy z této Azure SQL Database brány na služby. Následně se všechny následné pakety nasměrují přímo do clusteru a vycházejí z Azure SQL Database brány. Tento tok přenosů znázorňuje následující diagram.

![Přehled architektury](./media/sql-database-connectivity-architecture/connectivity-azure.png)

## <a name="connectivity-from-outside-of-azure"></a>Konektivita mimo Azure

Pokud se připojujete z oblasti mimo Azure, připojení mají `Proxy` ve výchozím nastavení zásady připojení. Zásada `Proxy` znamená, že je relace TCP vytvořená prostřednictvím brány Azure SQL Database a všech následných paketů toku prostřednictvím brány. Tento tok přenosů znázorňuje následující diagram.

![Přehled architektury](./media/sql-database-connectivity-architecture/connectivity-onprem.png)

## <a name="azure-sql-database-gateway-ip-addresses"></a>IP adresy brány Azure SQL Database

V následující tabulce jsou uvedené IP adresy bran podle oblasti. Pokud se chcete připojit k Azure SQL Database, je potřeba, abyste povolili & síťového provozu ze **všech** bran pro danou oblast.

Když budete pokračovat, přidáme do každé oblasti další brány a vyřadíme brány ve sloupci IP adresa vyřazené brány v následující tabulce. Další podrobnosti o procesu vyřazení z provozu uvedeného v následujícím článku: [Azure SQL Database migrace provozu na novější brány](sql-database-gateway-migration.md)


| Název oblasti          | IP adresa brány | Vyřazení brány z provozu </br> IP adresa| Poznámky k vyřazení z provozu | 
| --- | --- | --- | --- |
| Austrálie – střed    | 20.36.105.0 | | |
| Central2 Austrálie   | 20.36.113.0 | | |
| Austrálie – východ       | 13.75.149.87, 40.79.161.1 | | |
| Austrálie – jihovýchod | 191.239.192.109, 13.73.109.251 | | |
| Brazílie – jih         | 104.41.11.5        |                 | |
| Kanada – střed       | 40.85.224.249      |                 | |
| Kanada – východ          | 40.86.226.166      |                 | |
| Střední USA           | 13.67.215.62, 52.182.137.15 | 23.99.160.139 | Žádná připojení po 1. září 2019 |
| Čína – východ           | 139.219.130.35     |                 | |
| Čína – východ 2         | 40.73.82.1         |                 | |
| Čína – sever          | 139.219.15.17      |                 | |
| Čína – sever 2        | 40.73.50.0         |                 | |
| Východní Asie            | 191.234.2.139, 52.175.33.150 |       | |
| East US              | 40.121.158.30, 40.79.153.12 | 191.238.6.43 | Žádná připojení po 1. září 2019 |
| Východní USA 2            | 40.79.84.180, 52.177.185.181, 52.167.104.0 | 191.239.224.107    | Žádná připojení po 1. září 2019 |
| Francie – střed       | 40.79.137.0, 40.79.129.1 |           | |
| Střední Německo      | 51.4.144.100       |                 | |
| Německo – sever východ   | 51.5.144.179       |                 | |
| Indie – střed        | 104.211.96.159     |                 | |
| Indie – jih          | 104.211.224.146    |                 | |
| Indie – západ           | 104.211.160.80     |                 | |
| Japonsko – východ           | 13.78.61.196, 40.79.184.8, 13.78.106.224 | 191.237.240.43 | Žádná připojení po 1. září 2019 |
| Japonsko – západ           | 104.214.148.156, 40.74.100.192 | 191.238.68.11 | Žádná připojení po 1. září 2019 |
| Jižní Korea – střed        | 52.231.32.42       |                 | |
| Jižní Korea – jih          | 52.231.200.86      |                 | |
| Středoseverní USA     | 23.96.178.199      | 23.98.55.75     | Žádná připojení po 1. září 2019 |
| Severní Evropa         | 40.113.93.91       | 191.235.193.75  | Žádná připojení po 1. září 2019 |
| Jižní Afrika – sever   | 102.133.152.0      |                 | |
| Jižní Afrika – západ    | 102.133.24.0       |                 | |
| Středojižní USA     | 13.66.62.124       | 23.98.162.75    | Žádná připojení po 1. září 2019 |
| Jihovýchodní Asie      | 104.43.15.0        | 23.100.117.95   | Žádná připojení po 1. září 2019 |
| Spojené arabské emiráty – střed          | 20.37.72.64        |                 | |
| Spojené arabské emiráty – sever            | 65.52.248.0        |                 | |
| Velká Británie – jih             | 51.140.184.11      |                 | |
| Spojené království – západ              | 51.141.8.11        |                 | |
| Středozápadní USA      | 13.78.145.25       |                 | |
| Západní Evropa          | 40.68.37.158       | 191.237.232.75  | Žádná připojení po 1. září 2019 |
| USA – západ              | 104.42.238.205     | 23.99.34.75     | Žádná připojení po 1. září 2019 |
| USA – západ 2            | 13.66.226.202      |                 | |
|                      |                    |                 | |

## <a name="change-azure-sql-database-connection-policy"></a>Změna zásad Azure SQL Databaseho připojení

Chcete-li změnit zásady Azure SQL Database připojení pro Azure SQL Database Server, použijte příkaz Connection [-Policy](https://docs.microsoft.com/cli/azure/sql/server/conn-policy) .

- Pokud je vaše zásada připojení nastavená `Proxy`na, zaznamená všechny síťové pakety tok přes bránu Azure SQL Database. Pro toto nastavení je potřeba, abyste povolili odchozí jenom na IP adresu Azure SQL Database brány. Použití nastavení `Proxy` má větší latenci než `Redirect`nastavení.
- Pokud je `Redirect`vaše zásada připojení nastavená, všechny síťové pakety se nasměrují přímo do databázového clusteru. Pro toto nastavení je potřeba, abyste povolili odchozí i víc IP adres.

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

## <a name="next-steps"></a>Další postup

- Informace o tom, jak změnit zásady Azure SQL Databaseho připojení pro Azure SQL Database Server, najdete v tématu Connection [-Policy](https://docs.microsoft.com/cli/azure/sql/server/conn-policy).
- Informace o chování připojení Azure SQL Database pro klienty, kteří používají verzi ADO.NET 4,5 nebo novější, najdete v části [porty kromě 1433 pro ADO.NET 4,5](sql-database-develop-direct-route-ports-adonet-v12.md).
- Obecné informace o vývoji aplikací naleznete v tématu [SQL Database Application Development Overview](sql-database-develop-overview.md).
