---
title: Přístavy nad 1433
description: Připojení klientů z ADO.NET do Azure SQL Database můžete obejít proxy server a komunikovat přímo s databází pomocí portů než 1433.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MightyPen
ms.author: genemi
ms.reviewer: sstein
ms.date: 04/03/2019
ms.openlocfilehash: c0012b61cf43d01afd5e7f5f52948310b5eb8420
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "73828060"
---
# <a name="ports-beyond-1433-for-adonet-45"></a>Porty nad 1433 pro ADO.NET 4.5

Toto téma popisuje chování připojení azure SQL database pro klienty, kteří používají ADO.NET 4.5 nebo novější verzi.

> [!IMPORTANT]
> Informace o architektuře připojení najdete v [tématu Architektura připojení Azure SQL Database](sql-database-connectivity-architecture.md).
>

## <a name="outside-vs-inside"></a>Vnější vs uvnitř

Pro připojení k Azure SQL Database, musíme se nejprve zeptat, jestli váš klientský program běží *mimo* nebo *uvnitř* hranice cloudu Azure. Pododdíly popisují dva běžné scénáře.

### <a name="outside-client-runs-on-your-desktop-computer"></a>*Vně:* Klient běží na stolním počítači

Port 1433 je jediný port, který musí být otevřen ve stolním počítači, který je hostitelem klientské aplikace SQL Database.

### <a name="inside-client-runs-on-azure"></a>*Uvnitř:* Klient běží v Azure

Když váš klient běží uvnitř hranice cloudu Azure, použije to, co můžeme volat *přímou trasu* pro interakci se serverem SQL Database. Po navázání připojení, další interakce mezi klientem a databází zahrnují žádné Brány databáze Azure SQL.

Pořadí je následující:

1. ADO.NET 4.5 (nebo novější) iniciuje krátkou interakci s cloudem Azure a obdrží dynamicky identifikované číslo portu.

   * Dynamicky identifikované číslo portu je v rozsahu 11000-11999.
2. ADO.NET se pak připojí k serveru SQL Database přímo, bez middleware mezi nimi.
3. Dotazy jsou odesílány přímo do databáze a výsledky jsou vráceny přímo klientovi.

Ujistěte se, že rozsahy portů 11000-11999 v klientském počítači Azure zůstanou k dispozici pro ADO.NET 4.5 klientských interakcí s databází SQL.

* Zejména porty v rozsahu musí být bez jiných odchozích blokátorů.
* Na virtuálním počítači Azure řídí nastavení portu **brána Windows Firewall s pokročilým zabezpečením.**
  
  * [Pomocí uživatelského rozhraní brány firewall](https://msdn.microsoft.com/library/cc646023.aspx) můžete přidat pravidlo, pro které zadáte protokol **TCP** spolu s rozsahem portů se syntaxí jako **11000-11999**.

## <a name="version-clarifications"></a>Vysvětlení verze

Tato část objasňuje zástupné názvy, které odkazují na verze produktu. Obsahuje také seznam některých dvojic verzí mezi produkty.

### <a name="adonet"></a>ADO.NET

* ADO.NET 4.0 podporuje protokol TDS 7.3, ale ne 7.4.
* ADO.NET 4.5 a novější podporuje protokol TDS 7.4.

### <a name="odbc"></a>ODBC

* Microsoft SQL Server ODBC 11 nebo vyšší

### <a name="jdbc"></a>JDBC

* Microsoft SQL Server JDBC 4.2 nebo vyšší (JDBC 4.0 skutečně podporuje TDS 7.4, ale neimplementuje "přesměrování")

## <a name="related-links"></a>Související odkazy

* ADO.NET 4.6 byl vydán 20. Oznámení blogu od týmu .NET je k dispozici [zde](https://blogs.msdn.com/b/dotnet/archive/20../../announcing-net-framework-4-6.aspx).
* ADO.NET 4.5 byl propuštěn v srpnu 15, 2012. Oznámení blogu od týmu .NET je k dispozici [zde](https://blogs.msdn.com/b/dotnet/archive/20../../announcing-the-release-of-net-framework-4-5-rtm-product-and-source-code.aspx).
  * Blog post o ADO.NET 4.5.1 je k dispozici [zde](https://blogs.msdn.com/b/dotnet/archive/20../../announcing-the-net-framework-4-5-1-preview.aspx).

* Microsoft® OVLADAČ ODBC 17 pro SQL Server® - Windows, Linux, & macOShttps://www.microsoft.com/download/details.aspx?id=56567

* Připojení k Azure SQL Database V12 přes přesměrováníhttps://techcommunity.microsoft.com/t5/DataCAT/Connect-to-Azure-SQL-Database-V12-via-Redirection/ba-p/305362

* [Seznam verzí protokolu TDS](https://www.freetds.org/userguide/tdshistory.htm)
* [Přehled vývoje databáze SQL](sql-database-develop-overview.md)
* [Brána firewall azure databáze SQL](sql-database-firewall-configure.md)
* [Postup: Konfigurace nastavení brány firewall pro SQL Database](sql-database-configure-firewall-settings.md)


