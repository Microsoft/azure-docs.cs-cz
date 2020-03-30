---
title: Přehled automatického ladění
description: Azure SQL Database analyzuje dotaz SQL a automaticky se přizpůsobí zatížení uživatelů.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: danimir
ms.author: danil
ms.reviewer: jrasnik, carlrab
ms.date: 03/10/2020
ms.openlocfilehash: d4762c86268353ff0464ff3919250cd86f0038d4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79214103"
---
# <a name="automatic-tuning-in-azure-sql-database"></a>Automatické ladění v Azure SQL Database

Automatické ladění Azure SQL Database poskytuje špičkový výkon a stabilní úlohy prostřednictvím nepřetržitého ladění výkonu na základě ai a strojového učení.

Automatické ladění je plně spravovaná služba inteligentního výkonu, která využívá integrovanou inteligenci k průběžnému sledování dotazů prováděných v databázi a automaticky zlepšuje jejich výkon. Toho je dosaženo prostřednictvím dynamicképřizpůsobení databáze měnící se úlohy a použití doporučení ladění. Automatické ladění se učí horizontálně ze všech databází v Azure až po umělou ai a dynamicky zlepšuje jeho akce ladění. Čím déle je databáze spuštěna s automatickým laděním, tím lépe provádí.

Automatické ladění Azure SQL Database může být jednou z nejdůležitějších funkcí, které můžete povolit a poskytovat stabilní a špičkové databázové úlohy.

## <a name="what-can-automatic-tuning-do-for-you"></a>Co může automatické ladění udělat pro vás

- Automatické ladění výkonu databází Azure SQL
- Automatizované ověřování nárůstu výkonu
- Automatická vrácení zpět a vlastní korekce
- Historie ladění
- Akce ladění Skripty T-SQL pro ruční nasazení
- Proaktivní monitorování výkonu pracovního vytížení
- Škálování možností na stovkách tisíc databází
- Pozitivní dopad na zdroje DevOps a celkové náklady na vlastnictví

## <a name="safe-reliable-and-proven"></a>Bezpečné, spolehlivé a osvědčené

Tuningové operace používané pro databáze v Azure SQL Database jsou plně bezpečné pro výkon vašich nejintenzivnějších úloh. Systém byl navržen s opatrností, aby nezasahoval do zatížení uživatelů. Doporučení automatického ladění se používají pouze v době nízkého využití. Systém může také dočasně zakázat automatické ladění operace k ochraně výkonu pracovního vytížení. V takovém případě se na webu Azure Portal zobrazí zpráva "Zakázáno systémem". Automatické ladění se týká úloh s nejvyšší prioritou prostředků.

Automatické optimalizace mechanismy jsou zralé a byly zdokonaleny na několik milionů databází spuštěných v Azure. Automatické ladění operace použít jsou automaticky ověřeny, aby bylo zajištěno, že je pozitivní zlepšení výkonu pracovního vytížení. Regressed výkon doporučení jsou dynamicky zjištěny a okamžitě vrátit zpět. Prostřednictvím zaznamenané historie ladění existuje jasná trasa vylepšení optimalizace pro každou databázi Azure SQL Database.

![Jak funguje automatické ladění](./media/sql-database-automatic-tuning/how-does-automatic-tuning-work.png)

Automatické ladění Azure SQL Database sdílí svou základní logiku s motorem automatického ladění SERVERU SQL Server. Další technické informace o integrovaném mechanismu inteligence naleznete v tématu [automatické ladění serveru SQL Server](https://docs.microsoft.com/sql/relational-databases/automatic-tuning/automatic-tuning).

Přehled fungování automatického ladění a typické scénáře použití najdete v vloženém videu:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Improve-Azure-SQL-Database-Performance-with-automatic-tuning/player]

## <a name="enable-automatic-tuning"></a>Povolení automatického ladění

Automatické [ladění pro jednu a sdruženou databázi](sql-database-automatic-tuning-enable.md) můžete povolit na webu Azure Portal nebo pomocí příkazu ALTER [DATABASE](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-set-options?view=azuresqldb-current) T-SQL. Automatické ladění pro databáze instancí v nasazení spravované instance pomocí příkazu [ALTER DATABASE](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-set-options?view=azuresqldb-mi-current) T-SQL.

## <a name="automatic-tuning-options"></a>Možnosti automatického ladění

Možnosti automatického ladění dostupné v Azure SQL Database jsou:

| Možnost automatického ladění | Podpora jedné databáze a sdružených databází | Podpora databáze instancí |
| :----------------------------- | ----- | ----- |
| **VYTVOŘIT INDEX** - Identifikuje indexy, které mohou zlepšit výkon úlohy, vytvoří indexy a automaticky ověří, že výkon dotazů se zlepšila. | Ano | Ne |
| **DROP INDEX** - Identifikuje redundantní a duplicitní indexy denně, s výjimkou jedinečných indexů a indexů, které nebyly použity po dlouhou dobu (>90 dnů). Vezměte prosím na vědomí, že tato možnost není kompatibilní s aplikacemi pomocí přepínání oddílů a rady pro index. Uvolnění nepoužívaných indexů není podporováno pro úrovně služeb Premium a Business Critical. | Ano | Ne |
| **VYNUTIT POSLEDNÍ DOBRÝ PLÁN** (automatická oprava plánu) – identifikuje dotazy SQL pomocí plánu spuštění, který je pomalejší než předchozí dobrý plán a dotazy pomocí poslední známý dobrý plán namísto regresovaného plánu. | Ano | Ano |

