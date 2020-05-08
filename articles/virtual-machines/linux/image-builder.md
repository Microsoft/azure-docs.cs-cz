---
title: Vytvoření virtuálního počítače se systémem Linux pomocí nástroje Azure image Builder (Preview)
description: Vytvořte virtuální počítač se systémem Linux pomocí nástroje Azure image Builder.
author: cynthn
ms.author: cynthn
ms.date: 05/02/2019
ms.topic: how-to
ms.service: virtual-machines-linux
ms.subservice: imaging
ms.openlocfilehash: b50b2a9bfca99e1868c083759cce26bb176789f4
ms.sourcegitcommit: e0330ef620103256d39ca1426f09dd5bb39cd075
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/05/2020
ms.locfileid: "82792407"
---
# <a name="preview-create-a-linux-vm-with-azure-image-builder"></a>Verze Preview: Vytvoření virtuálního počítače se systémem Linux pomocí Azure image Builder

V tomto článku se dozvíte, jak vytvořit přizpůsobenou image pro Linux pomocí Azure image Builder a Azure CLI. Příklad v tomto článku [používá pro přizpůsobení](image-builder-json.md#properties-customize) image tři různé úpravy:

- Shell (ScriptUri) – stáhne a spustí [skript prostředí](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/customizeScript.sh).
- Shell (inlineed) – spustí konkrétní příkazy. V tomto příkladu vložené příkazy zahrnují vytvoření adresáře a aktualizaci operačního systému.
- Soubor – zkopíruje [soubor z GitHubu](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/exampleArtifacts/buildArtifacts/index.html) do adresáře na virtuálním počítači.

Můžete také zadat `buildTimeoutInMinutes`. Výchozí hodnota je 240 minut a můžete prodloužit dobu sestavování tak, aby umožňovala delší spuštění sestavení.

K nakonfigurování image budeme používat šablonu Sample. JSON. Soubor. JSON, který používáme, je tady: [helloImageTemplateLinux. JSON](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/0_Creating_a_Custom_Linux_Managed_Image/helloImageTemplateLinux.json). 

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

## <a name="setup-example-variables"></a>Příklady nastavení proměnných

Některé informace budeme používat opakovaně, takže vytvoříme některé proměnné, které tyto informace uloží.


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

Vytvořte proměnnou pro ID předplatného. Můžete to získat pomocí `az account show | grep id`.

```console
subscriptionID=<Your subscription ID>
```

## <a name="create-the-resource-group"></a>Vytvořte skupinu prostředků.
Slouží k uložení artefaktu šablony konfigurace image a image.

```azurecli-interactive
az group create -n $imageResourceGroup -l $location
```

## <a name="set-permissions-on-the-resource-group"></a>Nastavení oprávnění pro skupinu prostředků
Udělte přispěvateli image Builder oprávnění k vytvoření image ve skupině prostředků. Bez správných oprávnění se sestavení image nezdaří. 

`--assignee` Hodnota je ID registrace aplikace pro službu Tvůrce imagí. 

```azurecli-interactive
az role assignment create \
    --assignee cf32a0cc-373c-47c9-9156-0db11f6a6dfc \
    --role Contributor \
    --scope /subscriptions/$subscriptionID/resourceGroups/$imageResourceGroup
```

## <a name="download-the-template-example"></a>Stažení příkladu šablony

Vytvořili jste parametrizovanou šablonu konfigurace obrázku pro použití. Stáhněte si soubor Sample. JSON a nakonfigurujte ho pomocí proměnných, které jste nastavili dříve.

```bash
curl https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/0_Creating_a_Custom_Linux_Managed_Image/helloImageTemplateLinux.json -o helloImageTemplateLinux.json

sed -i -e "s/<subscriptionID>/$subscriptionID/g" helloImageTemplateLinux.json
sed -i -e "s/<rgName>/$imageResourceGroup/g" helloImageTemplateLinux.json
sed -i -e "s/<region>/$location/g" helloImageTemplateLinux.json
sed -i -e "s/<imageName>/$imageName/g" helloImageTemplateLinux.json
sed -i -e "s/<runOutputName>/$runOutputName/g" helloImageTemplateLinux.json
```

V případě potřeby můžete tento příklad. JSON upravit. Můžete například zvýšit hodnotu `buildTimeoutInMinutes` tak, aby umožňovala delší spuštění sestavení. Soubor můžete upravit v Cloud Shell pomocí textového editoru, jako `vi`je.

```bash
vi helloImageTemplateLinux.json
```

> [!NOTE]
> Pro zdrojovou image musíte vždycky [zadat verzi](https://github.com/danielsollondon/azvmimagebuilder/blob/master/troubleshootingaib.md#image-version-failure), kterou nemůžete použít `latest`.
>
> Pokud přidáte nebo změníte skupinu prostředků, ve které je bitová kopie distribuována, musíte zajistit, aby byla [pro skupinu prostředků nastavena oprávnění](#set-permissions-on-the-resource-group).


## <a name="submit-the-image-configuration"></a>Odeslat konfiguraci image
Odeslat konfiguraci image do služby tvůrce imagí VM

```azurecli-interactive
az resource create \
    --resource-group $imageResourceGroup \
    --properties @helloImageTemplateLinux.json \
    --is-full-object \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateLinux01
```

Pokud se úspěšně dokončí, vrátí zprávu o úspěchu a vytvoří artefakt šablony konfigurace tvůrce imagí v $imageResourceGroup. Pokud povolíte možnost Zobrazit skryté typy, můžete zobrazit skupinu prostředků na portálu.

Na pozadí vytvoří nástroj image Builder v předplatném taky pracovní skupinu prostředků. Nástroj image Builder používá pracovní skupinu prostředků pro sestavení image. Název skupiny prostředků bude v tomto formátu: `IT_<DestinationResourceGroup>_<TemplateName>`.

> [!IMPORTANT]
> Neodstraňujte pracovní skupinu prostředků přímo. Pokud odstraníte artefakt šablony image, automaticky se odstraní pracovní skupina prostředků. Další informace najdete v části [Vyčištění](#clean-up) na konci tohoto článku.

Pokud služba ohlásí chybu během odesílání šablony konfigurace bitové kopie, přečtěte si postup [řešení potíží](https://github.com/danielsollondon/azvmimagebuilder/blob/master/troubleshootingaib.md#template-submission-errors--troubleshooting) . Bude také nutné odstranit šablonu, aby bylo možné znovu odeslat sestavení. Odstranění šablony:

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

Počkejte na dokončení sestavení, v tomto případě může trvat 10-15 minut.

Pokud narazíte na nějaké chyby, přečtěte si prosím tyto kroky pro [řešení potíží](https://github.com/danielsollondon/azvmimagebuilder/blob/master/troubleshootingaib.md#image-build-errors--troubleshooting) .


## <a name="create-the-vm"></a>Vytvořte virtuální počítač.

Vytvořte virtuální počítač pomocí Image, kterou jste vytvořili.

```azurecli-interactive
az vm create \
  --resource-group $imageResourceGroup \
  --name myVM \
  --admin-username azureuser \
  --image $imageName \
  --location $location \
  --generate-ssh-keys
```

Získejte IP adresu z výstupu vytvoření virtuálního počítače a použijte ho pro SSH k virtuálnímu počítači.

```bash
ssh azureuser@<pubIp>
```

Měli byste vidět, že obrázek byl přizpůsoben se zprávou dne, jakmile se naváže připojení SSH.

```output

*******************************************************
**            This VM was built from the:            **
**      !! AZURE VM IMAGE BUILDER Custom Image !!    **
**         You have just been Customized :-)         **
*******************************************************
```

Zadejte `exit` , kdy jste hotovi s cílem zavřít připojení SSH.

## <a name="check-the-source"></a>Ověřit zdroj

V šabloně tvůrce imagí se ve vlastnostech zobrazí zdrojová image, skript vlastního nastavení, který se spustí a kde se distribuuje.

```bash
cat helloImageTemplateLinux.json
```

Podrobnější informace o tomto souboru. JSON najdete v tématu [Referenční dokumentace k šablonám tvůrce imagí](image-builder-json.md) .

## <a name="clean-up"></a>Vyčištění

Až budete hotovi, můžete prostředky odstranit.

Odstraňte šablonu tvůrce imagí.

```azurecli-interactive
az resource delete \
    --resource-group $imageResourceGroup \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateLinux01
```

Odstraňte skupinu prostředků image.

```azurecli
az group delete -n $imageResourceGroup
```


## <a name="next-steps"></a>Další kroky

Další informace o součástech souboru. JSON používaných v tomto článku najdete v tématu Referenční dokumentace k [šablonám tvůrce imagí](image-builder-json.md).
