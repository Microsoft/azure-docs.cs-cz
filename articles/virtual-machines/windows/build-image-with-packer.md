---
title: Jak vytvořit image virtuálních virtuálních počítačů s Windows pomocí Packeru v Azure
description: Naučte se používat Packer k vytváření image virtuálních počítačů s Windows v Azure
services: virtual-machines-windows
documentationcenter: virtual-machines
author: cynthn
manager: gwallace
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 02/22/2019
ms.author: cynthn
ms.openlocfilehash: cb81cbb12605a9d4b8870aab4bb461c8af079cf5
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81460745"
---
# <a name="how-to-use-packer-to-create-windows-virtual-machine-images-in-azure"></a>Jak pomocí Packeru vytvářet ibi virtuálních strojů windows v Azure
Každý virtuální počítač (VM) v Azure se vytvoří z image, která definuje distribuci Windows a verzi operačního systému. Bitové kopie mohou obsahovat předinstalované aplikace a konfigurace. Azure Marketplace poskytuje mnoho ihráží první a třetí strany pro většinu běžných operačních prostředí a aplikačních prostředí, nebo můžete vytvořit vlastní image přizpůsobené vašim potřebám. Tento článek podrobně popisuje, jak pomocí nástroje s otevřeným zdrojovým kódem [Packer](https://www.packer.io/) definovat a vytvářet vlastní image v Azure.

Tento článek byl naposledy testován na 2/21/2019 pomocí [modulu Az PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps) verze 1.3.0 a [Packer](https://www.packer.io/docs/install/index.html) verze 1.3.4.

> [!NOTE]
> Azure teď má službu Azure Image Builder (preview) pro definování a vytváření vlastních bitových kopií. Azure Image Builder je založený na Packeru, takže s ním můžete dokonce použít stávající skripty pro zřizování prostředí Packer. Pokud chcete začít pracovat s Azure Image Builder, najdete [v tématu Vytvoření virtuálního počítače s Windows pomocí Azure Image Builder](image-builder.md).

## <a name="create-azure-resource-group"></a>Vytvoření skupiny prostředků Azure
Během procesu sestavení Packer vytvoří dočasné prostředky Azure při vytváření zdrojového virtuálního počítače. To capture that source VM for use as an image, you must define a resource group. Výstup z procesu sestavení Packer je uložen v této skupině prostředků.

Vytvořte skupinu prostředků pomocí [skupiny New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup). Následující příklad vytvoří skupinu prostředků s názvem *myResourceGroup* v umístění *eastus:*

```azurepowershell
$rgName = "myResourceGroup"
$location = "East US"
New-AzResourceGroup -Name $rgName -Location $location
```

## <a name="create-azure-credentials"></a>Vytvoření přihlašovacích údajů Azure
Packer se ověřuje pomocí Azure pomocí instančního objektu. Instanční objekt Služby Azure je identita zabezpečení, kterou můžete používat s aplikacemi, službami a automatizačními nástroji, jako je Packer. Řídíte a definujete oprávnění, jaké operace může instanční objekt v Azure provádět.

Vytvořte instanční objekt s [new-azadserviceprincipal](https://docs.microsoft.com/powershell/module/az.resources/new-azadserviceprincipal) a přiřaďte oprávnění pro instanční objekt k vytvoření a správě prostředků pomocí [new-azroleassignment](https://docs.microsoft.com/powershell/module/az.resources/new-azroleassignment). Hodnota pro `-DisplayName` musí být jedinečná; podle potřeby nahradit vlastní hodnotou.  

```azurepowershell
$sp = New-AzADServicePrincipal -DisplayName "PackerServicePrincipal"
$BSTR = [System.Runtime.InteropServices.Marshal]::SecureStringToBSTR($sp.Secret)
$plainPassword = [System.Runtime.InteropServices.Marshal]::PtrToStringAuto($BSTR)
New-AzRoleAssignment -RoleDefinitionName Contributor -ServicePrincipalName $sp.ApplicationId
```

Pak výstup heslo a ID aplikace.

```powershell
$plainPassword
$sp.ApplicationId
```


K ověření azure, musíte také získat váš klient Azure a ID předplatného s [Get-AzSubscription](https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription):

```powershell
Get-AzSubscription
```


## <a name="define-packer-template"></a>Definovat šablonu packeru
Chcete-li vytvořit obrázky, vytvořte šablonu jako soubor JSON. V šabloně definujete tvůrce a zřazeče, kteří provádějí proces skutečného sestavení. Packer má [tvůrce pro Azure,](https://www.packer.io/docs/builders/azure.html) který umožňuje definovat prostředky Azure, jako jsou přihlašovací údaje o instančníobjekty vytvořené v předchozím kroku.

Vytvořte soubor s názvem *windows.json* a vložte následující obsah. Zadejte své vlastní hodnoty pro následující:

| Parametr                           | Kde získat |
|-------------------------------------|----------------------------------------------------|
| *client_id*                         | Zobrazit ID instančního objektu s`$sp.applicationId` |
| *client_secret*                     | Zobrazení automaticky generovaného hesla pomocí`$plainPassword` |
| *tenant_id*                         | Výstup `$sub.TenantId` z příkazu |
| *subscription_id*                   | Výstup `$sub.SubscriptionId` z příkazu |
| *managed_image_resource_group_name* | Název skupiny prostředků, kterou jste vytvořili v prvním kroku |
| *managed_image_name*                | Název vytvořené bitové kopie spravovaného disku |

```json
{
  "builders": [{
    "type": "azure-arm",

    "client_id": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx",
    "client_secret": "ppppppp-pppp-pppp-pppp-ppppppppppp",
    "tenant_id": "zzzzzzz-zzzz-zzzz-zzzz-zzzzzzzzzzzz",
    "subscription_id": "yyyyyyy-yyyy-yyyy-yyyy-yyyyyyyyyyy",

    "managed_image_resource_group_name": "myResourceGroup",
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

Tato šablona vytvoří virtuální ms Windows Server 2016, nainstaluje službu IIS a pak zobecní virtuální ho s programem Sysprep. Instalace služby IIS ukazuje, jak můžete pomocí zřizovatelu prostředí PowerShell spustit další příkazy. Konečný obrázek Packer pak obsahuje požadovanou instalaci a konfiguraci softwaru.


## <a name="build-packer-image"></a>Vytvořit obrázek packeru
Pokud ještě nemáte packer nainstalovaný v místním počítači, [postupujte podle pokynů k instalaci packeru](https://www.packer.io/docs/install/index.html).

Vytvořte obrázek otevřením výzvy cmd a zadáním souboru šablony Packer takto:

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

Trvá několik minut pro Packer k sestavení virtuálního počítače, spuštění zřazovacích programů a vyčištění nasazení.


## <a name="create-a-vm-from-the-packer-image"></a>Vytvoření virtuálního virtuálního virtuálního <a) z bitové kopie Packer
Teď můžete vytvořit virtuální počítač z obrázku s [New-AzVM](https://docs.microsoft.com/powershell/module/az.compute/new-azvm). Podpůrné síťové prostředky jsou vytvořeny, pokud ještě neexistují. Po zobrazení výzvy zadejte uživatelské jméno pro správu a heslo, které chcete vytvořit na virtuálním počítači. Následující příklad vytvoří virtuální hod s názvem *myVM* z *myPackerImage*:

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

Pokud chcete vytvořit virtuální počítače v jiné skupině prostředků nebo oblasti než obrázek Packer, zadejte ID obrázku spíše než název obrázku. ID obrázku můžete získat pomocí [get-azimage](https://docs.microsoft.com/powershell/module/az.compute/Get-AzImage).

Vytvoření virtuálního počítače z obrázku Packer trvá několik minut.


## <a name="test-vm-and-webserver"></a>Testování virtuálního virtuálního připojení a webového serveru
Získejte veřejnou IP adresu virtuálního počítače pomocí [get-azpublicipaddress](https://docs.microsoft.com/powershell/module/az.network/get-azpublicipaddress). Následující příklad získá dříve vytvořenou IP adresu *myPublicIP*:

```powershell
Get-AzPublicIPAddress `
    -ResourceGroupName $rgName `
    -Name "myPublicIPAddress" | select "IpAddress"
```

Chcete-li zobrazit virtuální počítač, který zahrnuje instalaci služby IIS z rozvaděče Packer, v akci zadejte veřejnou IP adresu do webového prohlížeče.

![Výchozí web služby IIS](./media/build-image-with-packer/iis.png) 


## <a name="next-steps"></a>Další kroky
Můžete také použít existující skripty Packer zřizovací ho s [Azure Image Builder](image-builder.md).
