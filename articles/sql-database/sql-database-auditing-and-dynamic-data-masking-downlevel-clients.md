---
title: "Tabulky auditování, přesměrování TDS IP koncové body pro databázi SQL Azure a | Microsoft Docs"
description: "Další informace o auditování, TDS přesměrování a IP koncový bod změny při implementaci tabulky auditování ve službě Azure SQL Database."
services: sql-database
author: giladm
manager: craigg
ms.service: sql-database
ms.custom: security
ms.topic: article
ms.date: 05/31/2017
ms.author: giladm
ms.openlocfilehash: 1053869ed26739463ab6035692052d7b6b34bff0
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/16/2018
---
# <a name="sql-database----downlevel-clients-support-and-ip-endpoint-changes-for-table-auditing"></a>SQL Database – podpora klientů nižší úrovně a změní koncový bod IP adresy pro auditování tabulka

> [!IMPORTANT]
> Tento dokument se vztahuje pouze na auditování tabulka, která je **nyní zastaralé**.<br>
> Použijte prosím nový [auditování objektů Blob](sql-database-auditing.md) metoda, která **nemá** vyžadovat úpravy řetězec připojení klientů nižší úrovně. Další informace o auditování objektů Blob najdete v [začít pracovat s auditování databáze SQL](sql-database-auditing.md).

[Databáze auditování](sql-database-auditing.md) automaticky spolupracuje se službou SQL klientů, které podporují přesměrování TDS. Všimněte si, že přesměrování nelze použít při použití metody auditování objektů Blob.

## <a id="subheading-1"></a>Podpora klientů nižší úrovně
Jakýkoli klient, který implementuje TDS 7.4 také podporuje přesměrování. Výjimky zahrnují JDBC 4.0, ve kterém funkci přesměrování není plně podporována a Tedious pro Node.JS, ve které přesměrování nebyla implementována.

Pro "Klienty nižší úrovně" tj. by měl být které podporu TDS 7.3 a pod - server plně kvalifikovaný název domény pro připojení řetězec verze upraven:

Původní server plně kvalifikovaný název domény v připojovacím řetězci: <*název serveru*>. database.windows.net

Upravené plně kvalifikovaný název domény serveru v připojovacím řetězci: <*název serveru*> .database. **zabezpečené**. windows.net

Částečný seznam "Klienty nižší úrovně" zahrnuje:

* Rozhraní .NET 4.0 a níže,
* ODBC 10.0 a níže.
* JDBC (při JDBC podporuje TDS 7.4, TDS přesměrování funkce není podporována plně)
* Zdlouhavé (pro platformu Node.JS)

**Poznámka:** výše uvedeném serveru úpravy plně kvalifikovaného názvu domény může být užitečné také pro použití zásad auditování na úrovni SQL serveru bez potřebu konfiguraci krok v každé databázi (dočasné zmírnění).

## <a id="subheading-2"></a>Koncový bod IP adresy se změní při zapínání auditování
Upozorňujeme, že když povolíte auditování tabulka, se změní koncový bod IP adresy vaší databáze. Pokud máte možnost nastavení striktní brány firewall, aktualizujte prosím tato nastavení brány firewall odpovídajícím způsobem.

Nový koncový bod IP databáze bude záviset na oblasti databáze:

| Oblast databáze | Možné koncové body IP |
| --- | --- |
| Čína – sever |139.217.29.176, 139.217.28.254 |
| Čína – východ |42.159.245.65, 42.159.246.245 |
| Austrálie – východ |104.210.91.32, 40.126.244.159, 191.239.64.60, 40.126.255.94 |
| Austrálie – jihovýchod |191.239.184.223, 40.127.85.81, 191.239.161.83, 40.127.81.130 |
| Brazílie – jih |104.41.44.161, 104.41.62.230, 23.97.99.54, 104.41.59.191 |
| Střed USA |104.43.255.70, 40.83.14.7, 23.99.128.244, 40.83.15.176 |
| Střed USA – EUAP |52.180.178.16, 52.180.176.190 |
| Východní Asie |23.99.125.133, 13.75.40.42, 23.97.71.138, 13.94.43.245 |
| Východní USA 2 |104.209.141.31, 104.208.238.177, 191.237.131.51, 104.208.235.50 |
| Východ USA |23.96.107.223, 104.41.150.122, 23.96.38.170, 104.41.146.44 |
| Východ USA – EUAP |52.225.190.86, 52.225.191.187 |
| Střed Indie |104.211.98.219, 104.211.103.71 |
| Indie – jih |104.211.227.102, 104.211.225.157 |
| Indie – západ |104.211.161.152, 104.211.162.21 |
| Japonsko – východ |104.41.179.1, 40.115.253.81, 23.102.64.207, 40.115.250.196 |
| Japonsko – západ |104.214.140.140, 104.214.146.31, 191.233.32.34, 104.214.146.198 |
| Střed USA – sever |191.236.155.178, 23.96.192.130, 23.96.177.169, 23.96.193.231 |
| Severní Evropa |104.41.209.221, 40.85.139.245, 137.116.251.66, 40.85.142.176 |
| Střed USA – jih |191.238.184.128, 40.84.190.84, 23.102.160.153, 40.84.186.66 |
| Jihovýchodní Asie |104.215.198.156, 13.76.252.200, 23.97.51.109, 13.76.252.113 |
| Západní Evropa |104.40.230.120, 13.80.23.64, 137.117.171.161, 13.80.8.37, 104.47.167.215, 40.118.56.193, 104.40.176.73, 40.118.56.20 |
| Západní USA |191.236.123.146, 138.91.163.240, 168.62.194.148, 23.99.6.91 |
| Západní USA 2 |13.66.224.156, 13.66.227.8 |
| Západní střed USA |52.161.29.186, 52.161.27.213 |
| Střední Kanada |13.88.248.106, 13.88.248.110 |
| Východní Kanada |40.86.227.82, 40.86.225.194 |
| Spojené království – sever |13.87.101.18, 13.87.100.232 |
| Spojené království – jih 2 |13.87.32.202, 13.87.32.226 |
