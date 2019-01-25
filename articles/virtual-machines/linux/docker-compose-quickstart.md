---
title: Použití Docker Compose na virtuální počítač s Linuxem v Azure | Dokumentace Microsoftu
description: Jak použít Docker a Compose na virtuální počítače s Linuxem pomocí Azure CLI
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
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
ms.author: cynthn
ms.openlocfilehash: aa573b17e8b590f416c4857186cb8989decc4aff
ms.sourcegitcommit: b4755b3262c5b7d546e598c0a034a7c0d1e261ec
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/24/2019
ms.locfileid: "54888630"
---
# <a name="get-started-with-docker-and-compose-to-define-and-run-a-multi-container-application-in-azure"></a>Začínáme s prostředím Docker a Compose pro definování a spouštění vícekontejnerová aplikace v Azure
S [Compose](http://github.com/docker/compose), definovat aplikaci skládající se z několika kontejnerů Dockeru pomocí souboru prostého textu. Potom zprovoznění aplikace stačí jediný příkaz, který obsahuje všechno k nasazení prostředí definované. Jako příklad Tento článek popisuje, jak rychle nastavit blog WordPress pomocí back-endu databáze MariaDB SQL na Virtuálním počítači se systémem Ubuntu. Také vám pomůže vytvořit nastavení složitějších aplikací.


## <a name="set-up-a-linux-vm-as-a-docker-host"></a>Nastavení virtuálního počítače s Linuxem jako hostitele Docker
Vytvoření virtuálního počítače s Linuxem a nastavit ho jako hostitele Docker můžete použít různé postupy Azure a dostupných imagí nebo šablony Resource Manageru na webu Azure Marketplace. Viz například [použití rozšíření Docker VM pro nasazení prostředí](dockerextension.md) rychle vytvořit virtuální počítač s Ubuntu pomocí rozšíření Azure Docker VM s využitím [šablonu pro rychlý Start](https://github.com/Azure/azure-quickstart-templates/tree/master/docker-simple-on-ubuntu). 

Při použití rozšíření Docker VM, váš virtuální počítač je automaticky nastavený jako hostitele Docker a Compose je již nainstalována.


### <a name="create-docker-host-with-azure-cli"></a>Vytvoření hostitele Docker pomocí Azure CLI
Nainstalujte nejnovější [rozhraní příkazového řádku Azure](/cli/azure/install-az-cli2) a přihlaste se k Azure pomocí účtu [az login](/cli/azure/reference-index).

Nejprve vytvořte skupinu prostředků pro vaše prostředí Docker [vytvořit skupiny az](/cli/azure/group#az_group_create). Následující příklad vytvoří skupinu prostředků *myResourceGroup* v umístění *eastus*:

```azurecli
az group create --name myResourceGroup --location eastus
```

V dalším kroku nasaďte virtuální počítač s [vytvořit nasazení skupiny pro az](/cli/azure/group/deployment#az_group_deployment_create) , který obsahuje rozšíření Azure Docker VM z [tuto šablonu Azure Resource Manageru na Githubu](https://github.com/Azure/azure-quickstart-templates/tree/master/docker-simple-on-ubuntu). Po zobrazení výzvy zadejte své vlastní jedinečné hodnoty *newStorageAccountName*, *adminUsername*, *adminPassword*, a *dnsNameForPublicIP*:

```azurecli
az group deployment create --resource-group myResourceGroup \
    --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/docker-simple-on-ubuntu/azuredeploy.json
```

Trvá několik minut na dokončení nasazení.


## <a name="verify-that-compose-is-installed"></a>Ověřte, zda je nainstalován Compose
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

Chcete-li zkontrolujte, jestli Compose je nainstalovaný na virtuálním počítači, spusťte následující příkaz:

```bash
docker-compose --version
```

Zobrazit výstup podobný *docker-compose 1.6.2, sestavení 4d 72027*.

> [!TIP]
> Pokud jste použili jinou metodu pro vytvoření hostitele Docker a muset nainstalovat sami Compose, najdete v článku [Compose dokumentaci](https://github.com/docker/compose/blob/882dc673ce84b0b29cd59b6815cb93f74a6c4134/docs/install.md).


## <a name="create-a-docker-composeyml-configuration-file"></a>Vytvoření konfiguračního souboru docker-compose.yml
Dále vytvoříte `docker-compose.yml` soubor, který je právě text konfigurační soubor, chcete-li definovat kontejnerů Dockeru a spouštět na virtuálním počítači. Tento soubor Určuje obrázek, který ke spuštění v každý kontejner (nebo to může být sestavení ze souboru Dockerfile), nezbytné proměnné prostředí a závislostech, porty a propojení mezi kontejnery. Podrobnosti o syntaxi souboru yml najdete v tématu [vytvořit odkaz na soubor](https://docs.docker.com/compose/compose-file/).

Vytvoření *docker-compose.yml* souboru. Pomocí oblíbeného textového editoru přidáte nějaká data do souboru. Následující příklad vytvoří soubor s výzvou pro `sensible-editor` k výběru editor, který chcete použít:

```bash
sensible-editor docker-compose.yml
```

Následující příklad vložte do souboru Docker Compose. Tato konfigurace používá Image z [Dockerhubu registru](https://registry.hub.docker.com/_/wordpress/) instalace Wordpressu (opensourcové blogů a systém správy obsahu) a propojené back-endu MariaDB SQL database. Zadejte vlastní *MYSQL_ROOT_PASSWORD* následujícím způsobem:

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

Tento příkaz spustí kontejnery Dockeru, zadaný v *docker-compose.yml*. Bude trvat minutu nebo dvě pro tento krok dokončit. Zobrazí výstup podobný následujícímu příkladu:

```bash
Creating wordpress_db_1...
Creating wordpress_wordpress_1...
...
```

> [!NOTE]
> Nezapomeňte použít **-d** možnost spuštění tak, že kontejnery běží na pozadí průběžně.


Chcete-li ověřit, že kontejnery jsou nahoru, zadejte `docker-compose ps`. By měl vypadat přibližně jako:

```bash
        Name                       Command               State         Ports
-----------------------------------------------------------------------------------
azureuser_db_1          docker-entrypoint.sh mysqld      Up      3306/tcp
azureuser_wordpress_1   docker-entrypoint.sh apach ...   Up      0.0.0.0:80->80/tcp
```

Teď můžete připojit wordpressu přímo ve virtuálním počítači na portu 80. Otevřete webový prohlížeč a zadejte název DNS virtuálního počítače (například `http://mypublicdns.eastus.cloudapp.azure.com`). Teď byste měli vidět Wordpressu úvodní obrazovku, kde můžete dokončit instalaci a začít pracovat s aplikací.

![Úvodní obrazovku aplikace WordPress][wordpress_start]

## <a name="next-steps"></a>Další postup
* Přejděte [uživatelská příručka k rozšíření Docker VM](https://github.com/Azure/azure-docker-extension/blob/master/README.md) další možnosti konfigurace Docker a Compose ve virtuálním počítači Dockeru. Například jednou z možností je umístit soubor yml Compose (převést na JSON) přímo v konfiguraci rozšíření Docker VM.
* Podívejte se [vytvořit odkaz na příkazový řádek](http://docs.docker.com/compose/reference/) a [uživatelská příručka](http://docs.docker.com/compose/) Další příklady vytváření a nasazení vícekontejnerových aplikací.
* Pomocí šablony Azure Resource Manageru, buď vaše vlastní nebo některý uživatel z [komunity](https://azure.microsoft.com/documentation/templates/), k nasazení virtuálního počítače Azure s využitím Dockeru a aplikace s Compose. Například [nasazení blog WordPress pomocí Dockeru](https://github.com/Azure/azure-quickstart-templates/tree/master/docker-wordpress-mysql) šablona používá k rychlému nasazení WordPress s MySQL back-endu na Virtuálním počítači se systémem Ubuntu Docker a Compose.
* Zkuste integraci se službou cluster Docker Swarm Docker Compose. Zobrazit [pomocí Compose se Swarmem](https://docs.docker.com/compose/swarm/) pro scénáře.

<!--Image references-->

[wordpress_start]: media/docker-compose-quickstart/WordPress.png
