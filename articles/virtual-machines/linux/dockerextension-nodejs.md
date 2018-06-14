---
title: Pomocí rozšíření virtuálního počítače Azure Docker 1.0 rozhraní příkazového řádku Azure | Microsoft Docs
description: Další informace o použití rozšíření virtuálního počítače Docker rychle a bezpečně nasadit Docker prostředí v Azure pomocí šablony Resource Manageru.
services: virtual-machines-linux
documentationcenter: ''
author: iainfoulds
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/11/2017
ms.author: iainfou
ms.openlocfilehash: 2f981a50887138660d26a9d011870a05f1270c94
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2018
ms.locfileid: "30906605"
---
# <a name="create-a-docker-environment-in-azure-using-the-docker-vm-extension-with-the-azure-cli-10"></a>Vytvořte prostředí Docker v Azure pomocí Azure CLI 1.0 rozšíření virtuálního počítače Docker
Docker je Oblíbené kontejner správy a vytváření bitové kopie platforma, která umožňuje rychle pracovat s kontejnery na Linuxu (a také Windows). V Azure existují různé způsoby, které můžete nasadit Docker podle svých potřeb. Tento článek se zaměřuje na pomocí rozšíření virtuálního počítače Docker a šablon Azure Resource Manageru. 

Další informace o různých metodách nasazení, včetně použití Docker počítače a služby kontejneru Azure najdete v následujících článcích:

