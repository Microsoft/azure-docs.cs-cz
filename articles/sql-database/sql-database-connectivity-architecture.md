---
title: Azure SQL Database a SQL Data Warehouse připojení architektury | Dokumentace Microsoftu
description: Tento dokument popisuje architekturu připojení k Azure SQL pro připojení k databázi z Azure nebo z mimo Azure.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: rohitnayakmsft
ms.author: rohitna
ms.reviewer: carlrab, vanto
manager: craigg
ms.date: 07/02/2019
ms.openlocfilehash: 8441e64981b7157e91a56124a08c0aa02a9b1db0
ms.sourcegitcommit: 084630bb22ae4cf037794923a1ef602d84831c57
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/03/2019
ms.locfileid: "67537929"
---
# <a name="azure-sql-connectivity-architecture"></a>Architektura připojení k Azure SQL

Azure SQL Database a SQL Data Warehouse připojení k architektuře a tento článek vysvětluje, jak různé součásti fungovat směrovat přenos dat k vaší instanci Azure SQL. Tyto funkce součástí připojení ke směrování síťového provozu do Azure SQL Database nebo SQL Data Warehouse s klientů připojujících se z v rámci Azure a klientů připojujících se z mimo Azure. Tento článek také obsahuje ukázkové skripty, chcete-li změnit, jak dojde k připojení a důležité informace související s Změna výchozího nastavení připojení.

## <a name="connectivity-architecture"></a>Architektura připojení

Následující diagram představuje podrobný přehled architektury připojení k Azure SQL Database.

![Přehled architektury](./media/sql-database-connectivity-architecture/connectivity-overview.png)

Následující kroky popisují, jak navázat připojení ke službě Azure SQL database:

- Klienti připojení k bráně, který má veřejnou IP adresu a naslouchá na portu 1433.
- Brána, v závislosti na zásady platné připojení, přesměrování nebo proxy serverech provoz do clusteru správnou databázi.
- Uvnitř databáze clusteru přenosy předávaly k příslušné databázi Azure SQL.

## <a name="connection-policy"></a>Zásady připojení

Azure SQL Database podporuje tři následující možnosti pro nastavení zásad připojení databáze SQL serveru:

