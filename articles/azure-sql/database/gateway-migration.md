---
title: Upozornění migrace provozu brány
description: Článek obsahuje oznámení uživatelům o migraci IP adres Azure SQL Database bran
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: sqldbrb=1 
ms.topic: conceptual
author: rohitnayakmsft
ms.author: rohitna
ms.reviewer: vanto
ms.date: 07/01/2019
ms.openlocfilehash: f5e45a4625b1cf9422f7ef7e10e9080a7878172d
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/27/2020
ms.locfileid: "84043391"
---
# <a name="azure-sql-database-traffic-migration-to-newer-gateways"></a>Azure SQL Database migrace provozu na novější brány
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Díky tomu, že se infrastruktura Azure zlepšuje, Microsoft pravidelně aktualizuje hardware, aby bylo zajištěno, že budeme poskytovat nejlepší možné prostředí pro zákazníky. V nadcházejících měsících plánujeme přidat brány postavené na novějších generacích hardwaru, migrovat provoz na ně a nakonec vyřadit brány z provozu na starší hardware v některých oblastech.  

Zákazníci budou upozorňováni e-mailem a v Azure Portal předem o jakékoli změně bran, které jsou dostupné v jednotlivých oblastech. Nejaktuálnější informace se budou udržovat v tabulce [IP adres Azure SQL Database brány](connectivity-architecture.md#gateway-ip-addresses) .

## <a name="impact-of-this-change"></a>Dopad této změny

První kolo migrace provozu na novější brány se plánuje na **14. října 2019** v následujících oblastech:

- Brazílie – jih
- USA – západ
- Západní Evropa
- USA – východ
- USA – střed
- Jihovýchodní Asie
- USA – středojih
- Severní Evropa
- USA – středosever
- Japonsko – západ
- Japonsko – východ
- USA – východ 2
- Východní Asie

Migrace provozu změní veřejnou IP adresu, kterou služba DNS pro váš SQL Database vyřeší.
Bude to mít vliv, pokud máte následující:

- Pevně zakóduje IP adresu pro konkrétní bránu v místní bráně firewall.
- Všechny podsítě používající Microsoft. SQL jako koncový bod služby, ale nemůžou komunikovat s IP adresami brány.

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
