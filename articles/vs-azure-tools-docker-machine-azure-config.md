---
title: Vytvořte hostitelů Docker v Azure s počítačem Docker | Microsoft Docs
description: Popisuje použití Docker počítač pro vytvoření hostitelů docker v Azure.
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
ms.openlocfilehash: 766d327a87ed13e04166d71c3d9ae0a1e7a66d19
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/11/2017
ms.locfileid: "23883877"
---
# <a name="create-docker-hosts-in-azure-with-docker-machine"></a>Vytvoření hostitelů s Dockerem v Azure pomocí Docker-Machine
Spuštění [Docker](https://www.docker.com/) kontejnery vyžaduje hostitele démona docker virtuálního počítače.
Toto téma popisuje postup použití [počítač docker](https://docs.docker.com/machine/) příkaz pro vytvoření nové virtuální počítače Linux nakonfigurované démona Docker, běží v Azure. 

**Poznámka:** 

* *Tento článek závisí na verzi počítač docker 0.9.0-rc2 nebo větší*
* *Kontejnery Windows budou podporovány prostřednictvím docker počítače v blízké budoucnosti*

## <a name="create-vms-with-docker-machine"></a>Vytvořit virtuální počítače s počítačem Docker
Vytvoření docker hostitele virtuálních počítačů v Azure pomocí `docker-machine create` příkaz pomocí `azure` ovladačů. 

Ovladač Azure vyžaduje ID vašeho předplatného. Můžete použít [rozhraní příkazového řádku Azure](cli-install-nodejs.md) nebo [portálu Azure](https://portal.azure.com) načíst vaše předplatné Azure. 

**Pomocí portálu Azure**

* Vyberte **odběry** z levé navigační stránce a zkopírujte id předplatného.

**Pomocí Azure CLI**

* Typ ```azure account list``` a zkopírujte id předplatného.

Typ `docker-machine create --driver azure` zobrazíte možnosti a jejich výchozí hodnoty.
Můžete také zjistit [dokumentace Azure ovladač Docker](https://docs.docker.com/machine/drivers/azure/) Další informace. 

Následující příklad závisí na [výchozí hodnoty](https://github.com/docker/machine/blob/master/drivers/azure/azure.go#L22), ale můžete nastavit tyto hodnoty: 

* Azure dns pro název spojený s veřejnou IP adresu a certifikáty generované. Toto je název DNS virtuálního počítače. Virtuální počítač může potom bezpečně zastavit, verzi dynamické IP a umožňují znovu připojit po virtuální počítač spustí znovu s novou IP Adresou. Předpona názvu musí být jedinečný pro danou oblast UNIQUE_DNSNAME_PREFIX.westus.cloudapp.azure.com.
* Otevřete port 80 pro virtuální počítač pro odchozí přístup k Internetu
* velikost virtuálního počítače, který využívají rychlejší storage úrovně premium
* použít pro disk virtuálního počítače služby storage úrovně Premium

```
docker-machine create -d azure --azure-subscription-id <Your AZURE_SUBSCRIPTION_ID> --azure-dns <Your UNIQUE_DNSNAME_PREFIX> --azure-open-port 80 --azure-size Standard_DS1_v2 --azure-storage-type "Premium_LRS" mydockerhost 
```

## <a name="choose-a-docker-host-with-docker-machine"></a>Zvolit hostitele s docker počítač docker
Jakmile máte položku v docker počítače pro hostitele, můžete nastavit výchozí hostitel při spuštění docker příkazů.

## <a name="using-powershell"></a>Pomocí prostředí PowerShell
```powershell
docker-machine env MyDockerHost | Invoke-Expression 
```

## <a name="using-bash"></a>Pomocí Bash
```bash
eval $(docker-machine env MyDockerHost)
```

Teď můžete spustit příkazy docker proti zadaného hostitele

```
docker ps
docker info
```

## <a name="run-a-container"></a>Spusťte kontejner
Pomocí konfigurovaného hostitele teď můžete spustit jednoduchý webový server k ověření, zda váš hostitel byla nakonfigurována správně.
Zde jsme použít bitovou kopii standardní nginx, zadejte, že musí naslouchat na portu 80, že pokud se hostitel virtuálních počítačů restartuje, kontejneru se restartování a také (`--restart=always`). 

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
Zkontrolujte spuštěných kontejnerů pomocí `docker ps`:

```bash
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS                         NAMES
d5b78f27b335        nginx               "nginx -g 'daemon off"   5 minutes ago       Up 5 minutes        0.0.0.0:80->80/tcp, 443/tcp   goofy_mahavira
```

A pokud chcete zobrazit spuštěné kontejneru, zadejte `docker-machine ip <VM name>` najít IP adresu, kterou zadat do prohlížeče:

```
PS C:\> docker-machine ip MyDockerHost
191.237.46.90
```

![Spuštěné ngnix kontejneru](./media/vs-azure-tools-docker-machine-azure-config/nginxsuccess.png)

## <a name="summary"></a>Souhrn
Pomocí docker počítače můžete snadno zřídit hostitelů docker v Azure pro vaše jednotlivé docker hostitele ověření.
Pro produkční prostředí hostování kontejnerů, najdete v článku [Azure Container Service](http://aka.ms/AzureContainerService)

K vývoji aplikací .NET Core pomocí sady Visual Studio, najdete v části [Docker Tools pro Visual Studio](http://aka.ms/DockerToolsForVS)

