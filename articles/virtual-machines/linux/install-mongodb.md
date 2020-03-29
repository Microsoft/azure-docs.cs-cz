---
title: Instalace MongoDB na virtuální počítač s Linuxem pomocí azure CLI
description: Přečtěte si, jak nainstalovat a nakonfigurovat MongoDB na linuxovém virtuálním počítači využívajícím rozhraní příkazového příkazu Azure
author: cynthn
manager: gwallace
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 12/15/2017
ms.author: cynthn
ms.openlocfilehash: e1bc7c8a6f97d6dc6bb1d6cb54825425244b2158
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78944886"
---
# <a name="how-to-install-and-configure-mongodb-on-a-linux-vm"></a>Jak nainstalovat a nakonfigurovat MongoDB na virtuálním počítači S II

[MongoDB](https://www.mongodb.org) je populární open-source, vysoce výkonná NoSQL databáze. Tento článek ukazuje, jak nainstalovat a nakonfigurovat MongoDB na virtuálním počítači SIS s Azure CLI. Příklady jsou zobrazeny, že podrobně, jak:

* [Ruční instalace a konfigurace základní instance MongoDB](#manually-install-and-configure-mongodb-on-a-vm)
* [Vytvoření základní instance MongoDB pomocí šablony Správce prostředků](#create-basic-mongodb-instance-on-centos-using-a-template)
* [Vytvoření složitého oddílového clusteru MongoDB se sadami replik pomocí šablony Správce prostředků](#create-a-complex-mongodb-sharded-cluster-on-centos-using-a-template)


## <a name="manually-install-and-configure-mongodb-on-a-vm"></a>Ruční instalace a konfigurace MongoDB na virtuálním počítači
MongoDB [poskytuje pokyny k instalaci](https://docs.mongodb.com/manual/administration/install-on-linux/) pro distribuce Linuxu včetně Red Hat / CentOS, SUSE, Ubuntu a Debian. Následující příklad vytvoří virtuální ho virtuálního montu *CentOS.* K vytvoření tohoto prostředí potřebujete nejnovější [azure cli](/cli/azure/install-az-cli2) nainstalované a přihlášené k účtu Azure pomocí [az přihlášení](/cli/azure/reference-index).

Vytvořte skupinu prostředků pomocí příkazu [az group create](/cli/azure/group). Následující příklad vytvoří skupinu prostředků s názvem *myResourceGroup* v umístění *eastus:*

```azurecli
az group create --name myResourceGroup --location eastus
```

Vytvořte virtuální počítač pomocí příkazu [az vm create](/cli/azure/vm). Následující příklad vytvoří virtuální počítač s názvem *myVM* s uživatelem s názvem *azureuser* pomocí ověřování veřejného klíče SSH

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image CentOS \
    --admin-username azureuser \
    --generate-ssh-keys
```

SSH do virtuálního počítače pomocí vlastního `publicIpAddress` uživatelského jména a uvedené ve výstupu z předchozího kroku:

```bash
ssh azureuser@<publicIpAddress>
```

Chcete-li přidat zdroje instalace pro MongoDB, vytvořte soubor úložiště **yum** takto:

```bash
sudo touch /etc/yum.repos.d/mongodb-org-3.6.repo
```

Otevřete repo soubor MongoDB pro úpravy, například s `vi` nebo `nano`. Přidejte následující řádky:

```sh
[mongodb-org-3.6]
name=MongoDB Repository
baseurl=https://repo.mongodb.org/yum/redhat/$releasever/mongodb-org/3.6/x86_64/
gpgcheck=1
enabled=1
gpgkey=https://www.mongodb.org/static/pgp/server-3.6.asc
```

Nainstalujte MongoDB pomocí **yum** takto:

```bash
sudo yum install -y mongodb-org
```

Ve výchozím nastavení je SELinux vynucen na obrázcích CentOS, které vám brání v přístupu k MongoDB. Nainstalujte nástroje pro správu zásad a nakonfigurujte SELinux tak, aby mongoDB mohl pracovat na svém výchozím portu TCP 27017 následujícím způsobem:

```bash
sudo yum install -y policycoreutils-python
sudo semanage port -a -t mongod_port_t -p tcp 27017
```

Spusťte službu MongoDB takto:

```bash
sudo service mongod start
```

Ověřte instalaci MongoDB připojením `mongo` pomocí místního klienta:

```bash
mongo
```

Nyní otestujte instanci MongoDB přidáním některých dat a následným hledáním:

```sh
> db
test
> db.foo.insert( { a : 1 } )  
> db.foo.find()  
{ "_id" : ObjectId("57ec477cd639891710b90727"), "a" : 1 }
> exit
```

V případě potřeby nakonfigurujte mongoDB tak, aby se automaticky spouštěl během restartování systému:

```bash
sudo chkconfig mongod on
```


## <a name="create-basic-mongodb-instance-on-centos-using-a-template"></a>Vytvoření základní instance MongoDB v CentOS pomocí šablony
Můžete vytvořit základní instanci MongoDB na jednom virtuálním počítači CentOS pomocí následující šablony rychlého startu Azure z GitHubu. Tato šablona používá rozšíření Vlastní skript pro Linux k přidání úložiště **yum** do nově vytvořeného virtuálního počítače CentOS a poté k instalaci MongoDB.

* [Základní instance MongoDB na CentOS](https://github.com/Azure/azure-quickstart-templates/tree/master/mongodb-on-centos) - https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/mongodb-on-centos/azuredeploy.json

K vytvoření tohoto prostředí potřebujete nejnovější [azure cli](/cli/azure/install-az-cli2) nainstalované a přihlášené k účtu Azure pomocí [az přihlášení](/cli/azure/reference-index). Nejdřív vytvořte skupinu prostředků pomocí příkazu [az group create](/cli/azure/group). Následující příklad vytvoří skupinu prostředků s názvem *myResourceGroup* v umístění *eastus:*

```azurecli
az group create --name myResourceGroup --location eastus
```

Dále nasaďte šablonu MongoDB s [vytvořením nasazení skupiny az](/cli/azure/group/deployment). Po zobrazení výzvy zadejte vlastní jedinečné hodnoty pro *newStorageAccountName*, *dnsNameForPublicIP*a uživatelské jméno a heslo správce:

```azurecli
az group deployment create --resource-group myResourceGroup \
  --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/mongodb-on-centos/azuredeploy.json
```

Přihlaste se k virtuálnímu počítači pomocí veřejné DNS adresy vašeho virtuálního počítače. Můžete zobrazit veřejnou ADRESU DNS s [az vm show](/cli/azure/vm):

```azurecli
az vm show -g myResourceGroup -n myLinuxVM -d --query [fqdns] -o tsv
```

SSH do virtuálního počítače pomocí vlastního uživatelského jména a veřejné ADRESY DNS:

```bash
ssh azureuser@mypublicdns.eastus.cloudapp.azure.com
```

Ověřte instalaci MongoDB takto `mongo` připojením pomocí místního klienta:

```bash
mongo
```

Nyní otestujte instanci přidáním některých dat a hledáním následujícím způsobem:

```sh
> db
test
> db.foo.insert( { a : 1 } )  
> db.foo.find()  
{ "_id" : ObjectId("57ec477cd639891710b90727"), "a" : 1 }
> exit
```


## <a name="create-a-complex-mongodb-sharded-cluster-on-centos-using-a-template"></a>Vytvoření komplexního stonožného clusteru MongoDB na CentOS pomocí šablony
Můžete vytvořit komplexní MongoDB oddílového pevného clusteru pomocí následující šablony Azure quickstart z GitHubu. Tato šablona se řídí [osvědčenými postupy oddílového děleného clusteru MongoDB,](https://docs.mongodb.com/manual/core/sharded-cluster-components/) které poskytují redundanci a vysokou dostupnost. Šablona vytvoří dva úlomky se třemi uzly v každé sadě replik. Je také vytvořena jedna sada replik konfiguračního serveru se třemi uzly a **dvěma servery směrovačů mongos,** které zajišťují konzistenci aplikací z různých oddílů.

* [MongoDB Sharding Cluster na CentOS](https://github.com/Azure/azure-quickstart-templates/tree/master/mongodb-sharding-centos) - https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/mongodb-sharding-centos/azuredeploy.json

> [!WARNING]
> Nasazení tohoto složitého oddílového clusteru MongoDB vyžaduje více než 20 jader, což je obvykle výchozí počet jader na oblast pro předplatné. Otevřete žádost o podporu Azure a zvyšte počet základních objektů.

K vytvoření tohoto prostředí potřebujete nejnovější [azure cli](/cli/azure/install-az-cli2) nainstalované a přihlášené k účtu Azure pomocí [az přihlášení](/cli/azure/reference-index). Nejdřív vytvořte skupinu prostředků pomocí příkazu [az group create](/cli/azure/group). Následující příklad vytvoří skupinu prostředků s názvem *myResourceGroup* v umístění *eastus:*

```azurecli
az group create --name myResourceGroup --location eastus
```

Dále nasaďte šablonu MongoDB s [vytvořením nasazení skupiny az](/cli/azure/group/deployment). V případě potřeby definujte vlastní názvy a velikosti prostředků, například pro *mongoAdminUsername*, *sizeOfDataDiskInGB*a *configNodeVmSize*:

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

Toto nasazení může trvat více než hodinu nasazení a konfigurace všech instancí virtuálních aplikací. Příznak `--no-wait` se používá na konci předchozího příkazu k vrácení řízení do příkazového řádku, jakmile bylo nasazení šablony přijato platformou Azure. Stav nasazení pak můžete zobrazit pomocí [výstavy nasazení skupiny AZ](/cli/azure/group/deployment). Následující příklad zobrazuje stav nasazení *myMongoDBCluster* ve skupině prostředků *myResourceGroup:*

```azurecli
az group deployment show \
    --resource-group myResourceGroup \
    --name myMongoDBCluster \
    --query [properties.provisioningState] \
    --output tsv
```

## <a name="next-steps"></a>Další kroky
V těchto příkladech se připojíte k instanci MongoDB místně z virtuálního počítači. Pokud se chcete připojit k instanci MongoDB z jiného virtuálního počítači nebo sítě, ujistěte se, že [jsou vytvořena příslušná pravidla skupiny zabezpečení sítě](nsg-quickstart.md).

Tyto příklady nasadit základní prostředí MongoDB pro účely vývoje. Použijte požadované možnosti konfigurace zabezpečení pro vaše prostředí. Další informace naleznete v [dokumentech zabezpečení MongoDB](https://docs.mongodb.com/manual/security/).

Další informace o vytváření pomocí šablon najdete v přehledu [Správce prostředků Azure](../../azure-resource-manager/management/overview.md).

Šablony Azure Resource Manager používají rozšíření vlastní skript ke stažení a spuštění skriptů na virtuálních počítačích. Další informace najdete [v tématu Použití rozšíření vlastního skriptu Azure s virtuálními počítači SIP](extensions-customscript.md).

