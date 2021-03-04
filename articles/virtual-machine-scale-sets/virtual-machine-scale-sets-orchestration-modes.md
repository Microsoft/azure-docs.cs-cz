---
title: Režimy orchestrace pro Virtual Machine Scale Sets v Azure
description: Naučte se používat flexibilní a jednotné režimy orchestrace pro služby Virtual Machine Scale Sets v Azure.
author: fitzgeraldsteele
ms.author: fisteele
ms.topic: how-to
ms.service: virtual-machine-scale-sets
ms.subservice: extensions
ms.date: 02/12/2021
ms.reviewer: jushiman
ms.custom: mimckitt
ms.openlocfilehash: 3350ff7aa05232173e5fd3b21451a76a0a40683d
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/04/2021
ms.locfileid: "102043707"
---
# <a name="preview-orchestration-modes-for-virtual-machine-scale-sets-in-azure"></a>Verze Preview: režimy orchestrace pro Virtual Machine Scale Sets v Azure 

Sady škálování Virtual Machines poskytují logické seskupení virtuálních počítačů spravovaných platformou. Pomocí sady škálování můžete vytvořit konfigurační model virtuálních počítačů, automaticky přidat nebo odebrat další instance na základě procesoru nebo zatížení paměti a automaticky upgradovat na nejnovější verzi operačního systému. Sady škálování umožňují vytvářet virtuální počítače pomocí modelu konfigurace virtuálních počítačů, který je k dispozici v době vytváření sady škálování, a sada škálování může spravovat jenom virtuální počítače, které jsou implicitně vytvořené na základě modelu konfigurace. 

Režimy orchestrace sady škálování umožňují lepší kontrolu nad tím, jak jsou instance virtuálních počítačů spravovány sadou škálování. 

> [!IMPORTANT]
> Režim orchestrace je definován při vytváření sady škálování a nelze ji změnit ani aktualizovat později.


## <a name="scale-sets-with-uniform-orchestration"></a>Škálování sad pomocí jednotné orchestrace
Optimalizováno pro rozsáhlá Bezstavová zatížení se stejnými instancemi.

Virtual Machine Scale Sets s jednotnou orchestrací používá profil nebo šablonu virtuálního počítače pro horizontální navýšení kapacity na požadovanou kapacitu. I když existuje určitá možnost správy nebo přizpůsobení jednotlivých instancí virtuálních počítačů, používá jednotné použití identické instance virtuálních počítačů. Jednotlivé uniformní instance virtuálních počítačů se zveřejňují prostřednictvím příkazů rozhraní API VM pro škálování sady virtuálních počítačů. Jednotlivé instance nejsou kompatibilní se standardními příkazy rozhraní API pro virtuální počítače Azure IaaS, funkcemi pro správu Azure, jako je Azure Resource Manager označení prostředků RBAC oprávnění, Azure Backup nebo Azure Site Recovery. Jednotná orchestrace poskytuje záruku vysoké dostupnosti v doméně selhání, pokud je nakonfigurovaná s méně než 100 instancemi. Jednotná orchestrace je všeobecně dostupná a podporuje celou škálu správy a orchestrace sady škálování, včetně automatického škálování na základě metrik, ochrany instancí a automatických upgradů operačního systému. 


## <a name="scale-sets-with-flexible-orchestration"></a>Škálování sad pomocí flexibilní orchestrace 
Dosažení vysoké dostupnosti se škálováním s identickým nebo několika typy virtuálních počítačů.

Díky flexibilní orchestraci nabízí Azure v ekosystému virtuálních počítačů Azure jednotný zážitek. Flexibilní orchestrace nabízí záruky vysoké dostupnosti (až 1000 virtuálních počítačů) tím, že šíří virtuální počítače napříč doménami selhání v oblasti nebo v rámci zóny dostupnosti. Díky tomu můžete škálovat aplikaci a přitom udržet izolaci domény selhání, která je základem pro spouštění stavových úloh založených na kvoru, včetně těchto:
- Úlohy na základě kvora
- Open-Source databáze
- Stavové aplikace
- Služby, které vyžadují vysokou dostupnost a velkou škálu
- Služby, které chtějí kombinovat typy virtuálních počítačů nebo využívat virtuální počítače na vyžádání a na vyžádání společně
- Existující aplikace skupiny dostupnosti  

