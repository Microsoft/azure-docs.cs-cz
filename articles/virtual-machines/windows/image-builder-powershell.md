---
title: Vytvoření virtuálního počítače s Windows pomocí Azure image Builder pomocí PowerShellu
description: Vytvořte virtuální počítač s Windows pomocí modulu PowerShellu pro sestavovatele imagí Azure.
author: cynthn
ms.author: cynthn
ms.date: 03/02/2021
ms.topic: how-to
ms.service: virtual-machines
ms.subervice: image-builder
ms.colletion: windows
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 90d09763f2c9e167d6a0a34adbbc444ebad14c46
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "101693454"
---
# <a name="preview-create-a-windows-vm-with-azure-image-builder-using-powershell"></a>Verze Preview: Vytvoření virtuálního počítače s Windows pomocí Azure image Builder pomocí PowerShellu

Tento článek ukazuje, jak můžete vytvořit přizpůsobenou image Windows pomocí modulu PowerShellu pro sestavovatele imagí Azure VM.

> [!CAUTION]
> Azure image Builder je momentálně ve verzi Public Preview. Tato verze Preview se poskytuje bez smlouvy o úrovni služeb. Nedoporučuje se pro produkční úlohy. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti. Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Požadavky

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

Pokud se rozhodnete použít prostředí PowerShell místně, Tento článek vyžaduje, abyste nainstalovali modul AZ PowerShell a připojili se k účtu Azure pomocí rutiny [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) . Další informace o instalaci modulu AZ PowerShell najdete v tématu [Install Azure PowerShell](/powershell/azure/install-az-ps).

> [!IMPORTANT]
> I když jsou moduly PowerShellu **AZ. ImageBuilder** a **AZ. ManagedServiceIdentity** ve verzi Preview, musíte je nainstalovat samostatně pomocí `Install-Module` rutiny s `AllowPrerelease` parametrem. Až budou tyto moduly PowerShellu všeobecně dostupné, stanou se součástí budoucích verzí v budoucnu AZ PowerShell Module releases a k dispozici nativně z Azure Cloud Shell.

```azurepowershell-interactive
'Az.ImageBuilder', 'Az.ManagedServiceIdentity' | ForEach-Object {Install-Module -Name $_ -AllowPrerelease}
```

[!INCLUDE [cloud-shell-try-it](../../../includes/cloud-shell-try-it.md)]

Pokud máte více předplatných Azure, vyberte příslušné předplatné, ve kterém se prostředky mají fakturovat. Vyberte konkrétní předplatné pomocí rutiny [set-AzContext](/powershell/module/az.accounts/set-azcontext) .

```azurepowershell-interactive
Set-AzContext -SubscriptionId 00000000-0000-0000-0000-000000000000
```

### <a name="register-features"></a>Funkce registrace

Pokud používáte Azure image Builder poprvé v rámci verze Preview, zaregistrujte novou funkci **VirtualMachineTemplatePreview** .

```azurepowershell-interactive
Register-AzProviderFeature -ProviderNamespace Microsoft.VirtualMachineImages -FeatureName VirtualMachineTemplatePreview
```

Ověřte stav registrace funkce.

> [!NOTE]
> **RegistrationState** může být ve `Registering` stavu několik minut, než se změní na `Registered` . Než budete pokračovat, počkejte, než se stav **zaregistruje** .

```azurepowershell-interactive
Get-AzProviderFeature -ProviderNamespace Microsoft.VirtualMachineImages -FeatureName VirtualMachineTemplatePreview
```

Pokud ještě nejsou zaregistrované, zaregistrujte následující poskytovatele prostředků pro použití s předplatným Azure.

- Microsoft.Compute
- Trezor Microsoft.
- Microsoft.Storage
- Microsoft.Network
- Microsoft. VirtualMachineImages

```azurepowershell-interactive
Get-AzResourceProvider -ProviderNamespace Microsoft.Compute, Microsoft.KeyVault, Microsoft.Storage, Microsoft.VirtualMachineImages, Microsoft.Network |
  Where-Object RegistrationState -ne Registered |
    Register-AzResourceProvider
```

## <a name="define-variables"></a>Definování proměnných

Opakovaně budete používat několik informací. Vytvořte proměnné pro uložení informací.

