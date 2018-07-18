---
title: Škálování prostředků Azure SQL Database | Dokumentace Microsoftu
description: Tento článek vysvětluje, jak škálovat přidáváním nebo odebíráním přidělené prostředky vaší databáze.
services: sql-database
author: jovanpop-msft
ms.reviewer: carlrab
ms.service: sql-database
ms.topic: conceptual
ms.date: 07/16/2018
ms.author: jovanpop
manager: craigg
ms.openlocfilehash: a6b987d9815cfabed6dd986a0d9842a97f5b5868
ms.sourcegitcommit: e32ea47d9d8158747eaf8fee6ebdd238d3ba01f7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/17/2018
ms.locfileid: "39092048"
---
# <a name="scale-database-resources"></a>Škálování databáze prostředků

Azure SQL Database umožňuje dynamicky přidat další prostředky do databáze s minimálními výpadky.

## <a name="overview"></a>Přehled

Když poptávka po vaší aplikaci roste od několika zařízení a zákazníků do milionů, Azure SQL Database se škáluje v reálném čase s minimálními výpadky. Škálovatelnost je jedním z nejdůležitějších vlastností PaaS, která umožňuje dynamicky přidat další prostředky na vaši službu, v případě potřeby. Azure SQL Database vám umožní snadno měnit prostředky (výkonu procesoru, paměti, vstupně-výstupních operací a úložiště) přidělené vaší databáze.  
Můžete zmírnit problémy s výkonem způsobený zvyšováním využití aplikace, které nelze opravit pomocí indexování nebo přepsání metody dotazu. Přidání více zdrojů, můžete rychle reagovat, když narazí na aktuální omezení prostředků a vyžaduje větší výkon pro zpracování příchozích úloh databáze. Azure SQL Database také umožňuje vám vertikální snížení kapacity prostředků že podle potřeby snížit náklady.
Nemusíte se starat o nákup hardwaru a změnou základní infrastruktury. Škálování databáze můžete snadno udělat pomocí webu Azure portal pomocí posuvníku.

![Škálování výkonu databáze](media/sql-database-scalability/scale-performance.svg)

Azure SQL Database nabízí [nákupní model založený na DTU](sql-database-service-tiers-dtu.md) nebo [nákupní model založený na virtuálních jádrech](sql-database-service-tiers-vcore.md). 
-   [Nákupní model založený na DTU](sql-database-service-tiers-dtu.md) nabízí kombinaci výpočetních, paměťových a vstupně-VÝSTUPNÍCH prostředků ve třech úrovních služeb pro podporu nejlehčích k těm nejnáročnějším: Basic, Standard a Premium. Úrovně výkonu na jednotlivých úrovních poskytují různou kombinaci těchto prostředků, ke kterým můžete přidat další prostředky úložiště.
-   [Nákupní model založený na virtuálních jádrech](sql-database-service-tiers-vcore.md) umožňuje výběr počtu virtuálních jader, velikost nebo paměti a množství a rychlosti úložiště.
S nízkými měsíčními náklady můžete sestavit svou první aplikaci s malou izolovanou databází a později ručně nebo programově změnit úroveň služby, aby splňovala požadavky vašeho řešení. Můžete upravit úroveň výkonu bez přerušení provozu aplikace a bez dopadu na vaše zákazníky. Dynamická škálovatelnost umožňuje databázím transparentně reagovat na rychle se měnící požadavky na prostředky a vy díky tomu platíte pouze za prostředky, které potřebujete, když je potřebujete.


> [!NOTE]
> Dynamická škálovatelnost se liší od automatického škálování. K automatickému škálování dochází, když se služba škáluje automaticky na základě kritérií, zatímco dynamická škálovatelnost umožňuje ruční škálování bez prostojů.
>


Izolovaná databáze Azure SQL Database podporuje ruční dynamickou škálovatelnost, nikoli automatické škálování. Pokud chcete *automatizovanější* prostředí, zvažte použití elastických fondů, které databázím umožňují sdílet prostředky ve fondu na základě potřeb jednotlivých databází.
Existují však skripty, které můžou usnadnit automatizaci škálovatelnost pro izolovanou databázi SQL Azure. Příklad najdete v tématu [Monitorování a škálování izolované databáze SQL pomocí PowerShellu](scripts/sql-database-monitor-and-scale-database-powershell.md).


Všechny tři typy služby Azure SQL Database nabízí některé schopnost dynamicky škálovat databáze:
-   V [izolovanou databázi SQL Azure](sql-database-single-database-scale.md), můžete použít buď [DTU](sql-database-dtu-resource-limits-single-databases.md) nebo [vCore](sql-database-vcore-resource-limits-single-databases.md) modely, které definují maximální množství prostředků, které se přiřadí každé databázi.
-   [Spravované Instance Azure SQL](sql-database-managed-instance.md) používá [virtuálních jader](/azure/sql-database/sql-database-managed-instance#vcore-based-purchasing-model-preview) režimu a umožňuje definovat maximální počet jader procesoru a maximální úložiště přidělené k vaší instanci. Všechny databáze v instanci se sdílení prostředků přidělených této instanci.
-   [Elastické fondy Azure SQL](sql-database-elastic-pool-scale.md) vám umožňují definovat prostředek maximální limit pro skupinu databází ve fondu.

## <a name="alternative-scale-methods"></a>Škálování alternativní metody
Škálování prostředků je nejjednodušší a nejúčinnější způsob, jak zlepšit výkon vaší databáze beze změny kódu databáze nebo aplikace.
V některých případech nemusí zpracovávat i nejvyšší úrovně výkonu a optimalizace výkonu vašich úloh na úspěšné a nákladově efektivní způsob, jak. V tomto případě máte další možnosti škálování databáze:
-   [Horizontální navýšení kapacity pro čtení](sql-database-read-scale-out.md) je funkce k dispozici ve kterém se zobrazuje jednu repliku pouze pro čtení dat ve kterém můžete spustit vyhovovat i vašim náročným dotazy jen pro čtení, jako je například sestavy. Repliku pouze Red bude zpracovávat úlohy jen pro čtení bez ovlivnění využití prostředků u primární databáze.
-   [Horizontální dělení databází](sql-database-elastic-scale-introduction.md) je sadu technik, které vám umožní rozdělit data do několika databází a nezávisle škálovat.

## <a name="next-steps"></a>Další postup
- Informace týkající se vylepšení výkonu databáze změnou kódu databáze najdete v tématu [vyhledání a použití doporučení k výkonu](sql-database-advisor-portal.md).
- Informace o umožňuje inteligentní funkce integrované database Optimalizujte vaši databázi, naleznete v tématu [automatické ladění](sql-database-automatic-tuning.md).
- Informace o čtení škálování ve službě Azure SQL Database najdete v tématu Jak [pomocí repliky jen pro čtení můžete načíst úlohy dotazu jen pro čtení Zůstatek](sql-database-read-scale-out.md).
- Informace o horizontální dělení databází najdete v tématu [horizontální navýšení kapacity s Azure SQL Database](sql-database-elastic-scale-introduction.md).

