---
title: Architektura připojení – Azure Database for MySQL
description: Popisuje architekturu připojení pro server Azure Database for MySQL.
author: Bashar-MSFT
ms.author: bahusse
ms.service: mysql
ms.topic: conceptual
ms.date: 2/11/2021
ms.openlocfilehash: c888a6882f2a408801492de914c57e3e9a6eeaed
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/14/2021
ms.locfileid: "100375506"
---
# <a name="connectivity-architecture-in-azure-database-for-mysql"></a>Architektura připojení v Azure Database for MySQL
Tento článek popisuje architekturu připojení Azure Database for MySQL a způsob, jakým jsou přenosy směrovány na vaši instanci Azure Database for MySQL od klientů v rámci i mimo Azure.

## <a name="connectivity-architecture"></a>Architektura připojení
Připojení k vašemu Azure Database for MySQL se naváže prostřednictvím brány zodpovědné za směrování příchozích připojení do fyzického umístění serveru v našich clusterech. Tok přenosů znázorňuje následující diagram.

:::image type="content" source="./media/concepts-connectivity-architecture/connectivity-architecture-overview-proxy.png" alt-text="Přehled architektury připojení":::

Když se klient připojí k databázi, řetězec připojení k serveru se přeloží na IP adresu brány. Brána naslouchá na IP adrese na portu 3306. V rámci databázového clusteru je přenos předáván odpovídajícím Azure Database for MySQL. Aby bylo možné připojit se k serveru, například z podnikových sítí, je nutné otevřít **bránu firewall na straně klienta, aby odchozí přenosy umožňovaly přístup k našim branám**. Níže můžete najít úplný seznam IP adres, které používají naše brány v jednotlivých oblastech.

## <a name="azure-database-for-mysql-gateway-ip-addresses"></a>IP adresy Azure Database for MySQL brány

Služba brány je hostovaná ve skupině bezstavových výpočetních uzlů s IP adresou, ke kterým má klient při pokusu o připojení k Azure Database for MySQL serveru nejprve přístup. 

V rámci průběžné údržby služby pravidelně aktualizujeme výpočetní hardware hostující brány, abychom zajistili, že poskytujeme nejbezpečnější a výkonné prostředí. Po obnovení hardwaru brány se nejprve vystaví nový prstenec výpočetních uzlů. Tento nový okruh obsluhuje provoz pro všechny nově vytvořené Azure Database for MySQL servery a bude mít jinou IP adresu než starší prstence brány ve stejné oblasti pro odlišení provozu. Jakmile je nový okruh plně funkční, starší hardware brány obsluhující stávající servery se plánuje na vyřazení z provozu. Před vyřazením hardwaru brány do provozu budou zákazníci, kteří používají své servery a připojení ke starším partnerům brány, dostávat e-mailem a v Azure Portal tři měsíce předem před vyřazením z provozu. Vyřazení bran může mít vliv na připojení k vašim serverům, pokud 

* V připojovacím řetězci vaší aplikace můžete zakódovat IP adresy brány. Nedoporučuje se. V připojovacím řetězci pro vaši aplikaci byste měli použít plně kvalifikovaný název domény (FQDN) vašeho serveru ve formátu <servername> . MySQL.Database.Azure.com. 
* Novější IP adresy brány v bráně firewall na straně klienta neaktualizujete, aby odchozí přenosy umožňovaly přístup k novým okruhům bran.

V následující tabulce jsou uvedené IP adresy brány Azure Database for MySQL brány pro všechny oblasti dat. Nejaktuálnější informace o IP adresách brány pro jednotlivé oblasti jsou zachovány v následující tabulce. Následující tabulka uvádí následující sloupce:

* **IP adresy brány:** V tomto sloupci jsou uvedeny aktuální IP adresy bran, jejichž hostitelem je nejnovější generace hardwaru. Pokud zřizujete nový server, doporučujeme, abyste otevřeli bránu firewall na straně klienta, abyste povolili odchozí přenosy pro IP adresy uvedené v tomto sloupci.
* **IP adresy brány (vyřazení z provozu):** V tomto sloupci jsou uvedené IP adresy bran, které jsou hostované na starší generaci hardwaru, který je teď vyřazený z provozu. Pokud zřizujete nový server, můžete tyto IP adresy ignorovat. Pokud máte existující server, pokračujte v zachování odchozího pravidla pro bránu firewall pro tyto IP adresy, protože jsme to ještě nemuseli vyřadit. Pokud jste pro tyto IP adresy vyřadíte pravidla brány firewall, můžete získat chyby připojení. Místo toho se očekává, že proaktivně přidáte nové IP adresy uvedené ve sloupci IP adresy brány do odchozího pravidla brány firewall, jakmile obdržíte oznámení pro vyřazení z provozu. Tím se zajistí, že se váš server migruje na nejnovější verzi brány, ale v připojení k vašemu serveru nedochází k výpadkům.
* **IP adresy brány (vyřazené z provozu):** V tomto sloupci jsou uvedené IP adresy okruhů brány, které jsou vyřazené z provozu a již nejsou v provozu. Tyto IP adresy můžete bezpečně odebrat z odchozího pravidla brány firewall. 