* Chcete rychle prototypu na aplikaci, můžete vytvořit pomocí jednoho hostitele Docker [počítač Docker](docker-machine.md).
* Pro větší, stabilnější prostředí, můžete použít rozšíření virtuálního počítače Azure Docker, který podporuje i [Docker Compose](https://docs.docker.com/compose/overview/) ke generování nasazení konzistentní kontejnerů. Tento článek údaje pomocí rozšíření virtuálního počítače Azure Docker.
* Pokud chcete vytvořit produkční prostředí, škálovatelné prostředí, které poskytují další plánování a nástroje pro správu, můžete nasadit [clusteru Docker Swarm na kontejneru služby Azure](../../container-service/dcos-swarm/container-service-deployment.md).

## <a name="cli-versions-to-complete-the-task"></a>Verze rozhraní příkazového řádku pro dokončení úlohy
K dokončení úlohy můžete využít jednu z následujících verzí rozhraní příkazového řádku:

- [Azure CLI 1.0](#azure-docker-vm-extension-overview) – naše rozhraní příkazového řádku pro classic a resource správu modelech nasazení (v tomto článku)
- [Azure CLI 2.0](dockerextension.md) – naše rozhraní příkazového řádku nové generace pro model nasazení správy prostředků 

## <a name="azure-docker-vm-extension-overview"></a>Přehled rozšíření Azure virtuální počítač Docker
Rozšíření virtuálního počítače Azure Docker nainstaluje a nakonfiguruje démon Docker, Docker klienta a Docker Compose ve virtuálním počítači (VM) Linux. Pomocí rozšíření virtuálního počítače Azure Docker máte další ovládací prvek a funkce než jednoduše pomocí Docker počítače nebo vytváření hostitelů Docker sami. Tyto další funkce, jako například [Docker Compose](https://docs.docker.com/compose/overview/), ověřte rozšíření virtuálního počítače Azure Docker vhodné pro robustnější vývojáře nebo v provozním prostředí.

Šablony Azure Resource Manageru definovat strukturu celého vašeho prostředí. Šablony umožňují vytvořit a nakonfigurovat prostředkům, například Docker hostitele virtuálních počítačů, úložiště, řízení přístupu na základě rolí (RBAC) a diagnostiky. Tyto šablony k vytvoření další nasazení konzistentním způsobem můžete znovu použít. Další informace o Azure Resource Manager a šablonách najdete v tématu [Přehled služby Správce prostředků](../../azure-resource-manager/resource-group-overview.md). 

## <a name="deploy-a-template-with-the-azure-docker-vm-extension"></a>Nasazení šablony s příponou virtuálního počítače Azure Docker
K vytvoření virtuálního počítače s Ubuntu využívající rozšíření virtuálního počítače Azure Docker na instalaci a konfiguraci hostitelů Docker použijeme existující šablony rychlý start. Můžete zobrazit šablonu zde: [jednoduché nasazení virtuálního počítače s Ubuntu pomocí Docker](https://github.com/Azure/azure-quickstart-templates/tree/master/docker-simple-on-ubuntu). 

Je nutné [nejnovější rozhraní příkazového řádku Azure](../../cli-install-nodejs.md) nainstalován a přihlášení pomocí režimu Resource Manager takto:

```azurecli
azure config mode arm
```

Šablonu nasaďte pomocí rozhraní příkazového řádku Azure, určení šablony URI. Následující příklad vytvoří skupinu prostředků *myResourceGroup* v umístění *westus*. Použijte vlastní název skupiny prostředků a umístění následujícím způsobem:

```azurecli
azure group create \
    --name myResourceGroup \
    --location westus \
    --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/docker-simple-on-ubuntu/azuredeploy.json
```

Odpovězte výzev a zadejte uživatelské jméno a heslo, název účtu úložiště a zadejte název DNS. Výstup se podobá následujícímu příkladu:

```azurecli
info:    Executing command group create
+ Getting resource group myResourceGroup
+ Updating resource group myResourceGroup
info:    Updated resource group myResourceGroup
info:    Supply values for the following parameters
newStorageAccountName: mystorageaccount
adminUsername: azureuser
adminPassword: P@ssword!
dnsNameForPublicIP: mypublicidns
+ Initializing template configurations and parameters
+ Creating a deployment
info:    Created template deployment "azuredeploy"
data:    Id:                  /subscriptions/guid/resourceGroups/myResourceGroup
data:    Name:                myResourceGroup
data:    Location:            westus
data:    Provisioning State:  Succeeded
data:    Tags: null
data:
info:    group create command OK
```

Vrátí rozhraní příkazového řádku Azure, můžete do příkazového řádku po jenom pár sekund, ale váš hostitel Docker stále probíhá a konfigurují rozšíření virtuálního počítače Azure Docker. Jak dlouho trvá několik minut na dokončení nasazení. Můžete zobrazit podrobnosti o používání stav hostitelů Docker `azure vm show` příkaz.

Následující příklad ověří stav virtuálního počítače s názvem *myDockerVM* (výchozí název ze šablony - neměnit tento název) ve skupině prostředků s názvem *myResourceGroup*. Zadejte název skupiny prostředků, kterou jste vytvořili v předchozím kroku:

```azurecli
azure vm show --resource-group myResourceGroup --name myDockerVM
```

Výstup `azure vm show` příkaz je podobně jako v následujícím příkladu:

```azurecli
info:    Executing command vm show
+ Looking up the VM "myDockerVM"
+ Looking up the NIC "myVMNicD"
+ Looking up the public ip "myPublicIPD"
data:    Id                              :/subscriptions/guid/resourceGroups/myresourcegroup/providers/Microsoft.Compute/virtualMachines/MyDockerVM
data:    ProvisioningState               :Succeeded
data:    Name                            :MyDockerVM
data:    Location                        :westus
data:    Type                            :Microsoft.Compute/virtualMachines
[...]
data:
data:    Network Profile:
data:      Network Interfaces:
data:        Network Interface #1:
data:          Primary                   :true
data:          MAC Address               :00-0D-3A-33-D3-95
data:          Provisioning State        :Succeeded
data:          Name                      :myVMNicD
data:          Location                  :westus
data:            Public IP address       :13.91.107.235
data:            FQDN                    :mypublicdns.westus.cloudapp.azure.com
data:
data:    Diagnostics Instance View:
info:    vm show command OK
```

V horní části výstupu se zobrazí **ProvisioningState** virtuálního počítače. Zobrazí se při *úspěšné*, nasazení úspěšně proběhlo, a můžete SSH k virtuálnímu počítači.

Na konci výstupu *plně kvalifikovaný název domény* zobrazuje plně kvalifikovaný název vaší Docker hostitele. Tento plně kvalifikovaný název domény se používá k SSH k hostiteli Docker v zbývající kroky.

## <a name="deploy-your-first-nginx-container"></a>Nasazení vaší první kontejner nginx a sváže
Po nasazení úspěšně proběhlo, SSH vašeho nového hostitele Docker z místního počítače. Zadejte uživatelské jméno a plně kvalifikovaný název domény následujícím způsobem:

```bash
ssh ops@mypublicdns.westus.cloudapp.azure.com
```

Po přihlášení k hostitelů Docker, můžeme spusťte kontejner nginx:

```bash
sudo docker run -d -p 80:80 nginx
```

Výstup se podobá v následujícím příkladu jako bitovou kopii nginx se stáhnou a spustí kontejner:

```bash
Unable to find image 'nginx:latest' locally
latest: Pulling from library/nginx
efd26ecc9548: Pull complete
a3ed95caeb02: Pull complete
a48df1751a97: Pull complete
8ddc2d7beb91: Pull complete
Digest: sha256:2ca2638e55319b7bc0c7d028209ea69b1368e95b01383e66dfe7e4f43780926d
Status: Downloaded newer image for nginx:latest
b6ed109fb743a762ff21a4606dd38d3e5d35aff43fa7f12e8d4ed1d920b0cd74
```

Zkontrolujte stav kontejnery spuštěné v hostiteli Docker následujícím způsobem:

```bash
sudo docker ps
```

Výstup se podobá v následujícím příkladu, zobrazující, že kontejner nginx a sváže spuštěná a porty TCP 80 a 443 a předávaná:

```bash
CONTAINER ID        IMAGE               COMMAND                  CREATED              STATUS              PORTS                         NAMES
b6ed109fb743        nginx               "nginx -g 'daemon off"   About a minute ago   Up About a minute   0.0.0.0:80->80/tcp, 443/tcp   adoring_payne
```

Informace o vaší kontejneru v akci, otevře webový prohlížeč a zadejte plně kvalifikovaný název domény název hostitele vašeho Docker:

![Spuštěné ngnix kontejneru](./media/dockerextension/nginxrunning.png)

## <a name="azure-docker-vm-extension-template-reference"></a>Azure odkaz na šablonu rozšíření virtuální počítač Docker
Předchozí příklad používá existující šablony rychlý start. Rozšíření virtuálního počítače Azure Docker můžete nasadit také pomocí vlastní šablony Resource Manageru. Uděláte to tak, přidejte následující šablony Resource Manageru, definování *vmName* vašeho virtuálního počítače správně:

```json
{
  "type": "Microsoft.Compute/virtualMachines/extensions",
  "name": "[concat(variables('vmName'), '/DockerExtension'))]",
  "apiVersion": "2015-05-01-preview",
  "location": "[parameters('location')]",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
  ],
  "properties": {
    "publisher": "Microsoft.Azure.Extensions",
    "type": "DockerExtension",
    "typeHandlerVersion": "1.1",
    "autoUpgradeMinorVersion": true,
    "settings": {},
    "protectedSettings": {}
  }
}
```

Můžete najít podrobnější návod na pomocí šablony Resource Manageru čtení [přehled Azure Resource Manageru](../../azure-resource-manager/resource-group-overview.md).

## <a name="next-steps"></a>Další postup
Pravděpodobně budete chtít [nakonfigurujte funkce Docker TCP port](https://docs.docker.com/engine/reference/commandline/dockerd/#/bind-docker-to-another-hostport-or-a-unix-socket), pochopit [Docker zabezpečení](https://docs.docker.com/engine/security/security/), nebo nasazení kontejnerů pomocí [Docker Compose](https://docs.docker.com/compose/overview/). Další informace o rozšíření virtuálního počítače Azure Docker, samotné, najdete v článku [Githubu projektu](https://github.com/Azure/azure-docker-extension/).

Přečtěte si další informace o dalších možnostech nasazení Docker v Azure:

* [Použít počítač Docker pomocí Azure ovladače](docker-machine.md)  
* [Začínáme s Docker a vytvářené definovat a spusťte aplikaci služby kontejneru na virtuální počítač Azure](docker-compose-quickstart.md).
* [Nasazení clusteru Azure Container Service](../../container-service/dcos-swarm/container-service-deployment.md)

