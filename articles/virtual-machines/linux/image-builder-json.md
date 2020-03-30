---
title: Vytvoření šablony Azure Image Builder (preview)
description: Přečtěte si, jak vytvořit šablonu, která se bude používat s Azure Image Builder.
author: danis
ms.author: danis
ms.date: 03/24/2020
ms.topic: article
ms.service: virtual-machines-linux
ms.subservice: imaging
manager: gwallace
ms.openlocfilehash: e1f1bc09406c34836c13deb805fa399ab4751d41
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80246785"
---
# <a name="preview-create-an-azure-image-builder-template"></a>Náhled: Vytvoření šablony Azure Image Builder 

Azure Image Builder používá soubor JSON k předání informací do služby Image Builder. V tomto článku půjdeme přes části souboru json, takže si můžete vytvořit svůj vlastní. Příklady úplných souborů JSON najdete v [tématu GitHub Azure Image Builder](https://github.com/danielsollondon/azvmimagebuilder/tree/master/quickquickstarts).

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
            "vmSize": "<vmSize>",
            "osDiskSizeGB": <sizeInGB>,
            "vnetConfig": {
                "name": "<vnetName>",
                "subnetName": "<subnetName>",
                "resourceGroupName": "<vnetRgName>"
            },
        "source": {}, 
        "customize": {}, 
        "distribute": {} 
      } 
 } 
```



## <a name="type-and-api-version"></a>Typ a verze rozhraní API

Jedná `type` se o typ prostředku, který musí být `"Microsoft.VirtualMachineImages/imageTemplates"`. Bude `apiVersion` měnit v průběhu času jako změny `"2019-05-01-preview"` rozhraní API, ale by měla být pro náhled.

```json
    "type": "Microsoft.VirtualMachineImages/imageTemplates",
    "apiVersion": "2019-05-01-preview",
```

## <a name="location"></a>Umístění

Umístění je oblast, kde bude vytvořena vlastní image. Pro náhled Tvůrce obrázků jsou podporovány následující oblasti:

- USA – východ
- USA – východ 2
- USA – středozápad
- USA – západ
- USA – západ 2
- Severní Evropa
- Západní Evropa


```json
    "location": "<region>",
```
## <a name="vmprofile"></a>vmProfil
Ve výchozím nastavení Image Builder bude používat "Standard_D1_v2" sestavení virtuálního počítače, můžete přepsat to, například pokud chcete přizpůsobit image pro virtuální virtuální počítače GPU, budete potřebovat velikost virtuálního počítače GPU. Tato položka je nepovinná.

```json
 {
    "vmSize": "Standard_D1_v2"
 },
```

## <a name="osdisksizegb"></a>osDiskSizeGB

Ve výchozím nastavení Image Builder nezmění velikost obrázku, bude používat velikost ze zdrojového obrazu. Můžete zvětšit velikost disku os (Win a Linux), to je volitelné a hodnota 0 znamená ponechat stejnou velikost jako zdrojový obraz. 

```json
 {
    "osDiskSizeGB": 100
 },
```

## <a name="vnetconfig"></a>vnetConfig
Pokud nezadáte žádné vlastnosti virtuální sítě, pak Image Builder vytvoří vlastní virtuální síť, veřejnou IP síť a nsg. Veřejná IP adresa se používá pro službu ke komunikaci s virtuálním počítačem sestavení, ale pokud nechcete, aby veřejná IP adresa nebo chcete, aby image builder měl přístup k vašim stávajícím prostředkům virtuální sítě, jako jsou konfigurační servery (DSC, Chef, Puppet, Ansible), sdílené složky atd. , pak můžete zadat virtuální síť. Další informace naleznete v [dokumentaci k síti](https://github.com/danielsollondon/azvmimagebuilder/blob/master/aibNetworking.md#networking-with-azure-vm-image-builder), toto je volitelné.

```json
    "vnetConfig": {
        "name": "<vnetName>",
        "subnetName": "<subnetName>",
        "resourceGroupName": "<vnetRgName>"
    }
