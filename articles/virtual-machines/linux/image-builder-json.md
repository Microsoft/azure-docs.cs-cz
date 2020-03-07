---
title: Vytvoření šablony Azure image Builder (Preview)
description: Naučte se, jak vytvořit šablonu pro použití s nástrojem Azure image Builder.
author: danis
ms.author: danis
ms.date: 01/23/2020
ms.topic: article
ms.service: virtual-machines-linux
manager: gwallace
ms.openlocfilehash: 08a1ca0c85d69d1a5262f1dcac5d46fb82b1c22b
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/05/2020
ms.locfileid: "78385099"
---
# <a name="preview-create-an-azure-image-builder-template"></a>Verze Preview: Vytvoření šablony Azure image Builder 

Azure image Builder k předávání informací do služby tvůrce imagí používá soubor. JSON. V tomto článku se přejdou na oddíly souboru JSON, takže si můžete vytvořit vlastní. Příklady úplných souborů. JSON najdete v tématu věnovaném [nástroji Azure image Builder GitHub](https://github.com/danielsollondon/azvmimagebuilder/tree/master/quickquickstarts).

Toto je základní formát šablony:

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
        "vmProfile": 
            {
            "vmSize": "<vmSize>"
            },
        "build": {}, 
        "customize": {}, 
        "distribute": {} 
      } 
 } 
```



## <a name="type-and-api-version"></a>Typ a verze rozhraní API

`type` je typ prostředku, který musí být `"Microsoft.VirtualMachineImages/imageTemplates"`. `apiVersion` se v průběhu času změní rozhraní API, ale měla by být `"2019-05-01-preview"` pro verzi Preview.

```json
    "type": "Microsoft.VirtualMachineImages/imageTemplates",
    "apiVersion": "2019-05-01-preview",
```

## <a name="location"></a>Umístění

Umístění je oblast, kde se vytvoří vlastní image. Pro náhled tvůrce imagí se podporují tyto oblasti:

- USA – východ
- USA – východ 2
- USA – středozápad
- USA – západ
- USA – západ 2


```json
    "location": "<region>",
```
## <a name="vmprofile"></a>vmProfile
Ve výchozím nastavení bude nástroj pro tvorbu obrázků používat virtuální počítač pro sestavení "Standard_D1_v2", můžete ho například přepsat, pokud chcete přizpůsobit image pro virtuální počítač GPU, potřebujete velikost virtuálního počítače GPU. Tato položka je nepovinná.

```json
 {
    "vmSize": "Standard_D1_v2"
 },
```

## <a name="osdisksizegb"></a>osDiskSizeGB

Ve výchozím nastavení nemění tvůrce imagí velikost obrázku, ale bude používat velikost ze zdrojové image. Můžete upravit velikost disku s operačním systémem (Win a Linux), poznamenat si, že nebudete příliš malá, než minimální požadované místo vyžadované operačním systémem. To je volitelné a hodnota 0 znamená, že zůstane stejná velikost jako zdrojová image. Tato položka je nepovinná.

```json
 {
    "osDiskSizeGB": 100
 },
```

## <a name="tags"></a>Značky

Jedná se o páry klíč/hodnota, které můžete zadat pro vygenerovanou bitovou kopii.

## <a name="depends-on-optional"></a>Závisí na (volitelné)

Tento volitelný oddíl lze použít k zajištění, aby byly před pokračováním dokončeny závislosti. 

```json
    "dependsOn": [],
```

Další informace najdete v tématu [Definování závislostí prostředků](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-define-dependencies#dependson).

## <a name="identity"></a>Identita
Ve výchozím nastavení podporuje tvůrce imagí použití skriptů nebo kopírování souborů z více umístění, jako je GitHub a Azure Storage. Aby je bylo možné použít, musí být veřejně přístupné.

Můžete také použít spravovanou identitu přiřazenou uživatelem Azure, kterou jste definovali, a zapnout tak přístup k tvůrci imagí Azure Storage, pokud mu byla v účtu úložiště Azure udělena minimální hodnota "úložiště BLOB data Reader". To znamená, že nemusíte mít externě přístup k objektům blob úložiště nebo nastavit tokeny SAS.


```json
    "identity": {
    "type": "UserAssigned",
          "userAssignedIdentities": {
            "<imgBuilderId>": {}
        }
        },
