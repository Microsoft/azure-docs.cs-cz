---
title: Vytvoření virtuálního počítače s Linuxem pomocí Image Builder pro Azure (preview)
description: Vytvoření virtuálního počítače s Linuxem pomocí Azure Image Builder.
author: cynthn
ms.author: cynthn
ms.date: 05/02/2019
ms.topic: article
ms.service: virtual-machines-linux
manager: gwallace
ms.openlocfilehash: 2966a1803d0664312d71ba992a5ba65f73b27370
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/09/2019
ms.locfileid: "67667513"
---
# <a name="preview-create-a-linux-vm-with-azure-image-builder"></a>Verze Preview: Vytvoření virtuálního počítače s Linuxem pomocí Azure Image Builder

Tento článek popisuje, jak můžete vytvořit vlastní image Linuxu s použitím Image Builder pro Azure a Azure CLI. V příkladu v tomto článku se používá tři různé [úpravcům přidávat nové](image-builder-json.md#properties-customize) pro přizpůsobení image:

- Prostředí (adresa_uri-skriptu) – soubory ke stažení a spuštění [skript prostředí](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/customizeScript.sh).
- Prostředí (inline) - spuštění konkrétních příkazů. V tomto příkladu vložené příkazy zahrnují vytváření adresáře a aktualizaci operačního systému.
- Soubor – zkopíruje [souboru z Githubu](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/exampleArtifacts/buildArtifacts/index.html) do adresáře na virtuálním počítači.


Použijeme Ukázková šablona .json konfigurace image. Soubor .json, který se používá, je zde: [helloImageTemplateLinux.json](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/0_Creating_a_Custom_Linux_Managed_Image/helloImageTemplateLinux.json). 

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

## <a name="setup-example-variables"></a>Instalační program příklad proměnné

Použijeme některé údaje opakovaně, takže si vytvoříme několik proměnných k ukládání těchto informací.


```azurecli-interactive
# Resource group name - we are using myImageBuilderRG in this example
imageResourceGroup=myImageBuilerRGLinux
# Datacenter location - we are using West US 2 in this example
location=WestUS2
# Name for the image - we are using myBuilderImage in this example
imageName=myBuilderImage
# Run output name
runOutputName=aibLinux
```

Vytvoření proměnné pro ID vašeho předplatného. Můžete získat pomocí `az account show | grep id`.

```azurecli-interactive
subscriptionID=<Your subscription ID>
```

## <a name="create-the-resource-group"></a>Vytvořte skupinu prostředků.
To se používá k ukládání artefaktů šablony konfigurace image a image.

```azurecli-interactive
az group create -n $imageResourceGroup -l $location
```

## <a name="set-permissions-on-the-resource-group"></a>Nastavení oprávnění ve skupině prostředků.
Udělení oprávnění "Přispěvatel" Image Builder pro vytvoření bitové kopie ve skupině prostředků. Bez správná oprávnění bitové kopie sestavení se nezdaří. 

`--assignee` Hodnota je ID registrace aplikace pro Image Builder pro službu. 

```azurecli-interactive
az role assignment create \
    --assignee cf32a0cc-373c-47c9-9156-0db11f6a6dfc \
    --role Contributor \
    --scope /subscriptions/$subscriptionID/resourceGroups/$imageResourceGroup
```

## <a name="download-the-template-example"></a>Stažení příkladu šablony

Můžete použít se vytvořila image parametrizované ukázka konfigurace šablony. Stáhněte si ukázkový soubor .json a nakonfigurovat proměnné, které jste nastavili dříve.

```azurecli-interactive
curl https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/0_Creating_a_Custom_Linux_Managed_Image/helloImageTemplateLinux.json -o helloImageTemplateLinux.json

sed -i -e "s/<subscriptionID>/$subscriptionID/g" helloImageTemplateLinux.json
sed -i -e "s/<rgName>/$imageResourceGroup/g" helloImageTemplateLinux.json
sed -i -e "s/<region>/$location/g" helloImageTemplateLinux.json
sed -i -e "s/<imageName>/$imageName/g" helloImageTemplateLinux.json
sed -i -e "s/<runOutputName>/$runOutputName/g" helloImageTemplateLinux.json
```

Tento příklad .json lze upravovat podle potřeby. Například můžete zvýšit hodnotu `buildTimeoutInMinutes` umožňující delší spuštěného sestavení. Můžete upravit soubor ve službě Cloud Shell pomocí `vi`.

```azurecli-interactive
vi helloImageTemplateLinux.json
```

> [!NOTE]
> Zdroj bitové kopie, je nutné vždy [určit verzi](https://github.com/danielsollondon/azvmimagebuilder/blob/master/troubleshootingaib.md#image-version-failure), nemůžete použít `latest`.
>
> Je-li přidat nebo změnit skupinu prostředků, ve kterém probíhá distribuce image, budete muset Ujistěte se, že [oprávnění nastavená pro skupinu prostředků](#set-permissions-on-the-resource-group).


## <a name="submit-the-image-configuration"></a>Odeslání konfigurace image
Odeslání image konfigurace ve službě VM Image Builder

```azurecli-interactive
az resource create \
    --resource-group $imageResourceGroup \
    --properties @helloImageTemplateLinux.json \
    --is-full-object \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateLinux01
```

Pokud se úspěšně dokončí, bude vrátí zprávu o úspěšném dokončení a vytvořit image builder konfigurace šablony artefakt v $imageResourceGroup. Skupinu prostředků na portálu můžete zobrazit, pokud povolíte možnost "Zobrazit skryté typy".

Navíc na pozadí vytvoří Image Builder pracovní skupiny prostředků ve vašem předplatném. Image Builder používá pracovní skupiny prostředků pro sestavení image. Název skupiny prostředků, bude mít tento formát: `IT_<DestinationResourceGroup>_<TemplateName>`.

> [!IMPORTANT]
> Přímo neodstraňujte pracovní skupiny prostředků. Při odstranění artefaktů image šablony, odstraní automaticky pracovní skupiny prostředků. Další informace najdete v tématu [vyčištění](#clean-up) oddílu na konci tohoto článku.

Pokud služba hlásí selhání při odesílání šablony konfigurace image, najdete v článku [řešení potíží s](https://github.com/danielsollondon/azvmimagebuilder/blob/master/troubleshootingaib.md#template-submission-errors--troubleshooting) kroky. Musíte také odstranit šablonu před dalším pokusem o odeslání sestavení. Pokud chcete odstranit šablonu:

```azurecli-interactive
az resource delete \
    --resource-group $imageResourceGroup \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateLinux01
```

## <a name="start-the-image-build"></a>Spustit sestavení image

Spusťte sestavení image.


```azurecli-interactive
az resource invoke-action \
     --resource-group $imageResourceGroup \
     --resource-type  Microsoft.VirtualMachineImages/imageTemplates \
     -n helloImageTemplateLinux01 \
     --action Run 
```

Počkejte, než se dokončí, v tomto příkladu sestavení, může trvat 10 až 15 minut.

Pokud narazíte na chyby, přečtěte si prosím tyto [řešení potíží s](https://github.com/danielsollondon/azvmimagebuilder/blob/master/troubleshootingaib.md#image-build-errors--troubleshooting) kroky.


## <a name="create-the-vm"></a>Vytvořte virtuální počítač.

Vytvoření virtuálního počítače pomocí image, kterou jste vytvořili.

```azurecli-interactive
az vm create \
  --resource-group $imageResourceGroup \
  --name myVM \
  --admin-username azureuser \
  --image $imageName \
  --location $location \
  --generate-ssh-keys
```

Získejte IP adresu z výstupu vytváření virtuálního počítače a použít ho k SSH k virtuálnímu počítači.

```azurecli-interactive
ssh azureuser@<pubIp>
```

Měli byste vidět, že byl upraven na obrázku a zobrazí se zpráva dne, jakmile se naváže připojení SSH.

```console

*******************************************************
**            This VM was built from the:            **
**      !! AZURE VM IMAGE BUILDER Custom Image !!    **
**         You have just been Customized :-)         **
*******************************************************
```

Typ `exit` po dokončení ukončete připojení SSH.

## <a name="check-the-source"></a>Kontrola zdroje

V šabloně Tvůrce bitové kopie 'Vlastnosti', zobrazí se zdrojového obrázku, přizpůsobení skriptu běží, a kde je distribuován.

```azurecli-interactive
cat helloImageTemplateLinux.json
```

Podrobné informace o tomto souboru .json, naleznete v tématu [Image builder referenčními informacemi k šablonám](image-builder-json.md)

## <a name="clean-up"></a>Vyčištění

Jakmile budete hotovi, můžete odstranit prostředky.

Odstraňte šablonu image builder.

```azurecli-interactive
az resource delete \
    --resource-group $imageResourceGroup \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateLinux01
```

Odstraníte skupinu prostředků obrázků.

```bash
az group delete -n $imageResourceGroup
```


## <a name="next-steps"></a>Další postup

Další informace o součástech soubor .json, používané v tomto článku najdete v tématu [referenčními informacemi k šablonám Image Builder](image-builder-json.md).
