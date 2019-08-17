---
title: Nainstalujte Visual Studio 2019 pro SQL Data Warehouse | Microsoft Docs
description: Instalace sady Visual Studio a SQL Server Development Tools (SSDT) pro Azure SQL Data Warehouse
services: sql-data-warehouse
ms.custom: vs-azure
ms.workload: azure-vs
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: development
ms.date: 08/15/2019
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: 02caa7154ef9cb8419b533f0cb2d0fd57e86bf9f
ms.sourcegitcommit: 39d95a11d5937364ca0b01d8ba099752c4128827
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/16/2019
ms.locfileid: "69563330"
---
# <a name="getting-started-with-visual-studio-2019-for-sql-data-warehouse"></a>Začínáme se sadou Visual Studio 2019 pro SQL Data Warehouse
Sady Visual Studio **2019** SQL Server Data Tools (SSDT) je jeden nástroj, který vám umožní provádět tyto akce:

- Připojení, dotazování a vývoj aplikací pro SQL Data Warehouse 
- Pomocí Průzkumníka objektů můžete vizuálně prozkoumat všechny objekty v datovém modelu, včetně tabulek, zobrazení, uložených procedur a atd.
- Generování skriptů DDL (Data Definition Language) jazyka T-SQL pro vaše objekty
- Vývoj datového skladu pomocí přístupu na základě stavu s projekty SSDT Database
- Integrujte svůj projekt databáze se systémy správy zdrojového kódu, jako je git s úložištěm Azure DevOps.
- Nastavení kanálů pro průběžnou integraci a nasazení se servery automatizace, jako je Azure DevOps [připravujeme]

> [!NOTE]
> Projekty databáze sady Visual Studio SSDT jsou momentálně ve verzi Preview. Pokud chcete dostávat pravidelné aktualizace této funkce, Hlasujte prosím na [UserVoice].

## <a name="install-visual-studio-2019"></a>Instalace sady Visual Studio 2019
Pokud si chcete stáhnout a nainstalovat Visual Studio, přečtěte si téma [Stáhnout Visual Studio 2019][] . Během instalace vyberte úlohu ukládání a zpracování dat. Samostatná instalace SSDT už není v aplikaci Visual Studio 2019 nutná.

## <a name="reporting-issues-with-ssdt-visual-studio-2019-preview"></a>Hlášení problémů s SSDT sadou Visual Studio 2019 (Preview)

Pokud chcete nahlásit problémy při použití SSDT s SQL Data Warehouse, pošlete e-mailem následující seznam distribuce e-mailů:<sqldwssdtpreview@service.microsoft.com>

## <a name="next-steps"></a>Další kroky

Teď, když máte nejnovější verzi SSDT, jste připraveni se [připojit][connect] k vašemu SQL Data Warehouse.

<!--Anchors-->

<!--Image references-->

<!--Articles-->
[connect]: ./sql-data-warehouse-query-visual-studio.md

<!--Other-->
[Stáhnout Visual Studio 2019]: https://visualstudio.microsoft.com/vs/preview/
[Installing Visual Studio]: https://msdn.microsoft.com/library/e2h7fzkw.aspx
[SSDT Download]: https://msdn.microsoft.com/library/mt204009.aspx
[UserVoice]: https://feedback.azure.com/forums/307516-sql-data-warehouse/suggestions/13313247-database-project-from-visual-studio-to-support-azu
