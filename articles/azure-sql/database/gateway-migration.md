---
title: Upozornění migrace provozu brány
description: Článek obsahuje oznámení uživatelům o migraci IP adres Azure SQL Database brány.
services: sql-database
ms.service: sql-db-mi
ms.subservice: service
ms.custom: sqldbrb=1
ms.topic: conceptual
author: rohitnayakmsft
ms.author: rohitna
ms.reviewer: vanto
ms.date: 07/01/2019
ms.openlocfilehash: eab1ebb3bf66b28bae80fbf16eaf24dbbc63bfb0
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/03/2021
ms.locfileid: "101690528"
---
# <a name="azure-sql-database-traffic-migration-to-newer-gateways"></a>Azure SQL Database migrace provozu na novější brány
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Díky tomu, že se infrastruktura Azure zlepšuje, Microsoft pravidelně aktualizuje hardware, aby bylo zajištěno, že budeme poskytovat nejlepší možné prostředí pro zákazníky. V nadcházejících měsících plánujeme přidat brány postavené na novějších generacích hardwaru, migrovat provoz na ně a nakonec vyřadit brány z provozu na starší hardware v některých oblastech.  

Zákazníkům se v rámci oznámení o stavu služeb předem oznámí jakákoli změna bran dostupných v jednotlivých oblastech. Zákazníci můžou [použít Azure Portal k nastavení upozornění protokolu aktivit](../../service-health/alerts-activity-log-service-notifications-portal.md).

