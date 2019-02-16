---
title: Použití Docker Compose na virtuální počítač s Linuxem v Azure | Dokumentace Microsoftu
description: Jak nainstalovat a používat Docker a Compose na virtuální počítače s Linuxem pomocí Azure CLI
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
ms.date: 02/14/2019
ms.author: cynthn
ms.openlocfilehash: 3aa3b29ef44d3efb21237dc0d82a1ee6e99e729b
ms.sourcegitcommit: d2329d88f5ecabbe3e6da8a820faba9b26cb8a02
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/16/2019
ms.locfileid: "56328941"
---
# <a name="get-started-with-docker-and-compose-to-define-and-run-a-multi-container-application-in-azure"></a>Začínáme s prostředím Docker a Compose pro definování a spouštění vícekontejnerová aplikace v Azure
S [Compose](http://github.com/docker/compose), definovat aplikaci skládající se z několika kontejnerů Dockeru pomocí souboru prostého textu. Potom zprovoznění aplikace stačí jediný příkaz, který obsahuje všechno k nasazení prostředí definované. Jako příklad Tento článek popisuje, jak rychle nastavit blog WordPress pomocí back-endu databáze MariaDB SQL na Virtuálním počítači se systémem Ubuntu. Také vám pomůže vytvořit nastavení složitějších aplikací.

Tento článek byl testován poslední o 2/14/2019 používání [Azure Cloud Shell](https://shell.azure.com/bash) a [rozhraní příkazového řádku Azure](https://docs.microsoft.com/cli/azure/install-azure-cli) verze 2.0.58.

## <a name="create-docker-host-with-azure-cli"></a>Vytvoření hostitele Docker pomocí Azure CLI
Nainstalujte nejnovější [rozhraní příkazového řádku Azure](/cli/azure/install-az-cli2) a přihlaste se k Azure pomocí účtu [az login](/cli/azure/reference-index).

Nejprve vytvořte skupinu prostředků pro vaše prostředí Docker [vytvořit skupiny az](/cli/azure/group). Následující příklad vytvoří skupinu prostředků *myResourceGroup* v umístění *eastus*:

```azurecli-interactive
az group create --name myDockerGroup --location eastus
```

Vytvořte soubor s názvem *cloud-init.txt* a vložte do něj následující konfiguraci. Zadáním příkazu `sensible-editor cloud-init.txt` soubor vytvořte a zobrazte seznam editorů k dispozici. 

```yaml
#include https://get.docker.com
```

Teď pomocí příkazu [az vm create](/cli/azure/vm#az-vm-create) vytvořte virtuální počítač. Pomocí parametru `--custom-data` předejte svůj konfigurační soubor cloud-init. Pokud jste konfigurační soubor *cloud-init.txt* uložili mimo aktuální pracovní adresář, zadejte úplnou cestu k němu. Následující příklad vytvoří virtuální počítač s názvem *myDockerVM* a otevře port 80 pro webový provoz.

```azurecli-interactive
az vm create \
    --resource-group myDockerGroup \
    --name myDockerVM \
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys \
    --custom-data cloud-init.txt
az vm open-port --port 80 \
    --resource-group myDockerGroup \
    --name myDockerVM
```

Vytvoření virtuálního počítače, instalace balíčků a spuštění aplikace trvá několik minut. Když vás Azure CLI vrátí na příkazový řádek, na pozadí stále poběží úlohy. Po vytvoření virtuálního počítače si poznamenejte hodnotu `publicIpAddress` zobrazenou v Azure CLI. 

                 

## <a name="install-compose"></a>Instalace Compose


Do nového hostitele Docker virtuálního počítače připojte přes SSH. Zadejte adresu IP.

```bash
ssh azureuser@10.10.111.11
```

Instalace Compose na virtuálním počítači.

```bash
sudo apt install docker-compose
```


## <a name="create-a-docker-composeyml-configuration-file"></a>Vytvoření konfiguračního souboru docker-compose.yml
Vytvoření `docker-compose.yml` konfiguračního souboru k definování kontejnerů Dockeru a spouštět na virtuálním počítači. Tento soubor Určuje obraz, který běží na každý kontejner, nezbytné proměnné prostředí a závislostech, porty a odkazů mezi kontejnery. Podrobnosti o syntaxi souboru yml najdete v tématu [vytvořit odkaz na soubor](https://docs.docker.com/compose/compose-file/).

Vytvoření *docker-compose.yml* souboru. Pomocí oblíbeného textového editoru přidáte nějaká data do souboru. Následující příklad vytvoří soubor s výzvou pro `sensible-editor` k výběru editor, který chcete použít.

```bash
sensible-editor docker-compose.yml
```

Následující příklad vložte do souboru Docker Compose. Tato konfigurace používá Image z [Dockerhubu registru](https://registry.hub.docker.com/_/wordpress/) instalace Wordpressu (opensourcové blogů a systém správy obsahu) a propojené back-endu MariaDB SQL database. Zadejte vlastní *MYSQL_ROOT_PASSWORD*.

```yml
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
sudo docker-compose up -d
```

Tento příkaz spustí kontejnery Dockeru, zadaný v *docker-compose.yml*. Bude trvat minutu nebo dvě pro tento krok dokončit. Se zobrazí výstup podobný následujícímu:

```
Creating wordpress_db_1...
Creating wordpress_wordpress_1...
...
```


Chcete-li ověřit, že kontejnery jsou nahoru, zadejte `sudo docker-compose ps`. By měl vypadat přibližně jako:

```
        Name                       Command               State         Ports
-----------------------------------------------------------------------------------
azureuser_db_1          docker-entrypoint.sh mysqld      Up      3306/tcp
azureuser_wordpress_1   docker-entrypoint.sh apach ...   Up      0.0.0.0:80->80/tcp
```

Teď můžete připojit wordpressu přímo ve virtuálním počítači na portu 80. Otevřete webový prohlížeč a zadejte název IP adresu vašeho virtuálního počítače. Teď byste měli vidět Wordpressu úvodní obrazovku, kde můžete dokončit instalaci a začít pracovat s aplikací.

![Úvodní obrazovku aplikace WordPress](./media/docker-compose-quickstart/wordpressstart.png)

## <a name="next-steps"></a>Další postup
* Podívejte se [vytvořit odkaz na příkazový řádek](http://docs.docker.com/compose/reference/) a [uživatelská příručka](http://docs.docker.com/compose/) Další příklady vytváření a nasazení vícekontejnerových aplikací.
* Pomocí šablony Azure Resource Manageru, buď vaše vlastní nebo některý uživatel z [komunity](https://azure.microsoft.com/documentation/templates/), k nasazení virtuálního počítače Azure s využitím Dockeru a aplikace s Compose. Například [nasazení blog WordPress pomocí Dockeru](https://github.com/Azure/azure-quickstart-templates/tree/master/docker-wordpress-mysql) šablona používá k rychlému nasazení WordPress s MySQL back-endu na Virtuálním počítači se systémem Ubuntu Docker a Compose.
* Zkuste integraci se službou cluster Docker Swarm Docker Compose. Zobrazit [pomocí Compose se Swarmem](https://docs.docker.com/compose/swarm/) pro scénáře.

