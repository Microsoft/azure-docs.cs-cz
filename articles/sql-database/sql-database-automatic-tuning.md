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
ms.openlocfilehash: 9ad36d37fef4c1ee05e31098b145b0264b6440ca
ms.sourcegitcommit: 2ad510772e28f5eddd15ba265746c368356244ae
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/28/2018
ms.locfileid: "43126436"
---
# <a name="automatic-tuning-in-azure-sql-database"></a>Automatické ladění ve službě Azure SQL Database

Azure automatického ladění SQL Database poskytuje nejvyšší výkon a stabilní verze úlohy prostřednictvím průběžné výkon ladění založené na AI a strojové učení.

Automatické ladění je plně spravované inteligentní výkonu Služba, která používá integrované inteligentní funkce pro průběžné monitorování dotazů na databázi a automaticky zlepšuje jejich výkon. Toho můžete dosáhnout dynamicky přizpůsobení databáze změna úloh a použití doporučení pro vyladění. Automatické ladění vodorovně učí ze všech databází v Azure pomocí AI a dynamicky zlepšuje její optimalizačních akcí. Čím delší spuštění služby Azure SQL Database s automatickým laděním na, tím lépe funguje.

Azure SQL Database automatické ladění může být jeden z vašich nejdůležitějších funkce umožňující k zajištění stabilních špičkovým výkonem databázové úlohy.

## <a name="what-can-automatic-tuning-do-for-you"></a>Co je automatické ladění? pro vás

- Ladění výkonu automatizované databází Azure SQL Database
- Automatické ověření zvýšení výkonu
- Automatické vrácení zpět a samostatné opravy
- Historie optimalizace
- Ladění skriptů T-SQL akce pro ruční nasazení
- Sledování výkonu aktivní úlohy
- Horizontální navýšení kapacity schopností na stovky tisíc databází
- Pozitivní dopad prostředky DevOps a celkové náklady na vlastnictví

## <a name="safe-reliable-and-proven"></a>Bezpečné, spolehlivé a osvědčené

Optimalizace operací u databází Azure SQL Database jsou plně bezpečné pro výkon nejintenzivnější úloh. V systému byly navržené s opatrností nechcete v konfliktu s úlohami uživatele. Doporučení automatického ladění se aplikují pouze na doby nízkého využití. Systém může také dočasně zakázat automatické optimalizace operací pro ochranu na výkon úloh. V takovém případě se na webu Azure portal zobrazí zpráva "Zakázáno systém". Automatické ladění jde o úlohy s nejvyšší prioritou prostředků.

Automatické ladění mechanismy jsou až po zralé a byla dovedete na několik milionů databáze spuštěné v Azure. Automatické ladění operace použije automaticky ověření k zajištění, že není kladná zlepšování na výkon úloh. Doporučení s nižším výkonem jsou dynamicky zjistila a o tom bezodkladně informuje vrátí zpět. Prostřednictvím optimalizace historie zaznamenán existuje vymazat trasování ladění vylepšení v každé databázi SQL Azure. 

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
 2. **DROP INDEX** -identifikuje každý den redundantní a duplicitních indexů a indexy, které nebyly použity po dlouhou dobu (> 90 dnů). Všimněte si, že tato možnost není kompatibilní s aplikací s použitím pomocné parametry přepnutí a index oddílu.
 3. **VYNUCENÍ POSLEDNÍHO dobrého plánu** -identifikuje dotazů SQL pomocí plán provádění, která je nižší než předchozí dobrým plánem a dotazy s posledního známého dobrého plánu místo nižším plánu.

Automatické ladění identifikuje **CREATE INDEX**, **DROP INDEX**, a **platnost poslední dobré plánování** doporučení, která můžete optimalizovat výkon své databáze a zobrazí je v [Webu azure portal](sql-database-advisor-portal.md)a zpřístupňuje je prostřednictvím [T-SQL](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-set-options?view=azuresqldb-current) a [rozhraní REST API](https://docs.microsoft.com/rest/api/sql/serverautomatictuning).

Můžete buď ručně provést doporučení pro vyladění pomocí portálu nebo můžete nechat automatické ladění autonomně platí doporučení pro vyladění za vás. Výhody autonomně umožněte systému platí doporučení pro vyladění je, že v takovém případě se automaticky ověřuje existuje kladné zisk na výkon úloh nebo jinak pokud regrese se detekuje automaticky změní zpět doporučení pro ladění. Všimněte si, že v případě dotazy ovlivněné doporučení, které nejsou provedeny často pro optimalizaci, ověření fáze může trvat až 72 hodin záměrné. V případě, že jsou ručně zavede ladění doporučení, automatické výkon ověřování a mechanismy opačný nejsou k dispozici.

Možnosti automatického ladění můžete nezávisle na sobě povolit nebo zakázat na databázi, nebo mohou být konfigurovány na logických serverů a použít na každou databázi, která dědí nastavení ze serveru. Logické servery můžete dědit Azure výchozí hodnoty pro nastavení automatického ladění. Výchozích nastavení Azure v tuto chvíli jsou nastaveny na FORCE_LAST_GOOD_PLAN je povoleno, je povoleno CREATE_INDEX a DROP_INDEX je zakázaná.

Konfigurace automatického ladění možnosti na serveru a nastavení pro databáze, které patří do nadřazeného serveru je doporučená metoda pro konfiguraci automatického ladění jako zjednodušuje správu možnosti automatického ladění pro velký počet databází.

## <a name="next-steps"></a>Další postup

- Pokud chcete povolit automatické ladění ve službě Azure SQL Database a spravovat vaše úlohy, naleznete v tématu [povolení automatického ladění](sql-database-automatic-tuning-enable.md).
- Chcete-li ručně zkontrolovat a použít automatické optimalizace doporučení, najdete v článku [vyhledání a použití doporučení k výkonu](sql-database-advisor-portal.md).
- Další informace o použití T-SQL pro použití a zobrazit automatické ladění doporučení, najdete v článku [spravovat automatické ladění prostřednictvím T-SQL](https://azure.microsoft.com/blog/automatic-tuning-introduces-automatic-plan-correction-and-t-sql-management/).
- Další informace o vytváření e-mailová oznámení pro doporučení automatického ladění, naleznete v tématu [e-mailová oznámení pro automatické ladění](sql-database-automatic-tuning-email-notifications.md).
- Další informace o integrovaných inteligentních funkcí používaných pro automatické ladění, naleznete v tématu [umělé inteligence vylaďuje databází Azure SQL Database](https://azure.microsoft.com/blog/artificial-intelligence-tunes-azure-sql-databases/).
- Další informace o tom, jak automatické ladění funguje v Azure SQL Database a SQL serveru 2017, najdete v článku [automatické ladění SQL serveru](https://docs.microsoft.com/sql/relational-databases/automatic-tuning/automatic-tuning).
