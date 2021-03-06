---
title: Konfigurace Azure Automation Start/Stop VMs during off-hours
description: Tento článek obsahuje informace o tom, jak nakonfigurovat funkci Start/Stop VMs during off-hours pro podporu různých případů nebo scénářů použití.
services: automation
ms.subservice: process-automation
ms.date: 03/12/2021
ms.topic: conceptual
ms.openlocfilehash: ef8b4ddcbf09fa3ec0e3b07ae797693aed095c88
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/08/2021
ms.locfileid: "107104962"
---
# <a name="configure-startstop-vms-during-off-hours"></a>Konfigurace Start/Stop VMs during off-hours

Tento článek popisuje, jak nakonfigurovat funkci [Start/Stop VMS during off-hours](automation-solution-vm-management.md) pro podporu popsaných scénářů. Můžete se také dozvědět, jak:

* [Konfigurace e-mailových oznámení](#configure-email-notifications)
* [Přidání virtuálního počítače](#add-a-vm)
* [Vyloučení virtuálního počítače](#exclude-a-vm)
* [Změna plánů spouštění a vypínání](#modify-the-startup-and-shutdown-schedules)

## <a name="scenario-1-startstop-vms-on-a-schedule"></a><a name="schedule"></a>Scénář 1: spuštění/zastavení virtuálních počítačů podle plánu

Tento scénář je výchozí konfigurací při prvním nasazení Start/Stop VMs during off-hours. Můžete třeba nakonfigurovat funkci tak, aby zastavila všechny virtuální počítače v rámci předplatného, když ponecháte práci na večeru, a po návratu do kanceláře ji začít používat na ráno. Když nakonfigurujete plány **naplánované – StartVM** a **Schedule-StopVM** během nasazení, začnou a zastavují cílové virtuální počítače. 

Konfigurace funkce pouze k zastavení virtuálních počítačů je podporována. Informace o tom, jak nakonfigurovat vlastní plán, najdete v tématu [Změna plánů spouštění a vypínání](#modify-the-startup-and-shutdown-schedules) .

> [!NOTE]
> Časové pásmo, které funkce používá, je vaše aktuální časové pásmo při konfiguraci parametru časový plán. Azure Automation ho ale v Azure Automation ukládá ve formátu UTC. Nemusíte provádět žádné převody časových pásem, protože to je zpracováno během nasazování počítače.

Pro řízení virtuálních počítačů, které jsou v oboru, nakonfigurujte proměnné: `External_Start_ResourceGroupNames` , `External_Stop_ResourceGroupNames` a `External_ExcludeVMNames` .

Můžete povolit buď cílení akce na předplatné a skupinu prostředků, nebo cílení na konkrétní seznam virtuálních počítačů, ale ne obojí.

### <a name="target-the-start-and-stop-actions-against-a-subscription-and-resource-group"></a>Cílem akcí spuštění a zastavení u předplatného a skupiny prostředků

1. Nakonfigurujte `External_Stop_ResourceGroupNames` proměnné a `External_ExcludeVMNames` pro určení cílových virtuálních počítačů.

1. Povolte a aktualizujte **naplánované –** plány plánování StartVM a **StopVM** .

1. Spusťte sadu **ScheduledStartStop_Parent** Runbook s parametrem **Akce** nastaveným na **Start** a polem parametr **WHATIF** nastavenou na hodnotu true pro náhled změn.

### <a name="target-the-start-and-stop-action-by-vm-list"></a>Zacílit akci spustit a zastavit podle seznamu VM

1. Spusťte sadu **ScheduledStartStop_Parent** Runbook s **akcí** nastavenou na **Start**.

1. Do pole parametr **VMList** přidejte čárkami oddělený seznam virtuálních počítačů (bez mezer). Seznam příkladů je `vm1,vm2,vm3` .

1. Chcete-li zobrazit náhled změn, nastavte pole parametr **WHATIF** na hodnotu true.

1. Nakonfigurujte `External_ExcludeVMNames` proměnnou pomocí čárkami odděleného seznamu virtuálních počítačů (VM1, VM2, VM3) bez mezer mezi hodnotami oddělenými čárkou.

1. Tento scénář nedodržuje `External_Start_ResourceGroupNames` proměnné a `External_Stop_ResourceGroupnames` . V tomto scénáři potřebujete vytvořit vlastní plán automatizace. Podrobnosti najdete v tématu [Naplánování runbooku v Azure Automation](shared-resources/schedules.md).

    > [!NOTE]
    > Hodnota pro **cílové názvy zdrojových zdrojů** je uložena jako hodnoty pro `External_Start_ResourceGroupNames` a `External_Stop_ResourceGroupNames` . Pro další členitost můžete každou z těchto proměnných upravit a cílit na jiné skupiny prostředků. Pro akci spustit použijte `External_Start_ResourceGroupNames` a použijte `External_Stop_ResourceGroupNames` pro akci zastavení. Virtuální počítače se automaticky přidají do plánů zahájení a zastavení.

## <a name="scenario-2-startstop-vms-in-sequence-by-using-tags"></a><a name="tags"></a>Scénář 2: spuštění/zastavení virtuálních počítačů v sekvenci pomocí značek

V prostředí, které obsahuje dvě nebo více součástí na více virtuálních počítačích, které podporují distribuovanou úlohu, je důležité zajistit, aby se v uvedeném pořadí podporovalo pořadí, ve kterém jsou komponenty spouštěny a zastaveny. 

### <a name="target-the-start-and-stop-actions-against-a-subscription-and-resource-group"></a>Cílem akcí spuštění a zastavení u předplatného a skupiny prostředků

1. Přidejte `sequencestart` a `sequencestop` značku s kladnými celočíselnými hodnotami do virtuálních počítačů, které jsou zaměřeny na `External_Start_ResourceGroupNames` a `External_Stop_ResourceGroupNames` proměnné. Akce spuštění a zastavení se provádí ve vzestupném pořadí. Informace o tom, jak označit virtuální počítač, najdete v tématu [označení virtuálního počítače s Windows v Azure](../virtual-machines/tag-portal.md) a [označení virtuálního počítače se systémem Linux v Azure](../virtual-machines/tag-cli.md).

1. Upravte plány **Sequenced-StartVM** a **Sequenced-StopVM** na datum a čas, který splňuje vaše požadavky, a povolte plán.

1. Pokud chcete zobrazit náhled změn, spusťte sadu **SequencedStartStop_Parent** Runbook s **akcí** nastavenou na **Start** a **WHATIF** nastavte na true.

1. Zobrazte náhled akce a proveďte potřebné změny před implementací na produkčních virtuálních počítačích. Až budete připraveni, ručně spusťte sadu Runbook s parametrem nastaveným na **hodnotu false**, nebo nechte rutiny Automation **Sequenced-StartVM** a **Sequenced-StopVM** spouštět automaticky podle předepsaného plánu.

### <a name="target-the-start-and-stop-actions-by-vm-list"></a>Cílení na spouštění a zastavování akcí podle seznamu VM

1. Přidejte `sequencestart` a `sequencestop` značku s kladnými celočíselnými hodnotami do virtuálních počítačů, které chcete přidat do `VMList` parametru.

1. Spusťte sadu **SequencedStartStop_Parent** Runbook s **akcí** nastavenou na **Start**.

1. Do pole parametr **VMList** přidejte čárkami oddělený seznam virtuálních počítačů (bez mezer). Seznam příkladů je `vm1,vm2,vm3` .

1. Nastavením **WHATIF** na hodnotu true zobrazíte náhled změn. 

1. Nakonfigurujte `External_ExcludeVMNames` proměnnou se seznamem virtuálních počítačů oddělených čárkami, bez mezer mezi hodnotami oddělenými čárkami.

1. Tento scénář nedodržuje `External_Start_ResourceGroupNames` proměnné a `External_Stop_ResourceGroupnames` . V tomto scénáři potřebujete vytvořit vlastní plán automatizace. Podrobnosti najdete v tématu [Naplánování runbooku v Azure Automation](shared-resources/schedules.md).

1. Zobrazte náhled akce a proveďte potřebné změny před implementací na produkčních virtuálních počítačích. Až budete připraveni, spusťte ručně **monitorování-and-Diagnostics/monitoring-Action-groupsrunbook** s parametrem nastaveným na **hodnotu false (NEPRAVDA**). Další možností je nechat plány Automation **Sequenced-StartVM** a **Sequenced-StopVM** spustit automaticky podle předepsaného plánu.

## <a name="scenario-3-start-or-stop-automatically-based-on-cpu-utilization"></a><a name="cpuutil"></a>Scénář 3: spuštění nebo zastavení automaticky na základě využití procesoru

Start/Stop VMs during off-hours může pomáhat spravovat náklady na spouštění Azure Resource Manager a klasických virtuálních počítačů ve vašem předplatném vyhodnocením počítačů, které se nepoužívají během období mimo špičku, například po hodinách, a automaticky je vypíná, pokud je využití procesoru menší než zadané procento.

Ve výchozím nastavení je tato funkce předem nakonfigurovaná k vyhodnocení procentuální metriky procesoru, aby se zobrazilo, jestli je průměrné využití 5 procent nebo míň. Tento scénář je řízen následujícími proměnnými a může být upraven, pokud výchozí hodnoty nesplňují vaše požadavky:

* `External_AutoStop_MetricName`
* `External_AutoStop_Threshold`
* `External_AutoStop_TimeAggregationOperator`
* `External_AutoStop_TimeWindow`
* `External_AutoStop_Frequency`
* `External_AutoStop_Severity`

U předplatného a skupiny prostředků můžete akci povolit a zaměřit na konkrétní seznam virtuálních počítačů.

Když spustíte **AutoStop_CreateAlert_Parent** Runbook, ověří, že existuje cílové předplatné, skupiny prostředků a virtuální počítače. Pokud virtuální počítače existují, sada Runbook zavolá sadu Runbook **AutoStop_CreateAlert_Child** pro každý virtuální počítač ověřený sadou Runbook. Tato podřízená sada Runbook:

* Vytvoří pravidlo upozornění metriky pro každý ověřený virtuální počítač.
* Aktivuje **AutoStop_VM_Child** sadu Runbook pro konkrétní virtuální počítač, pokud procesor klesne pod nastavenou prahovou hodnotu pro zadaný časový interval. 
* Pokusí se virtuální počítač zastavit.

### <a name="target-the-autostop-action-against-all-vms-in-a-subscription"></a>Cíl akce autostop u všech virtuálních počítačů v rámci předplatného

1. Ujistěte se, že `External_Stop_ResourceGroupNames` je proměnná prázdná, nebo ji nastavte na * (zástupný znak).

1. Volitelné Pokud chcete vyloučit některé virtuální počítače z akce automatického zastavení, můžete do proměnné přidat čárkami oddělený seznam názvů virtuálních počítačů `External_ExcludeVMNames` .

1. Povolte spuštění plánu **Schedule_AutoStop_CreateAlert_Parent** , abyste vytvořili vyžadovaná pravidla upozornění na metriky pro všechny virtuální počítače ve vašem předplatném. Spuštění tohoto typu plánu vám umožní vytvořit nová pravidla upozornění metriky, protože do předplatného jsou přidané nové virtuální počítače.

### <a name="target-the-autostop-action-against-all-vms-in-a-resource-group-or-multiple-resource-groups"></a>Cílit na akci autostop u všech virtuálních počítačů ve skupině prostředků nebo ve více skupinách prostředků

1. Přidejte do proměnné seznam názvů skupin prostředků oddělených čárkami `External_Stop_ResourceGroupNames` .

1. Pokud chcete některé z virtuálních počítačů vyloučit z automatického zastavení, můžete do proměnné přidat čárkami oddělený seznam názvů virtuálních počítačů `External_ExcludeVMNames` .

1. Povolte spuštění plánu **Schedule_AutoStop_CreateAlert_Parent** , abyste vytvořili vyžadovaná pravidla upozornění na metriky pro všechny virtuální počítače ve skupinách prostředků. Spuštění této operace podle plánu vám umožní vytvořit nová pravidla upozornění metriky, protože nové virtuální počítače se přidají do skupin prostředků.

### <a name="target-the-autostop-action-to-a-list-of-vms"></a>Cílení akce autostop na seznam virtuálních počítačů

1. Vytvořte nový [plán](shared-resources/schedules.md#create-a-schedule) a propojte ho s **AutoStop_CreateAlert_Parent** sadou Runbook a přidejte do parametru seznam názvů virtuálních počítačů oddělených čárkami `VMList` .

1. Případně, pokud chcete vyloučit některé virtuální počítače z akce automatického zastavení, můžete do proměnné přidat čárkami oddělený seznam názvů virtuálních počítačů (bez mezer) `External_ExcludeVMNames` .

## <a name="configure-email-notifications"></a>Konfigurace e-mailových oznámení

Chcete-li změnit e-mailová oznámení po nasazení Start/Stop VMs during off-hours, můžete upravit skupinu akcí vytvořenou během nasazování.  

> [!NOTE]
> Předplatná v cloudu Azure Government nepodporují funkce e-mailu této funkce.

1. V Azure Portal klikněte na **výstrahy** v části **monitorování** a pak na **Spravovat akce**. Na stránce **Spravovat akce** se ujistěte, že jste na kartě **skupiny akcí** . Vyberte skupinu akcí s názvem **StartStop_VM_Notification**.

    :::image type="content" source="media/automation-solution-vm-management/azure-monitor-sm.png" alt-text="Snímek obrazovky se stránkou monitor – skupiny akcí" lightbox="media/automation-solution-vm-management/azure-monitor-lg.png":::

1. Na stránce **StartStop_VM_Notification** se oddíl **základy** vyplní za vás a nebude možné ho upravit, s výjimkou pole **Zobrazovaný název** . Upravte název nebo přijměte navrhovaný název. V části **oznámení** klikněte na ikonu tužky a upravte podrobnosti akce. Tím se otevře podokno **zpráva e-mailu/zprávy SMS/oznámení/hlas** . Aktualizujte e-mailovou adresu a kliknutím na **OK** uložte změny.

    :::image type="content" source="media/automation-solution-vm-management/change-email.png" alt-text="Snímek obrazovky e-mailu/zprávy SMS/stránky pro vložení/hlas ukazující ukázkovou e-mailovou adresu.":::

    Do skupiny akcí můžete přidat další akce. Další informace o skupinách akcí najdete v tématu [skupiny akcí](../azure-monitor/platform/action-groups.md) .

Následuje příklad e-mailu, který se pošle, když funkce vypne virtuální počítače.

:::image type="content" source="media/automation-solution-vm-management/email.png" alt-text="Snímek obrazovky s ukázkovým e-mailem odeslaným, když funkce vypne virtuální počítače" lightbox="media/automation-solution-vm-management/email.png":::

## <a name="add-or-exclude-vms"></a><a name="add-exclude-vms"></a>Přidat nebo vyloučit virtuální počítače

Tato funkce umožňuje přidat virtuální počítače, které mají být cílené nebo vyloučené. 

### <a name="add-a-vm"></a>Přidání virtuálního počítače

Existují dva způsoby, jak zajistit, aby byl virtuální počítač zahrnutý při spuštění funkce:

* Každá z nadřazených [runbooků](automation-solution-vm-management.md#runbooks) funkce má `VMList` parametr. K tomuto parametru můžete předat seznam názvů virtuálních počítačů oddělených čárkami (bez mezer) při plánování příslušné nadřazené sady Runbook pro vaši situaci. Tyto virtuální počítače budou zahrnuty při spuštění funkce.

* Pokud chcete vybrat víc virtuálních počítačů, nastavte `External_Start_ResourceGroupNames` a `External_Stop_ResourceGroupNames` s názvy skupin prostředků, které obsahují virtuální počítače, které chcete spustit nebo zastavit. Můžete také nastavit proměnné na hodnotu, `*` aby se funkce spouštěla pro všechny skupiny prostředků v rámci předplatného.

### <a name="exclude-a-vm"></a>Vyloučení virtuálního počítače

Pokud chcete vyloučit virtuální počítač z virtuálních počítačů Stop/Start v době mimo špičku, můžete do proměnné přidat její název `External_ExcludeVMNames` . Tato proměnná je čárkami oddělený seznam specifických virtuálních počítačů (bez mezer), které se mají vyloučit z funkce. Tento seznam je omezený na 140 virtuálních počítačů. Pokud do tohoto seznamu přidáte více než 140 virtuálních počítačů, virtuální počítače, které mají být vyloučeny, mohou být neúmyslně spuštěny nebo zastaveny.

## <a name="modify-the-startup-and-shutdown-schedules"></a>Změna plánů spouštění a vypínání

Správa plánů spouštění a vypínání v této funkci se řídí stejnými kroky, jak je uvedeno v části [Naplánování runbooku v Azure Automation](shared-resources/schedules.md). Ke spuštění a zastavení virtuálních počítačů se vyžadují samostatné plány.

Konfigurace funkce tak, aby se v určitou dobu zastavily jenom virtuální počítače, se podporují. V tomto scénáři stačí vytvořit plán zastavení a žádný odpovídající plán zahájení. 

1. Ujistěte se, že jste přidali skupiny prostředků pro virtuální počítače, které se mají vypnout v `External_Stop_ResourceGroupNames` proměnné.

1. Vytvořte si vlastní plán pro čas, kdy chcete virtuální počítače vypnout.

1. Přejděte do sady Runbook **ScheduledStartStop_Parent** a klikněte na **plán**. To vám umožní vybrat plán, který jste vytvořili v předchozím kroku.

1. Vyberte **parametry a nastavení spuštění** a nastavte pole **Akce** na **zastavit**.

1. Výběrem **OK** uložte změny.

## <a name="next-steps"></a>Další kroky

* Chcete-li monitorovat funkci během operace, přečtěte si téma [protokoly dotazů z Start/Stop VMS during off-hours](automation-solution-vm-management-logs.md).
* Pokud chcete řešit problémy při správě virtuálních počítačů, přečtěte si téma [řešení potíží s Start/Stop VMS during off-hours](troubleshoot/start-stop-vm.md).
