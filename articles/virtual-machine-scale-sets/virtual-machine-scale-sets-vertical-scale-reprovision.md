---
title: Škálovací sady virtuálních strojů Azure svisle škálušednou škáluženy virtuálních strojů Azure
description: Jak vertikálně škálovat virtuální počítač v reakci na upozornění monitorování pomocí Azure Automation
author: mayanknayar
tags: azure-resource-manager
ms.assetid: 16b17421-6b8f-483e-8a84-26327c44e9d3
ms.service: virtual-machine-scale-sets
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-multiple
ms.topic: conceptual
ms.date: 04/18/2019
ms.author: manayar
ms.openlocfilehash: fa1dda2907e8400491c8d18897bb41fb9cff49fd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76274434"
---
# <a name="vertical-autoscale-with-virtual-machine-scale-sets"></a>Vertikální automatické škálování se sadami měřítek virtuálních strojů

Tento článek popisuje, jak vertikálně [škálovat škálovací sady virtuálních strojů](https://azure.microsoft.com/services/virtual-machine-scale-sets/) Azure s nebo bez opětovného zřízení. 

Vertikální škálování, označované také jako *vertikálně navýšit* kapacitu a *vertikálně snížit kapacitu*, znamená zvýšení nebo snížení velikosti virtuálních počítačů (VM) v reakci na zatížení. Porovnejte toto chování s [horizontální škálování](virtual-machine-scale-sets-autoscale-overview.md), označované také jako *horizontální navýšení kapacity* a *škálování v*, kde se mění počet virtuálních počítačích v závislosti na zatížení.

Reprovisioning znamená odebrání existujícího virtuálního mandu a jeho nahrazení novým. Když zvětšíte nebo zmenšíte velikost virtuálních počítačů ve škálovací sadě virtuálních počítačů, v některých případech chcete změnit velikost stávajících virtuálních počítačů a zachovat data, zatímco v jiných případech potřebujete nasadit nové virtuální počítače nové velikosti. Tento dokument se týká obou případů.

Vertikální měřítko může být užitečné, když:

* Služba postavená na virtuálních počítačích je nedostatečně využívaná (například o víkendech). Zmenšení velikosti virtuálního počítače může snížit měsíční náklady.
* Zvýšení velikosti virtuálních počítače, abyste se vyrovnali s větší poptávkou bez vytváření dalších virtuálních počítače.

Můžete nastavit vertikální škálování, které se aktivuje na základě upozornění založených na metrikách z škálovací sady virtuálních strojů. Když je výstraha aktivována, spustí webhook, který aktivuje runbook, který může škálovat nastavení škálování nahoru nebo dolů. Vertikální škálování lze nakonfigurovat následujícími kroky:

1. Vytvořte účet Azure Automation s funkcí run-as.
2. Importujte runbooky s vertikálním škálovacím systémem Azure Automation pro škálovací sady virtuálních strojů do vašeho předplatného.
3. Přidejte do sady Runbook webový hák.
4. Přidejte výstrahu do škálovací sady virtuálních strojů pomocí oznámení webhooku.

> [!NOTE]
> Vzhledem k velikosti prvního virtuálního počítače, velikosti, které lze škálovat na, může být omezena z důvodu dostupnosti jiných velikostí v clusteru aktuální virtuální počítač je nasazen v. V publikovaných runbooků automatizace použitých v tomto článku se postaráme o tento případ a pouze škálování v rámci dvojice velikosti virtuálního počítače. To znamená, že virtuální počítač Standard_D1v2 nebude náhle škálovat na Standard_G5 nebo zmenšen na Basic_A0. Také omezené velikosti virtuálních strojů navýšit/snížit kapacitu není podporována. Můžete se rozhodnout pro škálování mezi následujícími dvojicemi velikostí:
> 
> | Dvojice změn velikosti virtuálních počítače |  |
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

## <a name="create-an-azure-automation-account-with-run-as-capability"></a>Vytvoření účtu Azure Automation s funkcí run-as
První věc, kterou musíte udělat, je vytvořit účet Azure Automation, který je hostitelem runbooků používaných k škálování instancí škálovací sady virtuálních strojů. Nedávno [Azure Automation](https://azure.microsoft.com/services/automation/) představil funkci "Spustit jako účet", která umožňuje nastavení instančního objektu pro automatické spouštění runbooků jménem uživatele. Další informace naleznete v tématu:

* [Ověření runbooků pomocí účtu Spustit v Azure jako](../automation/automation-sec-configure-azure-runas-account.md)

## <a name="import-azure-automation-vertical-scale-runbooks-into-your-subscription"></a>Import runbooků Azure Automation s vertikálním škálovacím kamenem do předplatného

Sady runbooků potřebné k vertikálnímu škálování škálovacích sad virtuálních počítačů se už publikují v Galerii runbooků Azure Automation. Pokud je chcete importovat do předplatného, postupujte takto:

* [Galerie runbooků a modulů pro Azure Automation](../automation/automation-runbook-gallery.md)

Zvolte možnost Procházet galerii z nabídky Runbook:

![Sady Runbook, které mají být importovány][runbooks]

Zobrazí se sady Runbook, které je třeba importovat. Vyberte runbook na základě toho, jestli chcete vertikální změnu měřítka s nebo bez reprovisioningu:

![Galerie Runbooků][gallery]

## <a name="add-a-webhook-to-your-runbook"></a>Přidání webového háčku do sady Runbook

Po importu runbooků přidejte do sady Runbook webový hák, aby ho mohla aktivovat výstraha ze škálovací sady virtuálních strojů. Podrobnosti o vytvoření webhooku pro váš Runbook jsou popsány v tomto článku:

* [Webhooky Azure Automation](../automation/automation-webhooks.md)

> [!NOTE]
> Ujistěte se, že zkopírujete webhooku URI před zavřením webhooku dialogové okno, jak budete potřebovat tuto adresu v další části.
> 
> 

## <a name="add-an-alert-to-your-virtual-machine-scale-set"></a>Přidání upozornění do škálovací sady virtuálních strojů

Níže je skript prostředí PowerShell, který ukazuje, jak přidat výstrahu do škálovací sady virtuálních strojů. V následujícím článku získáte název metriky, na kterou se má výstraha zapálit: [Automatické škálování běžných metrik Azure Monitoru](../azure-monitor/platform/autoscale-common-metrics.md).

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
> Doporučuje se nakonfigurovat přiměřené časové okno pro výstrahu, aby se zabránilo aktivaci vertikální škálování a jakékoli přidružené přerušení služby, příliš často. Zvažte okno nejméně 20-30 minut nebo více. Pokud potřebujete zabránit přerušení, zvažte horizontální škálování.
> 
> 

Další informace o vytváření výstrah naleznete v následujících článcích:

* [Ukázky rychlého startu PowerShellu azure monitoru](../azure-monitor/platform/powershell-quickstart-samples.md)
* [Azure Monitor Rychlé spuštění příkazového systému pro různé platformy](../azure-monitor/platform/cli-samples.md)

## <a name="summary"></a>Souhrn

Tento článek ukázal jednoduché příklady vertikálního měřítka. S těmito stavebními bloky - Automatizační účet, runbooky, webhooky, výstrahy - můžete připojit bohatou škálu událostí s přizpůsobenou sadou akcí.

[runbooks]: ./media/virtual-machine-scale-sets-vertical-scale-reprovision/runbooks.png
[gallery]: ./media/virtual-machine-scale-sets-vertical-scale-reprovision/runbooks-gallery.png
