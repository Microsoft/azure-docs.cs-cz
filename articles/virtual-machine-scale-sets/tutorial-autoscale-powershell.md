---
title: Kurz – Automatické škálování škálovací sady pomocí Azure PowerShellu | Microsoft Docs
description: Zjistěte, jak pomocí Azure PowerShellu automaticky škálovat škálovací sadu virtuálních počítačů s ohledem na zvyšující a snižující se požadavky na CPU.
services: virtual-machine-scale-sets
documentationcenter: ''
author: iainfoulds
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/27/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: f184c30f1f39563d6e029d506237e6b0e23ec482
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/19/2018
---
# <a name="tutorial-automatically-scale-a-virtual-machine-scale-set-with-azure-powershell"></a>Kurz: Automatické škálování škálovací sady virtuálních počítačů pomocí Azure PowerShellu
Při vytváření škálovací sady definujete počet instancí virtuálních počítačů, které chcete spouštět. S měnícími se požadavky na aplikaci můžete počet instancí virtuálních počítačů automaticky zvyšovat nebo snižovat. Možnost automatického škálování umožňuje držet krok s požadavky zákazníků nebo reagovat na změny výkonu aplikace v průběhu jejího životního cyklu. V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Použití automatického škálování u škálovací sady
> * Vytvoření a použití pravidel automatického škálování
> * Zátěžový test instancí virtuálních počítačů a aktivace pravidel automatického škálování
> * Opětovné automatické horizontální snížení kapacity po snížení požadavků

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

[!INCLUDE [cloud-shell-powershell.md](../../includes/cloud-shell-powershell.md)]

Pokud se rozhodnete nainstalovat a používat PowerShell místně, musíte použít modul Azure PowerShell verze 5.6.0 nebo novější. Verzi zjistíte spuštěním příkazu `Get-Module -ListAvailable AzureRM`. Pokud potřebujete upgrade, přečtěte si téma [Instalace modulu Azure PowerShell](/powershell/azure/install-azurerm-ps). Pokud používáte PowerShell místně, je také potřeba spustit příkaz `Connect-AzureRmAccount` pro vytvoření připojení k Azure.


## <a name="create-a-scale-set"></a>Vytvoření škálovací sady
Pro zjednodušení vytváření pravidel automatického škálování definujte pro svou škálovací sadu několik proměnných. Následující příklad definuje proměnné pro škálovací sadu *myScaleSet* ve skupině prostředků *myResourceGroup* a v oblasti *Východní USA*. ID vašeho předplatného se získá pomocí rutiny [Get-AzureRmSubscription](/powershell/module/azurerm.profile/get-azurermsubscription). Pokud ke svému účtu máte přidružených více předplatných, vrátí se pouze první předplatné. Upravte názvy a ID předplatného následujícím způsobem:

```azurepowershell-interactive
$mySubscriptionId = (Get-AzureRmSubscription)[0].Id
$myResourceGroup = "myResourceGroup"
$myScaleSet = "myScaleSet"
$myLocation = "East US"
```

Pomocí rutiny [Get-Credential](https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.security/Get-Credential) nastavte uživatelské jméno a heslo správce instancí virtuálních počítačů:

```azurepowershell-interactive
$cred = Get-Credential
```

Teď vytvořte škálovací sadu virtuálních počítačů pomocí rutiny [New-AzureRmVmss](/powershell/module/azurerm.compute/new-azurermvmss). Za účelem distribuce provozu do jednotlivých instancí virtuálních počítačů se vytvoří také nástroj pro vyrovnávání zatížení. Nástroj pro vyrovnávání zatížení obsahuje pravidla pro distribuci provozu na portu TCP 80, stejně jako provozu vzdálené plochy na portu TCP 3389 a vzdálené komunikace PowerShellu na portu TCP 5985:

```azurepowershell-interactive
New-AzureRmVmss `
  -ResourceGroupName $myResourceGroup `
  -VMScaleSetName $myScaleSet `
  -Location $myLocation `
  -VirtualNetworkName "myVnet" `
  -SubnetName "mySubnet" `
  -PublicIpAddressName "myPublicIPAddress" `
  -LoadBalancerName "myLoadBalancer" `
  -Credential $cred
```

Vytvoření a konfigurace všech prostředků škálovací sady a virtuálních počítačů trvá několik minut.

