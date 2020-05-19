---
title: Konfigurace replikace na Edge Azure SQL (Preview)
description: Přečtěte si o konfiguraci replikace do Azure SQL Edge (Preview).
keywords: ''
services: sql-database-edge
ms.service: sql-database-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 05/19/2020
ms.openlocfilehash: e2b37e0f3ccf5fcebe4723c05d644f2cbb7c1d56
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/19/2020
ms.locfileid: "83596943"
---
# <a name="configure-replication-to-azure-sql-edge-preview"></a>Konfigurace replikace na Edge Azure SQL (Preview) 

Instance Azure SQL Edge se dá nakonfigurovat jako předplatitel nabízených oznámení pro jednosměrnou transakční replikaci nebo replikaci snímků. Instance Azure SQL Edge nemůže fungovat jako Vydavatel nebo distributor pro konfiguraci transakční replikace. Slučovací replikace, replikace P2P, publikování Oracle se u Edge SQL Azure nepodporuje.

## <a name="supported-configurations"></a>**Podporované konfigurace**:
  
- Instance Azure SQL Edge musí být předplatitelem nabízených oznámení pro vydavatele.
- Vydavatel a distributor můžou být buď
   - Instance SQL Server spuštěná v místním prostředí nebo instance SQL Server spuštěná na virtuálním počítači Azure. Další informace najdete v tématu [přehled SQL Server v Azure Virtual Machines](https://azure.microsoft.com/documentation/articles/virtual-machines-sql-server-infrastructure-services/). Instance SQL Server musí používat verzi větší než SQL Server 2016.
   - Instance Azure SQL Database Managed instance. Spravovaná instance může hostovat databáze vydavatelů, distributorů a předplatitelů. Další informace najdete v tématu [replikace s SQL Database Managed instance](https://docs.microsoft.com/azure/sql-database/replication-with-sql-database-managed-instance/).

- Distribuční databáze a agenti replikace nelze umístit do instance Azure SQL Edge.  

> [!NOTE]
> Pokud se pokusíte nakonfigurovat replikaci pomocí nepodporované verze, může dojít k chybě MSSQL_REPL20084 (proces se nemohl připojit k odběrateli) a MSSQL_REPL40532 (nemůže otevřít \< název serveru> požadovaný přihlášením. Přihlášení se nezdařilo.).  

Pokud chcete používat všechny funkce Azure SQL Edge, musíte používat nejnovější verze [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) a [SQL Server datových nástrojů](https://docs.microsoft.com/sql/ssdt/download-sql-server-data-tools-ssdt).  

## <a name="remarks"></a>Poznámky

- Replikaci můžete nakonfigurovat pomocí [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) nebo spuštěním příkazů jazyka Transact-SQL na vydavateli pomocí SQL Server Management Studio nebo [Azure Database Studio](https://docs.microsoft.com/sql/azure-data-studio/download-azure-data-studio) .
- Replikace může pro připojení k instanci Edge Azure SQL použít jenom přihlášení SQL Server ověřování.
- Replikované tabulky musí mít primární klíč.
- Jedna publikace na SQL Server může podporovat Azure SQL Edge i SQL Server (místně i SQL Server ve virtuálních počítačích Azure).  
- Správa replikace, monitorování a řešení potíží se musí provádět z místních SQL Server.  
- Podporují se jenom nabízená předplatná pro Azure SQL Edge.  
- `@subscriber_type = 0`V **Sp_addsubscription** pro Azure SQL Edge se podporuje jenom.  
- Azure SQL Edge nepodporuje replikaci obousměrné, okamžité, aktualizovatelné ani peer-to-peer.
- Azure SQL Edge podporuje pouze podmnožinu funkcí, které jsou k dispozici ve SQL Server nebo Azure SQL Database spravované instanci, protože tento pokus o replikaci databáze (nebo objektů v databázi), který obsahuje jednu nebo více nepodporovaných funkcí, způsobí selhání. Například pokus o replikaci databáze, která obsahuje objekty s prostorovými datovými typy, bude mít za následek chybu. Další informace o funkcích podporovaných přes Azure SQL Edge najdete v tématu [podporované funkce Azure SQL Edge](features.md).

## <a name="scenarios"></a>Scénáře  

### <a name="initializing-reference-data-on-an-edge-instance"></a>Inicializace referenčních dat v instanci Edge

Běžný scénář, kdy může být replikace užitečná, je v případě, že je potřeba inicializovat instanci Edge s referenčními daty, která se v průběhu času mění. Například aktualizace modelů ML na hraniční instanci poté, co byly vyškolené na místní instanci SQL Server.

1. Vytvoření publikace transakční replikace v místní databázi SQL Server.  
2. Na místních SQL Server použijte **Průvodce novým předplatným** nebo příkazy jazyka Transact-SQL k vytvoření nabízeného oznámení pro předplatné Azure SQL Edge.  
3. Replikovanou databázi v Azure SQL Edge můžete inicializovat buď pomocí snímku generovaného agentem snímku a distribuováni a dodaných agentem distribuce, nebo pomocí zálohy databáze od vydavatele. Pokud zálohování databáze obsahuje objekty nebo funkce, které Azure SQL Edge nepodporuje, operace obnovení se nezdaří.

## <a name="limitations"></a>Omezení

Pro odběry Azure SQL Edge nejsou podporované tyto možnosti:

- Kopírovat přidružení skupin souborů  
- Kopírovat schémata dělení tabulky  
- Kopírovat schémata dělení indexů  
- Kopírovat uživatelsky definované statistiky  
- Kopírovat výchozí vazby  
- Kopírovat vazby pravidla  
- Kopírovat fulltextové indexy  
- Kopírovat XSD XML  
- Kopírovat indexy XML  
- Kopírovat oprávnění  
- Kopírovat prostorové indexy  
- Kopírovat filtrované indexy  
- Kopírovat atribut komprese dat  
- Kopírovat atribut zhuštěného sloupce  
- Kopírování datových typů FILESTREAM, hierarchyid nebo prostorových dat.
- Převést hierarchyid na maximální počet datových typů  
- Převod prostorových datových typů na maximum  
- Kopírovat rozšířené vlastnosti  
- Kopírovat oprávnění  

## <a name="examples"></a>Příklady

Vytvořte publikaci a nabízený odběr. Další informace naleznete v tématu:
  
- [Vytvoření publikace](https://docs.microsoft.com/sql/relational-databases/replication/publish/create-a-publication)
- [Vytvořte nabízený odběr](https://docs.microsoft.com/sql/relational-databases/replication/create-a-push-subscription/) pomocí názvu nebo IP serveru Azure SQL Edge jako předplatitele (například **myEdgeinstance, 1433**) a názvu databáze na instanci Azure SQL Edge jako cílovou databázi (například **AdventureWorks**).  

## <a name="see-also"></a>Viz také  

- [Vytvoření publikace](https://docs.microsoft.com/sql/relational-databases/replication/publish/create-a-publication)
- [Vytvoření nabízeného odběru](https://docs.microsoft.com/sql/relational-databases/replication/create-a-push-subscription/)
- [Typy replikace](https://docs.microsoft.com/sql/relational-databases/replication/types-of-replication)
- [Monitorování (replikace)](https://docs.microsoft.com/sql/relational-databases/replication/monitor/monitoring-replication)
- [Inicializovat předplatné](https://docs.microsoft.com/sql/relational-databases/replication/initialize-a-subscription)  