```azurepowershell-interactive
# Destination image resource group name
$imageResourceGroup = 'myWinImgBuilderRG'

# Azure region
$location = 'WestUS2'

# Name of the image to be created
$imageTemplateName = 'myWinImage'

# Distribution properties of the managed image upon completion
$runOutputName = 'myDistResults'
```

Vytvořte proměnnou pro ID předplatného Azure. Pokud chcete potvrdit, že `subscriptionID` proměnná obsahuje ID vašeho předplatného, můžete spustit druhý řádek v následujícím příkladu.

```azurepowershell-interactive
# Your Azure Subscription ID
$subscriptionID = (Get-AzContext).Subscription.Id
Write-Output $subscriptionID
```

## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Vytvořte [skupinu prostředků Azure](../../azure-resource-manager/management/overview.md) pomocí rutiny [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) . Skupina prostředků je logický kontejner, ve kterém se nasazují a spravují prostředky Azure jako skupina.

Následující příklad vytvoří skupinu prostředků na základě názvu v `$imageResourceGroup` proměnné v oblasti určené v `$location` proměnné. Tato skupina prostředků se používá k uložení artefaktu šablony konfigurace image a image.

```azurepowershell-interactive
New-AzResourceGroup -Name $imageResourceGroup -Location $location
```

## <a name="create-user-identity-and-set-role-permissions"></a>Vytvoření identity uživatele a nastavení oprávnění role

Udělte oprávnění tvůrce imagí Azure k vytváření imagí v zadané skupině prostředků pomocí následujícího příkladu. Bez tohoto oprávnění nebude proces sestavení image úspěšně dokončen.

Vytvořte proměnné pro definici role a názvy identit. Tyto hodnoty musí být jedinečné.

```azurepowershell-interactive
[int]$timeInt = $(Get-Date -UFormat '%s')
$imageRoleDefName = "Azure Image Builder Image Def $timeInt"
$identityName = "myIdentity$timeInt"
```

Vytvořte identitu uživatele.

```azurepowershell-interactive
New-AzUserAssignedIdentity -ResourceGroupName $imageResourceGroup -Name $identityName
```

Uložte prostředky identity a ID objektů zabezpečení do proměnných.

```azurepowershell-interactive
$identityNameResourceId = (Get-AzUserAssignedIdentity -ResourceGroupName $imageResourceGroup -Name $identityName).Id
$identityNamePrincipalId = (Get-AzUserAssignedIdentity -ResourceGroupName $imageResourceGroup -Name $identityName).PrincipalId
```

### <a name="assign-permissions-for-identity-to-distribute-images"></a>Přiřazení oprávnění identitě k distribuci imagí

Stáhněte konfigurační soubor. JSON a upravte ho podle nastavení definovaných v tomto článku.

```azurepowershell-interactive
$myRoleImageCreationUrl = 'https://raw.githubusercontent.com/azure/azvmimagebuilder/master/solutions/12_Creating_AIB_Security_Roles/aibRoleImageCreation.json'
$myRoleImageCreationPath = "$env:TEMP\myRoleImageCreation.json"

Invoke-WebRequest -Uri $myRoleImageCreationUrl -OutFile $myRoleImageCreationPath -UseBasicParsing

$Content = Get-Content -Path $myRoleImageCreationPath -Raw
$Content = $Content -replace '<subscriptionID>', $subscriptionID
$Content = $Content -replace '<rgName>', $imageResourceGroup
$Content = $Content -replace 'Azure Image Builder Service Image Creation Role', $imageRoleDefName
$Content | Out-File -FilePath $myRoleImageCreationPath -Force
```

Vytvořte definici role.

```azurepowershell-interactive
New-AzRoleDefinition -InputFile $myRoleImageCreationPath
```

Udělte definici role instančnímu objektu Tvůrce imagí.

```azurepowershell-interactive
$RoleAssignParams = @{
  ObjectId = $identityNamePrincipalId
  RoleDefinitionName = $imageRoleDefName
  Scope = "/subscriptions/$subscriptionID/resourceGroups/$imageResourceGroup"
}
New-AzRoleAssignment @RoleAssignParams
```

> [!NOTE]
> Pokud se zobrazí chyba: "_New-AzRoleDefinition: překročil se limit definice role. Nedají se vytvořit žádné další definice rolí._", přečtěte si téma [řešení potíží s Azure RBAC](../../role-based-access-control/troubleshooting.md).

