---
title: Škálování prostředků
description: Tento článek vysvětluje, jak škálovat databázi v Azure SQL Database a spravované instanci SQL přidáním nebo odebráním přidělených prostředků.
services: sql-database
ms.service: sql-db-mi
ms.subservice: performance
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: wiassaf, sstein
ms.date: 06/25/2019
ms.openlocfilehash: 453d7e118b946d60eb3d84c6a66abdbea7db2410
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/02/2020
ms.locfileid: "96499216"
---
# <a name="dynamically-scale-database-resources-with-minimal-downtime"></a>Dynamické škálování prostředků databáze s minimálními výpadky
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Azure SQL Database a spravované instance SQL umožňují dynamicky přidávat do databáze více prostředků s minimálními [výpadky](https://azure.microsoft.com/support/legal/sla/sql-database). v průběhu období ale dojde k přepnutí připojení do databáze po krátkou dobu, kterou je možné zmírnit pomocí logiky opakování.

## <a name="overview"></a>Přehled

Když poptávka po vaší aplikaci roste od několik zařízení a zákazníků až po miliony, Azure SQL Database a SQL spravované instance průběžně s minimálními výpadky. Škálovatelnost je jedním z nejdůležitějších vlastností platformy jako služby (PaaS), která umožňuje v případě potřeby dynamicky přidávat další prostředky do vaší služby. Azure SQL Database umožňuje snadno měnit prostředky (výkon procesoru, paměť, propustnost v/v) přidělené vašim databázím.

Můžete zmírnit problémy s výkonem z důvodu zvýšeného využití aplikace, které nelze opravit pomocí indexování nebo metod přepisu dotazů. Přidání dalších prostředků vám umožní rychle reagovat na to, kdy databáze narazí na aktuální omezení prostředků, a potřebuje vyšší výkon pro zpracování příchozích úloh. Azure SQL Database také umožňuje horizontální navýšení kapacity prostředků, pokud není potřeba snížit náklady.

Nemusíte si dělat starosti s nákupem hardwaru a změnou základní infrastruktury. Škálování databáze lze snadno provádět prostřednictvím Azure Portal pomocí posuvníku.

![Škálování výkonu databáze](./media/scale-resources/scale-performance.svg)

Azure SQL Database nabízí [nákupní model založený na DTU](service-tiers-dtu.md) a [nákupní model založený na Vcore](service-tiers-vcore.md), zatímco Azure SQL Managed instance nabízí pouze [nákupní model založený na Vcore](service-tiers-vcore.md). 

- [Nákupní model založený na DTU](service-tiers-dtu.md) nabízí kombinaci výpočetních, paměťových a vstupně-výstupních prostředků ve třech úrovních služeb, aby se podporovaly odlehčené a těžké databázové úlohy: Basic, Standard a Premium. Úrovně výkonu na jednotlivých úrovních poskytují různou kombinaci těchto prostředků, ke kterým můžete přidat další prostředky úložiště.
- [Nákupní model založený na vCoreech](service-tiers-vcore.md) umožňuje zvolit počet virtuální jádra, velikost paměti a množství a rychlost úložiště. Tento model nákupu nabízí tři úrovně služeb: Pro obecné účely, Pro důležité obchodní informace a škálování.

Svou první aplikaci můžete vytvořit na malé a izolované databázi s nízkými náklady za měsíc v úrovni služeb Basic, Standard nebo Pro obecné účely a pak ji ručně nebo programově změnit na úroveň služeb Premium nebo Pro důležité obchodní informace, aby splňovala požadavky vašeho řešení. Můžete upravit úroveň výkonu bez přerušení provozu aplikace a bez dopadu na vaše zákazníky. Dynamická škálovatelnost umožňuje databázím transparentně reagovat na rychle se měnící požadavky na prostředky a vy díky tomu platíte pouze za prostředky, které potřebujete, když je potřebujete.

> [!NOTE]
> Dynamická škálovatelnost se liší od automatického škálování. Automatické škálování je tehdy, když se služba automaticky škáluje podle kritérií, zatímco dynamická škálovatelnost umožňuje ruční škálování s minimálními výpadky.

Samostatné databáze v Azure SQL Database podporují ruční dynamickou škálovatelnost, ale ne automatické škálování. Pokud chcete *automatizovanější* prostředí, zvažte použití elastických fondů, které databázím umožňují sdílet prostředky ve fondu na základě potřeb jednotlivých databází.
Existují však skripty, které mohou přispět k automatizaci škálovatelnosti pro izolovanou databázi v Azure SQL Database. Příklad najdete v tématu [Monitorování a škálování izolované databáze SQL pomocí PowerShellu](scripts/monitor-and-scale-database-powershell.md).

Změnit [úrovně služby DTU](service-tiers-dtu.md) nebo [charakteristiky virtuálních jader](resource-limits-vcore-single-databases.md) můžete kdykoli s minimální odstávkou aplikace (obecně v průměru méně než čtyři sekundy). Mnoha firmám a aplikacím stačí vytváření databází a nastavování výkonu na vyžádání, zejména v případě, že jsou vzorce používání relativně předvídatelné. Ale pokud vaše vzorce používání předvídatelné nejsou, může být správa nákladů a údržba obchodního modelu velmi těžká. V tomto scénáři použijete elastický fond s určitým počtem eDTU, který je sdílen mezi více databázemi ve fondu.

![Úvod do služby SQL Database: DTU izolované databáze podle úrovní](./media/scale-resources/single_db_dtus.png)

Azure SQL Database nabízí možnost dynamicky škálovat vaše databáze:

- V případě izolované [databáze](single-database-scale.md)můžete použít model [DTU](resource-limits-dtu-single-databases.md) nebo [Vcore](resource-limits-vcore-single-databases.md) k definování maximálního množství prostředků, které budou přiřazeny ke každé databázi.
- [Elastické fondy](elastic-pool-scale.md) umožňují definovat maximální limit prostředků na skupinu databází ve fondu.

Azure SQL Managed instance umožňuje také škálovat: 

- [Spravovaná instance SQL](../managed-instance/sql-managed-instance-paas-overview.md) používá režim [virtuální jádra](../managed-instance/sql-managed-instance-paas-overview.md#vcore-based-purchasing-model) a umožňuje definovat maximální počet jader procesoru a maximum úložiště přidělené vaší instanci. Všechny databáze v rámci spravované instance budou sdílet prostředky přidělené instanci.

Při inicializaci akce horizontálního navýšení nebo snížení kapacity v některém z těchto možností se restartuje proces databázového stroje a v případě potřeby ho přesune na jiný virtuální počítač. Přesunutí procesu databázového stroje do nového virtuálního počítače je **online proces** , kde můžete dál používat existující službu Azure SQL Database, zatímco probíhá proces. Jakmile je cílový databázový stroj plně inicializovaný a připravený ke zpracování dotazů, budou připojení [přepnuta ze zdrojového do cílového databázového stroje](single-database-scale.md#impact).

> [!NOTE]
> Po dokončení procesu horizontálního navýšení nebo snížení kapacity je možné očekávat krátké přerušení připojení. Pokud jste implementovali [logiku opakování pro standardní přechodné chyby](troubleshoot-common-connectivity-issues.md#retry-logic-for-transient-errors), nebudete si poznamenat převzetí služeb při selhání.

## <a name="alternative-scale-methods"></a>Alternativní metody škálování

Škálování prostředků je nejjednodušší a nejúčinnější způsob, jak vylepšit výkon databáze bez změny kódu databáze nebo aplikace. V některých případech se může stát, že i nejvyšší úrovně služeb, velikosti výpočtů a optimalizace výkonu nezvládnou úspěšné a nákladově efektivní způsob zpracování úloh. V takovém případě máte tyto další možnosti škálování databáze:

- Funkce [škálování na](read-scale-out.md) více instancí je dostupnou funkcí, kde získáte jednu repliku, která je jen pro čtení, kde můžete provádět náročné dotazy jen pro čtení, jako jsou sestavy. Replika jen pro čtení bude zpracovávat úlohy jen pro čtení, aniž by to mělo vliv na využití prostředků v primární databázi.
- [Horizontálního dělení databáze](elastic-scale-introduction.md) je sada technik, která umožňuje rozdělit data do několika databází a škálovat je nezávisle.

## <a name="next-steps"></a>Další kroky

- Informace o vylepšení výkonu databáze změnou kódu databáze najdete v tématu [věnovaném doporučením najít a použít výkon](database-advisor-find-recommendations-portal.md).
- Informace o tom, jak předdefinovaná databáze pro optimalizaci databáze, najdete v tématu [Automatické ladění](automatic-tuning-overview.md).
- Informace o škálování čtení na více instancí v Azure SQL Database najdete v tématu Jak [používat repliky jen pro čtení k vyrovnávání zatížení úloh dotazů jen pro čtení](read-scale-out.md).
- Informace o horizontálního dělení databáze najdete v tématu horizontální navýšení kapacity [pomocí Azure SQL Database](elastic-scale-introduction.md).
