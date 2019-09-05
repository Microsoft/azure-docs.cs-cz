---
title: Upozornění migrace provozu brány na Azure SQL Database | Microsoft Docs
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
ms.openlocfilehash: 2e578c6bfdfb25eaba3d0c692ff33aa5b0572669
ms.sourcegitcommit: f176e5bb926476ec8f9e2a2829bda48d510fbed7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/04/2019
ms.locfileid: "70306004"
---
# <a name="azure-sql-database-traffic-migration-to-newer-gateways"></a>Azure SQL Database migrace provozu na novější brány

Díky tomu, že se infrastruktura Azure zlepšuje, Microsoft pravidelně aktualizuje hardware, aby bylo zajištěno, že budeme poskytovat nejlepší možné prostředí pro zákazníky. V nadcházejících měsících plánujeme přidat brány postavené na novějších generacích hardwaru, migrovat provoz na ně a nakonec vyřadit brány z provozu na starší hardware v některých oblastech.  

Zákazníci budou upozorňováni e-mailem a v Azure Portal předem o jakékoli změně bran, které jsou dostupné v jednotlivých oblastech. Nejaktuálnější informace se budou udržovat v tabulce [IP adres Azure SQL Database brány](sql-database-connectivity-architecture.md#azure-sql-database-gateway-ip-addresses) .

## <a name="impact-of-this-change"></a>Dopad této změny

První kolo migrace provozu na novější brány se plánuje na **14. října 2019** v následujících oblastech:
- Brazílie – jih
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

Migrace provozu změní veřejnou IP adresu, kterou služba DNS pro váš SQL Database vyřeší.
Bude to mít vliv, pokud máte následující:
- Pevně zakóduje IP adresu pro konkrétní bránu v místní bráně firewall.
- Všechny podsítě používající Microsoft. SQL jako koncový bod služby, ale nemůžou komunikovat s IP adresami brány.

Nebudete mít vliv na tyto akce:
- Přesměrování jako zásady připojení
- Připojení k SQL Database v rámci Azure a používání značek služeb
- Připojení pomocí podporovaných verzí ovladače JDBC pro SQL Server se nijak neprojeví. Podporované verze JDBC najdete v tématu [Stažení ovladače Microsoft JDBC Driver for SQL Server](/sql/connect/jdbc/download-microsoft-jdbc-driver-for-sql-server).

## <a name="what-to-do-you-do-if-youre-affected"></a>Co dělat, pokud jste to ovlivnili

Doporučujeme, abyste povolili odchozí provoz na IP adresy pro všechny [Azure SQL Database IP adresy brány](sql-database-connectivity-architecture.md#azure-sql-database-gateway-ip-addresses) v oblasti na portu TCP 1433 a rozsahu portů 11000-11999 na zařízení brány firewall. Další informace o rozsahu portů najdete v tématu [zásady připojení](sql-database-connectivity-architecture.md#connection-policy).

Neúspěšné ověření certifikátu prostřednictvím připojení od aplikací pomocí ovladače Microsoft JDBC pod verzí 4,0. Nižší verze Microsoft JDBC spoléhají na běžný název (CN) v poli subjekt certifikátu. Zmírnění je zajistit, aby vlastnost hostNameInCertificate byla nastavena na hodnotu *. database.windows.net. Další informace o tom, jak nastavit vlastnost hostNameInCertificate, najdete v tématu [připojení pomocí šifrování SSL](/sql/connect/jdbc/connecting-with-ssl-encryption).

Pokud výše uvedené omezení nefunguje, zažádejte si do souboru žádost o podporu pro SQL Database pomocí následující adresy URL: https://aka.ms/getazuresupport

## <a name="next-steps"></a>Další kroky

- Přečtěte si další informace o [architektuře připojení Azure SQL](sql-database-connectivity-architecture.md) .