```
## <a name="tags"></a>Značky

Jedná se o páry klíč/hodnota, které můžete zadat pro obrázek, který je generován.

## <a name="depends-on-optional"></a>Závisí na (volitelné)

Tento volitelný oddíl lze zajistit, že závislosti jsou dokončeny před pokračováním. 

```json
    "dependsOn": [],
```

Další informace naleznete v tématu [Definování závislostí prostředků](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-define-dependencies#dependson).

## <a name="identity"></a>Identita
Ve výchozím nastavení image Builder podporuje používání skriptů nebo kopírování souborů z více umístění, jako je GitHub a úložiště Azure. Aby je bylo možné používat, musí být veřejně přístupné.

Můžete také použít azure uživatelem přiřazenou spravovanou identitu, definovanou vámi, abyste povolili přístup k image Builder u Azure Storage, pokud identita byla udělena minimálně "Čtečka dat objektů blob úložiště" na účtu úložiště Azure. To znamená, že není nutné, aby objekty BLOB úložiště externě přístupné nebo nastavení Tokeny SAS.


```json
    "identity": {
    "type": "UserAssigned",
          "userAssignedIdentities": {
            "<imgBuilderId>": {}
        }
        },
```

Úplný příklad najdete [v tématu Použití spravované identity přiřazené k uživateli Azure pro přístup k souborům ve službě Azure Storage](https://github.com/danielsollondon/azvmimagebuilder/tree/master/quickquickstarts/7_Creating_Custom_Image_using_MSI_to_Access_Storage).

Podpora tvůrce obrázků pro identitu přiřazenou uživateli: • Podporuje pouze jednu identitu • Nepodporuje vlastní názvy domén

Další informace najdete v tématu [Co je spravované identity pro prostředky Azure?](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview).
Další informace o nasazení této funkce najdete [v tématu Konfigurace spravovaných identit pro prostředky Azure na virtuálním počítači Azure pomocí rozhraní příkazového příkazu Kontu Azure](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm#user-assigned-managed-identity).

## <a name="properties-source"></a>Vlastnosti: zdroj

Část `source` obsahuje informace o zdrojovém obrázku, který bude použit tvůrcem obrázků.

Rozhraní API vyžaduje SourceType, který definuje zdroj pro sestavení bitové kopie, v současné době existují tři typy:
- PlatformImage - uvedeno, že zdrojový obrázek je obrázek Marketplace.
- ManagedImage - použijte to při spuštění z běžné spravované image.
- SharedImageVersion - to se používá, když používáte verzi obrázku v galerii sdílených obrázků jako zdroj.

### <a name="iso-source"></a>Zdroj ISO
Jsme zavržení tuto funkci od image builder, jak tam jsou nyní [RHEL Přineste si vlastní předplatné obrázky](https://docs.microsoft.com/azure/virtual-machines/workloads/redhat/byos), přečtěte si časové osy níže:
    * 31. března 2020 – Šablony obrázků se zdroji ISO RHEL budou nyní již poskytovatelem prostředků akceptovány.
    * 30. dubna 2020 – Šablony obrázků, které obsahují zdroje ISO RHEL, již nebudou zpracovány.

### <a name="platformimage-source"></a>Zdroj Image platformy 
Azure Image Builder podporuje Windows Server a klienta a image Linux Azure Marketplace, [tady najdete úplný](https://docs.microsoft.com/azure/virtual-machines/windows/image-builder-overview#os-support) seznam. 

```json
        "source": {
            "type": "PlatformImage",
                "publisher": "Canonical",
                "offer": "UbuntuServer",
                "sku": "18.04-LTS",
                "version": "latest"
        },
