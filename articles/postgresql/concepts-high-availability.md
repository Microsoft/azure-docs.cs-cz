---
title: Vysoká dostupnost – Azure Database for PostgreSQL – jeden server
description: Tento článek poskytuje informace o vysoké dostupnosti v Azure Database for PostgreSQL jednom serveru.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 5/6/2019
ms.openlocfilehash: 80229ff78c4570db583f1218d5d2f72da2dec388
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "74768567"
---
# <a name="high-availability-concepts-in-azure-database-for-postgresql---single-server"></a>Koncepty vysoké dostupnosti v Azure Database for PostgreSQL – jeden server
Služba Azure Database for PostgreSQL poskytuje zaručenou vysokou úroveň dostupnosti. Finančně zajištěná smlouva o úrovni služeb (SLA) je po všeobecné dostupnosti 99,99%. Při používání této služby nedochází k neprovozuschopnému použití.

## <a name="high-availability"></a>Vysoká dostupnost
Model vysoké dostupnosti (HA) vychází z vestavěných mechanismů převzetí služeb při selhání, když dojde k přerušení na úrovni uzlu. Přerušení na úrovni uzlu může vzniknout kvůli selhání hardwaru nebo v reakci na nasazení služby.

Změny provedené u Azure Database for PostgreSQL databázového serveru se projeví v kontextu transakce. Změny se zaznamenávají synchronně ve službě Azure Storage, když je transakce potvrzena. Pokud dojde k přerušení na úrovni uzlu, databázový server automaticky vytvoří nový uzel a připojí úložiště dat k novému uzlu. Všechna aktivní připojení jsou vyřazena a všechny transakce v transakci nejsou potvrzeny.

## <a name="application-retry-logic-is-essential"></a>Logika opakování aplikace je zásadní.
Je důležité, aby databázové aplikace PostgreSQL byly sestaveny tak, aby zjistily a opakovaly přerušená připojení a neúspěšné transakce. Když se aplikace opakuje, připojení aplikace se transparentně přesměruje na nově vytvořenou instanci, která převezme služby pro neúspěšnou instanci.

Interně v Azure se k přesměrování připojení k nové instanci používá brána. Po přerušení bude celý proces převzetí služeb při selhání obvykle trvat desítky sekund. Vzhledem k tomu, že je přesměrování zpracováváno interně bránou, externí připojovací řetězec pro klientské aplikace zůstane stejný.

## <a name="scaling-up-or-down"></a>Vertikální navýšení nebo snížení kapacity
Podobně jako Azure Database for PostgreSQL při horizontálním navýšení nebo snížení kapacity v modelu HA se vytvoří nová instance serveru se zadanou velikostí. Stávající úložiště dat je odpojené od původní instance a připojené k nové instanci.

Během operace škálování dojde k přerušení připojení k databázi. Klientské aplikace jsou odpojené a otevřené nepotvrzené transakce se zruší. Jakmile klientská aplikace znovu požádá o připojení nebo vytvoří nové připojení, brána nasměruje připojení k instanci nově nastavené velikosti. 

## <a name="next-steps"></a>Další kroky
- Další informace o [zpracování chyb s přechodným připojením](concepts-connectivity.md)
- Naučte [se replikovat data pomocí replik pro čtení](howto-read-replicas-portal.md) .
