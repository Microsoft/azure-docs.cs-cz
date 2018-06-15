---
title: Vyberte bitové kopie virtuálního počítače s Windows v Azure | Microsoft Docs
description: Další informace o použití prostředí Azure PowerShell k určení vydavatele, nabídky, SKU a verze pro Image Marketplace virtuálních počítačů.
services: virtual-machines-windows
documentationcenter: ''
author: dlepow
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 188b8974-fabd-4cd3-b7dc-559cbb86b98a
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 02/28/2018
ms.author: danlep
ms.openlocfilehash: 885ee10bc63b65d936f5b433a18c4435b2503720
ms.sourcegitcommit: 6e43006c88d5e1b9461e65a73b8888340077e8a2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/01/2018
ms.locfileid: "32313439"
---
# <a name="how-to-find-windows-vm-images-in-the-azure-marketplace-with-azure-powershell"></a>Postup nalezení bitové kopie virtuálního počítače s Windows v Azure Marketplace s prostředím Azure PowerShell

Tento článek popisuje, jak pomocí prostředí Azure PowerShell k vyhledání Image virtuálních počítačů v Azure Marketplace. Tyto informace slouží k určení image pořízenou prostřednictvím Marketplace, při vytváření virtuálního počítače prostřednictvím kódu programu v prostředí PowerShell šablony správce prostředků nebo jiných nástrojů.

Ujistěte se, zda je nainstalován a nakonfigurován nejnovější [modul Azure PowerShell](/powershell/azure/install-azurerm-ps).

[!INCLUDE [virtual-machines-common-image-terms](../../../includes/virtual-machines-common-image-terms.md)]

## <a name="table-of-commonly-used-windows-images"></a>Tabulka běžně používané bitových kopií systému Windows
| Vydavatel | Nabídka | Skladová jednotka (SKU) |
|:--- |:--- |:--- |:--- |
| MicrosoftWindowsServer |WindowsServer |2016 Datacenter |
| MicrosoftWindowsServer |WindowsServer |2016-Datacenter-Server-Core |
| MicrosoftWindowsServer |WindowsServer |2016 datového centra s kontejnery |
| MicrosoftWindowsServer |WindowsServer |2016-Nano-Server |
| MicrosoftWindowsServer |WindowsServer |2012-R2-Datacenter |
| MicrosoftWindowsServer |WindowsServer |2008-R2-SP1 |
| MicrosoftDynamicsNAV |DynamicsNAV |2017 |
| MicrosoftSharePoint |MicrosoftSharePointServer |2016 |
| MicrosoftSQLServer |SQL2014SP2-WS2012R2 |Enterprise |
| MicrosoftWindowsServerHPCPack |WindowsServerHPCPack |2012R2 |
| MicrosoftWindowsServerEssentials |WindowsServerEssentials |WindowsServerEssentials |

## <a name="navigate-the-images"></a>Přejděte bitové kopie

Jiný způsob, jak najít bitovou kopii v umístění je spuštění [Get-AzureRMVMImagePublisher](/powershell/module/azurerm.compute/get-azurermvmimagepublisher), [Get-AzureRMVMImageOffer](/powershell/module/azurerm.compute/get-azurermvmimageoffer), a [Get-AzureRMVMImageSku](/powershell/module/azurerm.compute/get-azurermvmimagesku) rutiny v pořadí. Pomocí těchto příkazů určit tyto hodnoty:

1. Vypsat vydavatele imagí.
2. Pro daného vydavatele vypsat jeho nabídky.
3. Pro danou nabídku vypsat její skladovou jednotku (SKU).

Spusťte pro vybranou SKU, [Get-AzureRMVMImage](/powershell/module/azurerm.compute/get-azurermvmimage) seznam verzí pro nasazení.

Nejprve vypište vydavatele pomocí následujících příkazů:

```powershell
$locName="<Azure location, such as West US>"
Get-AzureRMVMImagePublisher -Location $locName | Select PublisherName
```

