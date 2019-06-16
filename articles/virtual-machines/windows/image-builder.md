---
title: Vytvoření virtuálního počítače s Windows pomocí Image Builder pro Azure (preview)
description: Vytvoření virtuálního počítače Windows pomocí Tvůrce Imagí Azure.
author: cynthn
ms.author: cynthn
ms.date: 05/02/2019
ms.topic: article
ms.service: virtual-machines-windows
manager: jeconnoc
ms.openlocfilehash: 01109aa83c12bda9b1d21ec25784d663f8abf700
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "65159717"
---
# <a name="preview-create-a-windows-vm-with-azure-image-builder"></a>Verze Preview: Vytvoření virtuálního počítače Windows pomocí Azure Image Builder

Tento článek je až vám ukážeme, jak můžete vytvořit vlastní image Windows pomocí Image Builder pro virtuální počítač Azure. V příkladu v tomto článku se používá tři různé [úpravcům přidávat nové](../linux/image-builder-json.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json#properties-customize) pro přizpůsobení image:
- PowerShell (adresa_uri-skriptu) – stažení a spuštění [skript prostředí PowerShell](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/testPsScript.ps1).
- Restartování Windows - restartuje virtuální počítač.
- PowerShell (inline) – spusťte konkrétní příkaz. V tomto příkladu vytvoří adresář na virtuální počítač pomocí `mkdir c:\\buildActions`.
- Soubor – kopírování souboru z Githubu do virtuálního počítače. Tento příklad zkopíruje [index.md](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/exampleArtifacts/buildArtifacts/index.html) k `c:\buildArtifacts\index.html` na virtuálním počítači.

Použijeme Ukázková šablona .json konfigurace image. Soubor .json, který se používá, je zde: [helloImageTemplateWin.json](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/0_Creating_a_Custom_Windows_Managed_Image/helloImageTemplateWin.json). 


> [!IMPORTANT]
> Image Builder pro Azure je aktuálně ve verzi public preview.
> Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro úlohy v produkčním prostředí. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti. Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).


## <a name="register-the-features"></a>Registrace funkce

Pokud chcete použít Image Builder pro Azure ve verzi preview, budete muset registrovat novou funkci.

```azurecli-interactive
az feature register --namespace Microsoft.VirtualMachineImages --name VirtualMachineTemplatePreview
```

Zkontrolujte stav registrace funkce.

```azurecli-interactive
az feature show --namespace Microsoft.VirtualMachineImages --name VirtualMachineTemplatePreview | grep state
```

Zkontrolujte svou registraci.

```azurecli-interactive
az provider show -n Microsoft.VirtualMachineImages | grep registrationState

az provider show -n Microsoft.Storage | grep registrationState
```

Pokud třeba není registrovaný, spusťte následující příkaz:

```azurecli-interactive
az provider register -n Microsoft.VirtualMachineImages

az provider register -n Microsoft.Storage
```

## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Použijeme některé údaje opakovaně, takže si vytvoříme několik proměnných k ukládání těchto informací.

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

Vytvoření proměnné pro ID vašeho předplatného. Můžete získat pomocí `az account show | grep id`.

```azurecli-interactive
subscriptionID=<Your subscription ID>
```

Vytvořte skupinu prostředků.

```azurecli-interactive
az group create -n $imageResourceGroup -l $location
```

Udělení oprávnění Image Builder vytvářet prostředky v příslušné skupině prostředků. `--assignee` Hodnota je ID registrace aplikace pro Image Builder pro službu. 

```azurecli-interactive
az role assignment create \
    --assignee cf32a0cc-373c-47c9-9156-0db11f6a6dfc \
    --role Contributor \
    --scope /subscriptions/$subscriptionID/resourceGroups/$imageResourceGroup
```


## <a name="download-the-json-example"></a>Stáhnout příklad .json

Stáhněte si ukázkový soubor .json a nakonfigurovat proměnné, které jste vytvořili.

```azurecli-interactive
curl https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/0_Creating_a_Custom_Windows_Managed_Image/helloImageTemplateWin.json -o helloImageTemplateWin.json
sed -i -e "s/<subscriptionID>/$subscriptionID/g" helloImageTemplateWin.json
sed -i -e "s/<rgName>/$imageResourceGroup/g" helloImageTemplateWin.json
sed -i -e "s/<region>/$location/g" helloImageTemplateWin.json
sed -i -e "s/<imageName>/$imageName/g" helloImageTemplateWin.json
sed -i -e "s/<runOutputName>/$runOutputName/g" helloImageTemplateWin.json

```

## <a name="create-the-image"></a>Vytvoření image

Odeslání image konfigurace ve službě VM Image Builder

```azurecli-interactive
az resource create \
    --resource-group $imageResourceGroup \
    --properties @helloImageTemplateWin.json \
    --is-full-object \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateWin01
```

Spusťte sestavení image.

```azurecli-interactive
az resource invoke-action \
     --resource-group $imageResourceGroup \
     --resource-type  Microsoft.VirtualMachineImages/imageTemplates \
     -n helloImageTemplateWin01 \
     --action Run 
```

Počkejte na dokončení sestavení. Může to trvat přibližně 15 minut.

## <a name="create-the-vm"></a>Vytvořte virtuální počítač.

Vytvoření virtuálního počítače pomocí image, kterou jste vytvořili. Nahraďte *<password>* s vlastní heslo `aibuser` na virtuálním počítači.

```azurecli-interactive
az vm create \
  --resource-group $imageResourceGroup \
  --name aibImgWinVm00 \
  --admin-username aibuser \
  --admin-password <password> \
  --image $imageName \
  --location $location
```

## <a name="verify-the-customization"></a>Ověřte, přizpůsobení

Vytvoření připojení vzdálené plochy k virtuálnímu počítači pomocí uživatelského jména a hesla, které jste nastavili při vytváření virtuálního počítače. V tomto virtuálním počítači otevřete příkazový řádek a zadejte:

```console
dir c:\
```

Měli byste vidět tyto dva adresáře vytvořené během přizpůsobení bitové kopie:
- buildActions
- buildArtifacts

## <a name="clean-up"></a>Vyčištění

Až budete hotovi, odstraňte prostředky.

```azurecli-interactive
az resource delete \
    --resource-group $imageResourceGroup \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateWin01
az group delete -n $imageResourceGroup
```

## <a name="next-steps"></a>Další postup

Další informace o součástech soubor .json, používané v tomto článku najdete v tématu [Image builder referenčními informacemi k šablonám](../linux/image-builder-json.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

