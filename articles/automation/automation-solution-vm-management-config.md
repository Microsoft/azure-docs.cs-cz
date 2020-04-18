---
title: Azure Automation Konfigurace virtuálních počítačů Start/Stop mimo pracovní dobu
description: Tento článek popisuje, jak nakonfigurovat spuštění a zastavení virtuálních počítače během mimo pracovní dobu řešení pro podporu různých případů použití nebo scénáře.
services: automation
ms.subservice: process-automation
ms.date: 04/01/2020
ms.topic: conceptual
ms.openlocfilehash: 9842a736cf922e0490f2b0c8acb1d2e5833f3d6c
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/17/2020
ms.locfileid: "81604763"
---
# <a name="how-to-configure-startstop-vms-during-off-hours-solution"></a>Jak nakonfigurovat řešení spuštění a zastavení virtuálních počítače mimo pracovní dobu

S **řešením Start/Stop Virtuální chod mimo pracovní dobu** můžete:

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

1. Nakonfigurujte proměnné `External_Stop_ResourceGroupNames` a `External_ExcludeVMNames` určete cílové virtuální počítače.

2. Povolte a aktualizujte plány **Naplánovaného startvm** a **plánovaného stopvm.**

3. Spusťte **ScheduledStartStop_Parent** runbook s polem **parametru ACTION** nastaveným na **začátek** a polem parametru **WHATIF** nastaveným na hodnotu True pro zobrazení náhledu změn.

### <a name="target-the-start-and-stop-action-by-vm-list"></a>Cílení na počáteční a zastavení akce podle seznamu virtuálních mís

1. **Spusťte ScheduledStartStop_Parent** runbook s **nastavenou sadou AKCE** na **začátek**, přidejte seznam virtuálních knih odstředěných čárkami do pole parametru **VMList** a nastavte pole parametru **WHATIF** na hodnotu True. Prohlédněte si náhled změn.

2. Nakonfigurujte `External_ExcludeVMNames` proměnnou pomocí seznamu virtuálních počítače oddělených čárkami (VM1, VM2, VM3).

3. Tento scénář nerespektuje `External_Start_ResourceGroupNames` `External_Stop_ResourceGroupnames` a proměnné. V tomto scénáři je třeba vytvořit vlastní plán automatizace. Podrobnosti najdete [v tématu Plánování runbooku v Azure Automation](../automation/automation-schedules.md).

    > [!NOTE]
    > Hodnota pro **cílové názvy skupin y prostředků je** uložena jako hodnota pro obě a `External_Start_ResourceGroupNames` `External_Stop_ResourceGroupNames`. Pro další rozlišovací schopnost můžete upravit každou z těchto proměnných tak, aby cílila na různé skupiny prostředků. Pro počáteční akci `External_Start_ResourceGroupNames`použijte `External_Stop_ResourceGroupNames` a použijte pro akci stop. Virtuální aplikace se automaticky přidají do plánů zahájení a zastavení.

## <a name="scenario-2-startstop-vms-in-sequence-by-using-tags"></a><a name="tags"></a>Scénář 2: Spuštění a zastavení virtuálního počítače v pořadí pomocí značek

V prostředí, které obsahuje dvě nebo více součástí na více virtuálních počítačích podporujících distribuované úlohy, je důležité podporovat pořadí, ve kterém jsou komponenty spuštěny a zastaveny v pořadí. Tento scénář můžete provést provedením následujících kroků:

### <a name="target-the-start-and-stop-actions-against-a-subscription-and-resource-group"></a>Cílení na akce zahájení a zastavení proti předplatnému a skupině prostředků

1. Přidejte `sequencestart` a `sequencestop` značku s kladnou hodnotou celé číslo do `External_Start_ResourceGroupNames` `External_Stop_ResourceGroupNames` virtuálních zařízení, které jsou cílené v a proměnné. Akce start a stop se provádějí ve vzestupném pořadí. Informace o tom, jak označit virtuální počítač, najdete [v tématu Označení virtuálního počítače s Windows v Azure](../virtual-machines/windows/tag.md) a označení [virtuálního počítače s Linuxem v Azure](../virtual-machines/linux/tag.md).

2. Upravte plány **Sequenced-StartVM** a **Sequenced-StopVM** na datum a čas, které splňují vaše požadavky a umožňují plán.

3. Spusťte **SequencedStartStop_Parent** runbook s **nastavenou sadou AKCE** **a** **WHATIF** nastaveným na Hodnotu True, aby se zobrazil náhled změn.

4. Náhled akce a provést všechny nezbytné změny před implementací proti produkční virtuální chod. Až budete připraveni, ručně spusťte sadu runbook s `Sequenced-StartVM` parametrem na **hodnotu False**nebo nechte automatizační plán a `Sequenced-StopVM` spusťte automaticky podle předepsaného plánu.

