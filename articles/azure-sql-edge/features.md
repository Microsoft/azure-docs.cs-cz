---
title: Podporované funkce Edge SQL Azure (Preview)
description: Seznamte se s podrobnostmi o funkcích podporovaných Azure SQL Edge (Preview).
keywords: Seznámení s SQL Edgem, co je SQL Edge, Přehled SQL Edge
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 05/19/2020
ms.openlocfilehash: e3bac1cbb0e57f9be4a489207f2fe8b30b5d457e
ms.sourcegitcommit: f1132db5c8ad5a0f2193d751e341e1cd31989854
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/31/2020
ms.locfileid: "84233217"
---
# <a name="supported-features-of-azure-sql-edge-preview"></a>Podporované funkce Edge SQL Azure (Preview) 

Tento článek obsahuje podrobné informace o funkcích, které Azure SQL Edge podporuje. Azure SQL Edge je postaven na nejnovější verzi Microsoft SQL Server databázového stroje v systému Linux a podporuje podmnožinu funkcí podporovaných v SQL Server 2019 pro Linux, kromě některých funkcí, které aktuálně nejsou podporovány nebo nejsou k dispozici v SQL Server 2019 v systému Linux nebo Windows. Úplný seznam funkcí podporovaných v SQL Server on Linux najdete v tématu [edice a podporované funkce SQL Server 2019 na platformě Linux](https://docs.microsoft.com/sql/linux/sql-server-linux-editions-and-components-2019). Edice a podporované funkce SQL Server ve Windows najdete v [části edice a podporované funkce SQL Server 2019 (15. x)](https://docs.microsoft.com/sql/sql-server/editions-and-components-of-sql-server-version-15).

> [!NOTE]
> Azure SQL Edge je momentálně ve verzi Preview a takovým způsobem by se neměl používat v produkčním prostředí. Microsoft může po ověření nasazení a scénářů použití doporučit spuštění Azure SQL Edge v produkčním prostředí.

## <a name="azure-sql-edge-editions"></a>Edice Azure SQL Edge

Azure SQL Edge je k dispozici ve dvou různých edicích nebo softwarových plánech. Tyto edice mají stejné sady funkcí a liší se jenom v souvislosti s jejich právy na používání a množstvím procesorů nebo paměti, ke kterým mají přístup v hostitelském systému.

   |**Plán**  |**Popis**  |
   |---------|---------|
   |Vývojář Azure SQL Edge  |  Jenom vývojová SKU, každý kontejner pro vývojáře Azure SQL Edge je omezený na až 4 jádra a 32 GB paměti.  |
   |Azure SQL Edge    |  Produkční skladová jednotka, každý kontejner Azure SQL Edge je omezený na až 8 jader a 64 GB paměti.  |

## <a name="operating-system"></a>Operační systém

Kontejnery Azure SQL Edge jsou aktuálně založené na Ubuntu 16,04 a jsou podporované jenom pro spouštění na hostitelích Docker se systémem Ubuntu 16,04 (doporučeno) nebo Ubuntu 18,04. Azure SQL Edge můžete také spustit na jiných hostitelích operačního systému, například v jiných distribucích systému Linux nebo ve Windows (pomocí Docker CE nebo Docker EE), ale tyto konfigurace nejsou v Microsoftu důkladně testovány.

Azure SQL Edge se momentálně podporuje jenom pro nasazení prostřednictvím Azure IoT Edge. Další informace o podporovaných systémech pro Azure IoT Edge najdete v [Azure IoT Edge podporovaných systémech](https://docs.microsoft.com/azure/iot-edge/support).

Doporučená konfigurace pro provoz Azure SQL Edge ve Windows je konfigurace virtuálního počítače s Ubuntu na hostiteli Windows a následném spuštění SQL Edge v rámci virtuálního počítače se systémem Linux.

## <a name="hardware-support"></a>Hardwarová podpora

Azure SQL Edge vyžaduje 64 procesor, který může být od společnosti Intel, AMD nebo ARM s minimálně jedním procesorem a 1 GB paměti RAM na hostiteli. I když se nároky na paměť při spuštění v Azure SQL Edge blíží až 500 MB, je další paměť nutná pro ostatní IoT Edge moduly běžící na hraničním zařízení.

## <a name="azure-sql-edge-components"></a>Komponenty Edge Azure SQL

Azure SQL Edge podporuje jenom databázový stroj a nezahrnuje podporu pro další součásti dostupné v SQL Server 2019 ve Windows nebo s SQL Server 2019 v systému Linux. Konkrétně Azure SQL Edge nepodporuje SQL Server komponenty jako Analysis Services, Reporting Services, integrační služby, Master Data Services, Machine Learning Services (in-Database) a Machine Learning Server (samostatně).

## <a name="supported-features"></a>Podporované funkce

Kromě podpory podmnožiny funkcí SQL Server on Linux zahrnuje Azure SQL Edge podporu následujících nových funkcí. 

- Streamování SQL, které je založené na stejném stroji, který plní Azure Stream Analytics, poskytuje možnosti streamování dat v reálném čase v Azure SQL Edge. 
- Nové volání funkce T-SQL Date_Bucket pro analýzu dat Time-Series.
- Machine Learning možnosti prostřednictvím modulu runtime ONNX, který je součástí modulu SQL.

## <a name="sql-server-on-linux-features-not-supported-in-azure-sql-edge"></a>Nepodporované funkce SQL Server on Linux v Azure SQL Edge

Níže uvedený seznam obsahuje funkce SQL Server 2019 pro Linux, které se v Azure SQL Edge aktuálně nepodporují.

| Oblast | Nepodporovaná funkce nebo služba |
|-----|-----|
| **Návrh databáze** | OLTP v paměti a související příkazy DDL a funkce jazyka Transact-SQL, zobrazení katalogu a zobrazení dynamické správy |
| &nbsp; | HierarchyID data Type a související příkazy DDL a funkce jazyka Transact-SQL, zobrazení katalogu a zobrazení dynamické správy |
| &nbsp; | Prostorový datový typ a související příkazy DDL a funkce jazyka Transact-SQL, zobrazení katalogu a zobrazení dynamické správy |
| &nbsp; | Funkce Stretch DB a související příkazy jazyka DDL a funkce jazyka Transact-SQL, zobrazení katalogu a zobrazení dynamické správy |
| &nbsp; | Fulltextové indexy a hledání a související příkazy DDL a funkce jazyka Transact-SQL, zobrazení katalogu a zobrazení dynamické správy|
| &nbsp; | Soubory FileTable, FILESTREAM a související příkazy DDL a funkce jazyka Transact-SQL, zobrazení katalogu a zobrazení dynamické správy|
| **Databázový stroj** | Umístění. Azure SQL Edge se ale dá nakonfigurovat jako nabízený odběratel replikační topologie. |
| &nbsp; | PolyBase. Azure SQL Edge se ale dá nakonfigurovat jako cíl pro externí tabulky v základu. |
| &nbsp; | Rozšiřitelnost jazyků přes Java a Spark |
| &nbsp; | Integrace Active Directory |
| &nbsp; | Snímky databáze |
| &nbsp; | Podpora pro trvalou paměť |
| &nbsp; | DTC (Distributed Transaction Coordinator) Microsoftu |
| &nbsp; | Správce prostředků a zásady správného řízení prostředků v/v |
| &nbsp; | Rozšíření fondu vyrovnávací paměti |
| &nbsp; | Distribuovaný dotaz s připojeními třetích stran |
| &nbsp; | Odkazované servery |
| &nbsp; | Systémové rozšířené uložené procedury (XP_CMDSHELL atd.) |
| &nbsp; | Sestavení CLR a související příkazy jazyka DDL a funkce jazyka Transact-SQL, zobrazení katalogu a zobrazení dynamické správy |
| &nbsp; | Funkce T-SQL závislé na CLR, jako je ASSEMBLYPROPERTY, FORMAT, PARSE, TRY_PARSE |
| &nbsp; | Zobrazení katalogu data a času závislá na CLR, funkce a klauzule dotazu |
| &nbsp; | Rozšíření fondu vyrovnávací paměti |
| &nbsp; | Databázová pošta |
| **Agent SQL Server** |  Subsystémy: CmdExec, PowerShell, čtečka front, SSIS, SSAS, SSRS |
| &nbsp; | Výstrahy |
| &nbsp; | Spravované zálohování |
| **Vysoká dostupnost** | Skupiny dostupnosti Always On  |
| &nbsp; | Základní skupiny dostupnosti |
| &nbsp; | Instance clusteru s podporou převzetí služeb při selhání vždy |
| &nbsp; | Zrcadlení databáze |
| &nbsp; | Horké přidání paměti a procesoru |
| **Zabezpečení** | Rozšiřitelná Správa klíčů |
| &nbsp; | Integrace služby Active Directory|
| &nbsp; | Podpora pro zabezpečení Enclaves|
| **Služby** | SQL Server Browser |
| &nbsp; | Machine Learning prostřednictvím R a Pythonu |
| &nbsp; | StreamInsight |
| &nbsp; | Analysis Services |
| &nbsp; | Reporting Services |
| &nbsp; | Služba Data Quality Services |
| &nbsp; | Master Data Services |
| &nbsp; | Distribuované přehrávání |
| **Možnosti správy** | Řídicí bod Stavový nástroj systému SQL Server |

## <a name="next-steps"></a>Další kroky

- [Nasazení Edge Azure SQL](deploy-portal.md)
- [Konfigurace Edge Azure SQL](configure.md)
- [Připojení k instanci Azure SQL Edge pomocí SQL Server klientských nástrojů](connect.md)
- [Zálohování a obnovení databází v Azure SQL Edge](backup-restore.md)
