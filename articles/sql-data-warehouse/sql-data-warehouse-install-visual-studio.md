---
title: Instalace sady Visual Studio a SSDT pro SQL Data Warehouse | Dokumentace Microsoftu
description: Instalace sady Visual Studio a SQL Server Development Tools (SSDT) pro Azure SQL Data Warehouse
services: sql-data-warehouse
ms.custom: vs-azure
ms.workload: azure-vs
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: consume
ms.date: 04/05/2019
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: f67c2a4547ee923e5c1b49302c38693e9ffe87c4
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "61474245"
---
# <a name="install-visual-studio-and-ssdt-for-sql-data-warehouse"></a>Instalace sady Visual Studio a SSDT pro SQL Data Warehouse
Vývoj aplikací pro SQL Data Warehouse pomocí sady Visual Studio 2017. Visual Studio 2019 SSDT není aktuálně podporována pro SQL Data Warehouse. 

Pomocí sady Visual Studio s rozšířením SSDT vám umožňuje použít Průzkumník objektů systému SQL Server vizuálně zkoumat tabulky, zobrazení, uložené procedury a celou řadu dalších objektů ve službě SQL Data Warehouse také spouštět dotazy.

> [!NOTE]
> SQL Data Warehouse zatím nepodporuje databázové projekty sady Visual Studio. Pokud chcete dostávat pravidelné aktualizace týkající se této funkce, prosím Hlasujte o nich [UserVoice].
> 
> 

## <a name="step-1-install-visual-studio"></a>Krok 1: Instalace sady Visual Studio
Použijte tyto odkazy ke stažení a instalaci sady Visual Studio. Pokud už máte sadu Visual Studio 2013 nebo novější, můžete přeskočit ke kroku 2, instalaci rozšíření SSDT.

1. [Stáhněte si Visual Studio][].
2. Postupujte podle [instalaci sady Visual Studio] [ Installing Visual Studio] Průvodce na webu MSDN a zvolte výchozí konfigurace.

## <a name="step-2-install-ssdt"></a>Krok 2: Instalace rozšíření SSDT
Pokud chcete nainstalovat rozšíření SSDT pro Visual Studio, nejprve zkontrolujte dostupnost aktualizace SSDT ze sady Visual Studio pomocí následujících kroků.

1. V sadě Visual Studio klikněte na **nástroje** / **rozšíření a aktualizace...** / **Aktualizace**
2. Vyberte **Aktualizace produktu** a potom vyhledejte položku **Microsoft SQL Server Update for database tooling** (Aktualizace Microsoft SQL Serveru pro databázové nástroje).

Pokud se aktualizace nenajde, měli byste mít nainstalovanou nejnovější verzi.  Pokud chcete ověřit, že je rozšíření SSDT nainstalované, klikněte na **Nápověda** / **O sadě Microsoft Visual Studio** a vyhledejte v seznamu položku SQL Server Data Tools. Pokud možnost instalace není k dispozici v sadě Visual Studio, případně můžete navštívit [si rozšíření SSDT Stáhnout] [ SSDT Download] stránky stáhnout a nainstalovat ručně.

## <a name="next-steps"></a>Další postup
Teď, když máte nejnovější verzi rozšíření SSDT, budete chtít [připojení] [ connect] do služby SQL Data Warehouse.

<!--Anchors-->

<!--Image references-->

<!--Articles-->
[connect]: ./sql-data-warehouse-query-visual-studio.md

<!--Other-->
[Stáhněte si Visual Studio]: https://www.visualstudio.com/downloads/
[Installing Visual Studio]: https://msdn.microsoft.com/library/e2h7fzkw.aspx
[SSDT Download]: https://msdn.microsoft.com/library/mt204009.aspx
[UserVoice]: https://feedback.azure.com/forums/307516-sql-data-warehouse/suggestions/13313247-database-project-from-visual-studio-to-support-azu
