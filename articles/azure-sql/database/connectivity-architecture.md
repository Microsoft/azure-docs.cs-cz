---
title: Architektura připojení ke službě Azure SQL Database
description: Tento dokument popisuje architekturu připojení Azure SQL Database pro databázová připojení z Azure nebo mimo Azure.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: fasttrack-edit, sqldbrb=1
titleSuffix: Azure SQL Database and Azure Synapse Analytics
ms.devlang: ''
ms.topic: conceptual
author: rohitnayakmsft
ms.author: rohitna
ms.reviewer: sstein, vanto
ms.date: 06/26/2020
ms.openlocfilehash: 71bd250cbfb2642a291d495273c4cd66ebb2c350
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91325381"
---
# <a name="azure-sql-database-and-azure-synapse-analytics-connectivity-architecture"></a>Architektura připojení Azure SQL Database a Azure synapse Analytics
[!INCLUDE[appliesto-sqldb-asa](../includes/appliesto-sqldb-asa.md)]

Tento článek vysvětluje architekturu různých komponent, které směrují síťový provoz na server v Azure SQL Database nebo Azure synapse Analytics. Vysvětluje taky různé zásady připojení a jejich dopad na klienty připojující se z Azure a klienty, kteří se připojují mimo Azure.

> [!IMPORTANT]
> Tento článek se *nevztahuje na* **spravovanou instanci SQL Azure**. [Pro spravovanou instanci se podívejte na architekturu připojení](../managed-instance/connectivity-architecture-overview.md).

## <a name="connectivity-architecture"></a>Architektura připojení

Následující diagram poskytuje podrobný přehled architektury připojení.

![Přehled architektury](./media/connectivity-architecture/connectivity-overview.png)

Následující kroky popisují, jak se naváže připojení k Azure SQL Database:

- Klienti se připojují k bráně, která má veřejnou IP adresu a naslouchá na portu 1433.
- Brána v závislosti na efektivních zásadách připojení přesměruje nebo proxy data do správného databázového clusteru.
- V rámci provozu databázového clusteru se předají do příslušné databáze.

## <a name="connection-policy"></a>Zásady připojení

Servery v SQL Database a Azure synapse podporují následující tři možnosti pro nastavení zásad připojení k serveru:

- **Přesměrování (doporučeno):** Klienti navázali připojení přímo k uzlu, který je hostitelem databáze, což vede ke snížení latence a lepší propustnosti. Pro připojení, která používají tento režim, musí klienti:
  - Povolí odchozí komunikaci z klienta se všemi IP adresami Azure SQL v oblasti na portech v rozsahu 11000 11999. Zjednodušte správu pomocí značek služeb pro SQL.  
  - Povolí odchozí komunikaci od klienta až po Azure SQL Database IP adresy brány na portu 1433.

- **Proxy server:** V tomto režimu jsou všechna připojení proxy prostřednictvím bran Azure SQL Database, což vede ke zvýšení latence a snížení propustnosti. Pro připojení, která používají tento režim, musí klienti umožňovat odchozí komunikaci od klienta až po Azure SQL Database IP adresy brány na portu 1433.

- **Výchozí:** Toto je zásada připojení platná pro všechny servery po vytvoření, pokud zásadu připojení explicitně neupravíte na buď `Proxy` nebo `Redirect` . Výchozí zásady jsou `Redirect` pro všechna připojení klientů pocházející z Azure (například z virtuálního počítače Azure) a `Proxy` pro všechna klientská připojení, která pocházejí mimo (například připojení z místní pracovní stanice).

