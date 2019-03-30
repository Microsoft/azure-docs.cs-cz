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
ms.openlocfilehash: d3821f6a2bad56b46bccbcca8830be09ad1e44c7
ms.sourcegitcommit: 22ad896b84d2eef878f95963f6dc0910ee098913
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2019
ms.locfileid: "58648261"
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
> Kvůli velikosti první virtuální počítač, velikostí, které je možné škálovat, může být omezen z důvodu aktuální virtuální počítač nasazený v dostupnost dalších velikostí v clusteru. V runboocích publikované automation použité v tomto článku jsme postará o tento případ a pouze v rámci škálování následující dvojice velikost virtuálního počítače. To znamená, že virtuální počítač Standard_D1v2 není náhle být vertikálně kapacitu až na Standard_G5 úměrná Basic_A0. Také se nepodporuje omezené virtuální počítač velikosti škálovat směrem nahoru nebo dolů. Je možné škálovat mezi následující páry velikosti:
> 
> | Velikosti virtuálních počítačů škálování pár |  |
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
> 
> 

## <a name="create-an-azure-automation-account-with-run-as-capability"></a>Vytvoření účtu Azure Automation s možností Spustit jako
První věc, kterou musíte udělat, je vytvořit účet Azure Automation, který je hostitelem sady runbook použít ke škálování instance škálovací sady virtuálních počítačů. Nedávno [Azure Automation](https://azure.microsoft.com/services/automation/) zavedené "Účet Spustit jako" funkce, která nastavení se instanční objekt pro automatické spouštění sady runbook jménem uživatele. Další informace naleznete v tématu:

* [Ověření runbooků pomocí účtu Spustit v Azure jako](../automation/automation-sec-configure-azure-runas-account.md)

## <a name="import-azure-automation-vertical-scale-runbooks-into-your-subscription"></a>Import runbooků Azure Automation vertikální škálování do vašeho předplatného

Sady runbook potřebné pro vertikální škálování škálovací sady virtuálních počítačů jsou již publikován v galerii Runbooků Azure Automation. Chcete-li importovat je do vašeho předplatného postupujte podle kroků v tomto článku:

* [Galerie runbooků a modulů pro Azure Automation](../automation/automation-runbook-gallery.md)

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