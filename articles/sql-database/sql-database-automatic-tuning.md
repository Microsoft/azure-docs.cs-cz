---
title: Azure SQL Database – automatické ladění | Dokumentace Microsoftu
description: Azure SQL Database analyzuje dotaz SQL a automaticky se přizpůsobí uživatelské zatížení.
services: sql-database
author: danimir
manager: craigg
ms.service: sql-database
ms.custom: monitor & tune
ms.topic: conceptual
ms.date: 04/01/2018
ms.author: v-daljep
ms.reviewer: carlrab
ms.openlocfilehash: 6ed113d11a0c61620ae4a9d48eee957bb979dc11
ms.sourcegitcommit: 9819e9782be4a943534829d5b77cf60dea4290a2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/06/2018
ms.locfileid: "39526789"
---
# <a name="automatic-tuning-in-azure-sql-database"></a>Automatické ladění ve službě Azure SQL Database

Azure automatického ladění SQL Database nabízí výkon ve špičce a stabilními úlohami prostřednictvím průběžné výkon ladění využívá umělou inteligenci.

Automatické ladění je plně spravovaná služba, která používá integrované inteligentní funkce pro průběžné monitorování dotazů na databázi a automaticky zlepšuje jejich výkon. Toho můžete dosáhnout dynamicky přizpůsobení databáze změna úloh a použití doporučení pro vyladění. Automatické ladění vodorovně učí ze všech databází na Azure prostřednictvím umělé inteligence a dynamicky zlepšuje její optimalizačních akcí. Čím delší spuštění služby Azure SQL Database s automatickým laděním na, tím lépe funguje.

Azure SQL Database automatické ladění může být jeden z vašich nejdůležitějších funkce umožňující k zajištění stabilních špičkovým výkonem úloh.

## <a name="what-can-automatic-tuning-do-for-you"></a>Co je automatické ladění? pro vás

- Ladění výkonu automatizované databází Azure SQL Database
- Automatické ověření zvýšení výkonu
- Automatické vrácení zpět a samostatné opravy
- Ladění historie protokolu
- Ladění skriptů T-SQL akce pro ruční nasazení
- Sledování výkonu aktivní úlohy
- Horizontální navýšení kapacity schopností na stovky tisíc databází
- Pozitivní dopad prostředky DevOps a celkové náklady na vlastnictví

## <a name="safe-reliable-and-proven"></a>Bezpečné, spolehlivé a osvědčené

Optimalizace operací u databází Azure SQL Database jsou plně bezpečné pro výkon nejintenzivnější úloh. V systému byly navržené s opatrností nechcete v konfliktu s úlohami uživatele. Doporučení automatického ladění se aplikují pouze na doby nízkého využití. Systém může také dočasně zakázat automatické optimalizace operací pro ochranu na výkon úloh. V takovém případě se na webu Azure portal zobrazí zpráva "Zakázáno systém". Automatické ladění jde o úlohy s nejvyšší prioritou prostředků.

Automatické ladění mechanismy jsou až po zralé a byla dovedete na stovky tisíc databází, běží na Azure. Automatické ladění operace použije automaticky ověření k zajištění, že není kladná zlepšování na výkon úloh. Doporučení s nižším výkonem jsou dynamicky zjistila a o tom bezodkladně informuje vrátí zpět. Prostřednictvím optimalizace historie protokolu je vymazat trasování ladění vylepšení v každé databázi SQL Azure. 

![Jak funguje automatické ladění](./media/sql-database-automatic-tuning/how-does-automatic-tuning-work.png)

