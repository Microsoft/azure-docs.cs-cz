---
title: Program Azure Hybrid Benefit pro Windows Server | Dokumentace Microsoftu
description: Zjistěte, jak maximalizovat své výhody programu Software Assurance pro Windows a využít místní licence do Azure
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
ms.openlocfilehash: 1b893518ec67d6c11c2bcee3acc52c01cd573f86
ms.sourcegitcommit: cc4fdd6f0f12b44c244abc7f6bc4b181a2d05302
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2018
ms.locfileid: "47091562"
---
# <a name="azure-hybrid-benefit-for-windows-server"></a>Azure Hybrid Benefit pro Windows Server
Pro zákazníky s programem Software Assurance programu Azure Hybrid Benefit pro Windows Server vám umožní využít vaše licence v místním systému Windows Server a spusťte Windows virtual machines v Azure s nižšími náklady. Azure Hybrid Benefit pro Windows Server můžete použít k nasazení nových virtuálních počítačů s operačním systémem Windows. Tento článek prochází přes všechny kroky v tom, jak nasadit nové virtuální počítače s programem Azure Hybrid Benefit pro Windows Server a jak můžete aktualizovat existující spouštění virtuálních počítačů. Další informace o programu Azure Hybrid Benefit pro Windows Server najdete v článku úspory licencování a náklady [stránky licencování programu Azure Hybrid Benefit pro Windows Server](https://azure.microsoft.com/pricing/hybrid-use-benefit/).

> [!Important]
> Každá dvouprocesorová licence nebo každá sada 16jádrových licencí má nárok na dvě instance s nejvýše 8 jádry, nebo na jednu instanci s nejvýše 16 jádry. Azure Hybrid Benefit pro licence na edici Standard je možné využít jenom jednou, a to místně nebo v Azure. Výhody Datacenter Edition umožňují současné využití v místním prostředí i v Azure.
>

> [!Important]
> Všechny virtuální počítače, Server s operačním systémem Windows pomocí programu Azure Hybrid Benefit pro Windows Server jsou nyní podporovány ve všech oblastech, včetně virtuálních počítačů s další software, jako je SQL Server nebo softwaru jiných výrobců marketplace. 
>

> [!NOTE]
> Pro klasické virtuální počítače je podporováno pouze nasazení nového virtuálního počítače z vlastních imagí v místním prostředí. Abyste mohli využívat možnosti podporované v tomto článku, musí nejprve migrovat klasické virtuální počítače na model Resource Manager.
>


## <a name="ways-to-use-azure-hybrid-benefit-for-windows-server"></a>Možnosti využití programu Azure Hybrid Benefit pro Windows Server
Využijte virtuální počítače s Windows s programem Azure Hybrid Benefit několika způsoby:

1. Můžete nasazovat virtuální počítače z jednoho ze zadaných [Image Windows serveru na Azure Marketplace](# https://azuremarketplace.microsoft.com/en-us/marketplace/apps/Microsoft.WindowsServer?tab=Overview)
2. Můžete nahrát vlastní virtuální počítač a nasazení pomocí šablony Resource Manageru nebo Azure Powershellu
3. Přepnout, převod stávajícího virtuálního počítače mezi systémem s programem Azure Hybrid Benefit a platit náklady na vyžádání pro systém Windows Server
4. Můžete také použít Azure Hybrid Benefit pro Windows Server na škálovací sadu a virtuálních počítačů


## <a name="create-a-vm-with-azure-hybrid-benefit-for-windows-server"></a>Vytvoření virtuálního počítače s programem Azure Hybrid Benefit pro Windows Server
Všechny Image podle operačního systému Windows Server jsou podporovány pro programu Azure Hybrid Benefit pro Windows Server. Můžete použít Image platformy Azure podpory nebo nahráváte vlastní Image Windows serveru. 

### <a name="portal"></a>Portál
Vytvoření virtuálního počítače s programem Azure Hybrid Benefit pro Windows Server, použijte přepínač v části "Ušetřit".

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
V rámci své šablony Resource Manageru, další parametr `licenseType` musí být zadán. Další informace o [Tvorba šablon Azure Resource Manageru](../../resource-group-authoring-templates.md)
```json
"properties": {  
   "licenseType": "Windows_Server",
   "hardwareProfile": {
        "vmSize": "[variables('vmSize')]"
   }
```

## <a name="convert-an-existing-vm-using-azure-hybrid-benefit-for-windows-server"></a>Převod existujícího virtuálního počítače pomocí programu Azure Hybrid Benefit pro Windows Server
Pokud máte existující virtuální počítač, který se má převést na využít výhod programu Azure Hybrid Benefit pro Windows Server, můžete aktualizovat typ licence virtuálního počítače a podle pokynů uvedených níže.

> [!NOTE]
> Změna typu licence na virtuálním počítači nezpůsobí systém restartovat nebo způsobit interuption služby.  Je jednoduše aktualizaci metadat příznak.
> 

### <a name="portal"></a>Portál
Z portálu okna virtuálního počítače můžete aktualizovat virtuální počítač používat zvýhodněné hybridní využití Azure tak, že vyberete možnost "Konfigurace" a přepněte možnost "Azure hybrid benefit"

### <a name="powershell"></a>PowerShell
- Převod existujících virtuálních počítačů Windows serveru na Azure Hybrid Benefit pro Windows Server

    ```powershell
    $vm = Get-AzureRmVM -ResourceGroup "rg-name" -Name "vm-name"
    $vm.LicenseType = "Windows_Server"
    Update-AzureRmVM -ResourceGroupName rg-name -VM $vm
    ```
    
- Převést virtuální počítače s Windows serverem díky výhodě zpět na průběžné platby

    ```powershell
    $vm = Get-AzureRmVM -ResourceGroup "rg-name" -Name "vm-name"
    $vm.LicenseType = "None"
    Update-AzureRmVM -ResourceGroupName rg-name -VM $vm
    ```
    
### <a name="cli"></a>Rozhraní příkazového řádku
- Převod existujících virtuálních počítačů Windows serveru na Azure Hybrid Benefit pro Windows Server

    ```azurecli
    az vm update --resource-group myResourceGroup --name myVM --set licenseType=Windows_Server
    ```
    
### <a name="how-to-verify-your-vm-is-utilizing-the-licensing-benefit"></a>Jak ověřit váš virtuální počítač využívá výhodu licencování naplno
Jakmile nasadíte vašeho virtuálního počítače přes PowerShell, šablony Resource Manageru nebo webu portal, můžete ověřit nastavení v těchto metod.

### <a name="portal"></a>Portál
Z portálu okna virtuálního počítače můžete zobrazit tento přepínač k programu Azure Hybrid Benefit pro Windows Server tak, že vyberete kartu "Konfigurace".

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

Tento výstup uvádí vedle sebe s následující virtuální počítač nasadit bez licencování programu Azure Hybrid Benefit pro Windows Server:
```powershell
Type                     : Microsoft.Compute/virtualMachines
Location                 : westus
LicenseType              :
```

### <a name="cli"></a>Rozhraní příkazového řádku
```azurecli
az vm get-instance-view -g MyResourceGroup -n MyVM --query '[?licenseType==Windows_Server]' -o table
```

## <a name="list-all-vms-with-azure-hybrid-benefit-for-windows-server-in-a-subscription"></a>Seznam všech virtuálních počítačích s programem Azure Hybrid Benefit pro Windows Server v rámci předplatného
Pokud chcete zobrazit a spočítat všechny virtuální počítače nasazené pomocí programu Azure Hybrid Benefit pro Windows Server, spuštěním následujícího příkazu z předplatného:

### <a name="portal"></a>Portál
Z virtuálního počítače nebo virtuálního počítače škálovací sady prostředků okna můžete zobrazit seznam všech virtuálních počítačů a licencování typu tím, že nakonfigurujete sloupec tabulky zahrnout "Zvýhodněné hybridní využití Azure". Nastavení virtuálního počítače může být buď v "Povoleno", "Povoleno" nebo "Není podporován" stavu.

### <a name="powershell"></a>PowerShell
```powershell
$vms = Get-AzureRMVM 
$vms | ?{$_.LicenseType -like "Windows_Server"} | select ResourceGroupName, Name, LicenseType
```

### <a name="cli"></a>Rozhraní příkazového řádku
```azurecli
az vm list --query '[?licenseType==Windows_Server]' -o table
```

## <a name="deploy-a-virtual-machine-scale-set-with-azure-hybrid-benefit-for-windows-server"></a>Nasazení Škálovací sady virtuálních počítačů s programem Azure Hybrid Benefit pro Windows Server
V rámci virtuálního počítače škálovací sady šablon Resource Manageru, další parametr `licenseType` musí být zadán v rámci vaší VirtualMachineProfile vlastnosti. Můžete to provést během vytváření nebo aktualizace pro svou škálovací sadu pomocí šablony ARM, Powershellu, rozhraní příkazového řádku Azure nebo REST.

Šablony ARM pomocí image systému Windows Server 2016 Datacenter v následujícím příkladu:
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
Můžete také další informace o tom, jak [úprava škálovací sady virtuálních počítačů](../../virtual-machine-scale-sets/virtual-machine-scale-sets-upgrade-scale-set.md) další způsoby, jak aktualizovat škálovací nastavit.

## <a name="next-steps"></a>Další postup
- Další informace o [jak ušetřit s programem Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-use-benefit/)
- Další informace o [nejčastější dotazy k programu Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-use-benefit/faq/)
- Další informace o [Azure Hybrid Benefit pro Windows Server podrobné doprovodné materiály k licencování](https://docs.microsoft.com/windows-server/get-started/azure-hybrid-benefit)
- Další informace o [programu Azure Hybrid Benefit pro Windows Server a Azure Site Recovery proveďte migraci aplikací do Azure ještě výhodnější](https://azure.microsoft.com/blog/hybrid-use-benefit-migration-with-asr/)
- Další informace o [Windows 10 v Azure s právem hostování více klientů](https://docs.microsoft.com/azure/virtual-machines/windows/windows-desktop-multitenant-hosting-deployment)
- Další informace o [šablon pomocí Resource Manageru](../../azure-resource-manager/resource-group-overview.md)
