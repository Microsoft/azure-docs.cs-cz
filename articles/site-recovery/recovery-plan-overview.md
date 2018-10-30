---
title: Pomocí plánů obnovení zotavení po havárii pomocí Azure Site Recovery | Dokumentace Microsoftu
description: Další informace o použití plány obnovení pro zotavení po havárii pomocí služby Azure Site Recovery.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: article
ms.date: 10/28/2018
ms.author: raynew
ms.openlocfilehash: 463a7fc51cac6160fbb97fc53077d0b5eda80533
ms.sourcegitcommit: 6e09760197a91be564ad60ffd3d6f48a241e083b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/29/2018
ms.locfileid: "50215187"
---
# <a name="about-recovery-plans"></a>Plány obnovení

Tento článek popisuje plány obnovení v [Azure Site Recovery](site-recovery-overview.md).

Plán obnovení shromažďuje počítače do skupin pro obnovení. Plán můžete přizpůsobit tak, že přidáte k němu pořadí, pokyny a úkoly. Po definování plánu můžete spustit převzetí služeb při selhání na něj.



## <a name="why-use-a-recovery-plan"></a>Proč použít plán obnovení?

Plán obnovení vám umožní definovat proces systematické obnovení, tak, že vytvoříte malých nezávislých jednotek, které jste převzetí služeb při selhání. Jednotka představuje obvykle aplikace ve vašem prostředí. Plán obnovení definuje, jak počítače převzetí služeb při selhání a pořadí, ve kterém začnou po převzetí služeb při selhání. Pomocí plánů obnovení pro:

* Modelujte aplikaci kolem jeho závislosti.
* Automatizace úloh obnovení ke snížení RTO.
- Ověřte, že jste přípravy na migraci nebo po havárii pro obnovení tím, že zajišťuje, že vaše aplikace jsou součástí plánu obnovení.
* Spusťte testovací převzetí služeb při selhání na plány obnovení, a zajišťuje zotavení po havárii nebo migrace funguje podle očekávání.


## <a name="model-apps"></a>Model aplikace

Můžete naplánovat a vytvořte skupinu obnovení k zachycení vlastnosti specifické pro aplikaci. Jako příklad zvažte Typická třívrstvá aplikace s SQL serverem back-endu, middleware a webový front-end. Obvykle můžete přizpůsobit plán obnovení tak, aby počítače v jednotlivých vrstvách po převzetí služeb při selhání spustit ve správném pořadí.

    - Back-endu SQL by se měl spustit nejprve middleware další a nakonec webový front-end.
    - Toto počáteční pořadí zajistí, že aplikace funguje podle času poslední počítač spustí.
    - Toto pořadí zajistí, že když middleware spustí a pokusí se připojit k vrstvě SQL serveru, na úrovni serveru SQL Server je již spuštěna. 
    - Toto pořadí také pomáhá zajistit, aby front-endový server spustí poslední, tak, aby koncoví uživatelé nemusíte připojit adresu URL aplikace předtím, než se všechny součásti nahoru a spuštění a aplikace je připravena přijímat požadavky.

Pokud chcete vytvořit toto pořadí, přidejte skupiny do skupiny pro obnovení a přidat počítače do skupin. 
    - Pokud je zadáno pořadí, řazení se používá. Akce při paralelním spuštění, kde je to vhodné, ke zlepšení obnovení aplikace RTO.
    - Počítače v jedné skupině převzetí služeb při selhání paralelně.
    - Počítače v různých skupinách převzetí služeb při selhání v pořadí skupin tak, aby počítače 2. skupina spustí jejich převzetí služeb při selhání až po převzetí služeb při selhání a spustit všechny počítače v 1 skupině.

    ![Příklad plánu obnovení](./media/recovery-plan-overview/rp.png)

Pomocí této úprav na místě zde je, co se stane, když spustíte převzetí služeb při selhání v plánu obnovení: 

