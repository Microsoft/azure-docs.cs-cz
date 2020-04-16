---
title: Architektura připojení
description: Tento dokument vysvětluje architekturu připojení Azure SQL pro připojení k databázím z Azure nebo mimo Azure.
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
ms.date: 03/09/2020
ms.openlocfilehash: 2028aac9c01aedc4baa568d370c9f7d21c920647
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81419259"
---
# <a name="azure-sql-connectivity-architecture"></a>Architektura připojení k SQL Azure
> [!NOTE]
> Tento článek se vztahuje na Server Azure SQL a databáze SQL Database a SQL Data Warehouse, které jsou vytvořené na serveru Azure SQL. Pro zjednodušení se SQL Database používá k označení SQL Database i SQL Data Warehouse.

> [!IMPORTANT]
> Tento článek se *nevztahuje* na **azure SQL database spravované instance**. Odkazovat na [architekturu připojení pro spravovanou instanci](sql-database-managed-instance-connectivity-architecture.md).

Tento článek vysvětluje architekturu různých součástí, které směrují síťový provoz do databáze Azure SQL Database nebo SQL Data Warehouse. Vysvětluje také různé zásady připojení a jak ovlivňuje připojení klientů z Azure a klientů, kteří se připojují mimo Azure. 

## <a name="connectivity-architecture"></a>Architektura připojení

Následující diagram poskytuje podrobný přehled architektury připojení azure SQL database.

![přehled architektury](./media/sql-database-connectivity-architecture/connectivity-overview.png)

Následující kroky popisují, jak je navázáno připojení k databázi Azure SQL:

- Klienti se připojují k bráně, která má veřejnou IP adresu a naslouchá na portu 1433.
- Brána v závislosti na zásadách efektivního připojení přesměruje nebo přesměruje přenosy do správného databázového clusteru.
- Uvnitř clusteru databáze provoz je předán do příslušné databáze Azure SQL.

## <a name="connection-policy"></a>Zásady připojení

Azure SQL Database podporuje následující tři možnosti pro nastavení zásad připojení serveru SQL Database:

- **Přesměrování (doporučeno):** Klienti navázat připojení přímo k uzlu hostování databáze, což vede ke snížení latence a lepší propustnost. Aby připojení používali tento režim, klienti musí:
   - Povolit odchozí komunikaci z klienta na všechny IP adresy Azure SQL v oblasti na portech v rozsahu 11000 11999. Pomocí značek služby pro SQL to usnadníte správě.  
   - Povolit odchozí komunikaci z klienta na IP adresy brány Azure SQL Database na portu 1433.

- **Proxy:** V tomto režimu jsou všechna připojení proxied prostřednictvím brány Azure SQL Database, což vede ke zvýšení latence a snížení propustnosti. Aby připojení mohli používat tento režim, klienti musí povolit odchozí komunikaci z klienta na IP adresy brány Azure SQL Database na portu 1433.

