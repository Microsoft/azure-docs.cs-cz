---
title: "Nainstalujte MongoDB na virtuální počítač s Linuxem pomocí rozhraní příkazového řádku Azure | Microsoft Docs"
description: "Zjistěte, jak nainstalovat a nakonfigurovat MongoDB na iusing Linux virtuálního počítače Azure CLI 2.0"
services: virtual-machines-linux
documentationcenter: 
author: iainfoulds
manager: jeconnoc
editor: 
ms.assetid: 3f55b546-86df-4442-9ef4-8a25fae7b96e
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 12/15/2017
ms.author: iainfou
ms.openlocfilehash: b21b33a265d499136dbe3e72538923d8295e9876
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/09/2018
---
# <a name="how-to-install-and-configure-mongodb-on-a-linux-vm"></a>Postup instalace a konfigurace MongoDB na virtuální počítač s Linuxem
[MongoDB](http://www.mongodb.org) je Oblíbené databáze NoSQL open source a vysoce výkonné. Tento článek ukazuje, jak nainstalovat a nakonfigurovat MongoDB na virtuální počítač s Linuxem pomocí Azure CLI 2.0. K provedení těchto kroků můžete také využít [Azure CLI 1.0](install-mongodb-nodejs.md). Příklady jsou uvedeny této podrobnosti o tom, jak na:

* [Ručně nainstalujte a nakonfigurujte základní instance MongoDB](#manually-install-and-configure-mongodb-on-a-vm)
* [Vytvořte základní instance MongoDB pomocí šablony Resource Manageru](#create-basic-mongodb-instance-on-centos-using-a-template)
* [Vytvořit komplexní MongoDB horizontálně dělené clusteru s replikou nastaví pomocí šablony Resource Manageru](#create-a-complex-mongodb-sharded-cluster-on-centos-using-a-template)


## <a name="manually-install-and-configure-mongodb-on-a-vm"></a>Ručně nainstalujte a nakonfigurujte MongoDB na virtuálním počítači
MongoDB [poskytují pokyny k instalaci](https://docs.mongodb.com/manual/administration/install-on-linux/) pro distribucích systému Linux, včetně Red Hat nebo CentOS, SUSE, Ubuntu a Debian. Následující příklad vytvoří *CentOS* virtuálních počítačů. K vytvoření tohoto prostředí, je třeba nejnovější [Azure CLI 2.0](/cli/azure/install-az-cli2) nainstalován a přihlášení k účtu Azure pomocí [az přihlášení](/cli/azure/reference-index#az_login).

Vytvořte skupinu prostředků pomocí příkazu [az group create](/cli/azure/group#az_group_create). Následující příklad vytvoří skupinu prostředků *myResourceGroup* v umístění *eastus*:

```azurecli
az group create --name myResourceGroup --location eastus
```

Vytvořte virtuální počítač pomocí příkazu [az vm create](/cli/azure/vm#az_vm_create). Následující příklad vytvoří virtuální počítač s názvem *Můjvp* s uživatelem s názvem *azureuser* pomocí ověření veřejného klíče SSH

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image CentOS \
    --admin-username azureuser \
    --generate-ssh-keys
```

SSH k virtuálnímu počítači pomocí vlastního uživatelského jména a `publicIpAddress` uvedeného ve výstupu z předchozího kroku:

```bash
ssh azureuser@<publicIpAddress>
```

Chcete-li přidat zdroje instalace pro MongoDB, vytvořte **yum** soubor úložiště následujícím způsobem:

```bash
sudo touch /etc/yum.repos.d/mongodb-org-3.6.repo
```

Otevřete soubor úložišti MongoDB pro úpravy, jako například s `vi` nebo `nano`. Přidejte následující řádky:

```sh
[mongodb-org-3.6]
name=MongoDB Repository
baseurl=https://repo.mongodb.org/yum/redhat/$releasever/mongodb-org/3.6/x86_64/
gpgcheck=1
enabled=1
gpgkey=https://www.mongodb.org/static/pgp/server-3.6.asc
```

Nainstalujte MongoDB pomocí **yum** následujícím způsobem:

```bash
sudo yum install -y mongodb-org
```

Ve výchozím nastavení se SELinux vynucuje u CentOS bitové kopie, které brání v přístupu k MongoDB. Instalace nástroje pro správu zásad a konfigurace SELinux umožňující MongoDB pracovat na jeho výchozí port TCP 27017 následujícím způsobem:

```bash
sudo yum install -y policycoreutils-python
sudo semanage port -a -t mongod_port_t -p tcp 27017
```

Spusťte službu MongoDB následujícím způsobem:

```bash
sudo service mongod start
```

Ověření instalace MongoDB se připojíte pomocí místní `mongo` klienta:

```bash
mongo
```

Nyní testovací MongoDB instance přidáním některá data a pak vyhledávání:

```sh
> db
test
> db.foo.insert( { a : 1 } )  
> db.foo.find()  
{ "_id" : ObjectId("57ec477cd639891710b90727"), "a" : 1 }
> exit
```

V případě potřeby nakonfigurujte MongoDB spustit automaticky při restartu systému:

```bash
sudo chkconfig mongod on
```


## <a name="create-basic-mongodb-instance-on-centos-using-a-template"></a>Vytvořte základní instance MongoDB na CentOS pomocí šablony
Na jednom virtuálním počítači CentOS pomocí následující šablony Azure rychlý start z Githubu, můžete vytvořit základní instance MongoDB. Tato šablona používá k přidání rozšíření vlastních skriptů pro Linux **yum** úložiště pro vaše nově vytvořený CentOS virtuálních počítačů a potom nainstalujte MongoDB.

* [Základní instance MongoDB na CentOS](https://github.com/Azure/azure-quickstart-templates/tree/master/mongodb-on-centos) -https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/mongodb-on-centos/azuredeploy.json

K vytvoření tohoto prostředí, je třeba nejnovější [Azure CLI 2.0](/cli/azure/install-az-cli2) nainstalován a přihlášení k účtu Azure pomocí [az přihlášení](/cli/azure/reference-index#az_login). Nejdřív vytvořte skupinu prostředků pomocí příkazu [az group create](/cli/azure/group#az_group_create). Následující příklad vytvoří skupinu prostředků *myResourceGroup* v umístění *eastus*:

```azurecli
az group create --name myResourceGroup --location eastus
```

V dalším kroku nasaďte šablonu MongoDB s [vytvořit nasazení skupiny az](/cli/azure/group/deployment#az_group_deployment_create). Po zobrazení výzvy zadejte své vlastní jedinečné hodnoty pro *newStorageAccountName*, *dnsNameForPublicIP*a uživatelské jméno správce a heslo:

```azurecli
az group deployment create --resource-group myResourceGroup \
  --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/mongodb-on-centos/azuredeploy.json
```

Přihlaste se k virtuálnímu počítači pomocí veřejné adresy DNS vašeho virtuálního počítače. Můžete zobrazit veřejnou adresu DNS s [az virtuálních počítačů zobrazit](/cli/azure/vm#az_vm_show):

```azurecli
az vm show -g myResourceGroup -n myLinuxVM -d --query [fqdns] -o tsv
```

SSH k virtuálnímu počítači pomocí vlastního uživatelského jména a veřejnou adresu DNS:

```bash
ssh azureuser@mypublicdns.eastus.cloudapp.azure.com
```

Ověření instalace MongoDB se připojíte pomocí místní `mongo` klienta následujícím způsobem:

```bash
mongo
```

Nyní testovací instance přidáním některá data a hledání následujícím způsobem:

```sh
> db
test
> db.foo.insert( { a : 1 } )  
> db.foo.find()  
{ "_id" : ObjectId("57ec477cd639891710b90727"), "a" : 1 }
> exit
```


## <a name="create-a-complex-mongodb-sharded-cluster-on-centos-using-a-template"></a>Vytvořit Cluster horizontálně dělené komplexní MongoDB na CentOS pomocí šablony
Můžete vytvořit komplexní MongoDB horizontálně dělené clusteru pomocí následující šablony Azure rychlý start z Githubu. Tato šablona odpovídá [MongoDB horizontálně dělené clusteru osvědčené postupy](https://docs.mongodb.com/manual/core/sharded-cluster-components/) zajistit redundanci a vysokou dostupnost. Šablona vytvoří dvě horizontálních oddílů, se tři uzly v každé sady replik. Jeden konfigurace serveru repliky s tři uzly se také vytvoří plus dva **mongos** směrovač servery k zajištění konzistence k aplikacím z v horizontálních oddílů.

* [MongoDB horizontálního dělení clusteru na CentOS](https://github.com/Azure/azure-quickstart-templates/tree/master/mongodb-sharding-centos) -https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/mongodb-sharding-centos/azuredeploy.json

> [!WARNING]
> Nasazení tohoto komplexní horizontálně dělené clusteru MongoDB vyžaduje víc než 20 jádra, což obvykle představuje výchozí počet jader na oblast pro předplatné. Otevřete zvýšení počtu vaše základní požadavek podporu Azure.

K vytvoření tohoto prostředí, je třeba nejnovější [Azure CLI 2.0](/cli/azure/install-az-cli2) nainstalován a přihlášení k účtu Azure pomocí [az přihlášení](/cli/azure/reference-index#az_login). Nejdřív vytvořte skupinu prostředků pomocí příkazu [az group create](/cli/azure/group#az_group_create). Následující příklad vytvoří skupinu prostředků *myResourceGroup* v umístění *eastus*:

```azurecli
az group create --name myResourceGroup --location eastus
```

V dalším kroku nasaďte šablonu MongoDB s [vytvořit nasazení skupiny az](/cli/azure/group/deployment#az_group_deployment_create). Definovat vlastní prostředek názvy a velikosti, kde je potřeba, například jako u *mongoAdminUsername*, *sizeOfDataDiskInGB*, a *configNodeVmSize*:

```azurecli
az group deployment create --resource-group myResourceGroup \
  --parameters '{"adminUsername": {"value": "azureuser"},
    "adminPassword": {"value": "P@ssw0rd!"},
    "mongoAdminUsername": {"value": "mongoadmin"},
    "mongoAdminPassword": {"value": "P@ssw0rd!"},
    "dnsNamePrefix": {"value": "mypublicdns"},
    "environment": {"value": "AzureCloud"},
    "numDataDisks": {"value": "4"},
    "sizeOfDataDiskInGB": {"value": 20},
    "centOsVersion": {"value": "7.0"},
    "routerNodeVmSize": {"value": "Standard_DS3_v2"},
    "configNodeVmSize": {"value": "Standard_DS3_v2"},
    "replicaNodeVmSize": {"value": "Standard_DS3_v2"},
    "zabbixServerIPAddress": {"value": "Null"}}' \
  --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/mongodb-sharding-centos/azuredeploy.json \
  --name myMongoDBCluster \
  --no-wait
```

Toto nasazení může trvat přes hodinu můžete nasadit a nakonfigurovat všechny instance virtuálních počítačů. `--no-wait` Příznak se používá na konci předchozí příkaz k vrácení řízení na příkazovém řádku, jakmile nasazení šablony přijatá platformou Azure. Potom můžete zobrazit stav nasazení s [az skupiny nasazení zobrazit](/cli/azure/group/deployment#az_group_deployment_show). Následující příklad zobrazení stav *myMongoDBCluster* nasazení v *myResourceGroup* skupiny prostředků:

```azurecli
az group deployment show \
    --resource-group myResourceGroup \
    --name myMongoDBCluster \
    --query [properties.provisioningState] \
    --output tsv
```

## <a name="next-steps"></a>Další postup
V těchto příkladech můžete připojit k instanci MongoDB místně z virtuálního počítače. Pokud se chcete připojit k instanci MongoDB z jiného virtuálního počítače nebo sítě, zkontrolujte příslušné [vytvoří skupinu zabezpečení sítě pravidla](nsg-quickstart.md).

Tyto příklady nasadit prostředí základní MongoDB pro účely vývoje. Použít požadované bezpečnostní možnosti konfigurace pro vaše prostředí. Další informace najdete v tématu [MongoDB zabezpečení dokumentace](https://docs.mongodb.com/manual/security/).

Další informace o vytváření šablon najdete v tématu [přehled Azure Resource Manageru](../../azure-resource-manager/resource-group-overview.md).

Šablony Azure Resource Manager pomocí rozšíření vlastních skriptů ke stažení a spuštění skriptů na virtuální počítače. Další informace najdete v tématu [pomocí rozšíření vlastních skriptů Azure s virtuálním počítačům s Linuxem](extensions-customscript.md).

