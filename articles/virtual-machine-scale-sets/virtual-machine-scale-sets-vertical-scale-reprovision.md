---
title: Vertikální škálování škálovací sady virtuálních počítačů Azure | Dokumentace Microsoftu
description: Vertikální škálování virtuálního počítače v reakci na monitorování výstrahy se službou Azure Automation.
services: virtual-machine-scale-sets
documentationcenter: ''
author: mayanknayar
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
ms.author: manayar
ms.openlocfilehash: 6cd42675fa70f338fd4e1223d6a48bf8c6773915
ms.sourcegitcommit: 7cd706612a2712e4dd11e8ca8d172e81d561e1db
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/18/2018
ms.locfileid: "53579307"
---
# <a name="vertical-autoscale-with-virtual-machine-scale-sets"></a>Nastaví vertikální automatické škálování díky škálování virtuálního počítače
Tento článek popisuje, jak vertikální škálování Azure [Virtual Machine Scale Sets](https://azure.microsoft.com/services/virtual-machine-scale-sets/) s nebo bez něj neukončil. Vertikální škálování virtuálních počítačů, které nejsou ve škálovacích sadách najdete v tématu [vertikální škálování virtuálních počítačů Azure s využitím Azure Automation](../virtual-machines/windows/vertical-scaling-automation.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Vertikální škálování, označované také jako *vertikálně navýšit kapacitu* a *vertikálně snížit kapacitu*, znamená, že zvýšením nebo snížením velikosti virtuálních počítačů (VM) v reakci zatížení. Porovnat s tímto chováním [horizontální škálování](virtual-machine-scale-sets-autoscale-overview.md), která se také označují jako *horizontální navýšení kapacity* a *horizontálně*, kde je počet virtuálních počítačů změnit v závislosti na zatížení.

Neukončil znamená odebrání existujícího virtuálního počítače a jeho nahrazení atributem nový. Když při zvětšování a zmenšování velikosti virtuálních počítačů ve škálovací sadu virtuálních počítačů nastavení, v některých případech, kterou chcete změnit velikost stávajících virtuálních počítačů a uchovávat data, zatímco v jiných případech budete muset nasadit nové virtuální počítače nové velikosti. Tento dokument popisuje oba případy.

Vertikální škálování může být užitečné při:

* Služba založená na virtuálních počítačích je nevyužitých (například na bez víkendů). Zmenšení velikosti virtuálního počítače můžete snížit měsíční náklady.
* Zvětšení velikosti virtuálního počítače pro zvládnutí větší poptávky bez vytvoření dalších virtuálních počítačů.

Můžete nastavit vertikální škálování bude spouštěnou na základě na základě upozornění na metriku ze škálovací sady virtuálních počítačů. Při aktivaci upozornění aktivuje webhooku této aktivační události sady runbook, kterou chcete škálovat škálovací nastavena směrem nahoru nebo dolů. Vertikální škálování se dá nakonfigurovat pomocí následujících kroků:

1. Vytvoření účtu Azure Automation s možností Spustit jako.
2. Importujte runbooků Azure Automation vertikální škálování pro škálovací sady virtuálních počítačů do vašeho předplatného.
3. Přidání webhooku do runbooku.
4. Přidání výstrahy pro váš virtuální počítač škálovací sada s použitím oznámení webhooku.

> [!NOTE]
> Vertikální automatické škálování můžete provádět pouze v rámci určitých rozsahů o velikosti virtuálních počítačů. Porovnání specifikací jednotlivé velikosti před rozhodnutím o škálování na další (vyšší čísla vždy neznamená větší velikost virtuálního počítače). Je možné škálovat mezi následující páry velikosti:
> 
> | Velikosti virtuálních počítačů škálování pár |  |
> | --- | --- |
> | Standard_A0 |Standard_A11 |
> | Standard_D1 |Standard_D14 |
> | Standard_DS1 |Standard_DS14 |
> | Standard_D1v2 |Standard_D15v2 |
> | Standard_G1 |Standard_G5 |
> | Standard_GS1 |Standard_GS5 |
> 
> 

## <a name="create-an-azure-automation-account-with-run-as-capability"></a>Vytvoření účtu Azure Automation s možností Spustit jako
První věc, kterou musíte udělat, je vytvořit účet Azure Automation, který je hostitelem sady runbook použít ke škálování instance škálovací sady virtuálních počítačů. Nedávno [Azure Automation](https://azure.microsoft.com/services/automation/) zavedené "Účet Spustit jako" funkce, která nastavení se instanční objekt pro automatické spouštění sady runbook jménem uživatele. Další informace naleznete v tématu:

* [Ověření runbooků pomocí účtu Spustit v Azure jako](../automation/automation-sec-configure-azure-runas-account.md)

## <a name="import-azure-automation-vertical-scale-runbooks-into-your-subscription"></a>Import runbooků Azure Automation vertikální škálování do vašeho předplatného
Sady runbook potřebné pro vertikální škálování škálovací sady virtuálních počítačů jsou již publikován v galerii Runbooků Azure Automation. Chcete-li importovat je do vašeho předplatného postupujte podle kroků v tomto článku:

* [Galerie modulů a Runbooků Azure Automation](../automation/automation-runbook-gallery.md)

Zvolte možnost procházení Galerie v nabídce sady Runbook:

![Sady Runbook k importu][runbooks]

Sady runbook, které potřebují k importu se zobrazí. Vyberte sadu runbook na základě na, jestli chcete vertikální škálování s nebo bez něj neukončil:

![Galerie Runbooků][gallery]

## <a name="add-a-webhook-to-your-runbook"></a>Přidání webhooku do runbooku
Po importu sady runbook, přidání webhooku do sady runbook, může být aktivované výstrahy ze škálovací sady virtuálních počítačů. Podrobnosti o vytvoření webhooku pro své sadě Runbook jsou popsané v tomto článku:

* [Webhooky Azure Automation](../automation/automation-webhooks.md)

> [!NOTE]
> Zajistěte, aby že před jeho zavřením dialogu webhooku, budete ho potřebovat v další části tuto adresu zkopírujete webhooku identifikátoru URI.
> 
> 

## <a name="add-an-alert-to-your-virtual-machine-scale-set"></a>Přidání výstrahy pro škálovací sadu virtuálních počítačů
Následující skript prostředí PowerShell, který ukazuje, jak přidat upozornění na škálovací sadu virtuálních počítačů nastavena. Přečtěte si následující článek a získat tak název metriky, která se aktivuje upozornění na: [Azure Monitor běžné metriky automatického škálování](../azure-monitor/platform/autoscale-common-metrics.md).

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
> Doporučujeme nakonfigurovat přiměřené časové okno k výstraze, aby se zabránilo spouštění vertikální škálování a všechny přidružené přerušení služeb, příliš často. Vezměte v úvahu okno alespoň 20 – 30 minut nebo déle. Vezměte v úvahu horizontální škálování, pokud potřebujete, aby se zabránilo přerušení.
> 
> 

Další informace o tom, jak vytvářet výstrahy najdete v následujících článcích:

* [Ukázky Azure Powershellu monitorování rychlý start](../azure-monitor/platform/powershell-quickstart-samples.md)
* [Ukázky rychlý start Azure Cross-platform CLI monitorování](../azure-monitor/platform/cli-samples.md)

## <a name="summary"></a>Souhrn
Tento článek vám ukázal, jednoduché vertikální škálování příklady. Pomocí těchto stavebních bloků – účet služby Automation, sady runbook, webhooky, výstrahy – se můžete připojit bohatou řadu událostí, s vlastní sadu akcí.

[runbooks]: ./media/virtual-machine-scale-sets-vertical-scale-reprovision/runbooks.png
[gallery]: ./media/virtual-machine-scale-sets-vertical-scale-reprovision/runbooks-gallery.png
