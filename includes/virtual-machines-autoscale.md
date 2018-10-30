---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: 0e46caf855bca184ff8a714d2c93d573b910fea8
ms.sourcegitcommit: 6e09760197a91be564ad60ffd3d6f48a241e083b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/29/2018
ms.locfileid: "50226848"
---
Můžete snadno [automatické škálování](../articles/monitoring-and-diagnostics/insights-autoscale-best-practices.md) vaše [virtuální počítače (VM)](../articles/virtual-machines/windows/overview.md) při použití [škálovací sady virtuálních počítačů](../articles/virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md) a [funkce automatického škálování Azure Monitorování](../articles/monitoring-and-diagnostics/monitoring-overview-autoscale.md). Vaše virtuální počítače musí být členy škálovací sadu automaticky škálovat. Tento článek obsahuje informace, které umožňuje lépe pochopit, jak škálování virtuálních počítačů vertikálně a horizontálně pomocí metody Automatické a ruční.

## <a name="horizontal-or-vertical-scaling"></a>Vodorovné nebo svislé škálování

Funkce automatického škálování služby Azure Monitor pouze škáluje vodorovně, což je zvýšení ("out") nebo snížení počtu virtuálních počítačů ("v"). Horizontální škálování je flexibilnější v situaci, cloud jako umožňuje potenciálně spouštíme tisíce virtuálních počítačů pro zpracování zátěže. Můžete škálovat horizontálně tak, že automaticky nebo ručně změníte kapacity (nebo počet instancí) škálovací sady. 

Vertikální škálování udržuje stejný počet virtuálních počítačů, ale budou virtuální počítače ("nahoru") méně nebo více ("výpadek") výkonné. Měřený v atributům, jako je paměť, rychlost procesoru nebo místa na disku. Vertikální škálování je závislý na dostupnosti větší hardwaru, která rychle narazí na horní mez a jednotlivých oblastech můžou lišit. Vertikální škálování obvykle vyžaduje virtuální počítač zastavit a restartovat. Vertikální škálování tak, že nastavíte nové velikosti v konfiguraci virtuálních počítačů ve škálovací sadě.

Používání runbooků v [Azure Automation](../articles/automation/automation-intro.md), můžete snadno [škálování virtuálních počítačů ve škálovací sadě](../articles/virtual-machine-scale-sets/virtual-machine-scale-sets-vertical-scale-reprovision.md) nahoru nebo dolů.

## <a name="create-a-virtual-machine-scale-set"></a>Vytvoření škálovací sady virtuálních počítačů