```

Úplný příklad najdete v tématu [použití spravované identity přiřazené uživatelem Azure pro přístup k souborům v Azure Storage](https://github.com/danielsollondon/azvmimagebuilder/tree/master/quickquickstarts/7_Creating_Custom_Image_using_MSI_to_Access_Storage).

Podpora tvůrce imagí pro uživatelem přiřazenou identitu: • podporuje jenom jednu identitu • nepodporuje vlastní názvy domén.

Další informace najdete v tématu [co jsou spravované identity pro prostředky Azure?](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview).
Další informace o nasazení této funkce najdete v tématu [Konfigurace spravovaných identit pro prostředky Azure na virtuálním počítači Azure pomocí Azure CLI](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm#user-assigned-managed-identity).

## <a name="properties-source"></a>Vlastnosti: zdroj

Část `source` obsahuje informace o zdrojové imagi, kterou bude používat Tvůrce imagí.

Rozhraní API vyžaduje typ SourceType, který definuje zdroj pro sestavení image, v současné době existují tři typy:
- ISO – použijte tuto hodnotu, pokud je zdrojem RHEL ISO.
- PlatformImage – indikuje, že zdrojová Image je image na webu Marketplace.
- ManagedImage – Toto použijte při spuštění z obyčejné spravované image.
- SharedImageVersion – používá se, pokud používáte verzi image v galerii sdílených imagí jako zdroj.

### <a name="iso-source"></a>Zdroj ISO

Azure image Builder podporuje pro náhled jenom použití publikované Red Hat Enterprise Linux 7. x Binary DVD soubory ISO. Nástroj image Builder podporuje:
- RHEL 7.3 
- RHEL 7,4 
- RHEL 7.5 
 
```json
"source": {
       "type": "ISO",
       "sourceURI": "<sourceURI from the download center>",
       "sha256Checksum": "<checksum associated with ISO>"
}
```

Chcete-li získat hodnoty `sourceURI` a `sha256Checksum`, zadejte `https://access.redhat.com/downloads`, vyberte **Red Hat Enterprise Linux**produktu a podporovanou verzi. 

V seznamu **instalačních programů a imagí pro Red Hat Enterprise Linux Server**je nutné zkopírovat odkaz pro binární DVD Red Hat Enterprise Linux 7. x a kontrolní součet.

> [!NOTE]
> Přístupové tokeny těchto propojení se aktualizují v častých intervalech, takže pokaždé, když chcete odeslat šablonu, musíte ověřit, jestli se změnila adresa odkazu RH.
 
