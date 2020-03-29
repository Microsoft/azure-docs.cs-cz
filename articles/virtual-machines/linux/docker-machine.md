---
title: Použití Docker Machine k vytváření linuxových hostitelů
description: Popisuje, jak pomocí Docker Machine vytvořit hostitele Dockeru v Azure.
author: cynthn
ms.service: virtual-machines-linux
ms.devlang: multiple
ms.topic: article
ms.date: 12/15/2017
ms.author: cynthn
ms.openlocfilehash: c3165410809d98fd0ac4eeb515fbf30578633ef3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78968806"
---
# <a name="how-to-use-docker-machine-to-create-hosts-in-azure"></a>Jak používat Docker Machine k vytváření hostitelů v Azure
Tento článek podrobně popisuje, jak pomocí [Docker Machine](https://docs.docker.com/machine/) vytvořit hostitele v Azure. Příkaz `docker-machine` vytvoří virtuální počítač (VM) Linuxu v Azure a pak nainstaluje Docker. Pak můžete spravovat hostitele Dockeru v Azure pomocí stejných místních nástrojů a pracovních postupů. Chcete-li používat docker-machine v systému Windows 10, musíte použít Linux bash.

## <a name="create-vms-with-docker-machine"></a>Vytváření virtuálních počítačů pomocí počítače Docker Machine
Nejprve si můžete získat ID předplatného Azure s [účtem az takto:](/cli/azure/account)

```azurecli
sub=$(az account show --query "id" -o tsv)
```

Vytvoření virtuálních počítačů hostitele `docker-machine create` Dockeru v Azure s zadáním *azure* jako ovladač. Další informace najdete v [dokumentaci k ovladači Docker Azure](https://docs.docker.com/machine/drivers/azure/)

Následující příklad vytvoří virtuální počítač s názvem *myVM*na základě plánu "Standard D2 v2", vytvoří uživatelský účet s názvem *azureuser*a otevře port *80* na hostitelském virtuálním počítači. Postupujte podle všech výzev k přihlášení ke svému účtu Azure a udělte oprávnění Docker Machine k vytváření a správě prostředků.

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

## <a name="configure-your-docker-shell"></a>Konfigurace prostředí Dockeru
Chcete-li se připojit k hostiteli Dockeru v Azure, definujte příslušná nastavení připojení. Jak je uvedeno na konci výstupu, zobrazte informace o připojení pro vašeho hostitele Dockeru následujícím způsobem: 

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

Chcete-li definovat nastavení připojení, můžete buď spustit`eval $(docker-machine env myvm)`příkaz navrhované konfigurace ( ), nebo můžete nastavit proměnné prostředí ručně. 

## <a name="run-a-container"></a>Spuštění kontejneru
Chcete-li zobrazit kontejner v akci, umožňuje spustit základní webový server NGINX. Vytvořte kontejner `docker run` s portem 80 a zpřístupníte port 80 pro webový provoz následujícím způsobem:

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

Zobrazit spuštěné `docker ps`kontejnery s . Následující příklad výstupu ukazuje kontejner NGINX běžící s portem 80 vystaveny:

```bash
CONTAINER ID    IMAGE    COMMAND                   CREATED          STATUS          PORTS                          NAMES
d5b78f27b335    nginx    "nginx -g 'daemon off"    5 minutes ago    Up 5 minutes    0.0.0.0:80->80/tcp, 443/tcp    festive_mirzakhani
```

## <a name="test-the-container"></a>Otestujte nádobu
Získejte veřejnou IP adresu hostitele Dockeru následujícím způsobem:


```bash
docker-machine ip myvm
```

Chcete-li zobrazit kontejner v akci, otevřete webový prohlížeč a zadejte veřejnou IP adresu uvedenou ve výstupu předchozího příkazu:

![Běžící ngnix kontejner](./media/docker-machine/nginx.png)

## <a name="next-steps"></a>Další kroky
Příklady použití Docker Compose najdete [v tématu Začínáme s Dockerem a Compose v Azure](docker-compose-quickstart.md).
