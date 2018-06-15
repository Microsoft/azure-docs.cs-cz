---
title: Svisle škálování sady škálování virtuálního počítače Azure | Microsoft Docs
description: Postup svisle škálování virtuálního počítače v reakci na monitorování výstrahy s Azure Automation.
services: virtual-machine-scale-sets
documentationcenter: ''
author: gatneil
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 16b17421-6b8f-483e-8a84-26327c44e9d3
ms.service: virtual-machine-scale-sets
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-multiple
ms.devlang: na
ms.topic: article
ms.date: 08/03/2016
ms.author: negat
ms.openlocfilehash: 6e4733e023d1dc27fb099216f9afea07fe07446c
ms.sourcegitcommit: f46cbcff710f590aebe437c6dd459452ddf0af09
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/20/2017
ms.locfileid: "26781815"
---
# <a name="vertical-autoscale-with-virtual-machine-scale-sets"></a>Nastaví svislé škálování s škálování virtuálních počítačů
Tento článek popisuje postup svisle škálování Azure [sady škálování virtuálního počítače](https://azure.microsoft.com/services/virtual-machine-scale-sets/) s nebo bez reprovisioning. Svislé škálování virtuálních počítačů, které nejsou v sadách škálování, najdete v části [svisle škálování virtuální počítač Azure s Azure Automation](../virtual-machines/windows/vertical-scaling-automation.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Svislé škálování, také známé jako *škálovat* a *snižovat*, znamená zvýšením nebo snížením velikosti virtuálních počítačů (VM) v reakci na zatížení. Porovnání s tímto chováním [vodorovné škálování](virtual-machine-scale-sets-autoscale-overview.md), také nazývaný jako *škálovat* a *škálování v*, kde je počet virtuálních počítačů změnit v závislosti na zatížení.

Reprovisioning znamená odebrat existující virtuální počítač a jeho nahrazení novou. Když zvětšit nebo zmenšit velikost virtuálních počítačů v škálování virtuálních počítačů nastavení, v některých případech, kterou chcete změnit velikost existujících virtuálních počítačů a zachovat data, zatímco v ostatních případech je třeba nasadit nové virtuální počítače nové velikosti. Tento dokument popisuje obou případech.

Svislé škálování může být užitečné, když:

* Služba založený na virtuální počítače je v části využívat (třeba na víkendů). Zmenšení velikosti virtuálního počítače může snížit náklady na měsíční.
* Zvýšit velikost virtuálního počítače, aby se zvládl větší vyžádání bez vytvoření dalších virtuálních počítačů.

Nastavením svislé škálování má spouštěná na základě metriky na základě výstrahy od vaší škálovací sadu virtuálních počítačů. Když se aktivuje výstraha, aktivuje webhook, jehož této aktivační události, které se nastavit sadu runbook, která můžete škálovat vaše škálování nahoru nebo dolů. Svislé škálování lze nastavit pomocí následujících kroků:

1. Vytvořte účet Azure Automation se schopností spustit jako.
2. Importujte vertikální Škálováním Azure Automation runbook pro sady škálování virtuálního počítače do vašeho předplatného.
3. Přidejte webhook, jehož do runbooku.
4. Přidáte oznámení na vaše škálování virtuálních počítačů, nastavit pomocí webhooku oznámení.

> [!NOTE]
> Svislé automatické škálování se v určitém rozsahu velikostí virtuálních počítačů. Porovnání specifikace velikosti jednotlivých před rozhodnutím o škálování na další (vyšší počet vždy neindikuje větší velikost virtuálního počítače). Je možné škálovat mezi následující páry velikosti:
> 
> | Škálování pár velikosti virtuálních počítačů |  |
> | --- | --- |
> | Standard_A0 |Standard_A11 |
> | Standard_D1 |Standard_D14 |
> | Standard_DS1 |Standard_DS14 |
> | Standard_D1v2 |Standard_D15v2 |
> | Standard_G1 |Na úrovni Standard_G5 |
> | Standard_GS1 |Standard_GS5 |
> 
> 

## <a name="create-an-azure-automation-account-with-run-as-capability"></a>Vytvořit účet Azure Automation s možností Spustit jako
První věc, které musíte udělat, je vytvoření účtu Azure Automation, který je hostitelem sady runbook lze škálovat instance sady škálování virtuálního počítače. Nedávno [Azure Automation](https://azure.microsoft.com/services/automation/) zavedená funkce "Spustit jako účet", která zajišťuje nastavení se objekt služby pro automatické spouštění sady runbook jménem uživatele. Další informace naleznete v tématu:

* [Ověření runbooků pomocí účtu Spustit v Azure jako](../automation/automation-sec-configure-azure-runas-account.md)

## <a name="import-azure-automation-vertical-scale-runbooks-into-your-subscription"></a>Importování sad runbook Azure Automation vertikální Škálováním do předplatného
Sady runbook potřeby svisle škálovat vaše sady škálování virtuálního počítače jsou již publikován v galerii Azure Automation Runbook. Chcete-li importovat je do vašeho předplatného postupujte podle kroků v tomto článku:

* [Galerie Runbooků a modulů pro Azure Automation.](../automation/automation-runbook-gallery.md)

Vyberte možnost Procházet galerii v nabídce sady Runbook:

![Sady Runbook k importu][runbooks]

Sady runbook, které potřebují k importu se zobrazí. Vyberte sadu runbook založené na tom, zda chcete svislé škálování s nebo bez reprovisioning:

![Galerie Runbooků][gallery]

## <a name="add-a-webhook-to-your-runbook"></a>Přidat webhook, jehož do runbooku
Jakmile importujete sady runbook, přidání webhook, jehož do sady runbook, mohou být aktivovány výstrahu ze sady škálování virtuálního počítače. Podrobnosti o vytváření webhooku pro své sadě Runbook jsou popsané v tomto článku:

* [Webhooky Azure Automation.](../automation/automation-webhooks.md)

> [!NOTE]
> Ujistěte se, že zkopírujete webhooku URI před jeho zavřením dialogu webhooku, jako je nutné tuto adresu v další části.
> 
> 

## <a name="add-an-alert-to-your-virtual-machine-scale-set"></a>Přidání oznámení na vaše škálovací sadu virtuálních počítačů
Níže je skript prostředí PowerShell, který ukazuje, jak přidat oznámení škálování virtuálního počítače nastavit. Najdete v následujícím článku se získat název metriky do aktivovat upozornění na: [běžné metriky automatického škálování Azure monitorování](../monitoring-and-diagnostics/insights-autoscale-common-metrics.md).

```
$actionEmail = New-AzureRmAlertRuleEmail -CustomEmail user@contoso.com
$actionWebhook = New-AzureRmAlertRuleWebhook -ServiceUri <uri-of-the-webhook>
$threshold = <value-of-the-threshold>
$rg = <resource-group-name>
$id = <resource-id-to-add-the-alert-to>
$location = <location-of-the-resource>
$alertName = <name-of-the-resource>
$metricName = <metric-to-fire-the-alert-on>
$timeWindow = <time-window-in-hh:mm:ss-format>
$condition = <condition-for-the-threshold> # Other valid values are LessThanOrEqual, GreaterThan, GreaterThanOrEqual
$description = <description-for-the-alert>

Add-AzureRmMetricAlertRule  -Name  $alertName `
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
> Doporučujeme nakonfigurovat přiměřené časové okno k výstraze, aby se zabránilo spouštění svislé škálování a všechny přidružené přerušení služby, příliš často. Vezměte v úvahu okno minimálně 20-30 minut nebo déle. Vezměte v úvahu horizontální škálování, pokud je potřeba se zabránilo přerušení.
> 
> 

Další informace o tom, jak vytvářet výstrahy najdete v následujících článcích:

* [Rychlé spuštění ukázek Azure PowerShell monitorování](../monitoring-and-diagnostics/insights-powershell-samples.md)
* [Ukázek Azure rychlý start monitorování napříč platformami rozhraní příkazového řádku](../monitoring-and-diagnostics/insights-cli-samples.md)

## <a name="summary"></a>Souhrn
Tento článek vám ukázal, jednoduché příklady svislé škálování. Pomocí těchto stavební bloky – účet Automation, sady runbook, webhooků, výstrahy – můžete připojit mnoho různých událostí s vlastní sadu akcí.

[runbooks]: ./media/virtual-machine-scale-sets-vertical-scale-reprovision/runbooks.png
[gallery]: ./media/virtual-machine-scale-sets-vertical-scale-reprovision/runbooks-gallery.png
