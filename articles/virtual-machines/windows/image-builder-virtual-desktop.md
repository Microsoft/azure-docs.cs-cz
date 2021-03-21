---
title: Image Builder – vytvoření image virtuálního počítače s Windows
description: Pomocí nástroje Azure image Builder v PowerShellu Vytvořte bitovou kopii virtuálního počítače Azure pro virtuální počítače s Windows.
author: danielsollondon
ms.author: danis
ms.reviewer: cynthn
ms.date: 01/27/2021
ms.topic: article
ms.service: virtual-machines-windows
ms.collection: windows
ms.subservice: imaging
ms.openlocfilehash: 01b253747791fc29abf4434bebfd85865099f9ee
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "103602014"
---
# <a name="create-a-windows-virtual-desktop-image-using-azure-vm-image-builder-and-powershell"></a>Vytvoření image virtuálního počítače s Windows pomocí nástroje Azure VM Image Builder a PowerShellu

V tomto článku se dozvíte, jak vytvořit image virtuálního počítače s Windows pomocí těchto úprav:

* Instaluje se [FsLogix](https://github.com/DeanCefola/Azure-WVD/blob/master/PowerShell/FSLogixSetup.ps1).
* Spouští se [skript optimalizace virtuálních počítačů s Windows](https://github.com/The-Virtual-Desktop-Team/Virtual-Desktop-Optimization-Tool) z úložiště komunity.
* Nainstalujte [Microsoft Teams](https://docs.microsoft.com/azure/virtual-desktop/teams-on-wvd).
* [Restartovat](https://docs.microsoft.com/azure/virtual-machines/linux/image-builder-json?toc=%2Fazure%2Fvirtual-machines%2Fwindows%2Ftoc.json&bc=%2Fazure%2Fvirtual-machines%2Fwindows%2Fbreadcrumb%2Ftoc.json#windows-restart-customizer)
* Spustit [web Windows Update](https://docs.microsoft.com/azure/virtual-machines/linux/image-builder-json?toc=%2Fazure%2Fvirtual-machines%2Fwindows%2Ftoc.json&bc=%2Fazure%2Fvirtual-machines%2Fwindows%2Fbreadcrumb%2Ftoc.json#windows-update-customizer)

Ukážeme vám, jak to automatizovat pomocí Tvůrce imagí virtuálních počítačů Azure, a distribuovat image do [Galerie sdílených imagí](https://docs.microsoft.com/azure/virtual-machines/windows/shared-image-galleries), kde můžete replikovat do jiných oblastí, řídit škálování a sdílet image v rámci organizací i mimo ně.


V tomto příkladu se pro zjednodušení nasazení konfigurace tvůrce imagí používá šablona Azure Resource Manager s vnořenou šablonou tvůrce imagí. To vám dává nějaké další výhody, jako jsou proměnné a vstupy parametrů. Můžete také předat parametry z příkazového řádku.

Tento článek je určený jako kopírování a vkládání cvičení.

> [!NOTE]
> Skripty pro instalaci aplikací jsou umístěné na [GitHubu](https://github.com/danielsollondon/azvmimagebuilder/tree/master/solutions/14_Building_Images_WVD). Slouží pouze k ilustraci a testování, nikoli pro produkční úlohy. 

## <a name="tips-for-building-windows-images"></a>Tipy pro vytváření imagí Windows 

- Velikost virtuálního počítače – výchozí velikost virtuálního počítače je `Standard_D1_v2` , což není vhodné pro Windows. Použijte `Standard_D2_v2` nebo vyšší.
- V tomto příkladu se používají [skripty prostředí PowerShell pro přizpůsobení](../linux/image-builder-json.md). Je nutné použít tato nastavení, jinak sestavení přestane reagovat.

    ```json
      "runElevated": true,
      "runAsSystem": true,
    ```

    Například:

    ```json
      {
          "type": "PowerShell",
          "name": "installFsLogix",
          "runElevated": true,
          "runAsSystem": true,
          "scriptUri": "https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/solutions/14_Building_Images_WVD/0_installConfFsLogix.ps1"
    ```
- Komentář ke kódu – protokol sestavení AIB (přizpůsobení. log) je extrémně podrobný, pokud komentář ke skriptům pomocí příkazu write-host se pošle do protokolů a usnadní se odstraňování potíží.

    ```PowerShell
     write-host 'AIB Customization: Starting OS Optimizations script'
    ```

- Ukončovací kódy – AIB očekává, že všechny skripty vrátí 0 ukončovacího kódu, jakýkoli nenulový ukončovací kód způsobí AIB selhání přizpůsobení a zastavení sestavení. Pokud máte složité skripty, přidejte kódy ukončení instrumentace a generování, které se zobrazí v části přizpůsobení. log.

    ```PowerShell
     Write-Host "Exit code: " $LASTEXITCODE
    ```
- Test: testování a testování kódu před samostatným virtuálním počítačem, zajistěte, aby nedocházelo k žádnému uživateli, používáte správné oprávnění atd.

- Sítě – `Set-NetAdapterAdvancedProperty` . Tato funkce se nastavuje ve skriptu optimalizace, ale AIB sestavení se nezdařila, protože odpojuje síť, jedná se o komentář. Probíhá šetření.

## <a name="prerequisites"></a>Předpoklady

Musíte mít nainstalované nejnovější rutiny Azure PowerShell. Podrobnosti o instalaci najdete [tady](https://docs.microsoft.com/powershell/azure/overview) :.

```PowerShell
# Register for Azure Image Builder Feature
Register-AzProviderFeature -FeatureName VirtualMachineTemplatePreview -ProviderNamespace Microsoft.VirtualMachineImages

Get-AzProviderFeature -FeatureName VirtualMachineTemplatePreview -ProviderNamespace Microsoft.VirtualMachineImages

# wait until RegistrationState is set to 'Registered'

# check you are registered for the providers, ensure RegistrationState is set to 'Registered'.
Get-AzResourceProvider -ProviderNamespace Microsoft.VirtualMachineImages
Get-AzResourceProvider -ProviderNamespace Microsoft.Storage 
Get-AzResourceProvider -ProviderNamespace Microsoft.Compute
Get-AzResourceProvider -ProviderNamespace Microsoft.KeyVault

# If they do not saw registered, run the commented out code below.

## Register-AzResourceProvider -ProviderNamespace Microsoft.VirtualMachineImages
## Register-AzResourceProvider -ProviderNamespace Microsoft.Storage
## Register-AzResourceProvider -ProviderNamespace Microsoft.Compute
## Register-AzResourceProvider -ProviderNamespace Microsoft.KeyVault
```

## <a name="set-up-environment-and-variables"></a>Nastavení prostředí a proměnných

```azurepowershell-interactive
# Step 1: Import module
Import-Module Az.Accounts

# Step 2: get existing context
$currentAzContext = Get-AzContext

# destination image resource group
$imageResourceGroup="wvdImageDemoRg"

# location (see possible locations in main docs)
$location="westus2"

# your subscription, this will get your current subscription
$subscriptionID=$currentAzContext.Subscription.Id

# image template name
$imageTemplateName="wvd10ImageTemplate01"

# distribution properties object name (runOutput), i.e. this gives you the properties of the managed image on completion
$runOutputName="sigOutput"

# create resource group
New-AzResourceGroup -Name $imageResourceGroup -Location $location
```

## <a name="permissions-user-identity-and-role"></a>Oprávnění, identita a role uživatele 


 Vytvořte identitu uživatele.

```azurepowershell-interactive
# setup role def names, these need to be unique
$timeInt=$(get-date -UFormat "%s")
$imageRoleDefName="Azure Image Builder Image Def"+$timeInt
$idenityName="aibIdentity"+$timeInt

## Add AZ PS modules to support AzUserAssignedIdentity and Az AIB
'Az.ImageBuilder', 'Az.ManagedServiceIdentity' | ForEach-Object {Install-Module -Name $_ -AllowPrerelease}

# create identity
New-AzUserAssignedIdentity -ResourceGroupName $imageResourceGroup -Name $idenityName

$idenityNameResourceId=$(Get-AzUserAssignedIdentity -ResourceGroupName $imageResourceGroup -Name $idenityName).Id
$idenityNamePrincipalId=$(Get-AzUserAssignedIdentity -ResourceGroupName $imageResourceGroup -Name $idenityName).PrincipalId

```

Přiřaďte k identitě oprávnění k distribuci imagí. Tento příkaz stáhne a aktualizuje šablonu s parametry zadanými dříve.

```azurepowershell-interactive
$aibRoleImageCreationUrl="https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/solutions/12_Creating_AIB_Security_Roles/aibRoleImageCreation.json"
$aibRoleImageCreationPath = "aibRoleImageCreation.json"

# download config
Invoke-WebRequest -Uri $aibRoleImageCreationUrl -OutFile $aibRoleImageCreationPath -UseBasicParsing

((Get-Content -path $aibRoleImageCreationPath -Raw) -replace '<subscriptionID>',$subscriptionID) | Set-Content -Path $aibRoleImageCreationPath
((Get-Content -path $aibRoleImageCreationPath -Raw) -replace '<rgName>', $imageResourceGroup) | Set-Content -Path $aibRoleImageCreationPath
((Get-Content -path $aibRoleImageCreationPath -Raw) -replace 'Azure Image Builder Service Image Creation Role', $imageRoleDefName) | Set-Content -Path $aibRoleImageCreationPath

# create role definition
New-AzRoleDefinition -InputFile  ./aibRoleImageCreation.json

# grant role definition to image builder service principal
New-AzRoleAssignment -ObjectId $idenityNamePrincipalId -RoleDefinitionName $imageRoleDefName -Scope "/subscriptions/$subscriptionID/resourceGroups/$imageResourceGroup"
```

> [!NOTE] 
> Pokud se zobrazí tato chyba: ' New-AzRoleDefinition: překročil se limit definice role. Nelze vytvořit žádné další definice rolí. ' řešení najdete v tomto článku: https://docs.microsoft.com/azure/role-based-access-control/troubleshooting .



## <a name="create-the-shared-image-gallery"></a>Vytvoření galerie sdílených imagí 

Pokud ještě nemáte galerii sdílených imagí, je potřeba ji vytvořit.

```azurepowershell-interactive
$sigGalleryName= "myaibsig01"
$imageDefName ="win10wvd"

# create gallery
New-AzGallery -GalleryName $sigGalleryName -ResourceGroupName $imageResourceGroup  -Location $location

# create gallery definition
New-AzGalleryImageDefinition -GalleryName $sigGalleryName -ResourceGroupName $imageResourceGroup -Location $location -Name $imageDefName -OsState generalized -OsType Windows -Publisher 'myCo' -Offer 'Windows' -Sku '10wvd'

```

## <a name="configure-the-image-template"></a>Konfigurace šablony obrázku

V tomto příkladu máme šablonu připravenou k tomu, aby si stáhla a aktualizovala šablonu s výše zadaným parametrem, nainstaluje FsLogix, optimalizace OS, Microsoft teams a spustí web Windows Update na konci.

Pokud otevřete šablonu, která se zobrazí v části zdrojová vlastnost, která se používá, v tomto příkladu se používá obrázek s více relacemi Win 10. 

### <a name="windows-10-images"></a>Image Windows 10
Existují dva typy klíčů, o kterých byste měli vědět: víc relací a jedna relace.

Obrázky s více relacemi jsou určené pro použití ve fondu. Tady je příklad podrobností obrázku v Azure:

```json
"publisher": "MicrosoftWindowsDesktop",
"offer": "Windows-10",
"sku": "20h2-evd",
"version": "latest"
```

Obrázky jedné relace jsou určené k individuálnímu využití. Tady je příklad podrobností obrázku v Azure:

```json
"publisher": "MicrosoftWindowsDesktop",
"offer": "Windows-10",
"sku": "19h2-ent",
"version": "latest"
```

Můžete také změnit dostupné image Win10:

```azurepowershell-interactive
Get-AzVMImageSku -Location westus2 -PublisherName MicrosoftWindowsDesktop -Offer windows-10
```

## <a name="download-template-and-configure"></a>Stáhnout šablonu a nakonfigurovat

Teď je potřeba stáhnout šablonu a nakonfigurovat ji pro použití.

```azurepowershell-interactive
$templateUrl="https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/solutions/14_Building_Images_WVD/armTemplateWVD.json"
$templateFilePath = "armTemplateWVD.json"

Invoke-WebRequest -Uri $templateUrl -OutFile $templateFilePath -UseBasicParsing

((Get-Content -path $templateFilePath -Raw) -replace '<subscriptionID>',$subscriptionID) | Set-Content -Path $templateFilePath
((Get-Content -path $templateFilePath -Raw) -replace '<rgName>',$imageResourceGroup) | Set-Content -Path $templateFilePath
((Get-Content -path $templateFilePath -Raw) -replace '<region>',$location) | Set-Content -Path $templateFilePath
((Get-Content -path $templateFilePath -Raw) -replace '<runOutputName>',$runOutputName) | Set-Content -Path $templateFilePath

((Get-Content -path $templateFilePath -Raw) -replace '<imageDefName>',$imageDefName) | Set-Content -Path $templateFilePath
((Get-Content -path $templateFilePath -Raw) -replace '<sharedImageGalName>',$sigGalleryName) | Set-Content -Path $templateFilePath
((Get-Content -path $templateFilePath -Raw) -replace '<region1>',$location) | Set-Content -Path $templateFilePath
((Get-Content -path $templateFilePath -Raw) -replace '<imgBuilderId>',$idenityNameResourceId) | Set-Content -Path $templateFilePath

```

Můžete si klidně zobrazit [šablonu](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/solutions/14_Building_Images_WVD/armTemplateWVD.json)a veškerý kód zobrazit.


## <a name="submit-the-template"></a>Odeslat šablonu

Vaše šablona se musí odeslat do služby, stáhne se všechny závislé artefakty (například skripty), ověří, zkontroluje oprávnění a uloží je do pracovní skupiny prostředků, která je předem opravená, *IT_*.

```azurepowershell-interactive
New-AzResourceGroupDeployment -ResourceGroupName $imageResourceGroup -TemplateFile $templateFilePath -api-version "2020-02-14" -imageTemplateName $imageTemplateName -svclocation $location

# Optional - if you have any errors running the above, run:
$getStatus=$(Get-AzImageBuilderTemplate -ResourceGroupName $imageResourceGroup -Name $imageTemplateName)
$getStatus.ProvisioningErrorCode 
$getStatus.ProvisioningErrorMessage
```
 
## <a name="build-the-image"></a>Sestavení image
```azurepowershell-interactive
Start-AzImageBuilderTemplate -ResourceGroupName $imageResourceGroup -Name $imageTemplateName -NoWait
```

> [!NOTE]
> Příkaz nebude čekat, než služba image Builder dokončí sestavení bitové kopie, můžete zadat dotaz na stav uvedený níže.

```azurepowershell-interactive
$getStatus=$(Get-AzImageBuilderTemplate -ResourceGroupName $imageResourceGroup -Name $imageTemplateName)

# this shows all the properties
$getStatus | Format-List -Property *

# these show the status the build
$getStatus.LastRunStatusRunState 
$getStatus.LastRunStatusMessage
$getStatus.LastRunStatusRunSubState
```
## <a name="create-a-vm"></a>Vytvoření virtuálního počítače
Nyní je sestavení Hotovo. z Image si můžete vytvořit virtuální počítač, použijte příklady [z tohoto obrázku](https://docs.microsoft.com/powershell/module/az.compute/new-azvm#examples).

## <a name="clean-up"></a>Vyčištění

Nejdřív odstraňte šablonu skupiny prostředků, neodstraňujte jenom celou skupinu prostředků, jinak se nevyčistí pracovní skupina prostředků (*IT_*), kterou používá AIB.

Odeberte šablonu obrázku.

```azurepowershell-interactive
Remove-AzImageBuilderTemplate -ResourceGroupName $imageResourceGroup -Name wvd10ImageTemplate
```

Odstraňte přiřazení role.

```azurepowershell-interactive
Remove-AzRoleAssignment -ObjectId $idenityNamePrincipalId -RoleDefinitionName $imageRoleDefName -Scope "/subscriptions/$subscriptionID/resourceGroups/$imageResourceGroup"

## remove definitions
Remove-AzRoleDefinition -Name "$idenityNamePrincipalId" -Force -Scope "/subscriptions/$subscriptionID/resourceGroups/$imageResourceGroup"

## delete identity
Remove-AzUserAssignedIdentity -ResourceGroupName $imageResourceGroup -Name $idenityName -Force
```

Odstraňte skupinu prostředků.

```azurepowershell-interactive
Remove-AzResourceGroup $imageResourceGroup -Force
```

## <a name="next-steps"></a>Další kroky

Můžete vyzkoušet další příklady [na GitHubu](https://github.com/danielsollondon/azvmimagebuilder/tree/master/quickquickstarts).
