---
title: Oznámení o migraci provozu brány
description: Článek poskytuje uživatelům oznámení o migraci IP adres Azure SQL Database Gateways
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.topic: conceptual
author: rohitnayakmsft
ms.author: rohitna
ms.reviewer: vanto
ms.date: 07/01/2019
ms.openlocfilehash: 9e3c33bb7493f07d9fbf19710f21d0114e7abec8
ms.sourcegitcommit: 441db70765ff9042db87c60f4aa3c51df2afae2d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "80757062"
---
# <a name="azure-sql-database-traffic-migration-to-newer-gateways"></a>Migrace provozu databáze Azure SQL na novější brány

Jak se zlepšuje infrastruktura Azure, Microsoft bude pravidelně aktualizovat hardware, abychom zajistili, že poskytneme co nejlepší zákaznickou zkušenost. V nadcházejících měsících plánujeme přidat brány postavené na novějších generacích hardwaru, migrovat provoz na ně a nakonec vyřazení bran postavených na starším hardwaru v některých oblastech.  

Zákazníci budou upozorněni e-mailem a na webu Azure portal s hodně předstihem na jakékoli změny bran dostupných v každé oblasti. Nejaktuálnější informace budou udržovány v tabulce [IP adres brány Azure SQL Database.](sql-database-connectivity-architecture.md#azure-sql-database-gateway-ip-addresses)

## <a name="impact-of-this-change"></a>Dopad této změny

První kolo migrace provozu na novější brány je naplánováno na **14.**
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

Migrace provozu změní veřejnou IP adresu, kterou služba DNS řeší pro databázi SQL.
Budete ovlivněni, pokud máte:
- Pevný kód IP adresy pro konkrétní bránu v místní bráně firewall
- Všechny podsítě používající Microsoft.SQL jako koncový bod služby, ale nemohou komunikovat s IP adresami brány

Nebudete mít vliv, pokud máte:
- Přesměrování jako zásady připojení
- Připojení k databázi SQL z Azure a pomocí značek služeb
- Připojení provedená pomocí podporovaných verzí ovladače JDBC pro sql server neuvidí žádný dopad. Podporované verze jdbc naleznete [v tématu Stažení ovladače Microsoft JDBC pro SQL Server](/sql/connect/jdbc/download-microsoft-jdbc-driver-for-sql-server).

## <a name="what-to-do-you-do-if-youre-affected"></a>Co dělat, když se vás to týká

Doporučujeme povolit odchozí provoz na IP adresy pro všechny [IP adresy brány Azure SQL Database](sql-database-connectivity-architecture.md#azure-sql-database-gateway-ip-addresses) v oblasti na portu TCP 1433 a rozsah portů 11000-11999. Toto doporučení se vztahuje na klienty, kteří se připojují z místního prostředí, a také pro klienty, kteří se připojují prostřednictvím koncových bodů služby. Další informace o rozsahu portů naleznete v tématu [Zásady připojení](sql-database-connectivity-architecture.md#connection-policy).

Připojení z aplikací používajících ovladač Microsoft JDBC pod verzí 4.0 mohou nemít ověřování certifikátů. Nižší verze jdbc společnosti Microsoft spoléhají na běžný název (CN) v poli Předmět certifikátu. Zmírnění je zajistit, že hostNameInCertificate vlastnost je nastavena na *.database.windows.net. Další informace o nastavení vlastnosti hostNameInCertificate naleznete v [tématu Připojení pomocí šifrování](/sql/connect/jdbc/connecting-with-ssl-encryption).

Pokud výše uvedené zmírnění nefunguje, soubor žádost o podporu pro databázi SQL pomocí následující adresy URL:https://aka.ms/getazuresupport

## <a name="next-steps"></a>Další kroky

- Další informace o [architektuře azure sql připojení](sql-database-connectivity-architecture.md)