## <a name="create-a-rule-to-autoscale-out"></a>Vytvoření pravidla automatického horizontálního navýšení kapacity
Pokud se požadavky na vaši aplikaci zvýší, zvýší se i zatížení instancí virtuálních počítačů ve škálovací sadě. Pokud je toto zvýšené zatížení konzistentní, a nejedná se pouze o krátkou poptávku, můžete nakonfigurovat pravidla automatického škálování pro zvýšení počtu instancí virtuálních počítačů ve škálovací sadě. Po vytvoření těchto instancí virtuálních počítačů a nasazení aplikací do nich začne škálovací sada distribuovat provoz prostřednictvím nástroje pro vyrovnávání zatížení. Můžete řídit, které metriky se mají monitorovat, například CPU nebo disk, jak dlouho musí zatížení aplikace dosahovat dané prahové hodnoty a kolik instancí virtuálních počítačů se má do škálovací sady přidat.

Teď pomocí rutiny [New-AzureRmAutoscaleRule](/powershell/module/AzureRM.Insights/New-AzureRmAutoscaleRule) vytvořte pravidlo, které zvýší počet instancí virtuálních počítačů ve škálovací sadě, pokud je průměrné zatížení CPU vyšší než 70 % po dobu 5 minut. Když se pravidlo aktivuje, počet instancí virtuálních počítačů se zvýší o tři.

Pro toto pravidlo se používají následující parametry:

| Parametr               | Vysvětlení                                                                                                         | Hodnota          |
|-------------------------|---------------------------------------------------------------------------------------------------------------------|----------------|
| *-MetricName*           | Metrika výkonu, která se má monitorovat a na kterou se mají použít akce škálovací sady.                                                   | Procento CPU |
| *-TimeGrain*            | Četnost shromažďování metrik pro účely analýzy.                                                                   | 1 minuta       |
| *-MetricStatistic*      | Definuje způsob agregace shromážděných metrik pro účely analýzy.                                                | Průměr        |
| *-TimeWindow*           | Doba, která se monitoruje před porovnáním metrik a prahových hodnot.                                   | 5 minut      |
| *-Operator*             | Operátor sloužící k porovnání dat metriky s prahovou hodnotou.                                                     | Větší než   |
| *-Threshold*            | Hodnota, která způsobí aktivaci akce pravidlem automatického škálování.                                                      | 70 %            |
| *-ScaleActionDirection* | Definuje, jestli se má po použití pravidla kapacita škálovací sady vertikálně snížit nebo zvýšit.                                             | Zvýšit       |
| *–ScaleActionScaleType* | Určuje, že se má počet instancí virtuálních počítačů změnit o určitou hodnotu.                                    | Počet změn   |
| *-ScaleActionValue*     | Procento instancí virtuálních počítačů, které se mají po aktivaci pravidla změnit.                                            | 3              |
| *-ScaleActionCooldown*  | Doba, po kterou se má počkat před opětovným použitím pravidla, aby akce automatického škálování měly dostatek času se projevit. | 5 minut      |

Následující příklad vytvoří objekt *myRuleScaleOut* obsahující toto pravidlo vertikálního navýšení kapacity. Parametr *-MetricResourceId* používá dříve definované proměnné pro ID předplatného, název skupiny prostředků a název škálovací sady:

```azurepowershell-interactive
$myRuleScaleOut = New-AzureRmAutoscaleRule `
  -MetricName "Percentage CPU" `
  -MetricResourceId /subscriptions/$mySubscriptionId/resourceGroups/$myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/$myScaleSet `
  -TimeGrain 00:01:00 `
  -MetricStatistic "Average" `
  -TimeWindow 00:05:00 `
  -Operator "GreaterThan" `
  -Threshold 70 `
  -ScaleActionDirection "Increase" `
  –ScaleActionScaleType "ChangeCount" `
  -ScaleActionValue 3 `
  -ScaleActionCooldown 00:05:00
