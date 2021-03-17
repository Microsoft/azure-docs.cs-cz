---
title: Porty nad 1433
description: Připojení klientů z ADO.NET do Azure SQL Database mohou obejít proxy server a pracovat přímo s databází pomocí jiných než 1433 portů.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: sqldbrb=1, devx-track-dotnet
ms.devlang: ''
ms.topic: reference
author: stevestein
ms.author: sstein
ms.reviewer: genemi
ms.date: 06/11/2020
ms.openlocfilehash: 0d009522ea0d0986233983f8725549b618ffb537
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91444882"
---
# <a name="ports-beyond-1433-for-adonet-45"></a>Porty nad 1433 pro ADO.NET 4.5
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Toto téma popisuje chování Azure SQL Database připojení pro klienty, kteří používají ADO.NET 4,5 nebo novější verzi.

> [!IMPORTANT]
> Informace o architektuře připojení najdete v tématu [architektura Azure SQL Databaseho připojení](connectivity-architecture.md).
>

## <a name="outside-vs-inside"></a>Mimo vs uvnitř

Pro připojení k Azure SQL Database je potřeba nejdřív položit dotaz, jestli se Váš klientský program spouští *mimo* hranice cloudu Azure nebo *uvnitř* něj. Dílčí části projednávají dva běžné scénáře.

### <a name="outside-client-runs-on-your-desktop-computer"></a>*Mimo:* Klient běží na stolním počítači.

Port 1433 je jediným portem, který musí být otevřený na stolním počítači, který je hostitelem klientské aplikace SQL Database.

### <a name="inside-client-runs-on-azure"></a>*Uvnitř:* Klient běží na Azure.

Když se váš klient spouští v rámci hranice cloudu Azure, používá k interakci s SQL Database *přímou trasu* . Po navázání připojení nezahrnuje další interakce mezi klientem a databází žádnou bránu Azure SQL Database.

Sekvence je následující:

1. ADO.NET 4,5 (nebo novější) zahájí krátkou interakci s cloudem Azure a získá dynamicky identifikované číslo portu.

   * Dynamicky identifikované číslo portu je v rozsahu 11000-11999.
2. ADO.NET se pak připojuje přímo k SQL Database bez middlewaru mezi.
3. Dotazy jsou odesílány přímo do databáze a výsledky jsou vráceny přímo klientovi.

Zajistěte, aby byly rozsahy portů 11000-11999 na klientském počítači Azure k dispozici pro interakce klientů ADO.NET 4,5 s SQL Database.

* Konkrétně musí být porty v rozsahu bez jakéhokoli dalšího odchozího blokování.
* Na VIRTUÁLNÍm počítači Azure řídí **Brána Windows Firewall s pokročilým zabezpečením** nastavení portů.
  
  * Pomocí [uživatelského rozhraní brány firewall](/sql/sql-server/install/configure-the-windows-firewall-to-allow-sql-server-access) můžete přidat pravidlo, pro které zadáte protokol **TCP** spolu s rozsahem portů s syntaxí, jako je **11000-11999**.

## <a name="version-clarifications"></a>Vyjasnění verzí

Tato část vysvětluje monikery, které odkazují na verze produktu. Obsahuje taky některé páry verzí mezi produkty.

### <a name="adonet"></a>ADO.NET

* ADO.NET 4,0 podporuje protokol TDS 7,3, ale ne 7,4.
* ADO.NET 4,5 a novější podporuje protokol TDS 7,4.

### <a name="odbc"></a>ODBC

* Microsoft SQL Server ODBC 11 nebo vyšší

### <a name="jdbc"></a>JDBC

* Microsoft SQL Server JDBC 4,2 nebo vyšší (JDBC 4,0 ve skutečnosti podporuje TDS 7,4, ale neimplementuje "přesměrování")

## <a name="related-links"></a>Související odkazy

* ADO.NET 4,6 bylo vydáno 20. července 2015. Oznámení na blogu od týmu .NET je k dispozici [zde](https://devblogs.microsoft.com/dotnet/announcing-net-framework-4-6/).
* ADO.NET 4,5 byla vydána 15. srpna 2012. Oznámení na blogu od týmu .NET je k dispozici [zde](https://devblogs.microsoft.com/dotnet/announcing-the-release-of-net-framework-4-5-rtm-product-and-source-code/).
  * Blogový příspěvek o ADO.NET 4.5.1 je k dispozici [zde](https://devblogs.microsoft.com/dotnet/announcing-the-net-framework-4-5-1-preview/).

* Microsoft ODBC Driver 17 pro SQL Server https://aka.ms/downloadmsodbcsql

* Připojení k Azure SQL Database V12 prostřednictvím přesměrování https://techcommunity.microsoft.com/t5/DataCAT/Connect-to-Azure-SQL-Database-V12-via-Redirection/ba-p/305362

* [Seznam verzí protokolu TDS](https://www.freetds.org/)
* [Přehled vývoje SQL Database](develop-overview.md)
* [Azure SQL Database firewall](firewall-configure.md)
