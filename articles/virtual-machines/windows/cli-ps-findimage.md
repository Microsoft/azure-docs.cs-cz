---
title: Hledání a používání Azure Marketplacech imagí a plánů
description: Pomocí Azure PowerShell můžete najít a použít informace o vydavateli, nabídce, SKU, verzi a plánu pro image virtuálních počítačů Marketplace.
author: cynthn
ms.service: virtual-machines
ms.subservice: imaging
ms.topic: how-to
ms.workload: infrastructure
ms.date: 12/07/2020
ms.author: cynthn
ms.openlocfilehash: 45e6b157dba5ef7410d8a5c0223fd3ecb52f39d0
ms.sourcegitcommit: 80c1056113a9d65b6db69c06ca79fa531b9e3a00
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/09/2020
ms.locfileid: "96906263"
---
# <a name="find-and-use-azure-marketplace-vm-images-with-azure-powershell"></a>Vyhledání a použití imagí Azure Marketplace VM pomocí Azure PowerShell     

Tento článek popisuje, jak použít Azure PowerShell k nalezení imagí virtuálních počítačů v Azure Marketplace. Po vytvoření virtuálního počítače pak můžete zadat image na webu Marketplace a informace o plánu.

K dispozici je také možnost procházení dostupných imagí a nabídek pomocí [Azure Marketplace](https://azuremarketplace.microsoft.com/) prezentace, [Azure Portal](https://portal.azure.com)nebo rozhraní příkazového [řádku Azure](../linux/cli-ps-findimage.md). 


[!INCLUDE [virtual-machines-common-image-terms](../../../includes/virtual-machines-common-image-terms.md)]


## <a name="create-a-vm-from-vhd-with-plan-information"></a>Vytvoření virtuálního počítače z virtuálního pevného disku s informacemi o plánu

Pokud máte existující virtuální pevný disk, který byl vytvořen pomocí Azure Marketplace image, může být nutné při vytváření nového virtuálního počítače z tohoto virtuálního pevného disku dodat informace o plánu nákupu.

Pokud máte i nadále původní virtuální počítač nebo jiný virtuální počítač vytvořený ze stejné image, můžete z něj získat název plánu, vydavatele a informace o produktu pomocí Get-AzVM. Tento příklad načte virtuální počítač s názvem *myVM* ve skupině prostředků *myResourceGroup* a pak zobrazí informace o plánu nákupu.

```azurepowershell-interactive
$vm = Get-azvm `
   -ResourceGroupName myResourceGroup `
   -Name myVM
$vm.Plan
```

Pokud jste neobdrželi informace o plánu před odstraněním původního virtuálního počítače, můžete [požádat o podporu](https://ms.portal.azure.com/#create/Microsoft.Support). Budou potřebovat název virtuálního počítače, ID předplatného a časové razítko operace odstranění.

Pokud chcete vytvořit virtuální počítač pomocí virtuálního pevného disku, přečtěte si článek [Vytvoření virtuálního počítače ze specializovaného virtuálního pevného disku](create-vm-specialized.md) a přidejte ho do konfigurace virtuálního počítače pomocí [set-AzVMPlan](/powershell/module/az.compute/set-azvmplan) , který bude vypadat nějak takto:

```azurepowershell-interactive
$vmConfig = Set-AzVMPlan `
   -VM $vmConfig `
   -Publisher "publisherName" `
   -Product "productName" `
   -Name "planName"
```

## <a name="create-a-new-vm-from-a-marketplace-image"></a>Vytvoření nového virtuálního počítače z image Marketplace

Pokud již máte informace o tom, jakou Image chcete použít, můžete tyto informace předat do rutiny [set-AzVMSourceImage](/powershell/module/az.compute/set-azvmsourceimage) , aby se do konfigurace virtuálního počítače přidaly informace o bitové kopii. V dalších částech najdete informace o hledání a výpisu imagí dostupných na webu Marketplace.

Některé placené obrázky také vyžadují, abyste zadali informace o plánu nákupu pomocí [set-AzVMPlan](/powershell/module/az.compute/set-azvmplan). 

```powershell
...

$vmConfig = New-AzVMConfig -VMName "myVM" -VMSize Standard_D1

# Set the Marketplace image
$offerName = "windows-data-science-vm"
$skuName = "windows2016"
$version = "19.01.14"
$vmConfig = Set-AzVMSourceImage -VM $vmConfig -PublisherName $publisherName -Offer $offerName -Skus $skuName -Version $version

# Set the Marketplace plan information, if needed
$publisherName = "microsoft-ads"
$productName = "windows-data-science-vm"
$planName = "windows2016"
$vmConfig = Set-AzVMPlan -VM $vmConfig -Publisher $publisherName -Product $productName -Name $planName

...
```

Pak do rutiny předáte konfiguraci virtuálního počítače spolu s ostatními objekty konfigurace `New-AzVM` . Podrobný příklad použití konfigurace virtuálního počítače s prostředím PowerShell najdete v tomto [skriptu](https://github.com/Azure/azure-docs-powershell-samples/blob/master/virtual-machine/create-vm-detailed/create-windows-vm-detailed.ps1).

Pokud se vám zobrazí zpráva, jak přijmout podmínky obrázku, přečtěte si část [přijměte podmínky](#accept-the-terms) dále v tomto článku.

## <a name="list-images"></a>Zobrazit obrázky

Jedním ze způsobů, jak najít obrázek v umístění, je spustit rutiny [Get-AzVMImagePublisher](/powershell/module/az.compute/get-azvmimagepublisher), [Get-AzVMImageOffer](/powershell/module/az.compute/get-azvmimageoffer)a [Get-AzVMImageSku](/powershell/module/az.compute/get-azvmimagesku) v uvedeném pořadí:

1. Vypsat vydavatele imagí.
2. Pro daného vydavatele vypsat jeho nabídky.
3. Pro danou nabídku vypsat její skladovou jednotku (SKU).

Potom pro vybranou SKU spusťte příkaz [Get-AzVMImage](/powershell/module/az.compute/get-azvmimage) , který zobrazí seznam verzí k nasazení.

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
    
Z výstupu `Get-AzVMImage` příkazu můžete vybrat bitovou kopii verze k nasazení nového virtuálního počítače.

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

Nyní můžete zkombinovat vybraného vydavatele, nabídky, SKU a verzi do názvu URN (hodnoty oddělené:). `--image`Pokud vytvoříte virtuální počítač pomocí rutiny [New-AzVM](/powershell/module/az.compute/new-azvm) , předejte tento název URN k parametru. Pokud chcete získat nejnovější verzi image, můžete volitelně nahradit číslo verze v názvu URN řetězcem "poslední".

Pokud nasadíte virtuální počítač s Správce prostředků šablonou, nastavte parametry obrázku jednotlivě ve `imageReference` vlastnostech. Viz [referenční informace k šablonám](/azure/templates/microsoft.compute/virtualmachines).

[!INCLUDE [virtual-machines-common-marketplace-plan](../../../includes/virtual-machines-common-marketplace-plan.md)]

### <a name="view-plan-properties"></a>Zobrazit vlastnosti plánu

Chcete-li zobrazit informace o plánu nákupu obrázku, spusťte `Get-AzVMImage` rutinu. Pokud `PurchasePlan` vlastnost ve výstupu není `null` , obrázek obsahuje podmínky, které je třeba přijmout před programovým nasazením.  

Například bitová kopie *Windows serveru 2016 Datacenter* neobsahuje další informace, takže tyto `PurchasePlan` informace `null` :

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

Níže uvedený příklad ukazuje podobný příkaz pro bitovou kopii *Data Science Virtual Machine-Windows 2016* , která má následující `PurchasePlan` vlastnosti: `name` , `product` , a `publisher` . Některé obrázky také obsahují `promotion code` vlastnost. Chcete-li nasadit tuto bitovou kopii, v následujících částech přijměte podmínky a povolte programové nasazení.

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

Pokud si chcete zobrazit licenční smlouvy, použijte rutinu [Get-AzMarketplaceterms](/powershell/module/az.marketplaceordering/get-azmarketplaceterms) a předejte ji do parametrů plánu nákupu. Výstup poskytuje odkaz na požadavky na image na webu Marketplace a ukazuje, jestli jste už tyto výrazy přijali. Nezapomeňte v hodnotách parametrů používat všechna malá písmena.

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

Podmínky přijměte nebo odmítněte pomocí rutiny [set-AzMarketplaceterms](/powershell/module/az.marketplaceordering/set-azmarketplaceterms) . Pro bitovou kopii musíte pro Image přijmout jenom jednou za odběr. Nezapomeňte v hodnotách parametrů používat všechna malá písmena. 

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



## <a name="next-steps"></a>Další kroky

Informace o tom, jak rychle vytvořit virtuální počítač pomocí `New-AzVM` rutiny pomocí základních informací o imagi, najdete v tématu [Vytvoření virtuálního počítače s Windows pomocí PowerShellu](quick-create-powershell.md).

Další informace o použití Azure Marketplace imagí k vytváření vlastních imagí v galerii sdílených imagí najdete v tématu věnovaném [zadávání informací o plánu nákupu Azure Marketplace při vytváření imagí](../marketplace-images.md).
