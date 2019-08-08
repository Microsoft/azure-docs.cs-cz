---
title: Vytvoření virtuálního počítače s Windows pomocí Azure image Builder (Preview)
description: Vytvořte virtuální počítač s Windows pomocí Tvůrce imagí Azure.
author: cynthn
ms.author: cynthn
ms.date: 07/31/2019
ms.topic: article
ms.service: virtual-machines-windows
manager: gwallace
ms.openlocfilehash: 9dc4909db5560be6eb082dbad85d4b2d42113bdd
ms.sourcegitcommit: 3073581d81253558f89ef560ffdf71db7e0b592b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/06/2019
ms.locfileid: "68828689"
---
# <a name="preview-create-a-windows-vm-with-azure-image-builder"></a>Verze Preview: Vytvoření virtuálního počítače s Windows pomocí Azure image Builder

V tomto článku se dozvíte, jak můžete vytvořit vlastní image Windows pomocí Tvůrce imagí virtuálních počítačů Azure. Příklad v tomto článku používá pro [](../linux/image-builder-json.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json#properties-customize) přizpůsobení image vlastníky:
- PowerShell (ScriptUri) – Stáhněte a spusťte [powershellový skript](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/testPsScript.ps1).
- Restartování Windows – virtuální počítač se restartuje.
- PowerShell (inline) – spusťte konkrétní příkaz. V tomto příkladu na virtuálním počítači vytvoří adresář pomocí `mkdir c:\\buildActions`.
- Soubor – zkopírujte soubor z GitHubu do virtuálního počítače. Tento příklad zkopíruje [index.MD](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/exampleArtifacts/buildArtifacts/index.html) do `c:\buildArtifacts\index.html` virtuálního počítače.

Můžete také zadat `buildTimeoutInMinutes`. Výchozí hodnota je 240 minut a můžete prodloužit dobu sestavování tak, aby umožňovala delší spuštění sestavení.

K nakonfigurování image budeme používat šablonu Sample. JSON. Soubor. JSON, který používáme, je tady: [helloImageTemplateWin. JSON](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/0_Creating_a_Custom_Windows_Managed_Image/helloImageTemplateWin.json). 


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

az provider show -n Microsoft.Storage | grep registrationState
```

Pokud nevyžadují registraci, spusťte tento příkaz:

```azurecli-interactive
az provider register -n Microsoft.VirtualMachineImages

az provider register -n Microsoft.Storage
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

Vytvořte proměnnou pro ID předplatného. Můžete to získat pomocí `az account show | grep id`.

```azurecli-interactive
subscriptionID=<Your subscription ID>
```
## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků
Tato skupina prostředků se používá k uložení artefaktu šablony konfigurace image a image.


```azurecli-interactive
az group create -n $imageResourceGroup -l $location
```

## <a name="set-permissions-on-the-resource-group"></a>Nastavení oprávnění pro skupinu prostředků

Udělte přispěvateli image Builder oprávnění k vytvoření image ve skupině prostředků. Bez toho se sestavení image nezdaří. 

`--assignee` Hodnota je ID registrace aplikace pro službu Tvůrce imagí. 

```azurecli-interactive
az role assignment create \
    --assignee cf32a0cc-373c-47c9-9156-0db11f6a6dfc \
    --role Contributor \
    --scope /subscriptions/$subscriptionID/resourceGroups/$imageResourceGroup
```


## <a name="download-the-image-configuration-template-example"></a>Stažení příkladu šablony konfigurace image

Vytvořili jste šablonu pro konfiguraci parametrizovaných imagí, kterou můžete vyzkoušet. Stáhněte soubor example. JSON a nakonfigurujte ho pomocí proměnných, které jste nastavili dříve.

```azurecli-interactive
curl https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/0_Creating_a_Custom_Windows_Managed_Image/helloImageTemplateWin.json -o helloImageTemplateWin.json

sed -i -e "s/<subscriptionID>/$subscriptionID/g" helloImageTemplateWin.json
sed -i -e "s/<rgName>/$imageResourceGroup/g" helloImageTemplateWin.json
sed -i -e "s/<region>/$location/g" helloImageTemplateWin.json
sed -i -e "s/<imageName>/$imageName/g" helloImageTemplateWin.json
sed -i -e "s/<runOutputName>/$runOutputName/g" helloImageTemplateWin.json

```

Tento příklad můžete v terminálu upravit pomocí textového editoru, jako `vi`je.

```azurecli-interactive
vi helloImageTemplateLinux.json
```

> [!NOTE]
> Pro zdrojovou image musíte vždycky [zadat verzi](https://github.com/danielsollondon/azvmimagebuilder/blob/master/troubleshootingaib.md#image-version-failure), kterou nemůžete použít `latest`.
> Pokud přidáte nebo změníte skupinu prostředků, do které je bitová kopie distribuována, musíte [nastavit oprávnění](#set-permissions-on-the-resource-group) pro skupinu prostředků.
 
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

Po dokončení Tato akce vrátí zprávu o úspěchu zpět do konzoly a vytvoří `Image Builder Configuration Template` `$imageResourceGroup`v. Tento prostředek můžete zobrazit ve skupině prostředků v Azure Portal, pokud povolíte možnost Zobrazit skryté typy.

Na pozadí vytvoří Tvůrce imagí také pracovní skupinu prostředků ve vašem předplatném. Tato skupina prostředků se používá pro sestavení image. Bude v tomto formátu:`IT_<DestinationResourceGroup>_<TemplateName>`

> [!Note]
> Pracovní skupinu prostředků nesmíte odstranit přímo. Nejprve odstraňte artefakt šablony imagí, což způsobí odstranění pracovní skupiny prostředků.

Pokud služba ohlásí chybu během odesílání šablony konfigurace obrázku:
-  Projděte si tyto kroky pro [řešení potíží](https://github.com/danielsollondon/azvmimagebuilder/blob/master/troubleshootingaib.md#template-submission-errors--troubleshooting) . 
- Před opakováním odeslání budete muset šablonu odstranit pomocí následujícího fragmentu kódu.

```azurecli-interactive
az resource delete \
    --resource-group $imageResourceGroup \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateLinux01
```

## <a name="start-the-image-build"></a>Spustit sestavení image
Spusťte proces vytváření bitové kopie pomocí [AZ Resource Invoke-Action](/cli/azure/resource#az-resource-invoke-action).

```azurecli-interactive
az resource invoke-action \
     --resource-group $imageResourceGroup \
     --resource-type  Microsoft.VirtualMachineImages/imageTemplates \
     -n helloImageTemplateWin01 \
     --action Run 
```

Počkejte na dokončení sestavení. Tato možnost může trvat přibližně 15 minut.

Pokud narazíte na nějaké chyby, přečtěte si prosím tyto kroky pro [řešení potíží](https://github.com/danielsollondon/azvmimagebuilder/blob/master/troubleshootingaib.md#image-build-errors--troubleshooting) .


## <a name="create-the-vm"></a>Vytvořte virtuální počítač.

Vytvořte virtuální počítač pomocí Image, kterou jste vytvořili. *\<Heslo >* nahraďte `aibuser` vlastním heslem pro virtuální počítač.

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

### <a name="delete-the-image-resource-group"></a>Odstranit skupinu prostředků image
```azurecli-interactive
az group delete -n $imageResourceGroup
```


## <a name="next-steps"></a>Další postup

Další informace o součástech souboru. JSON používaných v tomto článku najdete v tématu Referenční dokumentace k [šablonám tvůrce imagí](../linux/image-builder-json.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
