---
title: O plánech obnovení v Azure Site Recovery
description: Další informace o plánech obnovení v Azure Site Recovery.
ms.topic: conceptual
ms.date: 01/23/2020
ms.openlocfilehash: beb92bd62d011ef8aaf304dbb769e7694e6d7e60
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79257768"
---
# <a name="about-recovery-plans"></a>Plány obnovení

Tento článek obsahuje přehled plánů obnovení v [Azure Site Recovery](site-recovery-overview.md).

Plán obnovení shromažďuje počítače do skupin obnovení za účelem převzetí služeb při selhání. Plán obnovení vám pomůže definovat systematický proces obnovení vytvořením malých nezávislých jednotek, které můžete přepojit převzetím služeb při selhání. Jednotka obvykle představuje aplikaci ve vašem prostředí.

- Plán obnovení definuje, jak počítače převzetí služeb při selhání a pořadí, ve kterém se spustí po převzetí služeb při selhání.
- Plány obnovení se používají pro převzetí služeb při selhání do Azure, ale nelze použít pro navrácení služeb po obnovení z Azure.
- Do jednoho plánu obnovení lze přidat až 100 chráněných instancí.
- Plán můžete přizpůsobit přidáním objednávky, pokynů a úkolů.
- Po definování plánu můžete spustit převzetí služeb při selhání na něm.
- Na počítače lze odkazovat ve více plánech obnovení, ve kterých následné plány přeskočí nasazení nebo spuštění počítače, pokud byl dříve nasazen pomocí jiného plánu obnovení.



### <a name="why-use-a-recovery-plan"></a>Proč použít plán obnovení?

Plány obnovení použijte k:

* Modelujte aplikaci podle jejích závislostí.
* Automatizujte úlohy obnovení, abyste zkrátili cíl doby obnovení (RTO).
* Ověřte, zda jste připraveni na migraci nebo zotavení po havárii, a to tak, že zajistíte, aby vaše aplikace byly součástí plánu obnovení.
* Spuštění testovacích převzetí služeb při selhání v plánech obnovení, abyste zajistili zotavení po havárii nebo migraci, funguje podle očekávání.


## <a name="model-apps"></a>Modelovat aplikace 
Můžete naplánovat a vytvořit skupinu obnovení pro zachycení vlastností specifických pro aplikaci. Jako příklad zvažme typické třívrstvé aplikace s back-endem serveru SQL, middleware a web front-end. Obvykle přizpůsobit plán obnovení tak, aby počítače v každé vrstvě spustit ve správném pořadí po převzetí služeb při selhání.

- Back-end SQL by měl začít jako první, middleware další a nakonec web front-end.
- Toto pořadí spuštění zajišťuje, že aplikace pracuje v době, kdy se spustí poslední počítač.
- Toto pořadí zajišťuje, že při spuštění middlewaru a pokusu o připojení k vrstvě SQL Server je již spuštěna úroveň SQL Server. 
- Toto pořadí také pomáhá zajistit, aby front-endový server začal jako poslední, aby se koncoví uživatelé nepřipojovali k adrese URL aplikace dříve, než budou všechny součásti v provozu a aplikace je připravena přijímat požadavky.

Chcete-li vytvořit toto pořadí, přidejte skupiny do skupiny pro obnovení a přidat počítače do skupin.
- Tam, kde je zadán pořadí, sesekvencování se používá. Akce spustit paralelně podle potřeby ke zlepšení obnovení aplikace RTO.
- Počítače v jedné skupině převzetí služeb při selhání paralelně.
- Počítače v různých skupinách převzetí služeb při selhání v pořadí skupiny tak, aby počítače skupiny 2 spustit jejich převzetí služeb při selhání pouze po převzetí služeb při selhání a všechny počítače ve skupině 1 byly převzetí služeb při selhání a spuštění.

    ![Příklad plánu obnovy](./media/recovery-plan-overview/rp.png)

S tímto přizpůsobením na místě, tady je to, co se stane, když spustíte převzetí služeb při selhání na plán obnovení: 

