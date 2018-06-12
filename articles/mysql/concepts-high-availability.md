---
title: Koncepty vysoké dostupnosti v Azure Database pro databázi MySQL
description: Toto téma obsahuje informace o vysoké dostupnosti při použití Azure databáze pro databázi MySQL
services: mysql
author: jasonwhowell
ms.author: jasonh
manager: kfile
editor: jasonwhowell
ms.service: mysql
ms.topic: article
ms.date: 02/28/2018
ms.openlocfilehash: 90dc603c0ee520774bd22531c7136e0949f6cf90
ms.sourcegitcommit: 1b8665f1fff36a13af0cbc4c399c16f62e9884f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/11/2018
ms.locfileid: "35264176"
---
# <a name="high-availability-concepts-in-azure-database-for-mysql"></a>Koncepty vysoké dostupnosti v Azure Database pro databázi MySQL
Databáze Azure pro službu MySQL poskytuje zaručené vysokou dostupnost. Finančně zálohovány smlouvu o úrovni služeb (SLA) je 99,99 % při obecné dostupnosti. Není prakticky žádná aplikace výpadek při používání této služby.

## <a name="high-availability"></a>Vysoká dostupnost
Model vysokou dostupnost (HA) je založen na integrovaný mechanismus převzetí služeb při selhání, když dojde k přerušení úrovni uzlu. Přerušení úrovni uzlu mohlo dojít z důvodu selhání hardwaru nebo v reakci na nasazení služby.

Vždy změny provedené v databázi Azure pro server databáze MySQL dojít v kontextu transakce. Změny se zaznamenávají synchronně v úložišti Azure, když je transakce potvrzena. Pokud dojde k přerušení úrovni uzlu, serveru databáze automaticky vytvoří nový uzel a připojí ukládání dat do nového uzlu. Žádné aktivní připojení jsou vyřazen a jakékoli aktivních pořadových transakce nejsou potvrzeny.

## <a name="application-retry-logic-is-essential"></a>Logika opakovaných pokusů aplikací je nezbytné
Je důležité, MySQL databázové aplikace jsou postaveny ke zjišťování a opakujte vyřadit připojení a transakce se nezdařilo. Při opakování aplikace, je připojení aplikace transparentně přesměrován na nově vytvořená instance, který má u nezdařených instancí.

Interně v Azure, brána slouží k přesměrování připojení k nové instanci. Celý proces převzetí služeb při selhání po přerušení, obvykle trvá desítkami sekund. Vzhledem k tomu, že přesměrování interně zpracovává bránou, externí připojovací řetězec zůstává stejná pro klientské aplikace.

## <a name="scaling-up-or-down"></a>Škálování nahoru nebo dolů
Podobně jako u HA model, při změně měřítka Azure Database pro databázi MySQL nahoru nebo dolů, je vytvořena nová instance serveru s po zadanou velikost. Je stávající úložiště dat je odpojený od původní instance a připojena k nové instanci.

Během operace škálování dojde k přerušení připojení databáze. Klientské aplikace jsou odpojené a otevřete nepotvrzené transakce budou zrušeny. Jakmile klientská aplikace opakuje připojení nebo vytvoří nové připojení, přesměruje bránu připojení k nově velikostí instance. 

## <a name="next-steps"></a>Další postup
- Přehled služby najdete v tématu [Azure databáze MySQL přehled](overview.md)
