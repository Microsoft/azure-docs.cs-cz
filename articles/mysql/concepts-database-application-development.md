---
title: Vývoj aplikací – Azure Database for MySQL
description: Zavádí aspekty návrhu, které by měl vývojář dodržovat při psaní kódu aplikace pro připojení k Azure Database for MySQL.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: 02ce6b00b6555f849d162b9f3b381c0ab358d712
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79532837"
---
# <a name="application-development-overview-for-azure-database-for-mysql"></a>Přehled vývoje aplikací pro Azure Database for MySQL 
Tento článek popisuje aspekty návrhu, které by měl vývojář sledovat při psaní kódu aplikace pro připojení k Azure Database for MySQL. 

> [!TIP]
> Kurz, který vám ukáže, jak vytvořit server, vytvořit bránu firewall na serveru, zobrazit vlastnosti serveru, vytvořit databázi a připojit se a dotazovat pomocí workbench a mysql.exe, najdete v [tématu Návrh první databáze Azure pro MySQL.](tutorial-design-database-using-portal.md)

## <a name="language-and-platform"></a>Jazyk a platforma
K dispozici jsou ukázky kódu pro různé programovací jazyky a platformy. Odkazy na ukázky kódu najdete na adrese: [Knihovny připojení používané pro připojení k Azure Database for MySQL](concepts-connection-libraries.md)

## <a name="tools"></a>Nástroje
Azure Database for MySQL používá komunitní verzi MySQL, kompatibilní s běžnými nástroji pro správu MySQL, jako jsou nástroje Workbench nebo MySQL, jako je mysql.exe, [phpMyAdmin](https://www.phpmyadmin.net/), [Navicat](https://www.navicat.com/products/navicat-for-mysql)a další. K interakci s databázovou službou můžete taky použít portál Azure, azure CLI a REST API.

## <a name="resource-limitations"></a>Omezení prostředků
Azure Database for MySQL spravuje prostředky, které jsou k dispozici pro server pomocí dvou různých mechanismů: 
- Řízení zdrojů.
- Prosazování limitů.

## <a name="security"></a>Zabezpečení
Azure Database for MySQL poskytuje prostředky pro omezení přístupu, ochranu dat, konfiguraci uživatelů a rolí a monitorování aktivit v databázi MySQL.

## <a name="authentication"></a>Ověřování
Azure Database for MySQL podporuje ověřování uživatelů a přihlášení serveru.

## <a name="resiliency"></a>Odolnost
Když dojde k přechodné chybě při připojování k databázi MySQL, váš kód by měl opakovat volání. Doporučujeme, aby logika opakování použít zpět nouzi logiku tak, aby není zahlceni sql databáze s více klientů opakování současně.

- Ukázky kódu: Ukázky kódu, které ilustrují logiku opakování, najdete v tématu ukázky jazyka, který si vyberete, na adrese: [Knihovny připojení používané k připojení k Azure Database for MySQL](concepts-connection-libraries.md)

## <a name="managing-connections"></a>Správa připojení
Připojení k databázi jsou omezeným zdrojem, proto doporučujeme rozumné použití připojení při přístupu k databázi MySQL k dosažení lepšího výkonu.
- Přístup k databázi pomocí sdružování připojení nebo trvalé připojení.
- Přístup k databázi pomocí krátké životnosti připojení. 
- Logiku opakování v aplikaci v okamžiku pokusu o připojení k zachycení selhání vyplývajících ze souběžných připojení dosáhly maximální povolené. V logice opakování nastavte krátkou prodlevu a počkejte na náhodný čas, než se pokusí o další připojení.