- **Přesměrování (doporučeno):** Klienti připojení přímo k uzlu, který je hostitelem databáze. Pokud chcete povolit připojení, klienti musí umožňovat odchozí pravidla brány firewall na všech IP adres Azure v oblasti pomocí skupin zabezpečení sítě (NSG) s [značky služeb](../virtual-network/security-overview.md#service-tags)) pro porty 11000 11999, ne jenom IP brány Azure SQL Database adresy na portu 1433. Vzhledem k tomu, že pakety přejít přímo k databázi, mají latenci a propustnost vyšší výkon.
- **Proxy server:** V tomto režimu se všechna připojení jsou směrovány přes proxy server prostřednictvím bran Azure SQL Database. Pokud chcete povolit připojení, klient musí mít odchozí pravidla brány firewall, které umožňují jenom brána Azure SQL Database IP adresy (obvykle dvě IP adresy v jedné oblasti). Výběr v tomto režimu může vést k vyšší latence a propustnosti nižší, v závislosti na povaze zatížení. Důrazně doporučujeme `Redirect` zásady připojení přes `Proxy` zásady připojení pro nejnižší latenci a propustnost nejvyšší.
- **Výchozí hodnota:** To platí zásady připojení ve všech serverech po vytvoření není-li explicitně změnit zásady připojení buď `Proxy` nebo `Redirect`. Platné zásady závisí na tom, jestli se připojení pocházejí z v rámci Azure (`Redirect`) nebo mimo systém Azure (`Proxy`).

## <a name="connectivity-from-within-azure"></a>Připojení z v rámci Azure

Pokud se chcete připojit z v rámci Azure připojení mají zásady připojení `Redirect` ve výchozím nastavení. Zásady `Redirect` znamená, že po vytvoření relace TCP ke službě Azure SQL database relace klienta je pak přesměrují na správnou databázi clusteru a z té brány Azure SQL Database, která z se změní na virtuální IP adresu cílového cluster. Po tomto datu všechny následné pakety flow přímo do clusteru, obcházení brány Azure SQL Database. Následující diagram znázorňuje tento tok provozu.

![Přehled architektury](./media/sql-database-connectivity-architecture/connectivity-azure.png)

## <a name="connectivity-from-outside-of-azure"></a>Připojení z mimo Azure

Pokud se chcete připojit z mimo Azure, vaše připojení mají zásady připojení `Proxy` ve výchozím nastavení. Zásady `Proxy` znamená, že je vytvořena relace TCP přes bránu Azure SQL Database a všechny následné pakety flow přes bránu. Následující diagram znázorňuje tento tok provozu.

![Přehled architektury](./media/sql-database-connectivity-architecture/connectivity-onprem.png)

## <a name="azure-sql-database-gateway-ip-addresses"></a>IP adresy brány Azure SQL Database

Následující tabulka uvádí IP adresy brány podle oblasti. Pro připojení ke službě Azure SQL Database, musíte povolit síťový provoz do a z **všechny** brány pro danou oblast.

Od této chvíle, přidáme další brány v jednotlivých oblastech a vyřadit z provozu brány v vyřadit z provozu brány IP adresu ve sloupci v tabulce níže. Další podrobnosti o vyřazení procesu zadaný v následujícím článku: [Provoz migrace do Azure SQL Database k novější brány](sql-database-gateway-migration.md)


| Název oblasti          | IP adresa brány | Vyřazená z provozu brány </br> IP adresa| Poznámky k vyřazení z provozu | 
| --- | --- | --- | --- |
| Austrálie – východ       | 13.75.149.87, 40.79.161.1 | | |
| Austrálie – jihovýchod | 191.239.192.109, 13.73.109.251 | | |
| Brazílie – jih         | 104.41.11.5        |                 | |
| Kanada – střed       | 40.85.224.249      |                 | |
| Kanada – východ          | 40.86.226.166      |                 | |
| USA – střed           | 13.67.215.62, 52.182.137.15 | 23.99.160.139 | Žádná připojení po 1. září 2019 |
| Východní Čína 1         | 139.219.130.35     |                 | |
| Čína – východ 2         | 40.73.82.1         |                 | |
| Čína – sever 1        | 139.219.15.17      |                 | |
| Čína – sever 2        | 40.73.50.0         |                 | |
| Východní Asie            | 191.234.2.139, 52.175.33.150 |       | |
| USA – východ 1            | 40.121.158.30, 40.79.153.12 | 191.238.6.43 | Žádná připojení po 1. září 2019 |
| Východní USA 2            | 40.79.84.180, 52.177.185.181, 52.167.104.0 | 191.239.224.107    | Žádná připojení po 1. září 2019 |
| Francie – střed       | 40.79.137.0, 40.79.129.1 |           | |
| Německo – střed      | 51.4.144.100       |                 | |
| Severovýchodní Německo   | 51.5.144.179       |                 | |
| Indie – střed        | 104.211.96.159     |                 | |
| Indie – jih          | 104.211.224.146    |                 | |
| Indie – západ           | 104.211.160.80     |                 | |
| Japonsko – východ           | 13.78.61.196, 40.79.184.8, 13.78.106.224 | 191.237.240.43 | Žádná připojení po 1. září 2019 |
| Japonsko – západ           | 104.214.148.156, 40.74.100.192 | 191.238.68.11 | Žádná připojení po 1. září 2019 |
| Korea – střed        | 52.231.32.42       |                 | |
| Jižní Korea – jih          | 52.231.200.86      |                 | |
| Středoseverní USA     | 23.96.178.199      | 23.98.55.75     | Žádná připojení po 1. září 2019 |
| Severní Evropa         | 40.113.93.91       | 191.235.193.75  | Žádná připojení po 1. září 2019 |
| Středojižní USA     | 13.66.62.124       | 23.98.162.75    | Žádná připojení po 1. září 2019 |
| Jihovýchodní Asie      | 104.43.15.0        | 23.100.117.95   | Žádná připojení po 1. září 2019 |
| Velká Británie – jih             | 51.140.184.11      |                 | |
| Spojené království – západ              | 51.141.8.11        |                 | |
| Západní střed USA      | 13.78.145.25       |                 | |
| Západní Evropa          | 191.237.232.75, 40.68.37.158 |       | |
| USA – západ 1            | 23.99.34.75, 104.42.238.205 |        | |
| Západní USA 2            | 13.66.226.202      |                 | |
|                      |                    |                 | |

## <a name="change-azure-sql-database-connection-policy"></a>Změna zásad připojení Azure SQL Database

Chcete-li změnit zásady připojení Azure SQL Database pro server Azure SQL Database, použijte [conn zásad](https://docs.microsoft.com/cli/azure/sql/server/conn-policy) příkazu.

- Pokud vaše zásady připojení nastavená na `Proxy`, všechny síťové pakety flow přes bránu Azure SQL Database. U tohoto nastavení musíte povolit odchozí pouze IP brány Azure SQL Database. Pomocí nastavení `Proxy` má další latenci než nastavení `Redirect`.
- Pokud nastavení zásad připojení `Redirect`, všechny síťové pakety flow přímo do databáze clusteru. U tohoto nastavení musíte povolit odchozí několik IP adres.

## <a name="script-to-change-connection-settings-via-powershell"></a>Skript, chcete-li změnit nastavení připojení prostřednictvím prostředí PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> Modul Azure PowerShell – Resource Manager je stále podporuje Azure SQL Database, ale všechny budoucí vývoj je Az.Sql modulu. Tyto rutiny najdete v části [AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Argumenty pro příkazy v modulu Az a moduly AzureRm podstatně totožné. Tento skript vyžaduje [modulu Azure PowerShell](/powershell/azure/install-az-ps).

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

## <a name="script-to-change-connection-settings-via-azure-cli"></a>Skript, chcete-li změnit nastavení připojení prostřednictvím rozhraní příkazového řádku Azure

> [!IMPORTANT]
> Tento skript vyžaduje [rozhraní příkazového řádku Azure](https://docs.microsoft.com/cli/azure/install-azure-cli).

### <a name="azure-cli-in-a-bash-shell"></a>Azure CLI v prostředí bash

> [!IMPORTANT]
> Tento skript vyžaduje [rozhraní příkazového řádku Azure](https://docs.microsoft.com/cli/azure/install-azure-cli).

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

### <a name="azure-cli-from-a-windows-command-prompt"></a>Rozhraní příkazového řádku Azure z příkazového řádku Windows

> [!IMPORTANT]
> Tento skript vyžaduje [rozhraní příkazového řádku Azure](https://docs.microsoft.com/cli/azure/install-azure-cli).

Následující skript rozhraní příkazového řádku ukazuje, jak změnit zásady připojení z příkazového řádku Windows (pomocí Azure CLI nainstalované).

```azurecli
# Get SQL Server ID and set URI
FOR /F "tokens=*" %g IN ('az sql server show --resource-group myResourceGroup-571418053 --name server-538465606 --query "id" -o tsv') do (SET sqlserverid=%g/connectionPolicies/Default)

# Get current connection policy
az resource show --ids %sqlserverid%

# Update connection policy
az resource update --ids %sqlserverid% --set properties.connectionType=Proxy
```

## <a name="next-steps"></a>Další postup

- Informace o tom, jak změnit zásady připojení Azure SQL Database pro server Azure SQL Database najdete v tématu [conn zásad](https://docs.microsoft.com/cli/azure/sql/server/conn-policy).
- Informace o chování připojení Azure SQL Database pro klienty, kteří používají technologii ADO.NET 4.5 nebo novější verze, najdete v části [porty nad 1433 pro technologii ADO.NET 4.5](sql-database-develop-direct-route-ports-adonet-v12.md).
- Obecná aplikace vývoj přehled informace najdete v tématu [přehled vývoje databázových aplikací SQL Database](sql-database-develop-overview.md).