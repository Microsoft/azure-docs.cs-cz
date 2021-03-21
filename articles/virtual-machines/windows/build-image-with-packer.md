---
title: PowerShell – postup vytvoření imagí virtuálních počítačů pomocí balírny
description: Naučte se používat balíčky a PowerShell k vytváření imagí virtuálních počítačů v Azure.
author: cynthn
ms.service: virtual-machines
ms.subservice: imaging
ms.collection: windows
ms.topic: how-to
ms.workload: infrastructure
ms.date: 08/05/2020
ms.author: cynthn
ms.openlocfilehash: a2cd6152f9c8e75223fcf1aab77fee7700223979
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "102561003"
---
# <a name="powershell-how-to-use-packer-to-create-virtual-machine-images-in-azure"></a>PowerShell: jak používat balíček k vytváření imagí virtuálních počítačů v Azure
Každý virtuální počítač (VM) v Azure se vytvoří z image, která definuje distribuci Windows a verzi operačního systému. Obrázky můžou zahrnovat předem nainstalované aplikace a konfigurace. Azure Marketplace poskytuje mnoho prvních a imagí jiných výrobců pro většinu běžných prostředí operačního systému a aplikací, nebo můžete vytvořit vlastní image přizpůsobené vašim potřebám. Tento článek podrobně popisuje, jak používat open source [sadu](https://www.packer.io/) nástrojů k definování a vytváření vlastních imagí v Azure.

Tento článek byl naposledy testován na 8/5/2020 pomocí [balíčku](https://www.packer.io/docs/install) verze 1.6.1.

> [!NOTE]
> Azure teď má službu, Azure image Builder (Preview) pro definování a vytváření vlastních imagí. Azure image Builder je postavená na balíčku, takže můžete s ním dokonce používat i existující skripty sestavovatele prostředí pro vytváření balíčků. Informace o tom, jak začít s Azure image Builder, najdete v tématu [Vytvoření virtuálního počítače s Windows pomocí Azure image Builder](image-builder.md).

## <a name="create-azure-resource-group"></a>Vytvoření skupiny prostředků Azure
Během procesu sestavování vytvoří balíček dočasné prostředky Azure při sestavení zdrojového virtuálního počítače. Pokud chcete zachytit zdrojový virtuální počítač pro použití jako image, musíte definovat skupinu prostředků. Výstup procesu sestavení balíčku je uložený v této skupině prostředků.

Vytvořte skupinu prostředků pomocí [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). Následující příklad vytvoří skupinu prostředků s názvem *myPackerGroup* v umístění *eastus* :

```azurepowershell
$rgName = "myPackerGroup"
$location = "East US"
New-AzResourceGroup -Name $rgName -Location $location
```

## <a name="create-azure-credentials"></a>Vytvoření přihlašovacích údajů Azure
Balíček se ověřuje pomocí instančního objektu pomocí služby Azure. Instanční objekt Azure je identita zabezpečení, kterou můžete používat s aplikacemi, službami a nástroji pro automatizaci, jako je například nástroj Pack. Oprávnění můžete řídit a definovat podle toho, jaké operace může instanční objekt v Azure provádět.

Vytvořte instanční objekt pomocí [New-AzADServicePrincipal](/powershell/module/az.resources/new-azadserviceprincipal). Hodnota pro `-DisplayName` musí být jedinečná; podle potřeby nahraďte vlastní hodnotou.  

```azurepowershell
$sp = New-AzADServicePrincipal -DisplayName "PackerSP$(Get-Random)"
$BSTR = [System.Runtime.InteropServices.Marshal]::SecureStringToBSTR($sp.Secret)
$plainPassword = [System.Runtime.InteropServices.Marshal]::PtrToStringAuto($BSTR)
```

Pak vytvořte výstup hesla a ID aplikace.

```powershell
$plainPassword
$sp.ApplicationId
```


K ověření v Azure je také potřeba získat ID vašeho tenanta Azure a ID předplatného pomocí [Get-AzSubscription](/powershell/module/az.accounts/get-azsubscription):

```powershell
Get-AzSubscription
```


## <a name="define-packer-template"></a>Definovat šablonu balíčku
K sestavení imagí vytvoříte šablonu jako soubor JSON. V šabloně definujete sestavení a zřizování, které provádějí vlastní proces sestavení. Balírna má [Tvůrce pro Azure](https://www.packer.io/docs/builders/azure.html) , který umožňuje definovat prostředky Azure, například přihlašovací údaje instančního objektu vytvořené v předchozím kroku.

Vytvořte soubor s názvem *windows.js* a vložte následující obsah. Zadejte vlastní hodnoty pro následující:

| Parametr                           | Kde získat |
|-------------------------------------|----------------------------------------------------|
| *client_id*                         | Zobrazit ID instančního objektu s `$sp.applicationId` |
| *client_secret*                     | Zobrazit automaticky generované heslo pomocí `$plainPassword` |
| *tenant_id*                         | Výstup z `$sub.TenantId` příkazu |
| *subscription_id*                   | Výstup z `$sub.SubscriptionId` příkazu |
| *managed_image_resource_group_name* | Název skupiny prostředků, kterou jste vytvořili v prvním kroku |
| *managed_image_name*                | Název vytvořené image spravovaného disku |

```json
{
  "builders": [{
    "type": "azure-arm",

    "client_id": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx",
    "client_secret": "ppppppp-pppp-pppp-pppp-ppppppppppp",
    "tenant_id": "zzzzzzz-zzzz-zzzz-zzzz-zzzzzzzzzzzz",
    "subscription_id": "yyyyyyy-yyyy-yyyy-yyyy-yyyyyyyyyyy",

    "managed_image_resource_group_name": "myPackerGroup",
    "managed_image_name": "myPackerImage",

    "os_type": "Windows",
    "image_publisher": "MicrosoftWindowsServer",
    "image_offer": "WindowsServer",
    "image_sku": "2016-Datacenter",

    "communicator": "winrm",
    "winrm_use_ssl": true,
    "winrm_insecure": true,
    "winrm_timeout": "5m",
    "winrm_username": "packer",

    "azure_tags": {
        "dept": "Engineering",
        "task": "Image deployment"
    },

    "build_resource_group_name": "myPackerGroup",
    "vm_size": "Standard_D2_v2"
  }],
  "provisioners": [{
    "type": "powershell",
    "inline": [
      "Add-WindowsFeature Web-Server",
      "while ((Get-Service RdAgent).Status -ne 'Running') { Start-Sleep -s 5 }",
      "while ((Get-Service WindowsAzureGuestAgent).Status -ne 'Running') { Start-Sleep -s 5 }",
      "& $env:SystemRoot\\System32\\Sysprep\\Sysprep.exe /oobe /generalize /quiet /quit",
      "while($true) { $imageState = Get-ItemProperty HKLM:\\SOFTWARE\\Microsoft\\Windows\\CurrentVersion\\Setup\\State | Select ImageState; if($imageState.ImageState -ne 'IMAGE_STATE_GENERALIZE_RESEAL_TO_OOBE') { Write-Output $imageState.ImageState; Start-Sleep -s 10  } else { break } }"
    ]
  }]
}
```

Tato šablona vytvoří virtuální počítač s Windows serverem 2016, nainstaluje službu IIS a pak provede generalizaci virtuálního počítače pomocí nástroje Sysprep. Instalace služby IIS ukazuje, jak můžete pomocí zřízení PowerShellu spustit další příkazy. Finální image balíčku pak zahrnuje instalaci a konfiguraci požadovaného softwaru.

Agent hosta systému Windows se účastní procesu Sysprep. Aby se virtuální počítač mohl sysprep'ed, musí být tento agent plně nainstalovaný. Aby se zajistilo, že se jedná o true, musí se před spuštěním sysprep.exe spustit všechny služby agenta. Předchozí fragment kódu JSON ukazuje jeden ze způsobů, jak to provést v zřízení PowerShellu. Tento fragment kódu je vyžadován pouze v případě, že je virtuální počítač konfigurován pro instalaci agenta, což je výchozí nastavení.


## <a name="build-packer-image"></a>Obrázek sady Build Pack
Pokud ještě nemáte v místním počítači nainstalovaný balíček, [postupujte podle pokynů k instalaci nástroje Pack](https://learn.hashicorp.com/packer/getting-started/install).

Sestavte bitovou kopii otevřením příkazového řádku a zadáním souboru šablony balíčku pomocí následujícího postupu:

```
./packer build windows.json
```

Příklad výstupu z předchozích příkazů je následující:

```bash
azure-arm output will be in this color.

==> azure-arm: Running builder ...
    azure-arm: Creating Azure Resource Manager (ARM) client ...
==> azure-arm: Creating resource group ...
==> azure-arm:  -> ResourceGroupName : ‘packer-Resource-Group-pq0mthtbtt’
==> azure-arm:  -> Location          : ‘East US’
==> azure-arm:  -> Tags              :
==> azure-arm:  ->> task : Image deployment
==> azure-arm:  ->> dept : Engineering
==> azure-arm: Validating deployment template ...
==> azure-arm:  -> ResourceGroupName : ‘packer-Resource-Group-pq0mthtbtt’
==> azure-arm:  -> DeploymentName    : ‘pkrdppq0mthtbtt’
==> azure-arm: Deploying deployment template ...
==> azure-arm:  -> ResourceGroupName : ‘packer-Resource-Group-pq0mthtbtt’
==> azure-arm:  -> DeploymentName    : ‘pkrdppq0mthtbtt’
==> azure-arm: Getting the certificate’s URL ...
==> azure-arm:  -> Key Vault Name        : ‘pkrkvpq0mthtbtt’
==> azure-arm:  -> Key Vault Secret Name : ‘packerKeyVaultSecret’
==> azure-arm:  -> Certificate URL       : ‘https://pkrkvpq0mthtbtt.vault.azure.net/secrets/packerKeyVaultSecret/8c7bd823e4fa44e1abb747636128adbb'
==> azure-arm: Setting the certificate’s URL ...
==> azure-arm: Validating deployment template ...
==> azure-arm:  -> ResourceGroupName : ‘packer-Resource-Group-pq0mthtbtt’
==> azure-arm:  -> DeploymentName    : ‘pkrdppq0mthtbtt’
==> azure-arm: Deploying deployment template ...
==> azure-arm:  -> ResourceGroupName : ‘packer-Resource-Group-pq0mthtbtt’
==> azure-arm:  -> DeploymentName    : ‘pkrdppq0mthtbtt’
==> azure-arm: Getting the VM’s IP address ...
==> azure-arm:  -> ResourceGroupName   : ‘packer-Resource-Group-pq0mthtbtt’
==> azure-arm:  -> PublicIPAddressName : ‘packerPublicIP’
==> azure-arm:  -> NicName             : ‘packerNic’
==> azure-arm:  -> Network Connection  : ‘PublicEndpoint’
==> azure-arm:  -> IP Address          : ‘40.76.55.35’
==> azure-arm: Waiting for WinRM to become available...
==> azure-arm: Connected to WinRM!
==> azure-arm: Provisioning with Powershell...
==> azure-arm: Provisioning with shell script: /var/folders/h1/ymh5bdx15wgdn5hvgj1wc0zh0000gn/T/packer-powershell-provisioner902510110
    azure-arm: #< CLIXML
    azure-arm:
    azure-arm: Success Restart Needed Exit Code      Feature Result
    azure-arm: ------- -------------- ---------      --------------
    azure-arm: True    No             Success        {Common HTTP Features, Default Document, D...
    azure-arm: <Objs Version=“1.1.0.1” xmlns=“http://schemas.microsoft.com/powershell/2004/04"><Obj S=“progress” RefId=“0"><TN RefId=“0”><T>System.Management.Automation.PSCustomObject</T><T>System.Object</T></TN><MS><I64 N=“SourceId”>1</I64><PR N=“Record”><AV>Preparing modules for first use.</AV><AI>0</AI><Nil /><PI>-1</PI><PC>-1</PC><T>Completed</T><SR>-1</SR><SD> </SD></PR></MS></Obj></Objs>
==> azure-arm: Querying the machine’s properties ...
==> azure-arm:  -> ResourceGroupName : ‘packer-Resource-Group-pq0mthtbtt’
==> azure-arm:  -> ComputeName       : ‘pkrvmpq0mthtbtt’
==> azure-arm:  -> Managed OS Disk   : ‘/subscriptions/guid/resourceGroups/packer-Resource-Group-pq0mthtbtt/providers/Microsoft.Compute/disks/osdisk’
==> azure-arm: Powering off machine ...
==> azure-arm:  -> ResourceGroupName : ‘packer-Resource-Group-pq0mthtbtt’
==> azure-arm:  -> ComputeName       : ‘pkrvmpq0mthtbtt’
==> azure-arm: Capturing image ...
==> azure-arm:  -> Compute ResourceGroupName : ‘packer-Resource-Group-pq0mthtbtt’
==> azure-arm:  -> Compute Name              : ‘pkrvmpq0mthtbtt’
==> azure-arm:  -> Compute Location          : ‘East US’
==> azure-arm:  -> Image ResourceGroupName   : ‘myResourceGroup’
==> azure-arm:  -> Image Name                : ‘myPackerImage’
==> azure-arm:  -> Image Location            : ‘eastus’
==> azure-arm: Deleting resource group ...
==> azure-arm:  -> ResourceGroupName : ‘packer-Resource-Group-pq0mthtbtt’
==> azure-arm: Deleting the temporary OS disk ...
==> azure-arm:  -> OS Disk : skipping, managed disk was used...
Build ‘azure-arm’ finished.

==> Builds finished. The artifacts of successful builds are:
--> azure-arm: Azure.ResourceManagement.VMImage:

ManagedImageResourceGroupName: myResourceGroup
ManagedImageName: myPackerImage
ManagedImageLocation: eastus
```

Zabere několik minut, než balíček sestaví virtuální počítač, spustí zřizování a vyčistí nasazení.


## <a name="create-a-vm-from-the-packer-image"></a>Vytvoření virtuálního počítače z image balíčku
Nyní můžete vytvořit virtuální počítač z Image pomocí [New-AzVM](/powershell/module/az.compute/new-azvm). Podpůrné síťové prostředky se vytvoří, pokud ještě neexistují. Po zobrazení výzvy zadejte uživatelské jméno a heslo pro správu, které se má vytvořit na virtuálním počítači. Následující příklad vytvoří virtuální počítač s názvem *myVM* z *myPackerImage*:

```powershell
New-AzVm `
    -ResourceGroupName $rgName `
    -Name "myVM" `
    -Location $location `
    -VirtualNetworkName "myVnet" `
    -SubnetName "mySubnet" `
    -SecurityGroupName "myNetworkSecurityGroup" `
    -PublicIpAddressName "myPublicIpAddress" `
    -OpenPorts 80 `
    -Image "myPackerImage"
```

Pokud chcete vytvořit virtuální počítače v jiné skupině prostředků nebo v jiné oblasti, než je image vašeho balíčku, zadejte ID image, nikoli název bitové kopie. ID image můžete získat pomocí [Get-AzImage](/powershell/module/az.compute/get-azimage).

Vytvoření virtuálního počítače z image balíčku trvá několik minut.


## <a name="test-vm-and-webserver"></a>Testovací virtuální počítač a Server
Získejte veřejnou IP adresu vašeho virtuálního počítače pomocí [Get-AzPublicIPAddress](/powershell/module/az.network/get-azpublicipaddress). Následující příklad získá dříve vytvořenou IP adresu *myPublicIP*:

```powershell
Get-AzPublicIPAddress `
    -ResourceGroupName $rgName `
    -Name "myPublicIPAddress" | select "IpAddress"
```

Pokud se chcete podívat na virtuální počítač, který zahrnuje instalaci IIS ze zřizovacího balíčku, v akci zadejte veřejnou IP adresu do webového prohlížeče.

![Výchozí web služby IIS](./media/build-image-with-packer/iis.png) 


## <a name="next-steps"></a>Další kroky
Pomocí nástroje [Azure image Builder](image-builder.md)můžete také používat existující skripty pro sestavovatele balíčku.