Škálovací sady umožňují snadno nasazovat a spravovat stejné virtuální počítače jako sadu. Můžete vytvořit Linux nebo Windows škálovacích sad pomocí [webu Azure portal](../articles/virtual-machine-scale-sets/virtual-machine-scale-sets-portal-create.md), [prostředí Azure PowerShell](../articles/virtual-machines/windows/tutorial-create-vmss.md), nebo [rozhraní příkazového řádku Azure](../articles/virtual-machines/linux/tutorial-create-vmss.md). Můžete také vytvářet a spravovat škálovací sady pomocí sady SDK, jako [Python](https://azure.microsoft.com/develop/python/) nebo [Node.js](/nodejs/azure), nebo přímo [rozhraní REST API](/rest/api/compute/virtualmachinescalesets). Automatické škálování virtuálních počítačů se provádí s použitím metriky a pravidla ke škálovací sadě.

## <a name="configure-autoscale-for-a-scale-set"></a>Konfigurace automatického škálování pro škálovací sady

Automatické škálování obsahuje správný počet virtuálních počítačů pro zpracování zátěže ve své aplikaci. Umožňuje přidat virtuální počítače, které zpracovat nárůst zatížení a ušetřit tak, že odeberete virtuální počítače, které jsou sedí nečinnosti. Můžete zadat minimální a maximální počet virtuálních počítačů pro spuštění na základě na sadu pravidel. S minimální díky že vaše aplikace vždycky běží i bez zatížení. S maximální hodnotou omezuje vaše možné celkové hodinové náklady.

Můžete povolit automatické škálování, při vytváření škálovací sady s použitím [prostředí Azure PowerShell](../articles/monitoring-and-diagnostics/insights-powershell-samples.md#create-and-manage-autoscale-settings) nebo [rozhraní příkazového řádku Azure](https://docs.microsoft.com/cli/azure/monitor/autoscale-settings). Můžete také povolit ho po vytvoření škálovací sady. Vytvoření škálovací sady, rozšíření nainstalovat a nakonfigurovat automatické škálování pomocí [šablony Azure Resource Manageru](../articles/virtual-machine-scale-sets/virtual-machine-scale-sets-windows-autoscale.md). Na webu Azure Portal povolit automatické škálování Azure monitor nebo povolit automatické škálování na základě nastavení škálovací sady.

![Povolit automatické škálování](./media/virtual-machines-autoscale/virtual-machines-autoscale-enable.png)
 
### <a name="metrics"></a>Metriky

Funkce automatického škálování služby Azure Monitor umožňuje škálovat počet spuštěných virtuálních počítačů nebo dolů na základě [metriky](../articles/monitoring-and-diagnostics/insights-autoscale-common-metrics.md). Ve výchozím nastavení virtuální počítače mají základní metriky na úrovni hostitele pro disk, síť a využití procesoru. Při konfiguraci shromažďování dat diagnostiky pomocí diagnostického rozšíření čítače výkonu další hostovaného operačního systému k dispozici pro disk, procesoru a paměti.

![Metriky kritéria](./media/virtual-machines-autoscale/virtual-machines-autoscale-criteria.png)

Pokud vaše aplikace potřebuje ke škálování na základě metrik, které nejsou k dispozici prostřednictvím hostitele a virtuální počítače ve škálovací sadě musí mít buď [diagnostického rozšíření Linuxu](../articles/virtual-machines/linux/diagnostic-extension.md) nebo [rozšíření diagnostiky Windows](../articles/virtual-machines/windows/ps-extensions-diagnostics.md)nainstalované. Pokud vytvoříte škálovací sadu pomocí webu Azure portal, musíte použít také Azure Powershellu nebo rozhraní příkazového řádku Azure k instalaci rozšíření se konfigurace diagnostiky, které potřebujete.
 
### <a name="rules"></a>Pravidla

[Pravidla](../articles/monitoring-and-diagnostics/monitoring-autoscale-scale-by-custom-metric.md) kombinovat metriku akce, která se má provést. Pokud se splní podmínky pravidel, se aktivují jednu nebo více akcí automatického škálování. Například může mít definované pravidlo, které zvýší počet virtuálních počítačů o 1, pokud průměrné využití procesoru překročí 85 procent.

![Akce automatického škálování](./media/virtual-machines-autoscale/virtual-machines-autoscale-actions.png)
 
### <a name="notifications"></a>Oznámení

Je možné [nastavení aktivační události](../articles/monitoring-and-diagnostics/insights-autoscale-to-webhook-email.md) tak, aby se nazývají konkrétní webové adresy URL nebo e-mailů se odesílá podle pravidel automatického škálování, které vytvoříte. Webhooky umožňují směrování Azure oznámení výstrah do jiných systémů pro následné zpracování nebo vlastních oznámení.

## <a name="manually-scale-vms-in-a-scale-set"></a>Ruční škálování virtuálních počítačů ve škálovací sadě

### <a name="horizontal"></a>Horizontálně

Můžete přidat nebo odebrat tak, že změna kapacity škálovací sady virtuálních počítačů. Na webu Azure Portal, můžete snížit nebo zvýšit počet virtuálních počítačů (zobrazené jako **počet instancí**) ve škálovací sadě posunutím panelu podmínku přepsání na obrazovce škálování doleva nebo doprava.

Pomocí Azure Powershellu, musíte získat škálovací sadu objekt pomocí [Get-AzureRmVmss](https://docs.microsoft.com/powershell/module/azurerm.compute/get-azurermvmss). Pak můžete nastavit **sku.capacity** vlastnost počtu instancí virtuálních počítačů, které chcete, aby a aktualizaci škálovací sady s [Update-AzureRmVmss](https://docs.microsoft.com/powershell/module/azurerm.compute/update-azurermvmss). Pomocí rozhraní příkazového řádku Azure, můžete změnit kapacitu s **– nové kapacity** parametr [az vmss scale](https://docs.microsoft.com/cli/azure/vmss#az_vmss_scale) příkazu.

### <a name="vertical"></a>Svisle

Můžete ručně změnit velikost virtuálního počítače na webu Azure Portal na velikost obrazovky pro škálovací sadu. Můžete použít Azure PowerShell s Get-AzureRmVmss, nastavení vlastnosti skladové položky referenční bitové kopie a následným použitím [Update-AzureRmVmss](https://docs.microsoft.com/powershell/module/azurerm.compute/update-azurermvmss) a [Update-AzureRmVmssInstance](https://docs.microsoft.com/powershell/module/azurerm.compute/update-azurermvmssinstance).

## <a name="next-steps"></a>Další postup

- Další informace o škálovacích sadách v [aspekty návrhu pro Škálovací sady](../articles/virtual-machine-scale-sets/virtual-machine-scale-sets-design-overview.md).

