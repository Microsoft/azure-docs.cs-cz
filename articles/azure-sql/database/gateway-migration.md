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
ms.openlocfilehash: 30e57736d0b0e40eb01573d6acca2c618dcf6ee3
ms.sourcegitcommit: 5a37753456bc2e152c3cb765b90dc7815c27a0a8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/04/2020
ms.locfileid: "87759695"
---
# <a name="azure-sql-database-traffic-migration-to-newer-gateways"></a>Azure SQL Database migrace provozu na novější brány
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Díky tomu, že se infrastruktura Azure zlepšuje, Microsoft pravidelně aktualizuje hardware, aby bylo zajištěno, že budeme poskytovat nejlepší možné prostředí pro zákazníky. V nadcházejících měsících plánujeme přidat brány postavené na novějších generacích hardwaru, migrovat provoz na ně a nakonec vyřadit brány z provozu na starší hardware v některých oblastech.  

Zákazníci budou upozorňováni e-mailem a v Azure Portal předem o jakékoli změně bran, které jsou dostupné v jednotlivých oblastech. Nejaktuálnější informace se budou udržovat v tabulce [IP adres Azure SQL Database brány](connectivity-architecture.md#gateway-ip-addresses) .

## <a name="status-updates"></a>Aktualizace stavu

# <a name="in-progress"></a>[Rozpracované](#tab/in-progress-ip)
### <a name="september-2020"></a>Září 2020

Nové brány SQL se přidávají do následujících oblastí. Tyto brány SQL začnou přijímat provoz zákazníků 10. září 2020:

- Středozápadní USA: 13.78.248.43 
- Jižní Afrika sever: 102.133.120.2  

Nové brány SQL se přidávají do následujících oblastí. Tyto brány SQL začnou přijímat provoz zákazníků dne 1. září 2020:

- Severní Evropa: 13.74.104.113 
- Západ USA 2:40.78.248.10 
- Západní Evropa: 52.236.184.163 
- Střed USA – jih: 20.45.121.1, 20.49.88.1 

Stávající brány SQL začnou přijímat přenosy v následujících oblastech. Tyto brány SQL začnou přijímat zákaznická data od 1. září 2020. :
- Japonsko – východ: 40.79.184.8, 40.79.192.5

### <a name="august-2020"></a>Srpen 2020

Nové brány SQL se přidávají do následujících oblastí:

- Austrálie – východ: 13.70.112.9
- Kanada – střed: 52.246.152.0, 20.38.144.1 
- Západní USA 2:40.78.240.8

Tyto brány SQL začnou přijímat provoz zákazníků 10. srpna 2020. 

# <a name="completed"></a>[Dokončeno](#tab/completed-ip)

Následující migrace brány jsou dokončené: 

### <a name="october-2019"></a>Říjen 2019
- Brazil South
- USA – západ
- West Europe
- East US
- Střední USA
- Jihovýchodní Asie
- Středojižní USA
- Severní Evropa
- USA – středosever
- Japonsko – západ
- Japan East
- USA – východ 2
- Východní Asie

---

## <a name="impact-of-this-change"></a>Dopad této změny

Migrace provozu může změnit veřejnou IP adresu, kterou DNS překládá pro vaši databázi v Azure SQL Database.
Může to mít vliv na to, jestli:

- Pevně zakóduje IP adresu pro konkrétní bránu v místní bráně firewall.
- Mít všechny podsítě používající Microsoft. SQL jako koncový bod služby, ale nemůžou komunikovat s IP adresami brány.
- Použít [redundantní konfiguraci zóny](high-availability-sla.md#zone-redundant-configuration) pro vaši databázi

Nebudete mít vliv na tyto akce:
 
- Přesměrování jako zásady připojení
- Připojení k SQL Database v rámci Azure a používání značek služeb
- Připojení pomocí podporovaných verzí ovladače JDBC pro SQL Server se nijak neprojeví. Podporované verze JDBC najdete v tématu [Stažení ovladače Microsoft JDBC Driver for SQL Server](/sql/connect/jdbc/download-microsoft-jdbc-driver-for-sql-server).

## <a name="what-to-do-you-do-if-youre-affected"></a>Co dělat, pokud jste to ovlivnili

Doporučujeme, abyste povolili odchozí přenosy na IP adresy pro všechny [IP adresy brány](connectivity-architecture.md#gateway-ip-addresses) v oblasti na portu TCP 1433 a rozsahu portů 11000-11999. Toto doporučení se vztahuje na klienty připojující se z místního prostředí a také na ty, které se připojují prostřednictvím koncových bodů služby. Další informace o rozsahu portů najdete v tématu [zásady připojení](connectivity-architecture.md#connection-policy).

Neúspěšné ověření certifikátu prostřednictvím připojení od aplikací pomocí ovladače Microsoft JDBC pod verzí 4,0. Nižší verze Microsoft JDBC spoléhají na běžný název (CN) v poli subjekt certifikátu. Zmírnění je zajistit, aby vlastnost hostNameInCertificate byla nastavena na hodnotu *. database.windows.net. Další informace o tom, jak nastavit vlastnost hostNameInCertificate, najdete v tématu [připojení pomocí šifrování](/sql/connect/jdbc/connecting-with-ssl-encryption).

Pokud výše uvedené omezení nefunguje, zaregistrujte žádost o podporu pro SQL Database nebo spravovanou instanci SQL pomocí následující adresy URL:https://aka.ms/getazuresupport

## <a name="next-steps"></a>Další kroky

- Přečtěte si další informace o [architektuře připojení Azure SQL](connectivity-architecture.md) .
