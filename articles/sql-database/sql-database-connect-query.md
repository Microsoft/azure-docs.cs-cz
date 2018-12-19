---
title: Připojení ke službě Azure SQL Database a její dotazování – Rychlé starty | Microsoft Docs
description: Rychlé starty pro službu Azure SQL Database, které ukazují připojení k databázi SQL Azure a její dotazování.
services: sql-database
ms.service: sql-database
ms.subservice: ''
ms.custom: ''
ms.devlang: ''
ms.topic: quickstart
author: CarlRabeler
ms.author: carlrab
ms.reviewer: ''
manager: craigg
ms.date: 11/01/2018
ms.openlocfilehash: 613b4cf2b08269259a4608a6960b815777cd0ae9
ms.sourcegitcommit: 4eeeb520acf8b2419bcc73d8fcc81a075b81663a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/19/2018
ms.locfileid: "53608030"
---
# <a name="quickstarts-azure-sql-database-connect-and-query"></a>Šablony rychlý start: Azure SQL Database připojení a dotazování

Následující dokument obsahuje odkazy na příklady Azure, které ukazují připojení k databázi SQL Azure a její dotazování. Obsahuje také několik doporučení týkající se protokolu TLS (Transport Level Security).

## <a name="quickstarts"></a>Šablony Rychlý start

| |  |
|---|---|
|[SQL Server Management Studio](sql-database-connect-query-ssms.md)|Tento rychlý start ukazuje použití SSMS k připojení k databázi SQL Azure a následné použití příkazů jazyka Transact-SQL k dotazování, vkládání, aktualizaci a odstraňování dat v databázi.|
|[Azure Data Studio](https://docs.microsoft.com/sql/azure-data-studio/quickstart-sql-database?toc=/azure/sql-database/toc.json)|Tento rychlý start ukazuje použití sady Azure Data Studio pro připojení k databázi SQL Azure a následné použití příkazů jazyka Transact-SQL (T-SQL) k vytvoření databáze TutorialDB používané v kurzech pro Azure Data Studio.|
|[Azure Portal](sql-database-connect-query-portal.md)|Tento rychlý start ukazuje použití editoru dotazů pro připojení k databázi SQL a následné použití příkazů jazyka Transact-SQL k dotazování, vkládání, aktualizaci a odstraňování dat v databázi.|
|[Visual Studio Code](sql-database-connect-query-vscode.md)|Tento rychlý start ukazuje použití Visual Studio Code pro připojení k databázi SQL Azure a následné použití příkazů jazyka Transact-SQL k dotazování, vkládání, aktualizaci a odstraňování dat v databázi.|
|[.NET se sadou Visual Studio](sql-database-connect-query-dotnet-visual-studio.md)|Tento rychlý start ukazuje použití rozhraní .NET Framework a sady Visual Studio k vytvoření programu v jazyce C# pro připojení k databázi SQL Azure a použití příkazů jazyka Transact-SQL k dotazování dat.|
|[.NET Core](sql-database-connect-query-dotnet-core.md)|Tento rychlý start ukazuje použití .NET Core v systému Windows, Linux nebo macOS k vytvoření programu v jazyce C# pro připojení k databázi SQL Azure a použití příkazů jazyka Transact-SQL k dotazování dat.|
|[Go](sql-database-connect-query-go.md)|Tento rychlý start ukazuje použití jazyka Go pro připojení k databázi SQL Azure. Ukazuje se také použití příkazů jazyka Transact-SQL k dotazování a úpravě dat.|
|[Java](sql-database-connect-query-java.md)|Tento rychlý start ukazuje použití Javy pro připojení k databázi SQL Azure a následné použití příkazů jazyka Transact-SQL k dotazování dat.|
|[Node.js](sql-database-connect-query-nodejs.md)|Tento rychlý start ukazuje použití Node.js k vytvoření programu pro připojení k databázi SQL Azure a použití příkazů jazyka Transact-SQL k dotazování dat.|
|[PHP](sql-database-connect-query-php.md)|Tento rychlý start ukazuje použití PHP k vytvoření programu pro připojení k databázi SQL Azure a použití příkazů jazyka Transact-SQL k dotazování dat.|
|[Python](sql-database-connect-query-python.md)|Tento rychlý start ukazuje použití Pythonu pro připojení k databázi SQL Azure a použití příkazů jazyka Transact-SQL k dotazování dat. |
|[Ruby](sql-database-connect-query-ruby.md)|Tento rychlý start ukazuje použití Ruby k vytvoření programu pro připojení k databázi SQL Azure a použití příkazů jazyka Transact-SQL k dotazování dat.|
|||

## <a name="tls-considerations-for-sql-database-connectivity"></a>Důležité informace o použití protokolu TLS pro připojení k databázi SQL
Protokol TLS (Transport Layer Security) používají všechny ovladače, které Microsoft používá nebo podporuje pro připojení k databázi SQL Azure. Není potřeba žádná zvláštní konfigurace. Pro všechna připojení k serveru SQL Server nebo k databázi SQL Azure doporučujeme nastavit u všech aplikací následující konfigurace nebo jejich ekvivalenty:

 - **Encrypt = On**
 - **TrustServerCertificate = Off**

Některé systémy pro uvedená klíčová slova konfigurací používají rozdílná, ale ekvivalentní klíčová slova. Tyto konfigurace zajistí, že ovladač klienta ověří identitu certifikátu TLS přijatého ze serveru.

Také doporučujeme zakázat v klientovi protokoly TLS 1.1 a 1.0, pokud potřebujete dodržovat standard PCI–DSS (Payment Card Industry – Data Security Standard).

Ovladače od jiných výrobců nemusí ve výchozím nastavení protokol TLS používat. To může při připojování k databázi SQL Azure hrát důležitou roli. Aplikace s integrovanými ovladači vám nemusí umožnit řídit tato nastavení připojení. Doporučujeme, abyste si ověřili zabezpečení takovýchto ovladačů a aplikací, dříve než je použijete v systémech, které pracují s důvěrnými osobními údaji.

## <a name="next-steps"></a>Další postup

Informace o připojení k architektuře najdete v článku o [architektuře připojení k databázi SQL Azure](sql-database-connectivity-architecture.md).