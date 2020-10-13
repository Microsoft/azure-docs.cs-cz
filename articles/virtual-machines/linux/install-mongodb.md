---
title: Instalace MongoDB na virtuálním počítači se systémem Linux pomocí Azure CLI
description: Přečtěte si, jak nainstalovat a nakonfigurovat MongoDB na virtuálním počítači se systémem Linux iusing v Azure CLI.
author: cynthn
manager: gwallace
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.devlang: azurecli
ms.topic: how-to
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 12/15/2017
ms.author: cynthn
ms.custom: devx-track-azurecli
ms.openlocfilehash: 49a0e48977393aeab7ff93b79e28acc55a87b51a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "87504615"
---
# <a name="how-to-install-and-configure-mongodb-on-a-linux-vm"></a>Jak nainstalovat a nakonfigurovat MongoDB na virtuálním počítači se systémem Linux

[MongoDB](https://www.mongodb.org) je oblíbená Open Source vysoce výkonná databáze NoSQL. V tomto článku se dozvíte, jak nainstalovat a nakonfigurovat MongoDB na virtuálním počítači se systémem Linux pomocí Azure CLI. Zobrazí se příklady, které podrobně popisují:

* [Ruční instalace a konfigurace základní instance MongoDB](#manually-install-and-configure-mongodb-on-a-vm)
* [Vytvoření základní instance MongoDB pomocí šablony Správce prostředků](#create-basic-mongodb-instance-on-centos-using-a-template)
* [Vytvoření komplexního clusteru MongoDB horizontálně dělené se sadami replik pomocí šablony Správce prostředků](#create-a-complex-mongodb-sharded-cluster-on-centos-using-a-template)


## <a name="manually-install-and-configure-mongodb-on-a-vm"></a>Ruční instalace a konfigurace MongoDB na virtuálním počítači
MongoDB [poskytuje pokyny k instalaci](https://docs.mongodb.com/manual/administration/install-on-linux/) pro Linux distribuce, včetně Red Hat/CentOS, SUSE, Ubuntu a Debian. Následující příklad vytvoří virtuální počítač *CentOS* . K vytvoření tohoto prostředí potřebujete mít nainstalované nejnovější rozhraní příkazového [řádku Azure](/cli/azure/install-az-cli2) a přihlásit se k účtu Azure pomocí [AZ Login](/cli/azure/reference-index).

Vytvořte skupinu prostředků pomocí příkazu [az group create](/cli/azure/group). Následující příklad vytvoří skupinu prostředků s názvem *myResourceGroup* v umístění *eastus* :

```azurecli
az group create --name myResourceGroup --location eastus
```

Vytvořte virtuální počítač pomocí příkazu [az vm create](/cli/azure/vm). Následující příklad vytvoří virtuální počítač s názvem *myVM* s uživatelem s názvem *azureuser* s použitím ověřování pomocí veřejného klíče SSH.

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

Chcete-li přidat zdroje instalace pro MongoDB, vytvořte soubor úložiště **Yumu** následujícím způsobem:

```bash
sudo touch /etc/yum.repos.d/mongodb-org-3.6.repo
```

Otevřete soubor úložiště MongoDB pro úpravy, například s `vi` nebo `nano` . Přidejte následující řádky:

```sh
[mongodb-org-3.6]
name=MongoDB Repository
baseurl=https://repo.mongodb.org/yum/redhat/$releasever/mongodb-org/3.6/x86_64/
gpgcheck=1
enabled=1
gpgkey=https://www.mongodb.org/static/pgp/server-3.6.asc
```

Nainstalujte MongoDB pomocí **Yumu** následujícím způsobem:

```bash
sudo yum install -y mongodb-org
```

Ve výchozím nastavení se SELinux vynutilo na CentOS imagí, které vám brání v přístupu k MongoDB. Nainstalujte nástroje pro správu zásad a nakonfigurujte SELinux, aby MongoDB mohl fungovat na svém výchozím portu TCP 27017 následujícím způsobem:

```bash
sudo yum install -y policycoreutils-python
sudo semanage port -a -t mongod_port_t -p tcp 27017
```

Spusťte službu MongoDB následujícím způsobem:

```bash
sudo service mongod start
```

Ověřte instalaci MongoDB připojením pomocí místního `mongo` klienta:

```bash
mongo
```

Nyní otestujte instanci MongoDB přidáním dat a následným hledáním:

```sh
> db
test
> db.foo.insert( { a : 1 } )  
> db.foo.find()  
{ "_id" : ObjectId("57ec477cd639891710b90727"), "a" : 1 }
> exit
```

V případě potřeby nakonfigurujte MongoDB tak, aby se automaticky spouštěla během restartování systému:

```bash
sudo chkconfig mongod on
```


## <a name="create-basic-mongodb-instance-on-centos-using-a-template"></a>Vytvoření základní instance MongoDB na CentOS pomocí šablony
Základní instanci MongoDB můžete vytvořit na jednom virtuálním počítači s CentOS pomocí následující šablony Azure pro rychlý Start z GitHubu. Tato šablona používá rozšíření vlastních skriptů pro Linux k přidání úložiště **Yumu** k nově vytvořenému virtuálnímu počítači s CentOS a instalaci MongoDB.

* [Základní instance MongoDB na CentOS](https://github.com/Azure/azure-quickstart-templates/tree/master/mongodb-on-centos) - https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/mongodb-on-centos/azuredeploy.json

K vytvoření tohoto prostředí potřebujete mít nainstalované nejnovější rozhraní příkazového [řádku Azure](/cli/azure/install-az-cli2) a přihlásit se k účtu Azure pomocí [AZ Login](/cli/azure/reference-index). Nejdřív vytvořte skupinu prostředků pomocí příkazu [az group create](/cli/azure/group). Následující příklad vytvoří skupinu prostředků s názvem *myResourceGroup* v umístění *eastus* :

```azurecli
az group create --name myResourceGroup --location eastus
```

V dalším kroku nasaďte šablonu MongoDB pomocí [AZ Group Deployment Create](/cli/azure/group/deployment). Po zobrazení výzvy zadejte vlastní jedinečné hodnoty pro *newStorageAccountName*, *dnsNameForPublicIP*a uživatelské jméno a heslo správce:

```azurecli
az group deployment create --resource-group myResourceGroup \
  --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/mongodb-on-centos/azuredeploy.json
```

Přihlaste se k virtuálnímu počítači pomocí veřejné adresy DNS vašeho virtuálního počítače. Veřejnou adresu DNS si můžete zobrazit pomocí [AZ VM show](/cli/azure/vm):

```azurecli
az vm show -g myResourceGroup -n myLinuxVM -d --query [fqdns] -o tsv
```

SSH k VIRTUÁLNÍmu počítači pomocí vlastního uživatelského jména a veřejné adresy DNS:

```bash
ssh azureuser@mypublicdns.eastus.cloudapp.azure.com
```

Pomocí místního klienta ověřte instalaci MongoDB následujícím `mongo` způsobem:

```bash
mongo
```

Nyní otestujte instanci přidáním dat a hledáním následujícím způsobem:

```sh
> db
test
> db.foo.insert( { a : 1 } )  
> db.foo.find()  
{ "_id" : ObjectId("57ec477cd639891710b90727"), "a" : 1 }
> exit
```


## <a name="create-a-complex-mongodb-sharded-cluster-on-centos-using-a-template"></a>Vytvoření komplexního clusteru MongoDB horizontálně dělené v CentOS pomocí šablony
Komplexní cluster MongoDB horizontálně dělené můžete vytvořit pomocí následující šablony Azure pro rychlý Start z GitHubu. Tato šablona se řídí [osvědčenými postupy pro cluster MongoDB horizontálně dělené](https://docs.mongodb.com/manual/core/sharded-cluster-components/) , která poskytuje redundanci a vysokou dostupnost. Šablona vytvoří dva horizontálních oddílů se třemi uzly v každé sadě replik. Vytvoří se taky jedna sada replik konfiguračního serveru se třemi uzly a zároveň dva servery **mongos** směrovače, které zajistí konzistenci aplikací z horizontálních oddílů.

* [MongoDB horizontálního dělení cluster v CentOS](https://github.com/Azure/azure-quickstart-templates/tree/master/mongodb-sharding-centos) - https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/mongodb-sharding-centos/azuredeploy.json

> [!WARNING]
> Nasazení tohoto komplexního clusteru MongoDB horizontálně dělené vyžaduje více než 20 jader, což je obvykle výchozí počet jader na oblast pro předplatné. Otevřete žádost o podporu Azure a zvyšte počet jader.

K vytvoření tohoto prostředí potřebujete mít nainstalované nejnovější rozhraní příkazového [řádku Azure](/cli/azure/install-az-cli2) a přihlásit se k účtu Azure pomocí [AZ Login](/cli/azure/reference-index). Nejdřív vytvořte skupinu prostředků pomocí příkazu [az group create](/cli/azure/group). Následující příklad vytvoří skupinu prostředků s názvem *myResourceGroup* v umístění *eastus* :

```azurecli
az group create --name myResourceGroup --location eastus
```

V dalším kroku nasaďte šablonu MongoDB pomocí [AZ Group Deployment Create](/cli/azure/group/deployment). V případě potřeby definujte vlastní názvy prostředků a velikosti, jako třeba pro *mongoAdminUsername*, *sizeOfDataDiskInGB*a *configNodeVmSize*:

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

Nasazení a konfigurace všech instancí virtuálních počítačů může trvat až hodinu. `--no-wait`Příznak se používá na konci předchozího příkazu, který vrátí řízení do příkazového řádku, jakmile bude nasazení šablony přijato platformou Azure. Pak můžete zobrazit stav nasazení pomocí [AZ Group Deployment show](/cli/azure/group/deployment). V následujícím příkladu se zobrazí stav nasazení *myMongoDBCluster* ve skupině prostředků *myResourceGroup* :

```azurecli
az group deployment show \
    --resource-group myResourceGroup \
    --name myMongoDBCluster \
    --query [properties.provisioningState] \
    --output tsv
```

## <a name="next-steps"></a>Další kroky
V těchto příkladech se z virtuálního počítače připojíte k instanci MongoDB místně. Pokud se chcete připojit k instanci MongoDB z jiného virtuálního počítače nebo sítě, ujistěte se, že [jsou vytvořená příslušná pravidla skupiny zabezpečení sítě](nsg-quickstart.md).

Tyto příklady nasazují základní prostředí MongoDB pro účely vývoje. Použijte požadované možnosti konfigurace zabezpečení pro vaše prostředí. Další informace najdete v [dokumentaci zabezpečení MongoDB](https://docs.mongodb.com/manual/security/).

Další informace o vytváření šablon pomocí šablon najdete v [přehledu Azure Resource Manager](../../azure-resource-manager/management/overview.md).

Šablony Azure Resource Manager používají rozšíření vlastních skriptů ke stažení a spuštění skriptů na vašich virtuálních počítačích. Další informace najdete v tématu [použití rozšíření vlastních skriptů Azure s Linux Virtual Machines](../extensions/custom-script-linux.md).
