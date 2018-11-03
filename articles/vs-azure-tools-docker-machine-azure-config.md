---
title: Vytvoření hostitelů s Dockerem v Azure pomocí Docker Machine | Dokumentace Microsoftu
description: Popisuje použití Docker Machine k vytvoření hostitelů docker v Azure.
services: azure-container-service
documentationcenter: na
author: mlearned
manager: douge
editor: ''
ms.assetid: 7a3ff6e1-fa93-4a62-b524-ab182d2fea08
ms.service: multiple
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 06/08/2016
ms.author: mlearned
ms.openlocfilehash: 98b2d76f4b8ba9ba8316f3a33fd2ff5ed5899833
ms.sourcegitcommit: ada7419db9d03de550fbadf2f2bb2670c95cdb21
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/02/2018
ms.locfileid: "50969528"
---
# <a name="create-docker-hosts-in-azure-with-docker-machine"></a>Vytvoření hostitelů s Dockerem v Azure pomocí Docker-Machine
Spuštění [Docker](https://www.docker.com/) kontejnery vyžaduje hostitele virtuálního počítače s démona dockeru.
Toto téma popisuje způsob použití [docker-machine](https://docs.docker.com/machine/) příkaz pro vytvoření nové virtuální počítače s Linuxem, nakonfigurovaný s démonem Dockeru, běží v Azure. 

**Poznámka:** 

* *Tento článek závisí na verzi docker-machine 0.9.0-rc2 nebo vyšší*
* *Kontejnery Windows se být podporovány prostřednictvím docker machine v blízké budoucnosti*

## <a name="create-vms-with-docker-machine"></a>Vytvoření virtuálních počítačů pomocí Docker Machine
Vytvoření docker v Azure pomocí hostování virtuálních počítačů `docker-machine create` příkazu `azure` ovladače. 

Ovladač Azure vyžaduje ID vašeho předplatného. Můžete použít [rozhraní příkazového řádku Azure](cli-install-nodejs.md) nebo [webu Azure Portal](https://portal.azure.com) načíst vaše předplatné Azure. 

**Pomocí webu Azure Portal**

* Vyberte **předplatná** z levé navigace stránky a zkopírujte id předplatného.

**Pomocí Azure CLI**

* Typ ```azure account list``` a zkopírujte id předplatného.

Typ `docker-machine create --driver azure` zobrazíte možnosti a jejich výchozí hodnoty.
Můžete zobrazit také [ovladač Azure Docker dokumentaci](https://docs.docker.com/machine/drivers/azure/) pro další informace. 

Následující příklad závisí [výchozí hodnoty](https://github.com/docker/machine/blob/master/drivers/azure/azure.go#L22), ale volitelně nastavit tyto hodnoty: 

* Azure dns pro název přidružený k veřejné IP adresy a certifikáty vygenerované. Jde o název DNS virtuálního počítače. Virtuální počítač může potom bezpečně zastavit, vydání dynamické IP a poskytují možnost znovu připojit po virtuální počítač spustí znovu s novou IP adresu. Předpona názvu musí být jedinečný pro tuto oblast UNIQUE_DNSNAME_PREFIX.westus.cloudapp.azure.com.
* Otevřete port 80 na virtuálním počítači pro odchozí přístup k Internetu
* velikost virtuálního počítače, aby se začala používat rychlejší storage úrovně premium
* použít pro disk virtuálního počítače služby Premium storage

```
docker-machine create -d azure --azure-subscription-id <Your AZURE_SUBSCRIPTION_ID> --azure-dns <Your UNIQUE_DNSNAME_PREFIX> --azure-open-port 80 --azure-size Standard_DS1_v2 --azure-storage-type "Premium_LRS" mydockerhost 
```

## <a name="choose-a-docker-host-with-docker-machine"></a>Zvolte hostitele docker pomocí docker-machine
Jakmile budete mít položku v docker-machine pro hostitele, můžete nastavit výchozí hostitel při spuštění příkazy dockeru.

## <a name="using-powershell"></a>Pomocí prostředí PowerShell
```powershell
docker-machine env MyDockerHost | Invoke-Expression 
```

## <a name="using-bash"></a>Pomocí skriptu Bash
```bash
eval $(docker-machine env MyDockerHost)
```

Nyní můžete spouštět příkazy dockeru pro zadaného hostitele

```
docker ps
docker info
```

## <a name="run-a-container"></a>Spuštění kontejneru
S hostiteli nakonfigurovaný teď můžete spustit jednoduchý webový server k ověření, zda byl váš hostitel správně nakonfigurován.
Tady můžeme použít image serveru nginx standardní, určete, zda by měl naslouchání na portu 80 a, pokud restartování hostitelského virtuálního počítače, kontejneru se restartovat i (`--restart=always`). 

```bash
docker run -d -p 80:80 --restart=always nginx
```

Výstup by měl vypadat přibližně takto:

```
Unable to find image 'nginx:latest' locally
latest: Pulling from library/nginx
efd26ecc9548: Pull complete
a3ed95caeb02: Pull complete
83f52fbfa5f8: Pull complete
fa664caa1402: Pull complete
Digest: sha256:12127e07a75bda1022fbd4ea231f5527a1899aad4679e3940482db3b57383b1d
Status: Downloaded newer image for nginx:latest
25942c35d86fe43c688d0c03ad478f14cc9c16913b0e1c2971cb32eb4d0ab721
```

## <a name="test-the-container"></a>Test kontejneru
Prozkoumejte spuštěné kontejnery pomocí `docker ps`:

```bash
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS                         NAMES
d5b78f27b335        nginx               "nginx -g 'daemon off"   5 minutes ago       Up 5 minutes        0.0.0.0:80->80/tcp, 443/tcp   goofy_mahavira
```

A pokud chcete zobrazit spuštěný kontejner, zadejte `docker-machine ip <VM name>` chcete zjistit IP adresu zadejte v prohlížeči:

```
PS C:\> docker-machine ip MyDockerHost
191.237.46.90
```

![Spuštěný kontejner ngnix](./media/vs-azure-tools-docker-machine-azure-config/nginxsuccess.png)

## <a name="summary"></a>Souhrn
Pomocí docker-machine se kterou jednoduše zprovozníte hostitelů docker v Azure pro vaše ověření hostitele jednotlivé dockeru.
Pro produkční prostředí hostování kontejnerů, najdete v článku [Azure Container Service](http://aka.ms/AzureContainerService)

K vývoji aplikací .NET Core pomocí sady Visual Studio, naleznete v tématu [nástroje Dockeru pro Visual Studio](http://aka.ms/DockerToolsForVS)

