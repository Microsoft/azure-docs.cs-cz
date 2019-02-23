---
title: Vytvoření vlastní image Azure DevTest Labs ze souboru VHD pomocí Powershellu | Dokumentace Microsoftu
description: Automatizace vytvoření vlastní image ve službě Azure DevTest Labs ze souboru VHD pomocí Powershellu
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
ms.date: 04/05/2018
ms.author: spelluru
ms.openlocfilehash: 7c18ac13b9663ad541ae206347a8df17ff06297c
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/23/2019
ms.locfileid: "56737378"
---
# <a name="create-a-custom-image-from-a-vhd-file-using-powershell"></a>Vytvoření vlastní image ze souboru VHD pomocí Powershellu

[!INCLUDE [devtest-lab-create-custom-image-from-vhd-selector](../../includes/devtest-lab-create-custom-image-from-vhd-selector.md)]

[!INCLUDE [devtest-lab-custom-image-definition](../../includes/devtest-lab-custom-image-definition.md)]

[!INCLUDE [devtest-lab-upload-vhd-options](../../includes/devtest-lab-upload-vhd-options.md)]

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="step-by-step-instructions"></a>Podrobné pokyny

Následující kroky vás provedou procesem vytvoření vlastní image ze souboru VHD pomocí Powershellu:

1. Na příkazový řádek Powershellu, přihlaste se ke svému účtu Azure pomocí následujícího volání **připojit AzAccount** rutiny.  
    
    ```PowerShell
    Connect-AzAccount
    ```

1.  Vyberte požadované předplatné Azure voláním **vyberte AzSubscription** rutiny. Nahraďte následující zástupnou hodnotu **$subscriptionId** proměnné s ID platné předplatné Azure. 

    ```PowerShell
    $subscriptionId = '<Specify your subscription ID here>'
    Select-AzSubscription -SubscriptionId $subscriptionId
    ```

1.  Získejte objekt služby testovacího prostředí pomocí volání **Get-AzResource** rutiny. Nahraďte následující zástupné symboly **$labRg** a **$labName** proměnné s příslušnými hodnotami pro vaše prostředí. 

    ```PowerShell
    $labRg = '<Specify your lab resource group name here>'
    $labName = '<Specify your lab name here>'
    $lab = Get-AzResource -ResourceId ('/subscriptions/' + $subscriptionId + '/resourceGroups/' + $labRg + '/providers/Microsoft.DevTestLab/labs/' + $labName)
    ```
 
1.  Získáte úložiště účtu a testovacího prostředí úložiště klíčových hodnot účtu testovacího prostředí z objektu testovacího prostředí. 

    ```PowerShell
    $labStorageAccount = Get-AzResource -ResourceId $lab.Properties.defaultStorageAccount 
    $labStorageAccountKey = (Get-AzStorageAccountKey -ResourceGroupName $labStorageAccount.ResourceGroupName -Name $labStorageAccount.ResourceName)[0].Value
    ```

1.  Nahraďte následující zástupnou hodnotu **$vhdUri** proměnné s identifikátorem URI pro nahraný soubor virtuálního pevného disku. Identifikátor URI souboru VHD můžete získat z okna účet úložiště blob na webu Azure Portal.

    ```PowerShell
    $vhdUri = '<Specify the VHD URI here>'
    ```

1.  Vytvoření s použitím vlastní image **New-AzResourceGroupDeployment** rutiny. Nahraďte následující zástupné symboly **$customImageName** a **$customImageDescription** proměnné smysluplné názvy pro vaše prostředí.

    ```PowerShell
    $customImageName = '<Specify the custom image name>'
    $customImageDescription = '<Specify the custom image description>'

    $parameters = @{existingLabName="$($lab.Name)"; existingVhdUri=$vhdUri; imageOsType='windows'; isVhdSysPrepped=$false; imageName=$customImageName; imageDescription=$customImageDescription}

    New-AzResourceGroupDeployment -ResourceGroupName $lab.ResourceGroupName -Name CreateCustomImage -TemplateUri 'https://raw.githubusercontent.com/Azure/azure-devtestlab/master/Samples/201-dtl-create-customimage-from-vhd/azuredeploy.json' -TemplateParameterObject $parameters
    ```

## <a name="powershell-script-to-create-a-custom-image-from-a-vhd-file"></a>Skript Powershellu pro vytvoření vlastní image ze souboru VHD

Následující skript prostředí PowerShell slouží k vytvoření vlastní image ze souboru virtuálního pevného disku. Nahraďte zástupné symboly (počáteční a koncovou pomocí ostrých závorek) odpovídajícími hodnotami pro vaše potřeby. 

```PowerShell
# Log in to your Azure account.  
Connect-AzAccount

# Select the desired Azure subscription. 
$subscriptionId = '<Specify your subscription ID here>'
Select-AzSubscription -SubscriptionId $subscriptionId

# Get the lab object.
$labRg = '<Specify your lab resource group name here>'
$labName = '<Specify your lab name here>'
$lab = Get-AzResource -ResourceId ('/subscriptions/' + $subscriptionId + '/resourceGroups/' + $labRg + '/providers/Microsoft.DevTestLab/labs/' + $labName)

# Get the lab storage account and lab storage account key values.
$labStorageAccount = Get-AzResource -ResourceId $lab.Properties.defaultStorageAccount 
$labStorageAccountKey = (Get-AzStorageAccountKey -ResourceGroupName $labStorageAccount.ResourceGroupName -Name $labStorageAccount.ResourceName)[0].Value

# Set the URI of the VHD file.  
$vhdUri = '<Specify the VHD URI here>'

# Set the custom image name and description values.
$customImageName = '<Specify the custom image name>'
$customImageDescription = '<Specify the custom image description>'

# Set up the parameters object.
$parameters = @{existingLabName="$($lab.Name)"; existingVhdUri=$vhdUri; imageOsType='windows'; isVhdSysPrepped=$false; imageName=$customImageName; imageDescription=$customImageDescription}

# Create the custom image. 
New-AzResourceGroupDeployment -ResourceGroupName $lab.ResourceGroupName -Name CreateCustomImage -TemplateUri 'https://raw.githubusercontent.com/Azure/azure-devtestlab/master/Samples/201-dtl-create-customimage-from-vhd/azuredeploy.json' -TemplateParameterObject $parameters
```

## <a name="related-blog-posts"></a>Související blogové příspěvky

- [Vlastní Image nebo vzorce?](https://blogs.msdn.microsoft.com/devtestlab/2016/04/06/custom-images-or-formulas/)
- [Kopírování vlastních Imagí mezi Azure DevTest Labs](http://www.visualstudiogeeks.com/blog/DevOps/How-To-Move-CustomImages-VHD-Between-AzureDevTestLabs#copying-custom-images-between-azure-devtest-labs)

## <a name="next-steps"></a>Další postup

- [Přidání virtuálního počítače do testovacího prostředí](devtest-lab-add-vm.md)