## <a name="create-a-shared-image-gallery"></a>Vytvoření služby Shared Image Gallery

Vytvořte galerii.

```azurepowershell-interactive
$myGalleryName = 'myImageGallery'
$imageDefName = 'winSvrImages'

New-AzGallery -GalleryName $myGalleryName -ResourceGroupName $imageResourceGroup -Location $location
```

Vytvořte definici galerie.

```azurepowershell-interactive
$GalleryParams = @{
  GalleryName = $myGalleryName
  ResourceGroupName = $imageResourceGroup
  Location = $location
  Name = $imageDefName
  OsState = 'generalized'
  OsType = 'Windows'
  Publisher = 'myCo'
  Offer = 'Windows'
  Sku = 'Win2019'
}
New-AzGalleryImageDefinition @GalleryParams
```

## <a name="create-an-image"></a>Vytvoření image

Vytvoří zdrojový objekt tvůrce imagí Azure. Platné hodnoty parametrů najdete [v tématu Vyhledání imagí virtuálních počítačů s Windows v Azure Marketplace s Azure PowerShell](./cli-ps-findimage.md) .

```azurepowershell-interactive
$SrcObjParams = @{
  SourceTypePlatformImage = $true
  Publisher = 'MicrosoftWindowsServer'
  Offer = 'WindowsServer'
  Sku = '2019-Datacenter'
  Version = 'latest'
}
$srcPlatform = New-AzImageBuilderSourceObject @SrcObjParams
```

Vytvoří objekt distribuce nástroje Azure image Builder.

```azurepowershell-interactive
$disObjParams = @{
  SharedImageDistributor = $true
  ArtifactTag = @{tag='dis-share'}
  GalleryImageId = "/subscriptions/$subscriptionID/resourceGroups/$imageResourceGroup/providers/Microsoft.Compute/galleries/$myGalleryName/images/$imageDefName"
  ReplicationRegion = $location
  RunOutputName = $runOutputName
  ExcludeFromLatest = $false
}
$disSharedImg = New-AzImageBuilderDistributorObject @disObjParams
```

Vytvoří objekt pro přizpůsobení nástroje Azure image Builder.

```azurepowershell-interactive
$ImgCustomParams01 = @{
  PowerShellCustomizer = $true
  CustomizerName = 'settingUpMgmtAgtPath'
  RunElevated = $false
  Inline = @("mkdir c:\\buildActions", "mkdir c:\\buildArtifacts", "echo Azure-Image-Builder-Was-Here  > c:\\buildActions\\buildActionsOutput.txt")
}
$Customizer01 = New-AzImageBuilderCustomizerObject @ImgCustomParams01
```

Vytvořte druhý objekt přizpůsobení pro Azure image Builder.

```azurepowershell-interactive
$ImgCustomParams02 = @{
  FileCustomizer = $true
  CustomizerName = 'downloadBuildArtifacts'
  Destination = 'c:\\buildArtifacts\\index.html'
  SourceUri = 'https://raw.githubusercontent.com/azure/azvmimagebuilder/master/quickquickstarts/exampleArtifacts/buildArtifacts/index.html'
}
$Customizer02 = New-AzImageBuilderCustomizerObject @ImgCustomParams02
```

Vytvoří šablonu Azure image Builder.

```azurepowershell-interactive
$ImgTemplateParams = @{
  ImageTemplateName = $imageTemplateName
  ResourceGroupName = $imageResourceGroup
  Source = $srcPlatform
  Distribute = $disSharedImg
  Customize = $Customizer01, $Customizer02
  Location = $location
  UserAssignedIdentityId = $identityNameResourceId
}
New-AzImageBuilderTemplate @ImgTemplateParams
```

Po dokončení se vrátí zpráva a vytvoří se šablona konfigurace tvůrce imagí v `$imageResourceGroup` .

Chcete-li zjistit, zda byl proces vytvoření šablony úspěšný, můžete použít následující příklad.

```azurepowershell-interactive
Get-AzImageBuilderTemplate -ImageTemplateName $imageTemplateName -ResourceGroupName $imageResourceGroup |
  Select-Object -Property Name, LastRunStatusRunState, LastRunStatusMessage, ProvisioningState
```

Na pozadí tvůrce imagí také vytvoří pracovní skupinu prostředků ve vašem předplatném. Tato skupina prostředků se používá pro sestavení image. Je ve formátu: `IT_<DestinationResourceGroup>_<TemplateName>` .

