---
title: Použití rozšíření Azure Docker VM | Dokumentace Microsoftu
description: Další informace o použití rozšíření Docker VM pro rychle a bezpečně nasazení prostředí Docker v Azure pomocí šablon Resource Manageru a rozhraní příkazového řádku Azure CLI 2.0
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
ms.assetid: 936d67d7-6921-4275-bf11-1e0115e66b7f
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 12/18/2017
ms.author: cynthn
ms.openlocfilehash: 44c307a5f21937cd2a3ef345fd4573c67efdaf59
ms.sourcegitcommit: aa988666476c05787afc84db94cfa50bc6852520
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/10/2018
ms.locfileid: "37928614"
---
# <a name="create-a-docker-environment-in-azure-using-the-docker-vm-extension"></a>Vytvoření prostředí pro Docker v Azure pomocí rozšíření Docker VM
Docker je oblíbených kontejneru platforma pro správu a pro vytváření bitových kopií, která umožňuje rychlou práci s kontejnery v Linuxu. V Azure existují různé způsoby, které můžete nasadit Docker podle vašich potřeb. Tento článek se týká použití rozšíření Docker VM a šablon Azure Resource Manageru s využitím rozhraní příkazového řádku Azure CLI 2.0. 

> [!WARNING]
> Rozšíření Azure Docker VM pro Linux je zastaralá a skončí. listopadu 2018.
> Rozšíření Docker, nainstaluje pouze, takže alternativy, třeba cloud-init nebo pomocí rozšíření vlastních skriptů jsou lepší způsob, jak nainstalovat verzi Docker podle výběru. Další informace o tom, jak pomocí cloud-init najdete v tématu [přizpůsobení virtuálního počítače s Linuxem pomocí cloud-init](tutorial-automate-vm-deployment.md).

