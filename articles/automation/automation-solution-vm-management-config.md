---
title: Konfigurace řešení virtuálních počítače Start/Stop
description: Tento článek popisuje, jak nakonfigurovat spuštění a zastavení virtuálních počítače během mimo pracovní dobu řešení pro podporu různých případů použití nebo scénáře.
services: automation
ms.subservice: process-automation
ms.date: 04/01/2020
ms.topic: conceptual
ms.openlocfilehash: d3ca8d17d6637f0ab2b5a5d3d7a99ac0beaafd2e
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/02/2020
ms.locfileid: "80550380"
---
# <a name="how-to-configure-startstop-vms-during-off-hours-solution"></a>Jak nakonfigurovat řešení spuštění a zastavení virtuálních počítače mimo pracovní dobu

S řešením Start/Stop Virtuální chod mimo pracovní dobu můžete:

- [Naplánujte spuštění a zastavení virtuálních montovek](#schedule).
- Naplánujte, aby se virtuální počítače spouštěla a zastavovala ve vzestupném pořadí [pomocí značek Azure](#tags) (není podporována pro klasické virtuální počítače).
- Automatické zastavení virtuálních počítačů na základě [nízkého využití procesoru](#cpuutil).

Tento článek popisuje, jak úspěšně nakonfigurovat řešení pro podporu těchto scénářů. Můžete se také dozvědět, jak provést další běžná nastavení konfigurace řešení, například:

* [Konfigurace e-mailových oznámení](#configure-email-notifications)

* [Přidání virtuálního počítače](#add-a-vm)

* [Vyloučení virtuálního počítače](#exclude-a-vm)

* [Úprava plánů spuštění a vypnutí](#modify-the-startup-and-shutdown-schedules)

## <a name="scenario-1-startstop-vms-on-a-schedule"></a><a name="schedule"></a>Scénář 1: Spuštění a zastavení virtuálních počítačů podle plánu

Tento scénář je výchozí konfigurace při prvním nasazení řešení. Můžete ji například nakonfigurovat tak, aby zastavila všechny virtuální počítače v rámci předplatného, když večer opustíte práci, a začít je ráno, když jste zpátky v kanceláři. Když nakonfigurujete plány **Naplánované StartVM** a **Scheduled-StopVM** během nasazení, spustí a zastaví cílené virtuální počítače. Konfigurace tohoto řešení pouze zastavit virtuální počítače je podporována, najdete [v tématu Úprava plánů spuštění a vypnutí se dozvíte,](#modify-the-startup-and-shutdown-schedules) jak nakonfigurovat vlastní plán.

> [!NOTE]
> Časové pásmo je aktuální časové pásmo při konfiguraci parametru plánu času. Je však uloženve formátu UTC v Azure Automation. Není třeba provést převod časového pásma, protože je zpracována během nasazení.

Virtuální chody jsou v oboru, které najdete tak, že nakonfigurujete následující proměnné: **External_Start_ResourceGroupNames**, **External_Stop_ResourceGroupNames**a **External_ExcludeVMNames**.

Můžete povolit cílení akce proti předplatnému a skupině prostředků nebo cílení na konkrétní seznam virtuálních aplikací, ale ne obojí.

### <a name="target-the-start-and-stop-actions-against-a-subscription-and-resource-group"></a>Cílení na akce zahájení a zastavení proti předplatnému a skupině prostředků

1. Nakonfigurujte **proměnné External_Stop_ResourceGroupNames** a **External_ExcludeVMNames** k určení cílových virtuálních počítače.

2. Povolte a aktualizujte plány **Naplánovaného startvm** a **plánovaného stopvm.**

3. Spusťte **ScheduledStartStop_Parent** runbook s parametrem ACTION nastaveným na **spuštění** a parametrem WHATIF nastaveným na **Hodnotu True,** aby se zobrazil náhled změn.

### <a name="target-the-start-and-stop-action-by-vm-list"></a>Cílení na počáteční a zastavení akce podle seznamu virtuálních mís

1. **Spusťte ScheduledStartStop_Parent** runbook s parametrem ACTION nastaveným na **začátek**, přidejte seznam virtuálních knih odstředěných čárkami do parametru *VMList* a nastavte parametr WHATIF na **hodnotu True**. Prohlédněte si náhled změn.

2. Nakonfigurujte **parametr External_ExcludeVMNames** se seznamem virtuálních počítače odděleným čárkami (VM1, VM2, VM3).

3. Tento scénář nerespektuje **proměnné External_Start_ResourceGroupNames** a **External_Stop_ResourceGroupnames.** V tomto scénáři je třeba vytvořit vlastní plán automatizace. Podrobnosti najdete [v tématu Plánování runbooku v Azure Automation](../automation/automation-schedules.md).

    > [!NOTE]
    > Hodnota pro **cílové názvy skupin zdrojů** je uložena jako hodnota pro **External_Start_ResourceGroupNames** i **External_Stop_ResourceGroupNames**. Pro další rozlišovací schopnost můžete upravit každou z těchto proměnných tak, aby cílila na různé skupiny prostředků. Pro akci zahájení použijte **External_Start_ResourceGroupNames**a pro akci stop použijte **External_Stop_ResourceGroupNames**. Virtuální aplikace se automaticky přidají do plánů zahájení a zastavení.

## <a name="scenario-2-startstop-vms-in-sequence-by-using-tags"></a><a name="tags"></a>Scénář 2: Spuštění a zastavení virtuálního počítače v pořadí pomocí značek

V prostředí, které obsahuje dvě nebo více součástí na více virtuálních počítačích podporujících distribuované úlohy, je důležité podporovat pořadí, ve kterém jsou komponenty spuštěny a zastaveny v pořadí. Tento scénář můžete provést provedením následujících kroků:

### <a name="target-the-start-and-stop-actions-against-a-subscription-and-resource-group"></a>Cílení na akce zahájení a zastavení proti předplatnému a skupině prostředků

1. Přidejte **sequencestart** a **sequencestop** tag s kladnou hodnotou celé číslo do virtuálních disek, které jsou cílené v **External_Start_ResourceGroupNames** a **External_Stop_ResourceGroupNames** proměnných. Akce start a stop se provádějí ve vzestupném pořadí. Informace o tom, jak označit virtuální počítač, najdete [v tématu Označení virtuálního počítače s Windows v Azure](../virtual-machines/windows/tag.md) a označení [virtuálního počítače s Linuxem v Azure](../virtual-machines/linux/tag.md).

2. Upravte plány **Sequenced-StartVM** a **Sequenced-StopVM** na datum a čas, které splňují vaše požadavky a umožňují plán.

3. Spusťte **SequencedStartStop_Parent** runbook s parametrem ACTION nastaveným na **spuštění** a parametrem WHATIF nastaveným na **hodnotu True** pro zobrazení náhledu změn.

4. Náhled akce a provést všechny nezbytné změny před implementací proti produkční virtuální chod. Až budete připraveni, ručně spusťte runbook s parametrem nastaveným na **False**nebo nechte automatizaci naplánovat **sekvenční StartVM** a **Sequenced-StopVM** spustit automaticky podle předepsaného plánu.

### <a name="target-the-start-and-stop-action-by-vm-list"></a>Cílení na počáteční a zastavení akce podle seznamu virtuálních mís

1. Přidejte **sequencestart** a **sequencestop** tag s kladnou hodnotou celé číslo do virtuálních disek, které chcete přidat do parametru **VMList.**

2. **Spusťte SequencedStartStop_Parent** runbook s parametrem ACTION nastaveným na **začátek**, přidejte seznam virtuálních knih odstředěných čárkami do parametru *VMList* a nastavte parametr WHATIF na **hodnotu True**. Prohlédněte si náhled změn.

3. Nakonfigurujte **parametr External_ExcludeVMNames** se seznamem virtuálních počítače odděleným čárkami (VM1, VM2, VM3).

4. Tento scénář nerespektuje **proměnné External_Start_ResourceGroupNames** a **External_Stop_ResourceGroupnames.** V tomto scénáři je třeba vytvořit vlastní plán automatizace. Podrobnosti najdete [v tématu Plánování runbooku v Azure Automation](../automation/automation-schedules.md).

5. Náhled akce a provést všechny nezbytné změny před implementací proti produkční virtuální chod. Až budete připraveni, ručně spusťte monitorování a diagnostiku/monitorování-akce-groupsrunbook s parametrem **nastaveným**na False , nebo nechte automatizace naplánovat **sequenced-StartVM** a **Sequenced-StopVM** spustit automaticky podle předepsaného plánu.

## <a name="scenario-3-startstop-automatically-based-on-cpu-utilization"></a><a name="cpuutil"></a>Scénář 3: Spuštění a zastavení automaticky na základě využití procesoru

Toto řešení může pomoci spravovat náklady na spuštění Azure Resource Manager a klasické virtuální počítače ve vašem předplatném vyhodnocením virtuálních počítačů, které se nepoužívají během období mimo špičku, jako je například po hodinách, a jejich automatické vypnutí, pokud je využití procesoru menší než zadané procento.

Ve výchozím nastavení je řešení předem nakonfigurováno tak, aby vyhodnotilo metriku procenta procesoru, aby se zjistilo, zda je průměrné využití 5 procent nebo méně. Tento scénář je řízen následujícími proměnnými a lze jej změnit, pokud výchozí hodnoty nesplňují vaše požadavky:

* **External_AutoStop_MetricName**

* **External_AutoStop_Threshold**

* **External_AutoStop_TimeAggregationOperator**

* **External_AutoStop_TimeWindow**

* **External_AutoStop_Frequency**

* **External_AutoStop_Severity**

Můžete povolit a cílit na akci proti předplatnéa skupiny prostředků nebo cílit na konkrétní seznam virtuálních aplikací.

Když spustíte **AutoStop_CreateAlert_Parent** runbook, ověří, že cílové předplatné, skupiny prostředků a virtuální chody existují. Pokud virtuální společnosti existují, pak volá **AutoStop_CreateAlert_Child** runbook pro každý ověřený virtuální účet nadřazeným runbookem. Tato podřízená runbook provádí následující:

* Vytvoří pravidlo upozornění metriky pro každý ověřený virtuální účet.

* Aktivuje **AutoStop_VM_Child** runbook pro konkrétní virtuální počítače, pokud by procesor klesl pod nakonfigurovanou prahovou hodnotu pro zadaný časový interval. Tento runbook se pak pokusí zastavit virtuální ho.

### <a name="to-target-the-auto-stop-action-against-all-vms-in-a-subscription"></a>Cílení akce automatického zastavení proti všem virtuálním virtuálním mům v předplatném

1. Ujistěte se, **že proměnná External_Stop_ResourceGroupNames** je prázdná nebo nastavena na * (zástupný znak).

2. [Volitelný krok] Pokud chcete vyloučit některé virtuální počítače z automatického vypnutí, můžete přidat seznam názvů virtuálních počítačů oddělených čárkami do **proměnné External_ExcludeVMNames.**

3. Povolte spuštění **Schedule_AutoStop_CreateAlert_Parent** plánu a vytvořte požadovaná pravidla upozornění na metriku stop virtuálního počítače pro všechny virtuální počítače ve vašem předplatném. Spuštění tohoto podle plánu vám umožní vytvořit nová pravidla upozornění metriky jako nové virtuální chody jsou přidány do předplatného.

### <a name="to-target-the-auto-stop-action-against-all-vms-in-a-resource-groupmultiple-resource-groups"></a>Cílení akce automatického zastavení proti všem virtuálním počítačům ve skupině prostředků nebo více skupinách prostředků

1. Přidejte do proměnné **External_Stop_ResourceGroupNames** společný oddělený seznam názvů skupin prostředků.

2. Volitelně pokud chcete vyloučit některé virtuální počítače z automatického vypnutí, můžete přidat seznam názvů virtuálních počítačů oddělených čárkami do **proměnné External_ExcludeVMNames.**

3. Povolte spuštění **Schedule_AutoStop_CreateAlert_Parent** plánu a vytvořte požadovaná pravidla upozornění **na metriku stop virtuálního počítače** pro všechny virtuální počítače ve skupinách prostředků. Spuštění tohoto podle plánu umožňuje vytvořit nová pravidla upozornění metriky jako nové virtuální chody jsou přidány do skupiny prostředků.

### <a name="to-target-the-auto-stop-action-to-a-list-of-vms"></a>Cílení akce automatického zastavení na seznam virtuálních<a0 000

1. Vytvořte nový [plán](shared-resources/schedules.md#creating-a-schedule) a propojte jej se **AutoStop_CreateAlert_Parent** runbook, přidání čárky oddělený seznam názvů virtuálních počítačů do parametru **VMList.**

2. Volitelně pokud chcete vyloučit některé virtuální počítače z automatického vypnutí, můžete přidat seznam názvů virtuálních počítačů oddělených čárkami do **proměnné External_ExcludeVMNames.**

## <a name="configure-email-notifications"></a>Konfigurace e-mailových oznámení

Chcete-li změnit e-mailová oznámení po nasazení řešení, upravte skupinu akcí, která byla vytvořena během nasazení.  

> [!NOTE]
> Předplatná v Azure Government Cloud nepodporují e-mailové funkce tohoto řešení.

1. Na webu Azure Portal přejděte do skupiny sledování -> akcí. Vyberte skupinu akcí s názvem **StartStop_VM_Notication**.

    ![Stránka řešení správa aktualizací automatizace](media/automation-solution-vm-management/azure-monitor.png)

2. Na **stránce StartStop_VM_Notification** klikněte v části **Podrobnosti**na Upravit **podrobnosti** . Otevře se stránka **E-mail/SMS/Push/Voice.** Aktualizujte e-mailovou adresu a kliknutím na **OK** změny uložte.

    ![Stránka řešení správa aktualizací automatizace](media/automation-solution-vm-management/change-email.png)

    Případně můžete do skupiny akcí přidat další akce, abyste se dozvěděli více o skupinách akcí, viz [skupiny akcí](../azure-monitor/platform/action-groups.md)

Následuje příklad e-mailu, který je odeslán při vypnutí řešení virtuálních počítačů.

![Stránka řešení správa aktualizací automatizace](media/automation-solution-vm-management/email.png)

## <a name="addexclude-vms"></a><a name="add-exclude-vms"></a>Přidání nebo vyloučení virtuálních měn

Řešení poskytuje možnost přidávat virtuální počítače, které mají být cílem řešení nebo konkrétně vyloučit počítače z řešení.

### <a name="add-a-vm"></a>Přidání virtuálního počítače

Existují dvě možnosti, které můžete použít k ujistěte se, že virtuální ho virtuálního zařízení je součástí řešení Start/Stop při spuštění.

* Každá nadřazená [runbooky](automation-solution-vm-management.md#runbooks) řešení má parametr **VMList.** Můžete předat seznam názvů virtuálních počítačů oddělených čárkami tomuto parametru při plánování příslušnénadřazené runbook pro vaši situaci a tyto virtuální počítače budou zahrnuty při spuštění řešení.

* Pokud chcete vybrat víc virtuálních počítačů, nastavte **External_Start_ResourceGroupNames** a **External_Stop_ResourceGroupNames** s názvy skupin prostředků, které obsahují virtuální počítač, který chtějí spustit nebo zastavit. Můžete také nastavit tuto `*`hodnotu , aby bylo řešení spuštěno proti všem skupinám prostředků v předplatném.

### <a name="exclude-a-vm"></a>Vyloučení virtuálního počítače

Chcete-li vyloučit virtuální ho virtuálního zařízení z řešení, můžete jej přidat do **proměnné External_ExcludeVMNames.** Tato proměnná je seznam specifických virtuálních zařízení oddělený čárkami, který má být vyloučen z řešení Start/Stop. Tento seznam je omezen na 140 virtuálních stránek. Pokud přidáte více než 140 virtuálních stránek do tohoto seznamu odděleného čárkami, virtuální chody, které jsou nastaveny na vyloučení, mohou být neúmyslně spuštěny nebo zastaveny.

## <a name="modify-the-startup-and-shutdown-schedules"></a>Úprava plánů spuštění a vypnutí

Správa plánů spuštění a vypnutí v tomto řešení se řídí stejnými kroky, jako je popsáno v [plánování sady Runbook v Azure Automation](automation-schedules.md). Musí existovat samostatný plán pro spuštění a zastavení virtuálních můe.

Konfigurace řešení pouze zastavit virtuální chod v určitém čase je podporována. V tomto scénáři stačí vytvořit **plán Stop** a žádné odpovídající **Start** naplánováno. Budete muset:

1. Ujistěte se, že jste přidali skupiny prostředků pro virtuální chod, které mají být ukončeny v **proměnné External_Stop_ResourceGroupNames.**

2. Vytvořte si vlastní plán pro čas, který chcete vypnout virtuální počítače.

3. Přejděte do **ScheduledStartStop_Parent** runbooku a klepněte na **tlačítko Naplánovat**. To umožňuje vybrat plán, který jste vytvořili v předchozím kroku.

4. Vyberte **Parametry a spusťte nastavení** a nastavte parametr **AKCE** na **Zastavit**.

5. Výběrem **OK** uložte změny.

## <a name="next-steps"></a>Další kroky

* Informace o tom, jak řešit potíže s virtuálními jevy Start/Stop mimo pracovní dobu, najdete [v tématu Poradce při potížích s virtuálními jevy start/stop](troubleshoot/start-stop-vm.md).

* [Zkontrolujte](automation-solution-vm-management-logs.md) záznamy automatizace zapsané do protokolů monitorování Azure a ukázkové vyhledávací dotazy protokolu a analyzujte stav úloh runbooku automatizace z virtuálních počítačů Start/Stop.
