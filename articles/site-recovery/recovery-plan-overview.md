---
title: O plánech obnovení v Azure Site Recovery
description: Přečtěte si o plánech obnovení v Azure Site Recovery.
ms.topic: conceptual
ms.date: 01/23/2020
ms.openlocfilehash: 8d191781cacc37242dd1be31d6cb87ef196e5e7a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "84343910"
---
# <a name="about-recovery-plans"></a>Plány obnovení

Tento článek poskytuje přehled plánů obnovení v [Azure Site Recovery](site-recovery-overview.md).

Plán obnovení shromažďuje počítače do skupin obnovení pro účely převzetí služeb při selhání. Plán obnovení vám pomůže definovat proces systematického obnovení tím, že vytvoří malé nezávislé jednotky, u kterých můžete převzít služby při selhání. Jednotka obvykle představuje aplikaci ve vašem prostředí.

- Plán obnovení definuje, jak počítače převezmou služby při selhání, a pořadí, ve kterém se spouštějí po převzetí služeb při selhání.
- Plány obnovení se dají použít při převzetí služeb při selhání i navrácení služeb po obnovení z Azure.
- Do jednoho plánu obnovení lze přidat až 100 chráněných instancí.
- Plán můžete přizpůsobit tak, že do něj přidáte pořadí, pokyny a úkoly.
- Po definování plánu můžete na něm spustit převzetí služeb při selhání.
- Na počítače se dá odkazovat v několika plánech obnovení, ve kterých následné plány přeskočí nasazení nebo spuštění počítače, pokud se dřív nasadil pomocí jiného plánu obnovení.



### <a name="why-use-a-recovery-plan"></a>Proč použít plán obnovení?

Plány obnovení použijte k těmto akcím:

* Namodelujte aplikaci kolem jejích závislostí.
* Automatizujte úlohy obnovení, aby se snížila plánovaná doba obnovení (RTO).
* Ověřte, že jste připraveni na migraci nebo zotavení po havárii tím, že zajistíte, aby vaše aplikace byly součástí plánu obnovení.
* Spusťte testovací převzetí služeb při selhání v plánech obnovení, aby se zajistilo, že zotavení po havárii nebo migrace bude fungovat podle očekávání.


## <a name="model-apps"></a>Modelování aplikací 
Můžete naplánovat a vytvořit skupinu obnovení pro zachycení vlastností specifických pro aplikaci. Řekněme například, že posuzujeme typickou trojrozměrnou aplikaci s back-endu SQL serveru, middlewarem a webovým front-endu. Obvykle můžete upravit plán obnovení tak, aby se počítače v každé úrovni po převzetí služeb při selhání spouštěly ve správném pořadí.

- Back-end SQL by se měl spustit jako první, middleware Next a nakonec webový front-end.
- Toto pořadí spuštění zajistí, že aplikace funguje v čase posledního spuštění počítače.
- Toto pořadí zajistí, že když se middleware spustí a pokusí se připojit k SQL Server vrstvě, SQL Server vrstva už je spuštěná. 
- Tato objednávka také pomáhá zajistit, že se server front-end začne používat jako poslední, aby se koncoví uživatelé nepřipojovali k adrese URL aplikace předtím, než budou všechny komponenty v provozu a aplikace je připravená přijímat žádosti.

Chcete-li vytvořit toto pořadí, přidejte skupiny do skupiny obnovení a přidejte počítače do skupin.
- Je-li zadáno pořadí, je použito sekvencování. Akce běží paralelně, aby se vylepšilo RTO obnovení aplikací.
- U počítačů v jedné skupině dojde k převzetí služeb při selhání paralelně.
- Počítače v různých skupinách převezmou služby při selhání v pořadí skupiny, aby počítače skupiny 2 spouštěly převzetí služeb při selhání až po převzetí služeb při selhání a spuštění všech počítačů ve skupině 1.

    ![Příklad plánu obnovení](./media/recovery-plan-overview/rp.png)

V takovém případě se toto přizpůsobení stane při spuštění převzetí služeb při selhání v plánu obnovení: 

