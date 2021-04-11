---
title: Architektura připojení – Azure Database for MySQL
description: Popisuje architekturu připojení pro server Azure Database for MySQL.
author: Bashar-MSFT
ms.author: bahusse
ms.service: mysql
ms.topic: conceptual
ms.date: 2/11/2021
ms.openlocfilehash: b8ee1f22429c1002ba8c3db5c41f5a186cc59451
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "105046466"
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
| Německo – sever | 51.116.56.0 | |
| Německo – sever východ | 51.5.144.179  | | |
| Německo – středozápad | 51.116.152.0 | |
| Indie – střed | 104.211.96.159     | | |
| Indie – jih | 104.211.224.146  | | |
| Indie – západ | 104.211.160.80    | | |
| Japonsko – východ | 40.79.192.23, 40.79.184.8 | 13.78.61.196 | |
| Japonsko – západ | 191.238.68.11, 40.74.96.6, 40.74.96.7     | 104.214.148.156 | |
| Jižní Korea – střed | 52.231.17.13   | 52.231.32.42 | |
| Jižní Korea – jih | 52.231.145.3     | 52.231.200.86 | |
| USA – středosever | 52.162.104.35, 52.162.104.36    | 23.96.178.199 | |
| Severní Evropa | 52.138.224.6, 52.138.224.7  | 40.113.93.91 |191.235.193.75 |
| Jižní Afrika – sever  | 102.133.152.0    | | |
| Jižní Afrika – západ | 102.133.24.0   | | |
| Středojižní USA |104.214.16.39, 20.45.120.0  |13.66.62.124  |23.98.162.75 |
| Jihovýchodní Asie | 40.78.233.2, 23.98.80.12     | 104.43.15.0 | |
| Švýcarsko – sever | 51.107.56.0 ||
| Švýcarsko – západ | 51.107.152.0||
| Spojené arabské emiráty – střed | 20.37.72.64  | | |
| Spojené arabské emiráty sever | 65.52.248.0    | | |
| Spojené království – jih | 51.140.184.11   | | |
| Spojené království – západ | 51.141.8.11  | | |
| USA – středozápad | 13.78.145.25     | | |
| West Europe |13.69.105.208, 104.40.169.187 | 40.68.37.158 | 191.237.232.75 |
| USA – západ |13.86.216.212, 13.86.217.212 |104.42.238.205  | 23.99.34.75|
| Západní USA 2 | 13.66.136.192 | 13.66.226.202  | | 
||||

## <a name="connection-redirection"></a>Přesměrování připojení

Azure Database for MySQL podporuje další zásady připojení, **přesměrování**, která pomáhá snižovat latenci sítě mezi klientskými aplikacemi a servery MySQL. Po navázání přesměrování a po navázání počáteční relace protokolu TCP na Azure Database for MySQL server vrátí server back-end adresu uzlu hostujícího Server MySQL klientovi. Následně se všechny následné pakety nasměrují přímo na server a vynechá bránu. Jako tok paketů přímo na server, latence a propustnost vylepší výkon.

Tato funkce je podporovaná v Azure Database for MySQL serverech s verzemi modulu 5,6, 5,7 a 8,0.