> [!WARNING]
> Neodstraňujte pracovní skupinu prostředků přímo. Odstraňte artefakt šablony imagí, což způsobí odstranění pracovní skupiny prostředků.

Pokud služba ohlásí chybu během odesílání šablony konfigurace obrázku:

- Přečtěte si téma [řešení potíží se sestavením imagí virtuálních počítačů Azure (AIB)](../linux/image-builder-troubleshoot.md).
- Před opakováním odstraňte šablonu pomocí následujícího příkladu.

```azurepowershell-interactive
Remove-AzImageBuilderTemplate -ImageTemplateName $imageTemplateName -ResourceGroupName $imageResourceGroup
```

## <a name="start-the-image-build"></a>Spustit sestavení image

Odešlete konfiguraci image do služby tvůrce imagí virtuálních počítačů.

```azurepowershell-interactive
Start-AzImageBuilderTemplate -ResourceGroupName $imageResourceGroup -Name $imageTemplateName
```

Počkejte, než se proces sestavení image dokončí. Tento krok může trvat až hodinu.

Pokud narazíte na chyby, přečtěte si téma [řešení potíží se sestavením imagí virtuálních počítačů Azure (AIB)](../linux/image-builder-troubleshoot.md).

## <a name="create-a-vm"></a>Vytvoření virtuálního počítače

Přihlašovací údaje pro virtuální počítač ukládejte do proměnné. Heslo musí být složité.

```azurepowershell-interactive
$Cred = Get-Credential
```

Vytvořte virtuální počítač pomocí Image, kterou jste vytvořili.

```azurepowershell-interactive
$ArtifactId = (Get-AzImageBuilderRunOutput -ImageTemplateName $imageTemplateName -ResourceGroupName $imageResourceGroup).ArtifactId

New-AzVM -ResourceGroupName $imageResourceGroup -Image $ArtifactId -Name myWinVM01 -Credential $Cred
```

## <a name="verify-the-customizations"></a>Ověřit vlastní nastavení

Vytvořte připojení ke vzdálené ploše virtuálního počítače pomocí uživatelského jména a hesla, které jste nastavili při vytváření virtuálního počítače. Uvnitř virtuálního počítače otevřete PowerShell a spusťte příkaz `Get-Content` , jak je znázorněno v následujícím příkladu:

```azurepowershell-interactive
Get-Content -Path C:\buildActions\buildActionsOutput.txt
```

Měl by se zobrazit výstup na základě obsahu souboru vytvořeného během procesu přizpůsobení bitové kopie.

```Output
Azure-Image-Builder-Was-Here
```

Ze stejné relace PowerShellu ověřte, že se druhé přizpůsobení úspěšně dokončilo, a to tak, že zkontrolujete přítomnost souboru, `c:\buildArtifacts\index.html` jak je znázorněno v následujícím příkladu:

```azurepowershell-interactive
Get-ChildItem c:\buildArtifacts\
```

Výsledek by měl být výpis adresáře zobrazující soubor stažený během procesu přizpůsobení bitové kopie.

```Output
    Directory: C:\buildArtifacts

Mode                 LastWriteTime         Length Name
----                 -------------         ------ ----
-a---          29/01/2021    10:04            276 index.html
```


## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud prostředky vytvořené v tomto článku nepotřebujete, můžete je odstranit spuštěním následujících příkladů.

### <a name="delete-the-image-builder-template"></a>Odstranění šablony tvůrce imagí

```azurepowershell-interactive
Remove-AzImageBuilderTemplate -ResourceGroupName $imageResourceGroup -Name $imageTemplateName
```

### <a name="delete-the-image-resource-group"></a>Odstranit skupinu prostředků image

> [!CAUTION]
> Následující příklad odstraní zadanou skupinu prostředků a všechny prostředky, které jsou v ní obsažené.
> Pokud v zadané skupině prostředků existují prostředky mimo rozsah tohoto článku, budou také odstraněny.

```azurepowershell-interactive
Remove-AzResourceGroup -Name $imageResourceGroup
```

## <a name="next-steps"></a>Další kroky

Další informace o součástech souboru. JSON používaných v tomto článku najdete v tématu Referenční dokumentace k [šablonám tvůrce imagí](../linux/image-builder-json.md).
