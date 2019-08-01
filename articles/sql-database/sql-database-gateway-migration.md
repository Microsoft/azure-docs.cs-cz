---
title: Oznámení o migraci brány pro Azure SQL Database z Gen2 do Gen3 | Microsoft Docs
description: Článek obsahuje oznámení uživatelům o migraci IP adres Azure SQL Database bran
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.topic: conceptual
author: rohitnayakmsft
ms.author: rohitna
ms.reviewer: vanto
ms.date: 07/01/2019
ms.openlocfilehash: 85691464684ff327c01a85bf357514f447564dd7
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/26/2019
ms.locfileid: "68568107"
---
# <a name="azure-sql-database-traffic-migration-to-newer-gateways"></a>Azure SQL Database migrace provozu na novější brány

Díky tomu, že se infrastruktura Azure zlepšuje, Microsoft pravidelně aktualizuje hardware, aby bylo zajištěno, že budeme poskytovat nejlepší možné prostředí pro zákazníky. V nadcházejících měsících plánujeme přidat brány postavené na novějších generacích hardwaru a vyřadit z provozu brány z provozu na starším hardwaru v některých oblastech.  

Zákazníci budou upozorňováni e-mailem a v Azure Portal předem o jakékoli změně bran, které jsou dostupné v jednotlivých oblastech. Nejaktuálnější informace se budou udržovat v tabulce [IP adres Azure SQL Database brány](sql-database-connectivity-architecture.md#azure-sql-database-gateway-ip-addresses) .

## <a name="impact-of-this-change"></a>Dopad této změny

První kolo vyřazení brány z provozu je naplánováno na 1. září 2019 v následujících oblastech:

- USA – západ
- Západní Evropa
- East US
- Střed USA
- Jihovýchodní Asie
- Střed USA – jih
- Severní Evropa
- Střed USA – sever
- Japonsko – západ
- Japonsko – východ
- Východní USA 2
- Východní Asie

Vyřazená IP adresa přestane přijímat přenosy a jakékoli nové pokusy o připojení budou směrovány do jedné z bran v dané oblasti.

Kde neuvidíte dopad této změny:

- U zákazníků, kteří používají přesměrování podle zásad připojení, se žádný dopad neprojeví.
- Připojení k SQL Database zevnitř v rámci Azure a používání značek služeb nebude mít vliv na.
- Připojení pomocí podporovaných verzí ovladače JDBC pro SQL Server se nijak neprojeví. Podporované verze JDBC najdete v tématu [Stažení ovladače Microsoft JDBC Driver for SQL Server](/sql/connect/jdbc/download-microsoft-jdbc-driver-for-sql-server).

## <a name="what-to-do-you-do-if-youre-affected"></a>Co dělat, pokud jste to ovlivnili

Doporučujeme, abyste povolili odchozí provoz na IP adresy pro všechny [Azure SQL Database IP adresy brány](sql-database-connectivity-architecture.md#azure-sql-database-gateway-ip-addresses) v oblasti na portu TCP 1433 a rozsahu portů 11000-11999 na zařízení brány firewall. Další informace o rozsahu portů najdete v tématu [zásady připojení](sql-database-connectivity-architecture.md#connection-policy).

Neúspěšné ověření certifikátu prostřednictvím připojení od aplikací pomocí ovladače Microsoft JDBC pod verzí 4,0. Nižší verze Microsoft JDBC spoléhají na běžný název (CN) v poli subjekt certifikátu. Zmírnění je zajistit, aby vlastnost hostNameInCertificate byla nastavena na hodnotu *. database.windows.net. Další informace o tom, jak nastavit vlastnost hostNameInCertificate, najdete v tématu [připojení pomocí šifrování SSL](/sql/connect/jdbc/connecting-with-ssl-encryption).

Pokud výše uvedené omezení nefunguje, zažádejte si do souboru žádost o podporu pro SQL Database pomocí následující adresy URL: https://aka.ms/getazuresupport

## <a name="next-steps"></a>Další postup

- Přečtěte si další informace o [architektuře připojení Azure SQL](sql-database-connectivity-architecture.md) .