Podpora pro přesměrování je dostupná v rozšíření PHP [mysqlnd_azure](https://github.com/microsoft/mysqlnd_azure) , vyvinuté společností Microsoft a je dostupná na [PECL](https://pecl.php.net/package/mysqlnd_azure). Další informace o tom, jak používat přesměrování ve svých aplikacích, najdete v článku [konfigurace přesměrování](./howto-redirection.md) .


> [!IMPORTANT]
> Podpora přesměrování [mysqlnd_azure](https://github.com/microsoft/mysqlnd_azure) rozšíření PHP je v současnosti ve verzi Preview.

## <a name="frequently-asked-questions"></a>Nejčastější dotazy

### <a name="what-you-need-to-know-about-this-planned-maintenance"></a>Co potřebujete znát o této plánované údržbě?
Jedná se pouze o změnu DNS, která umožňuje jejich transparentním klientům. I když se IP adresa pro plně kvalifikovaný název domény změnila na serveru DNS, místní mezipaměť DNS se aktualizuje během 5 minut a automaticky se provede operačními systémy. Po obnovení místních serverů DNS se všechna nová připojení připojí k nové IP adrese. všechna existující připojení budou zůstat připojená k staré IP adrese bez přerušení, dokud nebudou staré IP adresy plně vyřazeny. Před vyřazením z provozu bude předchozí IP adresa přibližně trvat tři až čtyři týdny. Proto by nemělo mít žádný vliv na klientské aplikace.

### <a name="what-are-we-decommissioning"></a>Co vyřadíme z provozu?
Vyřazeny se budou jenom uzly bran. Když se uživatelé připojí ke svým serverům, prvním zastavením připojení je uzel Brána, než se připojení přepošle na server. Vyřadíme z provozu staré okruhy brány (ne okruhy klientů, ve kterých je server spuštěný). Další informace najdete v [architektuře připojení](#connectivity-architecture) .

### <a name="how-can-you-validate-if-your-connections-are-going-to-old-gateway-nodes-or-new-gateway-nodes"></a>Jak se dá ověřit, jestli se vaše připojení budou stará o uzly brány nebo nové uzly brány?
Použijte například příkaz pro zadání názvu FQDN vašeho serveru  ``ping xxx.mysql.database.azure.com`` . Pokud je vrácená IP adresa jednou z IP adres uvedených v části IP adresy brány (vyřazení z provozu) v dokumentu výše, znamená to, že vaše připojení prochází starou bránou. Naopak, pokud je vrácená IP adresa jednou z IP adres uvedených v části IP adresy brány, znamená to, že vaše připojení prochází novou bránou.

Můžete také otestovat [PSPing](/sysinternals/downloads/psping) nebo TCPPing databázového serveru z klientské aplikace pomocí portu 3306 a zajistit, aby návratová IP adresa nebyla jednou z vyřazení IP adres.

### <a name="how-do-i-know-when-the-maintenance-is-over-and-will-i-get-another-notification-when-old-ip-addresses-are-decommissioned"></a>Návody vědět, kdy probíhá údržba, a zobrazí se další oznámení, když se staré IP adresy vyřadí z provozu?
Po zahájení práce údržby obdržíte e-mail s informací o tom, jak se vám bude informovat. Údržba může trvat až jeden měsíc v závislosti na počtu serverů, které potřebujeme migrovat v Al oblastech. Připravte prosím klienta, aby se připojil k databázovému serveru pomocí plně kvalifikovaného názvu domény, nebo použijte novou IP adresu z tabulky výše. 

### <a name="what-do-i-do-if-my-client-applications-are-still-connecting-to-old-gateway-server-"></a>Jak mám dělat, když se moje klientské aplikace stále připojují k původnímu serveru brány?
To znamená, že vaše aplikace se připojují k serveru pomocí statické IP adresy místo plně kvalifikovaného názvu domény. Zkontrolujte připojovací řetězce a nastavení sdružování připojení, nastavení AKS nebo dokonce i ve zdrojovém kódu.

### <a name="is-there-any-impact-for-my-application-connections"></a>Existují nějaké důsledky pro moje připojení aplikací?
Tato údržba je jenom změnou DNS, takže je pro klienta transparentní. Po obnovení mezipaměti DNS v klientovi (automaticky prováděné operačním systémem) se všechna nová připojení připojí k nové IP adrese a všechna existující připojení budou dál fungovat, dokud se stará IP adresa zcela nevyřadí z provozu, což obvykle několik týdnů později. A logika opakování není pro tento případ nutná, je ale dobré, že aplikace má nakonfigurovanou logiku opakování. Buď použijte plně kvalifikovaný název domény, abyste se připojili k databázovému serveru, nebo povolte seznam nové IP adresy brány v připojovacím řetězci aplikace.
Tato operace údržby neodstraní existující připojení. Vytvoří jenom nové požadavky na připojení, které budou jít na nový okruh brány.

### <a name="can-i-request-for-a-specific-time-window-for-the-maintenance"></a>Můžu pro údržbu požádat o konkrétní časové období? 
Protože by migrace měla být transparentní a nemá žádný vliv na připojení zákazníka, očekává se, že většina uživatelů nebude mít žádný problém. Zkontrolujte aplikaci proaktivně a ujistěte se, že buď použijete plně kvalifikovaný název domény pro připojení k databázovému serveru, nebo povolte seznam nové IP adresy brány v připojovacím řetězci aplikace.

### <a name="i-am-using-private-link-will-my-connections-get-affected"></a>Používám privátní propojení, bude moje připojení ovlivněno?
Ne, toto je vyřazení hardwaru brány z provozu a nemá žádný vztah k privátním linkám nebo privátním IP adresám, ovlivní jenom veřejné IP adresy uvedené v rámci vyřazení IP adres.



## <a name="next-steps"></a>Další kroky
* [Vytváření a Správa Azure Database for MySQL pravidel brány firewall pomocí Azure Portal](./howto-manage-firewall-using-portal.md)
* [Vytvoření a Správa pravidel brány firewall Azure Database for MySQL pomocí rozhraní příkazového řádku Azure](./howto-manage-firewall-using-cli.md)
* [Konfigurace přesměrování pomocí Azure Database for MySQL](./howto-redirection.md)