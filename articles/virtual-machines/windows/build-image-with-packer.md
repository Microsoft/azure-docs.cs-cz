---
title: Jak vytvořit Image virtuálních počítačů s Windows pomocí Packeru v Azure | Dokumentace Microsoftu
description: Zjistěte, jak vytvořit Image virtuálního počítače Windows v Azure pomocí Packeru
services: virtual-machines-windows
documentationcenter: virtual-machines
author: cynthn
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 02/22/2019
ms.author: cynthn
ms.openlocfilehash: 81dbd8082d5a7ab473cc0cbe5fcb6e564fbd750c
ms.sourcegitcommit: 24fd3f9de6c73b01b0cee3bcd587c267898cbbee
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/20/2019
ms.locfileid: "65951131"
---
# <a name="how-to-use-packer-to-create-windows-virtual-machine-images-in-azure"></a>Jak vytvořit Image virtuálních počítačů s Windows v Azure pomocí Packeru
Každý virtuální počítač (VM) v Azure je vytvořený z image, která definuje Windows distribuce a verze operačního systému. Image můžete zahrnout předinstalované aplikace a konfigurace. Na webu Azure Marketplace obsahuje celou řadu imagí první a třetí strany pro nejběžnější operační systém a prostředí aplikace, nebo můžete vytvořit vlastní Image přizpůsobené vašim potřebám. Tento článek podrobně popisuje, jak používat open source nástroj [Packeru](https://www.packer.io/) k definování a vytvoření vlastních imagí v Azure.

Tento článek byl testován poslední o 2/21/2019 používání [modulu Powershellu pro Az](https://docs.microsoft.com/powershell/azure/install-az-ps) verze 1.3.0 a [Packeru](https://www.packer.io/docs/install/index.html) verze 1.3.4.

> [!NOTE]
> Azure teď má službu, Image Builder pro Azure (preview) pro definování a vytvoření vlastní Image. Image Builder pro Azure je postavený na Packeru, takže se dá dokonce využít vaše stávající skripty prostředí zajištění webu Packeru s ním. Začínáme s Azure Image Builder, najdete v článku [vytvoření virtuálního počítače s Windows pomocí Azure Image Builder](image-builder.md).

## <a name="create-azure-resource-group"></a>Vytvoření skupiny prostředků Azure
Během procesu sestavování vytvoří Packeru dočasné prostředky Azure jako sestavení zdrojového virtuálního počítače. K zachycení této zdrojový virtuální počítač pro použití jako image, je nutné definovat skupinu prostředků. V této skupině prostředků se ukládá výstup z procesu sestavení Packeru.

Vytvořte skupinu prostředků s [New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup). Následující příklad vytvoří skupinu prostředků *myResourceGroup* v umístění *eastus*:

```azurepowershell
$rgName = "mypackerGroup"
$location = "East US"
New-AzResourceGroup -Name $rgName -Location $location
```

## <a name="create-azure-credentials"></a>Vytvoření přihlašovacích údajů Azure
Packeru se ověřuje pomocí Azure s využitím instančního objektu. Instanční objekt Azure je identita zabezpečení, který vám pomůže s aplikací, služeb a nástrojů automatizace, jako je Packeru. Kontrolou a můžete definovat oprávnění, jaké operace lze provádět instanční objekt služby v Azure.

Vytvořit instanční objekt s [New-AzADServicePrincipal](https://docs.microsoft.com/powershell/module/az.resources/new-azadserviceprincipal) a přiřadit oprávnění pro instanční objekt pro vytváření a správě prostředků s [New-AzRoleAssignment](https://docs.microsoft.com/powershell/module/az.resources/new-azroleassignment). Hodnota pro `-DisplayName` musí být jedinečný; nahraďte vlastní hodnotu, podle potřeby.  

```azurepowershell
$sp = New-AzADServicePrincipal -DisplayName "PackerServicePrincipal"
$BSTR = [System.Runtime.InteropServices.Marshal]::SecureStringToBSTR($sp.Secret)
$plainPassword = [System.Runtime.InteropServices.Marshal]::PtrToStringAuto($BSTR)
New-AzRoleAssignment -RoleDefinitionName Contributor -ServicePrincipalName $sp.ApplicationId
```

Ve výstupu ID hesla a aplikace.

```powershell
$plainPassword
$sp.ApplicationId
```


Pro ověření do Azure, budete potřebovat získat vaše Azure ID tenanta a předplatné s [Get-AzSubscription](https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription):

```powershell
Get-AzSubscription
```


## <a name="define-packer-template"></a>Definice šablony Packeru
Pokud chcete vytvořit Image, vytvořit šablonu jako soubor JSON. V šabloně definujte tvůrci a provisioners, které vykonávají proces skutečné sestavení. Má packeru [tvůrce pro Azure](https://www.packer.io/docs/builders/azure.html) , který umožňuje definovat prostředky Azure, jako jsou pověření instančního objektu služby, vytvořili v předchozím kroku.

Vytvořte soubor s názvem *windows.json* a vložte následující obsah. Zadejte vlastní hodnoty pro následující:

| Parametr                           | Kde můžete získat |
|-------------------------------------|----------------------------------------------------|
| *client_id*                         | Zobrazení ID instančního objektu s `$sp.applicationId` |
| *client_secret*                     | Zobrazit automaticky vytvořené heslo s `$plainPassword` |
| *tenant_id*                         | Výstup z `$sub.TenantId` příkaz |
| *subscription_id*                   | Výstup z `$sub.SubscriptionId` příkaz |
| *managed_image_resource_group_name* | Název skupiny prostředků, kterou jste vytvořili v prvním kroku |
| *managed_image_name*                | Název bitové kopie spravovaného disku, který je vytvořen |

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

    "location": "East US",
    "vm_size": "Standard_DS2_v2"
  }],
  "provisioners": [{
    "type": "powershell",
    "inline": [
      "Add-WindowsFeature Web-Server",
      "& $env:SystemRoot\\System32\\Sysprep\\Sysprep.exe /oobe /generalize /quiet /quit",
      "while($true) { $imageState = Get-ItemProperty HKLM:\\SOFTWARE\\Microsoft\\Windows\\CurrentVersion\\Setup\\State | Select ImageState; if($imageState.ImageState -ne 'IMAGE_STATE_GENERALIZE_RESEAL_TO_OOBE') { Write-Output $imageState.ImageState; Start-Sleep -s 10  } else { break } }"
    ]
  }]
}
```

Tato šablona vytvoří virtuální počítač s Windows serverem 2016, nainstaluje službu IIS a pak umožňuje zobecnit virtuální počítač pomocí nástroje Sysprep. Instalace služby IIS ukazuje, jak můžete pomocí Powershellu zajištění webu spustit další příkazy. Finální image Packeru potom zahrnuje instalace požadovaného softwaru a konfigurace.


## <a name="build-packer-image"></a>Sestavení image Packeru
Pokud ještě nemáte nainstalované na místním počítači, Packeru [postupujte podle pokynů k instalaci Packeru](https://www.packer.io/docs/install/index.html).

Sestavení image tak, že otevřete příkazový řádek a zadání vašeho Packeru soubor šablony následujícím způsobem:

```
./packer build windows.json
```

Příklad výstupu z výše uvedených příkazů vypadá takto:

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

Trvá několik minut, než Packeru pro vytvoření virtuálního počítače, spusťte provisioners a vyčištění nasazení.


## <a name="create-a-vm-from-the-packer-image"></a>Vytvoření virtuálního počítače z image Packeru
Nyní můžete vytvořit virtuální počítač z bitové kopie s [rutiny New-AzVM](https://docs.microsoft.com/powershell/module/az.compute/new-azvm). Podpůrné síťové prostředky se vytvoří, pokud ještě neexistují. Po zobrazení výzvy zadejte uživatelské jméno správce a heslo má být vytvořen na virtuálním počítači. Následující příklad vytvoří virtuální počítač s názvem *myVM* z *myPackerImage*:

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

Pokud chcete vytvořit virtuální počítače v jiné skupině prostředků nebo oblasti než vaší image Packeru, zadejte ID bitové kopie nikoli název image. Můžete získat ID bitové kopie s [Get-AzImage](https://docs.microsoft.com/powershell/module/az.compute/Get-AzImage).

Trvá několik minut, vytvoření virtuálního počítače z image Packeru.


## <a name="test-vm-and-webserver"></a>Testovací virtuální počítač a webového serveru
Získat veřejnou IP adresu vašeho virtuálního počítače s [Get-AzPublicIPAddress](https://docs.microsoft.com/powershell/module/az.network/get-azpublicipaddress). Následující příklad získá dříve vytvořenou IP adresu pro *myPublicIP*:

```powershell
Get-AzPublicIPAddress `
    -ResourceGroupName $rgName `
    -Name "myPublicIPAddress" | select "IpAddress"
```

Pro virtuální počítač, který zahrnuje instalaci služby IIS z zajištění Packeru webu, v akci, zadejte veřejnou IP adresu do webového prohlížeče.

![Výchozí web služby IIS](./media/build-image-with-packer/iis.png) 


## <a name="next-steps"></a>Další postup
Můžete také použít existující skripty zajištění webu Packeru s [Azure Image Builder](image-builder.md).