```


## <a name="create-a-rule-to-autoscale-in"></a>Vytvoření pravidla automatického horizontálního snížení kapacity
Večer nebo o víkendu se požadavky na vaši aplikaci můžou snížit. Pokud je toto snížené zatížení po určitou dobu konzistentní, můžete nakonfigurovat pravidla automatického škálování pro snížení počtu instancí virtuálních počítačů ve škálovací sadě. Tato akce horizontálního snížení kapacity sníží náklady na provoz škálovací sady, protože budete spouštět pouze takový počet instancí, který je potřeba ke zpracování aktuálních požadavků.

Pomocí rutiny [New-AzureRmAutoscaleRule](/powershell/module/AzureRM.Insights/New-AzureRmAutoscaleRule) vytvořte další pravidlo, které sníží počet instancí virtuálních počítačů ve škálovací sadě, pokud se průměrné zatížení CPU sníží pod 30 % po dobu 5 minut. Když se pravidlo aktivuje, počet instancí virtuálních počítačů se sníží o jeden. Následující příklad vytvoří objekt *myRuleScaleDown* obsahující toto pravidlo vertikálního navýšení kapacity. Parametr *-MetricResourceId* používá dříve definované proměnné pro ID předplatného, název skupiny prostředků a název škálovací sady:

```azurepowershell-interactive
$myRuleScaleIn = New-AzureRmAutoscaleRule `
  -MetricName "Percentage CPU" `
  -MetricResourceId /subscriptions/$mySubscriptionId/resourceGroups/$myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/$myScaleSet `
  -Operator "LessThan" `
  -MetricStatistic "Average" `
  -Threshold 30 `
  -TimeGrain 00:01:00 `
  -TimeWindow 00:05:00 `
  -ScaleActionCooldown 00:05:00 `
  -ScaleActionDirection "Decrease" `
  –ScaleActionScaleType "ChangeCount" `
  -ScaleActionValue 1
```


## <a name="define-an-autoscale-profile"></a>Definice profilu automatického škálování
Pravidla automatického škálování přidružíte ke škálovací sadě vytvořením profilu. Profil automatického škálování definuje výchozí, minimální a maximální kapacitu škálovací sady a přidruží k ní vaše pravidla automatického škálování. Vytvořte profil automatického škálování pomocí rutiny [New-AzureRmAutoscaleProfile](/powershell/module/AzureRM.Insights/New-AzureRmAutoscaleProfile). Následující příklad nastaví výchozí a minimální kapacitu na *2* instance virtuálních počítačů a maximální kapacitu na *10*. Pak se připojí pravidla horizontálního navýšení a snížení kapacity vytvořená v předchozích krocích:

```azurepowershell-interactive
$myScaleProfile = New-AzureRmAutoscaleProfile `
  -DefaultCapacity 2  `
  -MaximumCapacity 10 `
  -MinimumCapacity 2 `
  -Rules $myRuleScaleOut,$myRuleScaleIn `
  -Name "autoprofile"
```


## <a name="apply-autoscale-rules-to-a-scale-set"></a>Použití pravidel automatického škálování na škálovací sadu
Posledním krokem je použít profil automatického škálování na vaši škálovací sadu. Vaše škálovací sada pak bude moct horizontálně snížit nebo zvýšit svou kapacitu na základě požadavků na aplikaci. Následujícím způsobem použijte profil automatického škálování pomocí rutiny [Add-AzureRmAutoscaleSetting](/powershell/module/AzureRM.Insights/Add-AzureRmAutoscaleSetting):

```azurepowershell-interactive
Add-AzureRmAutoscaleSetting `
  -Location $myLocation `
  -Name "autosetting" `
  -ResourceGroup $myResourceGroup `
  -TargetResourceId /subscriptions/$mySubscriptionId/resourceGroups/$myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/$myScaleSet `
  -AutoscaleProfiles $myScaleProfile
```


## <a name="generate-cpu-load-on-scale-set"></a>Generování zatížení CPU ve škálovací sadě
Pokud chcete pravidla automatického škálování otestovat, vygenerujte nějaké zatížení CPU instancí virtuálních počítačů ve škálovací sadě. Toto simulované zatížení CPU způsobí, že pravidla automatického škálování provedou horizontální navýšení kapacity a zvýší počet instancí virtuálních počítačů. S následným snížením zatížení CPU pravidla automatického škálování provedou horizontální snížení kapacity a sníží počet instancí virtuálních počítačů.

Pokud chcete vypsat porty překladu adres pro připojení k instancím virtuálních počítačů ve škálovací sadě, získejte nejprve objekt nástroje pro vyrovnávání zatížení pomocí rutiny [Get-AzureRmLoadBalancer](/powershell/module/AzureRM.Network/Get-AzureRmLoadBalancer). Pak zobrazte pravidla příchozího překladu adres pomocí rutiny [Get-AzureRmLoadBalancerInboundNatRuleConfig](/powershell/module/AzureRM.Network/Get-AzureRmLoadBalancerInboundNatRuleConfig):

