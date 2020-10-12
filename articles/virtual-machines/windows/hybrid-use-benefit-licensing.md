---
title: Zvýhodněné hybridní využití Azure pro Windows Server
description: Přečtěte si, jak maximalizovat výhody programu Software Assurance v systému Windows, aby se do Azure přinášejí místní licence.
author: xujing-ms
ms.service: virtual-machines-windows
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 4/22/2018
ms.author: xujing
ms.openlocfilehash: 43784fe2bb94d5abd012f22ed530e4631a304614
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91335982"
---
# <a name="azure-hybrid-benefit-for-windows-server"></a>Zvýhodněné hybridní využití Azure pro Windows Server
Zákazníci se Software Assurance, Zvýhodněné hybridní využití Azure pro Windows Server, vám umožní využívat místní licence k Windows serveru a spouštět virtuální počítače s Windows v Azure s nižšími náklady. K nasazení nových virtuálních počítačů s operačním systémem Windows můžete použít Zvýhodněné hybridní využití Azure pro Windows Server. Tento článek se dokončí postupem, jak nasadit nové virtuální počítače s Zvýhodněné hybridní využití Azure pro Windows Server a jak aktualizovat existující běžící virtuální počítače. Další informace o Zvýhodněné hybridní využití Azure pro licencování Windows serveru a úspory nákladů najdete na [stránce zvýhodněné hybridní využití Azure licencování Windows serveru](https://azure.microsoft.com/pricing/hybrid-use-benefit/).

Každá licence 2 – procesor nebo každá sada 16bitových licencí má nárok na dvě instance až 8 jader nebo jednu instanci až 16 jader. Zvýhodněné hybridní využití Azure pro licence na Standard Edition se dá použít jenom jednou v místním prostředí nebo v Azure. Výhody Datacenter Edition umožňují současné využití v místním prostředí i v Azure.

Používání Zvýhodněné hybridní využití Azure pro Windows Server s libovolnými virtuálními počítači s operačním systémem Windows Server je teď podporované ve všech oblastech, včetně virtuálních počítačů s dalším softwarem, jako je SQL Server nebo software z webu Marketplace jiného výrobce. 


## <a name="classic-vms"></a>Klasické virtuální počítače

U klasických virtuálních počítačů se podporuje jenom nasazení nového virtuálního počítače z místních imagí. Aby bylo možné využít výhod podporovaných v tomto článku, musíte nejprve migrovat klasické virtuální počítače do modelu Správce prostředků.

[!INCLUDE [classic-vm-deprecation](../../../includes/classic-vm-deprecation.md)]
 

## <a name="ways-to-use-azure-hybrid-benefit-for-windows-server"></a>Způsoby použití Zvýhodněné hybridní využití Azure pro Windows Server
K dispozici je několik způsobů použití virtuálních počítačů s Windows s Zvýhodněné hybridní využití Azure:

1. Virtuální počítače můžete nasadit z jedné z uvedených imagí Windows serveru na Azure Marketplace
2. Můžete nahrát vlastní virtuální počítač a nasadit ho pomocí šablony Správce prostředků nebo Azure PowerShell
3. Existující virtuální počítač můžete přepínat a převádět z provozu pomocí Zvýhodněné hybridní využití Azure nebo platby na vyžádání pro Windows Server.
4. Můžete také použít Zvýhodněné hybridní využití Azure pro Windows Server v sadě škálování virtuálního počítače.


## <a name="create-a-vm-with-azure-hybrid-benefit-for-windows-server"></a>Vytvoření virtuálního počítače s Zvýhodněné hybridní využití Azure pro Windows Server
Všechny image založené na operačním systému Windows Server jsou podporované pro Zvýhodněné hybridní využití Azure pro Windows Server. Můžete použít image podpory platformy Azure nebo nahrát vlastní image Windows serveru. 

### <a name="portal"></a>Portál
Pokud chcete vytvořit virtuální počítač s Zvýhodněné hybridní využití Azure pro Windows Server, použijte přepínač v části "Uložit peníze".

### <a name="powershell"></a>PowerShell


```powershell
New-AzVm `
    -ResourceGroupName "myResourceGroup" `
    -Name "myVM" `
    -Location "East US" `
    -ImageName "Win2016Datacenter" `
    -LicenseType "Windows_Server"
```

### <a name="cli"></a>Rozhraní příkazového řádku
```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --location eastus \
    --license-type Windows_Server
```

### <a name="template"></a>Šablona
V rámci šablon Správce prostředků `licenseType` je třeba zadat další parametr. Další informace o [vytváření Azure Resource Manager šablon](../../azure-resource-manager/templates/template-syntax.md) najdete v článku.
```json
"properties": {
    "licenseType": "Windows_Server",
    "hardwareProfile": {
        "vmSize": "[variables('vmSize')]"
    }
```

## <a name="convert-an-existing-vm-using-azure-hybrid-benefit-for-windows-server"></a>Převod existujícího virtuálního počítače pomocí Zvýhodněné hybridní využití Azure pro Windows Server
Pokud máte existující virtuální počítač, který chcete převést, aby využíval výhody Zvýhodněné hybridní využití Azure pro Windows Server, můžete aktualizovat typ licence vašeho virtuálního počítače podle následujících pokynů.

> [!NOTE]
> Změna typu licence na virtuálním počítači nezpůsobí, že se systém restartuje nebo nezpůsobí interuption služby.  Je to jednoduše aktualizace příznaku metadat.
> 

### <a name="portal"></a>Portál
V okně virtuálního počítače portálu můžete virtuální počítač aktualizovat tak, aby používal Zvýhodněné hybridní využití Azure tím, že vyberete možnost konfigurace a přepnete možnost zvýhodněné hybridní využití Azure.

### <a name="powershell"></a>PowerShell
- Převod stávajících virtuálních počítačů s Windows serverem na Zvýhodněné hybridní využití Azure pro Windows Server

    ```powershell
    $vm = Get-AzVM -ResourceGroup "rg-name" -Name "vm-name"
    $vm.LicenseType = "Windows_Server"
    Update-AzVM -ResourceGroupName rg-name -VM $vm
    ```
    
- Převod virtuálních počítačů s Windows serverem s výhodou zpět na průběžné platby

    ```powershell
    $vm = Get-AzVM -ResourceGroup "rg-name" -Name "vm-name"
    $vm.LicenseType = "None"
    Update-AzVM -ResourceGroupName rg-name -VM $vm
    ```
    
### <a name="cli"></a>Rozhraní příkazového řádku
- Převod stávajících virtuálních počítačů s Windows serverem na Zvýhodněné hybridní využití Azure pro Windows Server

    ```azurecli
    az vm update --resource-group myResourceGroup --name myVM --set licenseType=Windows_Server
    ```

### <a name="how-to-verify-your-vm-is-utilizing-the-licensing-benefit"></a>Jak ověřit, že váš virtuální počítač využívá výhod licencování
Po nasazení virtuálního počítače pomocí PowerShellu, Správce prostředků šablony nebo portálu, můžete nastavení ověřit v následujících metodách.

### <a name="portal"></a>Portál
V okně virtuálního počítače portálu můžete kliknutím na kartu Konfigurace Zobrazit přepínač pro Zvýhodněné hybridní využití Azure pro Windows Server.

### <a name="powershell"></a>PowerShell
Následující příklad ukazuje typ licence pro jeden virtuální počítač.
```powershell
Get-AzVM -ResourceGroup "myResourceGroup" -Name "myVM"
```

Výstup:
```powershell
Type                     : Microsoft.Compute/virtualMachines
Location                 : westus
LicenseType              : Windows_Server
```

Tento výstup má na rozdíl od tohoto virtuálního počítače nasazeného bez Zvýhodněné hybridní využití Azure pro licencování Windows serveru:
```powershell
Type                     : Microsoft.Compute/virtualMachines
Location                 : westus
LicenseType              :
```

### <a name="cli"></a>Rozhraní příkazového řádku
```azurecli
az vm get-instance-view -g MyResourceGroup -n MyVM --query "[?licenseType=='Windows_Server']" -o table
```

> [!NOTE]
> Změna typu licence na virtuálním počítači nezpůsobí, že se systém restartuje nebo nezpůsobí interuption služby. Je to jenom příznak licencování metadat.
>

## <a name="list-all-vms-with-azure-hybrid-benefit-for-windows-server-in-a-subscription"></a>Zobrazí seznam všech virtuálních počítačů s Zvýhodněné hybridní využití Azure pro Windows Server v předplatném.
Pokud chcete zobrazit a spočítat všechny virtuální počítače nasazené pomocí Zvýhodněné hybridní využití Azure pro Windows Server, můžete z předplatného spustit tento příkaz:

### <a name="portal"></a>Portál
V okně virtuálního počítače nebo prostředku Virtual Machine Scale Sets můžete zobrazit seznam všech virtuálních počítačů a typu licencí, a to tak, že nakonfigurujete sloupec tabulka tak, aby zahrnoval "Zvýhodněné hybridní využití Azure". Nastavení virtuálního počítače může být buď ve stavu povoleno, není povoleno nebo není podporováno.

### <a name="powershell"></a>PowerShell
```powershell
$vms = Get-AzVM
$vms | ?{$_.LicenseType -like "Windows_Server"} | select ResourceGroupName, Name, LicenseType
```

### <a name="cli"></a>Rozhraní příkazového řádku
```azurecli
az vm list --query "[?licenseType=='Windows_Server']" -o table
```

## <a name="deploy-a-virtual-machine-scale-set-with-azure-hybrid-benefit-for-windows-server"></a>Nasazení sady škálování virtuálního počítače s Zvýhodněné hybridní využití Azure pro Windows Server
V rámci Správce prostředků šablon sady škálování virtuálního počítače `licenseType` musí být ve vlastnosti VirtualMachineProfile zadaný další parametr. To můžete provést během vytváření nebo aktualizace sady škálování prostřednictvím šablony ARM, PowerShellu, rozhraní příkazového řádku Azure nebo REST.

Následující příklad používá šablonu ARM s imagí Windows serveru 2016 Datacenter:
```json
"virtualMachineProfile": {
    "storageProfile": {
        "osDisk": {
            "createOption": "FromImage"
        },
        "imageReference": {
            "publisher": "MicrosoftWindowsServer",
            "offer": "WindowsServer",
            "sku": "2016-Datacenter",
            "version": "latest"
        }
    },
    "licenseType": "Windows_Server",
    "osProfile": {
            "computerNamePrefix": "[parameters('vmssName')]",
            "adminUsername": "[parameters('adminUsername')]",
            "adminPassword": "[parameters('adminPassword')]"
    }
```
Můžete si taky přečíst další informace o tom, jak [Upravit sadu škálování virtuálního počítače](../../virtual-machine-scale-sets/virtual-machine-scale-sets-upgrade-scale-set.md) pro další způsoby aktualizace sady škálování.

## <a name="next-steps"></a>Další kroky
- Přečtěte si další informace o [tom, jak ušetřit peníze pomocí zvýhodněné hybridní využití Azure](https://azure.microsoft.com/pricing/hybrid-use-benefit/)
- Přečtěte si další informace o [nejčastějších dotazech k zvýhodněné hybridní využití Azure](https://azure.microsoft.com/pricing/hybrid-use-benefit/faq/)
- Přečtěte si další informace o [zvýhodněné hybridní využití Azure s podrobnými pokyny pro licencování Windows serveru](/windows-server/get-started/azure-hybrid-benefit)
- Přečtěte si další informace o [zvýhodněné hybridní využití Azure pro Windows Server a Azure Site Recovery díky migraci aplikací do Azure ještě více cenově výhodné](https://azure.microsoft.com/blog/hybrid-use-benefit-migration-with-asr/) .
- Další informace o [Windows 10 v Azure s oprávněním pro hostování s více klienty](./windows-desktop-multitenant-hosting-deployment.md)
- Další informace o [používání šablon Správce prostředků](../../azure-resource-manager/management/overview.md)
