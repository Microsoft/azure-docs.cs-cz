---
title: Použití Docker Compose
description: Jak nainstalovat a používat Docker a vytvořit na virtuálních počítačích se systémem Linux pomocí Azure CLI.
author: cynthn
ms.service: virtual-machines-linux
ms.topic: how-to
ms.date: 02/14/2019
ms.author: cynthn
ms.custom: devx-track-azurecli
ms.openlocfilehash: e9f824a3b7e1e5e482bbc04c448ca4930bf88678
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "87500942"
---
# <a name="get-started-with-docker-and-compose-to-define-and-run-a-multi-container-application-in-azure"></a>Začínáme s Docker a seznámení s definováním a spuštěním aplikace s více kontejnery v Azure
Při [psaní](https://github.com/docker/compose)použijete jednoduchý textový soubor k definování aplikace sestávající z více kontejnerů Docker. Pak aplikaci vyvoláte v jediném příkazu, který provede všechno k nasazení definovaného prostředí. V tomto článku se dozvíte, jak rychle vytvořit blog WordPress s back-end MariaDB SQL Database na virtuálním počítači s Ubuntu. K nastavení složitějších aplikací můžete použít také možnost vytvořit.

Tento článek byl naposledy testován na 2/14/2019 pomocí [Azure Cloud Shell](https://shell.azure.com/bash) a [Azure CLI](/cli/azure/install-azure-cli) verze 2.0.58.

## <a name="create-docker-host-with-azure-cli"></a>Vytvoření hostitele Docker pomocí Azure CLI
Nainstalujte si nejnovější rozhraní příkazového [řádku Azure](/cli/azure/install-az-cli2) a přihlaste se k účtu Azure pomocí [AZ Login](/cli/azure/reference-index).

Nejdřív vytvořte skupinu prostředků pro prostředí Docker pomocí [AZ Group Create](/cli/azure/group). Následující příklad vytvoří skupinu prostředků s názvem *myResourceGroup* v umístění *eastus* :

```azurecli-interactive
az group create --name myDockerGroup --location eastus
```

Vytvořte soubor s názvem *cloud-init.txt* a vložte následující konfiguraci. Zadáním příkazu `sensible-editor cloud-init.txt` soubor vytvořte a zobrazte seznam editorů k dispozici. 

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

                 

## <a name="install-compose"></a>Nainstalovat sestavení


SSH k novému virtuálnímu počítači hostitele Docker. Zadejte vlastní IP adresu.

```bash
ssh azureuser@10.10.111.11
```

Nainstalujte na virtuální počítač.

```bash
sudo apt install docker-compose
```


## <a name="create-a-docker-composeyml-configuration-file"></a>Vytvoření konfiguračního souboru Docker-Compose. yml
Vytvořte `docker-compose.yml` konfigurační soubor pro definování kontejnerů Docker pro spuštění na virtuálním počítači. Soubor určuje image, která se má spustit na každém kontejneru, potřebné proměnné prostředí a závislosti, porty a propojení mezi kontejnery. Podrobnosti o syntaxi souboru YML naleznete v tématu [Vytvoření odkazu na soubor](https://docs.docker.com/compose/compose-file/).

Vytvořte soubor *Docker-Compose. yml* . K přidání dat do souboru použijte oblíbený textový editor. Následující příklad vytvoří soubor s výzvou `sensible-editor` k výběru editoru, který chcete použít.

```bash
sensible-editor docker-compose.yml
```

Do souboru Docker Compose vložte následující příklad. Tato konfigurace pomocí imagí z [registru dockerhubu](https://registry.hub.docker.com/_/wordpress/) nainstaluje WordPress (Open Source Blogový a systém pro správu obsahu) a propojenou back-end MARIADB databázi SQL. Zadejte vlastní *MYSQL_ROOT_PASSWORD*.

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

## <a name="start-the-containers-with-compose"></a>Spuštění kontejnerů s psaním
Ve stejném adresáři jako soubor *Docker-Compose. yml* spusťte následující příkaz (v závislosti na vašem prostředí budete možná muset spustit `docker-compose` pomocí `sudo` ):

```bash
sudo docker-compose up -d
```

Tento příkaz spustí kontejnery Docker zadané v *Docker-Compose. yml*. Dokončení tohoto kroku trvá minutu nebo dvě. Zobrazí se výstup podobný následujícímu:

```
Creating wordpress_db_1...
Creating wordpress_wordpress_1...
...
```


Chcete-li ověřit, zda jsou kontejnery v seznamu, zadejte `sudo docker-compose ps` . Mělo by se vám zobrazit přibližně toto:

```
        Name                       Command               State         Ports
-----------------------------------------------------------------------------------
azureuser_db_1          docker-entrypoint.sh mysqld      Up      3306/tcp
azureuser_wordpress_1   docker-entrypoint.sh apach ...   Up      0.0.0.0:80->80/tcp
```

Nyní se můžete připojit k WordPress přímo na VIRTUÁLNÍm počítači na portu 80. Otevřete webový prohlížeč a zadejte název IP adresy svého virtuálního počítače. Nyní byste měli vidět úvodní obrazovku WordPress, kde můžete dokončit instalaci a začít s aplikací.

![Úvodní obrazovka WordPress](./media/docker-compose-quickstart/wordpressstart.png)

## <a name="next-steps"></a>Další kroky
* Další příklady sestavování a nasazování aplikací s více kontejnery najdete v příručce k [příkazovému řádku pro psaní](https://docs.docker.com/compose/reference/) a v [uživatelské příručce](https://docs.docker.com/compose/) .
* K nasazení virtuálního počítače Azure s využitím Docker a aplikace nastavenou v sestavách použijte šablonu Azure Resource Manager, kterou vlastníte, nebo jednu z [komunit](https://azure.microsoft.com/documentation/templates/). Například [blog pro nasazení WordPress s použitím šablony Docker](https://github.com/Azure/azure-quickstart-templates/tree/master/docker-wordpress-mysql) používá Docker a napsat k rychlému nasazení WordPressu s back-endu MySQL na virtuálním počítači s Ubuntu.
* Zkuste integraci Docker Compose s clusterem Docker Swarm. Scénáře najdete v tématu [použití Swarm](https://docs.docker.com/compose/swarm/) .
