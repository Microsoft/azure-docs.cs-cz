---
title: Použití Docker Machine k vytvoření hostitelů se systémem Linux
description: Popisuje způsob použití Docker Machine k vytváření hostitelů Docker v Azure.
author: cynthn
ms.service: virtual-machines-linux
ms.devlang: multiple
ms.topic: how-to
ms.date: 12/15/2017
ms.author: cynthn
ms.openlocfilehash: 83799940f8c98952a435f582a9160585e9dbdfd4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "87289786"
---
# <a name="how-to-use-docker-machine-to-create-hosts-in-azure"></a>Jak používat Docker Machine k vytváření hostitelů v Azure
Tento článek podrobně popisuje, jak pomocí [Docker Machine](https://docs.docker.com/machine/) vytvářet hostitele v Azure. `docker-machine`Příkaz vytvoří virtuální počítač se systémem Linux v Azure a nainstaluje Docker. Potom můžete spravovat hostitele Docker v Azure pomocí stejných místních nástrojů a pracovních postupů. Pokud chcete použít Docker-Machine ve Windows 10, musíte použít Linux bash.

## <a name="create-vms-with-docker-machine"></a>Vytvoření virtuálních počítačů pomocí Docker Machine
Nejdřív Získejte ID vašeho předplatného Azure pomocí příkazu [AZ Account show](/cli/azure/account) následujícím způsobem:

```azurecli
sub=$(az account show --query "id" -o tsv)
```

Virtuální počítače s hostitelem Docker vytvoříte v Azure pomocí `docker-machine create` zadáním *Azure* jako ovladače. Další informace najdete v [dokumentaci k ovladači Docker Azure](https://docs.docker.com/machine/drivers/azure/) .

Následující příklad vytvoří virtuální počítač s názvem *myVM*na základě plánu Standard D2 v2, vytvoří uživatelský účet s názvem *azureuser*a na virtuálním počítači hostitele otevře port *80* . Postupujte podle všech výzev a přihlaste se k účtu Azure a udělte oprávnění počítače Docker pro vytváření a správu prostředků.

```bash
docker-machine create -d azure \
    --azure-subscription-id $sub \
    --azure-ssh-user azureuser \
    --azure-open-port 80 \
    --azure-size "Standard_DS2_v2" \
    myvm
```

Výstup bude vypadat podobně jako v následujícím příkladu:

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

## <a name="configure-your-docker-shell"></a>Konfigurace prostředí Docker
Pokud se chcete připojit k hostiteli Docker v Azure, definujte odpovídající nastavení připojení. Jak je uvedeno na konci výstupu, podívejte se na informace o připojení pro hostitele Docker následujícím způsobem: 

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

Pokud chcete definovat nastavení připojení, můžete buď spustit navrhovaný příkaz konfigurace ( `eval $(docker-machine env myvm)` ), nebo můžete nastavit proměnné prostředí ručně. 

## <a name="run-a-container"></a>Spuštění kontejneru
Pokud chcete zobrazit kontejner v akci, umožňuje spustit základní webserver NGINX. Vytvořte kontejner s `docker run` a vystavte port 80 pro webový provoz následujícím způsobem:

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

Zobrazit spuštěné kontejnery pomocí `docker ps` . Následující příklad výstupu ukazuje kontejner NGINX spuštěný s vystaveným portem 80:

```bash
CONTAINER ID    IMAGE    COMMAND                   CREATED          STATUS          PORTS                          NAMES
d5b78f27b335    nginx    "nginx -g 'daemon off"    5 minutes ago    Up 5 minutes    0.0.0.0:80->80/tcp, 443/tcp    festive_mirzakhani
```

## <a name="test-the-container"></a>Test kontejneru
Získejte veřejnou IP adresu hostitele Docker následujícím způsobem:


```bash
docker-machine ip myvm
```

Chcete-li zobrazit kontejner v akci, otevřete webový prohlížeč a zadejte veřejnou IP adresu, kterou si poznamenali ve výstupu předchozího příkazu:

![Spuštění kontejneru ngnix](./media/docker-machine/nginx.png)

## <a name="next-steps"></a>Další kroky
Příklady použití Docker Compose najdete v tématu Začínáme [s Docker a vytváření v Azure](docker-compose-quickstart.md).