```


Vlastnosti zde jsou stejné, které se používají k vytvoření virtuálních zařízení, pomocí Rozhraní příkazového příkazu AZ, spusťte níže získat vlastnosti: 
 
```azurecli-interactive
az vm image list -l westus -f UbuntuServer -p Canonical --output table –-all 
```

Můžete použít 'nejnovější' ve verzi, verze je vyhodnocena, když image sestavení probíhá, ne při odeslání šablony. Pokud tuto funkci použijete s cílem Galerie sdílených obrázků, můžete se vyhnout opětovnému odeslání šablony a znovu spustit sestavení obrázku v intervalech, aby byly vaše obrázky znovu vytvořeny z nejnovějších obrázků.

### <a name="managedimage-source"></a>Zdroj ManagedImage

Nastaví zdrojovou bitovou kopii jako existující spravovanou bitovou kopii generalizovaného virtuálního pevného disku nebo virtuálního virtuálního mísy. Zdrojová spravovaná bitová kopie musí být podporovaného operačního systému a musí být ve stejné oblasti jako vaše šablona Azure Image Builder. 

```json
        "source": { 
            "type": "ManagedImage", 
                "imageId": "/subscriptions/<subscriptionId>/resourceGroups/{destinationResourceGroupName}/providers/Microsoft.Compute/images/<imageName>"
        }
```

By `imageId` měl být ResourceId spravované bitové kopie. Slouží `az image list` k zobrazení seznamu dostupných obrázků.


### <a name="sharedimageversion-source"></a>Zdroj SharedImageVersion
Nastaví zdrojový obraz existující verzi obrazu v Galerii sdílených obrázků. Verze image musí být podporovaného operačního systému a image musí být replikována do stejné oblasti jako šablona Azure Image Builder. 

```json
        "source": { 
            "type": "SharedImageVersion", 
            "imageVersionID": "/subscriptions/<subscriptionId>/resourceGroups/<resourceGroup>/p  roviders/Microsoft.Compute/galleries/<sharedImageGalleryName>/images/<imageDefinitionName/versions/<imageVersion>" 
   } 
