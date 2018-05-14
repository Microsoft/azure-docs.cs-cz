---
title: Výhody Azure hybridní pro systém Windows Server | Microsoft Docs
description: Zjistěte, jak chcete maximalizovat své výhody Windows Software Assurance a dovést místní licencí do Azure
services: virtual-machines-windows
documentationcenter: ''
author: xujing
manager: jeconnoc
editor: ''
ms.assetid: 332583b6-15a3-4efb-80c3-9082587828b0
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 4/22/2018
ms.author: xujing-ms
ms.openlocfilehash: a4b0baefc8c3c839a06d6540e57b34657138c8ff
ms.sourcegitcommit: c52123364e2ba086722bc860f2972642115316ef
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/11/2018
---
# <a name="azure-hybrid-benefit-for-windows-server"></a>Azure Hybrid Benefit pro Windows Server
Pro zákazníky s programu Software Assurance umožňuje Azure hybridní výhody pro Windows Server používat své místní licence systému Windows Server a spustit virtuální počítače s Windows v Azure s malými náklady. Azure hybridní výhody pro Windows Server můžete použít k nasazení nových virtuálních počítačů s operačním systémem Windows. Tento článek přejde přes kroky na tom, jak nasadit nové virtuální počítače s Azure hybridní výhody pro Windows Server a jak můžete aktualizovat existující spuštěných virtuálních počítačů. Další informace o Azure hybridní výhody pro Windows Server najdete v části úspory licencování a náklady [licencování stránky Azure hybridní výhody pro Windows Server](https://azure.microsoft.com/pricing/hybrid-use-benefit/).

> [!Important]
> Každá dvouprocesorová licence nebo každá sada 16jádrových licencí má nárok na dvě instance s nejvýše 8 jádry, nebo na jednu instanci s nejvýše 16 jádry. Azure Hybrid Benefit pro licence na edici Standard je možné využít jenom jednou, a to místně nebo v Azure. Výhody Datacenter Edition umožňují současné využití v místním prostředí i v Azure.
>

> [!Important]
> Všechny virtuální počítače se systémem operačního systému Windows Server pomocí Azure hybridní výhody pro Windows Server jsou nyní podporovány ve všech oblastech, včetně virtuálních počítačů s další software, jako je SQL Server nebo software jiného výrobce marketplace. 
>

> [!NOTE]
> Pro klasické virtuální počítače je podporováno pouze nasazení nového virtuálního počítače z místní vlastních bitových kopií. Abyste mohli využívat funkce podporována v tomto článku, je nejprve nutné migrovat klasické virtuální počítače modelu Resource Manager.
>


## <a name="ways-to-use-azure-hybrid-benefit-for-windows-server"></a>Způsoby použití Azure hybridní výhody pro Windows Server
Existuje několik způsobů, jak používat virtuální počítače s Windows s využitím hybridní Azure:

1. Můžete nasadit virtuální počítače z jednoho ze zadaných [bitové kopie systému Windows Server v Azure Marketplace](#https://azuremarketplace.microsoft.com/en-us/marketplace/apps/Microsoft.WindowsServer?tab=Overview)
2. Můžete nahrát vlastní virtuální počítač a nasadit pomocí šablony Resource Manageru nebo Azure PowerShell
3. Můžete přepínat a převést stávající virtuální počítač mezi systémem s Azure hybridní Benefit nebo platit náklady na vyžádání pro systém Windows Server
4. Můžete taky použít Azure hybridní výhody pro Windows Server na také sad škálování virtuálního počítače


## <a name="create-a-vm-with-azure-hybrid-benefit-for-windows-server"></a>Vytvoření virtuálního počítače s Azure hybridní výhody pro systém Windows Server
Všechny bitové kopie operačního systému Windows Server na základě jsou podporovány pro Azure hybridní výhody pro Windows Server. Můžete použít Image podpory platformy Azure nebo nahrát vlastní vlastních bitových kopií systému Windows Server. 

### <a name="portal"></a>Portál
Pokud chcete vytvořit virtuální počítač s Azure hybridní výhody pro Windows Server, použijte přepínač v části "Ušetřit peníze".

### <a name="powershell"></a>PowerShell
```powershell
New-AzureRmVm `
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
V rámci šablony Resource Manageru, další parametr `licenseType` musí být zadán. Další informace o [vytváření šablon Azure Resource Manager](../../resource-group-authoring-templates.md)
```json
"properties": {  
   "licenseType": "Windows_Server",
   "hardwareProfile": {
        "vmSize": "[variables('vmSize')]"
   }
```

## <a name="convert-an-existing-vm-using-azure-hybrid-benefit-for-windows-server"></a>Převést stávající virtuální počítač pomocí Azure hybridní výhody pro Windows Server
Pokud máte existující virtuální počítač, který chcete převést na využít výhod programu Azure hybridní výhody pro Windows Server, můžete aktualizovat typ licence Virtuálního počítače takto:

### <a name="portal"></a>Portál
Z portálu okno virtuálních počítačů můžete aktualizovat virtuální počítač používat Azure hybridní těžit výběrem možnosti "Konfigurace" a přepněte možnost "Azure hybridní těžit"

### <a name="powershell"></a>PowerShell
- Převést stávající virtuální počítače Windows serveru na Azure hybridní výhody pro Windows Server

    ```powershell
    $vm = Get-AzureRmVM -ResourceGroup "rg-name" -Name "vm-name"
    $vm.LicenseType = "Windows_Server"
    Update-AzureRmVM -ResourceGroupName rg-name -VM $vm
    ```
    
- Převod virtuálních počítačů Windows serveru s průběžnými platbami výhody zpět

    ```powershell
    $vm = Get-AzureRmVM -ResourceGroup "rg-name" -Name "vm-name"
    $vm.LicenseType = "None"
    Update-AzureRmVM -ResourceGroupName rg-name -VM $vm
    ```
    
### <a name="cli"></a>Rozhraní příkazového řádku
- Převést stávající virtuální počítače Windows serveru na Azure hybridní výhody pro Windows Server

    ```azurecli
    az vm update --resource-group myResourceGroup --name myVM --set licenseType=Windows_Server
    ```
    
### <a name="how-to-verify-your-vm-is-utilizing-the-licensing-benefit"></a>Postup ověření virtuálního počítače je použití licencování výhody
Po nasazení virtuálního počítače prostřednictvím Powershellu, šablony Resource Manageru nebo portálu, můžete ověřit nastavení v následujících metod.

### <a name="portal"></a>Portál
Z portálu okno virtuálních počítačů můžete zobrazit přepínač pro Azure hybridní výhody pro Windows Server, vyberte "Konfigurace".

### <a name="powershell"></a>PowerShell
Následující příklad ukazuje typ licence pro jeden virtuální počítač
```powershell
Get-AzureRmVM -ResourceGroup "myResourceGroup" -Name "myVM"
```

Výstup:
```powershell
Type                     : Microsoft.Compute/virtualMachines
Location                 : westus
LicenseType              : Windows_Server
```

Tento výstup uvádí vedle sebe s následující virtuální počítač nasadit bez licencování Azure hybridní výhody pro Windows Server:
```powershell
Type                     : Microsoft.Compute/virtualMachines
Location                 : westus
LicenseType              :
```

### <a name="cli"></a>Rozhraní příkazového řádku
```azurecli
az vm get-instance-view -g MyResourceGroup -n MyVM --query '[?licenseType==Windows_Server]' -o table
```

## <a name="list-all-vms-with-azure-hybrid-benefit-for-windows-server-in-a-subscription"></a>Zobrazí seznam všech virtuálních počítačů s Azure hybridní výhody pro Windows Server v předplatném.
Pokud chcete zobrazit a počet všechny virtuální počítače nasazené se službou Azure hybridní výhody pro Windows Server, můžete spustit následující příkaz ze svého předplatného:

### <a name="portal"></a>Portál
Z virtuálního počítače nebo virtuálního počítače škálování nastaví okna prostředků můžete zobrazit seznam všechny virtuální počítače a licencování typ nakonfigurováním sloupec tabulky zahrnout "Benefit hybridní Azure". Nastavení virtuálního počítače může být buď v "Povoleno", "Povoleno" nebo "Není podporováno" stavu.

### <a name="powershell"></a>PowerShell
```powershell
$vms = Get-AzureRMVM 
$vms | ?{$_.LicenseType -like "Windows_Server"} | select ResourceGroupName, Name, LicenseType
```

### <a name="cli"></a>Rozhraní příkazového řádku
```azurecli
az vm list --query '[?licenseType==Windows_Server]' -o table
```

## <a name="deploy-a-virtual-machine-scale-set-with-azure-hybrid-benefit-for-windows-server"></a>Nasazení Škálovací sadu virtuálních počítačů s Azure hybridní výhody pro systém Windows Server
V rámci virtuálního počítače sady škálování šablony Resource Manageru, další parametr `licenseType` je třeba zadat v rámci vaší VirtualMachineProfile vlastnost. Můžete to udělat během vytváření nebo aktualizace pro vaše škálování nastavit pomocí šablony ARM, prostředí Powershell, rozhraní příkazového řádku Azure nebo REST.

Následující příklad používá šablonu ARM k bitové kopii systému Windows Server 2016 Datacenter:
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
Můžete také další informace o tom, jak [upravit škálovací sadu virtuálních počítačů](../../virtual-machine-scale-sets/virtual-machine-scale-sets-upgrade-scale-set.md) pro další způsoby, jak aktualizovat vaše škálování nastavit.

## <a name="next-steps"></a>Další postup
- Další informace o [jak ušetřit peníze s výhodou hybridní Azure](https://azure.microsoft.com/pricing/hybrid-use-benefit/)
- Další informace o [nejčastější dotazy k Azure hybridní výhody](https://azure.microsoft.com/pricing/hybrid-use-benefit/faq/)
- Další informace o [Azure hybridní výhody pro Windows Server licencování podrobné pokyny](https://docs.microsoft.com/windows-server/get-started/azure-hybrid-benefit)
- Další informace o [Azure hybridní výhody pro Windows Server a Azure Site Recovery zkontrolujte migrace aplikací do Azure i více nákladově efektivní](https://azure.microsoft.com/blog/hybrid-use-benefit-migration-with-asr/)
- Další informace o [Windows 10 v Azure s víceklientské hostování vpravo](https://docs.microsoft.com/azure/virtual-machines/windows/windows-desktop-multitenant-hosting-deployment)
- Další informace o [šablon pomocí Resource Manageru](../../azure-resource-manager/resource-group-overview.md)