Vyplňte název zvoleného vydavatele a spusťte následující příkazy:

```powershell
$pubName="<publisher>"
Get-AzureRMVMImageOffer -Location $locName -Publisher $pubName | Select Offer
```

Vyplňte název zvolené nabídky a spusťte následující příkazy:

```powershell
$offerName="<offer>"
Get-AzureRMVMImageSku -Location $locName -Publisher $pubName -Offer $offerName | Select Skus
```

Vyplňte zvolený název SKU a spusťte následující příkazy:

```powershell
$skuName="<SKU>"
Get-AzureRMVMImage -Location $locName -Publisher $pubName -Offer $offerName -Sku $skuName | Select Version
```

Z výstupu `Get-AzureRMVMImage` příkazů, můžete vybrat verzi image k nasazení nového virtuálního počítače.

Následující příkazy ukazují úplný příklad:

```powershell
$locName="West US"
Get-AzureRMVMImagePublisher -Location $locName | Select PublisherName

```

Výstup:

```
PublisherName
-------------
a10networks
aiscaler-cache-control-ddos-and-url-rewriting-
alertlogic
AlertLogic.Extension
Barracuda.Azure.ConnectivityAgent
barracudanetworks
basho
boxless
bssw
Canonical
...
```

Pro *MicrosoftWindowsServer* vydavatele:

```powershell
$pubName="MicrosoftWindowsServer"
Get-AzureRMVMImageOffer -Location $locName -Publisher $pubName | Select Offer
```

Výstup:

```
Offer
-----
Windows-HUB
WindowsServer
WindowsServerSemiAnnual
```

Pro *Windows Server* nabízí:

```powershell
$offerName="WindowsServer"
Get-AzureRMVMImageSku -Location $locName -Publisher $pubName -Offer $offerName | Select Skus
```

Výstup:

```
Skus
----
2008-R2-SP1
2008-R2-SP1-smalldisk
2012-Datacenter
2012-Datacenter-smalldisk
2012-R2-Datacenter
2012-R2-Datacenter-smalldisk
2016-Datacenter
2016-Datacenter-Server-Core
2016-Datacenter-Server-Core-smalldisk
2016-Datacenter-smalldisk
2016-Datacenter-with-Containers
2016-Datacenter-with-RDSH
2016-Nano-Server
```

Potom *2016 Datacenter* SKU:

```powershell
$skuName="2016-Datacenter"
Get-AzureRMVMImage -Location $locName -Publisher $pubName -Offer $offerName -Sku $skuName | Select Version
```

Teď se můžete sloučit vybrané vydavatele, nabídky, SKU a verzi do název URN (oddělených hodnoty:). Předat tuto URN s `--image` parametr při vytváření virtuálního počítače s [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm) rutiny. Mějte na paměti, že můžete volitelně nahradit číslo verze v název URN "poslední zálohy". Tato verze je vždy nejnovější verze bitové kopie. Můžete také použít název URN s [Set-AzureRMVMSourceImage](/powershell/module/azurerm.compute/set-azurermvmsourceimage) rutiny prostředí PowerShell. 

Pokud nasadíte virtuální počítač pomocí šablony Resource Manageru, můžete nastavit parametry image jednotlivě v `imageReference` vlastnosti. Najdete v článku [odkaz na šablonu](/azure/templates/microsoft.compute/virtualmachines).

[!INCLUDE [virtual-machines-common-marketplace-plan](../../../includes/virtual-machines-common-marketplace-plan.md)]


### <a name="view-plan-properties"></a>Zobrazit vlastnosti plánu

Chcete-li zobrazit informace o plánu nákupu bitové kopie, spusťte `Get-AzureRMVMImage` rutiny. Pokud `PurchasePlan` vlastnost ve výstupu není `null`, bitová kopie je podmínky je nutné splnit, než se programové nasazení.  