```

By `imageVersionId` měl být ResourceId verze bitové kopie. Použijte [az sig image-version seznam](/cli/azure/sig/image-version#az-sig-image-version-list) verzí obrázků.

## <a name="properties-buildtimeoutinminutes"></a>Vlastnosti: buildTimeoutInMinutes

Ve výchozím nastavení bude Tvůrce obrázků spuštěn po dobu 240 minut. Za to, že bude časový rámec a zastavit, zda je či není sestavení bitové kopie je dokončena. Pokud časový čas je hit, zobrazí se chyba podobná této:

```text
[ERROR] Failed while waiting for packerizer: Timeout waiting for microservice to
[ERROR] complete: 'context deadline exceeded'
```

Pokud nezadáte hodnotu buildTimeoutInMinutes nebo ji nenastavíte na hodnotu 0, použije se výchozí hodnota. Můžete zvýšit nebo snížit hodnotu až na maximálně 960mins (16hrs). Pro Windows nedoporučujeme nastavení pod 60 minut. Pokud zjistíte, že jste dosáhli časového očase, zkontrolujte [protokoly](https://github.com/danielsollondon/azvmimagebuilder/blob/master/troubleshootingaib.md#collecting-and-reviewing-aib-image-build-logs), abyste zjistili, zda krok přizpůsobení čeká na něco jako vstup uživatele. 

Pokud zjistíte, že potřebujete více času na dokončení vlastního nastavení, nastavte to na to, co si myslíte, že potřebujete, s malou režií. Ale nenastavovat příliš vysoko, protože budete muset počkat na časový čas před zobrazením chyby. 


## <a name="properties-customize"></a>Vlastnosti: přizpůsobení

Image Builder podporuje více 'personalizátory'. Úpravci jsou funkce, které se používají k přizpůsobení bitové kopie, jako je například spuštění skriptů nebo restartování serverů. 

Při `customize`použití : 
- Můžete použít více úpravců, ale musí `name`mít jedinečný .
- Úpravci se spouštějí v pořadí určeném v šabloně.
- Pokud jeden úpravce selže, celá součást přizpůsobení se nezdaří a oznamovat chybu.
- Důrazně doporučujeme skript důkladně otestovat před použitím v šabloně. Ladění skriptu na vlastním virtuálním počítači bude jednodušší.
- Nevpědejte citlivá data do skriptů. 
- Umístění skriptů musí být veřejně přístupná, pokud nepoužíváte [MSI](https://github.com/danielsollondon/azvmimagebuilder/tree/master/quickquickstarts/7_Creating_Custom_Image_using_MSI_to_Access_Storage).

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

 
Oddíl přizpůsobení je pole. Azure Image Builder bude procházet úpravéry v sekvenčním pořadí. Jakékoli selhání v libovolném úpravci nezdaří proces sestavení. 
 
 
### <a name="shell-customizer"></a>Úpravce prostředí

Úpravce prostředí podporuje spouštění skriptů prostředí, musí být veřejně přístupné pro IB, aby k nim měl přístup.

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

Podpora operačního systému: Linux 
 
Přizpůsobení vlastností:

- **typ** – Skořepina 
- **název** - název pro sledování přizpůsobení 
- **scriptUri** - URI do umístění souboru 
- **inline** - pole příkazů prostředí, oddělených čárkami.
- **sha256Checksum** - Hodnota sha256 kontrolní součet souboru, můžete generovat místně, a pak Image Builder bude kontrolní součet a ověřit.
    * Chcete-li generovat sha256Checksum, pomocí terminálu na Mac / Linux spustit:`sha256sum <fileName>`


Aby příkazy běžely s oprávněními super uživatele, `sudo`musí být předponou .

> [!NOTE]
> Při spuštění úpravce prostředí se zdrojem ISO RHEL je třeba zajistit, aby se vaše první prostředí přizpůsobení zaregistrovalo na serveru nároků Red Hat dříve, než dojde k jakémukoli přizpůsobení. Po dokončení vlastního nastavení by se měl skript zrušit registraci u serveru nároků.

### <a name="windows-restart-customizer"></a>Úpravce restartování systému Windows 
Restartcustomizer umožňuje restartovat virtuální počítač se systémem Windows a čekat na to vrátit online, to vám umožní nainstalovat software, který vyžaduje restart.  

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

Podpora operačního systému: Windows
 
Přizpůsobení vlastností:
- **Typ**: WindowsRestart
- **restartCommand** - Příkaz pro spuštění restartu (volitelné). Výchozí formát je `'shutdown /r /f /t 0 /c \"packer restart\"'`.
- **restartCheckCommand** – Příkaz pro kontrolu, zda byl restart úspěšný (volitelné). 
- **restartTimeout** - Časový čas restartování zadaný jako řetězec velikosti a jednotky. Například `5m` (5 minut) `2h` nebo (2 hodiny). Výchozí hodnota je: '5m'

### <a name="linux-restart"></a>Linux restart  
Neexistuje žádný úpravce restartu Linuxu, nicméně, pokud instalujete ovladače nebo součásti, které vyžadují restartování, můžete je nainstalovat a vyvolat restartování pomocí úpravce prostředí, je časový čas 20min SSH pro virtuální počítač sestavení.

### <a name="powershell-customizer"></a>Úpravce prostředí PowerShell 
Úpravce prostředí podporuje spouštění skriptů prostředí PowerShell a vestavěného příkazu, skripty musí být veřejně přístupné pro IB, aby k nim měl přístup.

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
             "validExitCodes": "<exit code>",
             "runElevated": "<true or false>" 
         } 
    ], 
```

Podpora operačního systému: Windows a Linux

Přizpůsobení vlastností:

