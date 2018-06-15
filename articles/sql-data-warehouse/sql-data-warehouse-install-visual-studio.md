---
title: Instalace sady Visual Studio a SSDT pro SQL Data Warehouse | Microsoft Docs
description: Instalace sady Visual Studio a SQL Server Development Tools (SSDT) pro Azure SQL Data Warehouse
services: sql-data-warehouse
author: kavithaj
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: consume
ms.date: 04/17/2018
ms.author: kavithaj
ms.reviewer: igorstan
ms.openlocfilehash: a2f01424dedb977000d0e4150f4a31c1a9a21cfb
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2018
ms.locfileid: "31790640"
---
# <a name="install-visual-studio-and-ssdt-for-sql-data-warehouse"></a>Instalace sady Visual Studio a SSDT pro SQL Data Warehouse
K vývoji aplikací pro SQL Data Warehouse, doporučujeme používat nejnovější verzi sady Visual Studio s nejnovější verzi systému SQL Server Data Tools (SSDT).  Kvůli zpětné kompatibilitě se rovněž podporuje Visual Studio 2013 Update 5 s rozšířením SSDT.  

Pomocí sady Visual Studio s rozšířením SSDT vám umožňuje používat Průzkumník objektů systému SQL Server vizuálně zkoumat tabulky, zobrazení, uložené procedury a celou řadu dalších objektů v SQL Data Warehouse a také spouštět dotazy.

> [!NOTE]
> SQL Data Warehouse zatím nepodporuje databázové projekty sady Visual Studio. Pokud chcete dostávat pravidelné aktualizace na tuto funkci, prosím hlasovat o [UserVoice].
> 
> 

## <a name="step-1-install-visual-studio"></a>Krok 1: Instalace sady Visual Studio
Ke stažení a instalaci sady Visual Studio na následujících odkazech. Pokud již máte Visual Studio 2013 nebo novější verzi, můžete přeskočit ke kroku 2, instalaci rozšíření SSDT.

1. [Stáhněte si Visual Studio][].
2. Postupujte podle [instalaci sady Visual Studio] [ Installing Visual Studio] Průvodce na webu MSDN a zvolte výchozí konfigurace.

## <a name="step-2-install-ssdt"></a>Krok 2: Instalace rozšíření SSDT
Pokud chcete nainstalovat rozšíření SSDT pro Visual Studio, nejprve zkontrolujte pro aktualizace SSDT ze sady Visual Studio pomocí následujících kroků.

1. V sadě Visual Studio klikněte na **nástroje** / **rozšíření a aktualizace...** / **Aktualizace**
2. Vyberte **Aktualizace produktu** a potom vyhledejte položku **Microsoft SQL Server Update for database tooling** (Aktualizace Microsoft SQL Serveru pro databázové nástroje).

Pokud se aktualizace nenajde, měli byste mít nainstalovanou nejnovější verzi.  Pokud chcete ověřit, že je rozšíření SSDT nainstalované, klikněte na **Nápověda** / **O sadě Microsoft Visual Studio** a vyhledejte v seznamu položku SQL Server Data Tools. Nejnovější verze rozšíření SSDT je 14.0.60525.0. Pokud není k dispozici ze sady Visual Studio možnost instalace, případně můžete navštívit [si rozšíření SSDT Stáhnout] [ SSDT Download] můžete stáhnout a nainstalovat ručně.

## <a name="next-steps"></a>Další postup
Teď, když máte nejnovější verzi rozšíření SSDT, jste připraveni k [připojit] [ connect] do SQL Data Warehouse.

<!--Anchors-->

<!--Image references-->

<!--Articles-->
[connect]: ./sql-data-warehouse-query-visual-studio.md

<!--Other-->
[Stáhněte si Visual Studio]: https://www.visualstudio.com/downloads/
[Installing Visual Studio]: https://msdn.microsoft.com/library/e2h7fzkw.aspx
[SSDT Download]: https://msdn.microsoft.com/library/mt204009.aspx
[UserVoice]: https://feedback.azure.com/forums/307516-sql-data-warehouse/suggestions/13313247-database-project-from-visual-studio-to-support-azu
