---
title: Porty nad 1433 pro službu SQL Database | Dokumentace Microsoftu
description: Připojení klienta z ADO.NET ke službě Azure SQL Database můžete používat proxy server a pracovat přímo s databází pomocí jiné porty než 1433.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MightyPen
ms.author: genemi
ms.reviewer: sstein
manager: craigg
ms.date: 11/07/2018
ms.openlocfilehash: b6fbb71a827c90abd1fac58d7975ab2f7b2a5674
ms.sourcegitcommit: ba035bfe9fab85dd1e6134a98af1ad7cf6891033
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/01/2019
ms.locfileid: "55560885"
---
# <a name="ports-beyond-1433-for-adonet-45"></a>Porty nad 1433 pro ADO.NET 4.5

Toto téma popisuje chování připojení Azure SQL Database pro klienty, kteří používají technologii ADO.NET 4.5 nebo novější. 

> [!IMPORTANT]
> Informace o připojení k architektuře, najdete v tématu [architektura připojení k Azure SQL Database](sql-database-connectivity-architecture.md).
>

## <a name="outside-vs-inside"></a>Vnější vs. vnitřní

Pro připojení ke službě Azure SQL Database, musíte nejprve požádáme, jestli se váš klientský program spouští *mimo* nebo *uvnitř* cloudu Azure. Témata se zabývají dvě běžné scénáře.

#### <a name="outside-client-runs-on-your-desktop-computer"></a>*Vnější:* Klient spustí na stolním počítači

Port 1433 je jediný port, který musí být otevřený ve stolním počítači, který je hostitelem klientské aplikace SQL Database.

#### <a name="inside-client-runs-on-azure"></a>*Uvnitř:* Klient spustí v Azure

Váš klient běží v cloudu Azure, používá, co jsme volat *přímé trasy* komunikovat se serverem SQL Database. Po vytvoření připojení, další interakce mezi klientem a databáze zahrnují žádná brána Azure SQL Database.

Sekvence je následujícím způsobem:

1. ADO.NET 4.5 (nebo novější) zahájí stručný interakci s Azure cloud a přijímá dynamicky identifikované nastavení portu.
   
   * Číslo portu dynamicky identifikované je v rozsahu 11000 11999 nebo 14000 14999.
2. ADO.NET pak připojí k databázi SQL server přímo, s žádné middleware mezi.
3. Dotazy se odesílají přímo do databáze a výsledky se vrátí přímo do klienta.

Ujistěte se, že port, který rozsahů 11000 11999 a 14000 14999 na svém počítači Klient služby Azure zůstává k dispozici pro technologii ADO.NET 4.5 klienta interakce s databází SQL.

* Porty v rozsahu musí být zejména bez dalších odchozí blokování.
* Na svém virtuálním počítači Azure **brány Windows Firewall s pokročilým zabezpečením** řídí nastavení portu.
  
  * Můžete použít [brány firewall na uživatelské rozhraní](https://msdn.microsoft.com/library/cc646023.aspx) přidáte pravidlo, u kterého nastavíte **TCP** protokolu společně s rozsah portů se syntaxí, jako jsou **11000 11999**.

## <a name="version-clarifications"></a>Vyjasnění verze
Tato část vysvětluje zástupných názvů, které odkazují na verze produktu. Také uvádí seznam některých páry verze mezi produkty.

#### <a name="adonet"></a>ADO.NET
* ADO.NET 4.0 podporuje protokol TDS 7.3, ale ne 7.4.
* ADO.NET 4.5 a vyšší podporuje protokol TDS 7.4.

#### <a name="odbc"></a>ODBC
* Microsoft SQL Server ODBC 11 nebo novější

#### <a name="jdbc"></a>JDBC
* Microsoft SQL Server JDBC 4.2 nebo novější (JDBC 4.0 skutečně podporuje TDS 7.4, ale neimplementuje "přesměrování")


## <a name="related-links"></a>Související odkazy
* 20. července 2015 byla vydána ADO.NET 4.6. Oznámení blogu od týmu .NET je k dispozici [tady](https://blogs.msdn.com/b/dotnet/archive/2015/07/20/announcing-net-framework-4-6.aspx).
* 15. srpna 2012 byla vydána ADO.NET 4.5. Oznámení blogu od týmu .NET je k dispozici [tady](https://blogs.msdn.com/b/dotnet/archive/2012/08/15/announcing-the-release-of-net-framework-4-5-rtm-product-and-source-code.aspx). 
  * Blogový příspěvek o ADO.NET 4.5.1 je k dispozici [tady](https://blogs.msdn.com/b/dotnet/archive/2013/06/26/announcing-the-net-framework-4-5-1-preview.aspx).

* Microsoft® ODBC Driver 17 pro SQL Server® – Windows, Linux a macOS https://www.microsoft.com/download/details.aspx?id=56567

* Připojení ke službě Azure SQL Database V12 prostřednictvím přesměrování https://blogs.msdn.microsoft.com/sqlcat/2016/09/08/connect-to-azure-sql-database-v12-via-redirection/

* [Seznam verzí protokolu TDS.](http://www.freetds.org/userguide/tdshistory.htm)
* [Přehled vývoje SQL Database](sql-database-develop-overview.md)
* [Brána firewall služby Azure SQL Database](sql-database-firewall-configure.md)
* [Postup: Konfigurace nastavení brány firewall pro službu SQL Database](sql-database-configure-firewall-settings.md)


