---
title: Vytvoření vlastní image ze souboru VHD pomocí Azure PowerShellu
description: Automatizace vytváření vlastní image v Azure DevTest Labs ze souboru Virtuálního pevného disku pomocí PowerShellu
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.assetid: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/16/2020
ms.author: spelluru
ms.openlocfilehash: cd144659dd8a8e981e267be998c9c783b7482840
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76169577"
---
# <a name="create-a-custom-image-from-a-vhd-file-using-powershell"></a>Vytvoření vlastního obrazu ze souboru VHD pomocí PowerShellu

[!INCLUDE [devtest-lab-create-custom-image-from-vhd-selector](../../includes/devtest-lab-create-custom-image-from-vhd-selector.md)]

[!INCLUDE [devtest-lab-custom-image-definition](../../includes/devtest-lab-custom-image-definition.md)]

[!INCLUDE [devtest-lab-upload-vhd-options](../../includes/devtest-lab-upload-vhd-options.md)]

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="step-by-step-instructions"></a>Podrobné pokyny

Následující kroky vás provedou vytvořením vlastního obrazu ze souboru VHD pomocí prostředí PowerShell:

1. Na výzvu PowerShellu se přihlaste ke svému účtu Azure pomocí následujícího volání rutiny **Connect-AzAccount.**

    ```powershell
    Connect-AzAccount
    ```

1.  Vyberte požadované předplatné Azure voláním rutiny **Select-AzSubscription.** Nahraďte následující zástupný symbol pro **proměnnou $subscriptionId** platným ID předplatného Azure.

    ```powershell
    $subscriptionId = '<Specify your subscription ID here>'
    Select-AzSubscription -SubscriptionId $subscriptionId
    ```

1.  Získejte objekt testovacího prostředí voláním rutiny **Get-AzResource.** Nahraďte následující zástupné symboly pro **proměnné $labRg** a **$labName** odpovídajícími hodnotami pro vaše prostředí.

    ```powershell
    $labRg = '<Specify your lab resource group name here>'
    $labName = '<Specify your lab name here>'
    $lab = Get-AzResource -ResourceId ('/subscriptions/' + $subscriptionId + '/resourceGroups/' + $labRg + '/providers/Microsoft.DevTestLab/labs/' + $labName)
    ```

1.  Nahraďte následující zástupný symbol **pro proměnnou $vhdUri** identifikátorem URI na nahraný soubor VHD. Identifikátor URI souboru VHD můžete získat z rozhraní blob účtu úložiště na webu Azure Portal.

    ```powershell
    $vhdUri = '<Specify the VHD URI here>'
    ```

1.  Vytvořte vlastní bitovou kopii pomocí rutiny **New-AzResourceGroupDeployment.** Nahraďte následující zástupné symboly pro **$customImageName** a **$customImageDescription** proměnných smysluplnými názvy pro vaše prostředí.

    ```powershell
    $customImageName = '<Specify the custom image name>'
    $customImageDescription = '<Specify the custom image description>'

    $parameters = @{existingLabName="$($lab.Name)"; existingVhdUri=$vhdUri; imageOsType='windows'; isVhdSysPrepped=$false; imageName=$customImageName; imageDescription=$customImageDescription}

    New-AzResourceGroupDeployment -ResourceGroupName $lab.ResourceGroupName -Name CreateCustomImage -TemplateUri 'https://raw.githubusercontent.com/Azure/azure-devtestlab/master/samples/DevTestLabs/QuickStartTemplates/201-dtl-create-customimage-from-vhd/azuredeploy.json' -TemplateParameterObject $parameters
    ```

## <a name="powershell-script-to-create-a-custom-image-from-a-vhd-file"></a>Skript PowerShellu pro vytvoření vlastního obrazu ze souboru VHD

Následující skript prostředí PowerShell lze použít k vytvoření vlastní image ze souboru VHD. Nahraďte zástupné symboly (počínaje a končící úhlovými závorkami) odpovídajícími hodnotami pro vaše potřeby.

```powershell
# Log in to your Azure account.
Connect-AzAccount

# Select the desired Azure subscription.
$subscriptionId = '<Specify your subscription ID here>'
Select-AzSubscription -SubscriptionId $subscriptionId

# Get the lab object.
$labRg = '<Specify your lab resource group name here>'
$labName = '<Specify your lab name here>'
$lab = Get-AzResource -ResourceId ('/subscriptions/' + $subscriptionId + '/resourceGroups/' + $labRg + '/providers/Microsoft.DevTestLab/labs/' + $labName)

# Set the URI of the VHD file.
$vhdUri = '<Specify the VHD URI here>'

# Set the custom image name and description values.
$customImageName = '<Specify the custom image name>'
$customImageDescription = '<Specify the custom image description>'

# Set up the parameters object.
$parameters = @{existingLabName="$($lab.Name)"; existingVhdUri=$vhdUri; imageOsType='windows'; isVhdSysPrepped=$false; imageName=$customImageName; imageDescription=$customImageDescription}

# Create the custom image.
New-AzResourceGroupDeployment -ResourceGroupName $lab.ResourceGroupName -Name CreateCustomImage -TemplateUri 'https://raw.githubusercontent.com/Azure/azure-devtestlab/master/samples/DevTestLabs/QuickStartTemplates/201-dtl-create-customimage-from-vhd/azuredeploy.json' -TemplateParameterObject $parameters
```

## <a name="related-blog-posts"></a>Související příspěvky blogu

- [Vlastní obrázky nebo vzorce?](https://blogs.msdn.microsoft.com/devtestlab/2016/04/06/custom-images-or-formulas/)
- [Kopírování vlastních ibi mezi azure devtest labs](https://www.visualstudiogeeks.com/blog/DevOps/How-To-Move-CustomImages-VHD-Between-AzureDevTestLabs#copying-custom-images-between-azure-devtest-labs)

## <a name="next-steps"></a>Další kroky

- [Přidání virtuálního počítače do testovacího prostředí](devtest-lab-add-vm.md)
