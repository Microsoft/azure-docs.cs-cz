---
title: Architektura připojení ve službě Azure Database pro MariaDB
description: Popisuje architekturu připojení k Azure Database pro MariaDB server.
author: kummanish
ms.author: manishku
ms.service: mariaDB
ms.topic: conceptual
ms.date: 05/23/2019
ms.openlocfilehash: 126e7ce4d9784a2f6a59f39562dff9a0b9d60ea0
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "66735388"
---
# <a name="connectivity-architecture-in-azure-database-for-mariadb"></a>Architektura připojení ve službě Azure Database pro MariaDB
Azure Database pro MariaDB připojení i architektura Tento článek vysvětluje, jak provoz se směřuje k Azure Database pro MariaDB instanci od klientů v rámci i mimo Azure.

## <a name="connectivity-architecture"></a>Architektura připojení

K Azure Database pro MariaDB připojení prostřednictvím brány, která zodpovídá za směrování příchozích připojení na fyzické umístění serveru v našich clusterů. Následující diagram znázorňuje tok provozu.

![Architektura připojení](./media/concepts-connectivity-architecture/connectivity-architecture-overview-proxy.png)

Jako klient připojení k databázi, dostanou připojovací řetězec, který se připojuje k bráně. Této brány má veřejnou IP adresu, která naslouchá na portu 3306. Uvnitř databáze clusteru přenosy předávaly do příslušné – Azure Database pro MariaDB. Proto aby bylo možné připojit k serveru, například z podnikové sítě, je nezbytné k otevření brány firewall na straně klienta k povolení odchozího přenosu do být schopný připojit naše brány. Níže najdete úplný seznam IP adresy používané službou naše brány v jedné oblasti.

## <a name="azure-database-for-mariadb-gateway-ip-addresses"></a>Azure Database pro MariaDB brány IP adresy

V následující tabulce jsou uvedeny primárních a sekundárních IP adresy služby Azure Database pro MariaDB bránu pro všechny datové oblasti. Primární IP adresa je aktuální IP adresu brány a druhou IP adresu je IP adresa převzetí služeb při selhání v případě selhání primární. Jak už bylo zmíněno, zákazníci měli povolit odchozí IP adresy. Druhá IP adresa není naslouchání žádné služby až po aktivaci Azure Database pro MariaDB tak, aby přijímal připojení.

| **Název oblasti** | **Primární IP adresu** | **Sekundární adresa** |
|:----------------|:-------------|:------------------------|
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
| Velká Británie – jih | 51.140.184.11 | |
| Spojené království – západ | 51.141.8.11| |
| Západní Evropa | 191.237.232.75 | 40.68.37.158 |
| USA – západ 1 | 23.99.34.75 | 104.42.238.205 |
| Západní USA 2 | 13.66.226.202 | |
||||

> [!NOTE]
> *USA – východ 2* má také terciární IP adresu z `52.167.104.0`.

## <a name="next-steps"></a>Další postup

* [Vytváření a správa Azure Database pro MariaDB pravidla brány firewall pomocí webu Azure portal](./howto-manage-firewall-portal.md)
* [Vytváření a správa Azure Database pro MariaDB pravidel brány firewall pomocí Azure CLI](./howto-manage-firewall-cli.md)
