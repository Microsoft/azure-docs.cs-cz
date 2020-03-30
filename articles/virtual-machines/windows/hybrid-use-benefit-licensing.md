---
title: Zvýhodněné hybridní využití Azure pro Windows Server
description: Zjistěte, jak maximalizovat výhody windows software assurance a přenést místní licence do Azure
services: virtual-machines-windows
documentationcenter: ''
author: xujing-ms
manager: gwallace
editor: ''
ms.assetid: 332583b6-15a3-4efb-80c3-9082587828b0
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 4/22/2018
ms.author: xujing
ms.openlocfilehash: 470e38c21a250273216f93eb38a5334a4bb581e7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77911779"
---
# <a name="azure-hybrid-benefit-for-windows-server"></a>Zvýhodněné hybridní využití Azure pro Windows Server
Pro zákazníky se softwarem Assurance vám hybridní výhoda Azure pro Windows Server umožňuje používat místní licence Windows Serveru a spouštět virtuální počítače Windows v Azure za sníženou cenu. Hybridní výhoda Azure pro Windows Server můžete použít k nasazení nových virtuálních počítačů s os windows. Tento článek popisuje postup nasazení nových virtuálních počítačů s hybridní výhodou Azure pro Windows Server a jak můžete aktualizovat stávající spuštěné virtuální počítače. Další informace o licencování hybridních výhod Azure pro Windows Server a úspoře nákladů najdete na [stránce licencování Azure Hybrid Benefit for Windows Server](https://azure.microsoft.com/pricing/hybrid-use-benefit/).

Každá licence se 2 procesory nebo každá sada 16jádrových licencí má nárok na dvě instance až 8 jader nebo na jednu instanci až 16 jader. Licence Azure Hybrid Benefit for Standard Edition lze použít pouze jednou místně nebo v Azure. Výhody datacenter edition umožňují současné využití jak v místním prostředí, tak v Azure.

Použití hybridnívýhody Azure pro Windows Server u všech virtuálních počítače se systémem Windows Server OS se teď podporuje ve všech oblastech, včetně virtuálních počítačů s dalším softwarem, jako je SQL Server nebo software marketplace jiných výrobců. 


## <a name="classic-vms"></a>Klasické virtuální počítače

Pro klasické virtuální počítačky je podporováno jenom nasazení nového virtuálního počítače z místních vlastních irek. Chcete-li využít možnosti podporované v tomto článku, musíte nejprve migrovat klasické virtuální počítače do modelu Resource Manager.

[!INCLUDE [classic-vm-deprecation](../../../includes/classic-vm-deprecation.md)]
 

## <a name="ways-to-use-azure-hybrid-benefit-for-windows-server"></a>Způsoby využití hybridních výhod Azure pro Windows Server
Virtuální počítače s Windows s hybridní výhodou Azure můžete používat jen několik způsobů:

1. Virtuální počítače můžete nasadit z jedné z dodaných ibií Windows Serveru na Azure Marketplace.
2. Můžete nahrát vlastní virtuální počítač a nasadit pomocí šablony Správce prostředků nebo Azure PowerShellu
3. Můžete přepínat a převádět stávající virtuální počítač mezi spuštěním s hybridní výhodou Azure nebo platit náklady na vyžádání za Windows Server
4. Hybridní výhody Azure pro Windows Server můžete použít také na škálovací sadě virtuálních strojů.


## <a name="create-a-vm-with-azure-hybrid-benefit-for-windows-server"></a>Vytvoření virtuálního počítače s hybridní výhodou Azure pro Windows Server
Všechny bitové kopie založené na operačním systému Windows Server jsou podporované pro hybridní výhody Azure pro Windows Server. Můžete použít image podpory platformy Azure nebo nahrát vlastní image Windows Serveru. 

### <a name="portal"></a>Portál
Pokud chcete vytvořit virtuální počítač s hybridní výhodou Azure pro Windows Server, použijte přepínač v části Ušetřete peníze.

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
V šablonách Správce prostředků musí `licenseType` být zadán další parametr. Další informace o [vytváření šablon Azure Resource Manageru](../../resource-group-authoring-templates.md)
```json
"properties": {
    "licenseType": "Windows_Server",
    "hardwareProfile": {
        "vmSize": "[variables('vmSize')]"
    }
```

## <a name="convert-an-existing-vm-using-azure-hybrid-benefit-for-windows-server"></a>Převod existujícího virtuálního počítače pomocí hybridní výhody Azure pro Windows Server
Pokud máte existující virtuální počítač, který chcete převést, abyste využili výhod hybridní výhody Azure pro Windows Server, můžete aktualizovat typ licence virtuálního počítače podle následujících pokynů.

> [!NOTE]
> Změna typu licence na virtuálním počítači nezpůsobí restartování systému nebo způsobit narušení služby.  Je to jednoduše aktualizace příznaku metadat.
> 

### <a name="portal"></a>Portál
Z okna virtuálního počítače portálu můžete aktualizovat virtuální počítač tak, aby používal hybridní výhody Azure, výběrem možnosti Konfigurace a přepnutím možnosti "Hybridní výhoda Azure"

### <a name="powershell"></a>PowerShell
- Převod stávajících virtuálních počítačů Windows Server na hybridní výhody Azure pro Windows Server

    ```powershell
    $vm = Get-AzVM -ResourceGroup "rg-name" -Name "vm-name"
    $vm.LicenseType = "Windows_Server"
    Update-AzVM -ResourceGroupName rg-name -VM $vm
    ```
    
- Převod virtuálních připojení Windows Server s výhodou zpět na průběžně placené

    ```powershell
    $vm = Get-AzVM -ResourceGroup "rg-name" -Name "vm-name"
    $vm.LicenseType = "None"
    Update-AzVM -ResourceGroupName rg-name -VM $vm
    ```
    
### <a name="cli"></a>Rozhraní příkazového řádku
- Převod stávajících virtuálních počítačů Windows Server na hybridní výhody Azure pro Windows Server

    ```azurecli
    az vm update --resource-group myResourceGroup --name myVM --set licenseType=Windows_Server
    ```

### <a name="how-to-verify-your-vm-is-utilizing-the-licensing-benefit"></a>Jak ověřit, že váš virtuální počítač využívá výhody licencování
Po nasazení virtuálního počítače prostřednictvím prostředí PowerShell, šablony Správce prostředků nebo portálu můžete ověřit nastavení v následujících metodách.

### <a name="portal"></a>Portál
Na okně portal virtuálního počítače můžete zobrazit přepínač pro hybridní výhody Azure pro Windows Server výběrem karty Konfigurace.

### <a name="powershell"></a>PowerShell
Následující příklad ukazuje typ licence pro jeden virtuální virtuální ms
```powershell
Get-AzVM -ResourceGroup "myResourceGroup" -Name "myVM"
```

Výstup:
```powershell
Type                     : Microsoft.Compute/virtualMachines
Location                 : westus
LicenseType              : Windows_Server
```

Tento výstup kontrastuje s následujícím virtuálním počítačem nasazeným bez licencování Hybridní výhoda Azure pro Windows Server:
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
> Změna typu licence na virtuálním počítači nezpůsobí restartování systému nebo způsobit narušení služby. Jedná se pouze o příznak licencování metadat.
>

## <a name="list-all-vms-with-azure-hybrid-benefit-for-windows-server-in-a-subscription"></a>Seznam všech virtuálních počítačů s hybridní výhodou Azure pro Windows Server v předplatném
Pokud chcete zobrazit a spočítat všechny virtuální počítače nasazené s hybridní výhodou Azure pro Windows Server, můžete z předplatného spustit následující příkaz:

### <a name="portal"></a>Portál
Z okna prostředků virtuálního počítače nebo virtuálního počítače můžete zobrazit seznam všech virtuálních počítačů a typu licencování tak, že nakonfigurujete sloupec tabulky tak, aby zahrnoval "Hybridní výhody Azure". Nastavení virtuálního připojení může být buď ve stavu "Povoleno", "Není povoleno" nebo "Není podporováno".

### <a name="powershell"></a>PowerShell
```powershell
$vms = Get-AzVM
$vms | ?{$_.LicenseType -like "Windows_Server"} | select ResourceGroupName, Name, LicenseType
```

### <a name="cli"></a>Rozhraní příkazového řádku
```azurecli
az vm list --query "[?licenseType=='Windows_Server']" -o table
```

## <a name="deploy-a-virtual-machine-scale-set-with-azure-hybrid-benefit-for-windows-server"></a>Nasazení škálovací sady virtuálních strojů s hybridnívýhodou Azure pro Windows Server
V rámci šablon Správce prostředků škálovací sady `licenseType` virtuálních strojů musí být v rámci vlastnosti VirtualMachineProfile zadán další parametr. Můžete to provést během vytváření nebo aktualizace škálovací sady prostřednictvím šablony ARM, PowerShellu, Azure CLI nebo REST.

Následující příklad používá šablonu ARM s bitovou fotkou datového centra Windows Serveru 2016:
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
Můžete také získat další informace o tom, jak [upravit škálovací sadu virtuálních strojů](../../virtual-machine-scale-sets/virtual-machine-scale-sets-upgrade-scale-set.md) pro další způsoby aktualizace škálovací sady.

## <a name="next-steps"></a>Další kroky
- Přečtěte si další informace o [tom, jak ušetřit peníze s hybridní výhodou Azure](https://azure.microsoft.com/pricing/hybrid-use-benefit/)
- Další informace o [nejčastějších dotazech týkajících se hybridní výhody Azure](https://azure.microsoft.com/pricing/hybrid-use-benefit/faq/)
- Další informace o [hybridnívýhodě Azure pro licencování Windows Serveru jsou podrobné pokyny](https://docs.microsoft.com/windows-server/get-started/azure-hybrid-benefit)
- Další informace o [hybridních výhodách Azure pro Windows Server a Azure Site Recovery dělají migraci aplikací do Azure ještě hospodárnější](https://azure.microsoft.com/blog/hybrid-use-benefit-migration-with-asr/)
- Další informace o [Windows 10 v Azure s víceklientským hostingovým právem](https://docs.microsoft.com/azure/virtual-machines/windows/windows-desktop-multitenant-hosting-deployment)
- Další informace o [používání šablon Správce prostředků](../../azure-resource-manager/management/overview.md)
