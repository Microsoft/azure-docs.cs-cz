---
title: Koncepty vysoké dostupnosti ve službě Azure Database for PostgreSQL – jeden Server
description: Tento článek obsahuje informace o vysoké dostupnosti při používání Azure Database for PostgreSQL – jeden Server.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 5/6/2019
ms.openlocfilehash: f54c83099957b4d8795c4049be52d70e8a0e2a61
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "65073440"
---
# <a name="high-availability-concepts-in-azure-database-for-postgresql---single-server"></a>Koncepty vysoké dostupnosti ve službě Azure Database for PostgreSQL – jeden Server
Služba Azure Database for PostgreSQL poskytuje zaručenou vysokou dostupnost. Je žádnou finančně zajištěnou smlouvu o úrovni služeb (SLA) 99,99 % při všeobecné dostupnosti. Neexistuje žádná aplikace prakticky časové prodlevy při používání této služby.

## <a name="high-availability"></a>Vysoká dostupnost
Model vysoké dostupnosti (HA) je založen na převzetí služeb při selhání vestavěné mechanismy, když dojde k přerušení úrovni uzlu. Přerušení úrovni uzlu mohlo dojít z důvodu selhání hardwaru nebo v reakci na nasazení služby.

Po celou dobu změny provedené v Azure Database for PostgreSQL databázový server dochází v kontextu transakce. Změny se zaznamenávají synchronně ve službě Azure storage, když je transakce potvrzena. Pokud dojde k přerušení úrovni uzlu, serveru databáze automaticky vytvoří nový uzel a připojí úložiště dat do nového uzlu. Každé aktivní propojení se zahodí a nejsou potvrzeny všechny probíhající transakce.

## <a name="application-retry-logic-is-essential"></a>Je základní aplikace logiky opakování
Je důležité, že PostgreSQL databáze aplikace se vytvářejí ke zjišťování a zkuste to znovu přerušení připojení a transakce se nezdařilo. Když aplikace opakování, připojení vaší aplikace transparentně přesměrují na nově vytvořená instance, která má pro chybné instance.

Interně v Azure, brány slouží k přesměrování připojení k nové instanci. Celý proces převzetí služeb při selhání při přerušení, obvykle trvá desítky vteřin. Protože přesměrování interně zpracována třídou brány, externí připojovací řetězec zůstává pro klientské aplikace.

## <a name="scaling-up-or-down"></a>Škálování směrem nahoru nebo dolů
Podobně jako u modelu vysokou dostupnost, když Azure Database for PostgreSQL je vertikálně navýšit nebo snížit, je vytvořena nová instance serveru s zadané velikosti. Je stávající úložiště dat je odpojena od původní instance a připojena k nové instanci.

Během operace škálování dojde k přerušení připojení databáze. Nejste připojení klientských aplikací a otevřít nepotvrzené transakce se zrušila. Jakmile se klientská aplikace opakuje připojení, nebo vytvoří nové připojení, brány směruje připojení na nově velikosti instance. 

## <a name="next-steps"></a>Další postup
- Další informace o [zpracování připojení přechodných chyb](concepts-connectivity.md)
- Zjistěte, jak [replikujte svá data pomocí repliky pro čtení](howto-read-replicas-portal.md)
