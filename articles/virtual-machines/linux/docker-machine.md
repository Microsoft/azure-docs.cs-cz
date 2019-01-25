---
title: Použití Docker Machine k vytvoření hostitelé s Linuxem v Azure | Dokumentace Microsoftu
description: Popisuje způsob použití Docker Machine k vytvoření hostitelů Docker v Azure.
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: tysonn
ms.assetid: 164b47de-6b17-4e29-8b7d-4996fa65bea4
ms.service: virtual-machines-linux
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 12/15/2017
ms.author: cynthn
ms.openlocfilehash: a59183365ada644f2ff1aae7d52bac5fb1f42354
ms.sourcegitcommit: b4755b3262c5b7d546e598c0a034a7c0d1e261ec
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/24/2019
ms.locfileid: "54888868"
---
# <a name="how-to-use-docker-machine-to-create-hosts-in-azure"></a>Použití Docker Machine k vytvoření hostitelů v Azure
Tento článek podrobně popisuje, jak používat [Docker Machine](https://docs.docker.com/machine/) k vytvoření hostitelů v Azure. `docker-machine` Příkaz vytvoří virtuální počítač (VM) s Linuxem v Azure, pak nainstaluje Dockeru. Potom můžete spravovat hostitele Docker v Azure s využitím stejných nástrojů místní a pracovních postupů. Použití docker machine v systému Windows 10, je nutné použít bash v systému Linux.

## <a name="create-vms-with-docker-machine"></a>Vytvoření virtuálních počítačů pomocí Docker Machine
Nejprve, získejte ID vašeho předplatného Azure s [zobrazit účet az](/cli/azure/account#az_account_show) následujícím způsobem:

```azurecli
sub=$(az account show --query "id" -o tsv)
```

Vytvoření hostitele Docker virtuálních počítačů v Azure pomocí `docker-machine create` zadáním *azure* jako ovladač. Další informace najdete v tématu [dokumentaci ovladač Azure Docker](https://docs.docker.com/machine/drivers/azure/)

Následující příklad vytvoří virtuální počítač s názvem *myVM*, na základě plánu "Standard D2 v2", vytvoří uživatelský účet s názvem *azureuser*a otevře port *80* na hostiteli virtuálního počítače. Postupujte podle výzev k přihlášení ke svému účtu Azure a udělení oprávnění Docker Machine k vytvoření a správě prostředků.

```bash
docker-machine create -d azure \
    --azure-subscription-id $sub \
    --azure-ssh-user azureuser \
    --azure-open-port 80 \
    --azure-size "Standard_DS2_v2" \
    myvm
```

Výstup vypadá podobně jako v následujícím příkladu:

```bash
Creating CA: /Users/user/.docker/machine/certs/ca.pem
Creating client certificate: /Users/user/.docker/machine/certs/cert.pem
Running pre-create checks...
(myvm) Completed machine pre-create checks.
Creating machine...
(myvm) Querying existing resource group.  name="docker-machine"
(myvm) Creating resource group.  name="docker-machine" location="westus"
(myvm) Configuring availability set.  name="docker-machine"
(myvm) Configuring network security group.  name="myvm-firewall" location="westus"
(myvm) Querying if virtual network already exists.  rg="docker-machine" location="westus" name="docker-machine-vnet"
(myvm) Creating virtual network.  name="docker-machine-vnet" rg="docker-machine" location="westus"
(myvm) Configuring subnet.  name="docker-machine" vnet="docker-machine-vnet" cidr="192.168.0.0/16"
(myvm) Creating public IP address.  name="myvm-ip" static=false
(myvm) Creating network interface.  name="myvm-nic"
(myvm) Creating storage account.  sku=Standard_LRS name="vhdski0hvfazyd8mn991cg50" location="westus"
(myvm) Creating virtual machine.  location="westus" size="Standard_A2" username="azureuser" osImage="canonical:UbuntuServer:16.04.0-LTS:latest" name="myvm
Waiting for machine to be running, this may take a few minutes...
Detecting operating system of created instance...
Waiting for SSH to be available...
Detecting the provisioner...
Provisioning with ubuntu(systemd)...
Installing Docker...
Copying certs to the local machine directory...
Copying certs to the remote machine...
Setting Docker configuration on the remote daemon...
Checking connection to Docker...
Docker is up and running!
To see how to connect your Docker Client to the Docker Engine running on this virtual machine, run: docker-machine env myvm
```

## <a name="configure-your-docker-shell"></a>Konfigurace vašeho prostředí Dockeru
Pro připojení k hostiteli vaše Docker v Azure, definujte nastavení pro příslušné připojení. Jak je uvedeno na konci výstupu, zobrazení informací o připojení pro hostitele Docker následujícím způsobem: 

```bash
docker-machine env myvm
```

Výstup se podobá následujícímu příkladu:

```bash
export DOCKER_TLS_VERIFY="1"
export DOCKER_HOST="tcp://40.68.254.142:2376"
export DOCKER_CERT_PATH="/Users/user/.docker/machine/machines/machine"
export DOCKER_MACHINE_NAME="machine"
# Run this command to configure your shell:
# eval $(docker-machine env myvm)
```

K definování nastavení připojení, můžete buď spustit příkaz navrhovaná konfigurace (`eval $(docker-machine env myvm)`), nebo můžete ručně nastavit proměnné prostředí. 

## <a name="run-a-container"></a>Spuštění kontejneru
Chcete-li zobrazit kontejneru v akci, umožňuje spustit základní webový server NGINX. Vytvořte kontejner s `docker run` a zpřístupňuje porty 80 pro webový provoz následujícím způsobem:

```bash
docker run -d -p 80:80 --restart=always nginx
```

Výstup se podobá následujícímu příkladu:

```bash
Unable to find image 'nginx:latest' locally
latest: Pulling from library/nginx
ff3d52d8f55f: Pull complete
226f4ec56ba3: Pull complete
53d7dd52b97d: Pull complete
Digest: sha256:41ad9967ea448d7c2b203c699b429abe1ed5af331cd92533900c6d77490e0268
Status: Downloaded newer image for nginx:latest
675e6056cb81167fe38ab98bf397164b01b998346d24e567f9eb7a7e94fba14a
```

Zobrazení spuštěné kontejnery s `docker ps`. Následující příklad výstupu ukazuje kontejner NGINX a spuštění s portem 80 vystavený:

```bash
CONTAINER ID    IMAGE    COMMAND                   CREATED          STATUS          PORTS                          NAMES
d5b78f27b335    nginx    "nginx -g 'daemon off"    5 minutes ago    Up 5 minutes    0.0.0.0:80->80/tcp, 443/tcp    festive_mirzakhani
```

## <a name="test-the-container"></a>Test kontejneru
Následujícím způsobem získejte veřejnou IP adresu hostitele Docker:


```bash
docker-machine ip myvm
```

Pokud chcete zobrazit kontejneru v akci, otevřete webový prohlížeč a zadejte veřejnou IP adresu uvedené ve výstupu předchozího příkazu:

![Spuštěný kontejner ngnix](./media/docker-machine/nginx.png)

## <a name="next-steps"></a>Další postup
Můžete také vytvořit hostitele s [rozšíření Docker VM](dockerextension.md). Příklady použití Docker Compose, naleznete v tématu [Začínáme s prostředím Docker a Compose v Azure](docker-compose-quickstart.md).
