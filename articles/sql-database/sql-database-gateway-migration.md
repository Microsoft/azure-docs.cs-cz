---
title: Všimněte si migraci brány pro Azure SQL Database z Gen2 Gen3 | Dokumentace Microsoftu
description: Článek obsahuje oznámení uživatelům informace o migraci z Azure SQL Database bran IP adresy
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.topic: conceptual
author: rohitnayakmsft
ms.author: rohitna
ms.reviewer: vanto
manager: craigg
ms.date: 07/01/2019
ms.openlocfilehash: 5894579c62c524394c7fea044b96885f7c8e8204
ms.sourcegitcommit: 084630bb22ae4cf037794923a1ef602d84831c57
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/03/2019
ms.locfileid: "67538377"
---
# <a name="azure-sql-database-traffic-migration-to-newer-gateways"></a>Provoz migrace do Azure SQL Database k novější brány

Infrastruktura Azure zlepšuje, Microsoft bude pravidelně aktualizovat, ujistěte se, že budeme poskytovat nejlepší možné zákazníka hardware. V nadcházejících měsících budeme plánování přidání že brány založená na novější generacemi hardwaru a vyřadit z provozu brány založená na starší hardware v některých oblastech.  

Budeme zákazníky informovat prostřednictvím e-mailu a na webu Azure Portal ještě před začátkem jakékoli změny brány k dispozici v jednotlivých oblastech. Nejnovější informace budou zachovány v [IP adresy brány Azure SQL Database](sql-database-connectivity-architecture.md#azure-sql-database-gateway-ip-addresses) tabulky.

## <a name="impact-of-this-change"></a>Tato změna

První kolo vyřazení z provozu brány je naplánovaná 1. září 2019 v těchto oblastech:

- Západní USA
- Západní Evropa
- East US
- USA – střed
- Jihovýchodní Asie
- Středojižní USA
- Severní Evropa
- Středoseverní USA
- Japonsko – západ
- Japonsko – východ
- Východní USA 2
- Východní Asie

IP adresa vyřadit z provozu se ukončí přijímá provoz a jakékoli pokusy o připojení se budou směrovat do jednoho z brány v oblasti.

Kde nezobrazí tato změna:

- Zákazníci, kteří používají přesměrování, protože jejich zásady připojení nezobrazí žádný vliv.
- Připojení ke službě SQL Database z v Azure a použití značky služeb nebudou ovlivněny.
- Připojení bylo vytvořeno s použitím podporovaných verzích ovladač JDBC pro SQL Server se zobrazí žádný vliv. Podporované verze JDBC, naleznete v tématu [stáhnout ovladač Microsoft JDBC pro SQL Server](/sql/connect/jdbc/download-microsoft-jdbc-driver-for-sql-server).

## <a name="what-to-do-you-do-if-youre-affected"></a>Co dělat, můžete dělat, když jste vliv

Doporučujeme povolit odchozí přenosy na IP adresy pro všechny [IP adresy brány Azure SQL Database](sql-database-connectivity-architecture.md#azure-sql-database-gateway-ip-addresses) v oblasti na TCP port 1433 a port rozsahu 11000 11999 vašeho zařízení brány firewall. Další informace o rozsahy portů najdete v tématu [zásady připojení](sql-database-connectivity-architecture.md#connection-policy).

Připojení z aplikací s použitím ovladač Microsoft JDBC nižší než verze 4.0 se nemusí podařit ověření certifikátu. Nižší verze Microsoft JDBC Spolehněte se na běžný název (CN) v poli subjekt certifikátu. Omezení rizik je zajistit, že je nastavena hostNameInCertificate na *. database.windows.net. Další informace o tom, jak nastavit vlastnost hostNameInCertificate najdete v tématu [připojení pomocí šifrování SSL](/sql/connect/jdbc/connecting-with-ssl-encryption).

Pokud výše uvedené omezení rizik nebude fungovat, založte žádost o podporu pro SQL Database s použitím následující adresu URL: https://aka.ms/getazuresupport

## <a name="next-steps"></a>Další postup

- Další informace o [architektura připojení SQL Azure](sql-database-connectivity-architecture.md)