```azurepowershell-interactive
# Get the load balancer object
$lb = Get-AzureRmLoadBalancer -ResourceGroupName "myResourceGroup" -Name "myLoadBalancer"

# View the list of inbound NAT rules
Get-AzureRmLoadBalancerInboundNatRuleConfig -LoadBalancer $lb | Select-Object Name,Protocol,FrontEndPort,BackEndPort
```

Následující příklad výstupu ukazuje název instance, veřejnou IP adresu nástroje pro vyrovnávání zatížení a číslo portu, na které pravidla překladu adres směrují provoz:

```powershell
Name        Protocol FrontendPort BackendPort
----        -------- ------------ -----------
myRDPRule.0 Tcp             50001        3389
myRDPRule.1 Tcp             50002        3389
```

Název (*Name*) pravidla odpovídá názvu instance virtuálního počítače uvedenému ve výstupu předchozího příkazu [Get-AzureRmVmssVM](/powershell/module/azurerm.compute/get-azurermvmssvm). Pokud se například chcete připojit k instanci virtuálního počítače *0*, použijte *myRDPRule.0* a připojte se k portu *50001*. Pokud se chcete připojit k instanci virtuálního počítače *1*, použijte hodnotu z *myRDPRule.1* a připojte se k portu *50002*.

Zobrazte veřejnou IP adresu nástroje pro vyrovnávání zatížení pomocí rutiny [Get-AzureRmPublicIpAddress](/powershell/module/AzureRM.Network/Get-AzureRmPublicIpAddress):

```azurepowershell-interactive
Get-AzureRmPublicIpAddress -ResourceGroupName "myResourceGroup" -Name myPublicIP | Select IpAddress
```

Příklad výstupu:

```powershell
IpAddress
---------
52.168.121.216
```

Vytvořte vzdálené připojení k první instanci virtuálního počítače. Zadejte vlastní veřejnou IP adresu a číslo portu požadované instance virtuálního počítače uvedené ve výstupech předchozích příkazů. Po zobrazení výzvy zadejte přihlašovací údaje, které jste použili při vytváření škálovací sady (v ukázkových příkazech je to ve výchozím nastavení *azureuser* a *P@ssw0rd!*). Pokud používáte Azure Cloud Shell, proveďte tento krok z příkazového řádku místního PowerShellu nebo klienta Vzdálené plochy. Následující příklad se připojí k instanci virtuálního počítače *0*:

```powershell
mstsc /v 52.168.121.216:50001
```

Po přihlášení z hlavního panelu otevřete aplikaci Internet Explorer.

- Výběrem **OK** povolte příkazovému řádku *Použít doporučená nastavení zabezpečení, ochrany osobních údajů a kompatibility*.
- Do adresního řádku zadejte *http://download.sysinternals.com/files/CPUSTRES.zip*.
- Vzhledem k tomu, že je zapnutá konfigurace rozšířeného zabezpečení aplikace Internet Explorer, zvolte **Přidat** doménu *http://download.sysinternals.com* na seznam důvěryhodných webů.
- Po zobrazení výzvy ke stažení souboru vyberte **Otevřít** a pak výběrem **Spustit** spusťte nástroj *CPUSTRES.EXE*.

Pokud chcete vygenerovat nějaké zatížení CPU, zaškrtněte dvě políčka u vláken **Active** (Aktivní). V rozevírací nabídce **Activity** (Aktivita) u obou vláken vyberte *Maximum* (Maximální). Můžete otevřít Správce úloh a zkontrolovat, že je zatížení CPU na virtuálním počítači na 100 %.

![Nástroj CPU Stress generuje zatížení na instanci virtuálního počítače](media/tutorial-autoscale-powershell/cpu-stress-load.PNG)

Nechte relaci připojení ke vzdálené ploše otevřenou a otevřete další relaci připojení ke vzdálené ploše. Připojte se k druhé instanci virtuálního počítače s použitím čísla portu uvedeného ve výstupu předchozí rutiny [Get-AzureRmLoadBalancerInboundNatRuleConfig](/powershell/module/AzureRM.Network/Get-AzureRmLoadBalancerInboundNatRuleConfig):

```powershell
mstsc /v 52.168.121.216:50002
```