- **typ** – PowerShell.
- **scriptUri** - URI do umístění souboru skriptu prostředí PowerShell. 
- **inline** – inline příkazy, které mají být spuštěny, oddělené čárkami.
- **validExitCodes** – Volitelné, platné kódy, které mohou být vráceny z příkazu skript/inline, tím se zabrání hlášené chybě skriptu/inline příkazu.
- **runElevated** – Volitelné, logické, podpora pro spouštění příkazů a skriptů se zvýšenými oprávněními.
- **sha256Checksum** - Hodnota sha256 kontrolní součet souboru, můžete generovat místně, a pak Image Builder bude kontrolní součet a ověřit.
    * Chcete-li generovat sha256Checksum, pomocí prostředí PowerShell v systému Windows [Get-Hash](https://docs.microsoft.com/powershell/module/microsoft.powershell.utility/get-filehash?view=powershell-6)


### <a name="file-customizer"></a>Úpravce souborů

Úpravce souborů umožňuje tvůrce bitových obrázků stáhnout soubor z úložiště GitHub nebo Azure. Pokud máte kanál sestavení image, který závisí na artefakty sestavení, pak můžete nastavit úpravce souboru ke stažení ze sdílené složky sestavení a přesunout artefakty do bitové kopie.  

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

Podpora operačního systému: Linux a Windows 

Vlastnosti úpravce souborů:

- **sourceUri** - koncový bod s přístupným úložištěm, může to být GitHub nebo úložiště Azure. Můžete stáhnout pouze jeden soubor, ne celý adresář. Pokud potřebujete stáhnout adresář, použijte komprimovaný soubor a pak ho rozbalte pomocí úpravců prostředí nebo prostředí PowerShell. 
- **cíl** – toto je úplná cílová cesta a název souboru. Všechny odkazované cesty a podadresáře musí existovat, použijte shell nebo PowerShell úpravče nastavit tyto předem. Cestu můžete vytvořit pomocí úpravců skriptů. 

To je podporováno adresáři systému Windows a cestami linuxu, ale existují určité rozdíly: 
- Linux OS je - jediná cesta Image builder může psát, je / tmp.
- Windows – žádné omezení cesty, ale cesta musí existovat.
 
 
Pokud dojde k chybě při pokusu o stažení souboru nebo jeho vložte do zadaného adresáře, krok přizpůsobení se nezdaří, a to bude v souboru customization.log.

> [!NOTE]
> Úpravce souborů je vhodný pouze pro malé stahování souborů, < 20MB. U větších souborů ke stažení použijte skript nebo příkaz vřádku, `wget` použijte `curl`kód `Invoke-WebRequest`ke stažení souborů, například Linux nebo , Windows , .

Soubory v úpravci souborů lze stáhnout z Azure Storage pomocí [MSI](https://github.com/danielsollondon/azvmimagebuilder/tree/master/quickquickstarts/7_Creating_Custom_Image_using_MSI_to_Access_Storage).

### <a name="windows-update-customizer"></a>Úprava služby Windows Update
Tento úpravce je postaven na [komunitě Windows Update Provisioner](https://packer.io/docs/provisioners/community-supported.html) pro Packer, což je open source projekt spravovaný komunitou Packer. Společnost Microsoft testuje a ověřuje rozpis pomocí služby Image Builder a bude podporovat zkoumání problémů s ní a pracovat na řešení problémů, ale projekt s otevřeným zdrojovým kódem není společností Microsoft oficiálně podporován. Podrobné informace o a pomoc s Windows Update Provisioner naleznete v úložišti projektu.
 
     "customize": [
            {
                "type": "WindowsUpdate",
                "searchCriteria": "IsInstalled=0",
                "filters": [
                    "exclude:$_.Title -like '*Preview*'",
                    "include:$true"
                            ],
                "updateLimit": 20
            }
               ], 
Podpora operačního systému: Windows

Přizpůsobení vlastností:
- **typ** – WindowsUpdate.
- **searchCriteria** - Volitelné, definuje, který typ aktualizací jsou nainstalovány (Doporučeno, Důležité atd.), BrowseOnly = 0 a IsInstalled = 0 (Doporučeno) je výchozí.
- **filtry** – Volitelné, umožňuje zadat filtr zahrnout nebo vyloučit aktualizace.
- **updateLimit** – Volitelné, definuje, kolik aktualizací lze nainstalovat, výchozí 1000.
 
 

### <a name="generalize"></a>Generalizovat 
Ve výchozím nastavení Azure Image Builder také spustí 'deprovision' kód na konci každé fáze přizpůsobení image , aby "generalizovat" image. Generalizace je proces, kde je obraz nastaven, takže ji můžete znovu použít k vytvoření více virtuálních počítačů. Azure Image Builder používá program Sysprep pro virtuální počítače s Windows. Pro Linux Azure Image Builder spouští 'waagent -deprovision'. 

Příkazy Image Builder uživatelé generalizovat nemusí být vhodné pro každou situaci, takže Azure Image Builder vám umožní přizpůsobit tento příkaz, v případě potřeby. 

Pokud migrujete existující vlastní nastavení a používáte různé příkazy Sysprep/waagent, můžete použít obecné příkazy Image Builder a pokud se vytvoření virtuálního počítače nezdaří, použijte vlastní příkazy Sysprep nebo waagent.

Pokud Azure Image Builder úspěšně vytvoří vlastní bitovou kopii Windows a vytvoříte z ní virtuální počítač, pak zjistíte, že se vytvoření virtuálního počítače nezdaří nebo se nedokončí úspěšně, budete muset zkontrolovat dokumentaci k programu Windows Server Sysprep nebo vyvolat žádost o podporu pomocí Tým podpory zákaznické ho servisu Windows Server Sysprep, který může řešit potíže se správným použitím programu Sysprep a poradit mu.


#### <a name="default-sysprep-command"></a>Výchozí příkaz sysprep
```powershell
echo '>>> Waiting for GA to start ...'
while ((Get-Service RdAgent).Status -ne 'Running') { Start-Sleep -s 5 }
while ((Get-Service WindowsAzureTelemetryService).Status -ne 'Running') { Start-Sleep -s 5 }
while ((Get-Service WindowsAzureGuestAgent).Status -ne 'Running') { Start-Sleep -s 5 }
echo '>>> Sysprepping VM ...'
if( Test-Path $Env:SystemRoot\\windows\\system32\\Sysprep\\unattend.xml ){ rm $Env:SystemRoot\\windows\\system32\\Sysprep\\unattend.xml -Force} & $Env:SystemRoot\\System32\\Sysprep\\Sysprep.exe /oobe /generalize /quiet /quit
while($true) { $imageState = Get-ItemProperty HKLM:\\SOFTWARE\\Microsoft\\Windows\\CurrentVersion\\Setup\\State | Select ImageState; if($imageState.ImageState -ne 'IMAGE_STATE_GENERALIZE_RESEAL_TO_OOBE') { Write-Output $imageState.ImageState; Start-Sleep -s 5  } else { break } }
```
#### <a name="default-linux-deprovision-command"></a>Výchozí příkaz zrušení zřízení Linuxu

```bash
/usr/sbin/waagent -force -deprovision+user && export HISTSIZE=0 && sync
```

#### <a name="overriding-the-commands"></a>Přepsání příkazů
Chcete-li příkazy přepsat, vytvořte soubory příkazů s přesným názvem a vložte je do správných adresářů pomocí zřazených skriptů prostředí PowerShell nebo Shell a vložte je do správných adresářů:

* Windows: c:\DeprovisioningScript.ps1
* Linux: /tmp/DeprovisioningScript.sh

Image Builder bude číst tyto příkazy, ty jsou zapsány do protokolů AIB, 'customization.log'. Viz [řešení potíží se](https://github.com/danielsollondon/azvmimagebuilder/blob/master/troubleshootingaib.md#collecting-and-reviewing-aib-logs) shromažďováním protokolů.
 
## <a name="properties-distribute"></a>Vlastnosti: distribuovat

Azure Image Builder podporuje tři distribuční cíle: 

- **managedImage** - spravovaný obraz.
- **sharedImage** - Galerie sdílených obrázků.
- **VHD** - VHD v účtu úložiště.

Bitovou kopii můžete distribuovat do obou cílových typů ve stejné konfiguraci, viz [příklady](https://github.com/danielsollondon/azvmimagebuilder/blob/7f3d8c01eb3bf960d8b6df20ecd5c244988d13b6/armTemplates/azplatform_image_deploy_sigmdi.json#L80).

Vzhledem k tomu, že můžete mít více než jeden cíl distribuovat, Image Builder udržuje `runOutputName`stav pro každý cíl distribuce, které lze přistupovat dotazem .  Jedná `runOutputName` se o objekt, který můžete dotazovat na distribuci pošty pro informace o této distribuci. Můžete například zadat dotaz na umístění virtuálního pevného disku nebo oblastí, do kterých byla verze bitové kopie replikována, nebo vytvořit verzi bitové kopie SIG. Toto je vlastnost každého distribučního cíle. Musí `runOutputName` být jedinečný pro každý cíl distribuce. Zde je příklad, toto je dotazování distribuce Galerie sdílených obrázků:

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

Výstup obrazu bude prostředek spravované bitové kopie.

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
 
Rozmístit vlastnosti:
- **typ** – managedImage 
- **imageId** – ID prostředku cílovébitové bitové kopie, očekávaný formát: /subscriptions/\<subscriptionId>/resourceGroups/\<\<destinationResourceGroupName>/providers/Microsoft.Compute/images/imageName>
- **umístění** - umístění spravovaného obrázku.  
- **runOutputName** – jedinečný název pro identifikaci distribuce.  
- **artefakttagy** - Volitelné uživatelem zadané značky pár uhodnoty klíče.
 
 
> [!NOTE]
> Cílová skupina prostředků musí existovat.
> Pokud chcete, aby bitová kopie byla distribuována do jiné oblasti, zvýší se doba nasazení . 

### <a name="distribute-sharedimage"></a>Distribuce: sharedImage 
Galerie sdílených bitových kopií Azure je nová služba správy bitových kopií, která umožňuje správu replikace oblasti image, správy verzí a sdílení vlastních bitových kopií. Azure Image Builder podporuje distribuci s touto službou, takže můžete distribuovat image do oblastí podporovaných sdílených galerií obrázků. 
 
Sdílená galerie obrázků se skládá z: 
 
- Galerie - Kontejner pro více sdílených obrázků. Galerie je nasazena v jedné oblasti.
- Definice obrázků - koncepční seskupení pro obrázky. 
- Verze bitové kopie – jedná se o typ bitové kopie, který se používá k nasazení virtuálního virtuálního aplikace nebo škálovací sady. Verze bitové kopie lze replikovat do jiných oblastí, kde je potřeba nasadit virtuální chod.
 
Před distribucí do Galerie obrázků je nutné vytvořit galerii a definici obrázku, viz [Sdílené obrázky](shared-images.md). 

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

Distribuce vlastností pro sdílené galerie obrázků:

- **typ** - sharedImage  
- **galleryImageId** – ID sdílené galerie obrázků. Formát je:\</subscriptions/ subscriptionId>/resourceGroups/\<resourceGroupName>/providers/Microsoft.Compute/galleries/\<sharedImageGalleryName>/images/\<imageGalleryName>.
- **runOutputName** – jedinečný název pro identifikaci distribuce.  
- **artefakttagy** - Volitelné uživatelem zadané značky pár uhodnoty klíče.
- **replicationRegions** - Array oblastí pro replikaci. Jednou z oblastí musí být oblast, kde je galerie nasazena.
 
> [!NOTE]
> Azure Image Builder můžete použít v jiné oblasti než galerie, ale služba Azure Image Builder bude muset přenášet image mezi datovými centry a to bude trvat déle. Image Builder automaticky verze obrazu, na základě monotonické celé číslo, nelze zadat v současné době. 

### <a name="distribute-vhd"></a>Distribuce: Virtuální pevný disk  
Můžete výstup do virtuálního pevného disku. Potom můžete zkopírovat virtuální pevný disk a použít jej k publikování na Azure MarketPlace nebo použít s Azure Stack.  

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

Distribuce parametrů Virtuálního pevného disku:

- **typ** - VHD.
- **runOutputName** – jedinečný název pro identifikaci distribuce.  
- **tagy** - Volitelné uživatelem zadané značky dvojice hodnot klíče.
 
Azure Image Builder neumožňuje uživateli zadat umístění účtu úložiště, ale `runOutputs` můžete dotaz na stav umístění.  

```azurecli-interactive
az resource show \
   --ids "/subscriptions/$subscriptionId/resourcegroups/<imageResourceGroup>/providers/Microsoft.VirtualMachineImages/imageTemplates/<imageTemplateName>/runOutputs/<runOutputName>"  | grep artifactUri 
```

> [!NOTE]
> Po vytvoření virtuálního pevného disku jej zkopírujte co nejdříve do jiného umístění. Virtuální pevný disk se uchovává v účtu úložiště ve skupině dočasných prostředků vytvořené při odeslání šablony image do služby Azure Image Builder. Pokud odstraníte šablonu obrázku, ztratíte virtuální disk. 
 
## <a name="next-steps"></a>Další kroky

Existují ukázkové soubory JSON pro různé scénáře v [GitHubu Azure Image Builder](https://github.com/danielsollondon/azvmimagebuilder).
 