Nejaktuálnější informace se budou udržovat v tabulce [IP adres Azure SQL Database brány](connectivity-architecture.md#gateway-ip-addresses) .

## <a name="status-updates"></a>Aktualizace stavu

# <a name="in-progress"></a>[Rozpracované](#tab/in-progress-ip)
## <a name="march-2021"></a>Březen 2021
Probíhá deaktivace následujících bran SQL v několika oblastech:

- Brazílie – jih: 104.41.11.5
- Východní Asie: 191.234.2.139
- Východní USA: 191.238.6.43
- Japonsko – východ: 191.237.240.43
- Japonsko – západ: 191.238.68.11
- Severní Evropa: 191.235.193.75
- Střed USA – jih: 23.98.162.75
- Jihovýchodní Asie: 23.100.117.95
- Západní Evropa: 191.237.232.75
- Západní USA: 23.99.34.75

Nepředpokládá se žádný dopad na zákazníky, protože tyto brány (spuštěné na starším hardwaru) nesměrují provoz zákazníků. Po 15. březnu 2021 se IP adresy těchto bran deaktivují.

## <a name="february-2021"></a>Únor 2021
Nové brány SQL se přidávají do následujících oblastí:

- Střed USA: 13.89.169.20

Tyto brány SQL začnou přijímat zákaznická data od 28. února 2021.

## <a name="january-2021"></a>Leden 2021
Nové brány SQL se přidávají do následujících oblastí:

- Austrálie – střed: 20.36.104.6, 20.36.104.7 
- Austrálie – střed 2:20.36.112.6 
- Brazílie – jih: 191.234.144.16, 191.234.152.3 
- Kanada – východ: 40.69.105.9, 40.69.105.10
- Indie – střed: 104.211.86.30, 104.211.86.31 
- Východní Asie: 13.75.32.14 
- Francie – střed: 40.79.137.8, 40.79.145.12 
- Francie – jih: 40.79.177.10, 40.79.177.12
- Korea – střed: 52.231.17.22, 52.231.17.23
- Indie – západ: 104.211.144.4

Tyto brány SQL začnou přijímat provoz zákazníků 31. ledna 2021.

# <a name="completed"></a>[Dokončeno](#tab/completed-ip)
Následující migrace brány jsou dokončené: 

### <a name="october-2020"></a>Říjen 2020

Nové brány SQL se přidávají do následujících oblastí:

- Německo – středozápad: 51.116.240.0, 51.116.248.0

Tyto brány SQL začnou přijímat provoz zákazníků 12. října 2020. 

### <a name="september-2020"></a>Září 2020
Nové brány SQL se přidávají do následujících oblastí. Tyto brány SQL začnou přijímat provoz zákazníků 15. **září 2020**:

- Austrálie – jihovýchod: 13.77.48.10
- Kanada – východ: 40.86.226.166, 52.242.30.154
- Velká Británie – jih: 51.140.184.11, 51.105.64.0

Stávající brány SQL začnou přijímat přenosy v následujících oblastech. Tyto brány SQL začnou přijímat provoz zákazníků 15. **září 2020** :

- Austrálie – jihovýchod: 191.239.192.109 a 13.73.109.251
- Střed USA: 13.67.215.62, 52.182.137.15, 23.99.160.139, 104.208.16.96 a 104.208.21.1
- Východní Asie: 191.234.2.139, 52.175.33.150 a 13.75.32.4
- Východní USA: 40.121.158.30, 40.79.153.12, 191.238.6.43 a 40.78.225.32
- Východní USA 2:40.79.84.180, 52.177.185.181, 52.167.104.0, 191.239.224.107 a 104.208.150.3
- Francie – střed: 40.79.137.0 a 40.79.129.1
- Japonsko – západ: 104.214.148.156, 40.74.100.192, 191.238.68.11 a 40.74.97.10
- Střed USA – sever: 23.96.178.199, 23.98.55.75 a 52.162.104.33
- Jihovýchodní Asie: 104.43.15.0, 23.100.117.95 a 40.78.232.3
- Západní USA: 104.42.238.205, 23.99.34.75 a 13.86.216.196

Nové brány SQL se přidávají do následujících oblastí. Tyto brány SQL začnou přijímat provoz zákazníků 10. **září 2020**:

- Středozápadní USA: 13.78.248.43 
- Jižní Afrika sever: 102.133.120.2  

Nové brány SQL se přidávají do následujících oblastí. Tyto brány SQL začnou přijímat provoz zákazníků dne **1. září 2020**:

- Severní Evropa: 13.74.104.113 
- Západ USA 2:40.78.248.10 
- Západní Evropa: 52.236.184.163 
- Střed USA – jih: 20.45.121.1, 20.49.88.1 

Stávající brány SQL začnou přijímat přenosy v následujících oblastech. Tyto brány SQL začnou přijímat provoz zákazníků dne **1. září 2020**:
- Japonsko – východ: 40.79.184.8, 40.79.192.5


### <a name="august-2020"></a>Srpen 2020

Nové brány SQL se přidávají do následujících oblastí:

- Austrálie – východ: 13.70.112.9
- Kanada – střed: 52.246.152.0, 20.38.144.1 
- Západní USA 2:40.78.240.8

Tyto brány SQL začnou přijímat provoz zákazníků 10. srpna 2020. 

### <a name="october-2019"></a>Říjen 2019
- Brazílie – jih
- USA – západ
- West Europe
- East US
- USA – střed
- Jihovýchodní Asie
- Středojižní USA
- Severní Evropa
- USA – středosever
- Japonsko – západ
- Japonsko – východ
- USA – východ 2
- Východní Asie

---

## <a name="impact-of-this-change"></a>Dopad této změny

Migrace provozu může změnit veřejnou IP adresu, kterou DNS překládá pro vaši databázi v Azure SQL Database.
Může to mít vliv na to, jestli:

- Pevně zakóduje IP adresu pro konkrétní bránu v místní bráně firewall.
- Mít všechny podsítě používající Microsoft. SQL jako koncový bod služby, ale nemůžou komunikovat s IP adresami brány.
- Použít [zónu redundantní konfigurace pro vrstvu pro obecné účely](high-availability-sla.md#general-purpose-service-tier-zone-redundant-availability-preview)
- Použití [nadbytečné konfigurace zóny pro klíčové úrovně premium & pro firmy](high-availability-sla.md#premium-and-business-critical-service-tier-zone-redundant-availability)

Nebudete mít vliv na tyto akce:
 
- Přesměrování jako zásady připojení
- Připojení k SQL Database v rámci Azure a používání značek služeb
- Připojení pomocí podporovaných verzí ovladače JDBC pro SQL Server se nijak neprojeví. Podporované verze JDBC najdete v tématu [Stažení ovladače Microsoft JDBC Driver for SQL Server](/sql/connect/jdbc/download-microsoft-jdbc-driver-for-sql-server).

## <a name="what-to-do-you-do-if-youre-affected"></a>Co dělat, pokud jste to ovlivnili

Doporučujeme, abyste povolili odchozí přenosy na IP adresy pro všechny [IP adresy brány](connectivity-architecture.md#gateway-ip-addresses) v oblasti na portu TCP 1433 a rozsahu portů 11000-11999. Toto doporučení se vztahuje na klienty připojující se z místního prostředí a také na ty, které se připojují prostřednictvím koncových bodů služby. Další informace o rozsahu portů najdete v tématu [zásady připojení](connectivity-architecture.md#connection-policy).

Neúspěšné ověření certifikátu prostřednictvím připojení od aplikací pomocí ovladače Microsoft JDBC pod verzí 4,0. Nižší verze Microsoft JDBC spoléhají na běžný název (CN) v poli subjekt certifikátu. Zmírnění je zajistit, aby vlastnost hostNameInCertificate byla nastavena na hodnotu *. database.windows.net. Další informace o tom, jak nastavit vlastnost hostNameInCertificate, najdete v tématu [připojení pomocí šifrování](/sql/connect/jdbc/connecting-with-ssl-encryption).

Pokud výše uvedené omezení nefunguje, zaregistrujte žádost o podporu pro SQL Database nebo spravovanou instanci SQL pomocí následující adresy URL: https://aka.ms/getazuresupport

## <a name="next-steps"></a>Další kroky

- Přečtěte si další informace o [architektuře připojení Azure SQL](connectivity-architecture.md) .