---
title: Poznámky k verzi pro Azure SQL Edge
description: Poznámky k verzi s podrobnostmi o tom, co je nového nebo co se změnilo v obrázcích Azure SQL Edge.
keywords: Okraj SQL pro poznámky k verzi
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
ms.subservice: ''
author: VasiyaKrishnan
ms.author: vakrishn
ms.reviewer: sstein
ms.date: 11/24/2020
ms.openlocfilehash: e078fb91b3279b6f4321cd51dfb094f82bbe5f14
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "98696375"
---
# <a name="azure-sql-edge-release-notes"></a>Poznámky k verzi Azure SQL Edge 

Tento článek popisuje, co je nového a co se změnilo u každého nového buildu Azure SQL Edge.

## <a name="azure-sql-edge-102"></a>Azure SQL Edge 1.0.2

15.0.2000.1554 sestavení modulu SQL Engine

### <a name="fixes"></a>Opravy

- Streamování T-SQL  
   - Oprava vlastnictví a oprávnění pro objekty streamování
   - Vylepšení protokolování pomocí rotace protokolů a předpon protokolů
   - Azure Stream Analytics: vylepšení protokolování, vylepšení chybových kódů a chybových zpráv v adaptérech 

- ONNX
    - Opravy chyb pro scénář paralelního dotazu a chyby čištění modelu
    - Upgrade ONNX runtime na 1.5.1

## <a name="azure-sql-edge-101"></a>Azure SQL Edge 1.0.1

15.0.2000.1553 sestavení modulu SQL Engine

### <a name="whats-new"></a>Co je nového

- Povoluje Date_Bucket výrazy definované v počítaných sloupcích.

### <a name="fixes"></a>Opravy

- Oprava zásady uchovávání informací pro vyřazení tabulky, která má povolené zásady uchovávání informací s neomezeným časovým limitem
- Podpora DacFx nasazení funkcí streamování a funkcí zásad uchovávání informací 
- Oprava nasazení DacFx pro povolení nasazení z vnořené složky v adrese URL SAS 
- Předpovědět opravu pro podporu dlouhých názvů sloupců v chybových zprávách

## <a name="azure-sql-edge-100-rtm"></a>Azure SQL Edge 1.0.0 (RTM)

15.0.2000.1552 sestavení modulu SQL Engine

### <a name="whats-new"></a>Co je nového
- Image kontejneru založené na Ubuntu 18,04 
- Podpora `IGNORE NULL` a `RESPECT NULL` syntaxe `LAST_VALUE()` `FIRST_VALUE()` funkcí a 
- Vylepšení spolehlivosti pro předpověď pomocí ONNX
- Podpora pro vyčištění na základě zásad uchovávání dat:
   - Podpora kruhové vyrovnávací paměti pro úlohu čištění uchovávání pro řešení potíží
- Podpora nových funkcí: 
   - Rychlé obnovení
   - Automatické ladění dotazů
   - Scénáře paralelního provádění
- Vylepšení pro úsporu energie v režimu snížené spotřeby
- Podpora streamování nové funkce: 
   - [Okna snímků](/stream-analytics-query/snapshot-window-azure-stream-analytics): nový typ okna umožňuje seskupit podle událostí, které dorazí ve stejnou dobu.
   - [TopOne](/stream-analytics-query/topone-azure-stream-analytics) a [CollectTop](/stream-analytics-query/collecttop-azure-stream-analytics) je možné povolit jako analytické funkce. Můžete vracet záznamy seřazené podle zvoleného sloupce. Nemusí být součástí okna. 
   - Vylepšení [MATCH_RECOGNIZE](/stream-analytics-query/match-recognize-stream-analytics). 

### <a name="fixes"></a>Opravy
- Další chybové zprávy a podrobnosti pro řešení potíží s operacemi streamování T-SQL 
- Vylepšení pro zachování výdrže baterie v režimu nečinnosti 
- Oprava stroje pro streamování T-SQL: 
   - Vyčištění pro zastavené úlohy streamování 
   - Opravy pro lokalizaci 
   - Vylepšené zpracování Unicode 
   - Vylepšené ladění pro streamování SQL Edge T-SQL, které uživatelům umožňuje dotazovat se na chyby při selhání úlohy z get_streaming_job
- Vyčištění na základě zásad uchovávání dat: 
    - Opravy pro scénáře vytváření a mazání zásad uchovávání informací
- Opravy pro úlohy časovače na pozadí pro zlepšení úspory energie v režimu snížené spotřeby

### <a name="known-issues"></a>Známé problémy 
- Funkci Date_Bucket T-SQL nelze použít v počítaném sloupci.