`Redirect` `Proxy` Pro nejnižší latenci a nejvyšší propustnost důrazně doporučujeme zásady připojení oproti zásadám připojení. Budete ale muset splnit další požadavky na povolení síťového provozu, jak je uvedeno výše. Pokud se jedná o virtuální počítač Azure, můžete to udělat pomocí skupin zabezpečení sítě (NSG) s [visačkami služby](../../virtual-network/security-overview.md#service-tags). Pokud se klient připojuje z místní pracovní stanice, možná budete muset spolupracovat se správcem sítě a zapnout síťový provoz přes bránu firewall vaší firmy.

## <a name="connectivity-from-within-azure"></a>Konektivita uvnitř Azure

Pokud se připojujete z v rámci Azure, mají připojení `Redirect` ve výchozím nastavení zásady připojení. Zásada `Redirect` znamená, že po navázání relace TCP na Azure SQL Database se relace klienta přesměruje do správného databázového clusteru se změnou cílové virtuální IP adresy z této Azure SQL Database brány na cluster. Následně se všechny následné pakety nasměrují přímo do clusteru a vycházejí z Azure SQL Database brány. Tento tok přenosů znázorňuje následující diagram.

![Přehled architektury](./media/connectivity-architecture/connectivity-azure.png)

## <a name="connectivity-from-outside-of-azure"></a>Konektivita mimo Azure

Pokud se připojujete z oblasti mimo Azure, připojení mají `Proxy` ve výchozím nastavení zásady připojení. Zásada `Proxy` znamená, že je relace TCP vytvořená prostřednictvím brány Azure SQL Database a všech následných paketů toku prostřednictvím brány. Tento tok přenosů znázorňuje následující diagram.

![Přehled architektury](./media/connectivity-architecture/connectivity-onprem.png)

> [!IMPORTANT]
> Kromě toho otevřete porty TCP 1434 a 14000-14999, abyste umožnili [připojení pomocí DAC](https://docs.microsoft.com/sql/database-engine/configure-windows/diagnostic-connection-for-database-administrators?view=sql-server-2017#connecting-with-dac) .

## <a name="gateway-ip-addresses"></a>IP adresy brány

V následující tabulce jsou uvedené IP adresy bran podle oblasti. Pokud se chcete připojit k SQL Database nebo Azure synapse, je potřeba, abyste povolili síťový provoz do a ze **všech** bran pro danou oblast.

Podrobnosti o tom, jak se bude provoz migrovat na nové brány v konkrétních oblastech, najdete v následujícím článku: [Azure SQL Database migrace provozu na novější brány](gateway-migration.md)

| Název oblasti          | IP adresy brány |
| --- | --- |
| Austrálie – střed    | 20.36.105.0 |
| Central2 Austrálie   | 20.36.113.0 |
| Austrálie – východ       | 13.75.149.87, 40.79.161.1, 13.70.112.9 |
| Austrálie – jihovýchod | 191.239.192.109, 13.73.109.251, 13.77.48.10 |
| Brazil South         | 104.41.11.5, 191.233.200.14 |
| Střední Kanada       | 40.85.224.249, 52.246.152.0, 20.38.144.1 |
| Kanada – východ          | 40.86.226.166, 52.242.30.154 |
| Střední USA           | 13.67.215.62, 52.182.137.15, 23.99.160.139, 104.208.16.96, 104.208.21.1 |
| Čína – východ           | 139.219.130.35     |
| Čína – východ 2         | 40.73.82.1         |
| Čína – sever          | 139.219.15.17      |
| Čína – sever 2        | 40.73.50.0         |
| Východní Asie            | 191.234.2.139, 52.175.33.150, 13.75.32.4 |
| East US              | 40.121.158.30, 40.79.153.12, 191.238.6.43, 40.78.225.32 |
| USA – východ 2            | 40.79.84.180, 52.177.185.181, 52.167.104.0, 191.239.224.107, 104.208.150.3 |
| Francie – střed       | 40.79.137.0, 40.79.129.1 |
| Německo – střed      | 51.4.144.100       |
| Německo – sever východ   | 51.5.144.179       |
| Německo – středozápad | 51.116.240.0, 51.116.248.0, 51.116.152.0 |
| Indie – střed        | 104.211.96.159     |
| Indie – jih          | 104.211.224.146    |
| Indie – západ           | 104.211.160.80     |
| Japan East           | 13.78.61.196, 40.79.184.8, 13.78.106.224, 191.237.240.43, 40.79.192.5 |
| Japonsko – západ           | 104.214.148.156, 40.74.100.192, 191.238.68.11, 40.74.97.10 |
| Jižní Korea – střed        | 52.231.32.42       |
| Jižní Korea – jih          | 52.231.200.86      |
| USA – středosever     | 23.96.178.199, 23.98.55.75, 52.162.104.33 |
| Severní Evropa         | 40.113.93.91, 191.235.193.75, 52.138.224.1, 13.74.104.113 |
| Norsko – východ          | 51.120.96.0        |
| Norsko – západ          | 51.120.216.0       |
| Jižní Afrika – sever   | 102.133.152.0, 102.133.120.2       |
| Jižní Afrika – západ    | 102.133.24.0       |
| Středojižní USA     | 13.66.62.124, 23.98.162.75, 104.214.16.32, 20.45.121.1, 20.49.88.1   |
| Jihovýchodní Asie      | 104.43.15.0, 23.100.117.95, 40.78.232.3   |
| Švýcarsko – sever    | 51.107.56.0, 51.107.57.0 |
| Švýcarsko – západ     | 51.107.152.0, 51.107.153.0 |
| Spojené arabské emiráty – střed          | 20.37.72.64        |
| Spojené arabské emiráty sever            | 65.52.248.0        |
| Spojené království – jih             | 51.140.184.11, 51.105.64.0 |
| Spojené království – západ              | 51.141.8.11        |
| USA – středozápad      | 13.78.145.25, 13.78.248.43        |
| West Europe          | 40.68.37.158, 191.237.232.75, 104.40.168.105, 52.236.184.163  |
| USA – západ              | 104.42.238.205, 23.99.34.75, 13.86.216.196   |
| Západní USA 2            | 13.66.226.202, 40.78.240.8, 40.78.248.10  |
|                      |                    |

## <a name="next-steps"></a>Další kroky

- Informace o tom, jak změnit zásady Azure SQL Databaseho připojení pro server, najdete v části Connection [-Policy](https://docs.microsoft.com/cli/azure/sql/server/conn-policy).
- Informace o chování připojení Azure SQL Database pro klienty, kteří používají verzi ADO.NET 4,5 nebo novější, najdete v části [porty kromě 1433 pro ADO.NET 4,5](adonet-v12-develop-direct-route-ports.md).
- Obecné informace o vývoji aplikací naleznete v tématu [SQL Database Application Development Overview](develop-overview.md).
