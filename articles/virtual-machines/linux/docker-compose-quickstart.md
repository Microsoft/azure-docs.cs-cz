---
title: Použití Docker Compose
description: Jak nainstalovat a používat Docker a compose na virtuálních počítačích Linuxu s Azure CLI.
author: cynthn
ms.service: virtual-machines-linux
ms.topic: article
ms.date: 02/14/2019
ms.author: cynthn
ms.openlocfilehash: 434a3ef8c9bc1738252d59a5dca5bec16d85e45e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78970310"
---
# <a name="get-started-with-docker-and-compose-to-define-and-run-a-multi-container-application-in-azure"></a>Začínáme s Dockerem a Compose a definujte a spouštěj aplikaci s více kontejnery v Azure
Pomocí [funkce Compose](https://github.com/docker/compose)použijete jednoduchý textový soubor k definování aplikace skládající se z více kontejnerů Dockeru. Potom spusťte aplikaci v jednom příkazu, který dělá vše pro nasazení definovaného prostředí. Jako příklad tento článek ukazuje, jak rychle nastavit blog WordPress s backendovou databází MariaDB SQL na virtuálním počítači Ubuntu. Můžete také použít Compose nastavit složitější aplikace.

Tento článek byl naposledy testován na 2/14/2019 pomocí [Azure Cloud Shell](https://shell.azure.com/bash) a Azure [CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) verze 2.0.58.

## <a name="create-docker-host-with-azure-cli"></a>Vytvoření hostitele Dockeru pomocí Azure CLI
Nainstalujte nejnovější [azure cli](/cli/azure/install-az-cli2) a přihlaste se k účtu Azure pomocí [az přihlášení](/cli/azure/reference-index).

Nejprve vytvořte skupinu prostředků pro prostředí Dockeru pomocí [vytvoření skupiny az](/cli/azure/group). Následující příklad vytvoří skupinu prostředků s názvem *myResourceGroup* v umístění *eastus:*

```azurecli-interactive
az group create --name myDockerGroup --location eastus
```

Vytvořte soubor s názvem *cloud-init.txt* a vložte následující konfiguraci. Zadáním příkazu `sensible-editor cloud-init.txt` soubor vytvořte a zobrazte seznam editorů k dispozici. 

```yaml
#include https://get.docker.com
```

Teď pomocí příkazu [az vm create](/cli/azure/vm#az-vm-create) vytvořte virtuální počítač. Pomocí parametru `--custom-data` předejte svůj konfigurační soubor cloud-init. Pokud jste konfigurační soubor *cloud-init.txt* uložili mimo aktuální pracovní adresář, zadejte úplnou cestu k němu. Následující příklad vytvoří virtuální ho s názvem *myDockerVM* a otevře port 80 pro webový provoz.

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

                 

## <a name="install-compose"></a>Nainstalovat compose


SSH do nového virtuálního počítače hostitele Dockeru. Zadejte svou vlastní IP adresu.

```bash
ssh azureuser@10.10.111.11
```

Nainstalujte compose na virtuálním počítači.

```bash
sudo apt install docker-compose
```


## <a name="create-a-docker-composeyml-configuration-file"></a>Vytvoření konfiguračního souboru docker-compose.yml
Vytvořte `docker-compose.yml` konfigurační soubor pro definování kontejnerů Dockeru, které se mají spouštět na virtuálním počítači. Soubor určuje bitovou kopii, která má být spuštěna v každém kontejneru, proměnné a závislosti prostředí, porty a propojení mezi kontejnery. Podrobnosti o syntaxi souboru yml naleznete v tématu [Compose file reference](https://docs.docker.com/compose/compose-file/).

Vytvořte soubor *docker-compose.yml.* Pomocí oblíbeného textového editoru můžete do souboru přidat nějaká data. Následující příklad vytvoří soubor s `sensible-editor` výzvou k výběru editoru, který chcete použít.

```bash
sensible-editor docker-compose.yml
```

Vložte následující příklad do souboru Docker Compose. Tato konfigurace používá obrázky z [registru DockerHub](https://registry.hub.docker.com/_/wordpress/) k instalaci WordPress (open source blogů a systém pro správu obsahu) a propojené backendové databáze MariaDB SQL. Zadejte svůj vlastní *MYSQL_ROOT_PASSWORD*.

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

## <a name="start-the-containers-with-compose"></a>Spusťte nádoby s compose
Ve stejném adresáři jako soubor *docker-compose.yml* spusťte následující příkaz (v `docker-compose` závislosti `sudo`na vašem prostředí může být nutné spustit pomocí ):

```bash
sudo docker-compose up -d
```

Tento příkaz spustí kontejnery Dockeru zadané v *docker-compose.yml*. Dokončení tohoto kroku trvá minutu nebo dvě. Zobrazí se výstup podobný následujícímu:

```
Creating wordpress_db_1...
Creating wordpress_wordpress_1...
...
```


Chcete-li ověřit, zda `sudo docker-compose ps`jsou kontejnery nahoře, zadejte . Měli byste vidět přibližně toto:

```
        Name                       Command               State         Ports
-----------------------------------------------------------------------------------
azureuser_db_1          docker-entrypoint.sh mysqld      Up      3306/tcp
azureuser_wordpress_1   docker-entrypoint.sh apach ...   Up      0.0.0.0:80->80/tcp
```

Nyní se můžete připojit k WordPress přímo na virtuálním počítači na portu 80. Otevřete webový prohlížeč a zadejte název IP adresy virtuálního počítače. Nyní byste měli vidět úvodní obrazovku WordPress, kde můžete dokončit instalaci a začít s aplikací.

![Úvodní obrazovka WordPressu](./media/docker-compose-quickstart/wordpressstart.png)

## <a name="next-steps"></a>Další kroky
* Další příklady vytváření a nasazování aplikací s více kontejnery najdete v [odkazu na příkazový řádek compose](https://docs.docker.com/compose/reference/) a [v uživatelské příručce.](https://docs.docker.com/compose/)
* Použijte šablonu Azure Resource Manager, buď vlastní nebo přispěje z [komunity](https://azure.microsoft.com/documentation/templates/), nasadit virtuální počítač Azure s Dockera a aplikace nastavená s Compose. Například [nasazení blogu WordPress se šablonou Dockeru](https://github.com/Azure/azure-quickstart-templates/tree/master/docker-wordpress-mysql) používá Docker a Compose k rychlému nasazení WordPressu s backendem MySQL na virtuálním počítači Ubuntu.
* Zkuste integrovat Docker Compose s clusterem Docker Swarm. Scénáře najdete [v tématu Používání skládání s rojem](https://docs.docker.com/compose/swarm/) pro scénáře.

