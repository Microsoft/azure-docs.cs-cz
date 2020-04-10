---
title: Výběr ibi virtuálních počítačů s Windows v Azure
description: Azure PowerShell použijte k určení vydavatele, nabídky, skladové položky a verze pro image virtuálních virtuálních her Marketplace.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: 188b8974-fabd-4cd3-b7dc-559cbb86b98a
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 01/25/2019
ms.author: cynthn
ms.openlocfilehash: a96cf4b07e6d564be1cac7fbb62a45d2d9ea7943
ms.sourcegitcommit: ae3d707f1fe68ba5d7d206be1ca82958f12751e8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/10/2020
ms.locfileid: "81011153"
---
# <a name="find-windows-vm-images-in-the-azure-marketplace-with-azure-powershell"></a>Vyhledání imagí virtuálních počítačů s Windows na Azure Marketplace pomocí Azure PowerShellu

Tento článek popisuje, jak pomocí Azure PowerShellu najít image virtuálních počítače na Azure Marketplace. Obrázek Marketplace pak můžete zadat při programovém vytváření virtuálního počítače pomocí prostředí PowerShell, šablony Správce prostředků nebo jiných nástrojů.

Dostupné image a nabídky můžete taky procházet pomocí výlohy [Azure Marketplace,](https://azuremarketplace.microsoft.com/) [portálu Azure](https://portal.azure.com)nebo [rozhraní příkazového příkazu Konfrontací s azure](../linux/cli-ps-findimage.md). 

 

[!INCLUDE [virtual-machines-common-image-terms](../../../includes/virtual-machines-common-image-terms.md)]

## <a name="table-of-commonly-used-windows-images"></a>Tabulka běžně používaných bitových kopií systému Windows

V této tabulce je uvedena podmnožina dostupné aplikace Skus pro uvedené vydavatele a nabídky.

| Vydavatel | Nabídka | Skladová jednotka (SKU) |
|:--- |:--- |:--- |
| MicrosoftWindowsServer |WindowsServer |2019-Datové centrum |
| MicrosoftWindowsServer |WindowsServer |2019-Datové centrum-Core |
| MicrosoftWindowsServer |WindowsServer |2019-Datové centrum s kontejnery |
| MicrosoftWindowsServer |WindowsServer |2016-Datové centrum |
| MicrosoftWindowsServer |WindowsServer |2016-Datacenter-Server-Core |
| MicrosoftWindowsServer |WindowsServer |2016-Datacenter-s-kontejnery |
| MicrosoftWindowsServer |WindowsServer |2012-R2-Datacenter |
| MicrosoftWindowsServer |WindowsServer |2012-Datacenter |
| MicrosoftSharePoint |MicrosoftSharePointServer |aktualizace sp2019 |
| MicrosoftSQLServer |SQL2019-WS2016 |Enterprise |
| Server MicrosoftRServer |RServer-WS2016 |Enterprise |

## <a name="navigate-the-images"></a>Navigace v obrazech

Jedním ze způsobů, jak najít obrázek v umístění, je spustit rutiny [Get-AzVMImagePublisher](https://docs.microsoft.com/powershell/module/az.compute/get-azvmimagepublisher), [Get-AzVMImageOffer](https://docs.microsoft.com/powershell/module/az.compute/get-azvmimageoffer)a [Get-AzVMImageSku](https://docs.microsoft.com/powershell/module/az.compute/get-azvmimagesku) v pořadí:

1. Vypsat vydavatele imagí.
2. Pro daného vydavatele vypsat jeho nabídky.
3. Pro danou nabídku vypsat její skladovou jednotku (SKU).

Potom pro vybrané skladové položky, [spusťte Get-AzVMImage](https://docs.microsoft.com/powershell/module/az.compute/get-azvmimage) seznam verzí k nasazení.

1. Seznam vydavatelů:

    ```powershell
    $locName="<Azure location, such as West US>"
    Get-AzVMImagePublisher -Location $locName | Select PublisherName
    ```

2. Vyplňte zvolené jméno majitele stránek a uveďte nabídky:

    ```powershell
    $pubName="<publisher>"
    Get-AzVMImageOffer -Location $locName -PublisherName $pubName | Select Offer
    ```

3. Vyplňte zvolené jméno nabídky a uveďte seznam SKU:

    ```powershell
    $offerName="<offer>"
    Get-AzVMImageSku -Location $locName -PublisherName $pubName -Offer $offerName | Select Skus
    ```

4. Vyplňte zvolené jméno Skladové položky a získejte verzi obrázku:

    ```powershell
    $skuName="<SKU>"
    Get-AzVMImage -Location $locName -PublisherName $pubName -Offer $offerName -Sku $skuName | Select Version
    ```
    
Z výstupu příkazu `Get-AzVMImage` můžete vybrat bitovou kopii verze pro nasazení nového virtuálního počítače.

Následující příklad ukazuje úplnou sekvenci příkazů a jejich výstupů:

```powershell
$locName="West US"
Get-AzVMImagePublisher -Location $locName | Select PublisherName
```

Částečný výstup:

```
PublisherName
-------------
...
abiquo
accedian
accellion
accessdata-group
accops
Acronis
Acronis.Backup
actian-corp
actian_matrix
actifio
activeeon
adgs
advantech
advantech-webaccess
advantys
...
```

Pro vydavatele *microsoftwindowsserveru:*

```powershell
$pubName="MicrosoftWindowsServer"
Get-AzVMImageOffer -Location $locName -PublisherName $pubName | Select Offer
```

Výstup:

```
Offer
-----
Windows-HUB
WindowsServer
WindowsServerSemiAnnual
```

Pro nabídku *WindowsServer:*

```powershell
$offerName="WindowsServer"
Get-AzVMImageSku -Location $locName -PublisherName $pubName -Offer $offerName | Select Skus
```

Částečný výstup:

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
2019-Datacenter
2019-Datacenter-Core
2019-Datacenter-Core-smalldisk
2019-Datacenter-Core-with-Containers
...
```

Potom pro sku *datového centra 2019:*

```powershell
$skuName="2019-Datacenter"
Get-AzVMImage -Location $locName -PublisherName $pubName -Offer $offerName -Sku $skuName | Select Version
```

Nyní můžete kombinovat vybraného vydavatele, nabídku, skladovou položku a verzi do urnu (hodnoty oddělené :). Předajte `--image` tento URN s parametrem při vytváření virtuálního virtuálního trhu s rutinou [New-AzVM.](https://docs.microsoft.com/powershell/module/az.compute/new-azvm) Volitelně můžete nahradit číslo verze v URN s "nejnovější" získat nejnovější verzi bitové kopie.

Pokud nasadíte virtuální ho s šablonou Správce prostředků, pak nastavíte parametry image jednotlivě ve vlastnostech. `imageReference` Viz [referenční informace k šablonám](/azure/templates/microsoft.compute/virtualmachines).

[!INCLUDE [virtual-machines-common-marketplace-plan](../../../includes/virtual-machines-common-marketplace-plan.md)]

### <a name="view-plan-properties"></a>Zobrazit vlastnosti plánu

Chcete-li zobrazit informace o plánu `Get-AzVMImage` nákupu obrázku, spusťte rutinu. Pokud `PurchasePlan` vlastnost ve výstupu `null`není , bitová kopie má podmínky, které je třeba přijmout před programovým nasazením.  

Například bitová kopie *datového centra Windows Serveru 2016* `PurchasePlan` nemá `null`další termíny, takže informace jsou:

```powershell
$version = "2016.127.20170406"
Get-AzVMImage -Location $locName -PublisherName $pubName -Offer $offerName -Skus $skuName -Version $version
```

Výstup:

```
Id               : /Subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/Providers/Microsoft.Compute/Locations/westus/Publishers/MicrosoftWindowsServer/ArtifactTypes/VMImage/Offers/WindowsServer/Skus/2016-Datacenter/Versions/2019.0.20190115
Location         : westus
PublisherName    : MicrosoftWindowsServer
Offer            : WindowsServer
Skus             : 2019-Datacenter
Version          : 2019.0.20190115
FilterExpression :
Name             : 2019.0.20190115
OSDiskImage      : {
                     "operatingSystem": "Windows"
                   }
PurchasePlan     : null
DataDiskImages   : []

```

Následující příklad ukazuje podobný příkaz pro virtuální počítač pro datové vědy – bitovou `name` `product`kopii `publisher` *systému Windows 2016,* která má následující `PurchasePlan` vlastnosti: , a . Některé obrázky `promotion code` mají také vlastnost. Chcete-li nasadit tuto bitovou kopii, naleznete v následujících částech přijmout podmínky a povolit programové nasazení.

```powershell
Get-AzVMImage -Location "westus" -PublisherName "microsoft-ads" -Offer "windows-data-science-vm" -Skus "windows2016" -Version "0.2.02"
```

Výstup:

```
Id               : /Subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/Providers/Microsoft.Compute/Locations/westus/Publishers/microsoft-ads/ArtifactTypes/VMImage/Offers/windows-data-science-vm/Skus/windows2016/Versions/19.01.14
Location         : westus
PublisherName    : microsoft-ads
Offer            : windows-data-science-vm
Skus             : windows2016
Version          : 19.01.14
FilterExpression :
Name             : 19.01.14
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

### <a name="accept-the-terms"></a>Přijetí podmínek použití

Chcete-li zobrazit licenční podmínky, použijte rutinu [Get-AzMarketplaceterms](https://docs.microsoft.com/powershell/module/az.marketplaceordering/get-azmarketplaceterms) a předat parametry plánu nákupu. Výstup obsahuje odkaz na podmínky pro obrázek Marketplace a ukazuje, zda jste dříve přijali podmínky. Ujistěte se, že v hodnotách parametrů používáte všechna malá písmena.

```powershell
Get-AzMarketplaceterms -Publisher "microsoft-ads" -Product "windows-data-science-vm" -Name "windows2016"
```

Výstup:

```
Publisher         : microsoft-ads
Product           : windows-data-science-vm
Plan              : windows2016
LicenseTextLink   : https://storelegalterms.blob.core.windows.net/legalterms/3E5ED_legalterms_MICROSOFT%253a2DADS%253a24WINDOWS%253a2DDATA%253a2DSCIENCE%253a2DVM%253a24WINDOWS2016%253a24OC5SKMQOXSED66BBSNTF4XRCS4XLOHP7QMPV54DQU7JCBZWYFP35IDPOWTUKXUC7ZAG7W6ZMDD6NHWNKUIVSYBZUTZ245F44SU5AD7Q.txt
PrivacyPolicyLink : https://www.microsoft.com/EN-US/privacystatement/OnlineServices/Default.aspx
Signature         : 2UMWH6PHSAIM4U22HXPXW25AL2NHUJ7Y7GRV27EBL6SUIDURGMYG6IIDO3P47FFIBBDFHZHSQTR7PNK6VIIRYJRQ3WXSE6BTNUNENXA
Accepted          : False
Signdate          : 1/25/2019 7:43:00 PM
```

Pomocí rutiny [Set-AzMarketplaceterms](https://docs.microsoft.com/powershell/module/az.marketplaceordering/set-azmarketplaceterms) můžete podmínky přijmout nebo odmítnout. Podmínky pro bitovou kopii je třeba přijmout pouze jednou za předplatné. Ujistěte se, že v hodnotách parametrů používáte všechna malá písmena. 

```powershell
$agreementTerms=Get-AzMarketplaceterms -Publisher "microsoft-ads" -Product "windows-data-science-vm" -Name "windows2016"

Set-AzMarketplaceTerms -Publisher "microsoft-ads" -Product "windows-data-science-vm" -Name "windows2016" -Terms $agreementTerms -Accept
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

### <a name="deploy-using-purchase-plan-parameters"></a>Nasazení pomocí parametrů plánu nákupu

Po přijetí podmínek pro image, můžete nasadit virtuální ho v tomto předplatném. Jak je znázorněno v následujícím fragmentu, použijte rutinu [Set-AzVMPlan](https://docs.microsoft.com/powershell/module/az.compute/set-azvmplan) k nastavení informací o plánu Marketplace pro objekt virtuálního stavitele. Úplný skript pro vytvoření nastavení sítě pro virtuální počítače a dokončení nasazení najdete v [příkladech skriptů prostředí PowerShell](powershell-samples.md).

```powershell
...

$vmConfig = New-AzVMConfig -VMName "myVM" -VMSize Standard_D1

# Set the Marketplace plan information

$publisherName = "microsoft-ads"

$productName = "windows-data-science-vm"

$planName = "windows2016"

$vmConfig = Set-AzVMPlan -VM $vmConfig -Publisher $publisherName -Product $productName -Name $planName

$cred=Get-Credential

$vmConfig = Set-AzVMOperatingSystem -Windows -VM $vmConfig -ComputerName "myVM" -Credential $cred

# Set the Marketplace image

$offerName = "windows-data-science-vm"

$skuName = "windows2016"

$version = "19.01.14"

$vmConfig = Set-AzVMSourceImage -VM $vmConfig -PublisherName $publisherName -Offer $offerName -Skus $skuName -Version $version
...
```
Pak předáte konfiguraci virtuálního počítače spolu `New-AzVM` s objekty konfigurace sítě do rutiny.

## <a name="next-steps"></a>Další kroky

Pokud chcete pomocí `New-AzVM` rutiny rychle vytvořit virtuální počítač pomocí základních informací o obrázku, přečtěte si informace o vytvoření [virtuálního počítače s Windows pomocí prostředí PowerShell](quick-create-powershell.md).


Chcete-li [vytvořit plně nakonfigurovaný virtuální počítač ,](../scripts/virtual-machines-windows-powershell-sample-create-vm.md)podívejte se na příklad skriptu prostředí PowerShell .


