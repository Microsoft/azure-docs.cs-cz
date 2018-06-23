Můžete snadno [automaticky škálovat](../articles/monitoring-and-diagnostics/insights-autoscale-best-practices.md) vaše [virtuální počítače (VM)](../articles/virtual-machines/windows/overview.md) při použití [sady škálování virtuálního počítače](../articles/virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md) a [funkci Automatické škálování aplikace Azure Monitorování](../articles/monitoring-and-diagnostics/monitoring-overview-autoscale.md). Virtuální počítače musí být členy škálování nastaven na automaticky škálovat. Tento článek obsahuje informace, které umožňuje lépe pochopit, jak škálování virtuálních počítačů vertikálně a horizontálně pomocí automatické i ruční metody.

## <a name="horizontal-or-vertical-scaling"></a>Vodorovné nebo svislé škálování

Funkce škálování Azure monitorování pouze škáluje vodorovně, která je zvýšení ("na") nebo snížení počtu virtuálních počítačů ("v"). Vodorovné škálování je flexibilnější v situaci, cloud jako umožňuje spustit potenciálně tisíce virtuálních počítačů pro zpracování zátěže. Můžete škálovat horizontálně automaticky nebo ručně změnou kapacity (nebo počet instancí) byly sadou škálování. 

Svislé škálování udržuje stejný počet virtuálních počítačů, ale umožňuje virtuální počítače, další ("nahoru") nebo méně ("dolů") výkonné. Výkon se měří v atributy, jako je například paměť, rychlost procesoru nebo místa na disku. Svislé škálování je závislý na dostupnosti větší hardware, který rychle dotkne horní limit a můžete se liší podle oblasti. Svislé škálování také obvykle vyžaduje virtuální počítač zastavit a restartovat. Můžete škálovat svisle nastavením novou velikost v konfiguraci virtuálních počítačů v sadě škálování.

Používání sad runbook v [Azure Automation](../articles/automation/automation-intro.md), můžete snadno [škálování virtuální počítače ve škálovací sadě](../articles/virtual-machine-scale-sets/virtual-machine-scale-sets-vertical-scale-reprovision.md) nahoru nebo dolů.

## <a name="create-a-virtual-machine-scale-set"></a>Vytvoření škálovací sady virtuálních počítačů

Sady škálování usnadnit nasadit a spravovat virtuální počítače stejná jako sada. Můžete vytvořit Linux nebo Windows škálování nastaví pomocí [portál Azure](../articles/virtual-machine-scale-sets/virtual-machine-scale-sets-portal-create.md), [prostředí Azure PowerShell](../articles/virtual-machines/windows/tutorial-create-vmss.md), nebo [rozhraní příkazového řádku Azure](../articles/virtual-machines/linux/tutorial-create-vmss.md). Můžete také vytvořit a spravovat sady škálování s sady SDK, jako [Python](/develop/python) nebo [Node.js](/nodejs/azure), nebo přímo pomocí [rozhraní REST API](/rest/api/compute/virtualmachinescalesets). Použitím metriky a pravidla do sady škálování se provádí automatické škálování virtuálních počítačů.

## <a name="configure-autoscale-for-a-scale-set"></a>Konfigurace automatického škálování pro sadu škálování

Automatické škálování poskytuje správný počet virtuálních počítačů pro zpracování zátěže ve vaší aplikaci. Umožňuje přidat virtuální počítače a zpracovat nárůst zatížení ušetřit peníze odebráním virtuálních počítačů, které jsou uložený nečinnosti. Můžete zadat minimální a maximální počet virtuálních počítačů ke spuštění na základě na sadu pravidel. S minimální díky, že aplikace je vždy spuštěna ani v žádné zatížení. S maximální hodnotu, která omezuje vaše náklady možné každou hodinu.

