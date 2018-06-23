---
title: Vytvoření bitové kopie virtuálního počítače Windows Azure s balírna | Microsoft Docs
description: Další informace o použití balírna k vytvoření bitové kopie virtuálních počítačů s Windows v Azure
services: virtual-machines-windows
documentationcenter: virtual-machines
author: iainfoulds
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 03/29/2018
ms.author: iainfou
ms.openlocfilehash: b7f07ec8736086483f91746512f10118ee90762d
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2018
ms.locfileid: "36333153"
---
# <a name="how-to-use-packer-to-create-windows-virtual-machine-images-in-azure"></a>Jak používat balírna k vytváření bitových kopií systému Windows virtuálního počítače v Azure
Každý virtuální počítač (VM) v Azure je vytvořený z image, která definuje distribuci systému Windows a verze operačního systému. Bitové kopie může zahrnovat předinstalované aplikace a konfigurace. Azure Marketplace poskytuje celou řadu imagí první a třetí strany pro nejběžnější operačního systému a aplikací prostředí, nebo můžete vytvořit vlastní vlastních bitových kopií přizpůsobit svým potřebám. Tento článek popisuje, jak používat nástroj open-source [balírna](https://www.packer.io/) definovat a vytvářet vlastní bitové kopie v Azure.


## <a name="create-azure-resource-group"></a>Vytvoření skupiny prostředků Azure.
Během procesu vytváření balírna vytvoří dočasný prostředky Azure, jako sestavuje zdrojového virtuálního počítače. Když Pokud chcete zachytit tohoto zdrojového virtuálního počítače pro použití jako bitovou kopii, je nutné zadat skupinu prostředků. Výstup z procesu sestavení balírna je uložený v této skupině prostředků.

Vytvořte skupinu prostředků pomocí rutiny [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup). Následující příklad vytvoří skupinu prostředků *myResourceGroup* v umístění *eastus*:

```powershell
$rgName = "myResourceGroup"
$location = "East US"
New-AzureRmResourceGroup -Name $rgName -Location $location
```

## <a name="create-azure-credentials"></a>Vytvořit přihlašovací údaje Azure
Balírna ověřuje s Azure pomocí objektu služby. Objektu zabezpečení služby Azure je identita zabezpečení, která můžete použít s aplikací, služeb a automatizace nástroje, například balírna. Můžete řídit a definovat oprávnění, jaké operace objektu služby můžete provádět v Azure.

Vytvoření služby hlavní s [New-AzureRmADServicePrincipal](/powershell/module/azurerm.resources/new-azurermadserviceprincipal) a přiřaďte oprávnění pro objekt služby vytvořit a spravovat prostředky s [New-AzureRmRoleAssignment](/powershell/module/azurerm.resources/new-azurermroleassignment):

```powershell
$sp = New-AzureRmADServicePrincipal -DisplayName "AzurePacker" `
    -Password (ConvertTo-SecureString "P@ssw0rd!" -AsPlainText -Force)
Sleep 20
New-AzureRmRoleAssignment -RoleDefinitionName Contributor -ServicePrincipalName $sp.ApplicationId
```

K ověření do Azure, musíte také získat vaše Azure ID klienta a předplatné s [Get-AzureRmSubscription](/powershell/module/azurerm.profile/get-azurermsubscription):

```powershell
$sub = Get-AzureRmSubscription
$sub.TenantId[0]
$sub.SubscriptionId[0]
```

V dalším kroku použijete tyto dva identifikátory ID.


## <a name="define-packer-template"></a>Definovat balírna šablonu
K vytvoření bitové kopie, vytvořit šablonu jako soubor ve formátu JSON. V šabloně definujete tvůrce a provisioners, které provádějí procesu skutečné sestavení. Má balírna [tvůrce pro Azure](https://www.packer.io/docs/builders/azure.html) , což vám umožní definovat prostředky Azure, jako jsou hlavní přihlašovací údaje služby vytvořili v předchozím kroku.

Vytvořte soubor s názvem *windows.json* a vložte následující obsah. Zadejte vlastní hodnoty pro následující:

| Parametr                           | Kde můžete získat |
|-------------------------------------|----------------------------------------------------|
| *client_id*                         | Zobrazení ID objektu zabezpečení služby s `$sp.applicationId` |
| *client_secret*                     | Heslo, které jste zadali v `$securePassword` |
| *tenant_id*                         | Výstup z `$sub.TenantId` příkaz |
| *subscription_id*                   | Výstup z `$sub.SubscriptionId` příkaz |
| *object_id*                         | ID objektu zabezpečení služby zobrazení s `$sp.Id` |
| *managed_image_resource_group_name* | Název skupiny prostředků, kterou jste vytvořili v prvním kroku |
| *managed_image_name*                | Název bitové kopie spravovaného disku, který je vytvořen |

```json
{
  "builders": [{
    "type": "azure-arm",

    "client_id": "0831b578-8ab6-40b9-a581-9a880a94aab1",
    "client_secret": "P@ssw0rd!",
    "tenant_id": "72f988bf-86f1-41af-91ab-2d7cd011db47",
    "subscription_id": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx",
    "object_id": "a7dfb070-0d5b-47ac-b9a5-cf214fff0ae2",

    "managed_image_resource_group_name": "myResourceGroup",
    "managed_image_name": "myPackerImage",

    "os_type": "Windows",
    "image_publisher": "MicrosoftWindowsServer",
    "image_offer": "WindowsServer",
    "image_sku": "2016-Datacenter",

    "communicator": "winrm",
    "winrm_use_ssl": true,
    "winrm_insecure": true,
    "winrm_timeout": "3m",
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

Tato šablona vytvoří virtuální počítač s Windows Server 2016, nainstaluje službu IIS a pak umožňuje zobecnit virtuální počítač pomocí nástroje Sysprep. Instalace služby IIS ukazuje, jak lze pomocí zajištění webu prostředí PowerShell spustit další příkazy. Finální image balírna potom zahrnuje instalace požadovaného softwaru a konfigurace.


## <a name="build-packer-image"></a>Sestavení balírna bitové kopie
Pokud ještě nemáte balírna nainstalována na místním počítači, [postupujte podle pokynů pro instalaci balírna](https://www.packer.io/docs/install/index.html).

Vytvořit bitovou kopii zadáním vaší balírna soubor šablony následujícím způsobem:

```bash
./packer build windows.json
```

Příklad výstupu z předchozích příkazů vypadá takto:

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

Jak dlouho trvá několik minut, než balírna k vytvoření virtuálního počítače, spusťte provisioners a vyčistit nasazení.


## <a name="create-a-vm-from-the-packer-image"></a>Vytvořte virtuální počítač z bitové kopie balírna
Nyní můžete vytvořit virtuální počítač z bitové kopie s [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm). Doprovodné materiály sítě se vytvoří, pokud dosud neexistují. Po zobrazení výzvy zadejte správce uživatelské jméno a heslo vytvořit na virtuálním počítači. Následující příklad vytvoří virtuální počítač s názvem *Můjvp* z *myPackerImage*:

```powershell
New-AzureRmVm `
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

Pokud chcete vytvořit virtuální počítače v jiné skupině prostředků nebo oblasti než balírna image, zadejte ID bitové kopie místo název bitové kopie. Můžete získat ID bitové kopie s [Get-AzureRmImage](/powershell/module/AzureRM.Compute/Get-AzureRmImage).

Trvá několik minut vytvořit virtuální počítač z bitové kopie balírna.


## <a name="test-vm-and-webserver"></a>Testovací virtuální počítač a webový server
Získejte veřejnou IP adresu virtuálního počítače pomocí rutiny [Get-AzureRmPublicIPAddress](/powershell/module/azurerm.network/get-azurermpublicipaddress). Následující příklad získá dříve vytvořenou IP adresu pro *myPublicIP*:

```powershell
Get-AzureRmPublicIPAddress `
    -ResourceGroupName $rgName `
    -Name "myPublicIPAddress" | select "IpAddress"
```

Zobrazíte virtuálního počítače, který zahrnuje instalace služby IIS z zajištění balírna webu, v akci, zadejte veřejnou IP adresu v do webového prohlížeče.

![Výchozí web služby IIS](./media/build-image-with-packer/iis.png) 


## <a name="next-steps"></a>Další postup
V tomto příkladu jste použili balírna k vytvoření image virtuálního počítače s již nainstalovanou službu IIS. Můžete tuto bitovou kopii virtuálního počítače spolu s existující pracovní postupy nasazení, například k nasazení vaší aplikace na virtuální počítače vytvořené z bitové kopie s Team Services, Ansible, Chef nebo Puppet.

Další příklad šablony balírna pro ostatní distribucích systému Windows, najdete v části [toto úložiště GitHub](https://github.com/hashicorp/packer/tree/master/examples/azure).
