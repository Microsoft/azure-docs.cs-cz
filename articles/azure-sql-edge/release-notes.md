---
title: Poznámky k verzi pro Azure SQL Edge
description: Poznámky k verzi s podrobnostmi o tom, co je nového nebo co se změnilo v obrázcích Azure SQL Edge
keywords: Okraj SQL pro poznámky k verzi
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
ms.subservice: ''
author: VasiyaKrishnan
ms.author: vakrishn
ms.reviewer: sstein
ms.date: 09/22/2020
ms.openlocfilehash: 3306e51fe2fdbb2586be9684432d8f8c310afe95
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/22/2020
ms.locfileid: "90900597"
---
# <a name="azure-sql-edge-release-notes"></a>Poznámky k verzi Azure SQL Edge 

Tento článek popisuje, co je nového a co se změnilo u každého nového buildu Azure SQL Edge.

## <a name="azure-sql-edge---100-rtm"></a>Azure SQL Edge – 1.0.0 (RTM)

### <a name="sql-engine-build-number---15020001549"></a>Číslo sestavení pro modul SQL Engine – 15.0.2000.1549

### <a name="whats-new"></a>Co je nového?
1. Image kontejnerů založené na Ubuntu 18,04 
2. Podpora `IGNORE NULL` a `RESPECT NULL` syntaxe `LAST_VALUE()` `FIRST_VALUE()` funkcí a. 
3. Vylepšení spolehlivosti pro předpověď pomocí ONNX.
4. Podpora pro vyčištění na základě zásad uchovávání dat.      
   - Podpora optimalizovaného čištění clusterovaných indexů columnstore.
5. Podpora nových funkcí 
   - Rychlé obnovení
   - Automatické ladění dotazů

### <a name="fixes"></a>Opravy
1. Další chybové zprávy a podrobnosti pro řešení potíží s operacemi streamování TSQL. 
2. Vylepšení pro zachování výdrže baterie v režimu nečinnosti. 
3. Opravy modulu streamování TSQL: 
   - Vyčištění pro zastavenou úlohu streamování 
   - Opravy pro vylepšení lokalizace a zpracování kódu Unicode
4. Vyčištění na základě zásad uchovávání dat
   - Opravy pro scénáře vytvoření a vyčištění zásad uchovávání informací.
5. Opravy pro úlohy časovače na pozadí pro zlepšení úspory energie v režimu snížené spotřeby.


## <a name="ctp-23"></a>CTP 2,3
### <a name="sql-engine-build-number---15020001549"></a>Číslo sestavení pro modul SQL Engine – 15.0.2000.1549
### <a name="whats-new"></a>Co je nového?
1. Podpora pro vlastní zdroje v funkci Date_Bucket (). 
2. Podpora souborů BacPac jako součást nasazení SQL.
3. Podpora pro vyčištění na základě zásad uchovávání dat.      
   - Podpora jazyka DDL pro povolení zásad uchovávání informací 
   - Vyčištění uložených procedur a úlohy čištění na pozadí
   - Rozšířené události pro monitorování úloh čištění

### <a name="fixes"></a>Opravy
1. Další chybové zprávy a podrobnosti pro řešení potíží s operacemi streamování TSQL. 
2. Vylepšení pro zachování výdrže baterie v režimu nečinnosti. 
3. Opravy modulu streamování TSQL: 
   - Oprava potíží se zablokovaným vodoznakem v skákající okně podproudů 
   - Oprava zpracování výjimek rozhraní, aby se zajistilo, že se shromáždí jako chyba při akci uživatele


## <a name="ctp-22"></a>CTP 2,2
### <a name="sql-engine-build-number---15020001546"></a>Číslo sestavení pro modul SQL Engine – 15.0.2000.1546
### <a name="whats-new"></a>Co je nového?
1. Podpora pro jiné než kořenové kontejnery 
2. Podpora pro shromažďování dat využití a diagnostiky 
3. Aktualizace pro streamování T-SQL
   - Podpora znaků Unicode pro názvy objektů streamu

