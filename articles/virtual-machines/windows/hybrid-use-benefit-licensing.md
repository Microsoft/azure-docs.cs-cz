---
title: Výhody Azure hybridní pro systém Windows Server | Microsoft Docs
description: Zjistěte, jak chcete maximalizovat své výhody Windows Software Assurance a dovést místní licencí do Azure
services: virtual-machines-windows
documentationcenter: ''
author: kmouss
manager: jeconnoc
editor: ''
ms.assetid: 332583b6-15a3-4efb-80c3-9082587828b0
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 11/22/2017
ms.author: kmouss
ms.openlocfilehash: f445a2c77b14477ea2ef4ff7722183d641275f08
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2018
---
# <a name="azure-hybrid-benefit-for-windows-server"></a>Azure Hybrid Benefit pro Windows Server
Pro zákazníky s programu Software Assurance umožňuje Azure hybridní výhody pro Windows Server používat své místní licence systému Windows Server a spustit virtuální počítače s Windows v Azure s malými náklady. Azure hybridní výhody pro Windows Server můžete použít k nasazení nových virtuálních počítačů z libovolného Azure podporovaný image platformy systému Windows Server nebo vlastních bitových kopií systému Windows. Tento článek přejde přes kroky na tom, jak nasadit nové virtuální počítače s Azure hybridní výhody pro Windows Server a jak můžete aktualizovat existující spuštěných virtuálních počítačů. Další informace o Azure hybridní výhody pro Windows Server najdete v části úspory licencování a náklady [licencování stránky Azure hybridní výhody pro Windows Server](https://azure.microsoft.com/pricing/hybrid-use-benefit/).

> [!IMPORTANT]
> Od verze 11/9/2017 byl vyřazen starší verze systému Windows Server [ROZBOČOVAČE] bitové kopie, které byly publikované pro zákazníky s smlouvy Enterprise na webu Azure Marketplace, použijte standardní Windows Server s možností "Uložit peníze" na portálu Azure hybridní Benefit pro Windows Server. Další informace naleznete v to [článku.](https://support.microsoft.com/en-us/help/4036360/retirement-azure-hybrid-use-benefit-images-for-ea-subscriptions)
>

> [!NOTE]
> Pomocí Azure hybridní výhody pro Windows Server virtuálních počítačů, které budou účtovat další software, jako je například SQL Server ani v žádné z bitové kopie třetích stran marketplace je se nasazuje. Pokud se například zobrazí 409 Chyba: při změně hodnoty vlastnosti není povolena, LicenseType'; pak se pokoušíte převést nebo nasadit nové virtuální počítače Windows serveru, který má další náklady, software, který nemusí být podporována v této oblasti. Stejné, pokud se pokusíte vypadat pro konfigurace portálu možnost provést převod a neuvidí ho pro tento virtuální počítač.
>


> [!NOTE]
> Pro klasické virtuální počítače je podporováno pouze nasazení nového virtuálního počítače z místní vlastních bitových kopií. Abyste mohli využívat funkce podporována v tomto článku, je nejprve nutné migrovat klasické virtuální počítače modelu Resource Manager.
>


## <a name="ways-to-use-azure-hybrid-benefit-for-windows-server"></a>Způsoby použití Azure hybridní výhody pro Windows Server
Existuje několik způsobů, jak používat virtuální počítače s Windows s využitím hybridní Azure:

1. Můžete nasadit virtuální počítače z jednoho ze zadaných [bitové kopie systému Windows Server v Azure Marketplace](#https://azuremarketplace.microsoft.com/en-us/marketplace/apps/Microsoft.WindowsServer?tab=Overview)
2. Můžete [nahrát vlastní virtuální](#upload-a-windows-vhd) a [nasazení pomocí šablony Resource Manageru](#deploy-a-vm-via-resource-manager) nebo [prostředí Azure PowerShell](#detailed-powershell-deployment-walkthrough)
3. Můžete přepínat a převést stávající virtuální počítač mezi systémem s Azure hybridní Benefit nebo platit náklady na vyžádání pro systém Windows Server
4. Můžete taky nasadit nové škálování virtuálních počítačů s Azure hybridní výhody pro Windows Server

> [!NOTE]
> Převod existující škálování virtuálních počítačů, na nastavení používá Azure hybridní výhody pro Windows Server není podporován.
>

## <a name="deploy-a-vm-from-a-windows-server-marketplace-image"></a>Nasadit virtuální počítač z bitové kopie systému Windows Server Marketplace.
S Azure hybridní výhody pro Windows Server jsou povolené všechny bitové kopie systému Windows Server, které jsou k dispozici v Azure Marketplace. Například 2008SP1 systému Windows Server 2016, Windows Server 2012 R2, Windows Server 2012 a Windows Server a další. Tyto Image můžete použít k nasazení virtuálních počítačů přímo z portálu Azure, šablony Resource Manageru, prostředí Azure PowerShell nebo dalších sadách SDK.

Nasazením těchto bitových kopií přímo z portálu Azure. Pro použití v šablonách Resource Manageru a v prostředí Azure PowerShell zobrazí se seznam bitové kopie následujícím způsobem:

### <a name="powershell"></a>PowerShell
```powershell
Get-AzureRmVMImagesku -Location westus -PublisherName MicrosoftWindowsServer -Offer WindowsServer
```
Můžete provést kroky k [vytvoření virtuálního počítače s Windows pomocí prostředí PowerShell](#https://docs.microsoft.com/azure/virtual-machines/windows/quick-create-powershell?toc=%2Fazure%2Fvirtual-machines%2Fwindows%2Ftoc.json) a předejte LicenseType = "Windows_Server". Tato možnost umožňuje použít existující licence systému Windows Server na platformě Azure.

### <a name="portal"></a>Portál
Můžete provést kroky k [vytvoření virtuálního počítače s Windows pomocí portálu Azure](#https://docs.microsoft.com/azure/virtual-machines/windows/quick-create-portal) a vyberte možnost použít existující licence systému Windows Server.

## <a name="convert-an-existing-vm-using-azure-hybrid-benefit-for-windows-server"></a>Převést stávající virtuální počítač pomocí Azure hybridní výhody pro Windows Server
Pokud máte existující virtuální počítač, který chcete převést na využít výhod programu Azure hybridní výhody pro Windows Server, můžete aktualizovat typ licence Virtuálního počítače takto:

### <a name="convert-to-using-azure-hybrid-benefit-for-windows-server"></a>Převést na používání Azure hybridní výhody pro Windows Server
```powershell
$vm = Get-AzureRmVM -ResourceGroup "rg-name" -Name "vm-name"
$vm.LicenseType = "Windows_Server"
Update-AzureRmVM -ResourceGroupName rg-name -VM $vm
```

### <a name="convert-back-to-pay-as-you-go"></a>Převést zpátky na platit rovnou
```powershell
$vm = Get-AzureRmVM -ResourceGroup "rg-name" -Name "vm-name"
$vm.LicenseType = "None"
Update-AzureRmVM -ResourceGroupName rg-name -VM $vm
```

### <a name="portal"></a>Portál
Z portálu okno virtuálních počítačů můžete aktualizovat virtuální počítač používat Azure hybridní těžit výběrem možnosti "Konfigurace" a přepněte možnost "Azure hybridní těžit"

> [!NOTE]
> Pokud nevidíte možnost přepnout "Azure hybridní benefit" v části "Konfigurace", je to způsobeno převod zatím nepodporuje pro vybraný typ virtuálního počítače (třeba virtuální počítač vytvořen z vlastní image nebo obrázek, který obsahuje další placené software, jako je SQL Server nebo Azure Marketplace software třetích stran).
>

## <a name="upload-a-windows-server-vhd"></a>Nahrání virtuálního pevného disku serveru systému Windows
Pokud chcete nasadit virtuální počítač Windows serveru v Azure, musíte nejprve vytvořit virtuální pevný disk, který obsahuje vaše základní build Windows. Tento virtuální pevný disk musí být správně připravena pomocí nástroje Sysprep, před nahráním do Azure. Můžete [Další informace o požadavcích virtuálního pevného disku a procesu Sysprep](upload-generalized-managed.md) a [podpora nástroje Sysprep pro role serveru](https://msdn.microsoft.com/windows/hardware/commercialize/manufacture/desktop/sysprep-support-for-server-roles). Zálohování virtuálního počítače před spuštěním nástroje Sysprep. 

Jednou připravíte svůj disk VHD, nahrávat VHD do účtu úložiště Azure následujícím způsobem:

```powershell
Add-AzureRmVhd -ResourceGroupName "myResourceGroup" -LocalFilePath "C:\Path\To\myvhd.vhd" `
    -Destination "https://mystorageaccount.blob.core.windows.net/vhds/myvhd.vhd"
```

> [!NOTE]
> Microsoft SQL Server, SharePoint Server a Dynamics můžete také používat váš Software Assurance licencování. Musíte připravit bitovou kopii systému Windows Server tak, že instalace součásti vaší aplikace a poskytování aktivační kódy VLK odpovídajícím způsobem pak odesílání bitové kopie disku do Azure. Zkontrolujte v příslušné dokumentaci ke spuštění nástroje Sysprep s vaší aplikací, jako například [důležité informace týkající se instalace systému SQL Server pomocí nástroje Sysprep](https://msdn.microsoft.com/library/ee210754.aspx) nebo [sestavení SharePoint serveru 2016 referenční bitové kopie (Sysprep)](http://social.technet.microsoft.com/wiki/contents/articles/33789.build-a-sharepoint-server-2016-reference-image-sysprep.aspx).
>
>

Také další informace o [nahrávání virtuální pevný disk do Azure procesu](upload-generalized-managed.md#upload-the-vhd-to-your-storage-account)

## <a name="deploy-a-vm-via-resource-manager-template"></a>Nasazení virtuálního počítače prostřednictvím šablony Resource Manageru
V rámci šablony Resource Manageru, další parametr `licenseType` musí být zadán. Další informace o [vytváření šablon Azure Resource Manager](../../resource-group-authoring-templates.md). Až budete mít svůj disk VHD nahrán do Azure, upravte šablony Resource Manageru zahrnout typ licence jako součást poskytovatele výpočetních a nasazení vaší šablony jako za normálních okolností:

```json
"properties": {  
   "licenseType": "Windows_Server",
   "hardwareProfile": {
        "vmSize": "[variables('vmSize')]"
   }
```

## <a name="deploy-a-vm-via-powershell-quickstart"></a>Nasazení virtuálního počítače pomocí rychlého startu prostředí PowerShell
Při nasazování virtuálního počítače Windows serveru pomocí prostředí PowerShell, máte další parametr `-LicenseType`. Až budete mít svůj disk VHD nahrán do Azure, vytvoříte virtuální počítač pomocí `New-AzureRmVM` a zadejte typ licencování následujícím způsobem:

Windows Server:
```powershell
New-AzureRmVM -ResourceGroupName "myResourceGroup" -Location "West US" -VM $vm -LicenseType "Windows_Server"
```

Můžete si přečíst více popisný příručce na různé kroky, které [vytvořit virtuální počítač s Windows pomocí Resource Manageru a prostředí PowerShell](../virtual-machines-windows-ps-create.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

## <a name="verify-your-vm-is-utilizing-the-licensing-benefit"></a>Ověřte, zda že je virtuální počítač využívá licencování benefit
Po nasazení virtuálního počítače prostřednictvím Powershellu, šablony Resource Manageru nebo portálu, můžete ověřit, typ licence s `Get-AzureRmVM` následujícím způsobem:

```powershell
Get-AzureRmVM -ResourceGroup "myResourceGroup" -Name "myVM"
```

Výstup bude vypadat v následujícím příkladu pro systém Windows Server:

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

## <a name="list-all-azure-hybrid-benefit-for-windows-server-vms-in-a-subscription"></a>Zobrazí seznam všech Azure hybridní výhody pro virtuální počítače Windows serveru v předplatném.

Pokud chcete zobrazit a počet všechny virtuální počítače nasazené se službou Azure hybridní výhody pro Windows Server, můžete spustit následující příkaz ze svého předplatného:

```powershell
$vms = Get-AzureRMVM 
foreach ($vm in $vms) {"VM Name: " + $vm.Name, "   Azure Hybrid Benefit for Windows Server: "+ $vm.LicenseType}
```

## <a name="deploy-a-virtual-machine-scale-set-with-azure-hybrid-benefit-for-windows-server"></a>Nasazení virtuálního počítače škálování s Azure hybridní výhody pro Windows Server
V rámci virtuálního počítače sady škálování šablony Resource Manageru, další parametr `licenseType` musí být zadán. Další informace o [vytváření šablon Azure Resource Manager](../../resource-group-authoring-templates.md). Upravte šablonu Resource Manager, aby zahrnout vlastnost licenseType jako součást virtualMachineProfile byly sadou škálování a nasazení vaší šablony jako normální – viz následující ukázka pomocí bitové kopie systému Windows Server 2016:


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
Můžete také [vytvořit a nasadit škálovací sadu virtuálních počítačů](#https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-create) a nastavte vlastnost LicenseType

## <a name="next-steps"></a>Další postup
Další informace o [jak ušetřit peníze s výhodou hybridní Azure](https://azure.microsoft.com/pricing/hybrid-use-benefit/)

Další informace o [Azure hybridní výhody pro Windows Server licencování podrobné pokyny](https://docs.microsoft.com/windows-server/get-started/azure-hybrid-benefit)

Další informace o [šablon pomocí Resource Manageru](../../azure-resource-manager/resource-group-overview.md)

Další informace o [Azure hybridní výhody pro Windows Server a Azure Site Recovery zkontrolujte migrace aplikací do Azure i více nákladově efektivní](https://azure.microsoft.com/blog/hybrid-use-benefit-migration-with-asr/)

Další informace o [Windows 10 v Azure s víceklientské hostování vpravo](https://docs.microsoft.com/azure/virtual-machines/windows/windows-desktop-multitenant-hosting-deployment)

Další informace o [nejčastější dotazy](#https://azure.microsoft.com/en-us/pricing/hybrid-use-benefit/faq/)