- **Výchozí:** Toto je zásady připojení platné na všech serverech po `Proxy` vytvoření, pokud explicitně nezměníte zásady připojení na některou nebo . `Redirect` Výchozí zásada`Redirect` je pro všechna klientská připojení pocházející z Azure (například z virtuálního počítače Azure) a `Proxy`pro všechna klientská připojení pocházející zvenčí (například připojení z místní pracovní stanice).

 Důrazně doporučujeme `Redirect` zásady `Proxy` připojení přes zásady připojení pro nejnižší latenci a nejvyšší propustnost. Budete však muset splnit další požadavky pro povolení síťového provozu, jak je uvedeno výše. Pokud je klient virtuálním počítačem Azure, můžete to provést pomocí skupin zabezpečení sítě (NSG) se [značkami služeb](../virtual-network/security-overview.md#service-tags). Pokud se klient připojuje z místní pracovní stanice, bude pravděpodobně nutné spolupracovat se správcem sítě, aby byl síťový provoz povolen prostřednictvím podnikové brány firewall.

## <a name="connectivity-from-within-azure"></a>Připojení z Azure

Pokud se připojujete z Azure vaše připojení `Redirect` mají zásady připojení ve výchozím nastavení. Zásada `Redirect` znamená, že po vytvoření relace TCP do databáze Azure SQL je relace klienta přesměrována do pravého databázového clusteru se změnou cílové virtuální IP adresy z brány Azure SQL Database na bránu clusteru. Poté všechny následné pakety toku přímo do clusteru, obcházet bránu Azure SQL Database. Následující diagram znázorňuje tento tok provozu.

![přehled architektury](./media/sql-database-connectivity-architecture/connectivity-azure.png)

## <a name="connectivity-from-outside-of-azure"></a>Připojení mimo Azure

Pokud se připojujete z mimo Azure, vaše `Proxy` připojení mají zásady připojení ve výchozím nastavení. Zásada `Proxy` znamená, že relace TCP je vytvořena prostřednictvím brány Azure SQL Database a všechny následné pakety toku přes bránu. Následující diagram znázorňuje tento tok provozu.

![přehled architektury](./media/sql-database-connectivity-architecture/connectivity-onprem.png)

> [!IMPORTANT]
> Navíc otevřené porty 14000-14999 pro [připojení pomocí dac](https://docs.microsoft.com/sql/database-engine/configure-windows/diagnostic-connection-for-database-administrators?view=sql-server-2017#connecting-with-dac)


## <a name="azure-sql-database-gateway-ip-addresses"></a>IP adresy brány Azure SQL Database

V následující tabulce jsou uvedeny ip adresy bran podle oblastí. Chcete-li se připojit k azure sql database, musíte povolit síťový provoz & ze **všech** bran pro oblast.

Podrobnosti o tom, jak se bude přenos migrovat do nových bran v konkrétních oblastech, najdou v následujícím článku: [Migrace provozu databáze Azure SQL na novější brány](sql-database-gateway-migration.md)


| Název oblasti          | Ip adresy brány |
| --- | --- |
| Austrálie – střed    | 20.36.105.0 |
| Austrálie Central2   | 20.36.113.0 |
| Austrálie – východ       | 13.75.149.87, 40.79.161.1 |
| Austrálie – jihovýchod | 191.239.192.109, 13.73.109.251 |
| Brazílie – jih         | 104.41.11.5, 191.233.200.14 |
| Střední Kanada       | 40.85.224.249      |
| Kanada – východ          | 40.86.226.166      |
| USA – střed           | 13.67.215.62, 52.182.137.15, 23.99.160.139, 104.208.16.96, 104.208.21.1 | 
| Čína – východ           | 139.219.130.35     |
| Čína východ 2         | 40.73.82.1         |
| Čína – sever          | 139.219.15.17      |
| Čína Sever 2        | 40.73.50.0         |
| Východní Asie            | 191.234.2.139, 52.175.33.150, 13.75.32.4 |
| USA – východ              | 40.121.158.30, 40.79.153.12, 191.238.6.43, 40.78.225.32 |
| USA – východ 2            | 40.79.84.180, 52.177.185.181, 52.167.104.0, 191.239.224.107, 104.208.150.3 | 
| Francie – střed       | 40.79.137.0, 40.79.129.1 |
| Německo – střed      | 51.4.144.100       |
| Německo Severovýchod   | 51.5.144.179       |
| Indie – střed        | 104.211.96.159     |
| Indie – jih          | 104.211.224.146    |
| Indie – západ           | 104.211.160.80     |
| Japonsko – východ           | 13.78.61.196, 40.79.184.8, 13.78.106.224, 191.237.240.43, 40.79.192.5 | 
| Japonsko – západ           | 104.214.148.156, 40.74.100.192, 191.238.68.11, 40.74.97.10 | 
| Jižní Korea – střed        | 52.231.32.42       |
| Jižní Korea – jih          | 52.231.200.86      |
| USA – středosever     | 23.96.178.199, 23.98.55.75, 52.162.104.33 |
| Severní Evropa         | 40.113.93.91, 191.235.193.75, 52.138.224.1 | 
| Norsko východ          | 51.120.96.0        |
| Norsko Západ          | 51.120.216.0       |
| Jižní Afrika – sever   | 102.133.152.0      |
| Jižní Afrika – západ    | 102.133.24.0       |
| USA – středojih     | 13.66.62.124, 23.98.162.75, 104.214.16.32   | 
| Jihovýchodní Asie      | 104.43.15.0, 23.100.117.95, 40.78.232.3   | 
| SAE Centrální          | 20.37.72.64        |
| SAE Sever            | 65.52.248.0        |
| Spojené království – jih             | 51.140.184.11      |
| Spojené království – západ              | 51.141.8.11        |
| USA – středozápad      | 13.78.145.25       |
| Západní Evropa          | 40.68.37.158, 191.237.232.75, 104.40.168.105  |
| USA – západ              | 104.42.238.205, 23.99.34.75, 13.86.216.196   |
| USA – západ 2            | 13.66.226.202      |
|                      |                    |



## <a name="next-steps"></a>Další kroky

- Informace o tom, jak změnit zásady připojení databáze Azure SQL pro server Azure SQL Database, naleznete [v zásadách conn.](https://docs.microsoft.com/cli/azure/sql/server/conn-policy)
- Informace o chování připojení k Azure SQL Database pro klienty, kteří používají ADO.NET 4.5 nebo novější verzi, najdete v článku [Porty nad 1433 pro ADO.NET 4.5](sql-database-develop-direct-route-ports-adonet-v12.md).
- Obecné informace o přehledu vývoje aplikací naleznete v [tématu PŘEHLED vývoje aplikací databáze SQL](sql-database-develop-overview.md).