### <a name="platformimage-source"></a>PlatformImage zdroj 
Azure image Builder podporuje image Windows serveru a klienta a Azure Marketplace pro Linux. úplný seznam najdete [tady](https://docs.microsoft.com/azure/virtual-machines/windows/image-builder-overview#os-support) . 

```json
        "source": {
            "type": "PlatformImage",
                "publisher": "Canonical",
                "offer": "UbuntuServer",
                "sku": "18.04-LTS",
                "version": "18.04.201903060"
        },
```


Tady jsou vlastnosti, které se používají k vytvoření virtuálního počítače pomocí AZ CLI, pro získání vlastností použijte níže: 
 
```azurecli-interactive
az vm image list -l westus -f UbuntuServer -p Canonical --output table –-all 
```

> [!NOTE]
> Verze nemůže být nejnovější, abyste získali číslo verze, musíte použít výše uvedený příkaz. 

### <a name="managedimage-source"></a>ManagedImage zdroj

Nastaví zdrojovou Image jako existující spravovanou bitovou kopii zobecněného virtuálního pevného disku nebo virtuálního počítače. Zdrojová image spravovaná musí být podporovaného operačního systému a musí být ve stejné oblasti jako šablona Azure image Builder. 

```json
        "source": { 
            "type": "ManagedImage", 
                "imageId": "/subscriptions/<subscriptionId>/resourceGroups/{destinationResourceGroupName}/providers/Microsoft.Compute/images/<imageName>"
        }
```

`imageId` by měl být ResourceId spravované image. K vypsání dostupných imagí použijte `az image list`.


### <a name="sharedimageversion-source"></a>SharedImageVersion zdroj
Nastaví zdrojovou bitovou kopii existující verze image v galerii sdílených imagí. Verze bitové kopie musí být podporovaného operačního systému a bitová kopie musí být replikovaná do stejné oblasti jako šablona Azure image Builder. 

```json
        "source": { 
            "type": "SharedImageVersion", 
            "imageVersionID": "/subscriptions/<subscriptionId>/resourceGroups/<resourceGroup>/p  roviders/Microsoft.Compute/galleries/<sharedImageGalleryName>/images/<imageDefinitionName/versions/<imageVersion>" 
   } 
```

`imageVersionId` by měl být ResourceId verze image. K vypsání verzí imagí použijte příkaz [AZ SIG Image-Version list](/cli/azure/sig/image-version#az-sig-image-version-list) .

## <a name="properties-buildtimeoutinminutes"></a>Vlastnosti: buildTimeoutInMinutes

Ve výchozím nastavení se spustí Tvůrce imagí po dobu 240 minut. Po této instalaci dojde k vypršení časového limitu a zastavení, bez ohledu na to, jestli je sestavení image dokončené. Pokud dojde k vypršení časového limitu, zobrazí se chybová zpráva podobná této:

```text
[ERROR] Failed while waiting for packerizer: Timeout waiting for microservice to
[ERROR] complete: 'context deadline exceeded'
```

Pokud nezadáte hodnotu buildTimeoutInMinutes nebo nastavíte ji na 0, použije se výchozí hodnota. Můžete zvýšit nebo snížit hodnotu až do maximálního počtu 960mins (16hrs). V systému Windows nedoporučujeme toto nastavit níže 60 minut. Pokud zjistíte, že se vám časový limit nelíbí, zkontrolujte [protokoly](https://github.com/danielsollondon/azvmimagebuilder/blob/master/troubleshootingaib.md#collecting-and-reviewing-aib-image-build-logs)a podívejte se, jestli krok přizpůsobení čeká na něco jako uživatelský vstup. 

Pokud zjistíte, že k dokončení úprav potřebujete víc času, nastavte to podle toho, co si myslíte, že potřebujete, a s malým režijním časem. Ale nenastavuje se příliš vysoká, protože možná budete muset počkat na vypršení časového limitu před zobrazením chyby. 


## <a name="properties-customize"></a>Vlastnosti: přizpůsobení

Image Builder podporuje několik "úprav". Vlastníci jsou funkce, které se používají k přizpůsobení image, jako je spouštění skriptů nebo restartování serverů. 

Při použití `customize`: 
- Můžete použít více úprav, ale musí mít jedinečný `name`.
- Úpravci provádějí v pořadí zadaném v šabloně.
- Pokud jeden z úprav selže, celá komponenta přizpůsobení selže a ohlásí chybu.
- Důrazně doporučujeme skript před jeho použitím v šabloně důkladně otestovat. Ladění skriptu na vlastním VIRTUÁLNÍm počítači bude snazší.
- Do skriptů neumísťujte citlivá data. 
- Pokud nepoužíváte [MSI](https://github.com/danielsollondon/azvmimagebuilder/tree/master/quickquickstarts/7_Creating_Custom_Image_using_MSI_to_Access_Storage), musí být umístění skriptu veřejně přístupná.

```json
        "customize": [
            {
                "type": "Shell",
                "name": "<name>",
                "scriptUri": "<path to script>",
                "sha256Checksum": "<sha256 checksum>"
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

 
Oddíl Customization je pole. Azure image Builder se spustí prostřednictvím úprav v sekvenčním pořadí. Jakékoli selhání v jakémkoliv úpravách způsobí selhání procesu sestavení. 
 
 
### <a name="shell-customizer"></a>Přizpůsobení prostředí

Úpravce prostředí podporuje spouštění skriptů prostředí. Tyto skripty musí být veřejně přístupné pro přístup IB a k nim.

```json
    "customize": [ 
        { 
            "type": "Shell", 
            "name": "<name>", 
            "scriptUri": "<link to script>",
            "sha256Checksum": "<sha256 checksum>"       
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

Podpora OS: Linux 
 
Přizpůsobení vlastností:

- **typ** – prostředí 
- **název** – název pro sledování přizpůsobení 
- **scriptUri** -URI do umístění souboru 
- **vložené** – pole příkazů prostředí oddělené čárkami.
- **sha256Checksum** -hodnota kontrolního součtu SHA256 souboru, vygenerujete ho místně a pak tvůrce imagí provede kontrolu kontrolního součtu a ověření.
    * K vygenerování sha256Checksum pomocí terminálu pro Mac/Linux spusťte: `sha256sum <fileName>`


Příkazy, které se mají spustit s oprávněními super uživatele, musí být s předponou `sudo`.

> [!NOTE]
> Když spustíte úpravce prostředí se zdrojem RHEL ISO, musíte zajistit, aby vaše první prostředí pro přizpůsobení způsobilo registraci na serveru s Red Hat nárokem, a to ještě před tím, než dojde k přizpůsobení. Po dokončení přizpůsobení by se měl skript na serveru nároků zrušit.

### <a name="windows-restart-customizer"></a>Restart Windows – úprav 
Úpravce restartování vám umožní restartovat virtuální počítač s Windows a počkat na jeho návrat do režimu online. to vám umožní nainstalovat software, který vyžaduje restart.  

```json 
     "customize": [ 

            {
                "type": "WindowsRestart",
                "restartCommand": "shutdown /r /f /t 0", 
                "restartCheckCommand": "echo Azure-Image-Builder-Restarted-the-VM  > c:\\buildArtifacts\\azureImageBuilderRestart.txt",
                "restartTimeout": "5m"
            }
  
        ],
```

Podpora OS: Windows
 
Přizpůsobení vlastností:
- **Zadejte**: WindowsRestart
- **restartCommand** – příkaz pro spuštění restartování (volitelné). Výchozí formát je `'shutdown /r /f /t 0 /c \"packer restart\"'`.
- **restartCheckCommand** – příkaz pro kontrolu úspěšnosti restartování (volitelné). 
- **rečas_spuštění** – byl zadán časový limit restartování jako řetězec velikosti a jednotky. Například `5m` (5 minut) nebo `2h` (2 hodiny). Výchozí hodnota je: ' 5 min '

### <a name="linux-restart"></a>Restart pro Linux  
Není k dispozici žádný restart pro Linux, ale pokud instalujete ovladače nebo součásti, které vyžadují restart, můžete je nainstalovat a vyvolat restartování pomocí prostředí. k virtuálnímu počítači sestavení je 20min časový limit SSH.

### <a name="powershell-customizer"></a>Úpravám PowerShellu 
Úpravce prostředí podporuje spouštění PowerShellových skriptů a vložených příkazů, skripty musí být veřejně přístupné, aby k nim měly přístup IB.

```json 
     "customize": [
        { 
             "type": "PowerShell",
             "name":   "<name>",  
             "scriptUri": "<path to script>",
             "runElevated": "<true false>",
             "sha256Checksum": "<sha256 checksum>" 
        },  
        { 
             "type": "PowerShell", 
             "name": "<name>", 
             "inline": "<PowerShell syntax to run>", 
             "valid_exit_codes": "<exit code>",
             "runElevated": "<true or false>" 
         } 
    ], 
```

Podpora OS: Windows a Linux

Přizpůsobení vlastností:

- **typ** – PowerShell.
- **scriptUri** -URI do umístění souboru skriptu PowerShellu. 
- **vložené** – vložené příkazy, které mají být spuštěny, oddělené čárkami.
- **valid_exit_codes** – volitelné, platné kódy, které lze vrátit z příkazu Script/inline, tím se vyhnete nahlášené chybě příkazu Script/inline.
- **runElevated** – volitelná, logická hodnota, podpora spouštění příkazů a skriptů se zvýšenými oprávněními.
- **sha256Checksum** -hodnota kontrolního součtu SHA256 souboru, vygenerujete ho místně a pak tvůrce imagí provede kontrolu kontrolního součtu a ověření.
    * Vygenerování sha256Checksum pomocí prostředí PowerShell ve Windows [Get-hash](https://docs.microsoft.com/powershell/module/microsoft.powershell.utility/get-filehash?view=powershell-6)


### <a name="file-customizer"></a>Úprav souborů

Úpravou souboru se dá tvůrce imagí stáhnout soubor z GitHubu nebo Azure Storage. Máte-li kanál sestavení obrázku, který spoléhá na artefakty sestavení, můžete nastavit, aby se soubor úprav souborů stáhl ze sdílené složky sestavení a přesunul artefakty do bitové kopie.  

```json
     "customize": [ 
         { 
            "type": "File", 
             "name": "<name>", 
             "sourceUri": "<source location>",
             "destination": "<destination>",
             "sha256Checksum": "<sha256 checksum>"
         }
     ]
```

Podpora OS: Linux a Windows 

Vlastnosti úprav souborů:

- **SourceUri** – dostupný koncový bod úložiště, může to být GitHub nebo Azure Storage. Můžete stáhnout pouze jeden soubor, nikoli celý adresář. Pokud potřebujete stáhnout adresář, použijte komprimovaný soubor a pak ho dekomprimujte pomocí úprav prostředí nebo úprav prostředí PowerShell. 
- **cíl** – jedná se o úplnou cestu k cíli a název souboru. Musí existovat všechny odkazované cesty a podadresáře, pomocí prostředí PowerShell nebo úprav prostředí PowerShell je nastavit předem. Pomocí úprav skriptů můžete vytvořit cestu. 

To je podporováno v adresářích systému Windows a cestách pro Linux, ale existují několik rozdílů: 
- Operační systém Linux – pouze nástroj pro tvůrce obrázků s cestou může zapisovat do/tmp.
- Windows – žádné omezení cesty, ale cesta musí existovat.
 
 
Pokud při pokusu o stažení souboru nebo jeho umístění do zadaného adresáře dojde k chybě, krok přizpůsobení selže a bude v souboru Customization. log.

> [!NOTE]
> Soubor úprav souborů je vhodný jenom pro stahování malých souborů, < 20MB. U větších souborů ke stažení použijte skript nebo vložený příkaz, ke stažení souborů použijte kód, například `wget` Linux nebo `curl`, Windows `Invoke-WebRequest`.

Soubory v úpravách souborů je možné stáhnout z Azure Storage pomocí [MSI](https://github.com/danielsollondon/azvmimagebuilder/tree/master/quickquickstarts/7_Creating_Custom_Image_using_MSI_to_Access_Storage).

### <a name="generalize"></a>Generalizovat 
Ve výchozím nastavení bude Azure image Builder na konci každé fáze přizpůsobení image taky spouštět kód zrušení zřízení, aby se image generalizoval. Generalizace je proces, ve kterém je image nastavená tak, aby se mohla znovu použít k vytvoření více virtuálních počítačů. Pro virtuální počítače s Windows používá Azure image Builder nástroj Sysprep. Pro Linux spustí Azure image Builder "waagent-disvision". 

Příkazy pro sestavování obrázků uživatelů do generalizace nemusí být vhodné pro každou situaci, takže Azure image Builder vám v případě potřeby umožní upravit tento příkaz. 

Pokud migrujete existující přizpůsobení a používáte jiné příkazy Sysprep/waagent, můžete použít obecné příkazy pro tvůrce imagí, a pokud se vytvoření virtuálního počítače nepovede, použijte vlastní příkazy Sysprep nebo waagent.

Pokud Azure image Builder úspěšně vytvoří vlastní image Windows a z ní vytvoříte virtuální počítač, zjistíte, že vytvoření virtuálního počítače selže nebo že se neúspěšně nedokončí, budete si muset projít dokumentaci k nástroji Sysprep pro Windows Server nebo vyvolat žádost o podporu. Tým podpory Windows serveru Sysprep pro zákaznické služby, který může řešit problémy a radit se správným využitím nástroje Sysprep.


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
#### <a name="default-linux-deprovision-command"></a>Výchozí příkaz pro zrušení zřízení pro Linux

```bash
/usr/sbin/waagent -force -deprovision+user && export HISTSIZE=0 && sync
```

#### <a name="overriding-the-commands"></a>Přepsání příkazů
Pokud chcete příkazy přepsat, použijte modul pro vytváření skriptů PowerShellu nebo prostředí k vytvoření souborů příkazů s přesným názvem souboru a vložte je do správných adresářů:

* Windows: c:\DeprovisioningScript.ps1
* Linux:/tmp/DeprovisioningScript.sh

Nástroj image Builder tyto příkazy přečte a zapíše se do protokolů AIB, "Customize. log". Podívejte se na téma [Poradce při potížích](https://github.com/danielsollondon/azvmimagebuilder/blob/master/troubleshootingaib.md#collecting-and-reviewing-aib-logs) s postupem shromažďování protokolů.
 
## <a name="properties-distribute"></a>Vlastnosti: distribuce

Azure image Builder podporuje tři cíle distribuce: 

- Image spravovaná **managedImage**
- Galerie sdílených imagí **sharedImage**
- **VHD – VHD** v účtu úložiště

Můžete distribuovat obrázek do obou cílových typů ve stejné konfiguraci, viz [Příklady](https://github.com/danielsollondon/azvmimagebuilder/blob/7f3d8c01eb3bf960d8b6df20ecd5c244988d13b6/armTemplates/azplatform_image_deploy_sigmdi.json#L80).

Vzhledem k tomu, že můžete mít více než jeden cíl pro distribuci do nástroje, nástroj image Builder udržuje stav pro každý cíl distribuce, ke kterému se dá dostat dotazování `runOutputName`.  `runOutputName` je objekt, který můžete zadat dotazem na distribuci příspěvku, kde najdete informace o této distribuci. Můžete například zadat dotaz na umístění virtuálního pevného disku nebo oblasti, ve kterých byla verze bitové kopie replikována, nebo vytvořená verze image SIG. Toto je vlastnost všech cílů distribuce. `runOutputName` musí být pro každý cíl distribuce jedinečný. Tady je příklad, který se dotazuje na distribuci Galerie sdílených imagí:

```bash
subscriptionID=<subcriptionID>
imageResourceGroup=<resourceGroup of image template>
runOutputName=<runOutputName>

az resource show \
        --ids "/subscriptions/$subscriptionID/resourcegroups/$imageResourceGroup/providers/Microsoft.VirtualMachineImages/imageTemplates/ImageTemplateLinuxRHEL77/runOutputs/$runOutputName"  \
        --api-version=2019-05-01-preview
```

Výstup:
```json
{
  "id": "/subscriptions/xxxxxx/resourcegroups/rheltest/providers/Microsoft.VirtualMachineImages/imageTemplates/ImageTemplateLinuxRHEL77/runOutputs/rhel77",
  "identity": null,
  "kind": null,
  "location": null,
  "managedBy": null,
  "name": "rhel77",
  "plan": null,
  "properties": {
    "artifactId": "/subscriptions/xxxxxx/resourceGroups/aibDevOpsImg/providers/Microsoft.Compute/galleries/devOpsSIG/images/rhel/versions/0.24105.52755",
    "provisioningState": "Succeeded"
  },
  "resourceGroup": "rheltest",
  "sku": null,
  "tags": null,
  "type": "Microsoft.VirtualMachineImages/imageTemplates/runOutputs"
}
```

### <a name="distribute-managedimage"></a>Distribuovat: managedImage

Výstupem obrázku bude prostředek spravované image.

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
 
Vlastnosti distribuce:
- **typ** – managedImage 
- **imageId** – ID prostředku cílového obrázku, očekávaný formát:/subscriptions/\<SubscriptionId >/ResourceGroups/\<destinationResourceGroupName >/Providers/Microsoft.COMPUTE/images/\<ImageName >
- **umístění** – umístění spravované image.  
- **runOutputName** – jedinečný název pro identifikaci distribuce.  
- **artifactTags** – volitelné uživatelsky definované páry klíč-hodnota.
 
 
> [!NOTE]
> Cílová skupina prostředků musí existovat.
> Pokud chcete bitovou kopii distribuovat do jiné oblasti, prodlouží se doba nasazení. 

### <a name="distribute-sharedimage"></a>Distribuovat: sharedImage 
Galerie sdílených imagí Azure je nová služba pro správu imagí, která umožňuje správu replikace oblasti imagí, správy verzí a sdílení vlastních imagí. Azure image Builder podporuje distribuci s touto službou, takže můžete distribuovat image do oblastí podporovaných galeriemi sdílených imagí. 
 
Galerie sdílených imagí se skládá z těchto součástí: 
 
- Galerie – kontejner pro více sdílených imagí. Galerie je nasazená v jedné oblasti.
- Definice obrázků – koncepční seskupení pro obrázky. 
- Verze image – jedná se o typ image, který se používá k nasazení virtuálního počítače nebo sady škálování. Verze bitové kopie se dají replikovat do jiných oblastí, kde je potřeba nasadit virtuální počítače.
 
Než budete moct distribuovat do galerie imagí, musíte vytvořit galerii a definici image, viz [sdílené image](shared-images.md). 

```json
{
    "type": "sharedImage",
    "galleryImageId": "<resource ID>",
    "runOutputName": "<name>",
    "artifactTags": {
        "<name>": "<value>",
        "<name>": "<value>"
    },
    "replicationRegions": [
        "<region where the gallery is deployed>",
        "<region>"
    ]
}
``` 

Distribuovat vlastnosti pro galerie sdílených imagí:

- **typ** – sharedImage  
- **galleryImageId** – ID Galerie sdílených imagí Formát je:/subscriptions/\<subscriptionId >/resourceGroups/\<resourceGroupName >/providers/Microsoft.Compute/galleries/\<sharedImageGalleryName >/images/\<imageGalleryName >.
- **runOutputName** – jedinečný název pro identifikaci distribuce.  
- **artifactTags** – volitelné uživatelsky definované páry klíč-hodnota.
- **replicationRegions** – pole oblastí pro replikaci. Jedna z oblastí musí být oblast, ve které je galerie nasazena.
 
> [!NOTE]
> Pro galerii můžete použít Azure image Builder v jiné oblasti, ale služba Azure image Builder bude potřebovat přenést image mezi datacentry a to bude trvat déle. Nástroj image Builder automaticky nastaví verzi obrázku na základě monotónní celého čísla, nemůžete ho aktuálně zadat. 

### <a name="distribute-vhd"></a>Distribuovat: VHD  
Můžete vytvořit výstup do virtuálního pevného disku. Pak můžete zkopírovat VHD a použít ho k publikování na webu Azure MarketPlace nebo použít s Azure Stack.  

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
 
Podpora OS: Windows a Linux

Distribuovat parametry VHD:

- **Zadejte** -VHD.
- **runOutputName** – jedinečný název pro identifikaci distribuce.  
- **značky** – volitelné uživatelsky definované páry klíč-hodnota.
 
Azure image Builder neumožňuje uživateli zadat umístění účtu úložiště, můžete ale zadat dotaz na stav `runOutputs` a získat tak umístění.  

```azurecli-interactive
az resource show \
   --ids "/subscriptions/$subscriptionId/resourcegroups/<imageResourceGroup>/providers/Microsoft.VirtualMachineImages/imageTemplates/<imageTemplateName>/runOutputs/<runOutputName>"  | grep artifactUri 
```

> [!NOTE]
> Až se virtuální pevný disk vytvoří, zkopírujte ho do jiného umístění, co nejrychleji. Virtuální pevný disk je uložený v účtu úložiště v dočasné skupině prostředků vytvořené při odeslání šablony image do služby Azure image Builder. Pokud odstraníte šablonu image, ztratíte tím virtuální pevný disk. 
 
## <a name="next-steps"></a>Další kroky

V [GitHubu pro Azure image Builder](https://github.com/danielsollondon/azvmimagebuilder)jsou k dispozici ukázkové soubory. JSON pro různé scénáře.
 
