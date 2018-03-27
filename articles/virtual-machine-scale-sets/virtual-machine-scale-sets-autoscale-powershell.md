---
title: "Sadách škálování virtuálních počítačů škálování s prostředím Azure PowerShell | Microsoft Docs"
description: "Postup vytvoření pravidla automatického škálování pro škálování virtuálních počítačů nastaví pomocí prostředí Azure PowerShell"
services: virtual-machine-scale-sets
documentationcenter: 
author: iainfoulds
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.assetid: 88886cad-a2f0-46bc-8b58-32ac2189fc93
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/19/2017
ms.author: iainfou
ms.openlocfilehash: 8928e56f353858234db314714d411a9c2990eb4e
ms.sourcegitcommit: 901a3ad293669093e3964ed3e717227946f0af96
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/21/2017
---
# <a name="automatically-scale-a-virtual-machine-scale-set-with-azure-powershell"></a>Automatické škálování škálování virtuálních počítačů, nastavit pomocí prostředí Azure PowerShell
Když vytvoříte sadu škálování, definujete se počet instancí virtuálního počítače, které chcete spustit. Podle požadavků vaší aplikaci změní, můžete automaticky zvýšit nebo snížit počet instancí virtuálního počítače. Schopnost škálování umožňuje udržovat tempo s poptávku zákazníků nebo odpověď na změny výkonu aplikace v průběhu cyklu vaší aplikace.

Tento článek ukazuje, jak vytvořit pravidla škálování pomocí prostředí Azure PowerShell, který sledovat výkon instancí virtuálního počítače ve škálovací sadě. Tato pravidla škálování zvyšte nebo snižte počet instancí virtuálního počítače v reakci na tyto metriky výkonu. Můžete také provést tyto kroky s [Azure CLI 2.0](virtual-machine-scale-sets-autoscale-cli.md) nebo [portál Azure](virtual-machine-scale-sets-autoscale-portal.md).


## <a name="prerequisites"></a>Požadavky
K vytvoření pravidla automatického škálování, budete potřebovat existujícího virtuálního počítače sady škálování. Můžete vytvořit s měřítkem [portál Azure](virtual-machine-scale-sets-create-portal.md), [prostředí Azure PowerShell](virtual-machine-scale-sets-create-powershell.md), nebo [Azure CLI 2.0](virtual-machine-scale-sets-create-cli.md).

Aby bylo snazší pro vytvoření pravidel škálování, definujte některé proměnné pro škálovací sadu. V následujícím příkladu definuje proměnné s názvem sad škálování *myScaleSet* ve skupině prostředků s názvem *myResourceGroup* a v *východní USA* oblast. Vaše předplatné se získat ID s [Get-AzureRmSubscription](/powershell/module/azurerm.profile/get-azurermsubscription). Pokud máte více předplatných, které jsou spojené s vaším účtem, vrátí se pouze první předplatné. Názvy a ID předplatného upravte takto:

```powershell
$mySubscriptionId = (Get-AzureRmSubscription).Id
$myResourceGroup = "myResourceGroupScaleSet"
$myScaleSet = "myScaleSet"
$myLocation = "East US"
```


## <a name="create-a-rule-to-automatically-scale-out"></a>Vytvořit pravidlo, které automaticky škálovat
Pokud vaše aplikace vyžádání zvyšuje, nastavit zatížení instancím virtuálních počítačů ve vaší škálování zvyšuje. Pokud je tato zvýšená zátěž konzistentní, ne jenom stručný vyžádání, můžete nakonfigurovat pravidla škálování zvýšit počet instancí virtuálního počítače ve škálovací sadě. Při vytváření těchto instancí virtuálního počítače a aplikace nasadí, byly sadou škálování začne distribuovat provoz prostřednictvím nástroje pro vyrovnávání zatížení. Můžete určit, jaké metriky, které chcete monitorovat, jako je například procesoru nebo disk, jak dlouho zatížení aplikace musí splňovat danou prahovou hodnotu a kolik instancí virtuálních počítačů pro přidání do měřítka nastavit.

Umožňuje vytvořit pravidlo s [New-AzureRmAutoscaleRule](/powershell/module/AzureRM.Insights/New-AzureRmAutoscaleRule) , zvýší se počet instancí virtuálního počítače v škálování nastavená, pokud průměrná zatížení procesoru je větší než 70 % během období 5 minut. Pokud toto pravidlo aktivuje, je 20 % zvýšit počet instancí virtuálního počítače. V sady škálování s malým počtem instancí virtuálních počítačů, může nechat `-ScaleActionScaleType` a určovat pouze `-ScaleActionValue` a zvyšuje *1* nebo *2* instance. V sady škálování s velký počet instancí virtuálního počítače, zvýšení o 10 % nebo 20 % může být vhodnější instance virtuálních počítačů.