### <a name="fixes"></a>Opravy
1. Aktualizace pro streamování T-SQL
   - Vylepšení čištění procesů
   - Vylepšení protokolování a diagnostiky
2. Zlepšení výkonu pro přijímání dat

## <a name="ctp-21"></a>CTP 2,1 
### <a name="sql-engine-build-number---15020001545"></a>Číslo sestavení pro modul SQL Engine – 15.0.2000.1545
### <a name="fixes"></a>Opravy
1. Opravte předpověď s ONNX modely pro zpracování potíží s CPUID v ARM. 
2. Oprava pro zlepšení zpracování cesty selhání při spuštění streamování TSQL 
3. Pokud nejsou k dispozici žádná data, opravte nesprávnou hodnotu prodlevy vodoznaku v metrikách úlohy. 
4. Vyřešte problém s výstupním adaptérem, pokud adaptér obsahuje schéma proměnných mezi dávkami.  

## <a name="ctp-20"></a>CTP 2,0 
### <a name="sql-engine-build-number---15020001401"></a>Číslo sestavení pro modul SQL Engine – 15.0.2000.1401
### <a name="whats-new"></a>Co je nového?
1.  Název produktu se aktualizoval na Azure SQL Edge.
1.  Date_bucket funkce

    i.  Podpora pro datum, čas, typ DateTime
3.  PŘEDPOVĚĎ pomocí ONNX
    
    i.  Pro ONNX se vyžaduje parametr modulu RUNTIME. 
    
4.  Podpora streamování TSQL (omezená verze Preview) 
 
### <a name="known-issues"></a>Známé problémy

1. <b>Problém:</b> Potenciální chyby s použitím DACPAC při spuštění kvůli problému s časováním.

    <b>Alternativní řešení:</b> Restartování SQL Server nebo kontejneru se znovu pokusí použít DACPAC a tento problém by se měl vyřešit.
### <a name="request-support"></a>Požádat o podporu
1. Na [stránce podpory](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)můžete požádat o podporu.

4. Ujistěte se, že jsou vybrána následující pole: 
    * Typ problému – technický 
    * Služba – IoT Edge
    * Typ problému – můj problém má vztah k IoT Edge modulu
    * Podtyp problému – Azure SQL Edge

   ![Vzorový lístek podpory](media/get-support/support-ticket.png)

## <a name="ctp-15"></a>CTP 1,5
### <a name="sql-engine-build-number---15020001331"></a>Číslo sestavení pro modul SQL Engine – 15.0.2000.1331
### <a name="whats-new"></a>Co je nového?
1. Date_bucket funkce
    
    i. Podpora pro typ DateTimeOffset
2. Předpověď s ONNX modely

    i. Podpora nvarchar
 
## <a name="ctp-14"></a>CTP 1,4
### <a name="sql-engine-build-number---15020001247"></a>Číslo sestavení pro modul SQL Engine – 15.0.2000.1247
### <a name="whats-new"></a>Co je nového?
1.  Předpověď s ONNX modely
 
    i.  Varchar – podpora
    
    ii. Migrace na modul runtime ONNX verze 1,0 
2.  Podpora funkcí – jsou povolené následující funkce:

    i.   Podpora funkce CDC

    ii.  Tabulka historie s kompresí

    iii. Vysoký faktor škálování pro čtení protokolu předem

    iv.  Režim dávky ES pro filtrování směrem dolů

    v.   Optimalizace před čtením
 
## <a name="ctp-13"></a>CTP 1,3
### <a name="sql-engine-build-number---15020001147"></a>Číslo sestavení pro modul SQL Engine – 15.0.2000.1147
### <a name="whats-new"></a>Co je nového?
1. Nasazení portálu Azure IOT Portal 

    i.   Podpora pro nasazování imagí AMD64 a ARM

    ii.  Podpora vytváření úloh streamování

    iii. Nasazení DACPAC
2. Předpověď s ONNX modely

    i. Podpora číselného typu
3. Podpora funkcí – jsou povolené následující funkce:

    i.  Prohledávání směrem dolů do úložiště sloupců

    ii. Šťastné a kulaté kontroly
4. Omezení využití paměti a paměti
