---
title: Koncepty vysoké dostupnosti ve službě Azure Database for MySQL
description: Toto téma obsahuje informace o vysoké dostupnosti při používání Azure Database for MySQL
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 02/01/2019
ms.openlocfilehash: 055727695bfa1ce8a6bb160a7e071c2a161afb3b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "60837824"
---
# <a name="high-availability-concepts-in-azure-database-for-mysql"></a>Koncepty vysoké dostupnosti ve službě Azure Database for MySQL
Služba Azure Database for MySQL poskytuje zaručenou vysokou dostupnost. Je žádnou finančně zajištěnou smlouvu o úrovni služeb (SLA) 99,99 % při všeobecné dostupnosti. Neexistuje žádná aplikace prakticky časové prodlevy při používání této služby.

## <a name="high-availability"></a>Vysoká dostupnost
Model vysoké dostupnosti (HA) je založen na integrovaný mechanismus převzetí služeb při selhání, když dojde k přerušení úrovni uzlu. Přerušení úrovni uzlu mohlo dojít z důvodu selhání hardwaru nebo v reakci na nasazení služby.

Po celou dobu změny provedené serveru Azure Database for MySQL database dochází v kontextu transakce. Změny se zaznamenávají synchronně ve službě Azure storage, když je transakce potvrzena. Pokud dojde k přerušení úrovni uzlu, serveru databáze automaticky vytvoří nový uzel a připojí úložiště dat do nového uzlu. Každé aktivní propojení se zahodí a nejsou potvrzeny všechny probíhající transakce.

## <a name="application-retry-logic-is-essential"></a>Je základní aplikace logiky opakování
Je důležité, že MySQL database aplikace se vytvářejí ke zjišťování a zkuste to znovu přerušení připojení a transakce se nezdařilo. Když aplikace opakování, připojení vaší aplikace transparentně přesměrují na nově vytvořená instance, která má pro chybné instance.

Interně v Azure, brány slouží k přesměrování připojení k nové instanci. Celý proces převzetí služeb při selhání při přerušení, obvykle trvá desítky vteřin. Protože přesměrování interně zpracována třídou brány, externí připojovací řetězec zůstává pro klientské aplikace.

## <a name="scaling-up-or-down"></a>Škálování směrem nahoru nebo dolů
Podobně jako u modelu vysokou dostupnost, když Azure Database for MySQL je vertikálně navýšit nebo snížit, je vytvořena nová instance serveru s zadané velikosti. Je stávající úložiště dat je odpojena od původní instance a připojena k nové instanci.

Během operace škálování dojde k přerušení připojení databáze. Nejste připojení klientských aplikací a otevřít nepotvrzené transakce se zrušila. Jakmile se klientská aplikace opakuje připojení, nebo vytvoří nové připojení, brány směruje připojení na nově velikosti instance. 

## <a name="next-steps"></a>Další postup
- Další informace o [zpracování připojení přechodných chyb](concepts-connectivity.md)
- Zjistěte, jak [replikujte svá data pomocí repliky pro čtení](howto-read-replicas-portal.md)
