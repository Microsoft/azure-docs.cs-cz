---
title: Azure SQL Database škálování prostředků | Microsoft Docs
description: Tento článek vysvětluje, jak škálovat databázi přidáním nebo odebráním přidělených prostředků.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: jrasnik, carlrab
ms.date: 06/25/2019
ms.openlocfilehash: 98d24b4f497f09e982101917296b572a5c381f42
ms.sourcegitcommit: 87efc325493b1cae546e4cc4b89d9a5e3df94d31
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/29/2019
ms.locfileid: "73053600"
---
# <a name="dynamically-scale-database-resources-with-minimal-downtime"></a>Dynamické škálování prostředků databáze s minimálními výpadky

Azure SQL Database vám umožní dynamicky přidávat do databáze více prostředků s minimálními [výpadky](https://azure.microsoft.com/support/legal/sla/sql-database/v1_2/). v průběhu období ale dojde k přepnutí připojení do databáze po krátkou dobu, kterou je možné zmírnit pomocí logiky opakování.

## <a name="overview"></a>Přehled

Když poptávka po vaší aplikaci roste od několik zařízení a zákazníků po miliony, Azure SQL Database se průběžně škáluje s minimálními prostoji. Škálovatelnost je jedním z nejdůležitějších vlastností PaaS, které vám v případě potřeby umožní dynamicky přidávat další prostředky do vaší služby. Azure SQL Database umožňuje snadno měnit prostředky (výkon procesoru, paměť, propustnost v/v) přidělené vašim databázím.

Můžete zmírnit problémy s výkonem z důvodu zvýšeného využití aplikace, které nelze opravit pomocí indexování nebo metod přepisu dotazů. Přidání dalších prostředků vám umožní rychle reagovat na to, kdy databáze narazí na aktuální omezení prostředků, a potřebuje vyšší výkon pro zpracování příchozích úloh. Azure SQL Database také umožňuje horizontální navýšení kapacity prostředků, pokud není potřeba snížit náklady.

Nemusíte si dělat starosti s nákupem hardwaru a změnou základní infrastruktury. Škálování databáze je možné snadno provádět prostřednictvím Azure Portal pomocí posuvníku.

![Škálování výkonu databáze](media/sql-database-scalability/scale-performance.svg)

Azure SQL Database nabízí [nákupní model založený na DTU](sql-database-service-tiers-dtu.md) a [nákupní model založený na Vcore](sql-database-service-tiers-vcore.md).

- [Nákupní model založený na DTU](sql-database-service-tiers-dtu.md) nabízí kombinaci výpočetních, paměťových a vstupně-výstupních prostředků ve třech úrovních služeb pro podporu odlehčených a těžkých databázových úloh: Basic, Standard a Premium. Úrovně výkonu na jednotlivých úrovních poskytují různou kombinaci těchto prostředků, ke kterým můžete přidat další prostředky úložiště.
- [Nákupní model založený na vCoreech](sql-database-service-tiers-vcore.md) umožňuje zvolit počet virtuální jádra, velikost paměti a množství a rychlost úložiště. Tento model nákupu nabízí tři úrovně služeb: Pro obecné účely, Pro důležité obchodní informace a škálování.

Svou první aplikaci můžete vytvořit v malých, izolovaných databázích za nízkou cenu za měsíc v úrovni služeb Basic, Standard nebo Pro obecné účely a pak ručně nebo programově změnit úroveň služby na úroveň služeb Premium nebo Pro důležité obchodní informace, aby splňovala hodnotu ne. EDS vaše řešení. Můžete upravit úroveň výkonu bez přerušení provozu aplikace a bez dopadu na vaše zákazníky. Dynamická škálovatelnost umožňuje databázím transparentně reagovat na rychle se měnící požadavky na prostředky a vy díky tomu platíte pouze za prostředky, které potřebujete, když je potřebujete.

> [!NOTE]
> Dynamická škálovatelnost se liší od automatického škálování. Automatické škálování je tehdy, když se služba automaticky škáluje podle kritérií, zatímco dynamická škálovatelnost umožňuje ruční škálování s minimálními výpadky.

Izolovaná databáze Azure SQL Database podporuje ruční dynamickou škálovatelnost, nikoli automatické škálování. Pokud chcete *automatizovanější* prostředí, zvažte použití elastických fondů, které databázím umožňují sdílet prostředky ve fondu na základě potřeb jednotlivých databází.
Existují však skripty, které mohou přispět k automatizaci škálovatelnosti jednoho Azure SQL Database. Příklad najdete v tématu [Monitorování a škálování izolované databáze SQL pomocí PowerShellu](scripts/sql-database-monitor-and-scale-database-powershell.md).

Změnit [úrovně služby DTU](sql-database-service-tiers-dtu.md) nebo [charakteristiky virtuálních jader](sql-database-vcore-resource-limits-single-databases.md) můžete kdykoli s minimální odstávkou aplikace (obecně v průměru méně než čtyři sekundy). Mnoha firmám a aplikacím stačí vytváření databází a nastavování výkonu na vyžádání, zejména v případě, že jsou vzorce používání relativně předvídatelné. Ale pokud vaše vzorce používání předvídatelné nejsou, může být správa nákladů a údržba obchodního modelu velmi těžká. V tomto scénáři použijete elastický fond s určitým počtem eDTU, který je sdílen mezi více databázemi ve fondu.

![Úvod do služby SQL Database: DTU izolované databáze podle úrovní](./media/sql-database-what-is-a-dtu/single_db_dtus.png)

Všechny tři typy Azure SQL Database nabízejí určitou schopnost dynamicky škálovat vaše databáze:

- V případě izolované [databáze](sql-database-single-database-scale.md)můžete použít model [DTU](sql-database-dtu-resource-limits-single-databases.md) nebo [Vcore](sql-database-vcore-resource-limits-single-databases.md) k definování maximálního množství prostředků, které budou přiřazeny ke každé databázi.
- [Spravovaná instance](sql-database-managed-instance.md) používá režim [virtuální jádra](sql-database-managed-instance.md#vcore-based-purchasing-model) a umožňuje definovat maximální počet jader procesoru a maximum úložiště přidělené vaší instanci. Všechny databáze v rámci instance budou sdílet prostředky přidělené instanci.
- [Elastické fondy](sql-database-elastic-pool-scale.md) umožňují definovat maximální limit prostředků na skupinu databází ve fondu.

Při inicializaci akce horizontálního navýšení nebo snížení kapacity v některém z těchto možností se restartuje proces databázového stroje a v případě potřeby ho přesune na jiný virtuální počítač. Přesunutí procesu databázového stroje do nového virtuálního počítače je **online proces** , kde můžete dál používat existující službu Azure SQL Database, zatímco probíhá proces. Jakmile je cílový databázový stroj plně inicializovaný a připravený ke zpracování dotazů, budou připojení [přepnuta ze zdrojového do cílového databázového stroje](sql-database-single-database-scale.md#impact-of-changing-service-tier-or-rescaling-compute-size).

> [!NOTE]
> Po dokončení procesu horizontálního navýšení nebo snížení kapacity je možné očekávat krátké přerušení připojení. Pokud jste implementovali [logiku opakování pro standardní přechodné chyby](sql-database-connectivity-issues.md#retry-logic-for-transient-errors), nebudete si poznamenat převzetí služeb při selhání.

## <a name="alternative-scale-methods"></a>Alternativní metody škálování

Škálování prostředků je nejjednodušší a nejúčinnější způsob, jak zvýšit výkon databáze beze změny kódu databáze nebo aplikace. V některých případech se může stát, že i nejvyšší úrovně služeb, velikosti výpočtů a optimalizace výkonu nemůžou vaše zatížení zvládnout po úspěšném a nákladově efektivním způsobu. V takovém případě máte tyto další možnosti škálování databáze:

- Možnost [škálování](sql-database-read-scale-out.md) na více instancí je funkce, ve které získáte jednu repliku dat jen pro čtení, kde můžete provádět náročné dotazy jen pro čtení, například sestavy. Replika jen pro čtení zpracuje úlohu jen pro čtení, aniž by to mělo vliv na využití prostředků v primární databázi.
- [Horizontálního dělení databáze](sql-database-elastic-scale-introduction.md) je sada technik, která umožňuje rozdělit data do několika databází a škálovat je nezávisle.

## <a name="next-steps"></a>Další kroky

- Informace o vylepšení výkonu databáze změnou kódu databáze najdete v tématu [věnovaném doporučením najít a použít výkon](sql-database-advisor-portal.md).
- Informace o tom, jak předdefinovaná databáze pro optimalizaci databáze, najdete v tématu [Automatické ladění](sql-database-automatic-tuning.md).
- Informace o škálování čtení na více instancí ve službě Azure SQL Database najdete v tématu Jak [používat repliky jen pro čtení k vyrovnávání zatížení úloh dotazů jen pro čtení](sql-database-read-scale-out.md).
- Informace o horizontálního dělení databáze najdete v tématu horizontální navýšení kapacity [pomocí Azure SQL Database](sql-database-elastic-scale-introduction.md).
