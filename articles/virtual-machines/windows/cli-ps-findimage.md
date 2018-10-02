---
title: Vyberte Image virtuálních počítačů s Windows v Azure | Dokumentace Microsoftu
description: Zjistěte, jak pomocí prostředí Azure PowerShell k určení vydavatele, nabídky, SKU a verze pro Image Marketplace virtuálních počítačů.
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
ms.date: 09/28/2018
ms.author: danlep
ms.openlocfilehash: 4fb718eb7247bddd8869b8973479377e3baebdda
ms.sourcegitcommit: 7bc4a872c170e3416052c87287391bc7adbf84ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/02/2018
ms.locfileid: "48017174"
---
# <a name="how-to-find-windows-vm-images-in-the-azure-marketplace-with-azure-powershell"></a>Jak najít Image virtuálních počítačů s Windows v Azure Marketplace pomocí Azure Powershellu

Tento článek popisuje, jak pomocí Azure Powershellu k vyhledání imagí virtuálních počítačů na webu Azure Marketplace. Tyto informace slouží k určení Marketplace image, pokud vytvoříte virtuální počítač prostřednictvím kódu programu pomocí prostředí PowerShell, šablony Resource Manageru nebo jiných nástrojů.

Také procházet dostupné Image a pomocí nabídky [Azure Marketplace](https://azuremarketplace.microsoft.com/) výkladní skříň, [webu Azure portal](https://portal.azure.com), nebo [rozhraní příkazového řádku Azure](../linux/cli-ps-findimage.md). 

Ujistěte se, že jste nainstalovali a nakonfigurovali nejnovější [modulu Azure PowerShell](/powershell/azure/install-azurerm-ps).

[!INCLUDE [virtual-machines-common-image-terms](../../../includes/virtual-machines-common-image-terms.md)]

## <a name="table-of-commonly-used-windows-images"></a>Tabulka běžně používaných imagí Windows
| Vydavatel | Nabídka | Skladová jednotka (SKU) |
|:--- |:--- |:--- |:--- |
| MicrosoftWindowsServer |WindowsServer |2016-Datacenter |
| MicrosoftWindowsServer |WindowsServer |2016-Datacenter-Server-Core |
| MicrosoftWindowsServer |WindowsServer |2016 Datacenter s kontejnery |
| MicrosoftWindowsServer |WindowsServer |2012-R2-Datacenter |
| MicrosoftWindowsServer |WindowsServer |2012-Datacenter |
| MicrosoftWindowsServer |WindowsServer |2008-R2-SP1 |
| MicrosoftDynamicsNAV |DynamicsNAV |2017 |
| MicrosoftSharePoint |MicrosoftSharePointServer |2016 |
| MicrosoftSQLServer |SQL2017 WS2016 |Enterprise |
| MicrosoftRServer |R server WS2016 |Enterprise |

## <a name="navigate-the-images"></a>Procházením imagí

Dalším způsobem, jak najít image v umístění je spustit [Get-AzureRMVMImagePublisher](/powershell/module/azurerm.compute/get-azurermvmimagepublisher), [Get-AzureRMVMImageOffer](/powershell/module/azurerm.compute/get-azurermvmimageoffer), a [Get-AzureRMVMImageSku](/powershell/module/azurerm.compute/get-azurermvmimagesku) rutiny v pořadí. Tyto hodnoty můžete určit pomocí následujících příkazů:

1. Vypsat vydavatele imagí.
2. Pro daného vydavatele vypsat jeho nabídky.
3. Pro danou nabídku vypsat její skladovou jednotku (SKU).

Spusťte pro vybraná skladová položka [Get-AzureRMVMImage](/powershell/module/azurerm.compute/get-azurermvmimage) vypsat verze nasazení.

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

Vyplňte název zvolené skladové položky a spusťte následující příkazy:

```powershell
$skuName="<SKU>"
Get-AzureRMVMImage -Location $locName -Publisher $pubName -Offer $offerName -Sku $skuName | Select Version
```

Z výstupu `Get-AzureRMVMImage` příkazu, můžete vybrat verzi image k nasazení nového virtuálního počítače.

Následující příkazy ukazují, úplný příklad:

```powershell
$locName="West US"
Get-AzureRMVMImagePublisher -Location $locName | Select PublisherName

```

Částečný výstup:

```
PublisherName
-------------
...
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

Pro *WindowsServer* nabízejí:

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
```

Potom *2016-Datacenter* SKU:

```powershell
$skuName="2016-Datacenter"
Get-AzureRMVMImage -Location $locName -Publisher $pubName -Offer $offerName -Sku $skuName | Select Version
```

Nyní můžete zkombinovat vybrané vydavatele, nabídky, SKU a verze do název URN (hodnoty oddělené:). Předejte tento název URN s `--image` při vytváření virtuálního počítače se [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm) rutiny. Mějte na paměti, že můžete volitelně nahradit číslo verze v URN "poslední zálohy". Tato verze je vždy nejnovější verzi image.

Pokud provádíte nasazení virtuálního počítače pomocí šablony Resource Manageru, můžete nastavit parametry image jednotlivě v `imageReference` vlastnosti. Zobrazit [referenčními informacemi k šablonám](/azure/templates/microsoft.compute/virtualmachines).

[!INCLUDE [virtual-machines-common-marketplace-plan](../../../includes/virtual-machines-common-marketplace-plan.md)]

### <a name="view-plan-properties"></a>Zobrazit vlastnosti plánu

Chcete-li zobrazit informace o plánu nákupní obrazu, spusťte `Get-AzureRMVMImage` rutiny. Pokud `PurchasePlan` vlastnost ve výstupu není `null`, bitová kopie je podmínky musíte přijmout před programové nasazení.  

Image Windows serveru 2016 Datacenter například nemá další podmínky, protože `PurchasePlan` informace `null`:

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

Podobně jako příkaz systémem pro virtuální počítač pro datové vědy – Windows 2016 obrázek znázorňuje následující `PurchasePlan` vlastnosti: `name`, `product`, a `publisher`. (Některé obrázky také mít `promotion code` vlastnosti.) Pokud chcete nasadit tuto bitovou kopii, naleznete v následujících částech k přijetí podmínek a povolit programové nasazení.

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

Chcete-li zobrazit licenční podmínky, použijte [Get-AzureRmMarketplaceterms](/powershell/module/azurerm.marketplaceordering/get-azurermmarketplaceterms) rutiny a předejte jí nákupu plánu parametry. Výstup obsahuje odkaz na podmínky pro Marketplace image a ukazuje, zda jste již přijali podmínky. Nezapomeňte použít jenom malá písmena. hodnoty parametrů. Příklad:

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

Použití [Set-AzureRmMarketplaceterms](/powershell/module/azurerm.marketplaceordering/set-azurermmarketplaceterms) rutiny přijmout nebo odmítnout podmínky. Stačí přijmout podmínky jednou za předplatné pro bitovou kopii. Nezapomeňte použít jenom malá písmena. hodnoty parametrů. Příklad:

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
Signature         : XXXXXXK3MNJ5SROEG2BYDA2YGECU33GXTD3UFPLPC4BAVKAUL3PDYL3KBKBLG4ZCDJZVNSA7KJWTGMDSYDD6KRLV3LV274DLBXXXXXX
Accepted          : True
Signdate          : 2/23/2018 7:49:31 PM
```

### <a name="deploy-using-purchase-plan-parameters"></a>Nasazení pomocí parametrů koupit plán

Po přijetí podmínek pro bitovou kopii, můžete nasadit virtuální počítač v rámci předplatného. Jak je znázorněno v následujícím fragmentu kódu, použijte [Set-AzureRmVMPlan](/powershell/module/azurerm.compute/set-azurermvmplan) rutiny k nastavení plánu informace z Marketplace pro tento objekt virtuálního počítače. Celý skript k vytvoření nastavení sítě pro virtuální počítač a dokončit nasazení, najdete v článku [ukázkové skripty Powershellu](powershell-samples.md).

```powershell
...

$vmConfig = New-AzureRmVMConfig -VMName "myVM" -VMSize Standard_D1

# Set the Marketplace plan information

$publisherName = "microsoft-ads"

$productName = "windows-data-science-vm"

$planName = "windows2016"

$vmConfig = Set-AzureRmVMPlan -VM $vmConfig -Publisher $publisherName -Product $productName -Name $planName

$cred=Get-Credential

$vmConfig = Set-AzureRmVMOperatingSystem -Windows -VM $vmConfig -ComputerName "myVM" -Credential $cred

# Set the Marketplace image

$offerName = "windows-data-science-vm"

$skuName = "windows2016"

$version = "0.2.02"

$vmConfig = Set-AzureRmVMSourceImage -VM $vmConfig -PublisherName $publisherName -Offer $offerName -Skus $skuName -Version $version
...
```
Konfigurace virtuálního počítače společně s objekty konfigurace sítě a pak předáte `New-AzureRmVM` rutiny.

## <a name="next-steps"></a>Další postup
Chcete-li vytvořit virtuální počítač rychle s `New-AzureRmVM` s použitím informací o základní image, najdete v článku [vytvoření virtuálního počítače s Windows pomocí prostředí PowerShell](quick-create-powershell.md).

Podívejte se na příklad skriptu prostředí PowerShell k [vytvoření plně nakonfigurovaného virtuálního počítače](../scripts/virtual-machines-windows-powershell-sample-create-vm.md).