1. Krok vypnutí se pokusí vypnout místní počítače. Výjimkou je spuštění testovacího převzetí služeb při selhání. v takovém případě bude primární lokalita nadále spuštěna. 
2. Vypnutí vyvolá paralelní převzetí služeb při selhání pro všechny počítače v plánu obnovení.
3. Převzetí služeb při selhání připraví disky virtuálních počítačů pomocí replikovaných dat.
4. Spouštěcí skupiny běží v uvedeném pořadí a zahájí počítače v každé skupině. Nejdřív se spustí skupina 1, potom skupina 2 a nakonec skupina 3. Pokud je v libovolné skupině více než jeden počítač, všechny počítače se spustí paralelně.


## <a name="automate-tasks-in-recovery-plans"></a>Automatizace úloh v plánech obnovení

Obnovování rozsáhlých aplikací může být složitý úkol. Ruční kroky činí proces náchylný k chybě a osoba, která přebírá převzetí služeb při selhání, nemusí vědět o všech složitými rozhraními App. Plán obnovení můžete použít k vytvoření objednávky a automatizaci akcí potřebných v každém kroku, a to pomocí Azure Automation Runbooky pro převzetí služeb při selhání do Azure nebo skriptů. Pro úlohy, které nemůžou být automatizované, můžete do plánů obnovení vložit pauzy pro ruční akce. Existuje několik typů úloh, které můžete nakonfigurovat:

* **Úlohy na virtuálním počítači Azure po převzetí služeb při selhání**: když převezmete služby do Azure, obvykle je potřeba provést akce, abyste se mohli k virtuálnímu počítači připojit po převzetí služeb při selhání. Například: 
    * Vytvořte veřejnou IP adresu na virtuálním počítači Azure.
    * Přiřaďte skupinu zabezpečení sítě k síťovému adaptéru virtuálního počítače Azure.
    * Přidejte Nástroj pro vyrovnávání zatížení do skupiny dostupnosti.
* **Úkoly v rámci virtuálního počítače po převzetí služeb při selhání**: tyto úlohy obvykle překonfigurují aplikaci běžící na počítači, aby i nadále fungovala správně v novém prostředí. Například:
    * Upravte připojovací řetězec databáze v počítači.
    * Změňte konfiguraci nebo pravidla webového serveru.


### <a name="run-a-test-failover-on-recovery-plans"></a>Spuštění testovacího převzetí služeb při selhání v plánech obnovení

K aktivaci testovacího převzetí služeb při selhání můžete použít plán obnovení. Použijte následující osvědčené postupy:

- Před spuštěním úplného převzetí služeb při selhání vždy dokončete testovací převzetí služeb při selhání v aplikaci. Testovací převzetí služeb při selhání vám pomůžou zkontrolovat, jestli se aplikace objeví na webu pro obnovení.
- Pokud zjistíte, že jste něco nenechali, aktivujte vyčištění a pak znovu spusťte testovací převzetí služeb při selhání. 
- Spusťte testovací převzetí služeb při selhání několikrát, dokud nebudete mít jistotu, že se aplikace bude obnovovat hladce.
- Vzhledem k tomu, že každá aplikace je jedinečná, je třeba vytvořit plány obnovení, které jsou přizpůsobené pro jednotlivé aplikace, a spustit testovací převzetí služeb při selhání na každém z nich.
- Aplikace a jejich závislosti se často mění. Chcete-li zajistit aktuálnost plánů obnovení, spusťte testovací převzetí služeb při selhání každé čtvrtletí každou aplikaci.

    ![Snímek obrazovky s příkladem plánu testovacího obnovení v Site Recovery](./media/recovery-plan-overview/rptest.png)

## <a name="watch-a-recovery-plan-video"></a>Podívejte se na video s plánem obnovení.

Podívejte se na video s rychlým příkladem znázorňujícího převzetí služeb při selhání pro plán obnovení pro aplikaci WordPress se dvěma vrstvami.
    
> [!VIDEO https://channel9.msdn.com/Series/Azure-Site-Recovery/One-click-failover-of-a-2-tier-WordPress-application-using-Azure-Site-Recovery/player]



## <a name="next-steps"></a>Další kroky

- [Vytvořte](site-recovery-create-recovery-plans.md) plán obnovení.
- [Spusťte](site-recovery-failover.md) převzetí služeb při selhání. 
