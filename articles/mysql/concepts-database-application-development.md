---
title: Vývoj aplikací – Azure Database for MySQL
description: Zavádí faktory návrhu, které by měl vývojář postupovat při psaní kódu aplikace pro připojení k Azure Database for MySQL
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: 45447a26c0455fc5945af8b8e9f7442af7facfbe
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "99830681"
---
# <a name="application-development-overview-for-azure-database-for-mysql"></a>Přehled vývoje aplikací pro Azure Database for MySQL 
Tento článek popisuje faktory návrhu, které by měl vývojář postupovat při psaní kódu aplikace pro připojení k Azure Database for MySQL. 

> [!TIP]
> Návod, jak vytvořit server, vytvořit bránu firewall založenou na serveru, zobrazit vlastnosti serveru, vytvořit databázi a připojit se a dotazovat pomocí aplikace Workbench a mysql.exe, najdete v tématu [Návrh první databáze Azure Database for MySQL](tutorial-design-database-using-portal.md)

## <a name="language-and-platform"></a>Jazyk a platforma
K dispozici jsou ukázky kódu pro různé programovací jazyky a platformy. Odkazy na ukázky kódu najdete v tématech [knihovny připojení používané pro připojení k Azure Database for MySQL](concepts-connection-libraries.md)

## <a name="tools"></a>nástroje
Azure Database for MySQL používá komunitu MySQL, která je kompatibilní s nástroji pro správu MySQL, jako jsou například nástroje Workbench nebo MySQL, jako je například aplikace Workbench nebo MySQL, například mysql.exe, [phpMyAdmin](https://www.phpmyadmin.net/), [Navicat](https://www.navicat.com/products/navicat-for-mysql), [dbForge Studio pro MySQL](https://www.devart.com/dbforge/mysql/studio/) a další. K interakci s databázovou službou můžete použít taky Azure Portal, rozhraní příkazového řádku Azure a rozhraní REST API.

## <a name="resource-limitations"></a>Omezení prostředků
Azure Database for MySQL spravuje prostředky, které jsou k dispozici na serveru, pomocí dvou různých mechanismů: 
- Zásady správného řízení prostředků
- Vynucování omezení.

## <a name="security"></a>Zabezpečení
Azure Database for MySQL poskytuje prostředky pro omezení přístupu, ochranu dat, konfiguraci uživatelů a rolí a monitorování aktivit v databázi MySQL.

## <a name="authentication"></a>Authentication
Azure Database for MySQL podporuje ověřování uživatelů a přihlašovacích údajů serveru.

## <a name="resiliency"></a>Odolnost
Pokud při připojování k databázi MySQL dojde k přechodné chybě, váš kód by měl zavolat znovu. Doporučujeme, aby logika opakování použila zpětnou logiku, aby nedošlo k zahlcení databáze SQL s více klienty, které se opakují současně.

- Ukázky kódu: ukázky kódu, které ilustrují logiku opakování, najdete v ukázkách pro jazyk podle vašeho výběru v tématu: [knihovny připojení používané pro připojení k Azure Database for MySQL](concepts-connection-libraries.md)

## <a name="managing-connections"></a>Správa připojení
Databázová připojení jsou omezeného prostředku, proto doporučujeme, abyste při přístupu k databázi MySQL rozumné používání připojení, abyste dosáhli lepšího výkonu.
- Přístup k databázi pomocí sdružování připojení nebo trvalých připojení.
- Přístup k databázi pomocí krátkého životního rozsahu připojení. 
- V bodě pokusu o připojení, který vyplývají ze souběžných připojení, bylo dosaženo maximálního povoleného počtu, v aplikaci použijte logiku opakování. V logice opakování nastavte krátké zpoždění a potom před pokusy o připojení počkat na náhodný čas.
