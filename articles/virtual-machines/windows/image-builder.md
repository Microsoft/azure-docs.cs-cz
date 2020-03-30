---
title: Vytvoření virtuálního počítače s Windows pomocí Azure Image Builder (preview)
description: Vytvořte virtuální počítač s Windows pomocí Azure Image Builder.
author: cynthn
ms.author: cynthn
ms.date: 07/31/2019
ms.topic: article
ms.service: virtual-machines-windows
manager: gwallace
ms.openlocfilehash: e82d82dac833f7455e3d83d7e11c0c57c4eea816
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80238806"
---
# <a name="preview-create-a-windows-vm-with-azure-image-builder"></a>Náhled: Vytvoření virtuálního počítače s Windows pomocí Azure Image Builder

Tento článek vám ukáže, jak můžete vytvořit přizpůsobenou bitovou kopii Windows pomocí Azure VM Image Builder. Příklad v tomto článku používá [úpravy](../linux/image-builder-json.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json#properties-customize) pro přizpůsobení obrázku:
- PowerShell (ScriptUri) - stáhnout a spustit [skript Prostředí PowerShell](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/testPsScript.ps1).
- Restartování systému Windows - restartuje virtuální počítač.
- Prostředí PowerShell (inline) – spusťte určitý příkaz. V tomto příkladu vytvoří adresář na `mkdir c:\\buildActions`virtuálním počítači pomocí .
- Soubor – zkopírujte soubor z GitHubu do virtuálního počítače. Tento příklad zkopíruje [index.md](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/exampleArtifacts/buildArtifacts/index.html) `c:\buildArtifacts\index.html` na virtuálním počítači.

Můžete také zadat `buildTimeoutInMinutes`. Výchozí hodnota je 240 minut a můžete prodloužit dobu sestavení, aby bylo možné déle spuštěná sestavení.

Ke konfiguraci bitové kopie použijeme ukázkovou šablonu JSON. Soubor .json, který používáme, je zde: [helloImageTemplateWin.json](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/0_Creating_a_Custom_Windows_Managed_Image/helloImageTemplateWin.json). 


> [!IMPORTANT]
> Azure Image Builder je momentálně ve verzi Public Preview.
> Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro úlohy v produkčním prostředí. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti. Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).


## <a name="register-the-features"></a>Registrace funkcí

Chcete-li během náhledu používat Azure Image Builder, musíte zaregistrovat novou funkci.

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

Pokud neříkají registrované, spusťte následující:

```azurecli-interactive
az provider register -n Microsoft.VirtualMachineImages

az provider register -n Microsoft.Storage
```

## <a name="set-variables"></a>Nastavení proměnných

Budeme používat některé informace opakovaně, takže vytvoříme některé proměnné pro ukládání těchto informací.


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

Vytvořte proměnnou pro ID předplatného. Můžete si to `az account show | grep id`pomocí .

```azurecli-interactive
subscriptionID=<Your subscription ID>
```
## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků
Tato skupina prostředků se používá k uložení artefaktu šablony konfigurace obrázku a bitové kopie.


```azurecli-interactive
az group create -n $imageResourceGroup -l $location
```

## <a name="set-permissions-on-the-resource-group"></a>Nastavení oprávnění pro skupinu prostředků

Udělit přispěvateli tvůrce obrázků oprávnění k vytvoření obrázku ve skupině prostředků. Bez tohoto sestavení image se nezdaří. 

Hodnota `--assignee` je ID registrace aplikace pro službu Image Builder. 

```azurecli-interactive
az role assignment create \
    --assignee cf32a0cc-373c-47c9-9156-0db11f6a6dfc \
    --role Contributor \
    --scope /subscriptions/$subscriptionID/resourceGroups/$imageResourceGroup
```


## <a name="download-the-image-configuration-template-example"></a>Stažení příkladu šablony konfigurace obrázku

Byla vytvořena parametrizovaná konfigurační šablona obrázku, kterou můžete vyzkoušet. Stáhněte si ukázkový soubor JSON a nakonfigurujte jej pomocí dříve nastavených proměnných.

```azurecli-interactive
curl https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/0_Creating_a_Custom_Windows_Managed_Image/helloImageTemplateWin.json -o helloImageTemplateWin.json

sed -i -e "s/<subscriptionID>/$subscriptionID/g" helloImageTemplateWin.json
sed -i -e "s/<rgName>/$imageResourceGroup/g" helloImageTemplateWin.json
sed -i -e "s/<region>/$location/g" helloImageTemplateWin.json
sed -i -e "s/<imageName>/$imageName/g" helloImageTemplateWin.json
sed -i -e "s/<runOutputName>/$runOutputName/g" helloImageTemplateWin.json

```

