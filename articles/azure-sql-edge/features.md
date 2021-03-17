---
title: Podporované funkce Edge SQL Azure
description: Seznamte se s podrobnostmi o funkcích, které Azure SQL Edge podporuje.
keywords: Seznámení s SQL Edgem, co je SQL Edge, Přehled SQL Edge
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 09/03/2020
ms.openlocfilehash: 19dcbbf102a1d8d21f1b14780ea33816a1677c55
ms.sourcegitcommit: 0ce1ccdb34ad60321a647c691b0cff3b9d7a39c8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/05/2020
ms.locfileid: "93392023"
---
# <a name="supported-features-of-azure-sql-edge"></a>Podporované funkce Edge SQL Azure 

Azure SQL Edge je založený na nejnovější verzi modulu SQL Database. Kromě některých funkcí, které nejsou v současnosti podporované nebo dostupné v SQL Server 2019 na Linux (nebo v SQL Server ve Windows) podporuje také podmnožinu funkcí podporovaných v SQL Server 2019 na Linux.

Úplný seznam funkcí podporovaných v SQL Server on Linux najdete v tématu [edice a podporované funkce SQL Server 2019 na platformě Linux](/sql/linux/sql-server-linux-editions-and-components-2019). Edice a podporované funkce SQL Server ve Windows najdete v tématu [edice a podporované funkce SQL Server 2019 (15. x)](/sql/sql-server/editions-and-components-of-sql-server-version-15).

## <a name="azure-sql-edge-editions"></a>Edice Azure SQL Edge

Azure SQL Edge je k dispozici ve dvou různých edicích nebo softwarových plánech. Tyto edice mají stejné sady funkcí a liší se jenom v souvislosti s jejich právy na používání a velikostí paměti a jader, ke kterým mají přístup v hostitelském systému.

   |**Plán**  |**Popis**  |
   |---------|---------|
   |Vývojář Azure SQL Edge  |  Jenom pro vývoj. Každý kontejner pro vývojáře Azure SQL Edge je omezený na až 4 jádra a 32 GB paměti.  |
   |Azure SQL Edge    |  Pro produkční prostředí. Každý kontejner Edge SQL Azure je omezený na až 8 jader a 64 GB paměti.  |

## <a name="operating-system"></a>Operační systém

Kontejnery Azure SQL Edge jsou založené na Ubuntu 18,04 a jsou podporované jenom pro spouštění na hostitelích Docker, na kterých běží Ubuntu 18,04 LTS (doporučeno) nebo Ubuntu 20,04 LTS. Je možné spustit kontejnery Azure SQL Edge na jiných hostitelích operačního systému, například může běžet na jiných distribucích systému Linux nebo Windows (pomocí Docker CE nebo Docker EE), ale Microsoft to nedoporučuje, protože tato konfigurace nemusí být rozsáhly testována.

Doporučená konfigurace pro provoz Azure SQL Edge ve Windows je konfigurace virtuálního počítače s Ubuntu na hostiteli Windows a následném spuštění Azure SQL Edge v rámci virtuálního počítače se systémem Linux.

Doporučený a podporovaný systém souborů pro Azure SQL Edge je EXT4 a XFS. Pokud se pro zálohování úložiště databáze Azure SQL Edge používají trvalé svazky, musí být podkladový systém souborů hostitele EXT4 a XFS.

## <a name="hardware-support"></a>Hardwarová podpora

Azure SQL Edge vyžaduje 64 procesor (buď x64 nebo ARM64), minimálně jeden procesor a jeden GB RAM na hostiteli. I když se nároky na paměť při spuštění Azure SQL Edge blíží k 450MB, je potřeba další paměť pro ostatní IoT Edge moduly nebo procesy běžící na hraničním zařízení. Skutečná paměť a nároky na procesor pro Azure SQL Edge se budou lišit v závislosti na složitosti zatížení a objemu zpracovávaných dat. Při volbě hardwaru pro vaše řešení doporučuje společnost Microsoft spouštět rozsáhlé testy výkonu, abyste měli jistotu, že jsou splněné požadované charakteristiky výkonu pro vaše řešení.  

## <a name="azure-sql-edge-components"></a>Komponenty Edge Azure SQL

Azure SQL Edge podporuje jenom databázový stroj. Nezahrnuje podporu pro další součásti, které jsou k dispozici v SQL Server 2019 ve Windows nebo s SQL Server 2019 v systému Linux. Konkrétně Azure SQL Edge nepodporuje SQL Server komponenty jako Analysis Services, Reporting Services, Integration Services, Master Data Services, Machine Learning Services (in-Database) a Machine Learning Server (samostatně).

## <a name="supported-features"></a>Podporované funkce

Kromě podpory podmnožiny funkcí SQL Server on Linux zahrnuje Azure SQL Edge podporu pro následující nové funkce: 

- Streamování SQL, které je založené na stejném stroji, který plní Azure Stream Analytics, poskytuje možnosti streamování dat v reálném čase v Azure SQL Edge. 
- Volání funkce T-SQL `Date_Bucket` pro Time-Series analýzu dat.
- Možnosti strojového učení prostřednictvím modulu runtime ONNX, který je součástí modulu SQL.

## <a name="unsupported-features"></a>Nepodporované funkce

Následující seznam obsahuje SQL Server 2019 o funkcích systému Linux, které aktuálně nejsou podporovány v hraničních dotazech Azure SQL.

| Oblast | Nepodporovaná funkce nebo služba |
|-----|-----|
| **Návrh databáze** | OLTP v paměti a související příkazy DDL a funkce jazyka Transact-SQL, zobrazení katalogu a zobrazení dynamické správy. |
| &nbsp; | `HierarchyID` datový typ a související příkazy jazyka DDL a funkce jazyka Transact-SQL, zobrazení katalogu a zobrazení dynamické správy. |
| &nbsp; | `Spatial` datový typ a související příkazy jazyka DDL a funkce jazyka Transact-SQL, zobrazení katalogu a zobrazení dynamické správy. |
| &nbsp; | Stretch DB a souvisejících příkazů DDL a funkcí jazyka Transact-SQL, zobrazení katalogu a zobrazení dynamické správy. |
| &nbsp; | Fulltextové indexy a hledání a související příkazy jazyka DDL a funkce jazyka Transact-SQL, zobrazení katalogu a zobrazení dynamické správy.|
| &nbsp; | `FileTable`, `FILESTREAM` a související příkazy DDL a funkce jazyka Transact-SQL, zobrazení katalogu a zobrazení dynamické správy.|
| **Databázový stroj** | Umístění. Všimněte si, že můžete nakonfigurovat Azure SQL Edge jako nabízený předplatitel replikační topologie. |
| &nbsp; | PolyBase. Všimněte si, že můžete nakonfigurovat Azure SQL Edge jako cíl pro externí tabulky v základu. |
| &nbsp; | Jazykové rozšíření přes Java a Spark. |
| &nbsp; | Integrace služby Active Directory. |
| &nbsp; | Automatické zmenšení databáze. Vlastnost automatického zmenšení databáze lze nastavit pomocí `ALTER DATABASE <database_name> SET AUTO_SHRINK ON` příkazu, tato změna ale nemá žádný vliv. Úkol automatické zmenšování se nespustí proti databázi. Uživatelé mohou stále zmenšit soubory databáze pomocí příkazů DBCC. |
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
| &nbsp; | Service Broker |
| &nbsp; | Správa na základě zásad |
| &nbsp; | Datový sklad pro správu |
| &nbsp; | Databáze s omezením |
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