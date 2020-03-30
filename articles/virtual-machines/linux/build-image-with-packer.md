---
title: Vytváření bitových kopií virtuálních počítačů Linux Azure pomocí Packeru
description: Naučte se používat Packer k vytváření image virtuálních počítačů s Linuxem v Azure
author: cynthn
ms.service: virtual-machines-linux
ms.topic: article
ms.workload: infrastructure
ms.date: 05/07/2019
ms.author: cynthn
ms.openlocfilehash: 3aec50b8c8f2033b7340bde15ea7670c1a0b6bb9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79534215"
---
# <a name="how-to-use-packer-to-create-linux-virtual-machine-images-in-azure"></a>Jak používat Packer k vytváření ibi virtuálních automatů linuxu v Azure
Každý virtuální počítač (VM) v Azure se vytvoří z image, která definuje distribuci Linuxu a verzi operačního systému. Bitové kopie mohou obsahovat předinstalované aplikace a konfigurace. Azure Marketplace poskytuje mnoho ihráží první a třetí strany pro většinu běžných distribucí a aplikačních prostředí, nebo můžete vytvořit vlastní image přizpůsobené vašim potřebám. Tento článek podrobně popisuje, jak pomocí nástroje s otevřeným zdrojovým kódem [Packer](https://www.packer.io/) definovat a vytvářet vlastní image v Azure.

> [!NOTE]
> Azure teď má službu Azure Image Builder (preview) pro definování a vytváření vlastních bitových kopií. Azure Image Builder je založený na Packeru, takže s ním můžete dokonce použít stávající skripty pro zřizování prostředí Packer. Pokud chcete začít pracovat s Azure Image Builder, najdete [v tématu Vytvoření virtuálního počítače s Linuxem s Azure Image Builder](image-builder.md).


## <a name="create-azure-resource-group"></a>Vytvoření skupiny prostředků Azure
Během procesu sestavení Packer vytvoří dočasné prostředky Azure při vytváření zdrojového virtuálního počítače. To capture that source VM for use as an image, you must define a resource group. Výstup z procesu sestavení Packer je uložen v této skupině prostředků.

Vytvořte skupinu prostředků pomocí příkazu [az group create](/cli/azure/group). Následující příklad vytvoří skupinu prostředků s názvem *myResourceGroup* v umístění *eastus:*

```azurecli
az group create -n myResourceGroup -l eastus
```


## <a name="create-azure-credentials"></a>Vytvoření přihlašovacích údajů Azure
Packer se ověřuje pomocí Azure pomocí instančního objektu. Instanční objekt Služby Azure je identita zabezpečení, kterou můžete používat s aplikacemi, službami a automatizačními nástroji, jako je Packer. Řídíte a definujete oprávnění, jaké operace může instanční objekt v Azure provádět.

Vytvořte instanční objekt s [az az ad sp create-for-rbac](/cli/azure/ad/sp) a vyjádřete pověření, která Packer potřebuje:

```azurecli
az ad sp create-for-rbac --query "{ client_id: appId, client_secret: password, tenant_id: tenant }"
```

Příklad výstupu z předchozích příkazů je následující:

```output
{
    "client_id": "f5b6a5cf-fbdf-4a9f-b3b8-3c2cd00225a4",
    "client_secret": "0e760437-bf34-4aad-9f8d-870be799c55d",
    "tenant_id": "72f988bf-86f1-41af-91ab-2d7cd011db47"
}
```

K ověření azure, musíte také získat id předplatného Azure s [az účet zobrazit:](/cli/azure/account)

```azurecli
az account show --query "{ subscription_id: id }"
```

Výstup z těchto dvou příkazů použijete v dalším kroku.


## <a name="define-packer-template"></a>Definovat šablonu packeru
Chcete-li vytvořit obrázky, vytvořte šablonu jako soubor JSON. V šabloně definujete tvůrce a zřazeče, kteří provádějí proces skutečného sestavení. Packer má [zřazení pro Azure,](https://www.packer.io/docs/builders/azure.html) který umožňuje definovat prostředky Azure, jako jsou přihlašovací údaje instančního objektu vytvořené v předchozím kroku.

Vytvořte soubor s názvem *ubuntu.json* a vložte následující obsah. Zadejte své vlastní hodnoty pro následující:

| Parametr                           | Kde získat |
|-------------------------------------|----------------------------------------------------|
| *client_id*                         | První řádek výstupu `az ad sp` z příkazu create - *appId* |
| *client_secret*                     | Druhý řádek výstupu `az ad sp` z příkazu create - *heslo* |
| *tenant_id*                         | Třetí řádek výstupu `az ad sp` z příkazu create - *tenant* |
| *subscription_id*                   | Výstup `az account show` z příkazu |
| *managed_image_resource_group_name* | Název skupiny prostředků, kterou jste vytvořili v prvním kroku |
| *managed_image_name*                | Název vytvořené bitové kopie spravovaného disku |


```json
{
  "builders": [{
    "type": "azure-arm",

    "client_id": "f5b6a5cf-fbdf-4a9f-b3b8-3c2cd00225a4",
    "client_secret": "0e760437-bf34-4aad-9f8d-870be799c55d",
    "tenant_id": "72f988bf-86f1-41af-91ab-2d7cd011db47",
    "subscription_id": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx",

    "managed_image_resource_group_name": "myResourceGroup",
    "managed_image_name": "myPackerImage",

    "os_type": "Linux",
    "image_publisher": "Canonical",
    "image_offer": "UbuntuServer",
    "image_sku": "16.04-LTS",

    "azure_tags": {
        "dept": "Engineering",
        "task": "Image deployment"
    },

    "location": "East US",
    "vm_size": "Standard_DS2_v2"
  }],
  "provisioners": [{
    "execute_command": "chmod +x {{ .Path }}; {{ .Vars }} sudo -E sh '{{ .Path }}'",
    "inline": [
      "apt-get update",
      "apt-get upgrade -y",
      "apt-get -y install nginx",

      "/usr/sbin/waagent -force -deprovision+user && export HISTSIZE=0 && sync"
    ],
    "inline_shebang": "/bin/sh -x",
    "type": "shell"
  }]
}
```

Tato šablona vytvoří bitovou kopii Ubuntu 16.04 LTS, nainstaluje NGINX a pak zřídí virtuální ms.

> [!NOTE]
> Pokud rozbalíte na této šabloně zřídit pověření uživatele, upravte příkaz `-deprovision` provisioner, který deprovisions agentazure číst spíše než `deprovision+user`.
> Příznak `+user` odebere všechny uživatelské účty ze zdrojového virtuálního účtu.


## <a name="build-packer-image"></a>Vytvořit obrázek packeru
Pokud ještě nemáte packer nainstalovaný v místním počítači, [postupujte podle pokynů k instalaci packeru](https://www.packer.io/docs/install/index.html).

Vytvořte obrázek zadáním souboru šablony Packer takto:

```bash
./packer build ubuntu.json
```

Příklad výstupu z předchozích příkazů je následující:

```output
azure-arm output will be in this color.

==> azure-arm: Running builder ...
    azure-arm: Creating Azure Resource Manager (ARM) client ...
==> azure-arm: Creating resource group ...
==> azure-arm:  -> ResourceGroupName : ‘packer-Resource-Group-swtxmqm7ly’
==> azure-arm:  -> Location          : ‘East US’
==> azure-arm:  -> Tags              :
==> azure-arm:  ->> dept : Engineering
==> azure-arm:  ->> task : Image deployment
==> azure-arm: Validating deployment template ...
==> azure-arm:  -> ResourceGroupName : ‘packer-Resource-Group-swtxmqm7ly’
==> azure-arm:  -> DeploymentName    : ‘pkrdpswtxmqm7ly’
==> azure-arm: Deploying deployment template ...
==> azure-arm:  -> ResourceGroupName : ‘packer-Resource-Group-swtxmqm7ly’
==> azure-arm:  -> DeploymentName    : ‘pkrdpswtxmqm7ly’
==> azure-arm: Getting the VM’s IP address ...
==> azure-arm:  -> ResourceGroupName   : ‘packer-Resource-Group-swtxmqm7ly’
==> azure-arm:  -> PublicIPAddressName : ‘packerPublicIP’
==> azure-arm:  -> NicName             : ‘packerNic’
==> azure-arm:  -> Network Connection  : ‘PublicEndpoint’
==> azure-arm:  -> IP Address          : ‘40.76.218.147’
==> azure-arm: Waiting for SSH to become available...
==> azure-arm: Connected to SSH!
==> azure-arm: Provisioning with shell script: /var/folders/h1/ymh5bdx15wgdn5hvgj1wc0zh0000gn/T/packer-shell868574263
    azure-arm: WARNING! The waagent service will be stopped.
    azure-arm: WARNING! Cached DHCP leases will be deleted.
    azure-arm: WARNING! root password will be disabled. You will not be able to login as root.
    azure-arm: WARNING! /etc/resolvconf/resolv.conf.d/tail and /etc/resolvconf/resolv.conf.d/original will be deleted.
    azure-arm: WARNING! packer account and entire home directory will be deleted.
==> azure-arm: Querying the machine’s properties ...
==> azure-arm:  -> ResourceGroupName : ‘packer-Resource-Group-swtxmqm7ly’
==> azure-arm:  -> ComputeName       : ‘pkrvmswtxmqm7ly’
==> azure-arm:  -> Managed OS Disk   : ‘/subscriptions/guid/resourceGroups/packer-Resource-Group-swtxmqm7ly/providers/Microsoft.Compute/disks/osdisk’
==> azure-arm: Powering off machine ...
==> azure-arm:  -> ResourceGroupName : ‘packer-Resource-Group-swtxmqm7ly’
==> azure-arm:  -> ComputeName       : ‘pkrvmswtxmqm7ly’
==> azure-arm: Capturing image ...
==> azure-arm:  -> Compute ResourceGroupName : ‘packer-Resource-Group-swtxmqm7ly’
==> azure-arm:  -> Compute Name              : ‘pkrvmswtxmqm7ly’
==> azure-arm:  -> Compute Location          : ‘East US’
==> azure-arm:  -> Image ResourceGroupName   : ‘myResourceGroup’
==> azure-arm:  -> Image Name                : ‘myPackerImage’
==> azure-arm:  -> Image Location            : ‘eastus’
==> azure-arm: Deleting resource group ...
==> azure-arm:  -> ResourceGroupName : ‘packer-Resource-Group-swtxmqm7ly’
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


## <a name="create-vm-from-azure-image"></a>Vytvoření virtuálního počítače z image Azure
Nyní můžete vytvořit virtuální počítač z obrázku s [az vm vytvořit](/cli/azure/vm). Určete obrázek, `--image` který jste vytvořili s parametrem. Následující příklad vytvoří virtuální ho disponus názvem *myVM* z *myPackerImage* a generuje klíče SSH, pokud ještě neexistují:

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image myPackerImage \
    --admin-username azureuser \
    --generate-ssh-keys
```

Pokud chcete vytvořit virtuální počítače v jiné skupině prostředků nebo oblasti než obrázek Packer, zadejte ID obrázku spíše než název obrázku. ID obrázku můžete získat pomocí [az image show](/cli/azure/image#az-image-show).

Vytvoření virtuálního virtuálního mísy trvá několik minut. Po vytvoření virtuálního počítače, poznamenejte si `publicIpAddress` zobrazení v příkazovém příkazu Azure. Tato adresa slouží k přístupu na stránky NGINX prostřednictvím webového prohlížeče.

Pokud chcete umožnit přístup k virtuálnímu počítači webovému provozu, otevřete port 80 z internetu pomocí příkazu [az vm open-port](/cli/azure/vm):

```azurecli
az vm open-port \
    --resource-group myResourceGroup \
    --name myVM \
    --port 80
```

## <a name="test-vm-and-nginx"></a>Testování virtuálního stavitelství a NGINX
Nyní můžete otevřít webový prohlížeč a do adresního řádku zadat `http://publicIpAddress`. Zadejte vlastní veřejnou IP adresu získanou při vytváření virtuálního počítače. Výchozí stránka NGINX je zobrazena jako v následujícím příkladu:

![Výchozí web NGINX](./media/build-image-with-packer/nginx.png) 


## <a name="next-steps"></a>Další kroky
Můžete také použít existující skripty Packer zřizovací ho s [Azure Image Builder](image-builder.md).