> [!IMPORTANT]
> Služba Virtual Machine Scale Sets v flexibilním režimu orchestrace je aktuálně ve verzi Public Preview. K používání funkcí verze Public Preview, které jsou popsané níže, se vyžaduje postup výslovného souhlasu.
> Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro produkční úlohy. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti.
> Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).


## <a name="what-has-changed-with-flexible-orchestration-mode"></a>Co se změnilo pomocí flexibilního režimu orchestrace?
Jednou z hlavních výhod flexibilní orchestrace je to, že poskytuje funkce orchestrace přes standardní virtuální počítače Azure IaaS namísto nastavení škálování pro podřízené virtuální počítače. To znamená, že můžete použít všechna standardní rozhraní API virtuálních počítačů při správě flexibilních instancí orchestrace místo rozhraní API virtuálních počítačů s použitím jednotné orchestrace. Během období Preview existuje několik rozdílů mezi správou instancí v flexibilní orchestraci versus jednotnou orchestraci. Obecně platí, že pokud je to možné, doporučujeme používat standardní rozhraní API virtuálních počítačů Azure IaaS. V této části vyzvýrazníme příklady osvědčených postupů pro správu instancí virtuálních počítačů pomocí flexibilní orchestrace.  

### <a name="assign-fault-domain-during-vm-creation"></a>Přiřazení domény selhání během vytváření virtuálního počítače
Můžete zvolit počet domén selhání pro flexibilní sadu škálování orchestrace. Ve výchozím nastavení platí, že když přidáte virtuální počítač do flexibilní sady škálování, Azure rovnoměrně rozšíří instance napříč doménami selhání. I když se doporučuje, aby Azure přidělil doménu selhání pro pokročilé scénáře nebo scénáře řešení potíží, můžete toto výchozí chování přepsat a zadat doménu selhání, kde se instance bude nakládat.

```azurecli-interactive 
az vm create –vmss "myVMSS"  –-platform_fault_domain 1
```

### <a name="instance-naming"></a>Pojmenovávání instancí 
Když vytvoříte virtuální počítač a přidáte ho do flexibilní sady škálování, budete mít úplnou kontrolu nad názvy instancí v rámci pravidel konvence vytváření názvů Azure. Když se virtuální počítače automaticky přidají do sady škálování pomocí automatického škálování, zadáte předponu a Azure připojí na konec názvu jedinečné číslo.

### <a name="query-instances-for-power-state"></a>Instance dotazů pro stav napájení
Upřednostňovanou metodou je použití Azure Resource graphu k dotazování na všechny virtuální počítače v sadě škálování virtuálního počítače. Azure Resource Graph nabízí efektivní možnosti dotazů pro prostředky Azure ve velkém měřítku napříč předplatnými. 

``` 
| where type =~ 'Microsoft.Compute/virtualMachines' 
| where properties.virtualMachineScaleSet contains "demo" 
| extend powerState = properties.extended.instanceView.powerState.code 
| project name, resourceGroup, location, powerState 
| order by resourceGroup desc, name desc 
```

Dotazování na prostředky pomocí [Azure Resource graphu](../governance/resource-graph/overview.md) je pohodlný a účinný způsob dotazování prostředků Azure a minimalizace volání rozhraní API poskytovateli prostředků. Azure Resource Graph je nakonec konzistentní mezipaměť, kde se nové nebo aktualizované prostředky nemusí projevit po dobu až 60 sekund. Další možnosti:
- Vypíše virtuální počítače v rámci skupiny prostředků nebo předplatného.
- Pomocí možnosti Rozbalit načtěte zobrazení instance (přiřazení domény selhání, stavy napájení a zřizování) pro všechny virtuální počítače v rámci vašeho předplatného.
- K získání modelu a zobrazení instancí pro jednu instanci použijte příkaz získat rozhraní API a příkazy Get VM.