Tento příklad můžete upravit v terminálu pomocí `vi`textového editoru, jako je .

```azurecli-interactive
vi helloImageTemplateLinux.json
```

> [!NOTE]
> Pro zdrojový obraz musíte vždy [zadat verzi](https://github.com/danielsollondon/azvmimagebuilder/blob/master/troubleshootingaib.md#image-version-failure), `latest`kterou nelze použít .
> Pokud přidáte nebo změníte skupinu prostředků, do které je bitová kopie distribuována, je nutné nastavit [oprávnění](#set-permissions-on-the-resource-group) ve skupině prostředků.
 
## <a name="create-the-image"></a>Vytvoření image

Odeslání konfigurace image do služby Tvůrce obrázků virtuálního počítače

```azurecli-interactive
az resource create \
    --resource-group $imageResourceGroup \
    --properties @helloImageTemplateWin.json \
    --is-full-object \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateWin01
```

Po dokončení vrátí zpráva o úspěchu zpět do `Image Builder Configuration Template` konzoly `$imageResourceGroup`a vytvořit v . Tento prostředek uvidíte ve skupině prostředků na webu Azure Portal, pokud povolíte "Zobrazit skryté typy".

Na pozadí Image Builder také vytvoří pracovní skupinu prostředků ve vašem předplatném. Tato skupina prostředků se používá pro sestavení bitové kopie. Bude v tomto formátu:`IT_<DestinationResourceGroup>_<TemplateName>`

> [!Note]
> Pracovní skupinu prostředků nesmíte odstranit přímo. Nejprve odstraňte artefakt šablony obrázku, což způsobí odstranění skupiny pracovních prostředků.

Pokud služba hlásí chybu během odeslání šablony konfigurace obrázku:
-  Projděte si tyto kroky [pro řešení potíží.](https://github.com/danielsollondon/azvmimagebuilder/blob/master/troubleshootingaib.md#template-submission-errors--troubleshooting) 
- Před opakováním odeslání budete muset šablonu odstranit pomocí následujícího fragmentu.

```azurecli-interactive
az resource delete \
    --resource-group $imageResourceGroup \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateLinux01
```

## <a name="start-the-image-build"></a>Spuštění sestavení image
Spusťte proces vytváření obrázků pomocí akce [vyvolání prostředku az](/cli/azure/resource#az-resource-invoke-action).

```azurecli-interactive
az resource invoke-action \
     --resource-group $imageResourceGroup \
     --resource-type  Microsoft.VirtualMachineImages/imageTemplates \
     -n helloImageTemplateWin01 \
     --action Run 
```

Počkejte, až bude sestavení dokončeno. To může trvat asi 15 minut.

Pokud narazíte na nějaké chyby, přečtěte si tyto kroky [řešení potíží.](https://github.com/danielsollondon/azvmimagebuilder/blob/master/troubleshootingaib.md#image-build-errors--troubleshooting)


## <a name="create-the-vm"></a>Vytvořte virtuální počítač.

Vytvořte virtuální virtuální ms pomocí vytvořené image. Nahraďte * \<>hesla* vlastním `aibuser` heslem pro virtuální počítač.

```azurecli-interactive
az vm create \
  --resource-group $imageResourceGroup \
  --name aibImgWinVm00 \
  --admin-username aibuser \
  --admin-password <password> \
  --image $imageName \
  --location $location
```

## <a name="verify-the-customization"></a>Ověření vlastního nastavení

Vytvořte připojení ke vzdálené ploše k virtuálnímu počítači pomocí uživatelského jména a hesla, které jste nastavili při vytváření virtuálního počítače. Uvnitř virtuálního provozu otevřete výzvu cmd a zadejte:

```console
dir c:\
```

Měli byste vidět tyto dva adresáře vytvořené během přizpůsobení obrázku:
- buildActions
- buildArtifacts

## <a name="clean-up"></a>Vyčištění

Po dokončení odstraňte prostředky.

### <a name="delete-the-image-builder-template"></a>Odstranění šablony tvůrce obrázků

```azurecli-interactive
az resource delete \
    --resource-group $imageResourceGroup \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateWin01
```

### <a name="delete-the-image-resource-group"></a>Odstranění skupiny prostředků obrázku

```azurecli-interactive
az group delete -n $imageResourceGroup
```


## <a name="next-steps"></a>Další kroky

Další informace o součástech souboru JSON použitého v tomto článku naleznete v [tématu Odkaz na šablonu tvůrce obrázků](../linux/image-builder-json.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
