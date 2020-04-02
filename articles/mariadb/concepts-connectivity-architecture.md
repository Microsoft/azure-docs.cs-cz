---
title: Architektura připojení – databáze Azure pro MariaDB
description: Popisuje architekturu připojení pro váš server Azure Database pro MariaDB.
author: kummanish
ms.author: manishku
ms.service: mariadb
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: 38b6f797541ef07fa3fb5e1dc71029a4cbcf5d22
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/02/2020
ms.locfileid: "80546292"
---
# <a name="connectivity-architecture-in-azure-database-for-mariadb"></a>Architektura připojení v Azure Database pro MariaDB
Tento článek vysvětluje architekturu připojení Azure Database for MariaDB a také jak je provoz směrován do instance Azure Database for MariaDB od klientů v rámci Azure i mimo něj.

## <a name="connectivity-architecture"></a>Architektura připojení

Připojení k databázi Azure pro MariaDB se navazuje prostřednictvím brány, která je zodpovědná za směrování příchozích připojení k fyzickému umístění vašeho serveru v našich clusterech. Následující diagram znázorňuje tok provozu.

![Přehled architektury připojení](./media/concepts-connectivity-architecture/connectivity-architecture-overview-proxy.png)

Jako klient připojit k databázi, získají připojovací řetězec, který se připojuje k bráně. Tato brána má veřejnou IP adresu, která naslouchá portu 3306. Uvnitř databázového clusteru se přenosy předávají do příslušné databáze Azure pro MariaDB. Proto pro připojení k serveru, například z podnikových sítí, je nutné otevřít bránu firewall na straně klienta, aby se odchozí provoz mohl dostat k našim branám. Níže naleznete kompletní seznam IP adres používaných našimi branami v regionu.

## <a name="azure-database-for-mariadb-gateway-ip-addresses"></a>Azure Database for MariaDB adresy IP brány

V následující tabulce jsou uvedeny primární a sekundární IP adresy brány Azure Database for MariaDB pro všechny oblasti dat. Primární IP adresa je aktuální IP adresa brány a druhá IP adresa je ip adresa s podporou převzetí služeb při selhání v případě selhání primární. Jak již bylo zmíněno, zákazníci by měli povolit odchozí na obě IP adresy. Druhá IP adresa nenaslouchá žádným službám, dokud se neaktivuje azure database pro MariaDB přijímat připojení.

| **Název oblasti** | **Ip adresy brány** |
|:----------------|:-------------|
| Austrálie – střed| 20.36.105.0     |
| Austrálie Central2     | 20.36.113.0   |
| Austrálie – východ | 13.75.149.87, 40.79.161.1     |
| Austrálie – jihovýchod |191.239.192.109, 13.73.109.251   |
| Brazílie – jih | 104.41.11.5, 191.233.201.8, 191.233.200.16  |
| Střední Kanada |40.85.224.249  |
| Kanada – východ | 40.86.226.166    |
| USA – střed | 23.99.160.139, 13.67.215.62, 52.182.136.37, 52.182.136.38     |
| Čína – východ | 139.219.130.35    |
| Čína východ 2 | 40.73.82.1  |
| Čína – sever | 139.219.15.17    |
| Čína Sever 2 | 40.73.50.0     |
| Východní Asie | 191.234.2.139, 52.175.33.150, 13.75.33.20, 13.75.33.21     |
| USA – východ | 40.121.158.30, 191.238.6.43  |
| USA – východ 2 |40.79.84.180, 191.239.224.107, 52.177.185.181, 40.70.144.38, 52.167.105.38  |
| Francie – střed | 40.79.137.0, 40.79.129.1  |
| Německo – střed | 51.4.144.100     |
| Německo Severovýchod | 51.5.144.179  |
| Indie – střed | 104.211.96.159     |
| Indie – jih | 104.211.224.146  |
| Indie – západ | 104.211.160.80    |
| Japonsko – východ | 13.78.61.196, 191.237.240.43  |
| Japonsko – západ | 104.214.148.156, 191.238.68.11, 40.74.96.6, 40.74.96.7    |
| Jižní Korea – střed | 52.231.32.42   |
| Jižní Korea – jih | 52.231.200.86    |
| USA – středosever | 23.96.178.199, 23.98.55.75, 52.162.104.35, 52.162.104.36    |
| Severní Evropa | 40.113.93.91, 191.235.193.75, 52.138.224.6, 52.138.224.7    |
| Jižní Afrika – sever  | 102.133.152.0    |
| Jižní Afrika – západ | 102.133.24.0   |
| USA – středojih |13.66.62.124, 23.98.162.75, 104.214.16.39, 20.45.120.0   |
| Jihovýchodní Asie | 104.43.15.0, 23.100.117.95, 40.78.233.2, 23.98.80.12     |
| SAE Centrální | 20.37.72.64  |
| SAE Sever | 65.52.248.0    |
| Spojené království – jih | 51.140.184.11   |
| Spojené království – západ | 51.141.8.11  |
| USA – středozápad | 13.78.145.25     |
| Západní Evropa | 40.68.37.158, 191.237.232.75, 13.69.105.208  |
| USA – západ | 104.42.238.205, 23.99.34.75  |
| USA – západ 2 | 13.66.226.202  |
||||

## <a name="next-steps"></a>Další kroky

* [Vytváření a správa pravidel brány firewall Azure Database for MariaDB pomocí portálu Azure](./howto-manage-firewall-portal.md)
* [Vytvoření a správa pravidel brány firewall Azure Database for MariaDB pomocí azure cli](./howto-manage-firewall-cli.md)
