---
title: Instalace sady Visual Studio a SSDT pro SQL Data Warehouse | Microsoft Docs
description: Instalace sady Visual Studio a SQL Server Development Tools (SSDT) pro Azure SQL Data Warehouse
services: sql-data-warehouse
ms.custom: vs-azure
ms.workload: azure-vs
author: XiaoyuMSFT
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: development
ms.date: 04/05/2019
ms.author: xiaoyul
ms.reviewer: igorstan
ms.openlocfilehash: aa904ac62ce41cc89945ddfdef2e426143e42bb4
ms.sourcegitcommit: 75a56915dce1c538dc7a921beb4a5305e79d3c7a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/24/2019
ms.locfileid: "68479494"
---
# <a name="install-visual-studio-and-ssdt-for-sql-data-warehouse"></a>Instalace sady Visual Studio a SSDT pro SQL Data Warehouse
Pro vývoj aplikací pro SQL Data Warehouse použijte Visual Studio 2019. V současné době není pro SQL Data Warehouse podporovaná aplikace Visual Studio 2019 SSDT. 

Pomocí sady Visual Studio s SSDT můžete použít Průzkumník objektů systému SQL Server k prozkoumávání tabulek, zobrazení, uložených procedur a mnoha dalších objektů vizuálně v SQL Data Warehouse. Umožňuje také spouštět dotazy.

> [!NOTE]
> SQL Data Warehouse zatím nepodporuje databázové projekty sady Visual Studio. Pokud chcete dostávat pravidelné aktualizace této funkce, Hlasujte prosím na [UserVoice].
> 
> 

## <a name="step-1-install-visual-studio"></a>Krok 1: Instalace sady Visual Studio
Pomocí těchto odkazů si stáhněte a nainstalujte Visual Studio. Pokud již máte nainstalované Visual Studio 2013 nebo novější, můžete přeskočit ke kroku 2, nainstalovat SSDT.

1. [Stáhnout Visual Studio][].
2. Postupujte podle pokynů průvodce [instalací sady Visual Studio][Installing Visual Studio] na webu MSDN a zvolte výchozí konfigurace.

## <a name="step-2-install-ssdt"></a>Krok 2: Nainstalovat SSDT
Pokud chcete nainstalovat SSDT pro Visual Studio, nejdřív si vyhledáte aktualizaci SSDT ze sady Visual Studio pomocí následujících kroků.

1. V aplikaci Visual Studio klikněte  / na**rozšíření a aktualizace nástrojů...** / **Aktualizovány**
2. Vyberte **Aktualizace produktu** a potom vyhledejte položku **Microsoft SQL Server Update for database tooling** (Aktualizace Microsoft SQL Serveru pro databázové nástroje).

Pokud se aktualizace nenajde, měli byste mít nainstalovanou nejnovější verzi. Pokud chcete ověřit, že je rozšíření SSDT nainstalované, klikněte na **Nápověda** / **O sadě Microsoft Visual Studio** a vyhledejte v seznamu položku SQL Server Data Tools. Pokud možnost nainstalovat není dostupná ze sady Visual Studio, můžete navštívit stránku pro [stažení SSDT][SSDT Download] a stáhnout a nainstalovat SSDT ručně.

## <a name="next-steps"></a>Další postup
Teď, když máte nejnovější verzi SSDT, jste připraveni se [připojit][connect] k vašemu SQL Data Warehouse.

<!--Anchors-->

<!--Image references-->

<!--Articles-->
[connect]: ./sql-data-warehouse-query-visual-studio.md

<!--Other-->
[Stáhnout Visual Studio]: https://www.visualstudio.com/downloads/
[Installing Visual Studio]: https://msdn.microsoft.com/library/e2h7fzkw.aspx
[SSDT Download]: https://msdn.microsoft.com/library/mt204009.aspx
[UserVoice]: https://feedback.azure.com/forums/307516-sql-data-warehouse/suggestions/13313247-database-project-from-visual-studio-to-support-azu
