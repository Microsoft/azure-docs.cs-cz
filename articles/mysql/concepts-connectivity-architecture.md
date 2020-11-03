---
title: Architektura připojení – Azure Database for MySQL
description: Popisuje architekturu připojení pro server Azure Database for MySQL.
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: conceptual
ms.date: 03/16/2020
ms.openlocfilehash: 5d3842675cf4b36dfaa95ed6041bf8c6f0978a53
ms.sourcegitcommit: 80034a1819072f45c1772940953fef06d92fefc8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/03/2020
ms.locfileid: "93242870"
---
# <a name="connectivity-architecture-in-azure-database-for-mysql"></a>Architektura připojení v Azure Database for MySQL
Tento článek popisuje architekturu připojení Azure Database for MySQL a způsob, jakým jsou přenosy směrovány na vaši instanci Azure Database for MySQL od klientů v rámci i mimo Azure.

## <a name="connectivity-architecture"></a>Architektura připojení
Připojení k vašemu Azure Database for MySQL se naváže prostřednictvím brány zodpovědné za směrování příchozích připojení do fyzického umístění serveru v našich clusterech. Tok přenosů znázorňuje následující diagram.

:::image type="content" source="./media/concepts-connectivity-architecture/connectivity-architecture-overview-proxy.png" alt-text="Přehled architektury připojení":::

Když se klient připojí k databázi, získá připojovací řetězec, který se připojí k bráně. Tato brána má veřejnou IP adresu, která naslouchá na portu 3306. V rámci databázového clusteru je přenos předáván odpovídajícím Azure Database for MySQL. Aby bylo možné připojit se k serveru, například z podnikových sítí, je nutné otevřít bránu firewall na straně klienta, aby odchozí přenosy umožňovaly přístup k našim branám. Níže můžete najít úplný seznam IP adres, které používají naše brány v jednotlivých oblastech.

## <a name="azure-database-for-mysql-gateway-ip-addresses"></a>IP adresy Azure Database for MySQL brány
V následující tabulce je uveden seznam primárních a sekundárních IP adres Azure Database for MySQL brány pro všechny oblasti dat. Primární IP adresa je aktuální IP adresa brány a druhá IP adresa je IP adresa převzetí služeb při selhání v případě selhání primární služby. Jak už bylo zmíněno, zákazníci by měli mít odchozí připojení do obou IP adres. Druhá IP adresa nenaslouchá žádné službě, dokud ji neaktivujete Azure Database for MySQL, aby přijímala připojení.

| **Název oblasti** | **IP adresy brány** |
|:----------------|:-------------|
| Austrálie – střed| 20.36.105.0     |
| Central2 Austrálie     | 20.36.113.0   |
| Austrálie – východ | 13.75.149.87, 40.79.161.1     |
| Austrálie – jihovýchod |191.239.192.109, 13.73.109.251   |
| Brazil South | 104.41.11.5, 191.233.201.8, 191.233.200.16  |
| Střední Kanada |40.85.224.249  |
| Kanada – východ | 40.86.226.166    |
| Střední USA | 23.99.160.139, 13.67.215.62, 52.182.136.37, 52.182.136.38     |
| Čína – východ | 139.219.130.35    |
| Čína – východ 2 | 40.73.82.1  |
| Čína – sever | 139.219.15.17    |
| Čína – sever 2 | 40.73.50.0     |
| Východní Asie | 191.234.2.139, 52.175.33.150, 13.75.33.20, 13.75.33.21     |
| East US | 40.121.158.30, 191.238.6.43, 40.71.8.203, 40.71.83.113 |
| USA – východ 2 |40.79.84.180, 191.239.224.107, 52.177.185.181, 40.70.144.38, 52.167.105.38  |
| Francie – střed | 40.79.137.0, 40.79.129.1  |
| Francie – jih | 40.79.177.0     |
| Německo – střed | 51.4.144.100     |
| Německo – sever východ | 51.5.144.179  |
| Indie – střed | 104.211.96.159     |
| Indie – jih | 104.211.224.146  |
| Indie – západ | 104.211.160.80    |
| Japan East | 13.78.61.196, 191.237.240.43  |
| Japonsko – západ | 104.214.148.156, 191.238.68.11, 40.74.96.6, 40.74.96.7    |
| Jižní Korea – střed | 52.231.32.42   |
| Jižní Korea – jih | 52.231.200.86    |
| USA – středosever | 23.96.178.199, 23.98.55.75, 52.162.104.35, 52.162.104.36    |
| Severní Evropa | 40.113.93.91, 191.235.193.75, 52.138.224.6, 52.138.224.7    |
| Jižní Afrika – sever  | 102.133.152.0    |
| Jižní Afrika – západ | 102.133.24.0   |
| Středojižní USA |13.66.62.124, 23.98.162.75, 104.214.16.39, 20.45.120.0   |
| Jihovýchodní Asie | 104.43.15.0, 23.100.117.95, 40.78.233.2, 23.98.80.12     |
| Spojené arabské emiráty – střed | 20.37.72.64  |
| Spojené arabské emiráty sever | 65.52.248.0    |
| Spojené království – jih | 51.140.184.11   |
| Spojené království – západ | 51.141.8.11  |
| USA – středozápad | 13.78.145.25     |
| West Europe | 40.68.37.158, 191.237.232.75, 13.69.105.208 ,104.40.169.187 |
| USA – západ | 104.42.238.205, 23.99.34.75 ,13.86.216.212, 13.86.217.212 |
| Západní USA 2 | 13.66.226.202  |
||||

## <a name="connection-redirection"></a>Přesměrování připojení

Azure Database for MySQL podporuje další zásady připojení, **přesměrování** , která pomáhá snižovat latenci sítě mezi klientskými aplikacemi a servery MySQL. Po navázání počáteční relace protokolu TCP na Azure Database for MySQL server vrátí tato funkce server back-end uzel, který hostuje server MySQL, do klienta. Následně se všechny následné pakety nasměrují přímo na server a vynechá bránu. Jako tok paketů přímo na server, latence a propustnost vylepší výkon.

Tato funkce je podporovaná v Azure Database for MySQL serverech s verzemi modulu 5,6, 5,7 a 8,0.

Podpora pro přesměrování je dostupná v rozšíření PHP [mysqlnd_azure](https://github.com/microsoft/mysqlnd_azure) , vyvinuté společností Microsoft a je dostupná na [PECL](https://pecl.php.net/package/mysqlnd_azure). Další informace o tom, jak používat přesměrování ve svých aplikacích, najdete v článku [konfigurace přesměrování](./howto-redirection.md) .

> [!IMPORTANT]
> Podpora přesměrování [mysqlnd_azure](https://github.com/microsoft/mysqlnd_azure) rozšíření PHP je v současnosti ve verzi Preview.

## <a name="next-steps"></a>Další kroky

* [Vytváření a Správa Azure Database for MySQL pravidel brány firewall pomocí Azure Portal](./howto-manage-firewall-using-portal.md)
* [Vytvoření a Správa pravidel brány firewall Azure Database for MySQL pomocí rozhraní příkazového řádku Azure](./howto-manage-firewall-using-cli.md)
* [Konfigurace přesměrování pomocí Azure Database for MySQL](./howto-redirection.md)
