---
title: Vytvoření Image virtuálních počítačů Azure Linux s balírna | Microsoft Docs
description: Další informace o použití balírna k vytvoření bitové kopie virtuálních počítačích s Linuxem v Azure
services: virtual-machines-linux
documentationcenter: virtual-machines
author: iainfoulds
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/03/2018
ms.author: iainfou
ms.openlocfilehash: 7d7ba6a493cca3dd14829e6527136af6df424c05
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/07/2018
ms.locfileid: "33778180"
---
# <a name="how-to-use-packer-to-create-linux-virtual-machine-images-in-azure"></a>Jak používat balírna k vytvoření bitové kopie virtuálních počítačů Linux v Azure
Každý virtuální počítač (VM) v Azure je vytvořený z image, která definuje distribuci systému Linux a verzi operačního systému. Bitové kopie může zahrnovat předinstalované aplikace a konfigurace. Azure Marketplace poskytuje celou řadu imagí první a třetí strany pro aplikaci v prostředích a nejběžnější distribuce, nebo můžete vytvořit vlastní vlastních bitových kopií přizpůsobit svým potřebám. Tento článek popisuje, jak používat nástroj open source [balírna](https://www.packer.io/) definovat a vytvářet vlastní bitové kopie v Azure.


## <a name="create-azure-resource-group"></a>Vytvoření skupiny prostředků Azure.
Během procesu vytváření balírna vytvoří dočasný prostředky Azure, jako sestavuje zdrojového virtuálního počítače. Když Pokud chcete zachytit tohoto zdrojového virtuálního počítače pro použití jako bitovou kopii, je nutné zadat skupinu prostředků. Výstup z procesu sestavení balírna je uložený v této skupině prostředků.

Vytvořte skupinu prostředků pomocí příkazu [az group create](/cli/azure/group#az_group_create). Následující příklad vytvoří skupinu prostředků *myResourceGroup* v umístění *eastus*:

```azurecli
az group create -n myResourceGroup -l eastus
```


## <a name="create-azure-credentials"></a>Vytvořit přihlašovací údaje Azure
Balírna ověřuje s Azure pomocí objektu služby. Objektu zabezpečení služby Azure je identita zabezpečení, která můžete použít s aplikací, služeb a automatizace nástroje, například balírna. Můžete řídit a definovat oprávnění, jaké operace objektu služby můžete provádět v Azure.

Vytvoření služby hlavní s [az ad sp vytvořit pro rbac](/cli/azure/ad/sp#create-for-rbac) a výstupní přihlašovací údaje, které potřebuje balírna:

```azurecli
az ad sp create-for-rbac --query "{ client_id: appId, client_secret: password, tenant_id: tenant }"
```

Příklad výstupu z předchozích příkazů vypadá takto:

```azurecli
{
    "client_id": "f5b6a5cf-fbdf-4a9f-b3b8-3c2cd00225a4",
    "client_secret": "0e760437-bf34-4aad-9f8d-870be799c55d",
    "tenant_id": "72f988bf-86f1-41af-91ab-2d7cd011db47"
}
```

K ověření do Azure, musíte také získat svoje ID předplatného Azure s [az účet zobrazit](/cli/azure/account#az_account_show):

```azurecli
az account show --query "{ subscription_id: id }"
```

V dalším kroku použijete výstup z těchto dvou příkazů.


## <a name="define-packer-template"></a>Definovat balírna šablonu
K vytvoření bitové kopie, vytvořit šablonu jako soubor ve formátu JSON. V šabloně definujete tvůrce a provisioners, které provádějí procesu skutečné sestavení. Má balírna [zajištění webu pro Azure](https://www.packer.io/docs/builders/azure.html) , což vám umožní definovat prostředky Azure, jako jsou hlavní přihlašovací údaje služby vytvořili v předchozím kroku.

Vytvořte soubor s názvem *ubuntu.json* a vložte následující obsah. Zadejte vlastní hodnoty pro následující:

| Parametr                           | Kde můžete získat |
|-------------------------------------|----------------------------------------------------|
| *client_id*                         | První řádek výstupu z `az ad sp` vytvoření příkazu - *appId* |
| *client_secret*                     | Druhý řádek výstupu z `az ad sp` vytvoření příkazu - *heslo* |
| *tenant_id*                         | Třetí řádek výstupu z `az ad sp` vytvoření příkazu - *klienta* |
| *subscription_id*                   | Výstup z `az account show` příkaz |
| *managed_image_resource_group_name* | Název skupiny prostředků, kterou jste vytvořili v prvním kroku |
| *managed_image_name*                | Název bitové kopie spravovaného disku, který je vytvořen |


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

Tato šablona vytvoří bitovou kopii Ubuntu 16.04 LTS, nainstaluje NGINX a pak deprovisions virtuálního počítače.

> [!NOTE]
> Pokud rozbalíte na šabloně této zřízení uživatelských přihlašovacích údajů, upravte příkaz zajištění webu, který deprovisions agent Azure číst `-deprovision` místo `deprovision+user`.
> `+user` Příznak odebere všechny uživatelské účty ze zdrojového virtuálního počítače.


## <a name="build-packer-image"></a>Sestavení balírna bitové kopie
Pokud ještě nemáte balírna nainstalována na místním počítači, [postupujte podle pokynů pro instalaci balírna](https://www.packer.io/docs/install/index.html).

Vytvořit bitovou kopii zadáním vaší balírna soubor šablony následujícím způsobem:

```bash
./packer build ubuntu.json
```

Příklad výstupu z předchozích příkazů vypadá takto:

```bash
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

Jak dlouho trvá několik minut, než balírna k vytvoření virtuálního počítače, spusťte provisioners a vyčistit nasazení.


## <a name="create-vm-from-azure-image"></a>Vytvoření virtuálního počítače z Azure Image
Nyní můžete vytvořit virtuální počítač z bitové kopie s [vytvořit virtuální počítač az](/cli/azure/vm#az_vm_create). Určuje obrázek, který jste vytvořili pomocí `--image` parametr. Následující příklad vytvoří virtuální počítač s názvem *Můjvp* z *myPackerImage* a generuje klíče SSH, pokud už neexistují:

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image myPackerImage \
    --admin-username azureuser \
    --generate-ssh-keys
```

Pokud chcete vytvořit virtuální počítače v jiné skupině prostředků nebo oblasti než balírna image, zadejte ID bitové kopie místo název bitové kopie. Můžete získat ID bitové kopie s [az image zobrazit](/cli/azure/image#az-image-show).

Jak dlouho trvá několik minut pro vytvoření virtuálního počítače. Po vytvoření virtuálního počítače, poznamenejte si `publicIpAddress` zobrazí pomocí rozhraní příkazového řádku Azure. Tato adresa se používá pro přístup k webu NGINX prostřednictvím webového prohlížeče.

Pokud chcete umožnit přístup k virtuálnímu počítači webovému provozu, otevřete port 80 z internetu pomocí příkazu [az vm open-port](/cli/azure/vm#open-port):

```azurecli
az vm open-port \
    --resource-group myResourceGroup \
    --name myVM \
    --port 80
```

## <a name="test-vm-and-nginx"></a>Testovací virtuální počítač a NGINX
Nyní můžete otevřít webový prohlížeč a do adresního řádku zadat `http://publicIpAddress`. Zadejte vlastní veřejnou IP adresu získanou při vytváření virtuálního počítače. Výchozí NGINX stránky se zobrazí jako v následujícím příkladu:

![Výchozí web NGINX](./media/build-image-with-packer/nginx.png) 


## <a name="next-steps"></a>Další postup
V tomto příkladu jste použili balírna k vytvoření image virtuálního počítače s NGINX již nainstalován. Můžete tuto bitovou kopii virtuálního počítače spolu s existující pracovní postupy nasazení, například k nasazení vaší aplikace na virtuální počítače vytvořené z bitové kopie s Ansible, Chef nebo Puppet.

Další příklad šablony balírna pro ostatní distribucích systému Linux, najdete v části [toto úložiště GitHub](https://github.com/hashicorp/packer/tree/master/examples/azure).