| **Název oblasti** | **IP adresy brány** |**IP adresy brány (vyřazení z provozu)** | **IP adresy brány (vyřazené z provozu)** |
|:----------------|:-------------------------|:-------------------------------------------|:------------------------------------------|
| Austrálie – střed| 20.36.105.0  | | |
| Central2 Austrálie     | 20.36.113.0  | | |
| Austrálie – východ | 13.75.149.87, 40.79.161.1     |  | |
| Austrálie – jihovýchod |191.239.192.109, 13.73.109.251   |  | |
| Brazílie – jih |191.233.201.8, 191.233.200.16    |  | 104.41.11.5|
| Střední Kanada |40.85.224.249  | | |
| Kanada – východ | 40.86.226.166    | | |
| USA – střed | 23.99.160.139, 52.182.136.37, 52.182.136.38 | 13.67.215.62 | |
| Čína – východ | 139.219.130.35    | | |
| Čína – východ 2 | 40.73.82.1  | | |
| Čína – sever | 139.219.15.17    | | |
| Čína – sever 2 | 40.73.50.0     | | |
| Východní Asie | 191.234.2.139, 52.175.33.150, 13.75.33.20, 13.75.33.21     | | |
| East US |40.71.8.203, 40.71.83.113 |40.121.158.30|191.238.6.43 |
| USA – východ 2 | 40.70.144.38, 52.167.105.38  | 52.177.185.181 | |
| Francie – střed | 40.79.137.0, 40.79.129.1  | | |
| Francie – jih | 40.79.177.0     | | |
| Německo – střed | 51.4.144.100     | | |
| Německo – sever východ | 51.5.144.179  | | |
| Indie – střed | 104.211.96.159     | | |
| Indie – jih | 104.211.224.146  | | |
| Indie – západ | 104.211.160.80    | | |
| Japonsko – východ | 40.79.192.23 | 13.78.61.196 | |
| Japonsko – západ | 191.238.68.11, 40.74.96.6, 40.74.96.7     | 104.214.148.156 | |
| Jižní Korea – střed | 52.231.17.13   | 52.231.32.42 | |
| Jižní Korea – jih | 52.231.145.3     | 52.231.200.86 | |
| USA – středosever | 52.162.104.35, 52.162.104.36    | 23.96.178.199 | |
| Severní Evropa | 52.138.224.6, 52.138.224.7  | 40.113.93.91 |191.235.193.75 |
| Jižní Afrika – sever  | 102.133.152.0    | | |
| Jižní Afrika – západ | 102.133.24.0   | | |
| Středojižní USA |104.214.16.39, 20.45.120.0  |13.66.62.124  |23.98.162.75 |
| Jihovýchodní Asie | 40.78.233.2, 23.98.80.12     | 104.43.15.0 | |
| Spojené arabské emiráty – střed | 20.37.72.64  | | |
| Spojené arabské emiráty sever | 65.52.248.0    | | |
| Spojené království – jih | 51.140.184.11   | | |
| Spojené království – západ | 51.141.8.11  | | |
| USA – středozápad | 13.78.145.25     | | |
| West Europe |13.69.105.208, 104.40.169.187 | 40.68.37.158 | 191.237.232.75 |
| USA – západ |13.86.216.212, 13.86.217.212 |104.42.238.205  | 23.99.34.75|
| Západní USA 2 | 13.66.226.202  | | |
||||

## <a name="connection-redirection"></a>Přesměrování připojení

Azure Database for MySQL podporuje další zásady připojení, **přesměrování**, která pomáhá snižovat latenci sítě mezi klientskými aplikacemi a servery MySQL. Po navázání počáteční relace protokolu TCP na Azure Database for MySQL server vrátí tato funkce server back-end uzel, který hostuje server MySQL, do klienta. Následně se všechny následné pakety nasměrují přímo na server a vynechá bránu. Jako tok paketů přímo na server, latence a propustnost vylepší výkon.

Tato funkce je podporovaná v Azure Database for MySQL serverech s verzemi modulu 5,6, 5,7 a 8,0.

Podpora pro přesměrování je dostupná v rozšíření PHP [mysqlnd_azure](https://github.com/microsoft/mysqlnd_azure) , vyvinuté společností Microsoft a je dostupná na [PECL](https://pecl.php.net/package/mysqlnd_azure). Další informace o tom, jak používat přesměrování ve svých aplikacích, najdete v článku [konfigurace přesměrování](./howto-redirection.md) .

> [!IMPORTANT]
> Podpora přesměrování [mysqlnd_azure](https://github.com/microsoft/mysqlnd_azure) rozšíření PHP je v současnosti ve verzi Preview.

## <a name="next-steps"></a>Další kroky

* [Vytváření a Správa Azure Database for MySQL pravidel brány firewall pomocí Azure Portal](./howto-manage-firewall-using-portal.md)
* [Vytvoření a Správa pravidel brány firewall Azure Database for MySQL pomocí rozhraní příkazového řádku Azure](./howto-manage-firewall-using-cli.md)
* [Konfigurace přesměrování pomocí Azure Database for MySQL](./howto-redirection.md)