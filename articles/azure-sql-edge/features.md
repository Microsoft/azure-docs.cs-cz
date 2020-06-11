---
title: Podporované funkce Edge SQL Azure (Preview)
description: Seznamte se s podrobnostmi o funkcích, které podporuje Azure SQL Edge (Preview).
keywords: Seznámení s SQL Edgem, co je SQL Edge, Přehled SQL Edge
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 05/19/2020
ms.openlocfilehash: 34d2ba05b00ab92066bc7fa3ccd0b7b2aa59e15c
ms.sourcegitcommit: eeba08c8eaa1d724635dcf3a5e931993c848c633
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/10/2020
ms.locfileid: "84669633"
---
# <a name="supported-features-of-azure-sql-edge-preview"></a>Podporované funkce Edge SQL Azure (Preview) 

Azure SQL Edge je založený na nejnovější verzi Microsoft SQL Server databázového stroje v systému Linux. Kromě některých funkcí, které nejsou v současnosti podporované nebo dostupné v SQL Server 2019 na Linux (nebo v SQL Server ve Windows) podporuje také podmnožinu funkcí podporovaných v SQL Server 2019 na Linux.

Úplný seznam funkcí podporovaných v SQL Server on Linux najdete v tématu [edice a podporované funkce SQL Server 2019 na platformě Linux](https://docs.microsoft.com/sql/linux/sql-server-linux-editions-and-components-2019). Edice a podporované funkce SQL Server ve Windows najdete v tématu [edice a podporované funkce SQL Server 2019 (15. x)](https://docs.microsoft.com/sql/sql-server/editions-and-components-of-sql-server-version-15).

> [!NOTE]
> Azure SQL Edge je momentálně ve verzi Preview a takovým způsobem by se neměl používat v produkčních prostředích. Microsoft může doporučit spuštění Azure SQL Edge v produkčním prostředí v závislosti na ověřování nasazení a scénářích použití.

## <a name="azure-sql-edge-editions"></a>Edice Azure SQL Edge

Azure SQL Edge je k dispozici ve dvou různých edicích nebo softwarových plánech. Tyto edice mají stejné sady funkcí a liší se jenom v souvislosti s jejich právy na používání a velikostí paměti a jader, ke kterým mají přístup v hostitelském systému.

   |**Plán**  |**Popis**  |
   |---------|---------|
   |Vývojář Azure SQL Edge  |  Jenom pro vývoj. Každý kontejner pro vývojáře Azure SQL Edge je omezený na až 4 jádra a 32 GB paměti.  |
   |Azure SQL Edge    |  Pro produkční prostředí. Každý kontejner Edge SQL Azure je omezený na až 8 jader a 64 GB paměti.  |

## <a name="operating-system"></a>Operační systém

Kontejnery Azure SQL Edge jsou aktuálně založené na Ubuntu 16,04 a jsou podporované jenom pro spouštění v hostitelích Docker se systémem Ubuntu 16,04 (doporučeno) nebo Ubuntu 18,04. Azure SQL Edge můžete spustit taky na jiných hostitelích operačního systému. Můžete ho například spustit na jiných distribucích systému Linux nebo ve Windows (pomocí Docker CE nebo Docker EE). Upozorňujeme však, že společnost Microsoft tyto konfigurace nerozsáhle netestovala.

Azure SQL Edge se momentálně podporuje jenom pro nasazení prostřednictvím Azure IoT Edge. Další informace najdete v tématu [Azure IoT Edge podporovaných systémech](https://docs.microsoft.com/azure/iot-edge/support).

Doporučená konfigurace pro provoz Azure SQL Edge ve Windows je konfigurace virtuálního počítače s Ubuntu na hostiteli Windows a následném spuštění Azure SQL Edge v rámci virtuálního počítače se systémem Linux.

## <a name="hardware-support"></a>Hardwarová podpora

Azure SQL Edge vyžaduje 64 procesor, který může být od společnosti Intel, AMD nebo ARM s minimálně jedním procesorem a 1 GB paměti RAM na hostiteli. I když se nároky na paměť při spuštění v Azure SQL Edge blíží až 500 MB, je další paměť nutná pro ostatní IoT Edge moduly běžící na hraničním zařízení.

## <a name="azure-sql-edge-components"></a>Komponenty Edge Azure SQL

Azure SQL Edge podporuje jenom databázový stroj. Nezahrnuje podporu pro další součásti, které jsou k dispozici v SQL Server 2019 ve Windows nebo s SQL Server 2019 v systému Linux. Konkrétně Azure SQL Edge nepodporuje SQL Server komponenty jako Analysis Services, Reporting Services, Integration Services, Master Data Services, Machine Learning Services (in-Database) a Machine Learning Server (samostatně).

## <a name="supported-features"></a>Podporované funkce

Kromě podpory podmnožiny funkcí SQL Server on Linux zahrnuje Azure SQL Edge podporu pro následující nové funkce: 

- Streamování SQL, které je založené na stejném stroji, který plní Azure Stream Analytics, poskytuje možnosti streamování dat v reálném čase v Azure SQL Edge. 
- Volání funkce T-SQL `Date_Bucket` pro analýzu dat Time-Series.
- Možnosti strojového učení prostřednictvím modulu runtime ONNX, který je součástí modulu SQL.

## <a name="unsupported-features"></a>Nepodporované funkce

Následující seznam obsahuje SQL Server 2019 o funkcích systému Linux, které aktuálně nejsou podporovány v hraničních dotazech Azure SQL.

| Oblast | Nepodporovaná funkce nebo služba |
|-----|-----|
| **Návrh databáze** | OLTP v paměti a související příkazy DDL a funkce jazyka Transact-SQL, zobrazení katalogu a zobrazení dynamické správy. |
| &nbsp; | `HierarchyID`datový typ a související příkazy jazyka DDL a funkce jazyka Transact-SQL, zobrazení katalogu a zobrazení dynamické správy. |
| &nbsp; | `Spatial`datový typ a související příkazy jazyka DDL a funkce jazyka Transact-SQL, zobrazení katalogu a zobrazení dynamické správy. |
| &nbsp; | Stretch DB a souvisejících příkazů DDL a funkcí jazyka Transact-SQL, zobrazení katalogu a zobrazení dynamické správy. |
| &nbsp; | Fulltextové indexy a hledání a související příkazy jazyka DDL a funkce jazyka Transact-SQL, zobrazení katalogu a zobrazení dynamické správy.|
| &nbsp; | `FileTable`, `FILESTREAM` a související příkazy DDL a funkce jazyka Transact-SQL, zobrazení katalogu a zobrazení dynamické správy.|
| **Databázový stroj** | Umístění. Všimněte si, že můžete nakonfigurovat Azure SQL Edge jako nabízený předplatitel replikační topologie. |
| &nbsp; | PolyBase. Všimněte si, že můžete nakonfigurovat Azure SQL Edge jako cíl pro externí tabulky v základu. |
| &nbsp; | Jazykové rozšíření přes Java a Spark. |
| &nbsp; | Integrace služby Active Directory. |
| &nbsp; | Snímky databáze. |
| &nbsp; | Podpora pro trvalou paměť. |
| &nbsp; | Microsoft DTC (Distributed Transaction Coordinator). |
| &nbsp; | Správce prostředků a zásady správného řízení prostředků v/v. |
| &nbsp; | Rozšíření fondu vyrovnávací paměti. |
| &nbsp; | Distribuovaný dotaz s připojeními třetích stran. |
| &nbsp; | Propojené servery. |
| &nbsp; | Systémové rozšířené uložené procedury (například `XP_CMDSHELL` ). |
| &nbsp; | Sestavení CLR a související příkazy DDL a funkce jazyka Transact-SQL, zobrazení katalogu a zobrazení dynamické správy. |
| &nbsp; | Funkce T-SQL závislé na CLR, například `ASSEMBLYPROPERTY` , `FORMAT` , `PARSE` a `TRY_PARSE` . |
| &nbsp; | Zobrazení katalogu data a času závislá na CLR, funkce a klauzule dotazu. |
| &nbsp; | Rozšíření fondu vyrovnávací paměti. |
| &nbsp; | Databázový e-mail. |
| **Agent SQL Server** |  Subsystémy: CmdExec, PowerShell, čtečka front, SSIS, SSAS a SSRS. |
| &nbsp; | Generoval. |
| &nbsp; | Spravovaná záloha. |
| **Vysoká dostupnost** | Skupiny dostupnosti Always On.  |
| &nbsp; | Základní skupiny dostupnosti. |
| &nbsp; | Instance clusteru s podporou převzetí služeb při selhání vždy. |
| &nbsp; | Zrcadlení databáze. |
| &nbsp; | Horké přidání paměti a procesoru. |
| **Zabezpečení** | Rozšiřitelná Správa klíčů. |
| &nbsp; | Integrace služby Active Directory.|
| &nbsp; | Podpora pro zabezpečení enclaves.|
| **Služby** | SQL Server Browser. |
| &nbsp; | Machine Learning prostřednictvím R a Pythonu. |
| &nbsp; | StreamInsight. |
| &nbsp; | Analysis Services. |
| &nbsp; | Služba Reporting Services. |
| &nbsp; | Služby Data Quality Services. |
| &nbsp; | Master Data Services. |
| &nbsp; | Distribuované přehrávání. |
| **Možnosti správy** | Řídicí bod Stavový nástroj systému SQL Server. |

## <a name="next-steps"></a>Další kroky

- [Nasazení Edge Azure SQL](deploy-portal.md)
- [Konfigurace Edge Azure SQL](configure.md)
- [Připojení k instanci Azure SQL Edge pomocí SQL Server klientských nástrojů](connect.md)
- [Zálohování a obnovení databází v Azure SQL Edge](backup-restore.md)
