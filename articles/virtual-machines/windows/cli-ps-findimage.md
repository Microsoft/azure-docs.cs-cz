---
title: Výběr imagí virtuálních počítačů s Windows v Azure
description: K určení vydavatele, nabídky, SKU a verze imagí virtuálních počítačů Marketplace použijte Azure PowerShell.
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
ms.openlocfilehash: 2388b51c8103b6bcbae0c32d3c4d78a176caf282
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/05/2020
ms.locfileid: "78388663"
---
# <a name="find-windows-vm-images-in-the-azure-marketplace-with-azure-powershell"></a>Vyhledání imagí virtuálních počítačů s Windows v Azure Marketplace s využitím Azure PowerShell

Tento článek popisuje, jak použít Azure PowerShell k nalezení imagí virtuálních počítačů v Azure Marketplace. Pak můžete zadat image Marketplace, když vytvoříte virtuální počítač pomocí programu PowerShell, Správce prostředků šablon nebo jiných nástrojů.

K dispozici je také možnost procházení dostupných imagí a nabídek pomocí [Azure Marketplace](https://azuremarketplace.microsoft.com/) prezentace, [Azure Portal](https://portal.azure.com)nebo rozhraní příkazového [řádku Azure](../linux/cli-ps-findimage.md). 

 

[!INCLUDE [virtual-machines-common-image-terms](../../../includes/virtual-machines-common-image-terms.md)]

## <a name="table-of-commonly-used-windows-images"></a>Tabulka běžně používaných imagí Windows

Tato tabulka zobrazuje podmnožinu dostupných SKU pro označené vydavatele a nabídky.

| Vydavatel | Nabídka | Skladová jednotka (SKU) |
|:--- |:--- |:--- |
| MicrosoftWindowsServer |WindowsServer |2019 – Datacenter |
| MicrosoftWindowsServer |WindowsServer |2019-Datacenter-Core |
| MicrosoftWindowsServer |WindowsServer |2019-Datacenter-with-Containers |
| MicrosoftWindowsServer |WindowsServer |2016 – Datacenter |
| MicrosoftWindowsServer |WindowsServer |2016-Datacenter-Server-Core |
| MicrosoftWindowsServer |WindowsServer |2016-Datacenter-with-Containers |
| MicrosoftWindowsServer |WindowsServer |2012-R2-Datacenter |
| MicrosoftWindowsServer |WindowsServer |2012-Datacenter |
| MicrosoftDynamicsNAV |DynamicsNAV |2017 |
| MicrosoftSharePoint |MicrosoftSharePointServer |2019 |
| MicrosoftSQLServer |SQL2019-WS2016 |Enterprise |
| MicrosoftRServer |RServer-WS2016 |Enterprise |

## <a name="navigate-the-images"></a>Navigace v obrázcích

Jedním ze způsobů, jak najít obrázek v umístění, je spustit rutiny [Get-AzVMImagePublisher](https://docs.microsoft.com/powershell/module/az.compute/get-azvmimagepublisher), [Get-AzVMImageOffer](https://docs.microsoft.com/powershell/module/az.compute/get-azvmimageoffer)a [Get-AzVMImageSku](https://docs.microsoft.com/powershell/module/az.compute/get-azvmimagesku) v uvedeném pořadí:

1. Vypsat vydavatele imagí.
2. Pro daného vydavatele vypsat jeho nabídky.
3. Pro danou nabídku vypsat její skladovou jednotku (SKU).

Potom pro vybranou SKU spusťte příkaz [Get-AzVMImage](https://docs.microsoft.com/powershell/module/az.compute/get-azvmimage) , který zobrazí seznam verzí k nasazení.

1. Seznam vydavatelů:

    ```powershell
    $locName="<Azure location, such as West US>"
    Get-AzVMImagePublisher -Location $locName | Select PublisherName
    ```

2. Zadejte název zvoleného vydavatele a seznam nabídek:

    ```powershell
    $pubName="<publisher>"
    Get-AzVMImageOffer -Location $locName -PublisherName $pubName | Select Offer
    ```

3. Zadejte název zvolené nabídky a seznam SKU:

    ```powershell
    $offerName="<offer>"
    Get-AzVMImageSku -Location $locName -PublisherName $pubName -Offer $offerName | Select Skus
    ```

4. Vyplňte svůj zvolený název SKU a získejte verzi Image:

    ```powershell
    $skuName="<SKU>"
    Get-AzVMImage -Location $locName -PublisherName $pubName -Offer $offerName -Sku $skuName | Select Version
    ```
    
Z výstupu příkazu `Get-AzVMImage` můžete vybrat bitovou kopii verze k nasazení nového virtuálního počítače.

Následující příklad ukazuje úplnou sekvenci příkazů a jejich výstupy:

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

Pro vydavatele *MicrosoftWindowsServer* :

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

Pro nabídku *windowsserver* :

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

Potom pro SKU *2019 – Datacenter* :

```powershell
$skuName="2019-Datacenter"
Get-AzVMImage -Location $locName -PublisherName $pubName -Offer $offerName -Sku $skuName | Select Version
```

Nyní můžete zkombinovat vybraného vydavatele, nabídky, SKU a verzi do názvu URN (hodnoty oddělené:). Pokud vytvoříte virtuální počítač pomocí rutiny [New-AzVM](https://docs.microsoft.com/powershell/module/az.compute/new-azvm) , předejte tento název URN k parametru `--image`. Pokud chcete získat nejnovější verzi image, můžete volitelně nahradit číslo verze v názvu URN řetězcem "poslední".

Pokud nasadíte virtuální počítač s Správce prostředků šablonou, nastavte parametry obrázku jednotlivě ve vlastnostech `imageReference`. Viz [referenční informace k šablonám](/azure/templates/microsoft.compute/virtualmachines).

[!INCLUDE [virtual-machines-common-marketplace-plan](../../../includes/virtual-machines-common-marketplace-plan.md)]

### <a name="view-plan-properties"></a>Zobrazit vlastnosti plánu

Chcete-li zobrazit informace o plánu nákupu obrázku, spusťte rutinu `Get-AzVMImage`. Pokud vlastnost `PurchasePlan` ve výstupu není `null`, image má podmínky, které je třeba přijmout před programovým nasazením.  

Například bitová kopie *Windows serveru 2016 Datacenter* nemá další výrazy, takže informace o `PurchasePlan` jsou `null`:

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

Níže uvedený příklad ukazuje podobný příkaz pro bitovou kopii *Data Science Virtual Machine-Windows 2016* , která má následující vlastnosti `PurchasePlan`: `name`, `product`a `publisher`. Některé obrázky mají také vlastnost `promotion code`. Chcete-li nasadit tuto bitovou kopii, v následujících částech přijměte podmínky a povolte programové nasazení.

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

### <a name="accept-the-terms"></a>Přijměte podmínky

Pokud si chcete zobrazit licenční smlouvy, použijte rutinu [Get-AzMarketplaceterms](https://docs.microsoft.com/powershell/module/az.marketplaceordering/get-azmarketplaceterms) a předejte ji do parametrů plánu nákupu. Výstup poskytuje odkaz na požadavky na image na webu Marketplace a ukazuje, jestli jste už tyto výrazy přijali. Nezapomeňte v hodnotách parametrů používat všechna malá písmena.

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

Podmínky přijměte nebo odmítněte pomocí rutiny [set-AzMarketplaceterms](https://docs.microsoft.com/powershell/module/az.marketplaceordering/set-azmarketplaceterms) . Pro bitovou kopii musíte pro Image přijmout jenom jednou za odběr. Nezapomeňte v hodnotách parametrů používat všechna malá písmena. 

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

Po přijetí podmínek pro obrázek můžete nasadit virtuální počítač v tomto předplatném. Jak je znázorněno v následujícím fragmentu kódu, použijte rutinu [set-AzVMPlan](https://docs.microsoft.com/powershell/module/az.compute/set-azvmplan) pro nastavení informací o plánu Marketplace pro objekt virtuálního počítače. Úplný skript pro vytvoření nastavení sítě pro virtuální počítač a dokončení nasazení najdete v tématu [Příklady skriptu PowerShellu](powershell-samples.md).

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
Pak předáte konfiguraci virtuálních počítačů spolu s objekty konfigurace sítě do rutiny `New-AzVM`.

## <a name="next-steps"></a>Další kroky

Pokud chcete rychle vytvořit virtuální počítač pomocí rutiny `New-AzVM` pomocí základních informací o imagi, přečtěte si téma [Vytvoření virtuálního počítače s Windows pomocí PowerShellu](quick-create-powershell.md).


Pokud chcete [vytvořit plně nakonfigurovaný virtuální počítač](../scripts/virtual-machines-windows-powershell-sample-create-vm.md), podívejte se na příklad skriptu PowerShellu.


