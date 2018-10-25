---
title: Návrh první databáze SQL Azure – Jazyk C# | Microsoft Docs
description: Zjistěte, jak navrhnout první databázi SQL Azure a jak se k ní připojit pomocí programu v jazyce C# s využitím ADO.NET.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: ''
ms.topic: tutorial
author: MightyPen
ms.author: genemi
ms.reviewer: carlrab
manager: craigg-msft
ms.date: 06/07/2018
ms.openlocfilehash: 504fae03e06632f5960bfe0761cc0167b9e1d3fb
ms.sourcegitcommit: 62759a225d8fe1872b60ab0441d1c7ac809f9102
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/19/2018
ms.locfileid: "49465211"
---
# <a name="design-an-azure-sql-database-and-connect-with-cx23-and-adonet"></a>Návrh databáze SQL Azure a její připojení pomocí C&#x23; a ADO.NET

Azure SQL Database je relační databáze jako služba (DBaaS) v cloudu Microsoftu (Azure). V tomto kurzu zjistíte, jak pomocí webu Azure Portal a ADO.NET v sadě Visual Studio provádět následující úlohy:

> [!div class="checklist"]
> * Vytvořit databázi na webu Azure Portal
> * Vytvořit pravidlo brány firewall na úrovni serveru na webu Azure Portal
> * Připojit se k databázi pomocí ADO.NET a sady Visual Studio
> * Vytvářet tabulky pomocí ADO.NET
> * Vkládat, aktualizovat a odstraňovat data pomocí ADO.NET
> * Dotazovat data pomocí ADO.NET

Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

Instalaci sady [Visual Studio Community 2017, Visual Studio Professional 2017 nebo Visual Studio Enterprise 2017](https://www.visualstudio.com/downloads/).

<!-- The following included .md, sql-database-tutorial-portal-create-firewall-connection-1.md, is long.
And it starts with a ## H2.
-->

[!INCLUDE [sql-database-tutorial-portal-create-firewall-connection-1](../../includes/sql-database-tutorial-portal-create-firewall-connection-1.md)]

<!-- The following included .md, sql-database-csharp-adonet-create-query-2.md, is long.
And it starts with a ## H2.
-->

[!INCLUDE [sql-database-csharp-adonet-create-query-2](../../includes/sql-database-csharp-adonet-create-query-2.md)]

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se naučili základním úkonům při práci s databází, jako je vytvoření databáze a tabulek, načtení a dotazování dat a obnovení databáze k určitému bodu v čase. Naučili jste se tyto postupy:
> [!div class="checklist"]
> * Vytvoření databáze
> * Nastavení pravidla brány firewall
> * Připojení k databázi pomocí sady [Visual Studio a jazyka C#](sql-database-connect-query-dotnet-visual-studio.md)
> * Vytvoření tabulek
> * Vložení, aktualizace a odstranění dat
> * Dotazování dat

V dalším kurzu se dozvíte, jak migrovat data.

> [!div class="nextstepaction"]
> [Migrace databáze SQL Serveru do služby Azure SQL Database](sql-database-migrate-your-sql-server-database.md)