Bitovou kopii systému Windows Server 2016 Datacenter například nemá další podmínky, protože `PurchasePlan` informace `null`:

```powershell
$version = "2016.127.20170406"
Get-AzureRMVMImage -Location $locName -Publisher $pubName -Offer $offerName -Skus $skuName -Version $version
```

Výstup:

```
Id               : /Subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/Providers/Microsoft.Compute/Locations/westus/Publishers/MicrosoftWindowsServer/ArtifactTypes/VMImage/Offers/WindowsServer/Skus/2016-Datacenter/
                   Versions/2016.127.20170406
Location         : westus
PublisherName    : MicrosoftWindowsServer
Offer            : WindowsServer
Skus             : 2016-Datacenter
Version          : 2016.127.20170406
FilterExpression :
Name             : 2016.127.20170406
OSDiskImage      : {
                     "operatingSystem": "Windows"
                   }
PurchasePlan     : null
DataDiskImages   : []

```

Spuštění podobné příkazu pro datové vědy virtuálního počítače – Windows 2016 obrázek ukazuje následující `PurchasePlan` vlastnosti: `name`, `product`, a `publisher`. (Také mít některé obrázky, `promotion code` vlastnost.) Pokud chcete nasadit tuto bitovou kopii, najdete v následujících částech přijmout podmínky a povolit programové nasazení.

```powershell
Get-AzureRMVMImage -Location "westus" -Publisher "microsoft-ads" -Offer "windows-data-science-vm" -Skus "windows2016" -Version "0.2.02"
```

Výstup:

```
Id               : /Subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/Providers/Microsoft.Compute/Locations/westus/Publishers/microsoft-ads/ArtifactTypes/VMIma
                   ge/Offers/windows-data-science-vm/Skus/windows2016/Versions/0.2.02
Location         : westus
PublisherName    : microsoft-ads
Offer            : windows-data-science-vm
Skus             : windows2016
Version          : 0.2.02
FilterExpression :
Name             : 0.2.02
OSDiskImage      : {
                     "operatingSystem": "Windows"
                   }
PurchasePlan     : {
                     "publisher": "microsoft-ads",
                     "name": "windows2016",
                     "product": "windows-data-science-vm"
                   }
DataDiskImages   : []

```

### <a name="accept-the-terms"></a>Přijmout podmínky

Chcete-li zobrazit licenční podmínky, použijte [Get-AzureRmMarketplaceterms](/powershell/module/azurerm.marketplaceordering/get-azurermmarketplaceterms) rutiny a předejte jí nákupu plánování parametry. Výstup obsahuje odkaz na podmínky pro bitovou kopii Marketplace a uvádí, zda jste dříve přijali podmínky. Příklad:

```powershell
Get-AzureRmMarketplaceterms -Publisher "microsoft-ads" -Product "windows-data-science-vm" -Name "windows2016"
```

Výstup:

```
Publisher         : microsoft-ads
Product           : windows-data-science-vm
Plan              : windows2016
LicenseTextLink   : https://storelegalterms.blob.core.windows.net/legalterms/3E5ED_legalterms_MICROSOFT%253a2DADS%253a24WINDOWS%253a2DDATA%253a2DSCIENCE%253a2DV
                    M%253a24WINDOWS2016%253a24OC5SKMQOXSED66BBSNTF4XRCS4XLOHP7QMPV54DQU7JCBZWYFP35IDPOWTUKXUC7ZAG7W6ZMDD6NHWNKUIVSYBZUTZ245F44SU5AD7Q.txt
PrivacyPolicyLink : https://www.microsoft.com/EN-US/privacystatement/OnlineServices/Default.aspx
Signature         : 2UMWH6PHSAIM4U22HXPXW25AL2NHUJ7Y7GRV27EBL6SUIDURGMYG6IIDO3P47FFIBBDFHZHSQTR7PNK6VIIRYJRQ3WXSE6BTNUNENXA
Accepted          : False
Signdate          : 2/23/2018 7:43:00 PM
```

