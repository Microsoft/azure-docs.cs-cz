---
title: Použití rozšíření virtuálního počítače Azure Docker | Microsoft Docs
description: Další informace o použití rozšíření virtuálního počítače Docker rychle a bezpečně nasadit Docker prostředí v Azure pomocí šablony Resource Manageru a 2.0 rozhraní příkazového řádku Azure
services: virtual-machines-linux
documentationcenter: ''
author: iainfoulds
manager: jeconnoc
editor: ''
ms.assetid: 936d67d7-6921-4275-bf11-1e0115e66b7f
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 12/18/2017
ms.author: iainfou
ms.openlocfilehash: 6cf77a6fa5e2cb7f9ce349e72444e76d4c687f49
ms.sourcegitcommit: 828d8ef0ec47767d251355c2002ade13d1c162af
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/25/2018
ms.locfileid: "36937648"
---
# <a name="create-a-docker-environment-in-azure-using-the-docker-vm-extension"></a>Vytvořte prostředí Docker v Azure pomocí rozšíření virtuálního počítače Docker
Docker je Oblíbené kontejner správy a vytváření bitové kopie platforma, která umožňuje rychle pracovat s kontejnery v systému Linux. V Azure existují různé způsoby, které můžete nasadit Docker podle svých potřeb. Tento článek se zaměřuje na rozšíření virtuálního počítače Docker a šablon Azure Resource Manageru pomocí Azure CLI 2.0. 

> [!WARNING]
> Rozšíření virtuálního počítače Azure Docker pro Linux je zastaralá a vyřadí listopadu 2018.
> Rozšíření jenom nainstaluje Docker, proto alternativy, například cloudové init nebo rozšíření vlastních skriptů je lepší způsob, jak nainstalovat verzi Docker výběru. Další informace o tom, jak používat cloudové init najdete v tématu [přizpůsobení virtuálního počítače s Linuxem s inicializací cloudu](tutorial-automate-vm-deployment.md).

## <a name="azure-docker-vm-extension-overview"></a>Přehled rozšíření Azure virtuální počítač Docker
Rozšíření virtuálního počítače Azure Docker nainstaluje a nakonfiguruje démon Docker, Docker klienta a Docker Compose ve virtuálním počítači (VM) Linux. Pomocí rozšíření virtuálního počítače Azure Docker máte další ovládací prvek a funkce než jednoduše pomocí Docker počítače nebo vytváření hostitelů Docker sami. Tyto další funkce, jako například [Docker Compose](https://docs.docker.com/compose/overview/), ověřte rozšíření virtuálního počítače Azure Docker vhodné pro robustnější vývojáře nebo v provozním prostředí.

Další informace o různých metodách nasazení, včetně použití Docker počítače a služby kontejneru Azure najdete v následujících článcích:

* Chcete rychle prototypu na aplikaci, můžete vytvořit pomocí jednoho hostitele Docker [počítač Docker](docker-machine.md).
* Pokud chcete vytvořit produkční prostředí, škálovatelné prostředí, které poskytují další plánování a nástroje pro správu, můžete nasadit [Kubernetes](../../container-service/kubernetes/index.yml) nebo [Docker Swarm](../../container-service/dcos-swarm/index.yml) clusteru na kontejneru služby Azure.


## <a name="deploy-a-template-with-the-azure-docker-vm-extension"></a>Nasazení šablony s příponou virtuálního počítače Azure Docker
K vytvoření virtuálního počítače s Ubuntu využívající rozšíření virtuálního počítače Azure Docker na instalaci a konfiguraci hostitelů Docker použijeme existující šablony rychlý start. Můžete zobrazit šablonu zde: [jednoduché nasazení virtuálního počítače s Ubuntu pomocí Docker](https://github.com/Azure/azure-quickstart-templates/tree/master/docker-simple-on-ubuntu). Budete potřebovat nejnovější [Azure CLI 2.0](/cli/azure/install-az-cli2) nainstalován a přihlášení k účtu Azure pomocí [az přihlášení](/cli/azure/reference-index#az_login).

Nejdřív vytvořte skupinu prostředků pomocí příkazu [az group create](/cli/azure/group#az_group_create). Následující příklad vytvoří skupinu prostředků *myResourceGroup* v umístění *eastus*:

```azurecli
az group create --name myResourceGroup --location eastus
```

V dalším kroku nasaďte virtuální počítač s [vytvořit nasazení skupiny az](/cli/azure/group/deployment#az_group_deployment_create) obsahující rozšíření virtuálního počítače Azure Docker z [této šablony Azure Resource Manageru na Githubu](https://github.com/Azure/azure-quickstart-templates/tree/master/docker-simple-on-ubuntu). Po zobrazení výzvy zadejte své vlastní jedinečné hodnoty pro *newStorageAccountName*, *adminUsername*, *adminPassword*, a *dnsNameForPublicIP*:

```azurecli
az group deployment create --resource-group myResourceGroup \
    --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/docker-simple-on-ubuntu/azuredeploy.json
```

Jak dlouho trvá několik minut na dokončení nasazení.


## <a name="deploy-your-first-nginx-container"></a>Nasazení vaší první kontejner nginx a SVÁŽE
Chcete-li zobrazit podrobnosti o virtuálního počítače, včetně názvu DNS, použijte [az virtuálních počítačů zobrazit](/cli/azure/vm#az_vm_show):

```azurecli
az vm show \
    --resource-group myResourceGroup \
    --name myDockerVM \
    --show-details \
    --query [fqdns] \
    --output tsv
```

SSH vašeho nového Docker hostitele. Zadejte uživatelské jméno a název DNS z předchozích kroků:

```bash
ssh azureuser@mypublicdns.eastus.cloudapp.azure.com
```

Po přihlášení k hostitelů Docker, můžeme spusťte kontejner NGINX:

```bash
sudo docker run -d -p 80:80 nginx
```

Výstup se podobá v následujícím příkladu jako bitovou kopii NGINX se stáhnou a spustí kontejner:

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

Výstup se podobá v následujícím příkladu, zobrazující, že kontejner nginx a SVÁŽE spuštěná a porty TCP 80 a 443 a předávaná:

```bash
CONTAINER ID        IMAGE               COMMAND                  CREATED              STATUS              PORTS                         NAMES
b6ed109fb743        nginx               "nginx -g 'daemon off"   About a minute ago   Up About a minute   0.0.0.0:80->80/tcp, 443/tcp   adoring_payne
```

Informace o vaší kontejneru v akci, otevře webový prohlížeč a zadejte název DNS vašeho Docker hostitele:

![Spuštěné ngnix kontejneru](./media/dockerextension/nginxrunning.png)

## <a name="azure-docker-vm-extension-template-reference"></a>Azure odkaz na šablonu rozšíření virtuální počítač Docker
Předchozí příklad používá existující šablony rychlý start. Rozšíření virtuálního počítače Azure Docker můžete nasadit také pomocí vlastní šablony Resource Manageru. Uděláte to tak, přidejte následující šablony Resource Manageru, definování `vmName` vašeho virtuálního počítače správně:

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
    "typeHandlerVersion": "1.*",
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

