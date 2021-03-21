---
title: Vytvoření šablony Azure image Builder (Preview)
description: Naučte se, jak vytvořit šablonu pro použití s nástrojem Azure image Builder.
author: danielsollondon
ms.author: danis
ms.date: 03/02/2021
ms.topic: reference
ms.service: virtual-machines
ms.subservice: image-builder
ms.collection: linux
ms.reviewer: cynthn
ms.openlocfilehash: aaaabe758b036335062907c8e5549ae876c63997
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "104594729"
---
# <a name="preview-create-an-azure-image-builder-template"></a>Verze Preview: Vytvoření šablony Azure image Builder 

Azure image Builder k předávání informací do služby tvůrce imagí používá soubor. JSON. V tomto článku se přejdou na oddíly souboru JSON, takže si můžete vytvořit vlastní. Příklady úplných souborů. JSON najdete v tématu věnovaném [nástroji Azure image Builder GitHub](https://github.com/Azure/azvmimagebuilder/tree/main/quickquickstarts).

Toto je základní formát šablony:

```json
 { 
    "type": "Microsoft.VirtualMachineImages/imageTemplates", 
    "apiVersion": "2020-02-14", 
    "location": "<region>", 
    "tags": {
        "<name": "<value>",
        "<name>": "<value>"
     },
    "identity":{},           
    "dependsOn": [], 
    "properties": { 
        "buildTimeoutInMinutes": <minutes>, 
        "vmProfile": 
            {
            "vmSize": "<vmSize>",
            "osDiskSizeGB": <sizeInGB>,
            "vnetConfig": {
                "subnetId": "/subscriptions/<subscriptionID>/resourceGroups/<vnetRgName>/providers/Microsoft.Network/virtualNetworks/<vnetName>/subnets/<subnetName>"
                }
            },
        "source": {}, 
        "customize": {}, 
        "distribute": {} 
      } 
 } 
```



## <a name="type-and-api-version"></a>Typ a verze rozhraní API

`type`Je typ prostředku, který musí být `"Microsoft.VirtualMachineImages/imageTemplates"` . V `apiVersion` průběhu času se změny rozhraní API změní, ale měla by být `"2020-02-14"` pro verzi Preview.

```json
    "type": "Microsoft.VirtualMachineImages/imageTemplates",
    "apiVersion": "2020-02-14",
```

## <a name="location"></a>Umístění

Umístění je oblast, kde se vytvoří vlastní image. Pro náhled tvůrce imagí se podporují tyto oblasti:

- East US
- USA – východ 2
- USA – středozápad
- USA – západ
- Západní USA 2
- Severní Evropa
- West Europe


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

Ve výchozím nastavení nemění tvůrce imagí velikost obrázku, ale bude používat velikost ze zdrojové image. Velikost disku s operačním systémem (Win a Linux) můžete zvětšit **jenom** tak, že je volitelná a hodnota 0 znamená, že zůstane stejná velikost jako zdrojová image. Velikost disku operačního systému se nedá zmenšit na menší než velikost ze zdrojové image.

```json
 {
    "osDiskSizeGB": 100
 },
```

## <a name="vnetconfig"></a>vnetConfig
Pokud neurčíte žádné vlastnosti virtuální sítě, vytvoří Tvůrce imagí svou vlastní virtuální síť, veřejnou IP adresu a NSG. Veřejná IP adresa se používá ke komunikaci s virtuálním počítačem sestavení, ale pokud nechcete, aby měl tvůrce imagí přístup k existujícím prostředkům virtuální sítě, jako jsou konfigurační servery (DSC, saďte, Puppet, Ansible), sdílené složky atd., můžete zadat virtuální síť. Další informace najdete v [dokumentaci k síti](image-builder-networking.md), která je volitelná.

```json
    "vnetConfig": {
        "subnetId": "/subscriptions/<subscriptionID>/resourceGroups/<vnetRgName>/providers/Microsoft.Network/virtualNetworks/<vnetName>/subnets/<subnetName>"
        }
    }
```
## <a name="tags"></a>Značky

Jedná se o páry klíč/hodnota, které můžete zadat pro vygenerovanou bitovou kopii.

## <a name="depends-on-optional"></a>Závisí na (volitelné)

Tento volitelný oddíl lze použít k zajištění, aby byly před pokračováním dokončeny závislosti. 

```json
    "dependsOn": [],
```

Další informace najdete v tématu [Definování závislostí prostředků](../../azure-resource-manager/templates/define-resource-dependency.md#dependson).

## <a name="identity"></a>Identita

Požadováno – Pokud má Tvůrce imagí oprávnění ke čtení a zápisu obrázků, přečtěte si téma z Azure Storage musíte vytvořit identitu Azure User-Assigned, která má oprávnění k jednotlivým prostředkům. Podrobnosti o tom, jak nástroj image Builder funguje, a relevantní postup najdete v [dokumentaci](image-builder-user-assigned-identity.md).


```json
    "identity": {
    "type": "UserAssigned",
          "userAssignedIdentities": {
            "<imgBuilderId>": {}
        }
        },
```


Podpora tvůrce imagí pro User-Assignedou identitu:
* Podporuje jenom jedinou identitu.
* Nepodporuje vlastní názvy domén.

Další informace najdete v tématu [co jsou spravované identity pro prostředky Azure?](../../active-directory/managed-identities-azure-resources/overview.md).
Další informace o nasazení této funkce najdete v tématu [Konfigurace spravovaných identit pro prostředky Azure na virtuálním počítači Azure pomocí Azure CLI](../../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md#user-assigned-managed-identity).

## <a name="properties-source"></a>Vlastnosti: zdroj

`source`Část obsahuje informace o zdrojové imagi, kterou bude používat Tvůrce imagí. Nástroj image Builder aktuálně nativně podporuje vytváření imagí technologie Hyper-V Generation (Gen1) 1 do galerie sdílených imagí Azure (SIG) nebo spravované image. Pokud chcete vytvořit image Gen2, musíte použít zdrojovou image Gen2 a distribuovat ji na VHD. Potom budete muset vytvořit spravovanou bitovou kopii z virtuálního pevného disku a vložit ji do souboru SIG jako Gen2 image.

Rozhraní API vyžaduje typ SourceType, který definuje zdroj pro sestavení image, v současné době existují tři typy:
- PlatformImage – indikuje, že zdrojová Image je image na webu Marketplace.
- ManagedImage – Toto použijte při spuštění z obyčejné spravované image.
- SharedImageVersion – používá se, pokud používáte verzi image v galerii sdílených imagí jako zdroj.


> [!NOTE]
> Pokud používáte stávající vlastní image Windows, můžete spustit příkaz Sysprep až 8 časů na jedné imagi Windows, další informace najdete v dokumentaci k [nástroji Sysprep](/windows-hardware/manufacture/desktop/sysprep--generalize--a-windows-installation#limits-on-how-many-times-you-can-run-sysprep) .

### <a name="platformimage-source"></a>PlatformImage zdroj 
Azure image Builder podporuje image Windows serveru a klienta a Azure Marketplace pro Linux. úplný seznam najdete [tady](../image-builder-overview.md#os-support) . 

```json
        "source": {
            "type": "PlatformImage",
                "publisher": "Canonical",
                "offer": "UbuntuServer",
                "sku": "18.04-LTS",
                "version": "latest"
        },
```


Tady jsou vlastnosti, které se používají k vytvoření virtuálního počítače pomocí AZ CLI, pro získání vlastností použijte níže: 
 
```azurecli-interactive
az vm image list -l westus -f UbuntuServer -p Canonical --output table –-all 
```

V této verzi můžete použít ' nejnovější ', verze je vyhodnocena, když dojde k sestavení obrázku, nikoli při odeslání šablony. Pokud tuto funkci použijete s cílem Galerie sdílených imagí, můžete se vyhnout opětovnému odeslání šablony a znovu spustit sestavení image v intervalech, takže se vaše image znovu vytvoří z nejaktuálnějších imagí.

#### <a name="support-for-market-place-plan-information"></a>Podpora informací o plánu na místě na trhu
Můžete také zadat informace o plánu, například:
```json
    "source": {
        "type": "PlatformImage",
        "publisher": "RedHat",
        "offer": "rhel-byos",
        "sku": "rhel-lvm75",
        "version": "latest",
        "planInfo": {
            "planName": "rhel-lvm75",
            "planProduct": "rhel-byos",
            "planPublisher": "redhat"
       }
```
### <a name="managedimage-source"></a>ManagedImage zdroj

Nastaví zdrojovou Image jako existující spravovanou bitovou kopii zobecněného virtuálního pevného disku nebo virtuálního počítače.

> [!NOTE]
> Zdrojová image spravovaná musí být podporovaného operačního systému a image musí mít stejnou oblast jako vaše šablona Azure image Builder. 

```json
        "source": { 
            "type": "ManagedImage", 
                "imageId": "/subscriptions/<subscriptionId>/resourceGroups/{destinationResourceGroupName}/providers/Microsoft.Compute/images/<imageName>"
        }
```

`imageId`By měl být ResourceID spravované image. Slouží `az image list` k vypsání dostupných imagí.


### <a name="sharedimageversion-source"></a>SharedImageVersion zdroj
Nastaví zdrojovou bitovou kopii existující verze image v galerii sdílených imagí.

> [!NOTE]
> Zdrojová bitová kopie musí být podporovaného operačního systému a image musí mít stejnou oblast jako vaše šablona Azure image Builder. Pokud ne, replikujte prosím verzi image do oblasti šablony tvůrce imagí.


```json
        "source": { 
            "type": "SharedImageVersion", 
            "imageVersionID": "/subscriptions/<subscriptionId>/resourceGroups/<resourceGroup>/p  roviders/Microsoft.Compute/galleries/<sharedImageGalleryName>/images/<imageDefinitionName/versions/<imageVersion>" 
   } 
```

`imageVersionId`Měla by být ResourceID verze image. K vypsání verzí imagí použijte příkaz [AZ SIG Image-Version list](/cli/azure/sig/image-version#az-sig-image-version-list) .


## <a name="properties-buildtimeoutinminutes"></a>Vlastnosti: buildTimeoutInMinutes

Ve výchozím nastavení se spustí Tvůrce imagí po dobu 240 minut. Po této instalaci dojde k vypršení časového limitu a zastavení, bez ohledu na to, jestli je sestavení image dokončené. Pokud dojde k vypršení časového limitu, zobrazí se chybová zpráva podobná této:

```text
[ERROR] Failed while waiting for packerizer: Timeout waiting for microservice to
[ERROR] complete: 'context deadline exceeded'
```

Pokud nezadáte hodnotu buildTimeoutInMinutes, nebo ji nastavte na 0, použije se výchozí hodnota. Můžete zvýšit nebo snížit hodnotu až do maximálního počtu 960mins (16hrs). V systému Windows nedoporučujeme toto nastavit níže 60 minut. Pokud zjistíte, že se vám časový limit nelíbí, zkontrolujte [protokoly](image-builder-troubleshoot.md#customization-log)a podívejte se, jestli krok přizpůsobení čeká na něco jako uživatelský vstup. 

Pokud zjistíte, že k dokončení úprav potřebujete víc času, nastavte to podle toho, co si myslíte, že potřebujete, a s malým režijním časem. Ale nenastavuje se příliš vysoká, protože možná budete muset počkat na vypršení časového limitu před zobrazením chyby. 


## <a name="properties-customize"></a>Vlastnosti: přizpůsobení

Image Builder podporuje několik "úprav". Vlastníci jsou funkce, které se používají k přizpůsobení image, jako je spouštění skriptů nebo restartování serverů. 

Při použití `customize` : 
- Můžete použít více úprav, ale musí mít jedinečné `name` .
- Úpravci provádějí v pořadí zadaném v šabloně.
- Pokud jeden z úprav selže, celá komponenta přizpůsobení selže a ohlásí chybu.
- Důrazně doporučujeme skript před jeho použitím v šabloně důkladně otestovat. Ladění skriptu na vlastním VIRTUÁLNÍm počítači bude snazší.
- Do skriptů neumísťujte citlivá data. 
- Pokud nepoužíváte [MSI](./image-builder-user-assigned-identity.md), musí být umístění skriptu veřejně přístupná.

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

> [!NOTE]
> Vložené příkazy lze zobrazit v definici šablony obrázku a podpora Microsoftu při pomoci s případem podpory. Pokud máte citlivé informace, měl by se přesunout do skriptů v Azure Storage, kde Access vyžaduje ověření.
 
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

> [!NOTE]
> Vložené příkazy jsou uloženy jako součást definice šablony obrázku. Tyto příkazy lze zobrazit při výpisu definice bitové kopie a tyto jsou také podpora Microsoftu v případě případu podpory pro účely řešení potíží. Pokud máte citlivé příkazy nebo hodnoty, důrazně doporučujeme, abyste je přesunuli do skriptů a pomocí identity uživatele ověřili Azure Storage.

#### <a name="super-user-privileges"></a>Oprávnění super uživatele
Příkazy, které se mají spustit s oprávněními superuživatele, musí být s předponou `sudo` , můžete je přidat do skriptů nebo použít vložené příkazy, například:
```json
                "type": "Shell",
                "name": "setupBuildPath",
                "inline": [
                    "sudo mkdir /buildArtifacts",
                    "sudo cp /tmp/index.html /buildArtifacts/index.html"
```
Příklad skriptu používajícího sudo, na který můžete odkazovat pomocí scriptUri:
```bash
#!/bin/bash -e

echo "Telemetry: creating files"
mkdir /myfiles

echo "Telemetry: running sudo 'as-is' in a script"
sudo touch /myfiles/somethingElevated.txt
```

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
             "validExitCodes": "<exit code>",
             "runElevated": "<true or false>" 
         } 
    ], 
```

Podpora OS: Windows a Linux

Přizpůsobení vlastností:

- **typ** – PowerShell.
- **scriptUri** -URI do umístění souboru skriptu PowerShellu. 
- **vložené** – vložené příkazy, které mají být spuštěny, oddělené čárkami.
- **validExitCodes** – volitelné, platné kódy, které lze vrátit z příkazu Script/inline, tím se vyhnete nahlášené chybě příkazu Script/inline.
- **runElevated** – volitelná, logická hodnota, podpora spouštění příkazů a skriptů se zvýšenými oprávněními.
- **sha256Checksum** -hodnota kontrolního součtu SHA256 souboru, vygenerujete ho místně a pak tvůrce imagí provede kontrolu kontrolního součtu a ověření.
    * Vygenerování sha256Checksum pomocí prostředí PowerShell ve Windows [Get-hash](/powershell/module/microsoft.powershell.utility/get-filehash)


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

> [!NOTE]
> Pokud sourceUri je účet Azure Storage, nezávisle na tom, jestli je objekt BLOB označený jako Public, udělíte oprávnění identitě spravovaného uživatele pro přístup pro čtení objektu BLOB. Pokud chcete nastavit oprávnění úložiště, podívejte se prosím na tento [příklad](./image-builder-user-assigned-identity.md#create-a-resource-group) .

- **cíl** – jedná se o úplnou cestu k cíli a název souboru. Musí existovat všechny odkazované cesty a podadresáře, pomocí prostředí PowerShell nebo úprav prostředí PowerShell je nastavit předem. Pomocí úprav skriptů můžete vytvořit cestu. 

To je podporováno v adresářích systému Windows a cestách pro Linux, ale existují několik rozdílů: 
- Operační systém Linux – pouze nástroj pro tvůrce obrázků s cestou může zapisovat do/tmp.
- Windows – žádné omezení cesty, ale cesta musí existovat.
 
 
Pokud při pokusu o stažení souboru nebo jeho umístění do zadaného adresáře dojde k chybě, krok přizpůsobení selže a bude v souboru Customization. log.

> [!NOTE]
> Soubor úprav souborů je vhodný jenom pro stahování malých souborů, < 20MB. U větších souborů ke stažení použijte skript nebo vložený příkaz, ke stažení souborů, jako je Linux `wget` nebo Windows, použijte kód `curl` `Invoke-WebRequest` .

### <a name="windows-update-customizer"></a>web Windows Update úprav
Tento úprav je postaven na [komunitě web Windows Update zřídí](https://packer.io/docs/provisioners/community-supported.html) pro balírnu, což je open source projekt udržovaný komunitou pro balení. Společnost Microsoft testuje a ověřuje ve službě image Builder službu pro vytváření imagí a bude podporovat zkoumání problémů s IT a řešení problémů, ale open source projekt není oficiálně podporován společností Microsoft. Podrobnou dokumentaci a nápovědu k web Windows Update zřídíte v úložišti projektu.

```json
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
OS support: Windows
```

Přizpůsobení vlastností:
- **typ**  – windowsupdate.
- **Třída SearchCriteria** – volitelné, definuje, který typ aktualizací se má nainstalovat (doporučeno, důležité atd.), BrowseOnly = 0 a IsInstalled = 0 (doporučeno) je výchozí hodnota.
- **filtry** – volitelné, umožňuje zadat filtr pro zahrnutí nebo vyloučení aktualizací.
- **updateLimit** – volitelné, definuje, kolik aktualizací se dá nainstalovat, výchozí 1000.
 
> [!NOTE]
> V případě, že se vyskytnou nějaké nedokončené restarty Windows nebo jsou pořád spuštěné instalace aplikací, může se stát, web Windows Update že se vám tato chyba obvykle zobrazí v části přizpůsobení. log `System.Runtime.InteropServices.COMException (0x80240016): Exception from HRESULT: 0x80240016` . Důrazně doporučujeme zvážit přidání do restartování systému Windows a/nebo povolit aplikacím dostatek času k dokončení instalací pomocí příkazů [režimu spánku](/powershell/module/microsoft.powershell.utility/start-sleep) nebo čekání ve vložených příkazech nebo skriptech před spuštěním web Windows Update.

### <a name="generalize"></a>Generalizovat 
Ve výchozím nastavení bude Azure image Builder na konci každé fáze přizpůsobení image taky spouštět kód zrušení zřízení, aby se image generalizoval. Generalizace je proces, ve kterém je image nastavená tak, aby se mohla znovu použít k vytvoření více virtuálních počítačů. Pro virtuální počítače s Windows používá Azure image Builder nástroj Sysprep. Pro Linux spustí Azure image Builder "waagent-disvision". 

Příkazy pro sestavování obrázků uživatelů do generalizace nemusí být vhodné pro každou situaci, takže Azure image Builder vám v případě potřeby umožní upravit tento příkaz. 

Pokud migrujete existující přizpůsobení a používáte jiné příkazy Sysprep/waagent, můžete použít obecné příkazy pro tvůrce imagí, a pokud se vytvoření virtuálního počítače nepovede, použijte vlastní příkazy Sysprep nebo waagent.

Pokud Azure image Builder úspěšně vytvoří vlastní image Windows a vy z ní vytvoříte virtuální počítač, zjistíte, že vytvoření virtuálního počítače selže nebo že se neúspěšně dokončí, budete muset zkontrolovat dokumentaci k nástroji Sysprep systému Windows Server nebo vyvolat žádost o podporu pomocí týmu podpory Windows Server Sysprep pro zákazníky, kteří můžou řešit správné použití nástroje Sysprep a upozornit na ně.


#### <a name="default-sysprep-command"></a>Výchozí příkaz Sysprep
```powershell
Write-Output '>>> Waiting for GA Service (RdAgent) to start ...'
while ((Get-Service RdAgent).Status -ne 'Running') { Start-Sleep -s 5 }
Write-Output '>>> Waiting for GA Service (WindowsAzureTelemetryService) to start ...'
while ((Get-Service WindowsAzureTelemetryService) -and ((Get-Service WindowsAzureTelemetryService).Status -ne 'Running')) { Start-Sleep -s 5 }
Write-Output '>>> Waiting for GA Service (WindowsAzureGuestAgent) to start ...'
while ((Get-Service WindowsAzureGuestAgent).Status -ne 'Running') { Start-Sleep -s 5 }
Write-Output '>>> Sysprepping VM ...'
if( Test-Path $Env:SystemRoot\system32\Sysprep\unattend.xml ) {
  Remove-Item $Env:SystemRoot\system32\Sysprep\unattend.xml -Force
}
& $Env:SystemRoot\System32\Sysprep\Sysprep.exe /oobe /generalize /quiet /quit
while($true) {
  $imageState = (Get-ItemProperty HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\Setup\State).ImageState
  Write-Output $imageState
  if ($imageState -eq 'IMAGE_STATE_GENERALIZE_RESEAL_TO_OOBE') { break }
  Start-Sleep -s 5
}
Write-Output '>>> Sysprep complete ...'
```
#### <a name="default-linux-deprovision-command"></a>Výchozí příkaz pro zrušení zřízení pro Linux

```bash
/usr/sbin/waagent -force -deprovision+user && export HISTSIZE=0 && sync
```

#### <a name="overriding-the-commands"></a>Přepsání příkazů
Pokud chcete příkazy přepsat, použijte modul pro vytváření skriptů PowerShellu nebo prostředí k vytvoření souborů příkazů s přesným názvem souboru a vložte je do správných adresářů:

* Windows: c:\DeprovisioningScript.ps1
* Linux:/tmp/DeprovisioningScript.sh

Nástroj image Builder tyto příkazy přečte a zapíše se do protokolů AIB, "Customize. log". Podívejte se na téma [Poradce při potížích](image-builder-troubleshoot.md#customization-log) s postupem shromažďování protokolů.
 
## <a name="properties-distribute"></a>Vlastnosti: distribuce

Azure image Builder podporuje tři cíle distribuce: 

- Image spravovaná **managedImage**
- Galerie sdílených imagí **sharedImage**
- **VHD – VHD** v účtu úložiště

Můžete distribuovat obrázek do obou cílových typů ve stejné konfiguraci.

> [!NOTE]
> Výchozí příkaz Sysprep AIB neobsahuje "/Mode: VM", ale to může být nutné při vytváření imagí, na kterých bude nainstalovaná role HyperV. Pokud potřebujete přidat tento argument příkazu, musíte přepsat příkaz Sysprep.

Vzhledem k tomu, že můžete mít více než jeden cíl pro distribuci do nástroje, nástroj image Builder udržuje stav pro každý cíl distribuce, ke kterému lze přistup pomocí dotazování na `runOutputName` .  `runOutputName`Je objekt, který můžete odeslat dotazem na distribuci pro informace o této distribuci. Můžete například zadat dotaz na umístění virtuálního pevného disku nebo oblasti, ve kterých byla verze bitové kopie replikována, nebo vytvořená verze image SIG. Toto je vlastnost všech cílů distribuce. `runOutputName`Musí být jedinečný pro každý cíl distribuce. Tady je příklad, který se dotazuje na distribuci Galerie sdílených imagí:

```bash
subscriptionID=<subcriptionID>
imageResourceGroup=<resourceGroup of image template>
runOutputName=<runOutputName>

az resource show \
        --ids "/subscriptions/$subscriptionID/resourcegroups/$imageResourceGroup/providers/Microsoft.VirtualMachineImages/imageTemplates/ImageTemplateLinuxRHEL77/runOutputs/$runOutputName"  \
        --api-version=2020-02-14
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
{
       "type":"managedImage",
       "imageId": "<resource ID>",
       "location": "<region>",
       "runOutputName": "<name>",
       "artifactTags": {
            "<name": "<value>",
            "<name>": "<value>"
        }
}
```
 
Vlastnosti distribuce:
- **typ** – managedImage 
- **imageId** – ID prostředku cílového obrázku, očekávaný formát:/subscriptions/ \<subscriptionId> /resourceGroups/ \<destinationResourceGroupName> /providers/Microsoft.COMPUTE/images/\<imageName>
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
 
Než budete moct distribuovat do galerie imagí, musíte vytvořit galerii a definici image, viz [sdílené image](../shared-images-cli.md). 

```json
{
    "type": "SharedImage",
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
- **galleryImageId** – ID Galerie sdílených imagí, kterou lze zadat ve dvou formátech:
    * Automatické vytváření verzí – nástroj image Builder vygeneruje číslo verze monotónní, což je užitečné v případě, že chcete zachovat obnovování imagí ze stejné šablony: formát je: `/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.Compute/galleries/<sharedImageGalleryName>/images/<imageGalleryName>` .
    * Explicitní verze – můžete předat číslo verze, které má Tvůrce obrázků použít. Formát je následující: `/subscriptions/<subscriptionID>/resourceGroups/<rgName>/providers/Microsoft.Compute/galleries/<sharedImageGalName>/images/<imageDefName>/versions/<version e.g. 1.1.1>`

- **runOutputName** – jedinečný název pro identifikaci distribuce.  
- **artifactTags** – volitelné uživatelsky definované páry klíč-hodnota.
- **replicationRegions** – pole oblastí pro replikaci. Jedna z oblastí musí být oblast, ve které je galerie nasazena. Přidání oblastí bude znamenat zvýšení času sestavení, protože sestavení není dokončeno až do dokončení replikace.
- **excludeFromLatest** (volitelné) to umožňuje označit verzi image, kterou vytvoříte, se v definici SIG nepoužívá jako nejnovější verze, výchozí hodnota je false.
- **storageAccountType** (nepovinný) AIB podporuje zadání těchto typů úložiště pro verzi image, která se má vytvořit:
    * "Standard_LRS"
    * "Standard_ZRS"


> [!NOTE]
> Pokud šablona obrázku a odkazovaná `image definition` není ve stejném umístění, zobrazí se další čas pro vytváření imagí. Tvůrce imagí aktuálně neobsahuje `location` parametr pro prostředek verze image, a proto ho převezmeme od nadřazeného objektu `image definition` . Pokud je například definice obrázku v westus a chcete, aby byla verze image replikovaná do eastus, objekt BLOB se zkopíruje do do westus. z tohoto důvodu se vytvoří prostředek verze image v westus a pak se replikuje do eastus. Chcete-li se vyhnout další době replikace, zajistěte, aby byla `image definition` šablona image a ve stejném umístění.


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

## <a name="image-template-operations"></a>Operace s obrázkem šablony

### <a name="starting-an-image-build"></a>Spuštění sestavení obrázku
Chcete-li spustit sestavení, je nutné vyvolat ' Run ' na prostředku šablony obrázku, příklady `run` příkazů:

```PowerShell
Invoke-AzResourceAction -ResourceName $imageTemplateName -ResourceGroupName $imageResourceGroup -ResourceType Microsoft.VirtualMachineImages/imageTemplates -ApiVersion "2020-02-14" -Action Run -Force
```


```bash
az resource invoke-action \
     --resource-group $imageResourceGroup \
     --resource-type  Microsoft.VirtualMachineImages/imageTemplates \
     -n helloImageTemplateLinux01 \
     --action Run 
```

### <a name="cancelling-an-image-build"></a>Rušení sestavení obrázku
Pokud používáte sestavení bitové kopie, které se domníváte, že je nesprávné, čeká se na vstup uživatele, nebo jste se už neúspěšně dokončí, můžete sestavení zrušit.

Sestavení může být kdykoli zrušeno. Pokud byla fáze distribuce zahájena, můžete stále zrušit, ale budete muset vyčistit všechny bitové kopie, které nemusí být dokončeny. Příkaz Cancel nečeká na dokončení akce zrušit, monitorujte prosím `lastrunstatus.runstate` , abyste zrušili průběh pomocí těchto [příkazů](image-builder-troubleshoot.md#customization-log)stavu.


Příklady `cancel` příkazů:

```powerShell
Invoke-AzResourceAction -ResourceName $imageTemplateName -ResourceGroupName $imageResourceGroup -ResourceType Microsoft.VirtualMachineImages/imageTemplates -ApiVersion "2020-02-14" -Action Cancel -Force
```

```bash
az resource invoke-action \
     --resource-group $imageResourceGroup \
     --resource-type  Microsoft.VirtualMachineImages/imageTemplates \
     -n helloImageTemplateLinux01 \
     --action Cancel 
```

## <a name="next-steps"></a>Další kroky

V [GitHubu pro Azure image Builder](https://github.com/azure/azvmimagebuilder)jsou k dispozici ukázkové soubory. JSON pro různé scénáře.