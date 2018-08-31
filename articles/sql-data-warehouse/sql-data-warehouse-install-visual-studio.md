---
title: Instalace sady Visual Studio a SSDT pro SQL Data Warehouse | Dokumentace Microsoftu
description: Instalace sady Visual Studio a SQL Server Development Tools (SSDT) pro Azure SQL Data Warehouse
services: sql-data-warehouse
ms.custom: vs-azure
ms.workload: azure-vs
author: kavithaj
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: consume
ms.date: 04/17/2018
ms.author: kavithaj
ms.reviewer: igorstan
ms.openlocfilehash: ba84b64afb1d5ebcd5ec153787ddc7d0739bd8d8
ms.sourcegitcommit: 1fb353cfca800e741678b200f23af6f31bd03e87
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/30/2018
ms.locfileid: "43307381"
---
# <a name="install-visual-studio-and-ssdt-for-sql-data-warehouse"></a>Instalace sady Visual Studio a SSDT pro SQL Data Warehouse
Pro vývoj aplikací pro SQL Data Warehouse, doporučujeme používat nejnovější verzi sady Visual Studio s nejnovější verzí systému SQL Server Data Tools (SSDT).  Kvůli zpětné kompatibilitě se rovněž podporuje Visual Studio 2013 Update 5 s rozšířením SSDT.  

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

Pokud se aktualizace nenajde, měli byste mít nainstalovanou nejnovější verzi.  Pokud chcete ověřit, že je rozšíření SSDT nainstalované, klikněte na **Nápověda** / **O sadě Microsoft Visual Studio** a vyhledejte v seznamu položku SQL Server Data Tools. Nejnovější verze rozšíření SSDT je 14.0.60525.0. Pokud možnost instalace není k dispozici v sadě Visual Studio, případně můžete navštívit [si rozšíření SSDT Stáhnout] [ SSDT Download] stránky stáhnout a nainstalovat ručně.

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
