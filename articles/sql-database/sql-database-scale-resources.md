---
title: Škálování zdrojů
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
ms.openlocfilehash: c4366b2718271b1e27325e6946c5016e9230cea4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76835908"
---
# <a name="dynamically-scale-database-resources-with-minimal-downtime"></a>Dynamické škálování databázových prostředků s minimálními prostoji

Azure SQL Database umožňuje dynamicky přidávat další prostředky do databáze s minimálními [prostoji](https://azure.microsoft.com/support/legal/sla/sql-database/v1_2/); však je přepínač přes období, kdy dojde ke ztrátě připojení k databázi na krátkou dobu, které lze zmírnit pomocí logiky opakování.

## <a name="overview"></a>Přehled

Když poptávka po vaší aplikaci roste z několika zařízení a zákazníků na miliony, Azure SQL Database se průběžně škáluje s minimálními prostoji. Škálovatelnost je jednou z nejdůležitějších vlastností PaaS, která umožňuje dynamicky přidávat další prostředky do služby v případě potřeby. Azure SQL Database umožňuje snadno změnit prostředky (výkon procesoru, paměť, propustnost vi a úložiště) přidělené do vašich databází.

Můžete zmírnit problémy s výkonem z důvodu zvýšené využití aplikace, které nelze opravit pomocí metody indexování nebo přepisování dotazů. Přidání dalších prostředků umožňuje rychle reagovat, když databáze narazí na aktuální omezení prostředků a potřebuje více energie pro zpracování příchozí úlohy. Azure SQL Database také umožňuje škálovat prostředky, když nejsou potřeba ke snížení nákladů.

Nemusíte se starat o nákup hardwaru a změnu základní infrastruktury. Škálování databáze lze snadno provést prostřednictvím portálu Azure pomocí posuvníku.

![Škálování výkonu databáze](media/sql-database-scalability/scale-performance.svg)

Azure SQL Database nabízí [nákupní model založený na DTU](sql-database-service-tiers-dtu.md) a nákupní model založený na [virtuálních jádrech](sql-database-service-tiers-vcore.md).

- [Nákupní model založený na DTU](sql-database-service-tiers-dtu.md) nabízí kombinaci výpočetních, paměťových a vstupně-tou prostředků ve třech úrovních služeb, které podporují náročné až těžké databázové úlohy: Basic, Standard a Premium. Úrovně výkonu na jednotlivých úrovních poskytují různou kombinaci těchto prostředků, ke kterým můžete přidat další prostředky úložiště.
- [Nákupní model založený na virtuálních jádrech](sql-database-service-tiers-vcore.md) umožňuje zvolit počet virtuálních jader, množství nebo paměť a velikost a rychlost úložiště. Tento nákupní model nabízí tři úrovně služeb: obecné účely, důležité pro podnikání a hyperškálování.

První aplikaci můžete sestavit na malé, jediné databázi s nízkými náklady za měsíc na úrovni služeb Basic, Standard nebo General Purpose a pak kdykoli ručně nebo programově změnit její úroveň služeb na úroveň Premium nebo Business Critical, abyste splnili úroveň služby Premium nebo Business Critical potřeby vašeho řešení. Můžete upravit úroveň výkonu bez přerušení provozu aplikace a bez dopadu na vaše zákazníky. Dynamická škálovatelnost umožňuje databázím transparentně reagovat na rychle se měnící požadavky na prostředky a vy díky tomu platíte pouze za prostředky, které potřebujete, když je potřebujete.

> [!NOTE]
> Dynamická škálovatelnost se liší od automatického škálování. Automatické škálování je, když se služba automaticky škáluje na základě kritérií, zatímco dynamická škálovatelnost umožňuje ruční škálování s minimálními prostoji.

Izolovaná databáze Azure SQL Database podporuje ruční dynamickou škálovatelnost, nikoli automatické škálování. Pokud chcete *automatizovanější* prostředí, zvažte použití elastických fondů, které databázím umožňují sdílet prostředky ve fondu na základě potřeb jednotlivých databází.
Existují však skripty, které mohou pomoci automatizovat škálovatelnost pro jednu databázi Azure SQL. Příklad najdete v tématu [Monitorování a škálování izolované databáze SQL pomocí PowerShellu](scripts/sql-database-monitor-and-scale-database-powershell.md).

Změnit [úrovně služby DTU](sql-database-service-tiers-dtu.md) nebo [charakteristiky virtuálních jader](sql-database-vcore-resource-limits-single-databases.md) můžete kdykoli s minimální odstávkou aplikace (obecně v průměru méně než čtyři sekundy). Mnoha firmám a aplikacím stačí vytváření databází a nastavování výkonu na vyžádání, zejména v případě, že jsou vzorce používání relativně předvídatelné. Ale pokud vaše vzorce používání předvídatelné nejsou, může být správa nákladů a údržba obchodního modelu velmi těžká. V tomto scénáři použijete elastický fond s určitým počtem eDTU, které jsou sdíleny mezi více databází ve fondu.

![Úvod do služby SQL Database: DTU izolované databáze podle úrovní](./media/sql-database-what-is-a-dtu/single_db_dtus.png)

Všechny tři varianty Azure SQL Database nabízejí určitou možnost dynamicky škálovat vaše databáze:

- Pomocí [jedné databáze](sql-database-single-database-scale.md)můžete pomocí modelů [DTU](sql-database-dtu-resource-limits-single-databases.md) nebo [virtuálních jader](sql-database-vcore-resource-limits-single-databases.md) definovat maximální množství prostředků, které budou přiřazeny ke každé databázi.
- [Spravovaná instance](sql-database-managed-instance.md) používá režim [virtuálních jader](sql-database-managed-instance.md#vcore-based-purchasing-model) a umožňuje definovat maximální jádro procesoru a maximální úložiště přidělené vaší instanci. Všechny databáze v rámci instance budou sdílet prostředky přidělené instanci.
- [Elastické fondy](sql-database-elastic-pool-scale.md) umožňují definovat maximální limit prostředků na skupinu databází ve fondu.

Zahájení vertikálně navýšit nebo vertikálně snížit kapacitu akce v některé z variant by restartovat proces databázového stroje a přesunout jej do jiného virtuálního počítače v případě potřeby. Přesunutí procesu databázového stroje do nového virtuálního počítače je **online proces,** ve kterém můžete pokračovat v používání stávající služby Azure SQL Database během procesu. Jakmile je cílový databázový stroj plně inicializován a připraven ke zpracování dotazů, připojení budou [přepnuta ze zdroje na cílový databázový stroj](sql-database-single-database-scale.md#impact). 


> [!NOTE]
> Po dokončení procesu škálování nahoru/škálování můžete očekávat krátkou přestávku připojení. Pokud jste implementovali [logiku opakování pro standardní přechodné chyby](sql-database-connectivity-issues.md#retry-logic-for-transient-errors), nevšimnete si převzetí služeb při selhání.

## <a name="alternative-scale-methods"></a>Alternativní metody stupnice

Škálování prostředků je nejjednodušší a nejúčinnější způsob, jak zlepšit výkon databáze bez evidenční změny databáze nebo kódu aplikace. V některých případech i nejvyšší úrovně služeb, výpočetní velikosti a optimalizace výkonu nemusí zpracovat vaše úlohy úspěšným a nákladově efektivním způsobem. V takovém případě máte tyto další možnosti škálování databáze:

- [Škálovatelné měřítko pro čtení](sql-database-read-scale-out.md) je dostupná funkce, kde získáváte jednu repliku dat jen pro čtení, kde můžete provádět náročné dotazy jen pro čtení, jako jsou sestavy. Replika jen pro čtení bude zpracovávat vaše úlohy jen pro čtení bez ovlivnění využití prostředků v primární databázi.
- [Horizontálního rozdělení databáze](sql-database-elastic-scale-introduction.md) je sada technik, které umožňuje rozdělit data do několika databází a škálovat je nezávisle.

## <a name="next-steps"></a>Další kroky

- Informace o zlepšení výkonu databáze změnou kódu databáze naleznete v tématu [Hledání a použití doporučení pro výkon](sql-database-advisor-portal.md).
- Informace o optimalizaci integrované databázové inteligence v databázi naleznete v [tématu Automatické ladění](sql-database-automatic-tuning.md).
- Informace o škálování čtení ve službě Azure SQL Database najdete v tématu [použití replik jen pro čtení k vyrovnávání zatížení úloh dotazů jen pro čtení](sql-database-read-scale-out.md).
- Informace o horizontálních oddílů databáze, najdete v [tématu škálování pomocí Azure SQL Database](sql-database-elastic-scale-introduction.md).