### <a name="automatic-tuning-for-single-and-pooled-databases"></a>Automatické ladění pro jednu a sdruženou databázi

Automatické ladění pro jednu a sdružené databáze používá **CREATE INDEX**, **DROP INDEX**a FORCE LAST **GOOD PLAN** databáze poradce doporučení pro optimalizaci výkonu databáze. Další informace najdete [v tématu doporučení poradce pro databázi na webu Azure Portal](sql-database-advisor-portal.md), v [PowerShellu](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabaserecommendedaction)a v [rozhraní REST API](https://docs.microsoft.com/rest/api/sql/serverautomatictuning).

Můžete buď ručně použít doporučení pro optimalizaci pomocí portálu Azure, nebo můžete nechat automatické ladění samostatně použít doporučení pro ladění pro vás. Výhody nechat systém samostatně použít tuning doporučení pro vás je, že automaticky ověří, že existuje pozitivní zisk na zatížení výkonu, a pokud není zjištěna žádná významná zlepšení výkonu, bude automaticky vrátit doporučení pro ladění. Vezměte prosím na vědomí, že v případě dotazů ovlivněných doporučeními pro ladění, které nejsou prováděny často, může fáze ověření trvat až 72 hodin podle návrhu.

V případě, že používáte optimalizace doporučení prostřednictvím T-SQL, automatické ověření výkonu a storno mechanismy nejsou k dispozici. Doporučení použitá tímto způsobem zůstanou aktivní a zobrazí se v seznamu doporučení pro ladění po dobu 24-48 hodin. před tím, než je systém automaticky stáhne. Pokud chcete odebrat doporučení dříve, můžete ho zahodit z webu Azure Portal.

Možnosti automatického ladění lze nezávisle povolit nebo zakázat pro každou databázi nebo je lze nakonfigurovat na serverech SQL Database a použít na každé databázi, která dědí nastavení ze serveru. Sql Database servery můžete dědit Azure výchozí hodnoty pro nastavení automatického ladění. Výchozí hodnoty Azure jsou v tuto chvíli nastaveny na FORCE_LAST_GOOD_PLAN je povolená, CREATE_INDEX je povolená a DROP_INDEX je zakázáno.

> [!IMPORTANT]
> Od března 2020 změny výchozích hodnot Azure pro automatické ladění se projeví takto:
>
> - Nové výchozí hodnoty Azure budou FORCE_LAST_GOOD_PLAN = povoleno, CREATE_INDEX = zakázáno a DROP_INDEX = zakázáno.
> - Existující servery bez nakonfigurovaných předvoleb automatického ladění se automaticky nakonfigurují na INHERIT nové výchozí hodnoty Azure. To platí pro všechny zákazníky, kteří mají aktuálně nastavení serveru pro automatické ladění v nedefinovaném stavu.
> - Nové vytvořené servery se automaticky nakonfigurují na INHERIT nové výchozí hodnoty Azure (na rozdíl od dřívějších, kdy byla konfigurace automatického ladění v nedefinovaném stavu při vytváření nového serveru).

Konfigurace možností automatického ladění na serveru a dědění nastavení pro databáze patřící nadřazenému serveru je doporučená metoda konfigurace automatického ladění, protože zjednodušuje správu možností automatického ladění pro velký počet databází.

Další informace o vytváření e-mailových oznámení pro doporučení automatického ladění najdete v [tématu E-mailová oznámení pro automatické ladění](sql-database-automatic-tuning-email-notifications.md).

### <a name="automatic-tuning-for-instance-databases"></a>Automatické ladění pro například databáze

Automatické ladění pro databáze instancí v nasazení spravované instance podporuje pouze **FORCE LAST GOOD PLAN**. Další informace o konfiguraci možností automatického ladění prostřednictvím t-SQL naleznete v [tématu Automatické ladění zavádí automatickou opravu plánu](https://azure.microsoft.com/blog/automatic-tuning-introduces-automatic-plan-correction-and-t-sql-management/) a [automatickou opravu plánu](https://docs.microsoft.com/sql/relational-databases/automatic-tuning/automatic-tuning?view=sql-server-ver15#automatic-plan-correction).

## <a name="next-steps"></a>Další kroky

- Další informace o integrované inteligenci používané při automatickém ladění najdete v [tématu Umělé inteligence vyladování databází Azure SQL](https://azure.microsoft.com/blog/artificial-intelligence-tunes-azure-sql-databases/).
