---
title: Vytvoření virtuálního počítače s Windows pomocí Azure image Builder (Preview)
description: Vytvořte virtuální počítač s Windows pomocí Tvůrce imagí Azure.
author: cynthn
ms.author: cynthn
ms.date: 03/02/2020
ms.topic: how-to
ms.service: virtual-machines
ms.subervice: image-builder
ms.colletion: windows
ms.openlocfilehash: b93d236d0b716bfaf7dfb45b21c9524ece75fcae
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/20/2021
ms.locfileid: "107764562"
---
# <a name="preview-create-a-windows-vm-with-azure-image-builder"></a>Verze Preview: Vytvoření virtuálního počítače s Windows pomocí Azure image Builder

V tomto článku se dozvíte, jak můžete vytvořit vlastní image Windows pomocí Tvůrce imagí virtuálních počítačů Azure. Příklad v tomto [článku používá pro](../linux/image-builder-json.md#properties-customize) přizpůsobení image vlastníky:
- PowerShell (ScriptUri) – Stáhněte a spusťte [powershellový skript](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/testPsScript.ps1).
- Restartování Windows – virtuální počítač se restartuje.
- PowerShell (inline) – spusťte konkrétní příkaz. V tomto příkladu na virtuálním počítači vytvoří adresář pomocí `mkdir c:\\buildActions` .
- Soubor – zkopírujte soubor z GitHubu do virtuálního počítače. Tento příklad zkopíruje [index.MD](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/exampleArtifacts/buildArtifacts/index.html) do `c:\buildArtifacts\index.html` virtuálního počítače.
- buildTimeoutInMinutes – zvyšte čas sestavení tak, aby bylo možné déle spouštět buildy, výchozí hodnota je 240 minut a můžete prodloužit dobu sestavování, aby bylo možné spouštět buildy delší.
- vmProfile – určení vmSize a vlastností sítě
- osDiskSizeGB – velikost obrázku můžete zvětšit.
- Identita – poskytuje identitu pro Azure image Builder, která se má použít při sestavování.


Můžete také zadat `buildTimeoutInMinutes` . Výchozí hodnota je 240 minut a můžete prodloužit dobu sestavování tak, aby umožňovala delší spuštění sestavení.

K nakonfigurování image budeme používat šablonu Sample. JSON. Soubor. JSON, který používáme, je tady: [helloImageTemplateWin.js](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/0_Creating_a_Custom_Windows_Managed_Image/helloImageTemplateWin.json). 


> [!IMPORTANT]
> Azure image Builder je momentálně ve verzi Public Preview.
> Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro úlohy v produkčním prostředí. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti. Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).


## <a name="register-the-features"></a>Registrace funkcí

Chcete-li používat Azure image Builder v rámci verze Preview, je nutné zaregistrovat novou funkci.

```azurecli-interactive
az feature register --namespace Microsoft.VirtualMachineImages --name VirtualMachineTemplatePreview
```

Ověřte stav registrace funkce.

```azurecli-interactive
az feature show --namespace Microsoft.VirtualMachineImages --name VirtualMachineTemplatePreview | grep state
```

Ověřte vaši registraci.

```azurecli-interactive
az provider show -n Microsoft.VirtualMachineImages | grep registrationState
az provider show -n Microsoft.KeyVault | grep registrationState
az provider show -n Microsoft.Compute | grep registrationState
az provider show -n Microsoft.Storage | grep registrationState
az provider show -n Microsoft.Network | grep registrationState
```

Pokud nevyžadují registraci, spusťte tento příkaz:

```azurecli-interactive
az provider register -n Microsoft.VirtualMachineImages
az provider register -n Microsoft.Compute
az provider register -n Microsoft.KeyVault
az provider register -n Microsoft.Storage
az provider register -n Microsoft.Network
```


## <a name="set-variables"></a>Nastavení proměnných

Některé informace budeme používat opakovaně, takže vytvoříme některé proměnné, které tyto informace uloží.


```azurecli-interactive
# Resource group name - we are using myImageBuilderRG in this example
imageResourceGroup=myWinImgBuilderRG
# Region location 
location=WestUS2
# Name for the image 
imageName=myWinBuilderImage
# Run output name
runOutputName=aibWindows
# name of the image to be created
imageName=aibWinImage
```