Použití [Set-AzureRmMarketplaceterms](/powershell/module/azurerm.marketplaceordering/set-azurermmarketplaceterms) rutiny k přijetí nebo odmítnutí podmínky. Potřebujete souhlas s podmínkami jednou za předplatné pro bitovou kopii. Příklad:

```powershell

$agreementTerms=Get-AzureRmMarketplaceterms -Publisher "microsoft-ads" -Product "windows-data-science-vm" -Name "windows2016"

Set-AzureRmMarketplaceTerms -Publisher "microsoft-ads" -Product "windows-data-science-vm" -Name "windows2016" -Terms $agreementTerms -Accept

```

Výstup:

```
Publisher         : microsoft-ads
Product           : windows-data-science-vm
Plan              : windows2016
LicenseTextLink   : https://storelegalterms.blob.core.windows.net/legalterms/3E5ED_legalterms_MICROSOFT%253a2DADS%253a24WINDOWS%253a2DDATA%253a2DSCIENCE%253a2DV
                    M%253a24WINDOWS2016%253a24OC5SKMQOXSED66BBSNTF4XRCS4XLOHP7QMPV54DQU7JCBZWYFP35IDPOWTUKXUC7ZAG7W6ZMDD6NHWNKUIVSYBZUTZ245F44SU5AD7Q.txt
PrivacyPolicyLink : https://www.microsoft.com/EN-US/privacystatement/OnlineServices/Default.aspx
Signature         : VNMTRJK3MNJ5SROEG2BYDA2YGECU33GXTD3UFPLPC4BAVKAUL3PDYL3KBKBLG4ZCDJZVNSA7KJWTGMDSYDD6KRLV3LV274DLBJSS4GQ
Accepted          : True
Signdate          : 2/23/2018 7:49:31 PM
```

### <a name="deploy-using-purchase-plan-parameters"></a>Nasazení pomocí parametrů plánu nákupu
Po vyjádření souhlasu s podmínkami pro bitovou kopii, můžete nasadit virtuální počítač v rámci předplatného. Jak je znázorněno v následujícím fragmentu kódu, použijte [Set-AzureRmVMPlan](/powershell/module/azurerm.compute/set-azurermvmplan) rutiny se nastavit informace o plánu Marketplace pro objekt virtuálního počítače. Skript dokončení vytvoření nastavení sítě pro virtuální počítač a dokončení nasazení najdete v článku [příklady skript prostředí PowerShell](powershell-samples.md).

```powershell
...
$vmConfig = New-AzureRmVMConfig -VMName "myVM" -VMSize Standard_D1

# Set the Marketplace plan information
$vmConfig = Set-AzureRmVMPlan -VM $vmConfig -Publisher "imagePlanPublisher" -Product "imagePlanProduct" -Name "imagePlanName"

$cred=Get-Credential

$vmConfig = Set-AzureRmVMOperatingSystem -Windows -VM $vmConfig -ComputerName "myVM" -Credential $cred

# Set the Marketplace image
$vmConfig = Set-AzureRmVMSourceImage -VM $vmConfig -PublisherName "imagePublisher" -Offer "imageOffer" -Skus "imageSku" -Version "imageVersion"
...
```
Pak předejte konfigurace virtuálního počítače společně s objekty konfigurace sítě do `New-AzureRmVM` rutiny.

## <a name="next-steps"></a>Další postup
Chcete-li vytvořit virtuální počítač rychle s `New-AzureRmVM` pomocí informací o základní bitovou kopii, najdete v části [vytvoření virtuálního počítače s Windows pomocí prostředí PowerShell](quick-create-powershell.md).

Podívejte se na příklad skriptu prostředí PowerShell k [vytvořit kompletně nakonfigurovaný virtuální počítač](../scripts/virtual-machines-windows-powershell-sample-create-vm.md).


