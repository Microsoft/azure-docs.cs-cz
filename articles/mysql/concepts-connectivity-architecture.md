---
title: Architektura připojení – Azure Database for MySQL
description: Popisuje architekturu připojení pro server Azure Database for MySQL.
author: kummanish
ms.author: manishku
ms.service: mysql
ms.topic: conceptual
ms.date: 12/02/2019
ms.openlocfilehash: 22c77bee95533606156ec6cc337af1d743018005
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/03/2019
ms.locfileid: "74765320"
---
# <a name="connectivity-architecture-in-azure-database-for-mysql"></a>Architektura připojení v Azure Database for MySQL
Tento článek popisuje architekturu připojení Azure Database for MySQL a způsob, jakým jsou přenosy směrovány na vaši instanci Azure Database for MySQL od klientů v rámci i mimo Azure.

## <a name="connectivity-architecture"></a>Architektura připojení
Připojení k vašemu Azure Database for MySQL se naváže prostřednictvím brány zodpovědné za směrování příchozích připojení do fyzického umístění serveru v našich clusterech. Tok přenosů znázorňuje následující diagram.

![Přehled architektury připojení](./media/concepts-connectivity-architecture/connectivity-architecture-overview-proxy.png)

Když se klient připojí k databázi, získá připojovací řetězec, který se připojí k bráně. Tato brána má veřejnou IP adresu, která naslouchá na portu 3306. V rámci databázového clusteru je přenos předáván odpovídajícím Azure Database for MySQL. Aby bylo možné připojit se k serveru, například z podnikových sítí, je nutné otevřít bránu firewall na straně klienta, aby odchozí přenosy umožňovaly přístup k našim branám. Níže můžete najít úplný seznam IP adres, které používají naše brány v jednotlivých oblastech.

## <a name="azure-database-for-mysql-gateway-ip-addresses"></a>IP adresy Azure Database for MySQL brány
V následující tabulce je uveden seznam primárních a sekundárních IP adres Azure Database for MySQL brány pro všechny oblasti dat. Primární IP adresa je aktuální IP adresa brány a druhá IP adresa je IP adresa převzetí služeb při selhání v případě selhání primární služby. Jak už bylo zmíněno, zákazníci by měli mít odchozí připojení do obou IP adres. Druhá IP adresa nenaslouchá žádné službě, dokud ji neaktivujete Azure Database for MySQL, aby přijímala připojení.

| **Název oblasti** | **Primární IP adresa** | **Sekundární IP adresa** |
|:----------------|:-------------|:------------------------|
| Austrálie – východ | 13.75.149.87 | 40.79.161.1 |
| Austrálie – jihovýchod | 191.239.192.109 | 13.73.109.251 |
| Brazílie – jih | 104.41.11.5 | |
| Kanada – střed | 40.85.224.249 | |
| Kanada – východ | 40.86.226.166 | |
| Střední USA | 23.99.160.139 | 13.67.215.62 |
| Čína – východ 1 | 139.219.130.35 | |
| Čína – východ 2 | 40.73.82.1 | |
| Čína – sever 1 | 139.219.15.17 | |
| Čína – sever 2 | 40.73.50.0 | |
| Východní Asie | 191.234.2.139 | 52.175.33.150 |
| Východní USA 1 | 191.238.6.43 | 40.121.158.30 |
| Východ USA 2 | 191.239.224.107 | 40.79.84.180 * |
| Francie – střed | 40.79.137.0 | 40.79.129.1 |
| Německo – střed | 51.4.144.100 | |
| Indie – střed | 104.211.96.159 | |
| Indie – jih | 104.211.224.146 | |
| Indie – západ | 104.211.160.80 | |
| Japonsko – východ | 191.237.240.43 | 13.78.61.196 |
| Japonsko – západ | 191.238.68.11 | 104.214.148.156 |
| Korea – střed | 52.231.32.42 | |
| Korea – jih | 52.231.200.86 |  |
| Středoseverní USA | 23.98.55.75 | 23.96.178.199 |
| Severní Evropa | 191.235.193.75 | 40.113.93.91 |
| Středojižní USA | 23.98.162.75 | 13.66.62.124 |
| Jihovýchodní Asie | 23.100.117.95 | 104.43.15.0 |
| Jižní Afrika – sever | 102.133.152.0 | |
| Jižní Afrika – západ | 102.133.24.0 | |
| Spojené arabské emiráty – sever | 65.52.248.0 | |
| Velká Británie – jih | 51.140.184.11 | |
| Velká Británie – západ | 51.141.8.11| |
| Západní Evropa | 191.237.232.75 | 40.68.37.158 |
| Západní USA 1 | 23.99.34.75 | 104.42.238.205 |
| Západní USA 2 | 13.66.226.202 | |
||||

> [!NOTE]
> *Východní USA 2a* má také terciární IP adresu `52.167.104.0`.

## <a name="next-steps"></a>Další kroky

* [Vytváření a Správa Azure Database for MySQL pravidel brány firewall pomocí Azure Portal](./howto-manage-firewall-using-portal.md)
* [Vytvoření a Správa pravidel brány firewall Azure Database for MySQL pomocí rozhraní příkazového řádku Azure](./howto-manage-firewall-using-cli.md)