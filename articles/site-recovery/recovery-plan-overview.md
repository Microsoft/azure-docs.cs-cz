---
title: Používání plánů obnovení v Azure Site Recovery | Microsoft Docs
description: Další informace o plánech obnovení v Azure Site Recovery.
services: site-recovery
documentationcenter: ''
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.date: 03/21/2018
ms.author: raynew
ms.openlocfilehash: 871c9e8404438f966cab2fc5ab782e254295569e
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/23/2018
---
# <a name="about-recovery-plans"></a>O plánů obnovení

Tento článek popisuje plány obnovení v [Azure Site Recovery](site-recovery-overview.md).

Plán obnovení shromažďuje počítače do skupin pro obnovení. Plán můžete přizpůsobit tak, že k němu přidáte pořadí, pokynů a úlohy. Po definování plánu můžete spustit převzetí služeb při selhání na něm.





## <a name="why-use-a-recovery-plan"></a>Proč používat plán obnovení?

Plán obnovení umožňuje definovat proces systematické obnovení, vytvořením malých nezávislé jednotek, jež může převzít. Jednotka obvykle reprezentuje aplikace ve vašem prostředí. Plán obnovení definuje, jak počítače převzetí služeb při selhání a pořadí, ve kterém se spustit po převzetí služeb při selhání. Použijte plány obnovení pro:

* Model aplikace kolem jeho závislé součásti.
* Automatizovat úlohy obnovení ke snížení RTO.
- Ověřte, jestli máte připravené pro migraci nebo po havárii pro obnovení tím, že zajistí, že vaše aplikace jsou součástí plánu obnovení.
* Spustit test převzetí služeb při selhání v plánech obnovení, aby zotavení po havárii nebo migrace funguje podle očekávání.


## <a name="model-apps"></a>Model aplikace

Můžete naplánovat a vytvořit skupiny pro obnovení k zachycení vlastnosti specifické pro aplikace. Jako příklad zvažte Typická třívrstvá aplikace s SQL serverem back-end, middleware a front-end webové. Plán obnovení se obvykle přizpůsobit tak, aby počítače v jednotlivých vrstvách spustit ve správném pořadí po převzetí služeb při selhání.

    - Back-end SQL by se měl spustit nejprve middleware další a nakonec front-endu webové.
    - Tento počáteční pořadí zajistí, že aplikace funguje podle času poslední počítač spustí.
    - Toto pořadí zajistí, že když middleware spustí a pokusí se připojit k vrstvě systému SQL Server, vrstvy systému SQL Server je již spuštěna. 
    - Toto pořadí taky pomáhá zajistit, aby front-end serveru spustí poslední, tak, aby koncoví uživatelé Nepřipojovat a adresu URL aplikace předtím, než jsou všechny součásti a spuštění aplikace je připravena přijímat požadavky.

Pokud chcete vytvořit toto pořadí, přidání skupiny do skupiny pro obnovení a přidat počítače do skupin. 
    - Tam, kde je zadán pořadí, se používá sekvencování. Akce spustit souběžně, kde je to vhodné pro zlepšení aplikace obnovení RTO.
    - Počítače v jedné skupině selhání paralelně.
    - Počítače v různých skupinách převzetí služeb při selhání ve skupině pořadí tak, aby počítače 2. skupina spustí jejich převzetí služeb při selhání pouze po převzetí služeb při selhání a spustit všechny počítače ve skupině 1.

    ![Příklad plánu obnovení](./media/recovery-plan-overview/rp.png)

S toto vlastní nastavení na místě zde je, co se stane, když spustíte převzetí služeb při selhání v plánu obnovení: 

1. Krok vypnutí se pokusí vypněte místní počítače. Výjimkou je, pokud spustíte testovací převzetí služeb, v takovém případě primární lokality stále běží. 
2. Ukončení aktivuje paralelní převzetí služeb při selhání všech počítačů v plánu obnovení.
3. Převzetí služeb při selhání připraví disky virtuálního počítače pomocí replikovaná data.
4. Spuštění skupiny spuštěny v pořadí a spuštění počítačů v každé skupině. Nejprve 1. skupina spouští, pak 2. skupina a nakonec 3. skupina. Pokud je v kterékoli skupině více než jeden počítač, pak všechny počítače spusťte paralelně.


## <a name="automate-tasks"></a>Automatizaci úloh

Obnovení velké aplikace může být složité úlohy. Provedení ručních kroků proveďte proces náchylné k chybám a dokončeny převzetí služeb při selhání, nemusíte být vědomi všechny rozbor všech aplikací. Můžete použít plán obnovení ukládat pořadí a automatizaci akcí potřebných při každém kroku, pomocí Azure Automation runbook pro převzetí služeb při selhání do Azure nebo skripty. Pro úlohy, které nelze je možné automatizovat můžete vložit do plánů obnovení pozastaví pro ruční akce. Existuje několik typů úloh, které můžete konfigurovat:

* **Úlohy na virtuálním počítači Azure po převzetí služeb při selhání**: Pokud jste selhání do Azure, obvykle potřeba provádět akce, aby mohl připojit k virtuálnímu počítači po převzetí služeb při selhání. Příklad: 
    * Vytvoření veřejné IP adresy pro virtuální počítač Azure.
    * Skupina zabezpečení sítě přiřaďte k síťovému adaptéru virtuálního počítače Azure.
    * Nástroj pro vyrovnávání zatížení přidáte do skupiny dostupnosti.
* **Úlohy uvnitř virtuálního počítače po převzetí služeb při selhání**: tyto úlohy spuštěné na počítači aplikace obvykle překonfigurovat tak, aby nadále fungovat správně v nové verzi prostředí. Příklad:
    * Upravte připojovací řetězec databáze uvnitř počítače.
    * Změna konfigurace webového serveru nebo pravidla.


## <a name="test-failover"></a>Testovací převzetí služeb při selhání

Plán obnovení můžete použít k aktivaci převzetí služeb při selhání. Použijte následující osvědčené postupy:

- Vždy dokončení převzetí služeb při selhání v aplikaci, před spuštěním úplné převzetí služeb při selhání. Testovací převzetí služeb při selhání můžete zkontrolovat, zda aplikace se dodává v lokalitě pro obnovení.
- Pokud zjistíte, že jste něco vynechán, aktivovat čisté nahoru a znovu spusťte testu převzetí služeb. 
- Spusťte testovací převzetí služeb vícekrát, dokud si jisti, plynule obnoví aplikace.
- Protože každá aplikace je jedinečný, musíte sestavit plány obnovení, které jsou přizpůsobené pro každou aplikaci a spustit testovací převzetí služeb v každém.
- Aplikace a jejich závislosti často mění. K zajištění aktuálnosti plány obnovení, spusťte testovací převzetí služeb při selhání pro každou aplikaci každé čtvrtletí.

    ![Snímek obrazovky příklad testovací plán obnovení ve službě Site Recovery](./media/recovery-plan-overview/rptest.png)

## <a name="watch-the-video"></a>Přehrát video

Přehrát video zběžný Příklad zobrazující klikněte na na převzetí služeb při selhání pro aplikaci WordPress dvouvrstvá.
    
> [!VIDEO https://channel9.msdn.com/Series/Azure-Site-Recovery/One-click-failover-of-a-2-tier-WordPress-application-using-Azure-Site-Recovery/player]



## <a name="next-steps"></a>Další postup

- [Vytvoření](site-recovery-create-recovery-plans.md) plán obnovení.
* Další informace o [systémem převzetí služeb při selhání](site-recovery-failover.md).  
