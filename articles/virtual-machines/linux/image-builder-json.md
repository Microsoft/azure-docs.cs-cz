---
title: Vytvořit šablonu Image Builder pro Azure (preview)
description: Zjistěte, jak vytvořit šablonu pro použití s Azure Image Builder.
author: cynthn
ms.author: cynthn
ms.date: 05/10/2019
ms.topic: article
ms.service: virtual-machines-linux
manager: gwallace
ms.openlocfilehash: 16ad2a93c9ff035166a738edba40c99075a6e7ba
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/09/2019
ms.locfileid: "67671457"
---
# <a name="preview-create-an-azure-image-builder-template"></a>Verze Preview: Vytvoření šablony Azure Image Builder 

Image Builder pro Azure používá k předávání informací do služby Image Builder soubor .json. V tomto článku jsme se nevede části souboru json, abyste mohli sestavit vlastní. Příklady úplná .json souborů najdete v tématu [Azure Image Builder Githubu](https://github.com/danielsollondon/azvmimagebuilder/tree/master/quickquickstarts).

To je formát základní šablony:

```json
 { 
    "type": "Microsoft.VirtualMachineImages/imageTemplates", 
    "apiVersion": "2019-05-01-preview", 
    "location": "<region>", 
    "tags": {
        "<name": "<value>",
        "<name>": "<value>"
             }
    "identity":{},           
    "dependsOn": [], 
    "properties": { 
        "buildTimeoutInMinutes": <minutes>, 
        "build": {}, 
        "customize": {}, 
        "distribute": {} 
      } 
 } 
```



## <a name="type-and-api-version"></a>Verze typu a rozhraní API

`type` Je typ prostředku, který musí být `"Microsoft.VirtualMachineImages/imageTemplates"`. `apiVersion` Se mění v čase podle změn rozhraní API, ale musí být `"2019-05-01-preview"` pro verzi preview.

```json
    "type": "Microsoft.VirtualMachineImages/imageTemplates",
    "apiVersion": "2019-05-01-preview",
```

## <a name="location"></a>Location

Umístění je oblast, ve kterém se vytvoří vlastní image. Pro Image Builder ve verzi preview se podporují těchto oblastech:

- East US
- Východní USA 2
- Západní střed USA
- USA – západ
- USA – západ 2


```json
    "location": "<region>",
```
    
## <a name="depends-on-optional"></a>Závisí na (volitelné)

Tento volitelný oddíl je možné zajistit, že před pokračováním jsou dokončeny závislosti. 

```json
    "dependsOn": [],
```

Další informace najdete v tématu [definovat závislosti prostředků](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-define-dependencies#dependson).

## <a name="identity"></a>Identita
Ve výchozím nastavení podporuje Image Builder pomocí skriptů nebo kopírování souborů z více míst, jako jsou GitHub a Azure storage. Pokud chcete použít, musí být veřejně přístupná.

Můžete také Azure User-Assigned spravovaná identita, definované uživatelem, pokud chcete povolit přístup Image Builder pro Azure Storage, tak dlouho, dokud identity bylo uděleno minimálně 'Čtenář dat objektu Blob úložiště' účet Azure storage. To znamená, že nepotřebujete, aby objekty BLOB služby storage zvenku přístupný nebo tokeny SAS instalační program.


```json
    "identity": {
    "type": "UserAssigned",
          "userAssignedIdentities": {
            "<imgBuilderId>": {}
        }
        },
```

Kompletní příklad naleznete v tématu [ pomocí Identity spravované Azure User-Assigned přístup k souborům ve službě Azure Storage](https://github.com/danielsollondon/azvmimagebuilder/tree/master/quickquickstarts/7_Creating_Custom_Image_using_MSI_to_Access_Storage).

Image Builder podporu pro identitu User-Assigned: • podporuje jedinou identitu pouze • nepodporuje vlastní názvy domén

Další informace najdete v tématu [co je spravované identity pro prostředky Azure?](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview).
Další informace o nasazení této funkce najdete v tématu [konfigurace spravovaných identit pro prostředky Azure na Virtuálním počítači Azure pomocí Azure CLI](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm#user-assigned-managed-identity).

## <a name="properties-source"></a>Vlastnosti: zdroj

`source` Část obsahuje informace o zdrojového obrázku, který se použije Image Builder.

Rozhraní API vyžaduje "SourceType", který definuje zdroj pro sestavení image, aktuálně existují tři typy:
- ISO - využit, pokud je zdroj RHEL ISO.
- PlatformImage - označil zdrojové image je Marketplace image.
- ManagedImage – použijte ho, když se od pravidelných spravované image.
- SharedImageVersion – používá se v případě, že se používá verzi image v galerii Imagí Shared jako zdroj.

### <a name="iso-source"></a>Zdroj ISO

Image Builder pro Azure podporuje pouze pomocí publikovaných Red Hat Enterprise Linux 7.x binární DVD soubory ISO, pro verzi preview. Image Builder podporuje:
- RHEL 7.3 
- RHEL 7.4 
- RHEL 7.5 
 
```json
"source": {
       "type": "ISO",
       "sourceURI": "<sourceURI from the download center>",
       "sha256Checksum": "<checksum associated with ISO>"
}
```

Chcete-li získat `sourceURI` a `sha256Checksum` hodnoty, přejděte na `https://access.redhat.com/downloads` vyberte produkt, **Red Hat Enterprise Linux**a s podporovanou verzí. 

V seznamu **instalační programy a bitových kopií pro Red Hat Enterprise Linux Server**, budete muset zkopírovat odkaz pro Red Hat Enterprise Linux 7.x binární DVD a kontrolního součtu.

> [!NOTE]
> Přístupové tokeny odkazy se aktualizují v pravidelných intervalech, takže pokaždé, když chcete odeslat šablonu, musíte zkontrolovat, pokud odkaz RH adresy změnilo.
 
### <a name="platformimage-source"></a>PlatformImage zdroje 
Image Builder pro Azure podporuje následující Image Azure Marketplace:
* Ubuntu 18.04
* Ubuntu 16.04
* RHEL 7.6
* CentOS 7.6
* Windows 2016
* Windows 2019

```json
        "source": {
            "type": "PlatformImage",
                "publisher": "Canonical",
                "offer": "UbuntuServer",
                "sku": "18.04-LTS",
                "version": "18.04.201903060"
        },
```


Vlastnosti Zde jsou stejné, které se používají k vytvoření Virtuálního počítače, pomocí AZ rozhraní příkazového řádku, spusťte níže zobrazíte vlastnosti: 
 
```azurecli-interactive
az vm image list -l westus -f UbuntuServer -p Canonical --output table –-all 
```

> [!NOTE]
> Verze nemůže být 'nejnovější', chcete-li získat číslo verze musí pomocí výše uvedeného příkazu. 

### <a name="managedimage-source"></a>ManagedImage zdroje

Nastaví zdrojového obrazu jako existující spravované image generalizovaného virtuálního pevného disku nebo virtuálního počítače. Spravované image zdroje musí být podporovaný operační systém a být ve stejné oblasti jako šablona Image Builder pro Azure. 

```json
        "source": { 
            "type": "ManagedImage", 
                "imageId": "/subscriptions/<subscriptionId>/resourceGroups/{destinationResourceGroupName}/providers/Microsoft.Compute/images/<imageName>"
        }
```

`imageId` By měl být ResourceId spravované image. Použití `az image list` na seznam dostupných imagí.


### <a name="sharedimageversion-source"></a>SharedImageVersion source
Nastaví obrázek zdroj stávající verze image v galerii Imagí Shared. Verze bitové kopie musí být podporovaný operační systém a image se musí replikovat do stejné oblasti jako šablona Image Builder pro Azure. 

```json
        "source": { 
            "type": "SharedImageVersion", 
            "imageVersionID": "/subscriptions/<subscriptionId>/resourceGroups/<resourceGroup>/p  roviders/Microsoft.Compute/galleries/<sharedImageGalleryName>/images/<imageDefinitionName/versions/<imageVersion>" 
   } 
```

`imageVersionId` By měl být ResourceId verze image. Použití [az sig verze image seznamu](/cli/azure/sig/image-version#az-sig-image-version-list) na seznamu verze image.

## <a name="properties-customize"></a>Vlastnosti: přizpůsobení


Image Builder podporuje více "úpravcům přidávat nové". Jsou úpravcům přidávat nové funkce, které se používají k přizpůsobení image, jako je například spouštění skriptů nebo restartování serverů. 

Při použití `customize`: 
- Můžete použít více úpravcům přidávat nové, ale musí mít jedinečnou `name`.
- Úpravcům přidávat nové spuštění v pořadí, v této šabloně specifikovaný.
- Pokud jeden modifikátor nezdaří, pak komponenta celý přizpůsobení se nezdaří a nahlásit chybu.
- Zvažte, kolik času bitové kopie sestavení vyžadují a upravte vlastnost "buildTimeoutInMinutes" image builder pro vyhradit dostatek času k dokončení.
- Důrazně doporučujeme důkladně testu skript před jeho použitím v šabloně. Ladění skriptů na vlastním virtuálním počítači bude jednodušší.
- Neumisťujte citlivá data ve skriptech. 
- Umístění skriptu musí být veřejně dostupný, pokud nepoužíváte [MSI](https://github.com/danielsollondon/azvmimagebuilder/tree/master/quickquickstarts/7_Creating_Custom_Image_using_MSI_to_Access_Storage).

```json
        "customize": [
            {
                "type": "Shell",
                "name": "<name>",
                "scriptUri": "<path to script>"
            },
            {
                "type": "Shell",
                "name": "<name>",
                "inline": [
                    "<command to run inline>",
                ]
            }

        ],
```     

 
Vlastní části je pole. Image Builder pro Azure se spustí prostřednictvím úpravcům přidávat nové v sekvenčním pořadí. Jakékoli neúspěchy v jakékoli úpravce systému selže proces sestavení. 
 
 
### <a name="shell-customizer"></a>Modifikátor prostředí

Úpravce systému prostředí podporuje spouštění skriptů prostředí, ty musí být veřejně přístupný pro IB pro přístup k nim.

```json
    "customize": [ 
        { 
            "type": "Shell", 
            "name": "<name>", 
            "scriptUri": "<link to script>"        
        }, 
    ], 
        "customize": [ 
        { 
            "type": "Shell", 
            "name": "<name>", 
            "inline": "<commands to run>"
        }, 
    ], 
```

Podpora operačního systému: Linux 
 
Přizpůsobení vlastností:

- **typ** – skořápce 
- **název** – název přizpůsobení sledování 
- **adresa_uri-skriptu** -URI k umístění souboru 
- **vložené** -pole příkazy prostředí, oddělené čárkami.
 
> [!NOTE]
> Při spuštění prostředí úpravce systému RHEL ISO zdrojem, je potřeba zajistit vaši první popisovače prostředí přizpůsobení registraci serveru Red Hat nároku před provedením jakéhokoli přizpůsobení. Po dokončení úprav skript by měl zrušit registraci k serveru nárok.

### <a name="windows-restart-customizer"></a>Restartujte úpravce systému Windows 
Modifikátor restartování umožňuje restartování virtuálního počítače s Windows a počkejte na její návratu do režimu online, umožňuje instalaci softwaru vyžadujícího restart.  

```json 
     "customize": [ 
            "type{ ": "WindowsRestart", 
            "restartCommand": "shutdown /r /f /t 0 /c", 
            "restartCheckCommand": "echo Azure-Image-Builder-Restarted-the-VM  > buildArtifacts/azureImageBuilderRestart.txt",
            "restartTimeout": "5m"
         }],
```

Podpora operačního systému: Windows
 
Přizpůsobení vlastností:
- **Typ**: WindowsRestart
- **restartCommand** -příkaz k provedení restartu (volitelné). Výchozí hodnota je `'shutdown /r /f /t 0 /c \"packer restart\"'`.
- **restartCheckCommand** – příkaz a zkontrolujte, pokud restartování proběhlo úspěšně (volitelné). 
- **restartTimeout** – restartování časový limit zadaný jako řetězec velikosti a jednotku. Například `5m` (5 minut) nebo `2h` (2 hodiny). Výchozí hodnota je: "5 min.


### <a name="powershell-customizer"></a>Modifikátor prostředí PowerShell 
Modifikátor prostředí podporuje spouštění skriptů prostředí PowerShell a příkaz vložené, skripty musí být veřejně přístupný pro IB pro přístup k nim.

```json 
     "customize": [
        { 
             "type": "PowerShell",
             "name":   "<name>",  
             "scriptUri": "<path to script>" 
        },  
        { 
             "type": "PowerShell", 
             "name": "<name>", 
             "inline": "<PowerShell syntax to run>", 
             "valid_exit_codes": "<exit code>" 
         } 
    ], 
```

Podpora operačního systému: Windows a Linux

Přizpůsobení vlastností:

- **typ** – prostředí PowerShell.
- **adresa_uri-skriptu** -URI k umístění souboru skriptu Powershellu. 
- **vložené** – vložené příkazy ke spuštění, oddělené čárkami.
- **valid_exit_codes** – volitelné, příkaz platné kódy, které lze vrátit ze skriptu nebo vložené, tím se vyhnete ohlášené selhání příkazu skriptu/inline.

### <a name="file-customizer"></a>Úpravce systému souborů

Úpravce systému souborů umožňuje image builder pro stažení souboru z Githubu nebo služby Azure storage. Pokud máte kanál sestavení image, která závisí na artefakty sestavení, můžete pak nastavte úpravce systému souborů ke stažení ze sdílené složky sestavení a přesunout artefakty do bitové kopie.  

```json
     "customize": [ 
         { 
            "type": "File", 
             "name": "<name>", 
             "sourceUri": "<source location>",
             "destination": "<destination>" 
         }
     ]
```

Podpora operačního systému: Systémy Linux a Windows 

Vlastnosti úpravce systému souborů:

- **sourceUri** – koncový bod úložiště přístupné, může to být úložiště GitHub nebo v Azure. Můžete stáhnout jenom jeden soubor, nikoli celý adresář. Pokud budete muset stáhnout do adresáře, použijte komprimovaný soubor, pak dekomprimovat pomocí úpravcům přidávat nové prostředí nebo prostředí PowerShell. 
- **určení** – Toto je název cílového úplnou cestu a název souboru. Žádné odkazovanou cestu a jeho podadresářích musí existovat, použít úpravcům přidávat nové prostředí nebo prostředí PowerShell k nastavení těchto předem. Úpravci skript můžete použít k vytvoření cesty. 

To je podporována adresáře Windows a Linuxem cesty, ale existují určité rozdíly: 
- Linux OS – pouze cesta Image builder můžou zapisovat do je tmp.
- Windows – žádná omezení cesty, ale cesta musí existovat.
 
 
Pokud dojde k chybě pokusu o stažení souboru a vložit ho do zadaného adresáře vlastní krok se nezdaří a bude ve customization.log.

>> Poznámka:! Úpravce systému souborů je pouze vhodný pro malý soubor ke stažení, < 20MB. Pro větší stahování souborů pomocí skriptu nebo vložené příkazu, použít kód ke stažení souborů, jako je třeba Linux `wget` nebo `curl`, Windows, `Invoke-WebRequest`.

Soubory v souboru úpravce systému si můžete stáhnout z Azure Storage pomocí [MSI](https://github.com/danielsollondon/azvmimagebuilder/tree/master/quickquickstarts/7_Creating_Custom_Image_using_MSI_to_Access_Storage).

### <a name="generalize"></a>Generalizovat 
Ve výchozím nastavení bude Image Builder pro Azure také spustit kód "zrušení zřízení" na konci každé fáze přizpůsobení image, k zobecnění image. Zobecňuje se je proces, ve kterém na obrázku je nastavený tak můžete znovu použít k vytvoření několika virtuálních počítačů. Image Builder pro Azure pro virtuální počítače s Windows pomocí programu Sysprep. Pro Linux, Azure Image Builder spustí "waagent-zrušení zřízení". 

Příkazy Image Builder pro uživatele k zobecnění nemusí být vhodný pro každé situaci, takže Image Builder pro Azure vám umožní přizpůsobit tento příkaz, v případě potřeby. 

Pokud migrujete existující přizpůsobení a používáte různé příkazy nástroje Sysprep/waagent, můžete pomocí obecných příkazů Image Builder a pokud se nezdaří vytváření virtuálních počítačů použít vlastní nástroje Sysprep nebo waagent příkazy.

Pokud Azure Image Builder úspěšně vytvoří vlastní image Windows a vytvoření virtuálního počítače z, a zjistíte, že vytvoření virtuálního počítače selže nebo úspěšně nedokončí, bude muset najdete v dokumentaci nástroje Sysprep systému Windows Server nebo žádost o podporu se Tým podpory služby Windows Server Sysprep zákazníků, kteří řešení problémů a upozorňují na správné použití programu Sysprep.


#### <a name="default-sysprep-command"></a>Výchozí příkaz Sysprep
```powershell
echo '>>> Waiting for GA to start ...'
while ((Get-Service RdAgent).Status -ne 'Running') { Start-Sleep -s 5 }
while ((Get-Service WindowsAzureTelemetryService).Status -ne 'Running') { Start-Sleep -s 5 }
while ((Get-Service WindowsAzureGuestAgent).Status -ne 'Running') { Start-Sleep -s 5 }
echo '>>> Sysprepping VM ...'
if( Test-Path $Env:SystemRoot\\windows\\system32\\Sysprep\\unattend.xml ){ rm $Env:SystemRoot\\windows\\system32\\Sysprep\\unattend.xml -Force} & $Env:SystemRoot\\System32\\Sysprep\\Sysprep.exe /oobe /generalize /quiet /quit
while($true) { $imageState = Get-ItemProperty HKLM:\\SOFTWARE\\Microsoft\\Windows\\CurrentVersion\\Setup\\State | Select ImageState; if($imageState.ImageState -ne 'IMAGE_STATE_GENERALIZE_RESEAL_TO_OOBE') { Write-Output $imageState.ImageState; Start-Sleep -s 5  } else { break } }
```
#### <a name="default-linux-deprovision-command"></a>Výchozí Linuxový příkaz zrušení zřízení

```bash
/usr/sbin/waagent -force -deprovision+user && export HISTSIZE=0 && sync
```

#### <a name="overriding-the-commands"></a>Přepsání příkazy
K přepsání příkazy, pomocí provisioners skript prostředí PowerShell nebo prostředí můžete vytvořit soubory příkazů s přesný název souboru a umístit je do správného adresáře:

* Windows: c:\DeprovisioningScript.ps1
* Linux: /tmp/DeprovisioningScript.sh

Image Builder bude číst tyto příkazy, ty jsou zapsané do protokolů AIB "customization.log". Zobrazit [řešení potíží s](https://github.com/danielsollondon/azvmimagebuilder/blob/master/troubleshootingaib.md#collecting-and-reviewing-aib-logs) o tom, jak shromažďovat protokoly.
 
## <a name="properties-distribute"></a>Vlastnosti: distribuce

Image Builder pro Azure podporuje tři distribuce cíle: 

- **managedImage** – spravované image.
- **sharedImage** – sdílené Galerie obrázků.
- **Virtuální pevný disk** -VHD v účtu úložiště.

Můžete distribuci image do obou typů cíl ve stejné konfiguraci, podívejte se prosím [příklady](https://github.com/danielsollondon/azvmimagebuilder/blob/7f3d8c01eb3bf960d8b6df20ecd5c244988d13b6/armTemplates/azplatform_image_deploy_sigmdi.json#L80).

Jelikož můžete mít více než jeden cíl pro distribuci, Image Builder udržuje stav pro každý cíl distribuce, který je přístupný pomocí dotazu `runOutputName`.  `runOutputName` Je objekt můžete dát dotaz na příspěvek distribuce pro informace o příslušné distribuci. Například se můžete dotazovat umístění virtuálního pevného disku nebo oblastech, kde se verze image replikují do. To je součástí každého cíle rozdělení. `runOutputName` Musí být jedinečný pro každý cíl distribuce.
 
### <a name="distribute-managedimage"></a>Distribuce: managedImage

Výstup image bude prostředek spravované image.

```json
"distribute": [
        {
"type":"managedImage",
       "imageId": "<resource ID>",
       "location": "<region>",
       "runOutputName": "<name>",
       "artifactTags": {
            "<name": "<value>",
             "<name>": "<value>"
               }
         }]
```
 
Distribuujte vlastnosti:
- **typ** – managedImage 
- **ID obrázku** – ID prostředku cílového obrazu, očekával se formát: /subscriptions/<subscriptionId>/resourceGroups/<destinationResourceGroupName>/providers/Microsoft.Compute/images/<imageName>
- **umístění** – umístění spravované image.  
- **runOutputName** – jedinečný název pro identifikaci distribuce.  
- **artifactTags** -volitelné uživatel zadal značky pár klíč-hodnota.
 
 
> [!NOTE]
> Cílová skupina prostředků musí existovat.
> Pokud chcete, aby bitová kopie distribuována do jiné oblasti, zvýší dobu nasazení. 

### <a name="distribute-sharedimage"></a>Distribuce: sharedImage 
Galerie Imagí Azure Shared je nová služba Správa Imagí, které umožňuje správu replikace oblasti obrázku, Správa verzí a sdílení vlastních imagí. Image Builder pro Azure podporuje distribuce pomocí této služby, můžete k distribuci Image do oblastí nepodporuje sdílené bitové kopie galerie. 
 
Galerie obrázků sdílené se skládá ze: 
 
- Galerie – kontejner pro více sdílené bitové kopie. Galerie bude nasazena v jedné oblasti.
- Definice Image - koncepční seskupení pro bitové kopie. 
- Verze Image – Toto je typ image použité k nasazení virtuálního počítače nebo škálovací sady. Verze Image je možné replikovat do jiných oblastí, kde je potřeba nasadit virtuální počítače.
 
Před distribucí do Galerie Imagí, musíte vytvořit galerie a definici image, najdete v článku [sdílené bitové kopie](shared-images.md). 

```json
{
     "type": "sharedImage",
     "galleryImageId": “<resource ID>”,
     "runOutputName": "<name>",
     "artifactTags": {
          "<name": "<value>",
           "<name>": "<value>"
             }
     "replicationRegions": [
        "<region where the gallery is deployed>",
        "<region>"
    ]}
``` 

Distribuujte vlastnosti Galerie sdílené bitové kopie:

- **typ** -sharedImage  
- **galleryImageId** – ID galerii sdílené bitové kopie. Je ve formátu: /subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.Compute/galleries/<sharedImageGalleryName>/images/<imageGalleryName>.
- **runOutputName** – jedinečný název pro identifikaci distribuce.  
- **artifactTags** -volitelné uživatel zadal značky pár klíč-hodnota.
- **replicationRegions** -pole oblasti pro replikaci. Jednu z oblastí musí být oblast, ve kterém je nasazená v galerii.
 
> [!NOTE]
> Image Builder pro Azure můžete použít v jiné oblasti v galerii, ale Image Builder pro Azure service bude nutné přenosu bitové kopie mezi datová centra a bude to trvat déle. Image Builder bude automaticky verzi image, podle monotónní celé číslo, je aktuálně nelze určit. 

### <a name="distribute-vhd"></a>Distribuujte: VHD  
Zvládne výstup na virtuální pevný disk. Můžete zkopírovat virtuální pevný disk a použít k publikování na webu Azure MarketPlace, nebo pomocí služby Azure Stack.  

```json
 { 
     "type": "VHD",
     "runOutputName": "<VHD name>",
     "tags": {
          "<name": "<value>",
           "<name>": "<value>"
             }
 }
```
 
Podpora operačního systému: Windows a Linux

Distribuce virtuální pevný disk parametry:

- **typ** -virtuálního pevného disku.
- **runOutputName** – jedinečný název pro identifikaci distribuce.  
- **značky** -volitelné uživatel zadal značky pár klíč-hodnota.
 
Image Builder pro Azure neumožňuje uživateli zadat umístění účtu úložiště, ale může dotaz na stav `runOutputs` na umístění.  

```azurecli-interactive
az resource show \
   --ids "/subscriptions/$subscriptionId/resourcegroups/<imageResourceGroup>/providers/Microsoft.VirtualMachineImages/imageTemplates/<imageTemplateName>/runOutputs/<runOutputName>"  | grep artifactUri 
```

> [!NOTE]
> Po vytvoření virtuálního pevného disku, zkopírujte ho do jiného umístění, co nejdříve. Virtuální pevný disk uložený v účtu úložiště ve skupině dočasný prostředek, který vytvoří, když image šablony se odešle do služby Azure Image Builder. Pokud odstraníte image šablony, ztratíte virtuální pevný disk. 
 
## <a name="next-steps"></a>Další postup

Existují ukázkové soubory .json pro různé scénáře v [Azure Image Builder Githubu](https://github.com/danielsollondon/azvmimagebuilder).
 
 
 
 
 
 
 
 
 
 
