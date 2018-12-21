---
title: Tabulka auditování, přesměrování protokolu TDS a koncovými body IP pro službu Azure SQL Database | Dokumentace Microsoftu
description: Další informace o auditování, přesměrování protokolu TDS a IP změny koncových bodů při implementaci auditování tabulek ve službě Azure SQL Database.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: ronitr
ms.author: ronitr
ms.reviewer: vanto
manager: craigg
ms.date: 04/01/2018
ms.openlocfilehash: 41509c1f35d42a1e8558299b4244ad515c36ec17
ms.sourcegitcommit: 6f59cdc679924e7bfa53c25f820d33be242cea28
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2018
ms.locfileid: "48816051"
---
# <a name="sql-database----downlevel-clients-support-and-ip-endpoint-changes-for-table-auditing"></a>SQL Database – podpora klientů nižší úrovně a změny koncových bodů IP pro auditování tabulek

> [!IMPORTANT]
> V tomto dokumentu platí jenom pro auditování tabulek, což je **teď zastaralé**.<br>
> Použijte prosím nový [auditování objektů Blob](sql-database-auditing.md) metoda, která **nemá** vyžadovat úpravy řetězec připojení klientů nižší úrovně. Další informace o auditování objektů Blob najdete v [Začínáme s auditem SQL database](sql-database-auditing.md).

[Auditování databáze](sql-database-auditing.md) automaticky spolupracuje se službou SQL klienty, kteří podporují přesměrování TDS. Všimněte si, že přesměrování se nedá použít při použití metody auditování objektů Blob.

## <a id="subheading-1"></a>Podpora klientů nižší úrovně
Libovolného klienta, který implementuje TDS 7.4 by měl také podporovat přesměrování. Výjimky zahrnují JDBC 4.0, ve kterém se plně nepodporuje funkci přesměrování a Tedious pro Node.JS, v které přesměrování nebyla implementována.

Pro "Klienty nižší úrovně" tedy který podporu TDS verzi 7.3 a pod - server plně kvalifikovaný název domény v připojení řetězec třeba upravit:

Původní server plně kvalifikovaný název domény v připojovacím řetězci: <*název serveru*>. database.windows.net

Upravené server plně kvalifikovaný název domény v připojovacím řetězci: <*název serveru*> .database. **zabezpečené**. windows.net

Částečný seznam "Klienti nižší úrovně" zahrnuje:

* Rozhraní .NET 4.0 a nižší,
* ODBC 10.0 a nižší.
* JDBC (zatímco JDBC podporují TDS 7.4, funkce přesměrování protokolu TDS není plně podporovány)
* Tedious (pro Node.JS)

**Poznámka:** výše uvedeném serveru plně kvalifikovaného názvu domény změny mohou být užitečné také pro použití zásady auditování služby SQL Server úroveň bez potřebu konfigurační krok v každé databázi (dočasná omezení rizik).

## <a id="subheading-2"></a>Koncový bod IP adresy se změní při zapínání auditování
Mějte prosím na paměti, že když povolíte auditování tabulek, dojde ke změně koncový bod IP adresy vaší databáze. Pokud máte nastavení striktní brány firewall, aktualizujte prosím tato nastavení brány firewall odpovídajícím způsobem.

Nový koncový bod IP databáze závisí na oblasti databáze:

| Oblast databáze | Je to možné koncovými body IP |
| --- | --- |
| Čína – sever |139.217.29.176, 139.217.28.254 |
| Čína – východ |42.159.245.65, 42.159.246.245 |
| Austrálie – východ |104.210.91.32, 40.126.244.159, 191.239.64.60, 40.126.255.94 |
| Austrálie – jihovýchod |191.239.184.223, 40.127.85.81, 191.239.161.83, 40.127.81.130 |
| Brazílie – jih |104.41.44.161, 104.41.62.230, 23.97.99.54, 104.41.59.191 |
| USA – střed |104.43.255.70, 40.83.14.7, 23.99.128.244, 40.83.15.176 |
| Střed USA – EUAP |52.180.178.16, 52.180.176.190 |
| Východní Asie |23.99.125.133, 13.75.40.42, 23.97.71.138, 13.94.43.245 |
| Východní USA 2 |104.209.141.31, 104.208.238.177, 191.237.131.51, 104.208.235.50 |
| USA – východ |23.96.107.223, 104.41.150.122, 23.96.38.170, 104.41.146.44 |
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
| Kanada – střed |13.88.248.106, 13.88.248.110 |
| Kanada – východ |40.86.227.82, 40.86.225.194 |
| Velká Británie – sever |13.87.101.18, 13.87.100.232 |
| Velká Británie – jih 2 |13.87.32.202, 13.87.32.226 |
