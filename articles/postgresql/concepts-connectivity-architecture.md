---
title: Architektura připojení v Azure Database for PostgreSQL
description: Popisuje architekturu připojení pro server Azure Database for PostgreSQL.
author: kummanish
ms.author: manishku
ms.service: postgresql
ms.topic: conceptual
ms.date: 05/23/2019
ms.openlocfilehash: 92844f0fe3a851802836015a1340983eb4633ed2
ms.sourcegitcommit: d3dced0ff3ba8e78d003060d9dafb56763184d69
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/22/2019
ms.locfileid: "69900551"
---
# <a name="connectivity-architecture-in-azure-database-for-postgresql"></a>Architektura připojení v Azure Database for PostgreSQL
Tento článek popisuje architekturu připojení Azure Database for PostgreSQL a také způsob, jakým se přesměruje přenos do vaší Azure Database for PostgreSQL instance databáze od klientů v rámci i mimo Azure.

## <a name="connectivity-architecture"></a>Architektura připojení
Připojení k vašemu Azure Database for PostgreSQL se naváže prostřednictvím brány zodpovědné za směrování příchozích připojení do fyzického umístění serveru v našich clusterech. Tok přenosů znázorňuje následující diagram.

![Přehled architektury připojení](./media/concepts-connectivity-architecture/connectivity-architecture-overview-proxy.png)

Když se klient připojí k databázi, získá připojovací řetězec, který se připojí k bráně. Tato brána má veřejnou IP adresu, která naslouchá na portu 5432. V rámci provozu databázového clusteru se předají příslušné Azure Database for PostgreSQL. Aby bylo možné připojit se k serveru, například z podnikových sítí, je nutné otevřít bránu firewall na straně klienta, aby odchozí přenosy umožňovaly přístup k našim branám. Níže můžete najít úplný seznam IP adres, které používají naše brány v jednotlivých oblastech.

## <a name="azure-database-for-postgresql-gateway-ip-addresses"></a>IP adresy Azure Database for PostgreSQL brány
V následující tabulce je uveden seznam primárních a sekundárních IP adres Azure Database for PostgreSQL brány pro všechny oblasti dat. Primární IP adresa je aktuální IP adresa brány a druhá IP adresa je IP adresa převzetí služeb při selhání v případě selhání primární služby. Jak už bylo zmíněno, zákazníci by měli mít odchozí připojení do obou IP adres. Druhá IP adresa nenaslouchá žádné službě, dokud ji neaktivujete Azure Database for PostgreSQL, aby přijímala připojení.

| **Název oblasti** | **Primární IP adresa** | **Sekundární IP adresa** |
|:----------------|:-------------|:------------------------|
| Austrálie – východ | 13.75.149.87 | 40.79.161.1 |
| Austrálie – jihovýchod | 191.239.192.109 | 13.73.109.251 |
| Brazílie – jih | 104.41.11.5 | |
| Kanada – střed | 40.85.224.249 | |
| Kanada – východ | 40.86.226.166 | |
| Střed USA | 23.99.160.139 | 13.67.215.62 |
| Čína – východ 1 | 139.219.130.35 | |
| Čína – východ 2 | 40.73.82.1 | |
| Čína – sever 1 | 139.219.15.17 | |
| Čína – sever 2 | 40.73.50.0 | |
| Východní Asie | 191.234.2.139 | 52.175.33.150 |
| Východní USA 1 | 191.238.6.43 | 40.121.158.30 |
| Východní USA 2 | 191.239.224.107 | 40.79.84.180 * |
| Francie – střed | 40.79.137.0 | 40.79.129.1 |
| Střední Německo | 51.4.144.100 | |
| Indie – střed | 104.211.96.159 | |
| Indie – jih | 104.211.224.146 | |
| Indie – západ | 104.211.160.80 | |
| Japonsko – východ | 191.237.240.43 | 13.78.61.196 |
| Japonsko – západ | 191.238.68.11 | 104.214.148.156 |
| Jižní Korea – střed | 52.231.32.42 | |
| Jižní Korea – jih | 52.231.200.86 |  |
| Střed USA – sever | 23.98.55.75 | 23.96.178.199 |
| Severní Evropa | 191.235.193.75 | 40.113.93.91 |
| Střed USA – jih | 23.98.162.75 | 13.66.62.124 |
| Jihovýchodní Asie | 23.100.117.95 | 104.43.15.0 |
| Velká Británie – jih | 51.140.184.11 | |
| Spojené království – západ | 51.141.8.11| |
| Západní Evropa | 191.237.232.75 | 40.68.37.158 |
| Západní USA 1 | 23.99.34.75 | 104.42.238.205 |
| USA – západ 2 | 13.66.226.202 | |
||||

> [!NOTE]
> *Východní USA 2* má také terciární IP adresu `52.167.104.0`.

## <a name="next-steps"></a>Další postup

* [Vytváření a Správa Azure Database for PostgreSQL pravidel brány firewall pomocí Azure Portal](./howto-manage-firewall-using-portal.md)
* [Vytvoření a Správa pravidel brány firewall Azure Database for PostgreSQL pomocí rozhraní příkazového řádku Azure](./howto-manage-firewall-using-cli.md)
