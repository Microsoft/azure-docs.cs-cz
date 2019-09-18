---
title: Azure SQL Database – automatické ladění | Microsoft Docs
description: Azure SQL Database analyzuje dotaz SQL a automaticky se přizpůsobí uživatelskému zatížení.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: danimir
ms.author: danil
ms.reviewer: jrasnik, carlrab
ms.date: 03/06/2019
ms.openlocfilehash: 9aa9ea1fc8632671ac9ed2c045230b92edaf879d
ms.sourcegitcommit: f209d0dd13f533aadab8e15ac66389de802c581b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/17/2019
ms.locfileid: "71066964"
---
# <a name="automatic-tuning-in-azure-sql-database"></a>Automatické ladění v Azure SQL Database

Azure SQL Database automatické ladění nabízí špičkový výkon a stabilní úlohy prostřednictvím neustálého ladění výkonu na základě AI a strojového učení.

Automatické ladění je plně spravovaná služba inteligentního výkonu, která pomocí integrovaných inteligentních funkcí neustále monitoruje dotazy spouštěné v databázi a automaticky zlepšuje jejich výkon. K tomu je možné využít dynamickou úpravu databáze na měnící se úlohy a použití doporučení pro ladění. Automatické ladění se učí vodorovně ze všech databází v Azure prostřednictvím AI a dynamicky vylepšuje své akce optimalizace. Čím delší je Azure SQL Database běží na automatickém ladění, tím lepší je jeho výkon.

Azure SQL Database automatické ladění může být jednou z nejdůležitějších funkcí, které můžete povolit pro zajištění stabilních a špičkových úloh databáze.

## <a name="what-can-automatic-tuning-do-for-you"></a>Co vám automatické ladění může udělat?

- Automatizované ladění výkonu databází SQL Azure
- Automatizované ověřování nárůstů výkonu
- Automatické vrácení zpět a automatické opravy
- Historie optimalizace
- Optimalizace akce T-SQL skriptů pro ruční nasazení
- Monitorování výkonu proaktivní úlohy
- Možnosti horizontálního navýšení kapacity na stovkách tisíc databází
- Kladný dopad na prostředky DevOps a celkové náklady na vlastnictví

## <a name="safe-reliable-and-proven"></a>Bezpečná, spolehlivá a prověřená

Operace ladění aplikované na databáze SQL Azure jsou plně bezpečné pro výkon nejvýraznějších úloh. Systém je navržený tak, aby nenarušil úlohy uživatelů. Doporučení automatického ladění se aplikují jenom v době nízkého využití. Systém může také dočasně zakázat operace automatického ladění, aby se chránil výkon úloh. V takovém případě se v Azure Portal zobrazí zpráva "zakázáno systémem". Automatické ladění se týká úloh s nejvyšší prioritou prostředku.

Automatické optimalizační mechanismy jsou vyspělé a byly dokonalé na několika milionových databázích běžících na Azure. Použité automatizované operace ladění jsou automaticky ověřovány, aby bylo zajištěno kladné zlepšení výkonu úloh. Jsou dynamicky zjištěna doporučení k převráceným výsledkům a okamžitě se vrátí. V zaznamenané historii ladění existuje jasné trasování optimalizace, které byly provedeny u každé Azure SQL Database. 

![Jak funguje automatické ladění](./media/sql-database-automatic-tuning/how-does-automatic-tuning-work.png)

Azure SQL Database automatické ladění sdílí svou základní logiku s modulem automatického ladění SQL Server. Další technické informace o integrovaném mechanismu inteligentních funkcí najdete v tématu [SQL Server automatické ladění](https://docs.microsoft.com/sql/relational-databases/automatic-tuning/automatic-tuning).

## <a name="use-automatic-tuning"></a>Použít automatické ladění

V předplatném je nutné povolit automatické ladění. Pokud chcete povolit automatické ladění pomocí Azure Portal, přečtěte si téma [Povolení automatického ladění](sql-database-automatic-tuning-enable.md).

Automatické ladění může pracovat samostatně prostřednictvím automatických doporučení pro ladění, včetně automatizovaného ověřování nárůstu výkonu. 

Pro lepší kontrolu je možné vypnout automatické použití doporučení pro ladění a vyladit doporučení můžete ručně provést prostřednictvím Azure Portal. Řešení je také možné použít k zobrazení doporučení automatického ladění a ručně je použít prostřednictvím skriptů a nástrojů podle vašeho výběru. 

Přehled toho, jak funguje automatické ladění a pro typické scénáře použití, najdete v tématu vložené video:


> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Improve-Azure-SQL-Database-Performance-with-Automatic-Tuning/player]
>

## <a name="automatic-tuning-options"></a>Možnosti automatického ladění

K dispozici jsou možnosti automatického ladění Azure SQL Database:

