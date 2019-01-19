---
title: Instalace MongoDB na virtuálním počítači s Linuxem pomocí Azure CLI | Dokumentace Microsoftu
description: Zjistěte, jak nainstalovat a nakonfigurovat MongoDB na 1vytváření virtuálního počítače s Linuxem rozhraní příkazového řádku Azure
services: virtual-machines-linux
documentationcenter: ''
author: zr-msft
manager: jeconnoc
editor: ''
ms.assetid: 3f55b546-86df-4442-9ef4-8a25fae7b96e
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 12/15/2017
ms.author: zarhoads
ms.openlocfilehash: 138435664222ff268069465aa98a53f0902a9f99
ms.sourcegitcommit: 82cdc26615829df3c57ee230d99eecfa1c4ba459
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/19/2019
ms.locfileid: "54412695"
---
# <a name="how-to-install-and-configure-mongodb-on-a-linux-vm"></a>Jak nainstalovat a nakonfigurovat MongoDB na virtuálním počítači s Linuxem

[MongoDB](http://www.mongodb.org) je Oblíbené open source a vysoce výkonnou databází NoSQL. V tomto článku se dozvíte, jak nainstalovat a nakonfigurovat MongoDB na virtuálním počítači s Linuxem pomocí Azure CLI. Příklady jsou uvedeny této podrobnosti o tom, jak do:

* [Ručně nainstalujte a nakonfigurujte základní instanci databáze MongoDB](#manually-install-and-configure-mongodb-on-a-vm)
* [Vytvoření základní instanci databáze MongoDB pomocí šablony Resource Manageru](#create-basic-mongodb-instance-on-centos-using-a-template)
* [Vytvoření komplexní MongoDB horizontálně dělené clusteru s replikou nastaví pomocí šablony Resource Manageru](#create-a-complex-mongodb-sharded-cluster-on-centos-using-a-template)


## <a name="manually-install-and-configure-mongodb-on-a-vm"></a>Ručně nainstalujte a nakonfigurujte MongoDB na virtuálním počítači
MongoDB [poskytují pokyny k instalaci](https://docs.mongodb.com/manual/administration/install-on-linux/) pro distribuce Linuxu, včetně Red Hat nebo CentOS, SUSE, Ubuntu nebo Debian. Následující příklad vytvoří *CentOS* virtuálního počítače. K vytvoření tohoto prostředí, budete potřebovat nejnovější [rozhraní příkazového řádku Azure](/cli/azure/install-az-cli2) nainstalovaný a přihlášení k účtu Azure pomocí [az login](/cli/azure/reference-index).

Vytvořte skupinu prostředků pomocí příkazu [az group create](/cli/azure/group). Následující příklad vytvoří skupinu prostředků *myResourceGroup* v umístění *eastus*:

```azurecli
az group create --name myResourceGroup --location eastus
```

Vytvořte virtuální počítač pomocí příkazu [az vm create](/cli/azure/vm#az_vm_create). Následující příklad vytvoří virtuální počítač s názvem *myVM* jako uživatel s názvem *azureuser* pomocí ověření veřejného klíče SSH

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image CentOS \
    --admin-username azureuser \
    --generate-ssh-keys
```

SSH k virtuálnímu počítači pomocí své uživatelské jméno a `publicIpAddress` uvedená ve výstupu z předchozího kroku:

```bash
ssh azureuser@<publicIpAddress>
```

Chcete-li přidat zdroje instalace pro MongoDB, vytvořte **yumu** soubor úložiště následujícím způsobem:

```bash
sudo touch /etc/yum.repos.d/mongodb-org-3.6.repo
```

Otevřete soubor úložiště MongoDB pro úpravy, jako například s `vi` nebo `nano`. Přidejte následující řádky:

```sh
[mongodb-org-3.6]
name=MongoDB Repository
baseurl=https://repo.mongodb.org/yum/redhat/$releasever/mongodb-org/3.6/x86_64/
gpgcheck=1
enabled=1
gpgkey=https://www.mongodb.org/static/pgp/server-3.6.asc
```

Nainstalujte MongoDB pomocí **yumu** následujícím způsobem:

```bash
sudo yum install -y mongodb-org
```

Ve výchozím nastavení je vynucena SELinux na CentOS imagích, které vám brání v přístupu k MongoDB. Instalace nástroje pro správu zásad a konfigurace SELinux umožňující MongoDB má operace provést její výchozí port TCP 27017 následujícím způsobem:

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

Teď otestujte přidáním nějaká data a poté vyhledáním instanci databáze MongoDB:

```sh
> db
test
> db.foo.insert( { a : 1 } )  
> db.foo.find()  
{ "_id" : ObjectId("57ec477cd639891710b90727"), "a" : 1 }
> exit
```

V případě potřeby nakonfigurujte MongoDB na automatické spouštění během restartování systému:

```bash
sudo chkconfig mongod on
```


## <a name="create-basic-mongodb-instance-on-centos-using-a-template"></a>Vytvoření základní instanci databáze MongoDB na CentOS pomocí šablony
Základní instance MongoDB můžete vytvořit na jednoho virtuálního počítače CentOS pomocí následující šablony rychlý start Azure z Githubu. Tato šablona používá rozšíření vlastních skriptů pro Linux pro přidání **yumu** úložiště k nově vytvořenému virtuálnímu počítači CentOS a nainstalujte MongoDB.

* [Základní instance MongoDB na CentOS](https://github.com/Azure/azure-quickstart-templates/tree/master/mongodb-on-centos) - https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/mongodb-on-centos/azuredeploy.json

K vytvoření tohoto prostředí, budete potřebovat nejnovější [rozhraní příkazového řádku Azure](/cli/azure/install-az-cli2) nainstalovaný a přihlášení k účtu Azure pomocí [az login](/cli/azure/reference-index). Nejdřív vytvořte skupinu prostředků pomocí příkazu [az group create](/cli/azure/group). Následující příklad vytvoří skupinu prostředků *myResourceGroup* v umístění *eastus*:

```azurecli
az group create --name myResourceGroup --location eastus
```

V dalším kroku nasaďte šablonu MongoDB s [vytvořit nasazení skupiny pro az](/cli/azure/group/deployment#az_group_deployment_create). Po zobrazení výzvy zadejte své vlastní jedinečné hodnoty *newStorageAccountName*, *dnsNameForPublicIP*, uživatelské jméno admin a heslo:

```azurecli
az group deployment create --resource-group myResourceGroup \
  --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/mongodb-on-centos/azuredeploy.json
```

Přihlaste se k virtuálnímu počítači pomocí veřejné adresy DNS vašeho virtuálního počítače. Můžete zobrazit veřejnou adresu DNS s [az vm show](/cli/azure/vm):

```azurecli
az vm show -g myResourceGroup -n myLinuxVM -d --query [fqdns] -o tsv
```

SSH k virtuálnímu počítači pomocí uživatelského jména a veřejná adresa DNS:

```bash
ssh azureuser@mypublicdns.eastus.cloudapp.azure.com
```

Ověření instalace MongoDB se připojíte pomocí místní `mongo` klienta následujícím způsobem:

```bash
mongo
```

Otestujte teď instance přidáním nějaká data a hledání následujícím způsobem:

```sh
> db
test
> db.foo.insert( { a : 1 } )  
> db.foo.find()  
{ "_id" : ObjectId("57ec477cd639891710b90727"), "a" : 1 }
> exit
```


## <a name="create-a-complex-mongodb-sharded-cluster-on-centos-using-a-template"></a>Vytvořte Cluster horizontálně dělené komplexní MongoDB na CentOS pomocí šablony
Můžete vytvořit cluster komplexní horizontálně dělené MongoDB pomocí následující šablony rychlý start Azure z Githubu. Tato šablona se řídí [MongoDB horizontálně dělené clusteru osvědčené postupy](https://docs.mongodb.com/manual/core/sharded-cluster-components/) zajistit redundanci a vysokou dostupnost. Šablona vytvoří dva oddíly se třemi uzly v každé sady replik. Jeden konfigurační server nastavte se třemi uzly také vytvoření repliky, plus dvě **mongos** směrovače serverů pro zajištění konzistence k aplikacím z mezi horizontální oddíly.

* [MongoDB horizontálního dělení clusteru na CentOS](https://github.com/Azure/azure-quickstart-templates/tree/master/mongodb-sharding-centos) - https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/mongodb-sharding-centos/azuredeploy.json

> [!WARNING]
> Nasazení clusteru horizontálně dělené tento komplexní MongoDB vyžaduje více než 20 jader, což je obvykle výchozí počet jader na oblast a předplatné. Otevřete žádost o podporu Azure o navýšení vaší počet jader.

K vytvoření tohoto prostředí, budete potřebovat nejnovější [rozhraní příkazového řádku Azure](/cli/azure/install-az-cli2) nainstalovaný a přihlášení k účtu Azure pomocí [az login](/cli/azure/reference-index). Nejdřív vytvořte skupinu prostředků pomocí příkazu [az group create](/cli/azure/group). Následující příklad vytvoří skupinu prostředků *myResourceGroup* v umístění *eastus*:

```azurecli
az group create --name myResourceGroup --location eastus
```

V dalším kroku nasaďte šablonu MongoDB s [vytvořit nasazení skupiny pro az](/cli/azure/group/deployment#az_group_deployment_create). Definujte vlastní prostředek názvy a velikosti místech, jako u *mongoAdminUsername*, *sizeOfDataDiskInGB*, a *configNodeVmSize*:

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

Toto nasazení může trvat přes hodinu k nasazení a konfiguraci všech instancí virtuálních počítačů. `--no-wait` Příznak se používá na konci předchozího příkazu k vrácení ovládacího prvku na příkazový řádek po nasazení šablony se přijala platformou Azure. Pak můžete zobrazit stav nasazení s [az skupiny nasazení zobrazit](/cli/azure/group/deployment#az_group_deployment_show). Následující příklad zobrazí stav *myMongoDBCluster* nasazení v *myResourceGroup* skupina prostředků:

```azurecli
az group deployment show \
    --resource-group myResourceGroup \
    --name myMongoDBCluster \
    --query [properties.provisioningState] \
    --output tsv
```

## <a name="next-steps"></a>Další postup
V těchto příkladech můžete připojit k instanci MongoDB místně z virtuálního počítače. Pokud se chcete připojit k instanci MongoDB z jiného virtuálního počítače nebo sítě, zajistit odpovídající [pravidla skupiny zabezpečení sítě jsou vytvořeny](nsg-quickstart.md).

Tyto příklady nasadit prostředí základní MongoDB pro účely vývoje. Použijte požadované konfigurace možnosti zabezpečení pro vaše prostředí. Další informace najdete v tématu [MongoDB zabezpečení dokumentace](https://docs.mongodb.com/manual/security/).

Další informace o vytváření s použitím šablony, najdete v článku [přehled Azure Resource Manageru](../../azure-resource-manager/resource-group-overview.md).

Šablony Azure Resource Manageru pomocí rozšíření vlastních skriptů můžete stáhnout a spustit skripty na virtuálních počítačích. Další informace najdete v tématu [pomocí rozšíření vlastních skriptů Azure s virtuální počítače s Linuxem](extensions-customscript.md).

