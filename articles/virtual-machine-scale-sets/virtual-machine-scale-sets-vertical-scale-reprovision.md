---
title: Vertikálně škálované služby Virtual Machine Scale Sets v Azure
description: Jak vertikálně škálovat virtuální počítač v reakci na monitorování výstrah pomocí Azure Automation
author: ju-shim
ms.author: jushiman
ms.topic: how-to
ms.service: virtual-machine-scale-sets
ms.subservice: autoscale
ms.date: 04/18/2019
ms.reviewer: avverma
ms.custom: avverma
ms.openlocfilehash: 37602f7b9a8669ce0e8db984f7f7617cffdd431c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "87029276"
---
# <a name="vertical-autoscale-with-virtual-machine-scale-sets"></a>Vertikální automatické škálování se sadami škálování virtuálních počítačů

Tento článek popisuje, jak vertikálně škálovat [Virtual Machine Scale Sets](https://azure.microsoft.com/services/virtual-machine-scale-sets/) Azure s přezřizováním nebo bez něj. 

Vertikální škálování, označované také jako *horizontální navýšení kapacity* *, znamená*zvýšení nebo snížení velikosti virtuálních počítačů v reakci na zatížení. Porovnejte toto chování s [horizontálním škálováním](virtual-machine-scale-sets-autoscale-overview.md), které se také označuje jako horizontální navýšení *kapacity* a *horizontální*navýšení kapacity, kde se počet virtuálních počítačů mění v závislosti na zatížení.

Opětovné zřízení znamená odebrání existujícího virtuálního počítače a jeho nahrazení novým. Když zvětšíte nebo zmenšíte velikost virtuálních počítačů ve službě Virtual Machine Scale set, v některých případech budete chtít změnit velikost stávajících virtuálních počítačů a zachovat data, zatímco v jiných případech potřebujete nasadit nové virtuální počítače nové velikosti. Tento dokument popisuje oba případy.

Vertikální škálování může být užitečné v těchto případech:

* Služba založená na virtuálních počítačích je využívána v provozu (například u víkendů). Snížení velikosti virtuálního počítače může snížit měsíční náklady.
* Zvýšení velikosti virtuálních počítačů, aby se vypořádat s větší poptávkou bez vytváření dalších virtuálních počítačů.

Můžete nastavit svislé škálování, které se aktivuje na základě výstrah založených na metrikách ze sady škálování virtuálních počítačů. Když je výstraha aktivována, vyvolá Webhook, který aktivuje sadu Runbook, která může škálovat nastavené nebo vypnuté škálování. Vertikální škálování se dá nakonfigurovat pomocí následujících kroků:

1. Vytvořte účet Azure Automation s funkcí Run-as.
2. Importujte Azure Automation sady Runbook škálované do svého předplatného pro virtuální počítače.
3. Přidejte Webhook do Runbooku.
4. Pomocí oznámení Webhooku přidejte do sady škálování virtuálního počítače upozornění.

> [!NOTE]
> Vzhledem k velikosti prvního virtuálního počítače se můžou velikosti, na které se dá škálovat, omezit kvůli dostupnosti dalších velikostí v clusteru, na kterém je nasazený aktuální virtuální počítač. V publikovaných runbookůch Automation používaných v tomto článku se postará o tento případ a jenom škálovat v rámci dvojice velikostí virtuálních počítačů. To znamená, že virtuální počítač s Standard_D1v2 nebude po navýšení do Standard_G5 škálované na Basic_A0. Také omezené velikosti virtuálních počítačů se škálují nahoru/dolů nejsou podporované. Můžete si vybrat, jestli chcete škálovat mezi následujícími páry velikostí:
> 
> | Velikosti virtuálních počítačů škálované členské párování | Člen |
> | --- | --- |
> | Basic_A0 |Basic_A4 |
> | Standard_A0 |Standard_A4 |
> | Standard_A5 |Standard_A7 |
> | Standard_A8 |Standard_A9 |
> | Standard_A10 |Standard_A11 |
> | Standard_A1_v2 |Standard_A8_v2 |
> | Standard_A2m_v2 |Standard_A8m_v2  |
> | Standard_B1s |Standard_B2s |
> | Standard_B1ms |Standard_B8ms |
> | Standard_D1 |Standard_D4 |
> | Standard_D11 |Standard_D14 |
> | Standard_DS1 |Standard_DS4 |
> | Standard_DS11 |Standard_DS14 |
> | Standard_D1_v2 |Standard_D5_v2 |
> | Standard_D11_v2 |Standard_D14_v2 |
> | Standard_DS1_v2 |Standard_DS5_v2 |
> | Standard_DS11_v2 |Standard_DS14_v2 |
> | Standard_D2_v3 |Standard_D64_v3 |
> | Standard_D2s_v3 |Standard_D64s_v3 |
> | Standard_DC2s |Standard_DC4s |
> | Standard_E2_v3 |Standard_E64_v3 |
> | Standard_E2s_v3 |Standard_E64s_v3 |
> | Standard_F1 |Standard_F16 |
> | Standard_F1s |Standard_F16s |
> | Standard_F2sv2 |Standard_F72sv2 |
> | Standard_G1 |Standard_G5 |
> | Standard_GS1 |Standard_GS5 |
> | Standard_H8 |Standard_H16 |
> | Standard_H8m |Standard_H16m |
> | Standard_L4s |Standard_L32s |
> | Standard_L8s_v2 |Standard_L80s_v2 |
> | Standard_M8ms  |Standard_M128ms |
> | Standard_M32ls  |Standard_M64ls |
> | Standard_M64s  |Standard_M128s |
> | Standard_M64  |Standard_M128 |
> | Standard_M64m  |Standard_M128m |
> | Standard_NC6 |Standard_NC24 |
> | Standard_NC6s_v2 |Standard_NC24s_v2 |
> | Standard_NC6s_v3 |Standard_NC24s_v3 |
> | Standard_ND6s |Standard_ND24s |
> | Standard_NV6 |Standard_NV24 |
> | Standard_NV6s_v2 |Standard_NV24s_v2 |
> | Standard_NV12s_v3 |Standard_NV48s_v3 |
> 

## <a name="create-an-azure-automation-account-with-run-as-capability"></a>Vytvoření účtu Azure Automation s možností spuštění jako
První věc, kterou potřebujete udělat, je vytvořit účet Azure Automation, který hostuje Runbooky používané pro škálování instancí sady škálování virtuálních počítačů. Nedávno [Azure Automation](https://azure.microsoft.com/services/automation/) zavedli funkci účet Spustit jako, která umožňuje nastavit instanční objekt pro automatické spouštění Runbooků jménem uživatele. Další informace naleznete v tématech:

* [Ověření runbooků pomocí účtu Spustit v Azure jako](../automation/manage-runas-account.md)

## <a name="import-azure-automation-vertical-scale-runbooks-into-your-subscription"></a>Import Azure Automationch sad Runbook se vertikálním škálováním do předplatného

Sady Runbook, které jsou potřeba ke vertikálnímu škálování vašich virtuálních počítačů, jsou už publikované v galerii sady Runbook Azure Automation. Pokud je chcete importovat do svého předplatného, postupujte podle kroků v tomto článku:

* [Galerie runbooků a modulů pro Azure Automation](../automation/automation-runbook-gallery.md)

V nabídce Runbooky vyberte možnost Procházet galerii:

![Runbooky k importu][runbooks]

Zobrazí se Runbooky, které je třeba importovat. Vyberte sadu Runbook na základě toho, zda chcete svislé škálování s nebo bez opětovného zřizování:

![Galerie runbooků][gallery]

## <a name="add-a-webhook-to-your-runbook"></a>Přidání Webhooku do Runbooku

Po importu runbooků přidejte Webhook do Runbooku, aby ho mohl aktivovat výstraha ze sady škálování virtuálního počítače. Podrobnosti o vytvoření Webhooku pro váš Runbook jsou popsány v tomto článku:

* [Azure Automation Webhooky](../automation/automation-webhooks.md)

> [!NOTE]
> Před zavřením dialogu Webhooku si nezapomeňte zkopírovat identifikátor URI Webhooku, protože tuto adresu budete potřebovat v další části.
> 
> 

## <a name="add-an-alert-to-your-virtual-machine-scale-set"></a>Přidání výstrahy do sady škálování virtuálních počítačů

Níže je skript PowerShellu, který ukazuje, jak přidat výstrahu do sady škálování virtuálních počítačů. V následujícím článku najdete název metriky, na které se má upozornění aktivovat: [Azure monitor automatické škálování běžných metrik](../azure-monitor/platform/autoscale-common-metrics.md).

```powershell
$actionEmail = New-AzAlertRuleEmail -CustomEmail user@contoso.com
$actionWebhook = New-AzAlertRuleWebhook -ServiceUri <uri-of-the-webhook>
$threshold = <value-of-the-threshold>
$rg = <resource-group-name>
$id = <resource-id-to-add-the-alert-to>
$location = <location-of-the-resource>
$alertName = <name-of-the-resource>
$metricName = <metric-to-fire-the-alert-on>
$timeWindow = <time-window-in-hh:mm:ss-format>
$condition = <condition-for-the-threshold> # Other valid values are LessThanOrEqual, GreaterThan, GreaterThanOrEqual
$description = <description-for-the-alert>

Add-AzMetricAlertRule  -Name  $alertName `
                            -Location  $location `
                            -ResourceGroup $rg `
                            -TargetResourceId $id `
                            -MetricName $metricName `
                            -Operator  $condition `
                            -Threshold $threshold `
                            -WindowSize  $timeWindow `
                            -TimeAggregationOperator Average `
                            -Actions $actionEmail, $actionWebhook `
                            -Description $description
```

> [!NOTE]
> Doporučuje se nakonfigurovat přiměřené časové období pro výstrahu, aby se předešlo spouštění vertikálního škálování a jakékoli přidružené přerušení služby bylo příliš často. Vezměte v úvahu interval minimálně 20-30 minut. Pokud se potřebujete vyhnout jakémukoli přerušení, zvažte horizontální škálování.
> 
> 

Další informace o tom, jak vytvářet výstrahy, najdete v následujících článcích:

* [Ukázky Azure Monitor PowerShellu](../azure-monitor/samples/powershell-samples.md)
* [Azure Monitor ukázky rozhraní příkazového řádku pro různé platformy](../azure-monitor/samples/cli-samples.md)

## <a name="summary"></a>Shrnutí

Tento článek ukázal jednoduché příklady vertikálního škálování. Pomocí těchto stavebních bloků – účet Automation, Runbooky, Webhooky, výstrahy – můžete připojit bohatou řadu událostí s přizpůsobenou sadou akcí.

[runbooks]: ./media/virtual-machine-scale-sets-vertical-scale-reprovision/runbooks.png
[gallery]: ./media/virtual-machine-scale-sets-vertical-scale-reprovision/runbooks-gallery.png