### <a name="scale-sets-vm-batch-operations"></a>Operace dávky virtuálních počítačů v sadě škálování
Použijte příkazy standardního virtuálního počítače ke spuštění, zastavení, restartování a odstranění instancí namísto rozhraní API virtuálních počítačů pro škálování virtuálního počítače. Operace dávky virtuálních počítačů VM sady škálování virtuálního počítače (spustit vše, zastavit vše, obnovit image atd.) se nepoužívají s flexibilním režimem orchestrace. 

### <a name="monitor-application-health"></a>Monitorování stavu aplikace 
Monitorování stavu aplikací umožňuje vaší aplikaci poskytovat Azure pomocí prezenčního signálu k určení, jestli je vaše aplikace v pořádku nebo není v pořádku. Azure může automaticky nahradit instance virtuálních počítačů, které nejsou v pořádku. U instancí flexibilní sady škálování musíte na virtuální počítač nainstalovat a nakonfigurovat rozšíření pro stav aplikace. Pro instance sady s jednotným škálováním můžete použít buď rozšíření stavu aplikace, nebo změřit stav s Azure Load Balancer vlastní sondu stavu. 

### <a name="list-scale-sets-vm-api-changes"></a>Změny rozhraní API virtuálních počítačů v seznamu škálování sady 
Virtual Machine Scale Sets slouží k vypsání instancí, které patří do sady škálování. Díky flexibilní orchestraci nabízí příkaz list Virtual Machine Scale Sets VM seznam identifikátorů ID virtuálních počítačů sady škálování. Pak můžete zavolat příkazy GET Virtual Machine Scale Sets VM a získat další informace o tom, jak sada škálování funguje s instancí virtuálního počítače. Pokud chcete získat úplné podrobnosti o virtuálním počítači, použijte příkazy získat virtuální počítač Standard nebo [Azure Resource Graph](https://docs.microsoft.com/azure/governance/resource-graph/overview). 

### <a name="retrieve-boot-diagnostics-data"></a>Načíst data diagnostiky spouštění 
Pomocí standardních rozhraní API a příkazů virtuálních počítačů načtěte data diagnostiky spouštění instance a snímky obrazovky. Rozhraní API a příkazy pro diagnostiku spouštění Virtual Machine Scale Sets virtuálních počítačů se nepoužívají s flexibilními instancemi režimu orchestrace.

### <a name="vm-extensions"></a>Rozšíření virtuálních počítačů 
Místo rozšíření cílících na jednotné instance režimu orchestrace použijte rozšíření cílená pro standardní virtuální počítače.


## <a name="a-comparison-of-flexible-uniform-and-availability-sets"></a>Porovnání flexibilních, jednotných a skupin dostupnosti 
Následující tabulka porovnává flexibilní režim orchestrace, jednotný režim Orchestrace a skupiny dostupnosti podle jejich funkcí.

| Funkce | Podporováno flexibilní orchestrací (Preview) | Podporováno jednotnou orchestrací (Obecná dostupnost) | Podporováno aplikací AvSets (Obecná dostupnost) |
|-|-|-|-|
|         Typ virtuálního počítače  | Standardní virtuální počítač Azure IaaS (Microsoft. COMPUTE/VirtualMachines)  | Nastavit škálování pro konkrétní virtuální počítače (Microsoft. COMPUTE/virtualmachinescalesets/VirtualMachines)  | Standardní virtuální počítač Azure IaaS (Microsoft. COMPUTE/VirtualMachines)  |
|         Podporované SKU  |            D Series, E série, F Series, série, B Series, Intel, AMD  |            Všechny SKU  |            Všechny SKU  |
|         Zóny dostupnosti  |            Volitelně můžete zadat všechny instance půdy v jedné zóně dostupnosti. |            Určení instancí v rámci 1, 2 nebo 3 zón dostupnosti  |            Nepodporováno  |
|         Úplná kontrola nad virtuálními počítači, síťovými kartami a disky  |            Ano  |            Omezené řízení pomocí rozhraní API VM Virtual Machine Scale Sets  |            Ano  |
|         Automatické škálování  |            Ne  |            Ano  |            Ne  |
|         Přiřazení virtuálního počítače ke konkrétní doméně selhání  |            Ano  |             Ne   |            Ne  |
|         Odebrání síťových karet a disků při odstraňování instancí virtuálních počítačů  |            Ne  |            Ano  |            Ne  |
|         Zásady upgradu (VM Scale Sets) |            Ne  |            Automatické, vracení, ruční  |            –  |
|         Automatické aktualizace operačního systému (VM Scale Sets) |            Ne  |            Ano  |            –  |
|         Oprava zabezpečení hosta  |            Ano  |            Ne  |            Ano  |
|         Ukončení oznámení (VM Scale Sets) |            Ne  |            Ano  |            –  |
|         Oprava instance (VM Scale Sets) |            Ne  |            Ano   |            –  |
|         Urychlení sítě  |            Ano  |            Ano  |            Ano  |
|         Přímé instance a ceny   |            Ano, můžete mít instance obou přímých i běžných priorit.  |            Ano, instance musí být buď všechny přímé, nebo všechny běžné.  |            Ne, jenom instance běžné priority  |
|         Kombinace operačních systémů  |            Ano, Linux a Windows se můžou nacházet ve stejné flexibilní sadě škálování. |            Ne, instance se shodují s operačním systémem.  |               Ano, Linux a Windows se můžou nacházet ve stejné flexibilní sadě škálování. |
|         Monitorovat stav aplikace  |            Rozšíření stavu aplikace  |            Test stavu aplikace nebo služba Azure Load Balancer  |            Rozšíření stavu aplikace  |
|         UltraSSD disky   |            Ano  |            Ano, pouze pro oblast nasazení  |            Ne  |
|         InfiniBand   |            Ne  |            Ano, pouze jedna skupina umístění  |            Ano  |
|         Akcelerátor zápisu   |            Ne  |            Ano  |            Ano  |
|         Skupiny umístění blízkosti   |            Ano  |            Ano  |            Ano  |
|         Vyhrazení hostitelé Azure   |            Ne  |            Ano  |            Ano  |
|         Základní SLB   |            Ne  |            Ano  |            Ano  |
|         SKU Azure Load Balancer Standard |            Ano  |            Ano  |            Ano  |
|         Application Gateway  |            Ne  |            Ano  |            Ano  |
|         Řízení údržby   |            Ne  |            Ano  |            Ano  |
|         Vypsat virtuální počítače v sadě  |            Ano  |            Ano  |            Ano, vypsat virtuální počítače v AvSet  |
|         Výstrahy Azure  |            Ne  |            Ano  |            Ano  |
|         Přehledy virtuálních počítačů  |            Ne  |            Ano  |            Ano  |
|         Azure Backup  |            Ano  |            Ano  |            Ano  |
|         Azure Site Recovery  |            Ano, pouze PowerShell  |            Ano  |            Ano  |
|         Přidat nebo odebrat existující virtuální počítač do skupiny  |            Ne  |            Ne  |            Ne  | 


## <a name="register-for-flexible-orchestration-mode"></a>Zaregistrovat pro flexibilní režim orchestrace
Než budete moct v flexibilním režimu orchestrace nasadit Virtual Machine Scale Sets, musíte nejdřív zaregistrovat předplatné pro funkci Preview. Dokončení registrace může trvat několik minut. K registraci můžete použít následující Azure PowerShell nebo příkazy rozhraní příkazového řádku Azure.

### <a name="azure-powershell"></a>Azure PowerShell 
K povolení verze Preview pro vaše předplatné použijte rutinu [Register-AzProviderFeature](/powershell/module/az.resources/register-azproviderfeature) . 

```azurepowershell-interactive
Register-AzProviderFeature -FeatureName VMOrchestratorMultiFD -ProviderNamespace Microsoft.Compute `
Register-AzProviderFeature -FeatureName VMOrchestratorSingleFD -ProviderNamespace Microsoft.Compute  
```

Registrace funkce může trvat až 15 minut. Postup kontroly stavu registrace: 

```azurepowershell-interactive
Get-AzProviderFeature -FeatureName VMOrchestratorMultiFD -ProviderNamespace Microsoft.Compute 
```

Po registraci této funkce pro vaše předplatné dokončete proces výslovných přihlášení tím, že tuto změnu rozšíříte do zprostředkovatele výpočetních prostředků. 

```azurepowershell-interactive
Register-AzResourceProvider -ProviderNamespace Microsoft.Compute 
```

### <a name="azure-cli-20"></a>Azure CLI 2.0 
K povolení verze Preview pro vaše předplatné použijte [AZ Feature Registry](/cli/azure/feature#az-feature-register) . 

```azurecli-interactive
az feature register --namespace Microsoft.Compute --name VMOrchestratorMultiFD
az feature register --namespace microsoft.compute --name VMOrchestratorSingleFD 
```

Registrace funkce může trvat až 15 minut. Postup kontroly stavu registrace: 

```azurecli-interactive
az feature show --namespace Microsoft.Compute --name VMOrchestratorMultiFD 
```

Po registraci této funkce pro vaše předplatné dokončete proces výslovných přihlášení tím, že tuto změnu rozšíříte do zprostředkovatele výpočetních prostředků. 

```azurecli-interactive
az provider register --namespace Microsoft.Compute 
```


## <a name="get-started-with-flexible-orchestration-mode"></a>Začínáme s flexibilním režimem orchestrace

Začněte s flexibilním režimem orchestrace pro vaše sady škálování prostřednictvím Azure Portal, Azure CLI, Terraformu nebo REST API.

### <a name="azure-portal"></a>portál Azure

Pomocí Azure Portal vytvořit škálu virtuálních počítačů v flexibilním režimu orchestrace.

1. Přihlaste se na [Azure Portal](https://portal.azure.com).
1. Na panelu hledání vyhledejte a vyberte **Virtual Machine Scale Sets**. 
1. Na stránce **sady škálování virtuálních počítačů** vyberte **vytvořit** .
1. Na stránce **vytvořit sadu škálování virtuálního počítače** se podívejte na část **orchestrace** .
1. V **režimu orchestrace** vyberte možnost **flexibilní** .
1. Nastavte **počet domén selhání**.
1. Dokončete vytváření sady škálování. Další informace o tom, jak vytvořit sadu škálování, najdete [v tématu Vytvoření sady škálování v Azure Portal](quick-create-portal.md#create-virtual-machine-scale-set) .

:::image type="content" source="./media/virtual-machine-scale-sets-orchestration-modes/portal-create-orchestration-mode-flexible.png" alt-text="Režim orchestrace na portálu při vytváření sady škálování":::

V dalším kroku přidejte virtuální počítač do sady škálování v režimu flexibilní orchestrace.

1. Na panelu hledání vyhledejte a vyberte **virtuální počítače**.
1. Na stránce **virtuální počítače** vyberte **Přidat** .
1. Na kartě **základy** si prohlédněte část **Podrobnosti instance** .
1. Vyberte sadu škálování v **možnostech dostupnosti** a přidejte svůj virtuální počítač do sady škálování v flexibilním režimu orchestrace. Virtuální počítač můžete přidat do sady škálování ve stejné oblasti, zóně a skupině prostředků.
1. Dokončete vytváření virtuálního počítače. 

:::image type="content" source="./media/virtual-machine-scale-sets-orchestration-modes/vm-portal-orchestration-mode-flexible.png" alt-text="Přidání virtuálního počítače do sady škálování režimu flexibilní orchestrace":::


### <a name="azure-cli-20"></a>Azure CLI 2.0
Pomocí Azure CLI vytvořte flexibilní sadu virtuálních počítačů s měřítkem. Následující příklad ukazuje vytvoření flexibilní sady škálování, kde je počet domén selhání nastavený na 3, vytvoří se virtuální počítač a pak se přidá do flexibilní sady škálování. 

```azurecli-interactive
vmssflexname="my-vmss-vmssflex"  
vmname="myVM"  
rg="my-resource-group"  

az group create -n "$rg" -l $location  
az vmss create -n "$vmssflexname" -g "$rg" -l $location --orchestration-mode flexible --platform-fault-domain-count 3  
az vm create -n "$vmname" -g "$rg" -l $location --vmss $vmssflexname --image UbuntuLTS 
```

### <a name="terraform"></a>Terraform
Vytvořte flexibilní sadu škálování virtuálního počítače pomocí Terraformu. Tento proces vyžaduje **Terraformu Azurerm Provider v 2.15.0** nebo novějším. Všimněte si následujících parametrů:
- Pokud není zadaná žádná zóna, `platform_fault_domain_count` může být v závislosti na oblasti 1, 2 nebo 3.
- Je-li zadána zóna, `the fault domain count` může být 1.
- `single_placement_group``false`pro flexibilní sady virtuálních počítačů se musí nastavit parametr.
- Pokud provádíte místní nasazení, není nutné zadávat `zones` .

```terraform
resource "azurerm orchestrated_virtual_machine_scale_set" "tf_vmssflex" {
name = "tf_vmssflex"
location = azurerm_resource_group.myterraformgroup.location
resource_group_name = azurerm_resource_group.myterraformgroup.name
platform_fault_domain_count = 1
single_placement_group = false
zones = ["1"]
}
```


### <a name="rest-api"></a>REST API

1. Vytvořte prázdnou sadu škálování. Jsou vyžadovány následující parametry:
    - Rozhraní API verze 2019-12-01 (nebo vyšší) 
    - `false`Při vytváření flexibilní sady škálování musí být jediná skupina umístění.

    ```json
    {
    "type": "Microsoft.Compute/virtualMachineScaleSets",
    "name": "[parameters('virtualMachineScaleSetName')]",
    "apiVersion": "2019-12-01",
    "location": "[parameters('location')]",
    "properties": {
        "singlePlacementGroup": false,
        "platformFaultDomainCount": "[parameters('virtualMachineScaleSetPlatformFaultDomainCount')]"
        },
    "zones": "[variables('selectedZone')]"
    }
    ```

2. Přidejte virtuální počítače do sady škálování.
    1. Přiřaďte `virtualMachineScaleSet` vlastnost k sadě škálování, kterou jste předtím vytvořili. V `virtualMachineScaleSet` době vytváření virtuálního počítače je nutné zadat vlastnost. 
    1. Můžete použít funkci **kopírování ()** Azure Resource Manager šablonou k vytvoření více virtuálních počítačů současně. Podívejte se na téma [iterace prostředků](https://docs.microsoft.com/azure/azure-resource-manager/templates/copy-resources#iteration-for-a-child-resource) v šablonách Azure Resource Manager. 

    ```json
    {
    "type": "Microsoft.Compute/virtualMachines",
    "name": "[concat(parameters('virtualMachineNamePrefix'), copyIndex(1))]",
    "apiVersion": "2019-12-01",
    "location": "[parameters('location')]",
    "copy": {
        "name": "VMcopy",
        "count": "[parameters('virtualMachineCount')]"
        },
    "dependsOn": [
        "
        [resourceID('Microsoft.Compute/virtualMachineScaleSets', parameters('virtualMachineScaleSetName'))]",
        "
        [resourceID('Microsoft.Storage/storageAccounts', variables('diagnosticsStorageAccountName'))]",
        "
        [resourceID('Microsoft.Network/networkInterfaces', concat(parameters('virtualMachineNamePrefix'), copyIndex(1), '-NIC1'))]"
        ],
    "properties": {
        "virtualMachineScaleSet": {
            "id": "[resourceID('Microsoft.Compute/virtualMachineScaleSets', parameters('virtualMachineScaleSetName'))]"
        }
    }
    ```

Úplný příklad najdete v tématu [rychlý Start Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-vmss-flexible-orchestration-mode) .


## <a name="frequently-asked-questions"></a>Nejčastější dotazy

**Jak velká škála podporuje flexibilní orchestraci?**

Do sady škálování v flexibilním režimu orchestrace můžete přidat až 1000 virtuálních počítačů.

**Jak se dostupnost s flexibilní orchestrací porovnává se skupinami dostupnosti nebo jednotnou orchestraci?**

|   | Flexibilní orchestrace  | Jednotná orchestrace  | Skupiny dostupnosti  |
|-|-|-|-|
| Nasazení napříč zónami dostupnosti  | Ne  | Ano  | Ne  |
| Záruky dostupnosti domén selhání v rámci oblasti  | Ano, maximálně 1000 instancí se dá rozdělit do až tří domén selhání v oblasti. Maximální počet domén selhání se liší podle oblasti.  | Ano, až 100 instancí  | Ano, až 200 instancí  |
| Skupiny umístění  | Flexibilní režim vždycky používá více skupin umístění (singlePlacementGroup = false).  | Můžete zvolit jednu skupinu umístění nebo více skupin umístění. | –  |
| Aktualizační domény  | Žádná, údržba nebo aktualizace hostitele jsou v doméně selhání v doméně selhání.  | Až 5 aktualizačních domén  | Až 20 aktualizačních domén  |


## <a name="troubleshoot-scale-sets-with-flexible-orchestration"></a>Řešení potíží se sadami škálování pomocí flexibilní orchestrace
Najděte správné řešení pro váš scénář řešení potíží. 

```
InvalidParameter. The value 'False' of parameter 'singlePlacementGroup' is not allowed. Allowed values are: True
```

**Příčina:** Předplatné není zaregistrované pro režim flexibilní orchestrace Public Preview. 

**Řešení:** Postupujte podle výše uvedených pokynů a zaregistrujte se na flexibilní režim orchestrace Public Preview. 

```
InvalidParameter. The specified fault domain count 2 must fall in the range 1 to 1.
```

**Příčina:** `platformFaultDomainCount` Parametr není pro vybranou oblast nebo zónu platný. 

**Řešení:** Musíte vybrat platnou `platformFaultDomainCount` hodnotu. Pro nasazení napříč oblastmi `platformFaultDomainCount` je maximální hodnota 1. V případě regionálních nasazení, kde není určena žádná zóna, se maximální velikost `platformFaultDomainCount` liší v závislosti na oblasti. V tématu [Správa dostupnosti virtuálních počítačů pro skripty](../virtual-machines/manage-availability.md#use-managed-disks-for-vms-in-an-availability-set) můžete určit maximální počet domén selhání na oblast. 

```
OperationNotAllowed. Deletion of Virtual Machine Scale Set is not allowed as it contains one or more VMs. Please delete or detach the VM(s) before deleting the Virtual Machine Scale Set.
```

**Příčina:** Probíhá pokus o odstranění sady škálování v flexibilním režimu orchestrace, který je přidružen k jednomu nebo více virtuálním počítačům. 

**Řešení:** Odstraňte všechny virtuální počítače přidružené ke stupnici nastavené v flexibilním režimu Orchestrace a pak můžete odstranit sadu škálování.

```
InvalidParameter. The value 'True' of parameter 'singlePlacementGroup' is not allowed. Allowed values are: False.
```
**Příčina:** Předplatné je zaregistrované ve verzi Preview režimu flexibilní orchestrace; `singlePlacementGroup` parametr je však nastaven na *hodnotu true*. 

**Řešení:** `singlePlacementGroup` Vlastnost musí být nastavena na *hodnotu false*. 


## <a name="next-steps"></a>Další kroky
> [!div class="nextstepaction"]
> [Naučte se, jak nasadit aplikaci v sadě škálování.](virtual-machine-scale-sets-deploy-app.md)