### <a name="target-the-start-and-stop-action-by-vm-list"></a>Cílení na počáteční a zastavení akce podle seznamu virtuálních mís

1. Přidejte `sequencestart` a `sequencestop` značku s kladnou hodnotou celéčíslo do virtuálních `VMList` disek, které chcete přidat do parametru.

2. Spusťte **SequencedStartStop_Parent** runbook s **nastavenou sadou AKCE** na **spuštění**, přidejte seznam virtuálních knih odstředěných čárkami do pole parametru **VMList** a nastavte **whatif** na hodnotu True. Prohlédněte si náhled změn.

3. Nakonfigurujte `External_ExcludeVMNames` proměnnou pomocí seznamu virtuálních počítače oddělených čárkami (VM1, VM2, VM3).

4. Tento scénář nerespektuje `External_Start_ResourceGroupNames` `External_Stop_ResourceGroupnames` a proměnné. V tomto scénáři je třeba vytvořit vlastní plán automatizace. Podrobnosti najdete [v tématu Plánování runbooku v Azure Automation](../automation/automation-schedules.md).

5. Náhled akce a provést všechny nezbytné změny před implementací proti produkční virtuální chod. Když je připraven, ručně spusťte **monitorování a diagnostiku/monitorování-akce groupsrunbook** s parametrem nastaveným na **False**. Případně nechte automatizaci `Sequenced-StartVM` naplánovat a `Sequenced-StopVM` spustit automaticky podle předepsaného plánu.

## <a name="scenario-3-startstop-automatically-based-on-cpu-utilization"></a><a name="cpuutil"></a>Scénář 3: Spuštění a zastavení automaticky na základě využití procesoru

Toto řešení může pomoci spravovat náklady na spuštění Azure Resource Manager a klasické virtuální počítače ve vašem předplatném vyhodnocením virtuálních počítačů, které se nepoužívají během období mimo špičku, jako je například po hodinách, a jejich automatické vypnutí, pokud je využití procesoru menší než zadané procento.

Ve výchozím nastavení je řešení předem nakonfigurováno tak, aby vyhodnotilo metriku procenta procesoru, aby se zjistilo, zda je průměrné využití 5 procent nebo méně. Tento scénář je řízen následujícími proměnnými a lze jej změnit, pokud výchozí hodnoty nesplňují vaše požadavky:

* `External_AutoStop_MetricName`
* `External_AutoStop_Threshold`
* `External_AutoStop_TimeAggregationOperator`
* `External_AutoStop_TimeWindow`
* `External_AutoStop_Frequency`
* `External_AutoStop_Severity`

Můžete povolit a cílit na akci proti předplatnéa skupiny prostředků nebo cílit na konkrétní seznam virtuálních aplikací.

Když spustíte **AutoStop_CreateAlert_Parent** runbook, ověří, že cílové předplatné, skupiny prostředků a virtuální chody existují. Pokud virtuální společnosti existují, runbook pak volá **AutoStop_CreateAlert_Child** runbook pro každý ověřený virtuální účet nadřazeným runbookem. Tato podřízená runbook provádí následující:

* Vytvoří pravidlo upozornění metriky pro každý ověřený virtuální účet.

* Aktivuje **AutoStop_VM_Child** runbook pro konkrétní virtuální počítače, pokud procesor klesne pod nakonfigurovanou prahovou hodnotu pro zadaný časový interval. Tento runbook se pak pokusí zastavit virtuální ho.

### <a name="to-target-the-auto-stop-action-against-all-vms-in-a-subscription"></a>Cílení akce automatického zastavení proti všem virtuálním virtuálním mům v předplatném

1. Ujistěte `External_Stop_ResourceGroupNames` se, že proměnná je prázdná nebo nastavena na * (zástupný znak).

2. [Volitelný krok] Pokud chcete vyloučit některé virtuální počítače z automatického vypnutí, můžete do `External_ExcludeVMNames` proměnné přidat seznam názvů virtuálních počítačů oddělených čárkami.

3. Povolte `Schedule_AutoStop_CreateAlert_Parent` spuštění plánu a vytvořte požadovaná pravidla upozornění na metriku stop virtuálního počítače pro všechny virtuální počítače ve vašem předplatném. Spuštění tohoto typu plánu umožňuje vytvořit nová pravidla upozornění metriky jako nové virtuální chody jsou přidány do předplatného.

### <a name="to-target-the-auto-stop-action-against-all-vms-in-a-resource-group-or-multiple-resource-groups"></a>Cílení akce automatického zastavení proti všem virtuálním počítačům ve skupině prostředků nebo více skupinprostředků

1. Přidejte do `External_Stop_ResourceGroupNames` proměnné seznam názvů skupin prostředků oddělených čárkami.