1. Vypnutí krok pokusy o vypnout na místních počítačích. Výjimkou je, pokud spustíte testovací převzetí služeb, v takovém případě primární lokality zůstane spuštěný. 
2. Vypnutí počítače spustí paralelní převzetí služeb při selhání všech počítačů v plánu obnovení.
3. Převzetí služeb při selhání připraví disky virtuálních počítačů pomocí replikovaná data.
4. Po spuštění skupiny spuštěny v pořadí a spuštění počítačů v každé skupině. Nejprve skupiny 1 povolí, poběží, pak 2. skupina a nakonec, 3. skupina. Pokud existuje více než jeden počítač v libovolné skupině, všechny počítače spustit paralelně.


## <a name="automate-tasks"></a>Automatizace úloh

Obnovení velké aplikace může být složitý úkol. Vyžadováno provedení ručních kroků zjednodušit proces náchylné k chybám a dokončeny převzetí služeb při selhání se nemusíte být vědomi složitými rozhraními všechny aplikace. Můžete použít plán obnovení a stanovit pořadí a automatizaci akcí, třeba v každém kroku pomocí runbooků Azure Automation pro převzetí služeb při selhání do Azure nebo skriptů. Pro úlohy, které nelze automatizovat můžete vložit do plánů obnovení pozastaví ručně prováděné akce. Existuje několik typů úloh, které můžete nakonfigurovat:

* **Úlohy na virtuálním počítači Azure po převzetí služeb při selhání**: Pokud jste už převzetí služeb při selhání do Azure, je obvykle potřeba provádět akce, aby mohl připojit k virtuálnímu počítači po převzetí služeb při selhání. Příklad: 
    * Vytvoření veřejné IP adresy na virtuálním počítači Azure.
    * Přiřazení skupiny zabezpečení sítě k síťovému adaptéru virtuálního počítače Azure.
    * Nástroj pro vyrovnávání zatížení přidáte do skupiny dostupnosti.
* **Úlohy uvnitř virtuálního počítače po převzetí služeb při selhání**: tyto úlohy obvykle překonfigurovat aplikace spuštěné na počítači, tak, aby i nadále fungovat správně v novém prostředí. Příklad:
    * Upravte připojovací řetězec databáze na počítači.
    * Změna konfigurace webového serveru nebo pravidla.


## <a name="test-failover"></a>Testovací převzetí služeb při selhání

Plán obnovení můžete použít k aktivaci testovací převzetí služeb při selhání. Použijte následující osvědčené postupy:

- Vždy dokončete testovací převzetí služeb v aplikaci, před spuštěním úplné převzetí služeb při selhání. Testovací převzetí služeb při selhání můžete zkontrolovat, jestli aplikace se zobrazí v lokalitě pro obnovení.
- Pokud zjistíte, že jste něco Zmeškali, aktivovat čisté nahoru a poté znovu spusťte testovací převzetí služeb. 
- Spustíte testovací převzetí služeb více než jednou, dokud si jistí, plynule obnoví aplikace.
- Protože každá aplikace je jedinečný, musíte sestavit plány obnovení, které jsou přizpůsobené pro každou aplikaci a spustit testovací převzetí služeb v každém.
- Aplikace a jejich závislosti často měnit. K zajištění aktuálnosti plány obnovení, spusťte testovací převzetí služeb při selhání pro každou aplikaci každé čtvrtletí.

    ![Snímek obrazovky s příkladem testovacího plánu obnovení ve službě Site Recovery](./media/recovery-plan-overview/rptest.png)

## <a name="watch-the-video"></a>Přehrát video

Podívejte se na rychlý příklad video zobrazující kliknutím na převzetí služeb při selhání pro dvouvrstvou aplikaci WordPress.
    
> [!VIDEO https://channel9.msdn.com/Series/Azure-Site-Recovery/One-click-failover-of-a-2-tier-WordPress-application-using-Azure-Site-Recovery/player]



## <a name="next-steps"></a>Další postup

- [Vytvoření](site-recovery-create-recovery-plans.md) plánu obnovení.
* Další informace o [spuštění převzetí služeb při selhání](site-recovery-failover.md).  
