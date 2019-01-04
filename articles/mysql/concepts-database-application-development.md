---
title: Přehled vývoje databázových aplikací pro službu Azure Database for MySQL
description: Zavádí aspekty návrhu, které vývojář by měl dodržovat při psaní kódu aplikace k připojení ke službě Azure Database for MySQL
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 02/28/2018
ms.openlocfilehash: 946f7011c51b7c6844e023d03e01e4c2043d2578
ms.sourcegitcommit: 71ee622bdba6e24db4d7ce92107b1ef1a4fa2600
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/17/2018
ms.locfileid: "53544459"
---
# <a name="application-development-overview-for-azure-database-for-mysql"></a>Přehled vývoje databázových aplikací pro službu Azure Database for MySQL 
Tento článek popisuje aspekty návrhu, které vývojář by měl dodržovat při psaní kódu aplikace k připojení ke službě Azure Database for MySQL. 

> [!TIP]
> Kurz ukazuje, jak vytvořit server, vytvořit bránu firewall založenou na serveru, zobrazení vlastností serveru, databázi, vytvoření a připojení a dotazování pomocí aplikace workbench a mysql.exe, najdete v tématu [navrhnout vaši první databáze Azure Database for MySQL](tutorial-design-database-using-portal.md)

## <a name="language-and-platform"></a>Jazyk a platforma
K dispozici jsou ukázky kódu pro různé programovací jazyky a platformy. Odkazy na ukázky kódu najdete tady: [Knihovny pro připojení použít k připojení ke službě Azure Database for MySQL](concepts-connection-libraries.md)

## <a name="tools"></a>Nástroje
Azure Database for MySQL používá komunitní verze MySQL, kompatibilní s MySQL běžné nástroje pro správu jako jsou nástroje Workbench nebo MySQL, jako jsou mysql.exe, [phpmyadmin zobrazuje](https://www.phpmyadmin.net/), [Navicat](https://www.navicat.com/products/navicat-for-mysql)a další. Webu Azure portal, rozhraní příkazového řádku Azure a rozhraní REST API můžete také použít k interakci s databázová služba.

## <a name="resource-limitations"></a>Omezení prostředků
Azure Database for MySQL spravuje prostředky dostupné pro server pomocí dvou různých mechanismů: 
- Zásady správného řízení prostředků.
- Vynucení limitů.

## <a name="security"></a>Zabezpečení
Azure Database for MySQL poskytuje prostředky pro omezení přístupu, ochranu dat, konfigurace uživatele a role a monitorování aktivit na databázi MySQL.

## <a name="authentication"></a>Authentication
Azure Database for MySQL podporuje ověřování uživatelů a přihlášení serveru.

## <a name="resiliency"></a>Odolnost
Dojde-li k přechodné chybě při připojování k databázi MySQL, by měl váš kód volání zopakovat. Doporučujeme logika opakovaných pokusů použít regresní logiku tak, aby se databáze SQL není zahlcovat s současně opakovanými pokusy několika klientů.

- Ukázky kódu: Ukázky kódu, které ilustrují logiku opakování, najdete v ukázkách jazyka podle vašeho výběru v: [Knihovny pro připojení použít k připojení ke službě Azure Database for MySQL](concepts-connection-libraries.md)

## <a name="managing-connections"></a>Správa připojení
Připojení k databázi jsou omezené prostředků, proto při přístupu k vaší databázi MySQL můžete dosáhnout lepšího výkonu doporučujeme rozumné použití připojení.
- Přístup k databázi pomocí sdružování připojení nebo trvalé připojení.
- Přístup k databázi pomocí připojení krátkou životnost. 
- Logika opakování pro použití ve vaší aplikaci Přejme během pokusu o připojení pro zachycení chyb vyplývající z souběžných připojení bylo dosaženo maximální povolený počet. V logiku opakování nastavte krátkou prodlevou a potom počkejte náhodný čas před pokusy o další připojení.