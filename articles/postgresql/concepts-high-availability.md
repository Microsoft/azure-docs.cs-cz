---
title: Vysoká dostupnost – databáze Azure pro PostgreSQL – jeden server
description: Tento článek obsahuje informace o vysoké dostupnosti v Azure Database for PostgreSQL - Single Server.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 5/6/2019
ms.openlocfilehash: 80229ff78c4570db583f1218d5d2f72da2dec388
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74768567"
---
# <a name="high-availability-concepts-in-azure-database-for-postgresql---single-server"></a>Koncepty s vysokou dostupností v Azure Database for PostgreSQL – jeden server
Služba Azure Database for PostgreSQL poskytuje garantovanou vysokou úroveň dostupnosti. Smlouva o úrovni služeb (SLA) je 99,99 % při všeobecné dostupnosti. Při používání této služby prakticky neexistuje žádný prostoje aplikace.

## <a name="high-availability"></a>Vysoká dostupnost
Model vysoké dostupnosti (HA) je založen na integrované mechanismy převzetí služeb při selhání, když dojde k přerušení na úrovni uzlu. Přerušení na úrovni uzlu může dojít z důvodu selhání hardwaru nebo v reakci na nasazení služby.

Změny provedené v databázi Azure database pro databázový server PostgreSQL dojít v kontextu transakce. Změny se zaznamenávají synchronně v úložišti Azure, když je transakce potvrzena. Dojde-li k přerušení na úrovni uzlu, databázový server automaticky vytvoří nový uzel a připojí úložiště dat k novému uzlu. Všechna aktivní připojení jsou vynechány a všechny transakce v letu nejsou potvrzeny.

## <a name="application-retry-logic-is-essential"></a>Logika opakování aplikace je nezbytná
Je důležité, aby databázové aplikace PostgreSQL byly vytvořeny pro detekci a opakování vyřazených připojení a neúspěšných transakcí. Při opakování aplikace je připojení aplikace transparentně přesměrováno na nově vytvořenou instanci, která převezme pro instanci se nezdařilo.

Interně v Azure se brána používá k přesměrování připojení k nové instanci. Po přerušení celý proces převzetí služeb při selhání obvykle trvá desítky sekund. Vzhledem k tomu, že přesměrování je zpracováno interně bránou, externí připojovací řetězec zůstává stejný pro klientské aplikace.

## <a name="scaling-up-or-down"></a>Škálování nahoru nebo dolů
Podobně jako model HA při škálování databáze Azure pro PostgreSQL nahoru nebo dolů, vytvoří se nová instance serveru se zadanou velikostí. Existující úložiště dat je odpojeno od původní instance a připojeno k nové instanci.

Během operace škálování dojde k přerušení připojení databáze. Klientské aplikace jsou odpojeny a otevřené nepotvrzené transakce jsou zrušeny. Jakmile klientská aplikace znovu pokusí o připojení nebo vytvoří nové připojení, brána přesměruje připojení k nově velké instanci. 

## <a name="next-steps"></a>Další kroky
- Informace o [zpracování přechodných chyb připojení](concepts-connectivity.md)
- Přečtěte si, jak [replikovat data pomocí replik pro čtení.](howto-read-replicas-portal.md)
