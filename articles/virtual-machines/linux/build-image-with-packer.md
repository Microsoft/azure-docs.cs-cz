---
title: Jak vytvořit Linuxové Image virtuálních počítačů Azure pomocí Packeru | Dokumentace Microsoftu
description: Zjistěte, jak vytvořit Image virtuálních počítačů s Linuxem v Azure pomocí Packeru
services: virtual-machines-linux
documentationcenter: virtual-machines
author: cynthn
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
ms.author: cynthn
ms.openlocfilehash: 3a7ac2e7a86a135f20f46b03be2c38af330a5367
ms.sourcegitcommit: 3aa0fbfdde618656d66edf7e469e543c2aa29a57
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/05/2019
ms.locfileid: "55730335"
---
# <a name="how-to-use-packer-to-create-linux-virtual-machine-images-in-azure"></a>Jak vytvořit Linuxové Image virtuálních počítačů v Azure pomocí Packeru
Každý virtuální počítač (VM) v Azure je vytvořený z image, která definuje Linuxovou distribuci a verzi operačního systému. Image můžete zahrnout předinstalované aplikace a konfigurace. Na webu Azure Marketplace obsahuje celou řadu imagí první a třetí strany pro nejběžnější distribuce a prostředí aplikace, nebo můžete vytvořit vlastní Image přizpůsobené vašim potřebám. Tento článek podrobně popisuje, jak používat open source nástroj [Packeru](https://www.packer.io/) k definování a vytvoření vlastních imagí v Azure.


## <a name="create-azure-resource-group"></a>Vytvoření skupiny prostředků Azure
Během procesu sestavování vytvoří Packeru dočasné prostředky Azure jako sestavení zdrojového virtuálního počítače. K zachycení této zdrojový virtuální počítač pro použití jako image, je nutné definovat skupinu prostředků. V této skupině prostředků se ukládá výstup z procesu sestavení Packeru.

Vytvořte skupinu prostředků pomocí příkazu [az group create](/cli/azure/group). Následující příklad vytvoří skupinu prostředků *myResourceGroup* v umístění *eastus*:

```azurecli
az group create -n myResourceGroup -l eastus
```


## <a name="create-azure-credentials"></a>Vytvoření přihlašovacích údajů Azure
Packeru se ověřuje pomocí Azure s využitím instančního objektu. Instanční objekt Azure je identita zabezpečení, který vám pomůže s aplikací, služeb a nástrojů automatizace, jako je Packeru. Kontrolou a můžete definovat oprávnění, jaké operace lze provádět instanční objekt služby v Azure.

Vytvořit instanční objekt s [az ad sp create-for-rbac](/cli/azure/ad/sp) a přihlašovací údaje, které potřebuje Packeru výstup:

```azurecli
az ad sp create-for-rbac --query "{ client_id: appId, client_secret: password, tenant_id: tenant }"
```

Příklad výstupu z výše uvedených příkazů vypadá takto:

```azurecli
{
    "client_id": "f5b6a5cf-fbdf-4a9f-b3b8-3c2cd00225a4",
    "client_secret": "0e760437-bf34-4aad-9f8d-870be799c55d",
    "tenant_id": "72f988bf-86f1-41af-91ab-2d7cd011db47"
}
```

Pro ověření do Azure, budete potřebovat k získání ID vašeho předplatného Azure s [zobrazit účet az](/cli/azure/account):

```azurecli
az account show --query "{ subscription_id: id }"
```

Výstup z těchto dvou příkazů použijete v dalším kroku.


## <a name="define-packer-template"></a>Definice šablony Packeru
Pokud chcete vytvořit Image, vytvořit šablonu jako soubor JSON. V šabloně definujte tvůrci a provisioners, které vykonávají proces skutečné sestavení. Má packeru [zajištění webu pro Azure](https://www.packer.io/docs/builders/azure.html) , který umožňuje definovat prostředky Azure, jako jsou pověření instančního objektu služby, vytvořili v předchozím kroku.

Vytvořte soubor s názvem *ubuntu.json* a vložte následující obsah. Zadejte vlastní hodnoty pro následující:

| Parametr                           | Kde můžete získat |
|-------------------------------------|----------------------------------------------------|
| *client_id*                         | První řádek výstupu z `az ad sp` vytvoření příkazu - *appId* |
| *client_secret*                     | Druhý řádek výstupu z `az ad sp` vytvoření příkazu - *heslo* |
| *tenant_id*                         | Třetí řádek výstupu z `az ad sp` vytvoření příkazu - *tenanta* |
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

Tato šablona vytvoří bitovou kopii se systémem Ubuntu 16.04 LTS, nainstaluje server NGINX a pak deprovisions virtuálního počítače.

> [!NOTE]
> Pokud rozbalíte na této šabloně zřízení uživatelských přihlašovacích údajů, upravte příkaz zajištění webu, který deprovisions agenta služby Azure ke čtení `-deprovision` spíše než `deprovision+user`.
> `+user` Příznak odebere všechny uživatelské účty ze zdrojového virtuálního počítače.


## <a name="build-packer-image"></a>Sestavení image Packeru
Pokud ještě nemáte nainstalované na místním počítači, Packeru [postupujte podle pokynů k instalaci Packeru](https://www.packer.io/docs/install/index.html).

Sestavení image tak, že zadáte vaše Packeru soubor šablony následujícím způsobem:

```bash
./packer build ubuntu.json
```

Příklad výstupu z výše uvedených příkazů vypadá takto:

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

Trvá několik minut, než Packeru pro vytvoření virtuálního počítače, spusťte provisioners a vyčištění nasazení.


## <a name="create-vm-from-azure-image"></a>Vytvoření virtuálního počítače z Imagí Azure
Nyní můžete vytvořit virtuální počítač z bitové kopie s [az vm vytvořit](/cli/azure/vm). Určete bitovou kopii, která jste vytvořili pomocí `--image` parametru. Následující příklad vytvoří virtuální počítač s názvem *myVM* z *myPackerImage* a vygeneruje klíče SSH, pokud ještě neexistují:

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image myPackerImage \
    --admin-username azureuser \
    --generate-ssh-keys
```

Pokud chcete vytvořit virtuální počítače v jiné skupině prostředků nebo oblasti než vaší image Packeru, zadejte ID bitové kopie nikoli název image. Můžete získat ID bitové kopie s [az image show](/cli/azure/image#az-image-show).

Trvá několik minut pro vytvoření virtuálního počítače. Po vytvoření virtuálního počítače, poznamenejte si `publicIpAddress` zobrazí rozhraní příkazového řádku Azure. Tato adresa se používá pro přístup k webu NGINX přes webový prohlížeč.

Pokud chcete umožnit přístup k virtuálnímu počítači webovému provozu, otevřete port 80 z internetu pomocí příkazu [az vm open-port](/cli/azure/vm):

```azurecli
az vm open-port \
    --resource-group myResourceGroup \
    --name myVM \
    --port 80
```

## <a name="test-vm-and-nginx"></a>Testovací virtuální počítač a server NGINX
Nyní můžete otevřít webový prohlížeč a do adresního řádku zadat `http://publicIpAddress`. Zadejte vlastní veřejnou IP adresu získanou při vytváření virtuálního počítače. Jako v následujícím příkladu se zobrazí výchozí stránka serveru NGINX:

![Výchozí web NGINX](./media/build-image-with-packer/nginx.png) 


## <a name="next-steps"></a>Další postup
V tomto příkladu používá Packeru pro vytvoření image virtuálního počítače se serverem NGINX už nainstalovaná. Tato image virtuálního počítače společně s existující pracovní postupy nasazení, například můžete použít k nasazení vaší aplikace na virtuální počítače vytvořené z této Image pomocí Ansible, Chef nebo Puppet.

Další příklad šablony Packeru pro jiné distribuce Linuxu, naleznete v tématu [úložiště GitHub](https://github.com/hashicorp/packer/tree/master/examples/azure).