1. Krok vypnutí se pokusí vypnout místní počítače. Výjimkou je, pokud spustíte test převzetí služeb při selhání, v takovém případě primární lokality nadále spustit. 
2. Vypnutí spustí paralelní převzetí služeb při selhání všech počítačů v plánu obnovení.
3. Převzetí služeb při selhání připraví disky virtuálního počítače pomocí replikovaných dat.
4. Spouštěcí skupiny spustit v pořadí a spustit počítače v každé skupině. Nejprve skupina 1 vede, pak skupina 2 a nakonec skupina 3. Pokud je v libovolné skupině více než jeden počítač, pak všechny stroje začínají paralelně.


## <a name="automate-tasks-in-recovery-plans"></a>Automatizace úloh v plánech obnovení

Obnovení velkých aplikací může být složitý úkol. Ruční kroky, aby proces náchylný k chybě a osoba, která používá převzetí služeb při selhání nemusí být vědomi všech složitostí aplikace. Pomocí plánu obnovení můžete vytvořit pořadí a automatizovat akce potřebné v každém kroku pomocí runbooků Azure Automation pro převzetí služeb při selhání do Azure nebo skripty. U úkolů, které nelze automatizovat, můžete do plánů obnovení vložit pauzy pro ruční akce. Existuje několik typů úkolů, které můžete nakonfigurovat:

* **Úkoly na virtuálním počítači Azure po převzetí služeb při selhání**: Když jste převzetí služeb při selhání do Azure, obvykle potřebujete provést akce, takže se můžete připojit k virtuálnímu počítači po převzetí služeb při selhání. Například: 
    * Vytvořte veřejnou IP adresu na virtuálním počítači Azure.
    * Přiřaďte skupině zabezpečení sítě k síťovému adaptéru virtuálního počítače Azure.
    * Přidejte do skupiny dostupnosti odhad zatížení.
* **Úlohy uvnitř virtuálního počítače po převzetí služeb při selhání**: Tyto úlohy obvykle překonfigurovat aplikaci spuštěnou v počítači tak, aby i nadále fungovat správně v novém prostředí. Například:
    * Upravte připojovací řetězec databáze uvnitř počítače.
    * Změňte konfiguraci nebo pravidla webového serveru.


### <a name="run-a-test-failover-on-recovery-plans"></a>Spuštění zkušebního převzetí služeb při selhání v plánech obnovení

Plán obnovení můžete použít k aktivaci zkušební převzetí služeb při selhání. Používejte následující doporučené postupy:

- Před spuštěním úplného převzetí služeb při selhání vždy dokončete zkušební převzetí služeb při selhání v aplikaci. Testovací převzetí služeb při selhání vám pomůže zkontrolovat, zda se aplikace objeví na webu pro obnovení.
- Pokud zjistíte, že jste něco vynechali, aktivujte vyčištění a potom znovu spusťte převzetí služeb při selhání testu. 
- Spusťte zkušební převzetí služeb při selhání vícekrát, dokud si nejste jisti, že se aplikace obnoví hladce.
- Vzhledem k tomu, že každá aplikace je jedinečná, je třeba vytvořit plány obnovení, které jsou přizpůsobeny pro každou aplikaci, a spustit zkušební převzetí služeb při selhání v každé aplikaci.
- Aplikace a jejich závislosti se často mění. Chcete-li zajistit, aby byly plány obnovení aktuální, spusťte testovací převzetí služeb při selhání pro každou aplikaci každé čtvrtletí.

    ![Snímek obrazovky s ukázkovou testovací majekvatou v lokalitě Recovery](./media/recovery-plan-overview/rptest.png)

## <a name="watch-a-recovery-plan-video"></a>Podívejte se na video o plánu obnovení

Podívejte se na rychlé ukázkové video, které ukazuje převzetí služeb při selhání při kliknutí pro plán obnovení pro dvouvrstvou aplikaci WordPress.
    
> [!VIDEO https://channel9.msdn.com/Series/Azure-Site-Recovery/One-click-failover-of-a-2-tier-WordPress-application-using-Azure-Site-Recovery/player]



## <a name="next-steps"></a>Další kroky

- [Vytvořte](site-recovery-create-recovery-plans.md) plán obnovení.
- [Spuštění](site-recovery-failover.md) převzetí služeb při selhání. 
