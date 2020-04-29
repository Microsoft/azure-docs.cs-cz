---
title: Azure Automation nakonfigurovat spouštění a zastavování virtuálních počítačů během nepracovních řešení
description: Tento článek popisuje, jak nakonfigurovat řešení Start/Stop VMs during off-hours pro podporu různých případů nebo scénářů použití.
services: automation
ms.subservice: process-automation
ms.date: 04/01/2020
ms.topic: conceptual
ms.openlocfilehash: 9842a736cf922e0490f2b0c8acb1d2e5833f3d6c
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "81604763"
---
# <a name="how-to-configure-startstop-vms-during-off-hours-solution"></a>Jak nakonfigurovat spouštění a zastavování virtuálních počítačů v době mimo špičku

Pomocí řešení pro **spouštění a zastavování virtuálních počítačů v době mimo špičku** můžete:

- [Naplánujte spouštění a zastavování virtuálních počítačů](#schedule).
- Naplánování spouštění a zastavování virtuálních počítačů ve vzestupném pořadí [pomocí značek Azure](#tags) (není podporováno pro klasické virtuální počítače).
- Automaticky zastavovat virtuální počítače na základě [nízkého využití procesoru](#cpuutil).

Tento článek popisuje, jak úspěšně nakonfigurovat řešení pro podporu těchto scénářů. Můžete se také dozvědět, jak provádět další společná nastavení konfigurace pro řešení, jako například:

* [Konfigurace e-mailových oznámení](#configure-email-notifications)

* [Přidání virtuálního počítače](#add-a-vm)

* [Vyloučení virtuálního počítače](#exclude-a-vm)

* [Změna plánů spouštění a vypínání](#modify-the-startup-and-shutdown-schedules)

## <a name="scenario-1-startstop-vms-on-a-schedule"></a><a name="schedule"></a>Scénář 1: spuštění/zastavení virtuálních počítačů podle plánu

Tento scénář je výchozí konfigurací při prvním nasazení řešení. Můžete ji například nakonfigurovat tak, aby zastavila všechny virtuální počítače v rámci předplatného, když ponecháte práci v večeru, a po návratu do kanceláře ji začít používat v ráno. Když nakonfigurujete plány **naplánované – StartVM** a **Schedule-StopVM** během nasazení, začnou a zastavují cílové virtuální počítače. Konfigurace tohoto řešení pro jenom zastavení virtuálních počítačů je podporovaná. informace o tom, jak nakonfigurovat vlastní plán, najdete v tématu [Změna plánů spouštění a vypínání](#modify-the-startup-and-shutdown-schedules) .

> [!NOTE]
> Časové pásmo je vaším aktuálním časovým pásmem, když konfigurujete parametr časového plánu. Je ale uložený ve formátu UTC v Azure Automation. Nemusíte provádět žádné převody časových pásem, protože to je zpracováváno během nasazení.

Konfigurací následujících proměnných můžete řídit, které virtuální počítače jsou v oboru: **External_Start_ResourceGroupNames**, **External_Stop_ResourceGroupNames**a **External_ExcludeVMNames**.

Můžete povolit buď cílení akce na předplatné a skupinu prostředků, nebo cílení na konkrétní seznam virtuálních počítačů, ale ne obojí.

### <a name="target-the-start-and-stop-actions-against-a-subscription-and-resource-group"></a>Cílem akcí spuštění a zastavení u předplatného a skupiny prostředků

1. Nakonfigurujte proměnné `External_Stop_ResourceGroupNames` a `External_ExcludeVMNames` pro určení cílových virtuálních počítačů.

2. Povolte a aktualizujte **naplánované –** plány plánování StartVM a **StopVM** .

3. Spusťte sadu **ScheduledStartStop_Parent** Runbook s parametrem **Akce** nastaveným na **Start** a polem parametr **WHATIF** nastavenou na hodnotu true pro náhled změn.

### <a name="target-the-start-and-stop-action-by-vm-list"></a>Zacílit akci spustit a zastavit podle seznamu VM

1. Spusťte sadu **ScheduledStartStop_Parent** Runbook s **akcí** nastavenou na **Start**, do pole parametr **VMList** přidejte čárkami oddělený seznam virtuálních počítačů a potom nastavte pole parametr **WHATIF** na hodnotu true (pravda). Zobrazte náhled změn.

2. Nakonfigurujte `External_ExcludeVMNames` proměnnou pomocí čárkami odděleného seznamu virtuálních počítačů (VM1, VM2, VM3).

3. Tento scénář nedodržuje proměnné `External_Start_ResourceGroupNames` a `External_Stop_ResourceGroupnames` . V tomto scénáři potřebujete vytvořit vlastní plán automatizace. Podrobnosti najdete v tématu [plánování Runbooku v Azure Automation](../automation/automation-schedules.md).

    > [!NOTE]
    > Hodnota pro **název cílové zdrojové** služby je uložená jako hodnota pro i `External_Start_ResourceGroupNames` `External_Stop_ResourceGroupNames`. Pro další členitost můžete každou z těchto proměnných upravit a cílit na jiné skupiny prostředků. Pro akci spustit použijte `External_Start_ResourceGroupNames`a použijte `External_Stop_ResourceGroupNames` pro akci zastavení. Virtuální počítače se automaticky přidají do plánů zahájení a zastavení.

## <a name="scenario-2-startstop-vms-in-sequence-by-using-tags"></a><a name="tags"></a>Scénář 2: spuštění/zastavení virtuálních počítačů v sekvenci pomocí značek

V prostředí, které obsahuje dvě nebo více součástí na více virtuálních počítačích, které podporují distribuovanou úlohu, je důležité zajistit, aby se v uvedeném pořadí podporovalo pořadí, ve kterém jsou komponenty spouštěny a zastaveny. Tento scénář můžete dosáhnout provedením následujících kroků:

### <a name="target-the-start-and-stop-actions-against-a-subscription-and-resource-group"></a>Cílem akcí spuštění a zastavení u předplatného a skupiny prostředků

1. Přidejte `sequencestart` a `sequencestop` značku s kladnou celočíselnou hodnotou do virtuálních počítačů, které jsou zaměřeny na `External_Start_ResourceGroupNames` a `External_Stop_ResourceGroupNames` proměnné. Akce spuštění a zastavení se provádí ve vzestupném pořadí. Informace o tom, jak označit virtuální počítač, najdete v tématu [označení virtuálního počítače s Windows v Azure](../virtual-machines/windows/tag.md) a [označení virtuálního počítače se systémem Linux v Azure](../virtual-machines/linux/tag.md).

2. Upravte plány **Sequenced-StartVM** a **Sequenced-StopVM** na datum a čas, který splňuje vaše požadavky, a povolte plán.

3. Pokud chcete zobrazit náhled změn, spusťte sadu **SequencedStartStop_Parent** Runbook s **akcí** nastavenou na **Start** a **WHATIF** nastavte na true.

4. Zobrazte náhled akce a proveďte potřebné změny před implementací na produkčních virtuálních počítačích. Až budete připraveni, ručně spusťte sadu Runbook s parametrem nastaveným na **hodnotu false**nebo nechte plán `Sequenced-StartVM` Automation `Sequenced-StopVM` a spusťte automaticky podle předepsaného plánu.

### <a name="target-the-start-and-stop-action-by-vm-list"></a>Zacílit akci spustit a zastavit podle seznamu VM

1. Přidejte k `sequencestart` virtuálním `sequencestop` počítačům, které chcete přidat do `VMList` parametru, značku a pomocí kladné celočíselné hodnoty.

2. Spusťte sadu **SequencedStartStop_Parent** Runbook s **akcí** nastavenou na **Start**, do pole parametr **VMList** přidejte čárkami oddělený seznam virtuálních počítačů a pak nastavte **WHATIF** na hodnotu true. Zobrazte náhled změn.

3. Nakonfigurujte `External_ExcludeVMNames` proměnnou pomocí čárkami odděleného seznamu virtuálních počítačů (VM1, VM2, VM3).

4. Tento scénář nedodržuje proměnné `External_Start_ResourceGroupNames` a `External_Stop_ResourceGroupnames` . V tomto scénáři potřebujete vytvořit vlastní plán automatizace. Podrobnosti najdete v tématu [plánování Runbooku v Azure Automation](../automation/automation-schedules.md).

5. Zobrazte náhled akce a proveďte potřebné změny před implementací na produkčních virtuálních počítačích. Až budete připraveni, spusťte ručně **monitorování-and-Diagnostics/monitoring-Action-groupsrunbook** s parametrem nastaveným na **hodnotu false (NEPRAVDA**). Případně můžete plán `Sequenced-StartVM` automatizace `Sequenced-StopVM` spustit automaticky podle předepsaného plánu.

## <a name="scenario-3-startstop-automatically-based-on-cpu-utilization"></a><a name="cpuutil"></a>Scénář 3: spuštění/zastavení automaticky na základě využití procesoru

Toto řešení může přispět ke správě nákladů na provoz Azure Resource Manager a klasických virtuálních počítačů ve vašem předplatném Pomocí vyhodnocení virtuálních počítačů, které se nepoužívají během období mimo špičku, například po hodinách, a automaticky je vypíná, pokud je využití procesoru menší než zadané procento.

Ve výchozím nastavení je řešení předem nakonfigurované k vyhodnocení procentuální metriky procesoru, aby bylo možné zjistit, zda je průměrné využití 5 procent nebo méně. Tento scénář je řízen následujícími proměnnými a může být upraven, pokud výchozí hodnoty nesplňují vaše požadavky:

* `External_AutoStop_MetricName`
* `External_AutoStop_Threshold`
* `External_AutoStop_TimeAggregationOperator`
* `External_AutoStop_TimeWindow`
* `External_AutoStop_Frequency`
* `External_AutoStop_Severity`

U předplatného a skupiny prostředků můžete akci povolit a zaměřit na konkrétní seznam virtuálních počítačů.

Když spustíte **AutoStop_CreateAlert_Parent** Runbook, ověří, jestli existuje cílové předplatné, skupiny prostředků a virtuální počítače. Pokud virtuální počítače existují, sada Runbook potom zavolá sadu Runbook **AutoStop_CreateAlert_Child** pro každý ověřený virtuální počítač nadřazeným Runbookem. Tato podřízená sada Runbook provede následující akce:

* Vytvoří pravidlo upozornění metriky pro každý ověřený virtuální počítač.

* Aktivuje **AutoStop_VM_Child** sadu Runbook pro konkrétní virtuální počítač, pokud procesor klesne pod nastavenou prahovou hodnotu pro zadaný časový interval. Tato sada Runbook se pak pokusí zastavit virtuální počítač.

### <a name="to-target-the-auto-stop-action-against-all-vms-in-a-subscription"></a>Zaměření akce automatického zastavení na všechny virtuální počítače v rámci předplatného

1. Ujistěte se, `External_Stop_ResourceGroupNames` že je proměnná prázdná, nebo ji nastavte na * (zástupný znak).

2. [Volitelný krok] Pokud chcete vyloučit některé virtuální počítače z automatického vypnutí, můžete do `External_ExcludeVMNames` proměnné přidat čárkami oddělený seznam názvů virtuálních počítačů.

3. Povolte spuštění `Schedule_AutoStop_CreateAlert_Parent` plánu, aby se vytvořila pravidla upozornění metriky pro všechny virtuální počítače v rámci vašeho předplatného. Spuštění tohoto typu plánu vám umožní vytvořit nová pravidla upozornění metriky, protože do předplatného jsou přidané nové virtuální počítače.

### <a name="to-target-the-auto-stop-action-against-all-vms-in-a-resource-group-or-multiple-resource-groups"></a>Zaměření akce automatického zastavení na všechny virtuální počítače ve skupině prostředků nebo ve více skupinách prostředků

1. Přidejte do `External_Stop_ResourceGroupNames` proměnné seznam názvů skupin prostředků oddělených čárkami.

2. Pokud chcete některé z těchto virtuálních počítačů vyloučit z automatického vypnutí, můžete do `External_ExcludeVMNames` proměnné přidat čárkami oddělený seznam názvů virtuálních počítačů.

3. Povolte spuštění plánu **Schedule_AutoStop_CreateAlert_Parent** , abyste vytvořili vyžadovaná pravidla upozornění na metriky pro všechny virtuální počítače ve skupinách prostředků. Spuštění této operace podle plánu vám umožní vytvořit nová pravidla upozornění metriky, protože nové virtuální počítače se přidají do skupin prostředků.

### <a name="to-target-the-autostop-action-to-a-list-of-vms"></a>Zaměření na akci autostop na seznam virtuálních počítačů

1. Vytvořte nový [plán](shared-resources/schedules.md#creating-a-schedule) a propojte ho s **AutoStop_CreateAlert_Parent** sadou Runbook a přidejte do `VMList` parametru seznam názvů virtuálních počítačů oddělených čárkami.

2. Případně, pokud chcete některé virtuální počítače z automatického vypnutí vyloučit, můžete do `External_ExcludeVMNames` proměnné přidat čárkami oddělený seznam názvů virtuálních počítačů.

## <a name="configure-email-notifications"></a>Konfigurace e-mailových oznámení

Chcete-li změnit e-mailová oznámení po nasazení řešení, upravte skupinu akcí vytvořenou během nasazení.  

> [!NOTE]
> Předplatná v cloudu Azure Government nepodporují funkce e-mailu tohoto řešení.

1. V Azure Portal přejděte na **monitorování**a pak na **skupiny akcí**. Vyberte skupinu akcí s názvem **StartStop_VM_Notication**.

    ![Stránka řešení Update Management pro automatizaci](media/automation-solution-vm-management/azure-monitor.png)

2. Na stránce **StartStop_VM_Notification** klikněte na **Upravit podrobnosti** v části **Podrobnosti**. Tím se otevře stránka **e-mail/SMS/Push/Voice** . Aktualizujte e-mailovou adresu a kliknutím na **OK** uložte změny.

    ![Stránka řešení Update Management pro automatizaci](media/automation-solution-vm-management/change-email.png)

    Případně můžete do skupiny akcí přidat další akce. Další informace o skupinách akcí naleznete v tématu [skupiny akcí](../azure-monitor/platform/action-groups.md) .

Následuje příklad e-mailu, který se pošle, když řešení vypíná virtuální počítače.

![Stránka řešení Update Management pro automatizaci](media/automation-solution-vm-management/email.png)

## <a name="addexclude-vms"></a><a name="add-exclude-vms"></a>Přidat nebo vyloučit virtuální počítače

Řešení poskytuje možnost Přidat virtuální počítače, na které má řešení cílit, nebo konkrétně vyloučit počítače z řešení.

### <a name="add-a-vm"></a>Přidání virtuálního počítače

Existují dvě možnosti, pomocí kterých se můžete ujistit, že je virtuální počítač zahrnutý do řešení spustit/zastavit při spuštění.

* Každé nadřazené [Runbooky](automation-solution-vm-management.md#runbooks) řešení má `VMList` parametr. K tomuto parametru můžete předat čárkami oddělený seznam názvů virtuálních počítačů při plánování příslušné nadřazené sady Runbook pro vaši situaci a tyto virtuální počítače budou zahrnuty při spuštění řešení.

* Pokud chcete vybrat víc virtuálních počítačů `External_Start_ResourceGroupNames` , `External_Stop_ResourceGroupNames` nastavte a s názvy skupin prostředků, které obsahují virtuální počítače, které chcete spustit nebo zastavit. Můžete také nastavit proměnné na hodnotu, `*` aby bylo řešení spuštěno pro všechny skupiny prostředků v rámci předplatného.

### <a name="exclude-a-vm"></a>Vyloučení virtuálního počítače

Pokud chcete virtuální počítač z řešení vyloučit, můžete ho přidat do `External_ExcludeVMNames` proměnné. Tato proměnná je čárkami oddělený seznam konkrétních virtuálních počítačů, které se mají vyloučit z řešení spustit/zastavit. Tento seznam je omezený na 140 virtuálních počítačů. Pokud do tohoto seznamu odděleného čárkami přidáte více než 140 virtuálních počítačů, virtuální počítače, které mají být vyloučeny, mohou být neúmyslně spuštěny nebo zastaveny.

## <a name="modify-the-startup-and-shutdown-schedules"></a>Změna plánů spouštění a vypínání

Správa plánů spouštění a vypínání v tomto řešení se řídí stejnými kroky, jak je popsáno v části [plánování sady Runbook v Azure Automation](automation-schedules.md). Aby bylo možné začít a zastavovat virtuální počítače, musí být samostatný plán.

Konfigurace řešení pouze k zastavení virtuálních počítačů v určitou dobu je podporována. V tomto scénáři stačí vytvořit plán **zastavení** a žádný odpovídající plán **zahájení** . Budete muset:

1. Ujistěte se, že jste přidali skupiny prostředků pro virtuální počítače, které se mají `External_Stop_ResourceGroupNames` vypnout v proměnné.

2. Vytvořte si vlastní plán pro čas, kdy chcete virtuální počítače vypnout.

3. Přejděte do sady Runbook **ScheduledStartStop_Parent** a klikněte na **plán**. To vám umožní vybrat plán, který jste vytvořili v předchozím kroku.

4. Vyberte **parametry a nastavení spuštění** a nastavte pole **Akce** na **zastavit**.

5. Výběrem **OK** uložte změny.

## <a name="next-steps"></a>Další kroky

* Další informace o řešení potíží s Start/Stop VMs during off-hours najdete v tématu [řešení potíží s spuštěním nebo zastavením virtuálních počítačů](troubleshoot/start-stop-vm.md).

* [Zkontrolujte](automation-solution-vm-management-logs.md) záznamy automatizace napsané do protokolů Azure monitor a příklady dotazů na hledání v protokolu, které analyzují stav úloh služby Automation Runbook z možností spustit nebo zastavit virtuální počítače.