## <a name="ctp-23"></a>CTP 2,3
15.0.2000.1549 sestavení modulu SQL Engine
### <a name="whats-new"></a>Co je nového
- Podpora pro vlastní zdroje v funkci Date_Bucket () 
- Podpora souborů BACPAC jako součást nasazení SQL
- Podpora pro vyčištění na základě zásad uchovávání dat:      
   - Podpora DDL pro povolení zásad uchovávání informací 
   - Vyčištění uložených procedur a úlohy čištění na pozadí
   - Rozšířené události pro monitorování úloh čištění

### <a name="fixes"></a>Opravy
- Další chybové zprávy a podrobnosti pro řešení potíží s operacemi streamování T-SQL 
- Vylepšení pro zachování výdrže baterie v režimu nečinnosti 
- Modul streamování T-SQL: 
   - Oprava zablokování zablokování v okně skákající podproudů 
   - Oprava pro zpracování výjimek rozhraní, aby se zajistilo, že bude shromážděna jako chyba s akcí uživatele


## <a name="ctp-22"></a>CTP 2,2
15.0.2000.1546 sestavení modulu SQL Engine
### <a name="whats-new"></a>Co je nového
- Podpora pro nekořenové kontejnery 
- Podpora pro shromažďování dat využití a diagnostiky 
- Aktualizace pro streamování T-SQL:
   - Podpora znaků Unicode pro názvy objektů streamu

### <a name="fixes"></a>Opravy
- Aktualizace pro streamování T-SQL:
   - Vylepšení čištění procesů
   - Vylepšení protokolování a diagnostiky
- Zlepšení výkonu pro přijímání dat

## <a name="ctp-21"></a>CTP 2,1 
15.0.2000.1545 sestavení modulu SQL Engine
### <a name="fixes"></a>Opravy
- Povolené modely PREDIKTIVNÍho ONNXování pro zpracování potíží CPUID v ARM 
- Vylepšené zpracování cesty selhání při spuštění streamování T-SQL
- Opravená hodnota zpoždění meze v metrikách úlohy, když nejsou žádná data.
- Oprava problému s výstupním adaptérem, když má adaptér proměnné schéma mezi dávkami  

## <a name="ctp-20"></a>CTP 2,0 
15.0.2000.1401 sestavení modulu SQL Engine
### <a name="whats-new"></a>Co je nového
-   Název produktu se aktualizoval na *Edge Azure SQL* .
-  Date_Bucket funkce:
    - Podpora pro typy data, času a DateTime
- Předpovědět s ONNX:
    - Požadavek ONNX pro parametr RUNTIME  
- Podpora streamování T-SQL (s omezením verze Preview) 
 
### <a name="known-issues"></a>Známé problémy

- Problém: možné chyby s použitím DACPAC při spuštění kvůli problému s časováním.
- Alternativní řešení: Restartujte SQL Server. V opačném případě se kontejner znovu pokusí použít rozhraní DACPAC.

### <a name="request-support"></a>Požádat o podporu
Na [stránce podpory](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)můžete požádat o podporu. Vyberte následující pole: 
- **Typ problému**: *Technický* 
- **Služba**: *IoT Edge*
- **Typ problému**: *můj problém má vztah k IoT Edge modulu* .
- **Podtyp problému**: *Azure SQL Edge*

:::image type="content" source="media/get-support/support-ticket.png" alt-text="Snímek obrazovky znázorňující vzorový lístek podpory.":::

## <a name="ctp-15"></a>CTP 1,5
15.0.2000.1331 sestavení modulu SQL Engine
### <a name="whats-new"></a>Co je nového
- Date_Bucket funkce:
    - Podpora pro typ DateTimeOffset
- PŘEDPOVĚĎ pomocí ONNX modelů:
  - Podpora NVARCHAR
 
## <a name="ctp-14"></a>CTP 1,4
15.0.2000.1247 sestavení modulu SQL Engine
### <a name="whats-new"></a>Co je nového
-   PŘEDPOVĚĎ pomocí ONNX modelů:
    - VARCHAR – podpora
    - Migrace na modul runtime ONNX verze 1,0 

- Povolené jsou tyto funkce:
  - Podpora funkce CDC
  - Tabulka historie s kompresí
  - Vysoce škálovatelný faktor pro čtení z protokolu vpřed
  - Režim dávky ES pro filtrování směrem dolů
  - Optimalizace pro čtení před
 
## <a name="ctp-13"></a>CTP 1,3
15.0.2000.1147 sestavení modulu SQL Engine
### <a name="whats-new"></a>Co je nového
- Nasazení portálu Azure IoT Portal: 
    - Podpora pro nasazování imagí AMD64 a ARM
    - Podpora vytváření úloh streamování
    - Nasazení DACPAC
- PŘEDPOVĚĎ pomocí ONNX modelů:
    - Podpora číselného typu
- Povolené jsou tyto funkce:
    - Prohledávání směrem dolů do úložiště sloupců
    - Šťastné a kulaté kontroly
- Nároky na paměť a snížení spotřeby paměti