Vytvořte proměnnou pro ID předplatného. Můžete to získat pomocí `az account show | grep id` .

```azurecli-interactive
subscriptionID=<Your subscription ID>
```
## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků
Tato skupina prostředků se používá k uložení artefaktu šablony konfigurace image a image.


```azurecli-interactive
az group create -n $imageResourceGroup -l $location
```

## <a name="create-a-user-assigned-identity-and-set-permissions-on-the-resource-group"></a>Vytvoření uživatelsky přiřazené identity a nastavení oprávnění pro skupinu prostředků
Nástroj image Builder použije poskytnutou [identitu uživatele](../../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md#user-assigned-managed-identity) k vložení image do skupiny prostředků. V tomto příkladu vytvoříte definici role Azure, která má podrobné akce k provedení distribuce image. Definice role se pak přiřadí identitě User-identity.

## <a name="create-user-assigned-managed-identity-and-grant-permissions"></a>Vytvoření spravované identity přiřazené uživatelem a udělení oprávnění 
```bash
# create user assigned identity for image builder to access the storage account where the script is located
idenityName=aibBuiUserId$(date +'%s')
az identity create -g $imageResourceGroup -n $idenityName

# get identity id
imgBuilderCliId=$(az identity show -g $imageResourceGroup -n $idenityName | grep "clientId" | cut -c16- | tr -d '",')

# get the user identity URI, needed for the template
imgBuilderId=/subscriptions/$subscriptionID/resourcegroups/$imageResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/$idenityName

# download preconfigured role definition example
curl https://raw.githubusercontent.com/azure/azvmimagebuilder/master/solutions/12_Creating_AIB_Security_Roles/aibRoleImageCreation.json -o aibRoleImageCreation.json

imageRoleDefName="Azure Image Builder Image Def"$(date +'%s')

# update the definition
sed -i -e "s/<subscriptionID>/$subscriptionID/g" aibRoleImageCreation.json
sed -i -e "s/<rgName>/$imageResourceGroup/g" aibRoleImageCreation.json
sed -i -e "s/Azure Image Builder Service Image Creation Role/$imageRoleDefName/g" aibRoleImageCreation.json

# create role definitions
az role definition create --role-definition ./aibRoleImageCreation.json

# grant role definition to the user assigned identity
az role assignment create \
    --assignee $imgBuilderCliId \
    --role $imageRoleDefName \
    --scope /subscriptions/$subscriptionID/resourceGroups/$imageResourceGroup
```



## <a name="download-the-image-configuration-template-example"></a>Stažení příkladu šablony konfigurace image

Vytvořili jste šablonu pro konfiguraci parametrizovaných imagí, kterou můžete vyzkoušet. Stáhněte soubor example. JSON a nakonfigurujte ho pomocí proměnných, které jste nastavili dříve.

```azurecli-interactive
curl https://raw.githubusercontent.com/azure/azvmimagebuilder/master/quickquickstarts/0_Creating_a_Custom_Windows_Managed_Image/helloImageTemplateWin.json -o helloImageTemplateWin.json

sed -i -e "s/<subscriptionID>/$subscriptionID/g" helloImageTemplateWin.json
sed -i -e "s/<rgName>/$imageResourceGroup/g" helloImageTemplateWin.json
sed -i -e "s/<region>/$location/g" helloImageTemplateWin.json
sed -i -e "s/<imageName>/$imageName/g" helloImageTemplateWin.json
sed -i -e "s/<runOutputName>/$runOutputName/g" helloImageTemplateWin.json
sed -i -e "s%<imgBuilderId>%$imgBuilderId%g" helloImageTemplateWin.json

```

Tento příklad můžete v terminálu upravit pomocí textového editoru, jako je `vi` .

```azurecli-interactive
vi helloImageTemplateWin.json
```

> [!NOTE]
> Pro zdrojovou image musíte vždycky [zadat verzi](../linux/image-builder-troubleshoot.md#build--step-failed-for-image-version), kterou nemůžete použít `latest` .
> Pokud přidáte nebo změníte skupinu prostředků, do které je bitová kopie distribuována, musíte [nastavit oprávnění](#create-a-user-assigned-identity-and-set-permissions-on-the-resource-group) pro skupinu prostředků.
 
## <a name="create-the-image"></a>Vytvoření image

Odeslat konfiguraci image do služby tvůrce imagí VM

```azurecli-interactive
az resource create \
    --resource-group $imageResourceGroup \
    --properties @helloImageTemplateWin.json \
    --is-full-object \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateWin01
```

Po dokončení Tato akce vrátí zprávu o úspěchu zpět do konzoly a vytvoří `Image Builder Configuration Template` v `$imageResourceGroup` . Tento prostředek můžete zobrazit ve skupině prostředků v Azure Portal, pokud povolíte možnost Zobrazit skryté typy.

Na pozadí vytvoří Tvůrce imagí také pracovní skupinu prostředků ve vašem předplatném. Tato skupina prostředků se používá pro sestavení image. Bude v tomto formátu: `IT_<DestinationResourceGroup>_<TemplateName>`

> [!Note]
> Pracovní skupinu prostředků nesmíte odstranit přímo. Nejprve odstraňte artefakt šablony imagí, což způsobí odstranění pracovní skupiny prostředků.

Pokud služba ohlásí chybu během odesílání šablony konfigurace obrázku:
-  Projděte si tyto kroky pro [řešení potíží](../linux/image-builder-troubleshoot.md#troubleshoot-image-template-submission-errors) . 
- Před opakováním odeslání budete muset šablonu odstranit pomocí následujícího fragmentu kódu.

```azurecli-interactive
az resource delete \
    --resource-group $imageResourceGroup \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateLinux01
```

## <a name="start-the-image-build"></a>Spustit sestavení image
Spusťte proces vytváření bitové kopie pomocí [AZ Resource Invoke-Action](/cli/azure/resource#az_resource_invoke_action).

```azurecli-interactive
az resource invoke-action \
     --resource-group $imageResourceGroup \
     --resource-type  Microsoft.VirtualMachineImages/imageTemplates \
     -n helloImageTemplateWin01 \
     --action Run 
```

Počkejte na dokončení sestavení. Tato možnost může trvat přibližně 15 minut.

Pokud narazíte na nějaké chyby, přečtěte si prosím tyto kroky pro [řešení potíží](../linux/image-builder-troubleshoot.md#troubleshoot-common-build-errors) .


## <a name="create-the-vm"></a>Vytvoření virtuálního počítače

Vytvořte virtuální počítač pomocí Image, kterou jste vytvořili. Nahraďte *\<password>* vlastním heslem pro `aibuser` virtuální počítač.

```azurecli-interactive
az vm create \
  --resource-group $imageResourceGroup \
  --name aibImgWinVm00 \
  --admin-username aibuser \
  --admin-password <password> \
  --image $imageName \
  --location $location
```

## <a name="verify-the-customization"></a>Ověření přizpůsobení

Vytvořte připojení ke vzdálené ploše virtuálního počítače pomocí uživatelského jména a hesla, které jste nastavili při vytváření virtuálního počítače. Uvnitř virtuálního počítače otevřete příkazový řádek a zadejte příkaz:

```console
dir c:\
```

Během přizpůsobení Image by se měly zobrazit tyto dva adresáře:
- buildActions
- buildArtifacts

## <a name="clean-up"></a>Vyčištění

Až budete hotovi, odstraňte prostředky.

### <a name="delete-the-image-builder-template"></a>Odstranění šablony tvůrce imagí

```azurecli-interactive
az resource delete \
    --resource-group $imageResourceGroup \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateWin01
```

### <a name="delete-the-role-assignment-role-definition-and-user-identity"></a>Odstraňte přiřazení role, definici role a identitu uživatele.
```azurecli-interactive
az role assignment delete \
    --assignee $imgBuilderCliId \
    --role "$imageRoleDefName" \
    --scope /subscriptions/$subscriptionID/resourceGroups/$imageResourceGroup

az role definition delete --name "$imageRoleDefName"

az identity delete --ids $imgBuilderId
```

### <a name="delete-the-image-resource-group"></a>Odstranit skupinu prostředků image

```azurecli-interactive
az group delete -n $imageResourceGroup
```


## <a name="next-steps"></a>Další kroky

Další informace o součástech souboru. JSON používaných v tomto článku najdete v tématu Referenční dokumentace k [šablonám tvůrce imagí](../linux/image-builder-json.md).
