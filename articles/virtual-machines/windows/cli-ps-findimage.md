---
title: Vyberte Image virtuálních počítačů s Windows v Azure | Dokumentace Microsoftu
description: K určení vydavatele, nabídky, SKU a verze pro Image Marketplace virtuálních počítačů pomocí Azure Powershellu.
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
ms.date: 10/08/2018
ms.author: danlep
ms.openlocfilehash: 5934e955d2a18d111c625670bced134df37ef045
ms.sourcegitcommit: 07a09da0a6cda6bec823259561c601335041e2b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/18/2018
ms.locfileid: "49409590"
---
# <a name="find-windows-vm-images-in-the-azure-marketplace-with-azure-powershell"></a>Vyhledání imagí virtuálních počítačů Windows na webu Azure Marketplace pomocí Azure Powershellu

Tento článek popisuje, jak pomocí Azure Powershellu k vyhledání imagí virtuálních počítačů na webu Azure Marketplace. Můžete pak určit Marketplace image, když vytváříte virtuální počítač prostřednictvím kódu programu pomocí Powershellu, šablon Resource Manageru nebo jiných nástrojů.

Můžete také procházet dostupné Image a pomocí nabídky [Azure Marketplace](https://azuremarketplace.microsoft.com/) výkladní skříň, [webu Azure portal](https://portal.azure.com), nebo [rozhraní příkazového řádku Azure](../linux/cli-ps-findimage.md). 

Ujistěte se, že je nainstalujete a nakonfigurujete na nejnovější verzi [modulu Azure PowerShell](/powershell/azure/install-azurerm-ps).

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

Jeden ze způsobů, jak najít image v umístění je spustit [Get-AzureRMVMImagePublisher](/powershell/module/azurerm.compute/get-azurermvmimagepublisher), [Get-AzureRMVMImageOffer](/powershell/module/azurerm.compute/get-azurermvmimageoffer), a [Get-AzureRMVMImageSku](/powershell/module/azurerm.compute/get-azurermvmimagesku) rutiny v pořadí:

1. Vypsat vydavatele imagí.
2. Pro daného vydavatele vypsat jeho nabídky.
3. Pro danou nabídku vypsat její skladovou jednotku (SKU).

Spusťte pro vybraná skladová položka [Get-AzureRMVMImage](/powershell/module/azurerm.compute/get-azurermvmimage) vypsat verze nasazení.

1. Vypište vydavatele:

    ```powershell
    $locName="<Azure location, such as West US>"
    Get-AzureRMVMImagePublisher -Location $locName | Select PublisherName
    ```

2. Vyplňte název zvoleného vydavatele a nabídky seznamu:

    ```powershell
    $pubName="<publisher>"
    Get-AzureRMVMImageOffer -Location $locName -Publisher $pubName | Select Offer
    ```

3. Vyplňte název zvolené nabídky a skladové jednotky seznamu:

    ```powershell
    $offerName="<offer>"
    Get-AzureRMVMImageSku -Location $locName -Publisher $pubName -Offer $offerName | Select Skus
    ```
    
4. Vyplňte název zvolené skladové položky a získat verzi image:

    ```powershell
    $skuName="<SKU>"
    Get-AzureRMVMImage -Location $locName -Publisher $pubName -Offer $offerName -Sku $skuName | Select Version
    ```
    
Z výstupu `Get-AzureRMVMImage` příkazu, můžete vybrat verzi image k nasazení nového virtuálního počítače.

Následující příklad ukazuje úplnou pořadí příkazů a jejich výstupy:

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

Nyní můžete zkombinovat vybrané vydavatele, nabídky, SKU a verze do název URN (hodnoty oddělené:). Předejte tento název URN s `--image` při vytváření virtuálního počítače se [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm) rutiny. Volitelně můžete nahradit číslo verze v URN "poslední zálohy" získat nejnovější verzi image.

Pokud nasazení virtuálního počítače pomocí šablony Resource Manageru, pak budete nastavte parametry bitové kopie v jednotlivě `imageReference` vlastnosti. Zobrazit [referenčními informacemi k šablonám](/azure/templates/microsoft.compute/virtualmachines).

[!INCLUDE [virtual-machines-common-marketplace-plan](../../../includes/virtual-machines-common-marketplace-plan.md)]

### <a name="view-plan-properties"></a>Zobrazit vlastnosti plánu

Chcete-li zobrazit informace o plánu nákupní obrazu, spusťte `Get-AzureRMVMImage` rutiny. Pokud `PurchasePlan` vlastnost ve výstupu není `null`, bitová kopie je podmínky musíte přijmout před programové nasazení.  

Například *systému Windows Server 2016 Datacenter* obrázek nemá další podmínky, takže `PurchasePlan` informace jsou `null`:

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

Následující příklad ukazuje, podobně jako příkaz pro *virtuální počítač datové vědy – Windows 2016* bitovou kopii, která má následující `PurchasePlan` vlastnosti: `name`, `product`, a `publisher`. Také mít nějakou image `promotion code` vlastnost. Pokud chcete nasadit tuto bitovou kopii, naleznete v následujících částech k přijetí podmínek a chcete povolit programové nasazení.

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

Chcete-li zobrazit licenční podmínky, použijte [Get-AzureRmMarketplaceterms](/powershell/module/azurerm.marketplaceordering/get-azurermmarketplaceterms) rutiny a předejte jí nákupu plánu parametry. Výstup obsahuje odkaz na podmínky pro Marketplace image a ukazuje, zda jste již přijali podmínky. Nezapomeňte použít jenom malá písmena. hodnoty parametrů.

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

Použití [Set-AzureRmMarketplaceterms](/powershell/module/azurerm.marketplaceordering/set-azurermmarketplaceterms) rutiny přijmout nebo odmítnout podmínky. Stačí přijmout podmínky jednou za předplatné pro bitovou kopii. Nezapomeňte použít jenom malá písmena. hodnoty parametrů. 

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

Po přijetí podmínek pro bitovou kopii, můžete nasadit virtuální počítač v tomto předplatném. Jak je znázorněno v následujícím fragmentu kódu, použijte [Set-AzureRmVMPlan](/powershell/module/azurerm.compute/set-azurermvmplan) rutiny k nastavení plánu informace z Marketplace pro tento objekt virtuálního počítače. Celý skript k vytvoření nastavení sítě pro virtuální počítač a dokončit nasazení, najdete v článku [ukázkové skripty Powershellu](powershell-samples.md).

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
Potom budete předávat konfiguraci virtuálního počítače společně s objekty konfigurace sítě a `New-AzureRmVM` rutiny.

## <a name="next-steps"></a>Další postup
Chcete-li vytvořit virtuální počítač rychle se `New-AzureRmVM` rutiny s použitím informací o základní image, najdete v článku [vytvoření virtuálního počítače s Windows pomocí prostředí PowerShell](quick-create-powershell.md).

Podívejte se na příklad skriptu prostředí PowerShell k [vytvoření plně nakonfigurovaného virtuálního počítače](../scripts/virtual-machines-windows-powershell-sample-create-vm.md).