| Možnost automatického ladění | Podpora jedné databáze a databáze ve fondu | Podpora databáze instancí |
| :----------------------------- | ----- | ----- |
| **Vytvořit index** – identifikuje indexy, které mohou zlepšit výkon úloh, vytváří indexy a automaticky ověřuje, zda se zvýšil výkon dotazů. | Ano | Ne | 
| **Drop index** – identifikuje redundantní a duplicitní indexy denně, s výjimkou jedinečných indexů a indexů, které se nepoužily dlouhou dobu (> 90 dnů). Všimněte si, že v tuto chvíli není možnost kompatibilní s aplikacemi, které používají přepínání oddílů a parametry indexu. | Ano | Ne |
| **Vynutit poslední dobrý plán** (automatický opravný plán) – identifikuje dotazy SQL pomocí plánu spuštění, který je pomalejší než předchozí plán, a dotazy pomocí posledního známého funkčního plánu místo naplánovaného plánu. | Ano | Ano |

Automatické ladění identifikuje **vytváření indexů**, **drop index**a **vynutí poslední dobrá** doporučení pro plánování, která mohou optimalizovat výkon databáze a jejich zobrazení v [Azure Portal](sql-database-advisor-portal.md)a zpřístupňuje je prostřednictvím [T-SQL](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-set-options?view=azuresqldb-current) a [. REST API](https://docs.microsoft.com/rest/api/sql/serverautomatictuning). Další informace o tom, jak vynutit poslední dobrý plán a nakonfigurovat možnosti automatického ladění prostřednictvím T-SQL, najdete v tématu [Automatické ladění zavádí automatické opravy plánu](https://azure.microsoft.com/blog/automatic-tuning-introduces-automatic-plan-correction-and-t-sql-management/).

Doporučení pro ladění můžete buď ručně použít na portálu, nebo můžete povolit automatické ladění, a to na základě doporučení pro ladění. Výhodou toho, aby systém mohl samostatně použít doporučení pro vyladění, je to, že se automaticky ověří, že existuje kladný zisk na výkon úlohy a pokud se nezjistí žádné významné zlepšení výkonu, bude automaticky obnoví doporučení pro ladění. Počítejte s tím, že v případě dotazů, které jsou ovlivněné vyladěním doporučení, která se neprovádí často, může trvat až 72 hodin, než se návrh povede.

V případě ručního použití doporučení pro ladění prostřednictvím T-SQL není k dispozici automatické ověřování výkonu a mechanismy zpětného zpracování. Ručně použitá doporučení budou navíc aktivní a budou se zobrazovat v seznamu doporučení 24-48 hodin. předtím, než je systém automaticky stáhne. Pokud byste chtěli doporučení odebrat dřív, můžete ho ručně zahodit.

Možnosti automatického ladění můžou být nezávisle povolené nebo zakázané pro každou databázi, nebo je můžete nakonfigurovat na serverech SQL Database a použít je na všech databázích, které dědí nastavení ze serveru. Servery SQL Database můžou dědit výchozí nastavení Azure pro automatické ladění. Výchozí nastavení Azure v tuto chvíli jsou nastavená na FORCE_LAST_GOOD_PLAN, CREATE_INDEX je povolená a DROP_INDEX je zakázaná.

Konfigurace možností automatického ladění na serveru a dědění nastavení pro databáze patřící k nadřazenému serveru je doporučená metoda pro konfiguraci automatického ladění, protože zjednodušuje správu možností automatického ladění pro velký počet databází.

## <a name="next-steps"></a>Další postup

- Pokud chcete povolit automatické ladění v Azure SQL Database ke správě úloh, přečtěte si téma [Povolení automatického ladění](sql-database-automatic-tuning-enable.md).
- Pokud chcete ručně zkontrolovat a použít doporučení automatického ladění, přečtěte si téma [hledání a použití doporučení pro výkon](sql-database-advisor-portal.md).
- Informace o tom, jak použít T-SQL k použití a zobrazení doporučení pro automatické vyladění, najdete v tématu [Správa automatického ladění prostřednictvím T-SQL](https://azure.microsoft.com/blog/automatic-tuning-introduces-automatic-plan-correction-and-t-sql-management/).
- Informace o vytváření e-mailových oznámení pro doporučení automatického ladění najdete v tématu [e-mailová oznámení pro automatické ladění](sql-database-automatic-tuning-email-notifications.md).
- Další informace o integrovaných inteligentních informacích, které se používají při automatickém ladění, najdete v tématu [umělá logika Tune databáze SQL Azure](https://azure.microsoft.com/blog/artificial-intelligence-tunes-azure-sql-databases/).
- Další informace o tom, jak automatické ladění funguje v Azure SQL Database a SQL Server 2017, najdete v tématu [SQL Server automatické ladění](https://docs.microsoft.com/sql/relational-databases/automatic-tuning/automatic-tuning).
