---
title: Pomocí Docker Compose na virtuální počítač s Linuxem v Azure | Microsoft Docs
description: Jak používat Docker a vytvářené na virtuální počítače s Linuxem pomocí rozhraní příkazového řádku Azure
services: virtual-machines-linux
documentationcenter: ''
author: iainfoulds
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 02ab8cf9-318d-4a28-9d0c-4a31dccc2a84
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 12/18/2017
ms.author: iainfou
ms.openlocfilehash: 387f3095e8eebce3fa6c2f47ffc87995e65bfe2b
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/09/2018
ms.locfileid: "29848402"
---
# <a name="get-started-with-docker-and-compose-to-define-and-run-a-multi-container-application-in-azure"></a>Začínáme s Docker a vytvářené definovat a spuštění aplikace s více kontejnerů v Azure
S [vytvářené](http://github.com/docker/compose), používáte k definování aplikace, který se skládá z několika kontejnerů Docker jednoduchý textový soubor. Pak začne pracovat aplikace v jednom příkaz, který nemá všechno k nasazení prostředí definované. Jako příklad Tento článek ukazuje, jak rychle nastavit blog WordPress pomocí MariaDB SQL database na virtuálního počítače s Ubuntu back-end. Můžete taky vytvořit složitější aplikace můžete nastavit.


## <a name="set-up-a-linux-vm-as-a-docker-host"></a>Nastavení virtuálního počítače s Linuxem jako hostitele Docker
Vytvoření virtuálního počítače s Linuxem a nastavit jako hostitele Docker, můžete použít různé postupy Azure a dostupných imagí nebo šablony Resource Manageru v Azure Marketplace. Například v tématu [pomocí Docker rozšíření virtuálního počítače k nasazení prostředí](dockerextension.md) k rychlému vytvoření virtuálního počítače s Ubuntu pomocí rozšíření virtuálního počítače Azure Docker pomocí [šablony rychlý Start](https://github.com/Azure/azure-quickstart-templates/tree/master/docker-simple-on-ubuntu). 

Pokud používáte rozšíření virtuálního počítače Docker, virtuální počítač je automaticky nastavený jako hostitel Docker a Compose je již nainstalován.


### <a name="create-docker-host-with-azure-cli-20"></a>Vytvořit hostitele Docker s Azure CLI 2.0
Nainstalujte si nejnovější verzi [Azure CLI 2.0](/cli/azure/install-az-cli2) a přihlaste se k Azure účet pomocí [az přihlášení](/cli/azure/reference-index#az_login).

Nejprve vytvořte skupinu prostředků pro vaše prostředí Docker s [vytvořit skupinu az](/cli/azure/group#az_group_create). Následující příklad vytvoří skupinu prostředků *myResourceGroup* v umístění *eastus*:

```azurecli
az group create --name myResourceGroup --location eastus
```

V dalším kroku nasaďte virtuální počítač s [vytvořit nasazení skupiny az](/cli/azure/group/deployment#az_group_deployment_create) obsahující rozšíření virtuálního počítače Azure Docker z [této šablony Azure Resource Manageru na Githubu](https://github.com/Azure/azure-quickstart-templates/tree/master/docker-simple-on-ubuntu). Po zobrazení výzvy zadejte své vlastní jedinečné hodnoty pro *newStorageAccountName*, *adminUsername*, *adminPassword*, a *dnsNameForPublicIP*:

```azurecli
az group deployment create --resource-group myResourceGroup \
    --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/docker-simple-on-ubuntu/azuredeploy.json
```

Jak dlouho trvá několik minut na dokončení nasazení.


## <a name="verify-that-compose-is-installed"></a>Ověřte, zda je nainstalován Compose
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

Pokud chcete zkontrolovat, že Compose je nainstalovaný na Virtuálním počítači, spusťte následující příkaz:

```bash
docker-compose --version
```

Zobrazí výstup podobný *docker compose 1.6.2, sestavení 4d 72027*.

> [!TIP]
> Pokud jste použili jinou metodu a vytvořit hostitele Docker muset nainstalovat vytvořit sami, najdete v článku [tvoří dokumentaci](https://github.com/docker/compose/blob/882dc673ce84b0b29cd59b6815cb93f74a6c4134/docs/install.md).


## <a name="create-a-docker-composeyml-configuration-file"></a>Vytvořte konfigurační soubor docker-compose.yml
Dále vytvoříte `docker-compose.yml` souboru, který je právě text konfiguračního souboru k definování kontejnery Docker ke spuštění ve virtuálním počítači. Určuje soubor bitové kopie ke spuštění na každý kontejner (nebo to může být sestavení z soubor Docker), nezbytné proměnné prostředí a závislosti, porty a odkazů mezi kontejnery. Informace o syntaxi souboru yml v [vytvořit odkaz na soubor](https://docs.docker.com/compose/compose-file/).

Vytvoření *docker-compose.yml* souboru. Pomocí svém oblíbeném textovém editoru přidejte některá data do souboru. Následující příklad vytvoří soubor s výzvou pro `sensible-editor` vybrat editor, který chcete použít:

```bash
sensible-editor docker-compose.yml
```

Následující příklad vložte do souboru Docker Compose. Tato konfigurace používá bitové kopie z [DockerHub registru](https://registry.hub.docker.com/_/wordpress/) instalace WordPress (s otevřeným zdrojem blogu a systém správy obsahu) a propojené back-end MariaDB SQL database. Zadejte vlastní *MYSQL_ROOT_PASSWORD* následujícím způsobem:

```sh
wordpress:
  image: wordpress
  links:
    - db:mysql
  ports:
    - 80:80

db:
  image: mariadb
  environment:
    MYSQL_ROOT_PASSWORD: <your password>
```

## <a name="start-the-containers-with-compose"></a>Kontejnery začínat Compose
Ve stejném adresáři jako vaše *docker-compose.yml* souboru, spusťte následující příkaz (v závislosti na vašem prostředí, možná budete muset spustit `docker-compose` pomocí `sudo`):

```bash
docker-compose up -d
```

Tento příkaz spustí kontejnery Docker zadaný v *docker-compose.yml*. Bude trvat minutu nebo dvě pro tento krok dokončit. Zobrazí výstup, podobně jako v následujícím příkladu:

```bash
Creating wordpress_db_1...
Creating wordpress_wordpress_1...
...
```

> [!NOTE]
> Nezapomeňte použít **-d** možnost spuštění tak, aby kontejnery spouštět nepřetržitě na pozadí.


Chcete-li ověřit, zda jsou kontejnery, zadejte `docker-compose ps`. Měli byste vidět něco podobného jako:

```bash
        Name                       Command               State         Ports
-----------------------------------------------------------------------------------
azureuser_db_1          docker-entrypoint.sh mysqld      Up      3306/tcp
azureuser_wordpress_1   docker-entrypoint.sh apach ...   Up      0.0.0.0:80->80/tcp
```

Nyní můžete připojit k WordPress přímo na virtuálním počítači na portu 80. Otevřete webový prohlížeč a zadejte název DNS vašeho virtuálního počítače (například `http://mypublicdns.eastus.cloudapp.azure.com`). Teď byste měli vidět WordPress úvodní obrazovce, kde můžete dokončíte instalaci a začít pracovat s aplikací.

![WordPress úvodní obrazovka][wordpress_start]

## <a name="next-steps"></a>Další postup
* Přejděte na [uživatelská příručka k rozšíření virtuálního počítače Docker](https://github.com/Azure/azure-docker-extension/blob/master/README.md) další možnosti konfigurace Docker a vytvářené v Docker virtuálního počítače. Jednou z možností je například umístění souboru yml vytvářené (převést na JSON) přímo v konfiguraci rozšíření Docker virtuálního počítače.
* Podívejte se [tvoří reference k příkazovému řádku](http://docs.docker.com/compose/reference/) a [uživatelská příručka](http://docs.docker.com/compose/) Další příklady vytváření a nasazování aplikací pro službu kontejneru.
* Pomocí šablony Azure Resource Manager, buď vaše vlastní nebo jeden podílí z [komunity](https://azure.microsoft.com/documentation/templates/), k nasazení virtuálního počítače Azure s Docker a nastavit vytvářené aplikace. Například [nasazení blog WordPress pomocí Docker](https://github.com/Azure/azure-quickstart-templates/tree/master/docker-wordpress-mysql) šablona používá Docker a skládání s back-end databáze MySQL na virtuálního počítače s Ubuntu rychle nasadit WordPress.
* Zkuste integraci Docker Compose do clusteru s podporou Docker Swarm. V tématu [pomocí tvoří s Swarm](https://docs.docker.com/compose/swarm/) pro scénáře.

<!--Image references-->

[wordpress_start]: media/docker-compose-quickstart/WordPress.png