2. Pokud chcete vyloučit některé virtuální počítače z automatického vypnutí, můžete do `External_ExcludeVMNames` proměnné přidat seznam názvů virtuálních počítačů oddělených čárkami.

3. Povolte **spuštění Schedule_AutoStop_CreateAlert_Parent** plánu a vytvořte požadovaná pravidla upozornění na metriku stop virtuálního počítače pro všechny virtuální počítače ve skupinách prostředků. Spuštění této operace podle plánu umožňuje vytvořit nová pravidla upozornění metriky jako nové virtuální chody jsou přidány do skupiny prostředků.

### <a name="to-target-the-autostop-action-to-a-list-of-vms"></a>Cílení akce autostop na seznam virtuálních ms

1. Vytvořte nový [plán](shared-resources/schedules.md#creating-a-schedule) a propojte ho se **AutoStop_CreateAlert_Parent** runbook, přidání mj. `VMList`

2. Volitelně pokud chcete vyloučit některé virtuální počítače z automatického vypnutí, můžete přidat seznam názvů virtuálních `External_ExcludeVMNames` počítačů oddělených čárkami do proměnné.

## <a name="configure-email-notifications"></a>Konfigurace e-mailových oznámení

Chcete-li změnit e-mailová oznámení po nasazení řešení, upravte skupinu akcí vytvořenou během nasazení.  

> [!NOTE]
> Předplatná v Azure Government Cloud nepodporují e-mailové funkce tohoto řešení.

1. Na webu Azure Portal přejděte na **Monitor**a potom **na skupiny akcí**. Vyberte skupinu akcí nazvanou **StartStop_VM_Notication**.

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

* Každý z nadřazených [runbooků](automation-solution-vm-management.md#runbooks) řešení má `VMList` parametr. Můžete předat seznam názvů virtuálních počítačů oddělených čárkami tomuto parametru při plánování příslušnénadřazené runbook pro vaši situaci a tyto virtuální počítače budou zahrnuty při spuštění řešení.

* Chcete-li vybrat více `External_Start_ResourceGroupNames` virtuálních počítačů, nastavte a `External_Stop_ResourceGroupNames` s názvy skupin prostředků, které obsahují virtuální chod, které chcete spustit nebo zastavit. Můžete také nastavit proměnné na hodnotu `*` mít řešení spustit proti všechny skupiny prostředků v předplatném.

### <a name="exclude-a-vm"></a>Vyloučení virtuálního počítače

Chcete-li vyloučit virtuální ho virtuálního zařízení `External_ExcludeVMNames` z řešení, můžete jej přidat do proměnné. Tato proměnná je seznam specifických virtuálních zařízení oddělený čárkami, který má být vyloučen z řešení Start/Stop. Tento seznam je omezen na 140 virtuálních stránek. Pokud přidáte více než 140 virtuálních stránek do tohoto seznamu odděleného čárkami, virtuální chody, které jsou nastaveny na vyloučení, mohou být neúmyslně spuštěny nebo zastaveny.

## <a name="modify-the-startup-and-shutdown-schedules"></a>Úprava plánů spuštění a vypnutí

Správa plánů spuštění a vypnutí v tomto řešení se řídí stejnými kroky, jako je popsáno v [plánování sady Runbook v Azure Automation](automation-schedules.md). Musí existovat samostatný plán pro spuštění a zastavení virtuálních můe.

Konfigurace řešení pouze zastavit virtuální chod v určitém čase je podporována. V tomto scénáři stačí vytvořit plán **Stop** a žádný odpovídající **plán zahájení.** Budete muset:

1. Ujistěte se, že jste přidali skupiny prostředků `External_Stop_ResourceGroupNames` pro virtuální chod vypnout v proměnné.

2. Vytvořte si vlastní plán pro čas, který chcete vypnout virtuální počítače.

3. Přejděte do **ScheduledStartStop_Parent** runbooku a klepněte na **tlačítko Naplánovat**. To umožňuje vybrat plán, který jste vytvořili v předchozím kroku.

4. Vyberte **Parametry a spusťte nastavení** a nastavte pole **AKCE** na **Hodnotu Zastavit**.

5. Výběrem **OK** uložte změny.

## <a name="next-steps"></a>Další kroky

* Informace o tom, jak řešit potíže s virtuálními jevy Start/Stop mimo pracovní dobu, najdete [v tématu Poradce při potížích s virtuálními jevy start/stop](troubleshoot/start-stop-vm.md).

* [Zkontrolujte](automation-solution-vm-management-logs.md) záznamy automatizace zapsané do protokolů monitorování Azure a ukázkové vyhledávací dotazy protokolu a analyzujte stav úloh runbooku automatizace z virtuálních počítačů Start/Stop.
