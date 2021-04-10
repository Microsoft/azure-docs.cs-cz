---
title: Vytvoření imagí virtuálních počítačů Azure pro Linux pomocí balíčku
description: Naučte se používat balíček k vytváření imagí virtuálních počítačů se systémem Linux v Azure.
author: cynthn
ms.service: virtual-machines
ms.subservice: imaging
ms.topic: how-to
ms.workload: infrastructure
ms.date: 05/07/2019
ms.author: cynthn
ms.collection: linux
ms.openlocfilehash: 4d85106cb78b5f4799a78d18463d83594f54dbac
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "102556736"
---
# <a name="how-to-use-packer-to-create-linux-virtual-machine-images-in-azure"></a>Použití balíčku k vytváření imagí virtuálních počítačů se systémem Linux v Azure
Každý virtuální počítač (VM) v Azure je vytvořený z image, která definuje distribuci Linux a verzi operačního systému. Obrázky můžou zahrnovat předem nainstalované aplikace a konfigurace. Azure Marketplace poskytuje spoustu prvních a dalších imagí pro většinu běžných distribucí a prostředí aplikací, nebo můžete vytvořit vlastní image přizpůsobené vašim potřebám. Tento článek podrobně popisuje, jak pomocí Open [source nástroje pro](https://www.packer.io/) definování a vytváření vlastních imagí v Azure.

> [!NOTE]
> Azure teď má službu, Azure image Builder (Preview) pro definování a vytváření vlastních imagí. Azure image Builder je postavená na balíčku, takže můžete s ním dokonce používat i existující skripty sestavovatele prostředí pro vytváření balíčků. Informace o tom, jak začít s Azure image Builder, najdete v tématu [Vytvoření virtuálního počítače se systémem Linux pomocí nástroje Azure image Builder](image-builder.md).


## <a name="create-azure-resource-group"></a>Vytvoření skupiny prostředků Azure
Během procesu sestavování vytvoří balíček dočasné prostředky Azure při sestavení zdrojového virtuálního počítače. Pokud chcete zachytit zdrojový virtuální počítač pro použití jako image, musíte definovat skupinu prostředků. Výstup procesu sestavení balíčku je uložený v této skupině prostředků.

Vytvořte skupinu prostředků pomocí příkazu [az group create](/cli/azure/group). Následující příklad vytvoří skupinu prostředků s názvem *myResourceGroup* v umístění *eastus* :

```azurecli
az group create -n myResourceGroup -l eastus
```


## <a name="create-azure-credentials"></a>Vytvoření přihlašovacích údajů Azure
Balíček se ověřuje pomocí instančního objektu pomocí služby Azure. Instanční objekt Azure je identita zabezpečení, kterou můžete používat s aplikacemi, službami a nástroji pro automatizaci, jako je například nástroj Pack. Oprávnění můžete řídit a definovat podle toho, jaké operace může instanční objekt v Azure provádět.

Vytvořte instanční objekt pomocí [AZ AD SP Create-for-RBAC](/cli/azure/ad/sp) a zajistěte výstup přihlašovacích údajů, které potřebuje balírna:

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

K ověření v Azure je také potřeba získat ID předplatného Azure pomocí [AZ Account show](/cli/azure/account):

```azurecli
az account show --query "{ subscription_id: id }"
```

Výstup z těchto dvou příkazů můžete použít v dalším kroku.


## <a name="define-packer-template"></a>Definovat šablonu balíčku
K sestavení imagí vytvoříte šablonu jako soubor JSON. V šabloně definujete sestavení a zřizování, které provádějí vlastní proces sestavení. Balírna má [pro Azure zřízeného](https://www.packer.io/docs/builders/azure.html) úložiště, které umožňuje definovat prostředky Azure, například přihlašovací údaje instančního objektu vytvořené v předchozím kroku.

Vytvořte soubor s názvem *ubuntu.js* a vložte následující obsah. Zadejte vlastní hodnoty pro následující:

| Parametr                           | Kde získat |
|-------------------------------------|----------------------------------------------------|
| *client_id*                         | První řádek výstupu z `az ad sp` příkazu pro vytvoření – *appId* |
| *client_secret*                     | Druhý řádek výstupu z `az ad sp` příkazového *hesla* pro vytvoření |
| *tenant_id*                         | Třetí řádek výstupu z `az ad sp` příkazu CREATE – *tenant* |
| *subscription_id*                   | Výstup z `az account show` příkazu |
| *managed_image_resource_group_name* | Název skupiny prostředků, kterou jste vytvořili v prvním kroku |
| *managed_image_name*                | Název vytvořené image spravovaného disku |


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

Tato šablona vytvoří image Ubuntu 16,04 LTS, nainstaluje NGINX a potom odstaví virtuální počítač.

> [!NOTE]
> Pokud rozbalíte tuto šablonu a zřídíte přihlašovací údaje uživatele, upravte příkaz Správce, který odřadí agenta Azure pro čtení, `-deprovision` nikoli `deprovision+user` .
> `+user`Příznak odebere ze zdrojového virtuálního počítače všechny uživatelské účty.


## <a name="build-packer-image"></a>Obrázek sady Build Pack
Pokud ještě nemáte v místním počítači nainstalovaný balíček, [postupujte podle pokynů k instalaci nástroje Pack](https://www.packer.io/docs/install).

Sestavte bitovou kopii tak, že zadáte soubor šablony balíčku následujícím způsobem:

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

Zabere několik minut, než balíček sestaví virtuální počítač, spustí zřizování a vyčistí nasazení.


## <a name="create-vm-from-azure-image"></a>Vytvoření virtuálního počítače z image Azure
Nyní můžete vytvořit virtuální počítač z Image pomocí [AZ VM Create](/cli/azure/vm). Zadejte obrázek, který jste vytvořili pomocí `--image` parametru. Následující příklad vytvoří virtuální počítač s názvem *myVM* z *myPackerImage* a vygeneruje klíče SSH, pokud ještě neexistují:

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image myPackerImage \
    --admin-username azureuser \
    --generate-ssh-keys
```

Pokud chcete vytvořit virtuální počítače v jiné skupině prostředků nebo v jiné oblasti, než je image vašeho balíčku, zadejte ID image, nikoli název bitové kopie. ID obrázku můžete získat pomocí [AZ image show](/cli/azure/image#az-image-show).

Vytvoření virtuálního počítače trvá několik minut. Až se virtuální počítač vytvoří, poznamenejte si ho v rozhraní příkazového `publicIpAddress` řádku Azure CLI. Tato adresa se používá pro přístup k webu NGINX prostřednictvím webového prohlížeče.

Pokud chcete umožnit přístup k virtuálnímu počítači webovému provozu, otevřete port 80 z internetu pomocí příkazu [az vm open-port](/cli/azure/vm):

```azurecli
az vm open-port \
    --resource-group myResourceGroup \
    --name myVM \
    --port 80
```

## <a name="test-vm-and-nginx"></a>Testovací virtuální počítač a NGINX
Nyní můžete otevřít webový prohlížeč a do adresního řádku zadat `http://publicIpAddress`. Zadejte vlastní veřejnou IP adresu získanou při vytváření virtuálního počítače. Výchozí stránka NGINX se zobrazí jako v následujícím příkladu:

![Výchozí web NGINX](./media/build-image-with-packer/nginx.png) 


## <a name="next-steps"></a>Další kroky
Pomocí nástroje [Azure image Builder](image-builder.md)můžete také používat existující skripty pro sestavovatele balíčku.
