---
title: Kurz Azure Container Service – Správa DC/OS
description: Kurz Azure Container Service – Správa DC/OS
services: container-service
author: iainfoulds
manager: jeconnoc
ms.service: container-service
ms.topic: tutorial
ms.date: 02/26/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: 5b7f2f1bd1872f78377a0d16567ca4df8f8d0968
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/02/2018
ms.locfileid: "39440673"
---
# <a name="azure-container-service-tutorial---manage-dcos"></a>Kurz Azure Container Service – Správa DC/OS

DC/OS poskytuje distribuovanou platformu pro spouštění moderních a kontejnerizovaných aplikací. Se službou Azure Container Service je zřízení clusteru DC/OS připraveného pro produkční prostředí snadné a rychlé. Tento rychlý start podrobně popisuje základní kroky nezbytné k nasazení clusteru DC/OS a spuštění základní úlohy.

> [!div class="checklist"]
> * Vytvoření clusteru ACS DC/OS
> * Připojení ke clusteru
> * Instalace rozhraní příkazového řádku DC/OS
> * Nasazení aplikace do clusteru
> * Škálování aplikace v clusteru
> * Škálování uzlů clusteru DC/OS
> * Základní správa DC/OS
> * Odstranění clusteru DC/OS

Tento kurz vyžaduje Azure CLI verze 2.0.4 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete upgrade, přečtěte si téma [Instalace Azure CLI 2.0]( /cli/azure/install-azure-cli). 

## <a name="create-dcos-cluster"></a>Vytvoření clusteru DC/OS

