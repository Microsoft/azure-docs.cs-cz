---
title: Vytvoření vlastní image ze souboru VHD pomocí Azure PowerShell
description: Automatizace vytvoření vlastní image v Azure DevTest Labs ze souboru VHD pomocí PowerShellu
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 4b0712fdbec1ce23ad9e09d972e425cb7941107b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "87288979"
---
# <a name="create-a-custom-image-from-a-vhd-file-using-powershell"></a>Vytvoření vlastní image ze souboru VHD pomocí PowerShellu

[!INCLUDE [devtest-lab-create-custom-image-from-vhd-selector](../../includes/devtest-lab-create-custom-image-from-vhd-selector.md)]

[!INCLUDE [devtest-lab-custom-image-definition](../../includes/devtest-lab-custom-image-definition.md)]

[!INCLUDE [devtest-lab-upload-vhd-options](../../includes/devtest-lab-upload-vhd-options.md)]

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="step-by-step-instructions"></a>Podrobné pokyny

Následující kroky vás provedou vytvořením vlastní image ze souboru VHD pomocí PowerShellu:

1. Na příkazovém řádku PowerShellu se přihlaste ke svému účtu Azure pomocí následujícího volání rutiny **Connect-AzAccount** .

    ```powershell
    Connect-AzAccount
    ```

1.  Vyberte požadované předplatné Azure voláním rutiny **Select-AzSubscription** . Nahraďte následující zástupný symbol pro **$SubscriptionId** proměnnou s PLATNÝm ID předplatného Azure.

    ```powershell
    $subscriptionId = '<Specify your subscription ID here>'
    Select-AzSubscription -SubscriptionId $subscriptionId
    ```

1.  Získejte objekt testovacího prostředí voláním rutiny **Get-AzResource** . Nahraďte následující zástupné symboly **$labRg** a **$labName** proměnných odpovídajícími hodnotami pro vaše prostředí.

    ```powershell
    $labRg = '<Specify your lab resource group name here>'
    $labName = '<Specify your lab name here>'
    $lab = Get-AzResource -ResourceId ('/subscriptions/' + $subscriptionId + '/resourceGroups/' + $labRg + '/providers/Microsoft.DevTestLab/labs/' + $labName)
    ```

1.  U proměnné **$vhdUri** nahraďte následující zástupný symbol identifikátorem URI nahraného souboru VHD. Identifikátor URI souboru VHD můžete získat z okna objektu BLOB účtu úložiště v Azure Portal.

    ```powershell
    $vhdUri = '<Specify the VHD URI here>'
    ```

1.  Vytvořte vlastní image pomocí rutiny **New-AzResourceGroupDeployment** . Nahraďte následující zástupné symboly **$customImageName** a **$customImageDescription** proměnných podle smysluplných názvů pro vaše prostředí.

    ```powershell
    $customImageName = '<Specify the custom image name>'
    $customImageDescription = '<Specify the custom image description>'

    $parameters = @{existingLabName="$($lab.Name)"; existingVhdUri=$vhdUri; imageOsType='windows'; isVhdSysPrepped=$false; imageName=$customImageName; imageDescription=$customImageDescription}

    New-AzResourceGroupDeployment -ResourceGroupName $lab.ResourceGroupName -Name CreateCustomImage -TemplateUri 'https://raw.githubusercontent.com/Azure/azure-devtestlab/master/samples/DevTestLabs/QuickStartTemplates/201-dtl-create-customimage-from-vhd/azuredeploy.json' -TemplateParameterObject $parameters
    ```

## <a name="powershell-script-to-create-a-custom-image-from-a-vhd-file"></a>Skript PowerShellu pro vytvoření vlastní image ze souboru VHD

Následující skript PowerShellu se dá použít k vytvoření vlastní image ze souboru VHD. Zástupné symboly (počínaje a končící lomenými závorkami) nahraďte odpovídajícími hodnotami podle vašich potřeb.

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

## <a name="related-blog-posts"></a>Související blogové příspěvky

- [Vlastní obrázky nebo vzorce?](./devtest-lab-faq.md#blog-post)
- [Kopírování vlastních imagí mezi Azure DevTest Labs](https://www.visualstudiogeeks.com/blog/DevOps/How-To-Move-CustomImages-VHD-Between-AzureDevTestLabs#copying-custom-images-between-azure-devtest-labs)

## <a name="next-steps"></a>Další kroky

- [Přidání virtuálního počítače do testovacího prostředí](devtest-lab-add-vm.md)
