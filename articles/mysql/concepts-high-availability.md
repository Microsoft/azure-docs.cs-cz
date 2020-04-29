---
title: Vysoká dostupnost – Azure Database for MySQL
description: Toto téma poskytuje informace o vysoké dostupnosti při použití Azure Database for MySQL
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: a793de35ffff84009d362f005e599b4419f0763f
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "79532769"
---
# <a name="high-availability-concepts-in-azure-database-for-mysql"></a>Koncepty vysoké dostupnosti v Azure Database for MySQL
Služba Azure Database for MySQL poskytuje zaručenou vysokou úroveň dostupnosti. Finančně zajištěná smlouva o úrovni služeb (SLA) je po všeobecné dostupnosti 99,99%. Při používání této služby nedochází k neprovozuschopnému použití.

## <a name="high-availability"></a>Vysoká dostupnost
Model vysoké dostupnosti (HA) vychází z vestavěných mechanismů pro převzetí služeb při selhání, když dojde k přerušení na úrovni uzlu. Přerušení na úrovni uzlu může vzniknout kvůli selhání hardwaru nebo v reakci na nasazení služby.

Změny provedené u Azure Database for MySQL databázového serveru se projeví v kontextu transakce. Změny se zaznamenávají synchronně ve službě Azure Storage, když je transakce potvrzena. Pokud dojde k přerušení na úrovni uzlu, databázový server automaticky vytvoří nový uzel a připojí úložiště dat k novému uzlu. Všechna aktivní připojení jsou vyřazena a všechny transakce v transakci nejsou potvrzeny.

## <a name="application-retry-logic-is-essential"></a>Logika opakování aplikace je zásadní.
Je důležité, aby databázové aplikace MySQL byly vybudovány k detekci a opakovanému přerušení připojení a neúspěšných transakcí. Když se aplikace opakuje, připojení aplikace se transparentně přesměruje na nově vytvořenou instanci, která převezme služby pro neúspěšnou instanci.

Interně v Azure se k přesměrování připojení k nové instanci používá brána. Po přerušení bude celý proces převzetí služeb při selhání obvykle trvat desítky sekund. Vzhledem k tomu, že je přesměrování zpracováváno interně bránou, externí připojovací řetězec pro klientské aplikace zůstane stejný.

## <a name="scaling-up-or-down"></a>Vertikální navýšení nebo snížení kapacity
Podobně jako Azure Database for MySQL při horizontálním navýšení nebo snížení kapacity v modelu HA se vytvoří nová instance serveru se zadanou velikostí. Stávající úložiště dat je odpojené od původní instance a připojené k nové instanci.

Během operace škálování dojde k přerušení připojení k databázi. Klientské aplikace jsou odpojené a otevřené nepotvrzené transakce se zruší. Jakmile klientská aplikace znovu požádá o připojení nebo vytvoří nové připojení, brána nasměruje připojení k instanci nově nastavené velikosti. 

## <a name="next-steps"></a>Další kroky
- Další informace o [zpracování chyb s přechodným připojením](concepts-connectivity.md)
- Naučte [se replikovat data pomocí replik pro čtení](howto-read-replicas-portal.md) .
