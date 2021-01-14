---
title: Úloha DevOps služby Azure image Builder
description: Úloha Azure DevOps k vložení artefaktů sestavení do image virtuálního počítače, abyste mohli nainstalovat a nakonfigurovat svoji aplikaci a operační systém.
author: danielsollondon
ms.author: danis
ms.date: 08/10/2020
ms.topic: article
ms.service: virtual-machines
ms.subservice: imaging
ms.openlocfilehash: 43447454b82b74c10b1d53c41c7883b0b9bef242
ms.sourcegitcommit: 2bd0a039be8126c969a795cea3b60ce8e4ce64fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/14/2021
ms.locfileid: "98196499"
---
# <a name="azure-image-builder-service-devops-task"></a>Úloha DevOps služby Azure image Builder

V tomto článku se dozvíte, jak pomocí úlohy Azure DevOps vložit artefakty sestavení do image virtuálního počítače, abyste mohli nainstalovat a nakonfigurovat svou aplikaci a operační systém.

## <a name="devops-task-versions"></a>Verze úloh DevOps
Existují dva úlohy DevOps pro sestavovatele bitových kopií virtuálních počítačů (AIB) Azure:

* [Úkol "stabilní" AIB](https://marketplace.visualstudio.com/items?itemName=AzureImageBuilder.devOps-task-for-azure-image-builder), který nám umožňuje předcházet nejnovějším aktualizacím a funkcím, umožnit zákazníkům jejich otestování před tím, než ho budeme povýšit na úkol "stabilní", přibližně 1 týden později. 


* [Nestabilní úloha AIB](https://marketplace.visualstudio.com/items?itemName=AzureImageBuilder.devOps-task-for-azure-image-builder-canary), která nám umožní umístit do nejnovějších aktualizací a funkcí a umožnit zákazníkům jejich otestování, než ji povýšíme na "stabilní" úlohu. Pokud neexistují žádné nahlášené problémy a naše telemetrie nezobrazuje žádné problémy, přibližně 1 týden později, budeme povýšit kód úlohy na stabilní. 

## <a name="prerequisites"></a>Požadavky

* Nainstalujte [stabilní úlohu DevOps z Visual Studio Marketplace](https://marketplace.visualstudio.com/items?itemName=AzureImageBuilder.devOps-task-for-azure-image-builder).
* Musíte mít účet VSTS DevOps a vytvořený kanál sestavení.
* Zaregistrujte a povolte požadavky funkcí Tvůrce imagí v předplatném, které používají kanály:
    * [AZ PowerShell](../windows/image-builder-powershell.md#register-features)
    * [AZ CLI](../windows/image-builder.md#register-the-features)
    
* Vytvořte ve skupině prostředků zdrojové image standard Azure Storage účet, můžete použít jiné skupiny prostředků nebo účty úložiště. Účet úložiště se používá k přenosu artefaktů sestavení z úlohy DevOps do bitové kopie.

    ```powerShell
    # Az PowerShell
    $timeInt=$(get-date -UFormat "%s")
    $storageAccName="aibstorage"+$timeInt
    $location=westus
    # create storage account and blob in resource group
    New-AzStorageAccount -ResourceGroupName $strResourceGroup -Name $storageAccName -Location $location -SkuName Standard_LRS
    ```

    ```bash
    # Az CLI
    location=westus
    scriptStorageAcc=aibstordot$(date +'%s')
    # create storage account and blob in resource group
    az storage account create -n $scriptStorageAcc -g $strResourceGroup -l $location --sku Standard_LRS
    ```

## <a name="add-task-to-release-pipeline"></a>Přidat úlohu do kanálu uvolnění

Vybrat   >  **úpravu** kanálu vydaných verzí

V uživatelském agentovi vyberte, pokud *+* chcete přidat a pak vyhledat **Image Builder**. Vyberte **Add** (Přidat).

Nastavte následující vlastnosti úlohy:

### <a name="azure-subscription"></a>Předplatné Azure

V rozevírací nabídce vyberte předplatné, ve kterém chcete tvůrce imagí spustit. Použijte stejné předplatné, ve kterém jsou umístěné zdrojové image a kde se mají distribuovat obrázky. Musíte autorizovat oprávnění přispěvatele image Builder k předplatnému nebo skupině prostředků.

### <a name="resource-group"></a>Skupina prostředků

Použijte skupinu prostředků, do které se uloží artefakt šablony dočasné image. Při vytváření artefaktu šablony se vytvoří další dočasná skupina prostředků tvůrce imagí `IT_<DestinationResourceGroup>_<TemplateName>_guid` . Dočasná skupina prostředků ukládá metadata imagí, například skripty. Na konci úlohy se odstraní artefakt šablony imagí a dočasná skupina prostředků tvůrce imagí.
 
### <a name="location"></a>Umístění

Umístění je oblast, kde se spustí Tvůrce imagí. Podporován je pouze nastavený počet [oblastí](../windows/image-builder-overview.md#regions) . V tomto umístění se musí nacházet zdrojové image. Pokud například používáte sdílenou galerii imagí, musí replika existovat v této oblasti.

### <a name="managed-identity-required"></a>Spravovaná identita (povinné)
Image Builder vyžaduje spravovanou identitu, kterou používá ke čtení zdrojových vlastních imagí, připojení k Azure Storage a vytváření vlastních imagí. Další podrobnosti najdete [tady](./image-builder-overview.md#permissions).

### <a name="vnet-support"></a>Podpora virtuální sítě

V současné době úloha DevOps nepodporuje zadání existující podsítě, ale pokud chcete využít existující virtuální síť, můžete použít šablonu ARM s vnořenou šablonou tvůrce imagí. Podívejte se na příklady šablon imagí Windows Image Builder, jak to dosáhnete, nebo můžete použít [AZ AIB PowerShell](../windows/image-builder-powershell.md).

### <a name="source"></a>Zdroj

Zdrojové image musí mít podporovanou verzi nástroje pro sestavovatele bitových kopií (OSs). Můžete zvolit existující vlastní image ve stejné oblasti, ve které je spuštěný nástroj image Builder:
* Spravovaná Image – je třeba předat ID resourceId, například:
    ```json
    /subscriptions/<subscriptionID>/resourceGroups/<rgName>/providers/Microsoft.Compute/images/<imageName>
    ```
* Galerie sdílených imagí Azure – je třeba předat identifikátory resourceId verze image, například:
    ```json
    /subscriptions/$subscriptionID/resourceGroups/$sigResourceGroup/providers/Microsoft.Compute/galleries/$sigName/images/$imageDefName/versions/<versionNumber>
    ```

    Pokud potřebujete získat nejnovější verzi Galerie sdílených imagí, můžete mít úlohu AZ PowerShell nebo AZ CLI, která bude mít nejnovější verzi, a nastavit proměnnou DevOps. V úloze AZ VM Image Builder DevOps použijte proměnnou. Další informace najdete v [příkladech](https://github.com/danielsollondon/azvmimagebuilder/tree/master/solutions/8_Getting_Latest_SIG_Version_ResID#getting-the-latest-image-version-resourceid-from-shared-image-gallery).

* Marketplace Základní obrázek: v rozevíracím seznamu oblíbených imagí se vždycky používá nejnovější verze podporovaného operačního systému. 

    Pokud základní obrázek není v seznamu, můžete zadat přesnou Image pomocí `Publisher:Offer:Sku` .

    Základní verze Image (volitelné) – můžete zadat verzi image, kterou chcete použít. výchozí hodnota je `latest` .

### <a name="customize"></a>Přizpůsobení

#### <a name="provisioner"></a>Zřizovací

Zpočátku jsou podporované dva Úpravové prostředí – **Shell** a **PowerShell**. Je podporována pouze vložená vložení. Pokud chcete skripty stáhnout, můžete k tomu předat vložené příkazy.

Pro váš operační systém vyberte PowerShell nebo Shell.

#### <a name="windows-update-task"></a>web Windows Update úkol

V případě systému Windows je úloha spuštěna web Windows Update na konci úprav. Zpracovává požadovaná restartování.

Spustí se následující konfigurace web Windows Update:
```json
    "type": "WindowsUpdate",
    "searchCriteria": "IsInstalled=0",
    "filters": [
        "exclude:$_.Title -like '*Preview*'",
        "include:$true"
```
Nainstaluje důležité a doporučené aktualizace systému Windows, které nejsou ve verzi Preview.

#### <a name="handling-reboots"></a>Zpracování restartování
V současné době úloha DevOps neobsahuje podporu pro restartování sestavení Windows. Pokud se pokusíte restartovat kód PowerShellu, sestavení se nezdaří. Kód však můžete použít k restartování sestavení systému Linux.

#### <a name="build-path"></a>Cesta sestavení

Úkol je navržený tak, aby bylo možné do image vložit artefakty vydané DevOps sestavení. Chcete-li tuto práci provést, je třeba nastavit kanál sestavení. V nastavení kanálu vydání musíte přidat úložiště artefaktů sestavení.

:::image type="content" source="./media/image-builder-devops-task/add-artifact.png" alt-text="Vyberte Přidat artefakt do kanálu vydání.":::

Vyberte tlačítko **cesta sestavení** a zvolte složku sestavení, kterou chcete umístit na obrázek. Úloha tvůrce imagí zkopíruje všechny soubory a adresáře, které jsou v něm obsažené. Při vytváření bitové kopie nasadí tvůrce imagí soubory a adresáře do různých cest v závislosti na operačním systému.

> [!IMPORTANT]
> Při přidávání artefaktu úložiště může být adresář s předponou podtržítka *_*. Podtržítko může způsobit problémy s vloženými příkazy. Použijte v příkazech příslušné uvozovky.
> 

Následující příklad vysvětluje, jak to funguje:

:::image type="content" source="./media/image-builder-devops-task/build-artifacts.png" alt-text="Adresářová struktura znázorňující hierarchii.":::


* Windows – soubory existují v `C:\` . Vytvoří se adresář s názvem, `buildArtifacts` který obsahuje `webapp` adresář.

* Linux – soubory existují v  `/tmp` . `webapp`Vytvoří se adresář, který bude obsahovat všechny soubory a adresáře. Soubory je nutné přesunout z tohoto adresáře. V opačném případě se odstraní, protože se nachází v dočasném adresáři.

#### <a name="inline-customization-script"></a>Vložený skript pro přizpůsobení

* Windows – můžete zadat vložené příkazy prostředí PowerShell oddělené čárkami. Pokud chcete spustit skript v adresáři sestavení, můžete použít:

    ```PowerShell
    & 'c:\buildArtifacts\webapp\webconfig.ps1'
    ```

* Linux – v systémech Linux jsou artefakty sestavení vloženy do `/tmp` adresáře. V mnoha systémech Linux OSs se ale při restartování odstraní obsah adresáře adresáře/TMP. Pokud chcete, aby artefakty v imagi existovaly, musíte vytvořit další adresář a zkopírovat je přes.  Příklad:

    ```bash
    sudo mkdir /lib/buildArtifacts
    sudo cp -r "/tmp/_ImageBuilding/webapp" /lib/buildArtifacts/.
    ```
    
    Pokud pracujete v aplikaci ok pomocí adresáře "adresáře/tmp", můžete skript spustit pomocí následujícího kódu.
    
    ```bash
    # grant execute permissions to execute scripts
    sudo chmod +x "/tmp/_ImageBuilding/webapp/coreConfig.sh"
    echo "running script"
    sudo . "/tmp/AppsAndImageBuilderLinux/_WebApp/coreConfig.sh"
    ```
    
#### <a name="what-happens-to-the-build-artifacts-after-the-image-build"></a>Co se stane s artefakty sestavení po sestavení bitové kopie?

> [!NOTE]
> Nástroj image Builder automaticky neodebere artefakty sestavení, důrazně doporučujeme, abyste vždy měli kód pro odebrání artefaktů sestavení.
> 

* Windows – nástroj image Builder nasadí soubory do `c:\buildArtifacts` adresáře. Adresář je trvalý, je nutné odebrat adresář. Můžete ho odebrat ve skriptu, který spustíte. Příklad:

    ```PowerShell
    # Clean up buildArtifacts directory
    Remove-Item -Path "C:\buildArtifacts\*" -Force -Recurse
    
    # Delete the buildArtifacts directory
    Remove-Item -Path "C:\buildArtifacts" -Force 
    ```
    
* Linux – artefakty sestavení jsou vloženy do `/tmp` adresáře. V mnoha systémech Linux OSs se ale při restartování `/tmp` odstraní obsah adresáře. Doporučujeme, abyste měli kód pro odebrání obsahu a nespoléhá se na operační systém, aby se obsah odebral. Příklad:

    ```bash
    sudo rm -R "/tmp/AppsAndImageBuilderLinux"
    ```
    
#### <a name="total-length-of-image-build"></a>Celková délka sestavení obrázku

V úloze kanálu DevOps se zatím nedají změnit celkovou délku. Používá výchozí hodnotu 240 minut. Pokud chcete zvýšit [buildTimeoutInMinutes](./image-builder-json.md#properties-buildtimeoutinminutes), můžete použít úlohu AZ CLI v kanálu vydání. Nakonfigurujte úkol pro zkopírování šablony a odeslání. Příklad najdete v tomto [řešení](https://github.com/danielsollondon/azvmimagebuilder/tree/master/solutions/4_Using_ENV_Variables#using-environment-variables-and-parameters-with-image-builder), nebo použijte AZ PowerShell.


#### <a name="storage-account"></a>Účet úložiště

Vyberte účet úložiště, který jste vytvořili v části požadavky. Pokud ho v seznamu nevidíte, tvůrce imagí k němu nemá oprávnění.

Po spuštění sestavení vytvoří Tvůrce imagí kontejner s názvem `imagebuilder-vststask` . Kontejner je místo, kde jsou uloženy artefakty sestavení z úložiště.

> [!NOTE]
> Po každém sestavení musíte účet úložiště nebo kontejner odstranit ručně.
>

### <a name="distribute"></a>Rozmístit

Podporují se 3 distribuované typy.

#### <a name="managed-image"></a>Spravovaná image

* Prostředku
    ```bash
    /subscriptions/<subscriptionID>/resourceGroups/<rgName>/providers/Microsoft.Compute/images/<imageName>
    ```

* Umístění

#### <a name="azure-shared-image-gallery"></a>Galerie sdílených imagí Azure

Sdílená Galerie imagí již **musí** existovat.

* Prostředku 
    ```bash
    /subscriptions/<subscriptionID>/resourceGroups/<rgName>/providers/Microsoft.Compute/galleries/<galleryName>/images/<imageDefName>
    ```

* Oblasti: seznam oblastí, oddělené čárkami. Například westus, eastus, centralus

#### <a name="vhd"></a>VHD

Do této položky nemůžete předat žádné hodnoty. tvůrce imagí vygeneruje VHD do dočasné skupiny prostředků pro tvůrce imagí, `IT_<DestinationResourceGroup>_<TemplateName>` v kontejneru *VHD* . Když spustíte sestavení pro vydání, nástroj image Builder vygeneruje protokoly. Až se dokončí, vygeneruje adresu URL virtuálního pevného disku.

### <a name="optional-settings"></a>Volitelná nastavení

* [Velikost virtuálního počítače](image-builder-json.md#vmprofile) – velikost virtuálního počítače můžete přepsat z výchozí *Standard_D1_v2*. Můžete přepsat, aby se snížil celkový čas přizpůsobení, nebo proto, že chcete vytvořit image závislé na některých velikostech virtuálních počítačů, například GPU/HPC atd.

## <a name="how-it-works"></a>Jak to funguje

Při vytváření vydaných verzí úloha vytvoří kontejner v účtu úložiště s názvem *imagebuilder-vststask*. Zips a nahraje artefakty sestavení a vytvoří token SAS pro soubor zip.

Úloha pomocí vlastností předaných úloze vytvoří artefakt šablony tvůrce imagí. Úkol provede následující akce:
* Stáhne soubor zip artefaktu sestavení a všechny další přidružené skripty. Soubory se ukládají v účtu úložiště v dočasné skupině prostředků tvůrce imagí `IT_<DestinationResourceGroup>_<TemplateName>` .
* Vytvoří předpevněnou šablonu Template *T_* a monotónní celé číslo s 10 číslicemi. Šablona se uloží do skupiny prostředků, kterou jste vybrali. Šablona existuje po dobu trvání sestavení ve skupině prostředků. 

Příklad výstupu:

```text
start reading task parameters...
found build at:  /home/vsts/work/r1/a/_ImageBuilding/webapp
end reading parameters
getting storage account details for aibstordot1556933914
created archive /home/vsts/work/_temp/temp_web_package_21475337782320203.zip
Source for image:  { type: 'SharedImageVersion',
  imageVersionId: '/subscriptions/<subscriptionID>/resourceGroups/<rgName>/providers/Microsoft.Compute/galleries/<galleryName>/images/<imageDefName>/versions/<imgVersionNumber>' }
template name:  t_1556938436xxx
starting put template...
```

Po spuštění sestavení image se v protokolech vydaných verzí hlásí stav spuštění:

```text
starting run template...
```

Po dokončení sestavení obrázku se zobrazí výstup podobný následujícímu textu:

```text
2019-05-06T12:49:52.0558229Z starting run template...
2019-05-06T13:36:33.8863094Z run template:  Succeeded
2019-05-06T13:36:33.8867768Z getting runOutput for  SharedImage_distribute
2019-05-06T13:36:34.6652541Z ==============================================================================
2019-05-06T13:36:34.6652925Z ## task output variables ##
2019-05-06T13:36:34.6658728Z $(imageUri) =  /subscriptions/<subscriptionID>/resourceGroups/aibwinsig/providers/Microsoft.Compute/galleries/my22stSIG/images/winWAppimages/versions/0.23760.13763
2019-05-06T13:36:34.6659989Z ==============================================================================
2019-05-06T13:36:34.6663500Z deleting template t_1557146959485...
2019-05-06T13:36:34.6673713Z deleting storage blob imagebuilder-vststask\webapp/18-1/webapp_1557146958741.zip
2019-05-06T13:36:34.9786039Z blob imagebuilder-vststask\webapp/18-1/webapp_1557146958741.zip is deleted
2019-05-06T13:38:37.4884068Z delete template:  Succeeded
```

Šablona obrázku a `IT_<DestinationResourceGroup>_<TemplateName>` je odstraněna.

Můžete převzít proměnnou VSTS ' $ (imageUri) ' a použít ji v dalším úkolu nebo ji použít jenom a vytvořit virtuální počítač.

## <a name="output-devops-variables"></a>Výstupní proměnné DevOps

Publikování/nabídka/SKU/verze zdrojové image Marketplace:
* $ (pirPublisher)
* $ (pirOffer)
* $ (pirSku)
* $ (pirVersion)

Identifikátor URI image – ResourceID distribuované Image:
* $ (imageUri)

## <a name="faq"></a>Časté otázky

### <a name="can-i-use-an-existing-image-template-i-have-already-created-outside-of-devops"></a>Můžu použít existující šablonu obrázku, kterou už jste vytvořili, mimo DevOps?

V současné době není aktuálně k dispozici.

### <a name="can-i-specify-the-image-template-name"></a>Můžu zadat název šablony obrázku?

No. Použije se jedinečný název šablony, který se pak odstraní.

### <a name="the-image-builder-failed-how-can-i-troubleshoot"></a>Tvůrce imagí se nezdařil. Jak můžu řešit potíže?

Pokud dojde k selhání sestavení, úloha DevOps neodstraní pracovní skupinu prostředků. Můžete získat přístup k pracovní skupině prostředků, která obsahuje protokol vlastního nastavení sestavení.

V protokolu DevOps se zobrazí chyba pro úlohu tvůrce imagí virtuálních počítačů a podívejte se na umístění přizpůsobení. log. Příklad:

:::image type="content" source="./media/image-builder-devops-task/devops-task-error.png" alt-text="Ukázková chyba úlohy DevOps, která zobrazuje chybu.":::

Další informace o řešení potíží najdete v tématu [řešení potíží se službou Azure image Builder](image-builder-troubleshoot.md). 

Po prošetření selhání můžete odstranit pracovní skupinu prostředků. Nejprve odstraňte artefakt prostředku šablony imagí. Artefakt má předponu *T_* a najdete ho v protokolu sestavení úlohy DevOps:

```text
...
Source for image:  { type: 'SharedImageVersion',
  imageVersionId: '/subscriptions/<subscriptionID>/resourceGroups/<rgName>/providers/Microsoft.Compute/galleries/<galleryName>/images/<imageDefName>/versions/<imgVersionNumber>' }
...
template name:  t_1556938436xxx
...

```

Artefakt prostředku šablony obrázku je ve skupině prostředků zadané zpočátku v úloze. Po dokončení odstraňování potíží odstraňte artefakt. Při odstraňování pomocí Azure Portal v rámci skupiny prostředků vyberte **Zobrazit skryté typy** a zobrazte artefakt.


## <a name="next-steps"></a>Další kroky

Další informace najdete v tématu [Přehled nástroje Azure image Builder](image-builder-overview.md).