Nejdřív vytvořte skupinu prostředků pomocí příkazu [az group create](/cli/azure/group#az-group-create). Skupina prostředků Azure je logický kontejner, ve kterém se nasazují a spravují prostředky Azure. 

Následující příklad vytvoří skupinu prostředků *myResourceGroup* v umístění *westeurope*.

```azurecli
az group create --name myResourceGroup --location westeurope
```

Potom vytvořte cluster DC/OS pomocí příkazu [az acs create](/cli/azure/acs#az-acs-create).

Následující příklad vytvoří cluster DC/OS *myDCOSCluster* a vytvoří klíče SSH, pokud ještě neexistují. Chcete-li použít konkrétní sadu klíčů, použijte možnost `--ssh-key-value`.  

```azurecli
az acs create \
  --orchestrator-type dcos \
  --resource-group myResourceGroup \
  --name myDCOSCluster \
  --generate-ssh-keys
```

Po několika minutách se příkaz dokončí a vrátí informace o nasazení.

## <a name="connect-to-dcos-cluster"></a>Připojení ke clusteru DC/OS

Po vytvoření je cluster DC/OS přístupný prostřednictvím tunelu SSH. Spuštění následujícího příkazu vrátí veřejnou IP adresu hlavního uzlu DC/OS. Tato IP adresa se uloží do proměnné a použije v dalším kroku.

```azurecli
ip=$(az network public-ip list --resource-group myResourceGroup --query "[?contains(name,'dcos-master')].[ipAddress]" -o tsv)
```

Pokud chcete vytvořit tunel SSH, spusťte následující příkaz a postupujte podle pokynů na obrazovce. Pokud se už používá port 80, příkaz selže. Aktualizujte port tunelu na nějaký, který se nepoužívá, například `85:localhost:80`. 

```azurecli
sudo ssh -i ~/.ssh/id_rsa -fNL 80:localhost:80 -p 2200 azureuser@$ip
```

## <a name="install-dcos-cli"></a>Instalace rozhraní příkazového řádku DC/OS

Nainstalujte rozhraní příkazového řádku DC/OS pomocí příkazu [az acs dcos install-cli](/azure/acs/dcos#install-cli). Pokud používáte Azure Cloud Shell, rozhraní příkazového řádku DC/OS je už nainstalované. Pokud používáte Azure CLI v systému macOS nebo Linuxu, možná budete muset příkaz spustit s příkazem sudo.

```azurecli
az acs dcos install-cli
```

Než bude možné používat rozhraní příkazového řádku s clusterem, musí se nakonfigurovat pro použití tunelu SSH. To provedete spuštěním následujícího příkazu, ve kterém podle potřeby upravte port.

```azurecli
dcos config set core.dcos_url http://localhost
```

## <a name="run-an-application"></a>Spuštění aplikace

Výchozím plánovacím mechanismem pro cluster DC/OS je Marathon. Marathon slouží ke spouštění aplikace a správě stavu aplikace v clusteru DC/OS. Pokud chcete naplánovat aplikaci přes Marathon, vytvořte soubor **marathon-app.json** a zkopírujte do něj následující obsah. 

```json
{
  "id": "demo-app-private",
  "cmd": null,
  "cpus": 1,
  "mem": 32,
  "disk": 0,
  "instances": 1,
  "container": {
    "docker": {
      "image": "nginx",
      "network": "BRIDGE",
      "portMappings": [
        {
          "containerPort": 80,
          "protocol": "tcp",
          "name": "80",
          "labels": null
        }
      ]
    },
    "type": "DOCKER"
  }
}
```

Spuštěním následujícího příkazu v aplikaci naplánujte spouštění v clusteru DC/OS.

```azurecli
dcos marathon app add marathon-app.json
```

Pokud chcete zobrazit stav nasazení aplikace, spusťte následující příkaz.

```azurecli
dcos marathon app list
```

Až se hodnota ve sloupci **TASKS** (ÚKOLY) změní z *0/1* na *1/1*, nasazení aplikace je dokončené.

```azurecli
ID     MEM  CPUS  TASKS  HEALTH  DEPLOYMENT  WAITING  CONTAINER  CMD   
/test   32   1     0/1    ---       ---      False      DOCKER   None
```

## <a name="scale-marathon-application"></a>Škálování aplikace Marathon

V předchozím příkladu se vytvořila jedna instance aplikace. Pokud chcete toho nasazení aktualizovat tak, aby byly dostupné tři instance aplikace, otevřete soubor **marathon app.json** a aktualizujte vlastnost instance na 3.

```json
{
  "id": "demo-app-private",
  "cmd": null,
  "cpus": 1,
  "mem": 32,
  "disk": 0,
  "instances": 3,
  "container": {
    "docker": {
      "image": "nginx",
      "network": "BRIDGE",
      "portMappings": [
        {
          "containerPort": 80,
          "protocol": "tcp",
          "name": "80",
          "labels": null
        }
      ]
    },
    "type": "DOCKER"
  }
}
```

Aktualizujte aplikaci pomocí příkazu `dcos marathon app update`.

```azurecli
dcos marathon app update demo-app-private < marathon-app.json
```

Pokud chcete zobrazit stav nasazení aplikace, spusťte následující příkaz.

```azurecli
dcos marathon app list
```

Až se hodnota ve sloupci **TASKS** (ÚKOLY) změní z *1/3* na *3/1*, nasazení aplikace je dokončené.

```azurecli
ID     MEM  CPUS  TASKS  HEALTH  DEPLOYMENT  WAITING  CONTAINER  CMD   
/test   32   1     1/3    ---       ---      False      DOCKER   None
```

## <a name="run-internet-accessible-app"></a>Spuštění aplikace přístupné z internetu

Cluster ACS DC/OS se skládá ze dvou sad uzlů. Jedna je veřejná a je přístupná z internetu a druhá je privátní a z internetu přístupná není. Výchozí sadou jsou privátní uzly, které se použily v předchozím příkladu.

Pokud chcete aplikaci zpřístupnit z internetu, nasaďte ji do veřejné sady uzlů. Za tím účelem přiřaďte objektu `acceptedResourceRoles` hodnotu `slave_public`.

Vytvořte soubor s názvem **nginx-public.json** a zkopírujte do něj následující obsah.

```json
{
  "id": "demo-app",
  "cmd": null,
  "cpus": 1,
  "mem": 32,
  "disk": 0,
  "instances": 1,
  "container": {
    "docker": {
      "image": "nginx",
      "network": "BRIDGE",
      "portMappings": [
        {
          "containerPort": 80,
          "hostPort": 80,
          "protocol": "tcp",
          "name": "80",
          "labels": null
        }
      ]
    },
    "type": "DOCKER"
  },
  "acceptedResourceRoles": [
    "slave_public"
  ]
}
```

Spuštěním následujícího příkazu v aplikaci naplánujte spouštění v clusteru DC/OS.

```azurecli 
dcos marathon app add nginx-public.json
```

Získejte veřejnou IP adresu agentů veřejného clusteru DC/OS.

```azurecli 
az network public-ip list --resource-group myResourceGroup --query "[?contains(name,'dcos-agent')].[ipAddress]" -o tsv
```

Po přechodu na tuto adresu se vrátí výchozí web NGINX.

![NGINX](./media/container-service-dcos-manage-tutorial/nginx.png)

## <a name="scale-dcos-cluster"></a>Škálování clusteru DC/OS

V předchozím příkladu se aplikace škálovala na několik instancí. Infrastruktura DC/OS se také dá škálovat, aby poskytovala více nebo méně výpočetní kapacity. Používá se k tomu příkaz [az acs scale](). 

Ke zjištění aktuálního počtu agentů DC/OS použijte příkaz [az acs show](/cli/azure/acs#az-acs-show).

```azurecli
az acs show --resource-group myResourceGroup --name myDCOSCluster --query "agentPoolProfiles[0].count"
```

Ke zvýšení jejich počtu na 5 použijte příkaz [az acs scale](/cli/azure/acs#az-acs-scale). 

```azurecli
az acs scale --resource-group myResourceGroup --name myDCOSCluster --new-agent-count 5
```

## <a name="delete-dcos-cluster"></a>Odstranění clusteru DC/OS

Pokud už je nepotřebujete, můžete k odebrání skupiny prostředků, clusteru DC/OS a všech souvisejících prostředků použít příkaz [az group delete](/cli/azure/group#az-group-delete).

```azurecli 
az group delete --name myResourceGroup --no-wait
```

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se seznámili se základními úlohami správy DC/OS, včetně následujících. 

> [!div class="checklist"]
> * Vytvoření clusteru ACS DC/OS
> * Připojení ke clusteru
> * Instalace rozhraní příkazového řádku DC/OS
> * Nasazení aplikace do clusteru
> * Škálování aplikace v clusteru
> * Škálování uzlů clusteru DC/OS
> * Odstranění clusteru DC/OS

Přejděte k dalšímu kurzu, kde se seznámíte s aplikací vyrovnávání zatížení v DC/OS v Azure. 

> [!div class="nextstepaction"]
> [Vyrovnávání zatížení aplikací](container-service-load-balancing.md)