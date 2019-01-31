---
title: Směruje Azure provoz do Azure SQL Database a SQL Data Warehouse | Dokumentace Microsoftu
description: Tento dokument popisuje, Azure SQL Database a SQL Data Warehouse připojení architektury z v Azure nebo mimo Azure.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: carlrab
manager: craigg
ms.date: 01/25/2019
ms.openlocfilehash: de31ab4e617b872239c1b83324e5b8d52b0b4094
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/31/2019
ms.locfileid: "55469105"
---
# <a name="azure-sql-connectivity-architecture"></a>Architektura připojení k Azure SQL

Azure SQL Database a SQL Data Warehouse připojení k architektuře a tento článek vysvětluje, jak různé součásti fungovat směrovat přenos dat k vaší instanci Azure SQL. Tyto funkce součástí připojení ke směrování síťového provozu do Azure SQL Database nebo SQL Data Warehouse s klientů připojujících se z v rámci Azure a klientů připojujících se z mimo Azure. Tento článek také obsahuje ukázkové skripty, chcete-li změnit, jak dojde k připojení a důležité informace související s Změna výchozího nastavení připojení.

> [!IMPORTANT]
> **[Nadcházející změny] Pro koncový bod připojení služby pro servery Azure SQL `Default` chování připojení se změní na `Redirect`.**
>
> Změny se promítnou pro všechny oblasti nebo před 2. ledna 2019.
>
> Zabránit možnosti připojení prostřednictvím koncového bodu služby rozdělení v existujících prostředích v důsledku této změny, použijeme telemetrie postupujte takto:
> - U serverů, které byly přístupné prostřednictvím koncových bodů služby před provedením změny, které zjistíme, můžeme Přepnout typ připojení na `Proxy`.
> - Pro všechny ostatní servery, můžeme Přepnout připojení typu bude přepínat na `Redirect`.
>
> Uživatelé koncový bod služby může být stále postižená v následujících scénářích:
> - Aplikace se připojí k existující server zřídka, naše telemetrie nebyla zaznamenat informace o těchto aplikací
> - Automatické nasazení logic vytvoří server služby SQL Database za předpokladu, že je výchozí chování pro koncový bod připojení služby `Proxy`
>
> Pokud nelze navázat koncový bod připojení služby k serveru Azure SQL a jsou podezření, že se vás tato změna, ověřte prosím, že typ připojení je explicitně nastaveno `Redirect`. Pokud je to tento případ, budete muset otevřít pravidla brány firewall virtuálního počítače a skupiny zabezpečení sítě (NSG) Azure IP adres v oblasti, které patří do Sql [značka služby](../virtual-network/security-overview.md#service-tags) pro porty 11000 12000. Pokud to není pro vás, přepněte server explicitně na `Proxy`.
> [!NOTE]
> Toto téma se týká k Azure SQL serveru a databází SQL Database a SQL Data Warehouse, které jsou vytvořené na serveru Azure SQL. Pro zjednodušení se SQL Database používá k označení SQL Database i SQL Data Warehouse.

## <a name="connectivity-architecture"></a>Architektura připojení

Následující diagram představuje podrobný přehled architektury připojení k Azure SQL Database.

![Přehled architektury](./media/sql-database-connectivity-architecture/connectivity-overview.png)

Následující kroky popisují, jak navázat připojení ke službě Azure SQL database:

- Klienti připojení k bráně, který má veřejnou IP adresu a naslouchá na portu 1433.
- Brána, v závislosti na zásady platné připojení, přesměrování nebo proxy serverech provoz do clusteru správnou databázi.
- Uvnitř databáze clusteru přenosy předávaly k příslušné databázi Azure SQL.

## <a name="connection-policy"></a>Zásady připojení

Azure SQL Database podporuje tři následující možnosti pro nastavení zásad připojení databáze SQL serveru:

- **Přesměrování (doporučeno):** Klienti připojení přímo k uzlu, který je hostitelem databáze. Pokud chcete povolit připojení, klienti musí umožňovat odchozí pravidla brány firewall na všech IP adres Azure v oblasti pomocí skupin zabezpečení sítě (NSG) s [značky služeb](../virtual-network/security-overview.md#service-tags)) pro porty 11000 12000, ne jenom IP brány Azure SQL Database adresy na portu 1433. Vzhledem k tomu, že pakety přejít přímo k databázi, mají latenci a propustnost vyšší výkon.
- **Proxy server:** V tomto režimu se všechna připojení jsou směrovány přes proxy server prostřednictvím bran Azure SQL Database. Pokud chcete povolit připojení, klient musí mít odchozí pravidla brány firewall, které umožňují jenom brána Azure SQL Database IP adresy (obvykle dvě IP adresy v jedné oblasti). Výběr v tomto režimu může vést k vyšší latence a propustnosti nižší, v závislosti na povaze zatížení. Důrazně doporučujeme `Redirect` zásady připojení přes `Proxy` zásady připojení pro nejnižší latenci a propustnost nejvyšší.
- **Výchozí hodnota:** To platí zásady připojení ve všech serverech po vytvoření není-li explicitně změnit zásady připojení buď `Proxy` nebo `Redirect`. Platné zásady závisí na tom, jestli se připojení pocházejí z v rámci Azure (`Redirect`) nebo mimo systém Azure (`Proxy`).

## <a name="connectivity-from-within-azure"></a>Připojení z v rámci Azure

Pokud se chcete připojit z v rámci Azure připojení mají zásady připojení `Redirect` ve výchozím nastavení. Zásady `Redirect` znamená, že po vytvoření relace TCP ke službě Azure SQL database relace klienta je pak přesměrují na správnou databázi clusteru a z té brány Azure SQL Database, která z se změní na virtuální IP adresu cílového cluster. Po tomto datu všechny následné pakety flow přímo do clusteru, obcházení brány Azure SQL Database. Následující diagram znázorňuje tento tok provozu.

![Přehled architektury](./media/sql-database-connectivity-architecture/connectivity-azure.png)

## <a name="connectivity-from-outside-of-azure"></a>Připojení z mimo Azure

Pokud se chcete připojit z mimo Azure, vaše připojení mají zásady připojení `Proxy` ve výchozím nastavení. Zásady `Proxy` znamená, že je vytvořena relace TCP přes bránu Azure SQL Database a všechny následné pakety flow přes bránu. Následující diagram znázorňuje tento tok provozu.

![Přehled architektury](./media/sql-database-connectivity-architecture/connectivity-onprem.png)

## <a name="azure-sql-database-gateway-ip-addresses"></a>IP adresy brány Azure SQL Database

Pro připojení k databázi Azure SQL z místních prostředků, budete muset povolit odchozí síťový provoz do Azure SQL Database brány pro vaši oblast Azure. Připojení, jdou pouze přes bránu pro připojení v `Proxy` režim, ve kterém je výchozí nastavení při připojování z místních prostředků.

V následující tabulce jsou uvedeny primárních a sekundárních IP adresy brány Azure SQL Database pro všechny datové oblasti. V některých oblastech existují dvě IP adresy. V těchto oblastech primární IP adresa je aktuální IP adresu brány a druhou IP adresu je IP adresa převzetí služeb při selhání. Převzetí služeb při selhání adresa je adresa, do kterého jsme může přesunout vašeho serveru, abyste měli vysokou dostupnost služeb. Pro tyto oblasti doporučujeme vám povolit odchozí IP adresy. Druhá IP adresa není ve vlastnictví společnosti Microsoft a není naslouchání žádné služby, dokud je aktivovaná služba Azure SQL Database tak, aby přijímal připojení.

| Název oblasti | Primární IP adresu | Sekundární adresa IP |
| --- | --- |--- |
| Austrálie – východ | 13.75.149.87 | 40.79.161.1 |
| Austrálie – jihovýchod | 191.239.192.109 | 13.73.109.251 |
| Brazílie – jih | 104.41.11.5 | |
| Kanada – střed | 40.85.224.249 | |
| Kanada – východ | 40.86.226.166 | |
| USA – střed | 23.99.160.139 | 13.67.215.62 |
| Východní Čína 1 | 139.219.130.35 | |
| Čína – východ 2 | 40.73.82.1 | |
| Čína – sever 1 | 139.219.15.17 | |
| Čína – sever 2 | 40.73.50.0 | |
| Východní Asie | 191.234.2.139 | 52.175.33.150 |
| USA – východ 1 | 191.238.6.43 | 40.121.158.30 |
| Východní USA 2 | 191.239.224.107 | 40.79.84.180 * |
| Francie – střed | 40.79.137.0 | 40.79.129.1 |
| Německo – střed | 51.4.144.100 | |
| Severovýchodní Německo | 51.5.144.179 | |
| Indie – střed | 104.211.96.159 | |
| Indie – jih | 104.211.224.146 | |
| Indie – západ | 104.211.160.80 | |
| Japonsko – východ | 191.237.240.43 | 13.78.61.196 |
| Japonsko – západ | 191.238.68.11 | 104.214.148.156 |
| Jižní Korea – střed | 52.231.32.42 | |
| Jižní Korea – jih | 52.231.200.86 |  |
| Středoseverní USA | 23.98.55.75 | 23.96.178.199 |
| Severní Evropa | 191.235.193.75 | 40.113.93.91 |
| Středojižní USA | 23.98.162.75 | 13.66.62.124 |
| Jihovýchodní Asie | 23.100.117.95 | 104.43.15.0 |
| Velká Británie – sever | 13.87.97.210 | |
| Velká Británie – jih 1 | 51.140.184.11 | |
| Velká Británie – jih 2 | 13.87.34.7 | |
| Spojené království – západ | 51.141.8.11 | |
| Západní střed USA | 13.78.145.25 | |
| Západní Evropa | 191.237.232.75 | 40.68.37.158 |
| USA – západ 1 | 23.99.34.75 | 104.42.238.205 |
| Západní USA 2 | 13.66.226.202 | |
||||

\* **POZNÁMKA:** *USA – východ 2* má také terciární IP adresu z `52.167.104.0`.

## <a name="change-azure-sql-database-connection-policy"></a>Změna zásad připojení Azure SQL Database

Chcete-li změnit zásady připojení Azure SQL Database pro server Azure SQL Database, použijte [conn zásad](https://docs.microsoft.com/cli/azure/sql/server/conn-policy) příkazu.

- Pokud vaše zásady připojení nastavená na `Proxy`, všechny síťové pakety flow přes bránu Azure SQL Database. U tohoto nastavení musíte povolit odchozí pouze IP brány Azure SQL Database. Pomocí nastavení `Proxy` má další latenci než nastavení `Redirect`.
- Pokud nastavení zásad připojení `Redirect`, všechny síťové pakety flow přímo do databáze clusteru. U tohoto nastavení musíte povolit odchozí několik IP adres.

## <a name="script-to-change-connection-settings-via-powershell"></a>Skript, chcete-li změnit nastavení připojení prostřednictvím prostředí PowerShell

> [!IMPORTANT]
> Tento skript vyžaduje [modulu Azure PowerShell](/powershell/azure/install-az-ps).

Následující skript prostředí PowerShell ukazuje, jak změnit zásady připojení.

```powershell
# Get SQL Server ID
$sqlserverid=(Get-AzureRmSqlServer -ServerName sql-server-name -ResourceGroupName sql-server-group).ResourceId

# Set URI
$id="$sqlserverid/connectionPolicies/Default"

# Get current connection policy
(Get-AzureRmResource -ResourceId $id).Properties.connectionType

# Update connection policy
Set-AzureRmResource -ResourceId $id -Properties @{"connectionType" = "Proxy"} -f
```

## <a name="script-to-change-connection-settings-via-azure-cli"></a>Skript, chcete-li změnit nastavení připojení prostřednictvím rozhraní příkazového řádku Azure

> [!IMPORTANT]
> Tento skript vyžaduje [rozhraní příkazového řádku Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

Následující skript rozhraní příkazového řádku ukazuje, jak změnit zásady připojení.

```azurecli-interactive
# Get SQL Server ID
sqlserverid=$(az sql server show -n sql-server-name -g sql-server-group --query 'id' -o tsv)

# Set URI
id="$sqlserverid/connectionPolicies/Default"

# Get current connection policy
az resource show --ids $id

# Update connection policy
az resource update --ids $id --set properties.connectionType=Proxy
```

## <a name="next-steps"></a>Další postup

- Informace o tom, jak změnit zásady připojení Azure SQL Database pro server Azure SQL Database najdete v tématu [conn zásad](https://docs.microsoft.com/cli/azure/sql/server/conn-policy).
- Informace o chování připojení Azure SQL Database pro klienty, kteří používají technologii ADO.NET 4.5 nebo novější verze, najdete v části [porty nad 1433 pro technologii ADO.NET 4.5](sql-database-develop-direct-route-ports-adonet-v12.md).
- Obecná aplikace vývoj přehled informace najdete v tématu [přehled vývoje databázových aplikací SQL Database](sql-database-develop-overview.md).
