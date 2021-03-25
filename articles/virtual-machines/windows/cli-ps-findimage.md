---
title: Vyhledání a použití informací o plánu nákupu na webu Marketplace pomocí PowerShellu
description: Pomocí Azure PowerShell můžete najít parametry plánu image urny a nákup, jako je Vydavatel, nabídka, SKU a verze, pro image virtuálních počítačů Marketplace.
author: cynthn
ms.service: virtual-machines
ms.subservice: imaging
ms.topic: how-to
ms.workload: infrastructure
ms.date: 03/17/2021
ms.author: cynthn
ms.custom: contperf-fy21q3
ms.openlocfilehash: 34fd6720b93a1462836b51856d73573a86809367
ms.sourcegitcommit: a8ff4f9f69332eef9c75093fd56a9aae2fe65122
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2021
ms.locfileid: "105022819"
---
# <a name="find-and-use-azure-marketplace-vm-images-with-azure-powershell"></a>Vyhledání a použití imagí Azure Marketplace VM pomocí Azure PowerShell

Tento článek popisuje, jak použít Azure PowerShell k nalezení imagí virtuálních počítačů v Azure Marketplace. Po vytvoření virtuálního počítače pak můžete zadat image na webu Marketplace a informace o plánu.

K dispozici je také možnost procházení dostupných imagí a nabídek pomocí [Azure Marketplace](https://azuremarketplace.microsoft.com/) nebo rozhraní příkazového [řádku Azure](../linux/cli-ps-findimage.md). 

## <a name="terminology"></a>Terminologie

Image na Marketplace v Azure má následující atributy:

* **Vydavatel**: organizace, která bitovou kopii vytvořila. Příklady: Canonical, MicrosoftWindowsServer
* **Nabídka**: název skupiny souvisejících imagí vytvořených vydavatelem. Příklady: UbuntuServer, WindowsServer
* **SKU**: instance nabídky, jako je například hlavní verze distribuce. Příklady: 18,04-LTS, 2019-Datacenter
* **Version (verze**): číslo verze SKU image. 

Tyto hodnoty je možné předat individuálně nebo jako *název URN* obrázku a kombinovat hodnoty oddělené dvojtečkou (:). Příklad: *Vydavatel*:*Nabídka*:*SKU*:*verze*. Číslo verze v názvu URN můžete nahradit, pokud `latest` chcete použít nejnovější verzi image. 

Pokud Vydavatel obrázku poskytuje další licenční a nákupní podmínky, musíte je přijmout, než budete moct image použít.  Další informace najdete v tématu [přijetí podmínek plánu nákupu](#accept-purchase-plan-terms).

## <a name="list-images"></a>Zobrazit obrázky

K zúžení seznamu imagí můžete použít PowerShell. Hodnoty proměnných nahraďte odpovídajícími vašimi požadavky.

1. Seznam vydavatelů obrázků pomocí [Get-AzVMImagePublisher](/powershell/module/az.compute/get-azvmimagepublisher).
    
    ```powershell
    $locName="<location>"
    Get-AzVMImagePublisher -Location $locName | Select PublisherName
    ```
1. Pro daného vydavatele uveďte jejich nabídky pomocí [Get-AzVMImageOffer](/powershell/module/az.compute/get-azvmimageoffer).
    
    ```powershell
    $pubName="<publisher>"
    Get-AzVMImageOffer -Location $locName -PublisherName $pubName | Select Offer
    ```
1. Pro daného vydavatele a nabídku uveďte seznam SKU k dispozici pomocí [Get-AzVMImageSku](/powershell/module/az.compute/get-azvmimagesku).
    
    ```powershell
    $offerName="<offer>"
    Get-AzVMImageSku -Location $locName -PublisherName $pubName -Offer $offerName | Select Skus
    ```
1. V případě SKU uveďte verze Image pomocí [Get-AzVMImage](/powershell/module/az.compute/get-azvmimage).

    ```powershell
    $skuName="<SKU>"
    Get-AzVMImage -Location $locName -PublisherName $pubName -Offer $offerName -Sku $skuName | Select Version
    ```
    Můžete použít také `latest` v případě, že chcete použít nejnovější bitovou kopii a ne na konkrétní starší verzi.


Nyní můžete zkombinovat vybraného vydavatele, nabídky, SKU a verzi do názvu URN (hodnoty oddělené:). `--image`Pokud vytvoříte virtuální počítač pomocí rutiny [New-AzVM](/powershell/module/az.compute/new-azvm) , předejte tento název URN k parametru. Můžete také nahradit číslo verze v názvu URN `latest` a získat tak nejnovější verzi image.

Pokud nasadíte virtuální počítač s Správce prostředků šablonou, nastavte parametry obrázku jednotlivě ve `imageReference` vlastnostech. Viz [referenční informace k šablonám](/azure/templates/microsoft.compute/virtualmachines).


## <a name="view-purchase-plan-properties"></a>Zobrazit vlastnosti plánu nákupu

Některé image virtuálních počítačů v Azure Marketplace mají další licenční a nákupní podmínky, které musíte přijmout, než je můžete nasadit programově. U každého předplatného budete muset přijmout podmínky pro image.

Chcete-li zobrazit informace o plánu nákupu obrázku, spusťte `Get-AzVMImage` rutinu. Pokud `PurchasePlan` vlastnost ve výstupu není `null` , obrázek obsahuje podmínky, které je třeba přijmout před programovým nasazením.  

Například bitová kopie *Windows serveru 2016 Datacenter* neobsahuje další informace, takže tyto `PurchasePlan` informace `null` :

```powershell
$version = "2016.127.20170406"
Get-AzVMImage -Location $locName -PublisherName $pubName -Offer $offerName -Skus $skuName -Version $version
```

Výstup bude vypadat podobně jako v následujícím příkladu:

```output
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

Výstup bude vypadat podobně jako v následujícím příkladu:

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

Pokud si chcete zobrazit licenční smlouvy, použijte rutinu [Get-AzMarketplaceterms](/powershell/module/az.marketplaceordering/get-azmarketplaceterms) a předejte ji do parametrů plánu nákupu. Výstup poskytuje odkaz na požadavky na image na webu Marketplace a ukazuje, jestli jste už tyto výrazy přijali. Nezapomeňte v hodnotách parametrů používat všechna malá písmena.

```powershell
Get-AzMarketplaceterms -Publisher "microsoft-ads" -Product "windows-data-science-vm" -Name "windows2016"
```

Výstup bude vypadat podobně jako v následujícím příkladu:

```output
Publisher         : microsoft-ads
Product           : windows-data-science-vm
Plan              : windows2016
LicenseTextLink   : https://storelegalterms.blob.core.windows.net/legalterms/3E5ED_legalterms_MICROSOFT%253a2DADS%253a24WINDOWS%253a2DDATA%253a2DSCIENCE%253a2DVM%253a24WINDOWS2016%253a24OC5SKMQOXSED66BBSNTF4XRCS4XLOHP7QMPV54DQU7JCBZWYFP35IDPOWTUKXUC7ZAG7W6ZMDD6NHWNKUIVSYBZUTZ245F44SU5AD7Q.txt
PrivacyPolicyLink : https://www.microsoft.com/EN-US/privacystatement/OnlineServices/Default.aspx
Signature         : 2UMWH6PHSAIM4U22HXPXW25AL2NHUJ7Y7GRV27EBL6SUIDURGMYG6IIDO3P47FFIBBDFHZHSQTR7PNK6VIIRYJRQ3WXSE6BTNUNENXA
Accepted          : False
Signdate          : 1/25/2019 7:43:00 PM
```

## <a name="accept-purchase-plan-terms"></a>Přijmout podmínky plánu nákupu

Podmínky přijměte nebo odmítněte pomocí rutiny [set-AzMarketplaceterms](/powershell/module/az.marketplaceordering/set-azmarketplaceterms) . Pro bitovou kopii musíte pro Image přijmout jenom jednou za odběr. Nezapomeňte v hodnotách parametrů používat všechna malá písmena. 

```powershell
$agreementTerms=Get-AzMarketplaceterms -Publisher "microsoft-ads" -Product "windows-data-science-vm" -Name "windows2016"

Set-AzMarketplaceTerms -Publisher "microsoft-ads" -Product "windows-data-science-vm" -Name "windows2016" -Terms $agreementTerms -Accept
```



```output
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

Pokud se vám zobrazí zpráva týkající se přijetí podmínek obrázku, přečtěte si článek o přijetí [podmínek nákupu](#accept-purchase-plan-terms)v předchozí části.

## <a name="create-a-new-vm-from-a-vhd-with-purchase-plan-information"></a>Vytvoření nového virtuálního počítače z virtuálního pevného disku s informacemi o plánu nákupu

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


## <a name="next-steps"></a>Další kroky

Informace o tom, jak rychle vytvořit virtuální počítač pomocí `New-AzVM` rutiny pomocí základních informací o imagi, najdete v tématu [Vytvoření virtuálního počítače s Windows pomocí PowerShellu](quick-create-powershell.md).

Další informace o použití Azure Marketplace imagí k vytváření vlastních imagí v galerii sdílených imagí najdete v tématu věnovaném [zadávání informací o plánu nákupu Azure Marketplace při vytváření imagí](../marketplace-images.md).
