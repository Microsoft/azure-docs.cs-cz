---
title: Vytvoření virtuálního počítače s Linuxem pomocí Azure Image Builder (preview)
description: Vytvořte virtuální počítač s Linuxem pomocí Azure Image Builder.
author: cynthn
ms.author: cynthn
ms.date: 05/02/2019
ms.topic: article
ms.service: virtual-machines-linux
ms.subservice: imaging
ms.openlocfilehash: 0d36d7db4d85ece8de77040925c535305951562b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80066673"
---
# <a name="preview-create-a-linux-vm-with-azure-image-builder"></a>Náhled: Vytvoření virtuálního počítače s Linuxem pomocí Azure Image Builder

Tento článek ukazuje, jak můžete vytvořit vlastní image Linuxu pomocí Azure Image Builder a Azure CLI. Příklad v tomto článku používá tři různé [úpravce](image-builder-json.md#properties-customize) pro přizpůsobení obrázku:

- Shell (ScriptUri) - stáhne a spustí [shell skript](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/customizeScript.sh).
- Shell (inline) - spustí konkrétní příkazy. V tomto příkladu vkládá vložkové příkazy vytvoření adresáře a aktualizaci operačního systému.
- Soubor - zkopíruje [soubor z GitHubu](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/exampleArtifacts/buildArtifacts/index.html) do adresáře na virtuálním počítači.

Můžete také zadat `buildTimeoutInMinutes`. Výchozí hodnota je 240 minut a můžete prodloužit dobu sestavení, aby bylo možné déle spuštěná sestavení.

Ke konfiguraci bitové kopie použijeme ukázkovou šablonu JSON. Soubor .json, který používáme, je zde: [helloImageTemplateLinux.json](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/0_Creating_a_Custom_Linux_Managed_Image/helloImageTemplateLinux.json). 

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

## <a name="setup-example-variables"></a>Nastavení ukázkových proměnných

Budeme používat některé informace opakovaně, takže vytvoříme některé proměnné pro ukládání těchto informací.


```console
# Resource group name - we are using myImageBuilderRG in this example
imageResourceGroup=myImageBuilerRGLinux
# Datacenter location - we are using West US 2 in this example
location=WestUS2
# Name for the image - we are using myBuilderImage in this example
imageName=myBuilderImage
# Run output name
runOutputName=aibLinux
```

Vytvořte proměnnou pro ID předplatného. Můžete si to `az account show | grep id`pomocí .

```console
subscriptionID=<Your subscription ID>
```

## <a name="create-the-resource-group"></a>Vytvořte skupinu prostředků.
Používá se k uložení artefaktu šablony konfigurace obrázku a obrázku.

```azurecli-interactive
az group create -n $imageResourceGroup -l $location
```

## <a name="set-permissions-on-the-resource-group"></a>Nastavení oprávnění pro skupinu prostředků
Udělit přispěvateli tvůrce obrázků oprávnění k vytvoření obrázku ve skupině prostředků. Bez správných oprávnění sestavení bitové kopie se nezdaří. 

Hodnota `--assignee` je ID registrace aplikace pro službu Image Builder. 

```azurecli-interactive
az role assignment create \
    --assignee cf32a0cc-373c-47c9-9156-0db11f6a6dfc \
    --role Contributor \
    --scope /subscriptions/$subscriptionID/resourceGroups/$imageResourceGroup
```

## <a name="download-the-template-example"></a>Stáhnout příklad šablony

Byla vytvořena parametrizovaná šablona konfigurace ukázkového obrazu, kterou můžete použít. Stáhněte si ukázkový soubor JSON a nakonfigurujte jej pomocí dříve nastavených proměnných.

```bash
curl https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/0_Creating_a_Custom_Linux_Managed_Image/helloImageTemplateLinux.json -o helloImageTemplateLinux.json

sed -i -e "s/<subscriptionID>/$subscriptionID/g" helloImageTemplateLinux.json
sed -i -e "s/<rgName>/$imageResourceGroup/g" helloImageTemplateLinux.json
sed -i -e "s/<region>/$location/g" helloImageTemplateLinux.json
sed -i -e "s/<imageName>/$imageName/g" helloImageTemplateLinux.json
sed -i -e "s/<runOutputName>/$runOutputName/g" helloImageTemplateLinux.json
```

Tento příklad json můžete podle potřeby upravit. Můžete například zvýšit hodnotu `buildTimeoutInMinutes` povolit delší spuštění sestavení. Soubor můžete upravit v prostředí Cloud Shell `vi`pomocí textového editoru, jako je .

```bash
vi helloImageTemplateLinux.json
```

> [!NOTE]
> Pro zdrojový obraz musíte vždy [zadat verzi](https://github.com/danielsollondon/azvmimagebuilder/blob/master/troubleshootingaib.md#image-version-failure) `latest`, kterou nelze použít .
>
> Pokud přidáte nebo změníte skupinu prostředků, ve které je bitová kopie distribuována, je třeba se ujistit, že [jsou pro skupinu prostředků nastavena oprávnění](#set-permissions-on-the-resource-group).


## <a name="submit-the-image-configuration"></a>Odeslat konfiguraci obrázku
Odeslání konfigurace image do služby Tvůrce obrázků virtuálního počítače

```azurecli-interactive
az resource create \
    --resource-group $imageResourceGroup \
    --properties @helloImageTemplateLinux.json \
    --is-full-object \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateLinux01
```

Pokud se úspěšně dokončí, vrátí zprávu o úspěchu a vytvoří artefakt šablony konfigurace tvůrce obrázků v $imageResourceGroup. Pokud povolíte možnost Zobrazit skryté typy, zobrazí se skupina prostředků na portálu.

Také na pozadí Image Builder vytvoří pracovní skupinu prostředků ve vašem předplatném. Tvůrce bitové kopie používá skupinu pracovních prostředků pro sestavení bitové kopie. Název skupiny prostředků bude v tomto `IT_<DestinationResourceGroup>_<TemplateName>`formátu: .

> [!IMPORTANT]
> Neodstraňujte skupinu pracovních prostředků přímo. Pokud odstraníte artefakt šablony obrázku, automaticky odstraní pracovní skupinu prostředků. Další informace naleznete v části [Vyčištění](#clean-up) na konci tohoto článku.

Pokud služba hlásí chybu během odeslání šablony konfigurace obrázku, podívejte se na postup [řešení potíží.](https://github.com/danielsollondon/azvmimagebuilder/blob/master/troubleshootingaib.md#template-submission-errors--troubleshooting) Před opakováním odeslání sestavení budete také muset šablonu odstranit. Odstranění šablony:

```azurecli-interactive
az resource delete \
    --resource-group $imageResourceGroup \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateLinux01
```

## <a name="start-the-image-build"></a>Spuštění sestavení image

Spusťte sestavení bitové kopie.


```azurecli-interactive
az resource invoke-action \
     --resource-group $imageResourceGroup \
     --resource-type  Microsoft.VirtualMachineImages/imageTemplates \
     -n helloImageTemplateLinux01 \
     --action Run 
```

Počkejte, dokud sestavení je dokončena, pro tento příklad může trvat 10-15 minut.

Pokud narazíte na nějaké chyby, přečtěte si tyto kroky [řešení potíží.](https://github.com/danielsollondon/azvmimagebuilder/blob/master/troubleshootingaib.md#image-build-errors--troubleshooting)


## <a name="create-the-vm"></a>Vytvořte virtuální počítač.

Vytvořte virtuální virtuální ms pomocí vytvořené image.

```azurecli-interactive
az vm create \
  --resource-group $imageResourceGroup \
  --name myVM \
  --admin-username azureuser \
  --image $imageName \
  --location $location \
  --generate-ssh-keys
```

Získejte IP adresu z výstupu vytvoření virtuálního počítačů a použít jej k SSH do virtuálního počítačů.

```bash
ssh azureuser@<pubIp>
```

Měli byste vidět, že obraz byl přizpůsoben zprávou dne, jakmile je vaše připojení SSH navázáno!

```output

*******************************************************
**            This VM was built from the:            **
**      !! AZURE VM IMAGE BUILDER Custom Image !!    **
**         You have just been Customized :-)         **
*******************************************************
```

Po `exit` dokončení zavření připojení SSH zadejte.

## <a name="check-the-source"></a>Zkontrolujte zdroj

V šabloně tvůrce obrázků se v části Vlastnosti zobrazí zdrojový obrázek, skript přizpůsobení, který spouští, a místo, kde je distribuován.

```bash
cat helloImageTemplateLinux.json
```

Podrobnější informace o tomto souboru JSON naleznete v [tématu Odkaz na šablonu tvůrce obrázků](image-builder-json.md)

## <a name="clean-up"></a>Vyčištění

Po dokončení můžete prostředky odstranit.

Odstraňte šablonu tvůrce obrázků.

```azurecli-interactive
az resource delete \
    --resource-group $imageResourceGroup \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateLinux01
```

Odstraňte skupinu prostředků obrázku.

```azurecli
az group delete -n $imageResourceGroup
```


## <a name="next-steps"></a>Další kroky

Další informace o součástech souboru JSON použitého v tomto článku naleznete v [tématu Odkaz na šablonu tvůrce obrázků](image-builder-json.md).