Azure automatického ladění SQL Database je svou logikou základní sdílení s modul automatického ladění SQL serveru. Další technické informace o mechanismu integrované inteligentní funkce, najdete v části [automatické ladění SQL serveru](https://docs.microsoft.com/sql/relational-databases/automatic-tuning/automatic-tuning).

## <a name="use-automatic-tuning"></a>Použít automatické optimalizace

Automatické ladění je potřeba ručně povolit v rámci předplatného. Pokud chcete povolit automatické ladění pomocí webu Azure portal, najdete v článku [povolení automatického ladění](sql-database-automatic-tuning-enable.md).

Automatické ladění může pracovat samostatně prostřednictvím automatické použití doporučení pro vyladění, včetně automatické ověření zvýšení výkonu. 

Pro větší kontrolu může být vypnuto automatické použití doporučení pro optimalizaci a doporučení pro optimalizaci lze ručně aplikovat pomocí webu Azure portal. Je také možné pomocí řešení můžete zobrazit automatizovaných doporučení pro vyladění jenom a použít je ručně pomocí skriptů a nástrojů podle vašeho výběru. 

Přehled funkce automatické optimalizace funguje a typické scénáře využití modelu najdete v tématu vložené video:


> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Improve-Azure-SQL-Database-Performance-with-Automatic-Tuning/player]
>

## <a name="automatic-tuning-options"></a>Možnosti automatického ladění

Možnosti automatického ladění ve službě Azure SQL Database k dispozici jsou:
 1. **Vytvoření indexu** -identifikuje indexy, které může zlepšit výkon vaší úlohy, vytvoří indexy a automaticky ověřuje, že se zvýšil výkon dotazů.
 2. **DROP INDEX** -identifikuje redundantní a duplicitních indexů a indexy, které nebyly použity pro velmi dlouhý časový úsek. Mějte prosím na paměti, že tato možnost není kompatibilní s aplikací s použitím pomocné parametry přepnutí a index oddílu.
 3. **VYNUCENÍ POSLEDNÍHO dobrého plánu** -identifikuje dotazů SQL pomocí plán provádění, která je nižší než předchozí dobrým plánem a dotazy s posledního známého dobrého plánu místo nižším plánu.

Azure SQL Database Určuje **CREATE INDEX**, **DROP INDEX**, a **platnost poslední dobré plánování** doporučení, která můžete optimalizovat vaše databáze a zobrazuje na webu Azure portal. Další informace o identifikaci indexy, které by měla být změněna na [najít doporučení indexu na webu Azure portal](sql-database-advisor-portal.md). Můžete buď ručně provést doporučení pomocí portálu nebo můžete nechat službu Azure SQL Database automaticky používat doporučení, monitorování úloh po provedení změny a ověřte, že doporučení vyšší výkon vašich úloh. 

Možnosti automatického ladění můžete nezávisle na sobě povolit nebo zakázat na databázi, nebo mohou být konfigurovány na logických serverů a použít na každou databázi, která dědí nastavení ze serveru. Logické servery můžete dědit Azure výchozí hodnoty pro nastavení automatického ladění. Výchozích nastavení Azure v tuto chvíli jsou nastaveny na FORCE_LAST_GOOD_PLAN je povoleno, je povoleno CREATE_INDEX a DROP_INDEX je zakázaná.

Konfigurace automatického ladění možnosti na serveru a nastavení pro databáze, které patří do nadřazeného serveru je doporučená metoda pro konfiguraci automatického ladění jako zjednodušuje správu možnosti automatického ladění pro velký počet databází.

## <a name="next-steps"></a>Další postup

- Pokud chcete povolit automatické ladění ve službě Azure SQL Database a spravovat vaše úlohy, naleznete v tématu [povolení automatického ladění](sql-database-automatic-tuning-enable.md).
- Chcete-li ručně zkontrolovat a použít automatické optimalizace doporučení, najdete v článku [vyhledání a použití doporučení k výkonu](sql-database-advisor-portal.md).
- Další informace o vytváření e-mailová oznámení pro doporučení automatického ladění, naleznete v tématu [e-mailová oznámení pro automatické ladění](sql-database-automatic-tuning-email-notifications.md)
- Další informace o integrovaných inteligentních funkcí používaných pro automatické ladění, naleznete v tématu [umělé inteligence vylaďuje databází Azure SQL Database](https://azure.microsoft.com/blog/artificial-intelligence-tunes-azure-sql-databases/).
- Další informace o tom, jak automatické ladění funguje v Azure SQL Database a SQL serveru 2017, najdete v článku [automatické ladění SQL serveru](https://docs.microsoft.com/sql/relational-databases/automatic-tuning/automatic-tuning).