## <a name="azure-docker-vm-extension-overview"></a>Přehled rozšíření Azure Docker VM
Rozšíření Azure Docker VM nainstaluje a nakonfiguruje démona Dockeru, klient Docker a Docker Compose ve virtuální počítač s Linuxem (VM). Pomocí rozšíření Azure Docker VM, máte další ovládací prvek a funkce než jednoduše pomocí Docker Machine nebo vytvoření hostitele Docker sami. Tyto další funkce, jako [Docker Compose](https://docs.docker.com/compose/overview/), ověřte rozšíření Azure Docker VM vhodné robustnější pro vývojáře nebo produkční prostředí.

Další informace o různých metodách nasazení, včetně použití Docker Machine a služby Azure Container Service najdete v následujících článcích:

* Rychle vytvářet prototypy na aplikaci, můžete vytvořit jeden hostitele Docker pomocí [Docker Machine](docker-machine.md).
* K vytváření připravené pro produkční prostředí, škálovatelné prostředí, které poskytují další nástroje pro plánování a správu, můžete nasadit [Kubernetes](../../container-service/kubernetes/index.yml) nebo [Docker Swarm](../../container-service/dcos-swarm/index.yml) clusteru v Azure Container Service.


## <a name="deploy-a-template-with-the-azure-docker-vm-extension"></a>Nasazení šablony pomocí rozšíření Azure Docker VM
Použijeme existující šablony rychlý start k vytvoření virtuálního počítače s Ubuntu, který používá rozšíření Azure Docker VM pro instalaci a konfiguraci hostitele Dockeru. Můžete zobrazit šablonu tady: [jednoduchému nasazení virtuálního počítače s Ubuntu s Dockerem](https://github.com/Azure/azure-quickstart-templates/tree/master/docker-simple-on-ubuntu). Budete potřebovat nejnovější [příkazového řádku Azure CLI 2.0](/cli/azure/install-az-cli2) nainstalovaný a přihlášení k účtu Azure pomocí [az login](/cli/azure/reference-index#az_login).

Nejdřív vytvořte skupinu prostředků pomocí příkazu [az group create](/cli/azure/group#az_group_create). Následující příklad vytvoří skupinu prostředků *myResourceGroup* v umístění *eastus*:

```azurecli
az group create --name myResourceGroup --location eastus
```

V dalším kroku nasaďte virtuální počítač s [vytvořit nasazení skupiny pro az](/cli/azure/group/deployment#az_group_deployment_create) , který obsahuje rozšíření Azure Docker VM z [tuto šablonu Azure Resource Manageru na Githubu](https://github.com/Azure/azure-quickstart-templates/tree/master/docker-simple-on-ubuntu). Po zobrazení výzvy zadejte své vlastní jedinečné hodnoty *newStorageAccountName*, *adminUsername*, *adminPassword*, a *dnsNameForPublicIP*:

```azurecli
az group deployment create --resource-group myResourceGroup \
    --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/docker-simple-on-ubuntu/azuredeploy.json
```

Trvá několik minut na dokončení nasazení.


## <a name="deploy-your-first-nginx-container"></a>Nasadíte svůj první kontejner NGINX
Chcete-li zobrazit podrobnosti o vašich virtuálních počítačů, včetně názvu DNS, použijte [az vm show](/cli/azure/vm#az_vm_show):

```azurecli
az vm show \
    --resource-group myResourceGroup \
    --name myDockerVM \
    --show-details \
    --query [fqdns] \
    --output tsv
```

SSH na nového hostitele Dockeru. Zadejte své uživatelské jméno a název DNS z předchozích kroků:

```bash
ssh azureuser@mypublicdns.eastus.cloudapp.azure.com
```

Po přihlášení k hostitele Docker, umožňuje spuštění kontejneru NGINX:

```bash
sudo docker run -d -p 80:80 nginx
```

Výstup se podobá následujícímu příkladu se stáhne image serveru NGINX a spuštění kontejneru:

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

Postup kontroly stavu kontejnery spuštěné na hostitele Docker následujícím způsobem:

```bash
sudo docker ps
```

Výstup se podobá následujícímu příkladu, zobrazující, že kontejner NGINX a spuštění a porty TCP 80 a 443 a předávaná:

```bash
CONTAINER ID        IMAGE               COMMAND                  CREATED              STATUS              PORTS                         NAMES
b6ed109fb743        nginx               "nginx -g 'daemon off"   About a minute ago   Up About a minute   0.0.0.0:80->80/tcp, 443/tcp   adoring_payne
```

Pokud chcete zobrazit váš kontejner v akci, otevřete webový prohlížeč a zadejte název DNS hostitele Docker:

![Spuštěný kontejner ngnix](./media/dockerextension/nginxrunning.png)

## <a name="azure-docker-vm-extension-template-reference"></a>Referenční informace šablony rozšíření Azure Docker VM
Předchozí příklad používá existující šablony rychlý start. Rozšíření virtuálního počítače Azure Docker můžete také nasadit pomocí šablony Resource Manageru. Uděláte to tak, přidejte do své šablony Resource Manageru, definování následující `vmName` vašeho virtuálního počítače správně:

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

Najdete podrobný návod na použití šablon Resource Manageru najdete [přehled Azure Resource Manageru](../../azure-resource-manager/resource-group-overview.md).

## <a name="next-steps"></a>Další postup
Možná budete chtít [konfiguraci démona Dockeru TCP port](https://docs.docker.com/engine/reference/commandline/dockerd/#/bind-docker-to-another-hostport-or-a-unix-socket), pochopit [zabezpečení Dockeru](https://docs.docker.com/engine/security/security/), nebo nasazujte kontejnery pomocí [Docker Compose](https://docs.docker.com/compose/overview/). Další informace o rozšíření Azure Docker VM, sama, najdete v článku [projektu z Githubu](https://github.com/Azure/azure-docker-extension/).

Přečtěte si další informace o dalších možnostech nasazení Docker v Azure:

* [Použití Docker Machine s Azure ovladače](docker-machine.md)  
* [Začínáme s prostředím Docker a Compose definovat a spouštět aplikace na virtuálním počítači Azure](docker-compose-quickstart.md).
* [Nasazení clusteru Azure Container Service](../../container-service/dcos-swarm/container-service-deployment.md)