Můžete povolit škálování, když vytvoříte pomocí sad škálování [prostředí Azure PowerShell](../articles/monitoring-and-diagnostics/insights-powershell-samples.md#create-and-manage-autoscale-settings) nebo [rozhraní příkazového řádku Azure](https://docs.microsoft.com/cli/azure/monitor/autoscale-settings). Můžete také ho povolit po se vytvoří sada škálování. Můžete vytvořit sadu škálování, nainstalujte rozšíření a nakonfigurovat automatické škálování [šablony Azure Resource Manageru](../articles/virtual-machine-scale-sets/virtual-machine-scale-sets-windows-autoscale.md). Na portálu Azure povolit škálování z monitorování Azure nebo povolit škálování z nastavení sady škálování.

![Povolit automatické škálování](./media/virtual-machines-autoscale/virtual-machines-autoscale-enable.png)
 
### <a name="metrics"></a>Metriky

Funkce škálování Azure monitorování umožňuje škálovat počet spuštěných virtuálních počítačů nebo dolů na základě [metriky](../articles/monitoring-and-diagnostics/insights-autoscale-common-metrics.md). Virtuální počítače ve výchozím nastavení, poskytují základní metriky na úrovni hostitele pro disk, síť a využití procesoru. Když nakonfigurujete shromažďování dat diagnostiky pomocí diagnostiky rozšíření, čítače výkonu další hostovaného operačního systému k dispozici pro disk, procesoru a paměti.

![Metriky kritéria](./media/virtual-machines-autoscale/virtual-machines-autoscale-criteria.png)

Pokud aplikace potřebuje škálovat podle metriky, které nejsou k dispozici prostřednictvím hostitele a potom virtuální počítače ve škálovací sadě musí mít buď [rozšíření diagnostiky Linux](../articles/virtual-machines/linux/diagnostic-extension.md) nebo [rozšíření diagnostiky Windows](../articles/virtual-machines/windows/ps-extensions-diagnostics.md)nainstalována. Pokud vytvoříte škálování nastavit pomocí portálu Azure, budete muset použít také prostředí Azure PowerShell nebo rozhraní příkazového řádku Azure k instalaci rozšíření se konfigurace diagnostiky, které potřebujete.
 
### <a name="rules"></a>Pravidla

[Pravidla](../articles/monitoring-and-diagnostics/monitoring-autoscale-scale-by-custom-metric.md) kombinovat metriky s akci, kterou chcete provést. Pokud jsou splněny podmínky pravidla, vyvolají se jeden nebo víc akcí škálování. Například můžete mít definováno pravidlo, které zvýší počet virtuálních počítačů pomocí 1, pokud průměrné využití procesoru překročí 85 procent.

![Akce škálování](./media/virtual-machines-autoscale/virtual-machines-autoscale-actions.png)
 
### <a name="notifications"></a>Oznámení

Můžete [nastavit aktivace](../articles/monitoring-and-diagnostics/insights-autoscale-to-webhook-email.md) tak, aby konkrétní webové adresy URL se nazývají nebo e-mailů jsou zasílány pomocí pravidel automatického škálování, které vytvoříte. Webhooky umožňují směrovat Azure oznámení výstrah do jiných systémů pro následné zpracování nebo vlastních oznámení.

## <a name="manually-scale-vms-in-a-scale-set"></a>Ručně škálovat, virtuální počítače ve škálovací sadě

### <a name="horizontal"></a>Horizontálně

Můžete přidat nebo odebrat tak, že změníte kapacitu škálovací sady virtuálních počítačů. Na portálu Azure, můžete snížit nebo zvýšit počet virtuálních počítačů (zobrazené jako **instance počet**) v škálování, která nastavuje klouzavé panelu podmínku přepsání na obrazovce škálování doleva nebo doprava.

Pomocí Azure PowerShell, které je potřeba získat objekt nastavení škálování pomocí [Get-AzureRmVmss](https://docs.microsoft.com/powershell/module/azurerm.compute/get-azurermvmss). Pak můžete nastavit **sku.capacity** vlastnosti tak, aby počet virtuálních počítačů, které chcete a aktualizace měřítka nastavit s [aktualizace AzureRmVmss](https://docs.microsoft.com/powershell/module/azurerm.compute/update-azurermvmss). Použití Azure CLI, změníte kapacitu s **– nové kapacity** parametr pro [az vmss škálování](https://docs.microsoft.com/cli/azure/vmss#az_vmss_scale) příkaz.

### <a name="vertical"></a>Svisle

Můžete ručně změnit velikost na portálu Azure na velikost obrazovky pro škálovací sadu virtuálních počítačů. Můžete použít Azure PowerShell s Get-AzureRmVmss, vlastnost sku referenční bitové kopie a používat [aktualizace AzureRmVmss](https://docs.microsoft.com/powershell/module/azurerm.compute/update-azurermvmss) a [aktualizace AzureRmVmssInstance](https://docs.microsoft.com/powershell/module/azurerm.compute/update-azurermvmssinstance).

## <a name="next-steps"></a>Další postup

- Další informace o sadách škálování v [aspekty návrhu pro sadách škálování](../articles/virtual-machine-scale-sets/virtual-machine-scale-sets-design-overview.md).