Pro toto pravidlo se používají tyto parametry:

| Parametr               | Vysvětlení                                                                                                         | Hodnota          |
|-------------------------|---------------------------------------------------------------------------------------------------------------------|----------------|
| *-MetricName*           | Metrika výkonu ke sledování a škálování použít na nastavit akce.                                                   | Procento CPU |
| *-Časovými úseky*            | Jak často metriky se shromažďují pro analýzu.                                                                   | 1 minuta       |
| *-MetricStatistic*      | Definuje, jak by měla být agregován shromažďovat metriky pro analýzu.                                                | Průměr        |
| *-Hodnota TimeWindow*           | Množství času, které jsou monitorovány před porovnání hodnot metriky a prahová hodnota.                                   | 10 minut      |
| *-– Operátor*             | Operátor použit k porovnání metriky data před prahovou hodnotu.                                                     | Větší než   |
| *-Prahová hodnota*            | Hodnota, která způsobí, že pravidlo škálování akci aktivovat.                                                      | 70%            |
| *-ScaleActionDirection* | Určuje, zda byly sadou škálování by měl škálovat nahoru nebo dolů, když se pravidlo vztahuje.                                             | Zvětšit       |
| *– ScaleActionScaleType* | Označuje, že se počet instancí virtuálního počítače by měli měnit procentuální hodnota.                                 | Procentuální změnu |
| *-ScaleActionValue*     | Procento instancí virtuálních počítačů by mělo být změněno, když se pravidlo spustí.                                            | 20             |
| *-ScaleActionCooldown*  | Množství času, který se má čekat před pravidlo se použije znovu tak, aby akce škálování čas vstoupily v platnost. | 5 minut      |

Následující příklad vytvoří objekt s názvem *myRuleScaleOut* , obsahuje tento rozšiřování škálování využívajících pravidlo. *- MetricResourceId* používá předtím definovaný pro ID předplatného, název skupiny prostředků a škálování, nastavte název proměnné:

```powershell
$myRuleScaleOut = New-AzureRmAutoscaleRule `
  -MetricName "Percentage CPU" `
  -MetricResourceId /subscriptions/$mySubscriptionId/resourceGroups/$myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/$myScaleSet `
  -TimeGrain 00:01:00 `
  -MetricStatistic Average `
  -TimeWindow 00:10:00 `
  -Operator GreaterThan `
  -Threshold 70 `
  -ScaleActionDirection Increase `
  –ScaleActionScaleType PercentChangeCount `
  -ScaleActionValue 20 `
  -ScaleActionCooldown 00:05:00
```


## <a name="create-a-rule-to-automatically-scale-in"></a>Vytvořit pravidlo, které automaticky škálovat v
Večer nebo o víkendech se mohou snížit, vyžádání vaší aplikace. Je-li tento ke snížení zatížení přes v časovém intervalu konzistentní, můžete nakonfigurovat pravidla automatického škálování pro snížení počtu instancí virtuálních počítačů v sadě škálování. Tato akce škálování v snižuje náklady na provozování vaší škálování nastavena jako spustíte se počet instancí, které jsou nutné ke splnění aktuální vyžádání.

Vytvořit pravidlo s [New-AzureRmAutoscaleRule](/powershell/module/AzureRM.Insights/New-AzureRmAutoscaleRule) , sníží se počet instancí virtuálního počítače v škálování nastavená, pokud průměrná zatížení procesoru pak klesne pod 30 % po dobu 10 minut. Pokud toto pravidlo aktivuje, je počet instancí virtuálního počítače snížena o 20 %. Následující příklad vytvoří objekt s názvem *myRuleScaleDown* , obsahuje tento rozšiřování škálování využívajících pravidlo. *- MetricResourceId* používá předtím definovaný pro ID předplatného, název skupiny prostředků a škálování, nastavte název proměnné:

```powershell
$myRuleScaleIn = New-AzureRmAutoscaleRule `
  -MetricName "Percentage CPU" `
  -MetricResourceId /subscriptions/$mySubscriptionId/resourceGroups/$myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/$myScaleSet `
  -Operator LessThan `
  -MetricStatistic Average `
  -Threshold 30 `
  -TimeGrain 00:01:00 `
  -TimeWindow 00:10:00 `
  -ScaleActionCooldown 00:05:00 `
  -ScaleActionDirection Decrease `
  –ScaleActionScaleType PercentChangeCount `
  -ScaleActionValue 20
```