Po přihlášení k druhé instanci virtuálního počítače zopakujte předchozí kroky a stáhněte a spusťte *CPUSTRES.EXE*. Opět spusťte dvě vlákna **Active** (Aktivní) a nastavte aktivitu na *Maximum* (Maximální).

Obě relace připojení ke vzdálené ploše nechte otevřené, aby nástroj **CPU Stress** mohl zůstat spuštěný.


## <a name="monitor-the-active-autoscale-rules"></a>Monitorování aktivních pravidel automatického škálování
K monitorování počtu instancí virtuálních počítačů ve škálovací sadě použijte **while**. Zahájení procesu horizontálního navýšení kapacity na základě zatížení CPU generovaného nástrojem *CPUStress* na jednotlivých instancích virtuálních počítačů pravidly automatického škálování trvá přibližně 5 minut:

```azurepowershell-interactive
while (1) {Get-AzureRmVmssVM `
    -ResourceGroupName $myResourceGroup `
    -VMScaleSetName $myScaleSet; sleep 10}
```

Po dosažení prahové hodnoty CPU pravidla automatického škálování navýší počet instancí virtuálních počítačů ve škálovací sadě. Následující výstup ukazuje tři virtuální počítače vytvořené při automatickém horizontálním navýšení kapacity škálovací sady:

```powershell
ResourceGroupName         Name Location          Sku Capacity InstanceID ProvisioningState
-----------------         ---- --------          --- -------- ---------- -----------------
MYRESOURCEGROUP   myScaleSet_2   eastus Standard_DS2                   2         Succeeded
MYRESOURCEGROUP   myScaleSet_3   eastus Standard_DS2                   3         Succeeded
MYRESOURCEGROUP   myScaleSet_4   eastus Standard_DS2                   4          Creating
MYRESOURCEGROUP   myScaleSet_5   eastus Standard_DS2                   5          Creating
MYRESOURCEGROUP   myScaleSet_6   eastus Standard_DS2                   6          Creating
```

V relacích připojení ke vzdálené ploše obou instancí virtuálních počítačů ukončete nástroj **CPU Stress**. Průměrné zatížení CPU ve škálovací sadě se vrátí do normálu. Po dalších 5 minutách pak pravidla automatického škálování horizontálně sníží kapacitu počtu instancí virtuálních počítačů. Akce horizontálního snížení kapacity odeberou nejprve instance virtuálních počítačů s nejvyšším ID. Když škálovací sada používá skupiny nebo zóny dostupnosti, akce pro horizontální snížení kapacity se mezi tyto instance virtuálních počítačů rovnoměrně rozloží. Následující příklad výstupu ukazuje jednu instanci virtuálního počítače odstraněnou při automatickém horizontálním snížení kapacity škálovací sady:

```powershell
MYRESOURCEGROUP   myScaleSet_6   eastus Standard_DS2                   6          Deleting
```

Stisknutím `Ctrl-c` ukončete *while*. Kapacita škálovací sady se nadále bude každých 5 minut horizontálně snižovat odebráním jedné instance virtuálního počítače, dokud se nedosáhne minimálního počtu 2 instancí.


## <a name="clean-up-resources"></a>Vyčištění prostředků
Pokud chcete odebrat škálovací sadu a další prostředky, odstraňte skupinu prostředků a všechny její prostředky pomocí rutiny [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup). Parametr `-Force` potvrdí, že chcete prostředky odstranit, aniž by se na to zobrazoval další dotaz. Parametr `-AsJob` vrátí řízení na příkazový řádek bez čekání na dokončení operace.

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name "myResourceGroup" -Force -AsJob
```


## <a name="next-steps"></a>Další kroky
V tomto kurzu jste zjistili, jak automaticky horizontálně snižovat nebo zvyšovat kapacitu škálovací sady pomocí Azure PowerShellu:

> [!div class="checklist"]
> * Použití automatického škálování u škálovací sady
> * Vytvoření a použití pravidel automatického škálování
> * Zátěžový test instancí virtuálních počítačů a aktivace pravidel automatického škálování
> * Opětovné automatické horizontální snížení kapacity po snížení požadavků

Další příklady škálovacích sad virtuálních počítačů v akci najdete v následujících ukázkových skriptech Azure PowerShellu:

> [!div class="nextstepaction"]
> [Ukázkové skripty Azure PowerShellu pro škálovací sady](powershell-samples.md)
