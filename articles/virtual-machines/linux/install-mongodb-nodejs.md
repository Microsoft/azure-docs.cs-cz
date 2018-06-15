---
title: Nainstalujte MongoDB na virtuální počítač s Linuxem pomocí Azure CLI 1.0 | Microsoft Docs
description: Zjistěte, jak nainstalovat a nakonfigurovat MongoDB na virtuální počítač s Linuxem v Azure pomocí modelu nasazení Resource Manager.
services: virtual-machines-linux
documentationcenter: ''
author: iainfoulds
manager: jeconnoc
editor: ''
ms.assetid: 3f55b546-86df-4442-9ef4-8a25fae7b96e
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/11/2017
ms.author: iainfou
ms.openlocfilehash: a334a0b7b3b638229c61eef086b1919b4c303338
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2018
ms.locfileid: "30907883"
---
# <a name="how-to-install-and-configure-mongodb-on-a-linux-vm-using-the-azure-cli-10"></a>Postup instalace a konfigurace MongoDB na virtuální počítač s Linuxem pomocí Azure CLI 1.0
[MongoDB](http://www.mongodb.org) je Oblíbené databáze NoSQL open source a vysoce výkonné. Tento článek ukazuje, jak nainstalovat a nakonfigurovat MongoDB na virtuální počítač s Linuxem v Azure pomocí modelu nasazení Resource Manager. Příklady jsou uvedeny této podrobnosti o tom, jak na:

* [Ručně nainstalujte a nakonfigurujte základní instance MongoDB](#manually-install-and-configure-mongodb-on-a-vm)
* [Vytvořte základní instance MongoDB pomocí šablony Resource Manageru](#create-basic-mongodb-instance-on-centos-using-a-template)
* [Vytvořit komplexní MongoDB horizontálně dělené clusteru s replikou nastaví pomocí šablony Resource Manageru](#create-a-complex-mongodb-sharded-cluster-on-centos-using-a-template)


## <a name="cli-versions-to-complete-the-task"></a>Verze rozhraní příkazového řádku pro dokončení úlohy
K dokončení úlohy můžete využít jednu z následujících verzí rozhraní příkazového řádku:

- Azure CLI 1.0 – naše rozhraní příkazového řádku pro klasické modely nasazení a modely nasazení správy prostředků (tento článek)
- [Azure CLI 2.0](create-cli-complete-nodejs.md) – naše rozhraní příkazového řádku nové generace pro model nasazení správy prostředků


## <a name="manually-install-and-configure-mongodb-on-a-vm"></a>Ručně nainstalujte a nakonfigurujte MongoDB na virtuálním počítači
MongoDB [poskytují pokyny k instalaci](https://docs.mongodb.com/manual/administration/install-on-linux/) pro distribucích systému Linux, včetně Red Hat nebo CentOS, SUSE, Ubuntu a Debian. Následující příklad vytvoří *CentOS* virtuálního počítače pomocí klíče SSH uložené v *~/.ssh/id_rsa.pub*. Odpovězte pokynů pro název účtu úložiště, název DNS a přihlašovací údaje správce:

```azurecli
azure vm quick-create \
    --image-urn CentOS \
    --ssh-publickey-file ~/.ssh/id_rsa.pub 
```

Přihlaste se k virtuálnímu počítači pomocí veřejné adresy IP, zobrazí se na konci předchozího kroku vytváření virtuálních počítačů:

```bash
ssh azureuser@40.78.23.145
```

Chcete-li přidat zdroje instalace pro MongoDB, vytvořte **yum** soubor úložiště následujícím způsobem:

```bash
sudo touch /etc/yum.repos.d/mongodb-org-3.4.repo
```

Otevřete soubor úložišti MongoDB pro úpravy. Přidejte následující řádky:

```sh
[mongodb-org-3.4]
name=MongoDB Repository
baseurl=https://repo.mongodb.org/yum/redhat/$releasever/mongodb-org/3.4/x86_64/
gpgcheck=1
enabled=1
gpgkey=https://www.mongodb.org/static/pgp/server-3.4.asc
```

Nainstalujte MongoDB pomocí **yum** následujícím způsobem:

```bash
sudo yum install -y mongodb-org
```

Ve výchozím nastavení se SELinux vynucuje u CentOS bitové kopie, které brání v přístupu k MongoDB. Instalace nástroje pro správu zásad a konfigurace SELinux umožňující MongoDB pracovat na jeho výchozí port TCP 27017 následujícím způsobem. 

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
Na jednom virtuálním počítači CentOS pomocí následující šablony Azure rychlý start z Githubu, můžete vytvořit základní instance MongoDB. Tato šablona používá k přidání rozšíření vlastních skriptů pro Linux `yum` úložiště pro vaše nově vytvořený CentOS virtuálních počítačů a potom nainstalujte MongoDB.

* [Základní instance MongoDB na CentOS](https://github.com/Azure/azure-quickstart-templates/tree/master/mongodb-on-centos) - https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/mongodb-on-centos/azuredeploy.json

Následující příklad vytvoří skupinu prostředků s názvem `myResourceGroup` v `eastus` oblast. Zadejte vlastní hodnoty následujícím způsobem:

```azurecli
azure group create \
    --name myResourceGroup \
    --location eastus \
    --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/mongodb-on-centos/azuredeploy.json
```

> [!NOTE]
> Azure CLI vrátí můžete výzvu během několika sekund vytváření nasazení, ale instalace a konfigurace trvá několik minut. Zkontrolujte stav nasazení s `azure group deployment show myResourceGroup`, podle toho zadáte název vaší skupiny prostředků. Počkejte **ProvisioningState** ukazuje *úspěšné* před pokusem o SSH k virtuálnímu počítači.

Po nasazení dokončení SSH k virtuálnímu počítači. Získat IP adresu virtuální počítač pomocí `azure vm show` příkaz jako v následujícím příkladu:

```azurecli
azure vm show --resource-group myResourceGroup --name myLinuxVM
```

Téměř na konci výstupu se zobrazí veřejnou IP adresu. SSH k virtuálnímu počítači s IP adresou vašeho virtuálního počítače:

```bash
ssh azureuser@138.91.149.74
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

* [MongoDB horizontálního dělení clusteru na CentOS](https://github.com/Azure/azure-quickstart-templates/tree/master/mongodb-sharding-centos) - https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/mongodb-sharding-centos/azuredeploy.json

> [!WARNING]
> Tento komplexní cluster horizontálně dělené MongoDB nasazení vyžaduje víc než 20 Vcpu, což obvykle představuje výchozí počet virtuálních procesorů na oblast pro předplatné. Otevřete požadavek podporu Azure zvýšení počtu vaše virtuální procesory.

Následující příklad vytvoří skupinu prostředků s názvem *myResourceGroup* v *eastus* oblast. Zadejte vlastní hodnoty následujícím způsobem:

```azurecli
azure group create \
    --name myResourceGroup \
    --location eastus \
    --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/mongodb-sharding-centos/azuredeploy.json
```

> [!NOTE]
> Azure CLI vrátí vám výzvu během několika sekund vytváření nasazení, ale instalace a konfigurace může trvat přes hodinu. Zkontrolujte stav nasazení s `azure group deployment show myResourceGroup`, upraví název vaší skupiny prostředků odpovídajícím způsobem. Počkejte **ProvisioningState** ukazuje *úspěšné* před připojením k virtuálním počítačům.


## <a name="next-steps"></a>Další postup
V těchto příkladech můžete připojit k instanci MongoDB místně z virtuálního počítače. Pokud se chcete připojit k instanci MongoDB z jiného virtuálního počítače nebo sítě, zkontrolujte příslušné [vytvoří skupinu zabezpečení sítě pravidla](nsg-quickstart.md).

Další informace o vytváření šablon najdete v tématu [přehled Azure Resource Manageru](../../azure-resource-manager/resource-group-overview.md).

Šablony Azure Resource Manager pomocí rozšíření vlastních skriptů ke stažení a spuštění skriptů na virtuální počítače. Další informace najdete v tématu [pomocí rozšíření vlastních skriptů Azure s virtuálním počítačům s Linuxem](extensions-customscript.md).