## <a name="define-an-autoscale-profile"></a>Definování profilu škálování
Pokud chcete přidružit škálovací sadu pravidel škálování, vytvořte profil. Profil škálování definuje výchozí, minimální a maximální měřítko sadu kapacity a přidruží pravidel škálování. Vytvoření profilu škálování s [New-AzureRmAutoscaleProfile](/powershell/module/AzureRM.Insights/New-AzureRmAutoscaleProfile). Následující příklad nastaví výchozí a minimální, kapacity *2* virtuálních počítačů instancí a maximálně *10*. Horizontální navýšení kapacity a pak připojených měřítka ve pravidel vytvořených v předchozích krocích:

```powershell
$myScaleProfile = New-AzureRmAutoscaleProfile `
  -DefaultCapacity 2  `
  -MaximumCapacity 10 `
  -MinimumCapacity 2 `
  -Rules $myRuleScaleOut,$myRuleScaleIn `
  -Name "autoprofile"
```


## <a name="apply-autoscale-rules-to-a-scale-set"></a>Použití pravidel automatického škálování pro sadu škálování
Posledním krokem je použít profil škálování pro škálovací sadu. Vaše měřítko je poté automaticky škálovat příchozí nebo odchozí v závislosti na aplikaci. Použít profil škálování s [přidat AzureRmAutoscaleSetting](/powershell/module/AzureRM.Insights/Add-AzureRmAutoscaleSetting) následujícím způsobem:

```powershell
Add-AzureRmAutoscaleSetting `
  -Location $myLocation `
  -Name "autosetting" `
  -ResourceGroup $myResourceGroup `
  -TargetResourceId /subscriptions/$mySubscriptionId/resourceGroups/$myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/$myScaleSet `
  -AutoscaleProfiles $myScaleProfile
```


## <a name="monitor-number-of-instances-in-a-scale-set"></a>Monitorování počtu instancí v sadě škálování
Pokud chcete zobrazit počet a stav instancí virtuálních počítačů, zobrazit seznam instancí ve vaší měřítku, s [Get-AzureRmVmssVM](/powershell/module/AzureRM.Compute/Get-AzureRmVmssVM). Stav označuje, pokud je jako měřítka nastavit automaticky horizontálně navýší kapacitu, nebo je zrušení zřízení jako měřítka automaticky přizpůsobí v zřizování instance virtuálního počítače. Následující příklad zobrazení stavu instance virtuálních počítačů pro škálování nastavení s názvem *myScaleSet* ve skupině prostředků s názvem *myResourceGroup*:

```powershell
Get-AzureRmVmssVM -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet"
```


## <a name="autoscale-based-on-a-schedule"></a>Automatické škálování podle plánu
V předchozích příkladech automaticky škálovat škálování nastavit příchozí nebo odchozí metriky základní hostitele, jako je například využití procesoru. Můžete také vytvořit pravidla automatické škálování podle plánů. Tato pravidla na základě plánu umožňují automaticky škálovat počet instancí virtuálního počítače před očekávaný nárůst vyžádání aplikace, jako je základní pracovní hodiny a poté automaticky škálovat počet instancí v čase, který předpokládáte méně vyžádání, jako je například víkendu.

K vytvoření pravidel škálování podle plánu, nikoli hostitele metriky, použijte portál Azure. Pravidla na základě plánu aktuálně nejde vytvořit v prostředí Azure PowerShell.


## <a name="next-steps"></a>Další postup
V tomto článku jste zjistili, jak používat automatické škálování pravidla můžete škálovat horizontálně a zvýšit nebo snížit *číslo* instance virtuálních počítačů ve vaší škálování nastavit. Můžete taky škálovat svisle zvýšení nebo snížení instance virtuálního počítače *velikost*. Další informace najdete v tématu [svislé škálování s sady škálování virtuálního počítače](virtual-machine-scale-sets-vertical-scale-reprovision.md).

Informace o tom, jak spravovat instancím virtuálních počítačů najdete v tématu [sadách škálování virtuálních počítačů spravovat pomocí prostředí Azure PowerShell](virtual-machine-scale-sets-windows-manage.md).

Zjistěte, jak vygenerovat upozornění, když vaše škálování pravidla aktivační událost, najdete v tématu [používat akce škálování k odesílání e-mailu a webhooku oznámení výstrah v monitorování Azure](../monitoring-and-diagnostics/insights-autoscale-to-webhook-email.md). Můžete také [protokoly auditu použijte k odesílání e-mailu a webhooku oznámení výstrah v monitorování Azure](../monitoring-and-diagnostics/insights-auditlog-to-webhook-email.md).
