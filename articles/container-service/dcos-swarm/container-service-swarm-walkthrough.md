---
title: Rychlý start – Cluster Azure Docker Swarm pro Linux
description: Rychle se naučíte, jak pomocí Azure CLI vytvořit cluster Docker Swarm pro kontejnery Linuxu ve službě Azure Container Service.
services: container-service
author: neilpeterson
manager: timlt
ms.service: container-service
ms.topic: quickstart
ms.date: 02/26/2018
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: 7bd781d2fd71c6075cb458b222a93953a22892b6
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2018
---
# <a name="deploy-docker-swarm-cluster"></a>Nasazení clusteru Docker Swarm

V tomto rychlém startu se nasadí cluster Docker Swarm pomocí Azure CLI. Následně se na tomto clusteru nasadí a spustí vícekontejnerová aplikace skládající se z webu front-end a instance Redis. Po dokončení bude aplikace přístupná přes internet.

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

Tento rychlý start vyžaduje použití Azure CLI verze 2.0.4 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI 2.0]( /cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Vytvořte skupinu prostředků pomocí příkazu [az group create](/cli/azure/group#az_group_create). Skupina prostředků Azure je logická skupina, ve které se nasazují a spravují prostředky Azure.

Následující příklad vytvoří skupinu prostředků *myResourceGroup* v umístění *westus*.

```azurecli-interactive
az group create --name myResourceGroup --location westus
```

Výstup:

```json
{
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup",
  "location": "westcentralus",
  "managedBy": null,
  "name": "myResourceGroup",
  "properties": {
    "provisioningState": "Succeeded"
  },
  "tags": null
}
```

## <a name="create-docker-swarm-cluster"></a>Vytvoření clusteru Docker Swarm

Vytvořte cluster Docker Swarm ve službě Azure Container Service pomocí příkazu [az acs create](/cli/azure/acs#az_acs_create). 

Následující příklad vytvoří cluster *mySwarmCluster* s jedním hlavním linuxovým uzlem a třemi agentskými linuxovými uzly.

```azurecli-interactive
az acs create --name mySwarmCluster --orchestrator-type Swarm --resource-group myResourceGroup --generate-ssh-keys
```

V některých případech, například s omezenou zkušební verzí, má předplatné Azure omezený přístup k prostředkům Azure. Pokud se nasazení nezdaří kvůli omezenému počtu dostupných jader, snižte výchozí počet agentů přidáním možnosti `--agent-count 1` do příkazu [az acs create](/cli/azure/acs#az_acs_create). 

Po několika minutách se příkaz dokončí a vrátí informace o clusteru ve formátu JSON.

## <a name="connect-to-the-cluster"></a>Připojení ke clusteru

V průběhu tohoto rychlého startu budete potřebovat IP adresu hlavního uzlu Dockeru Swarm i fondu agentských uzlů Dockeru. Spusťte následující příkaz, který vrátí obě IP adresy.


```bash
az network public-ip list --resource-group myResourceGroup --query "[*].{Name:name,IPAddress:ipAddress}" -o table
```

Výstup:

```bash
Name                                                                 IPAddress
-------------------------------------------------------------------  -------------
swarmm-agent-ip-myswarmcluster-myresourcegroup-d5b9d4agent-66066781  52.179.23.131
swarmm-master-ip-myswarmcluster-myresourcegroup-d5b9d4mgmt-66066781  52.141.37.199
```

Vytvořte tunel SSH k hlavnímu uzlu Swarm. Nahraďte `IPAddress` IP adresou hlavního uzlu Swarm.

```bash
ssh -p 2200 -fNL 2375:localhost:2375 azureuser@IPAddress
```

Nastavte proměnnou prostředí `DOCKER_HOST`. To vám umožní spouštět příkazy Dockeru pro Docker Swarm, aniž byste museli zadávat název hostitele.

```bash
export DOCKER_HOST=:2375
```

Nyní jste připraveni spustit služby Dockeru v Dockeru Swarm.


## <a name="run-the-application"></a>Spuštění aplikace

Vytvořte soubor `docker-compose.yaml` a zkopírujte do něj následující obsah.

```yaml
version: '3'
services:
  azure-vote-back:
    image: redis
    container_name: azure-vote-back
    ports:
        - "6379:6379"

  azure-vote-front:
    image: microsoft/azure-vote-front:v1
    container_name: azure-vote-front
    environment:
      REDIS: azure-vote-back
    ports:
        - "80:80"
```

Spuštěním následujícího příkazu vytvořte službu Azure Vote.

```bash
docker-compose up -d
```

Výstup:

```bash
Creating network "user_default" with the default driver
Pulling azure-vote-front (microsoft/azure-vote-front:v1)...
swarm-agent-EE873B23000005: Pulling microsoft/azure-vote-front:v1...
swarm-agent-EE873B23000004: Pulling microsoft/azure-vote-front:v1... : downloaded
Pulling azure-vote-back (redis:latest)...
swarm-agent-EE873B23000004: Pulling redis:latest... : downloaded
Creating azure-vote-front ... 
Creating azure-vote-back ... 
Creating azure-vote-front
Creating azure-vote-back ...
```

## <a name="test-the-application"></a>Testování aplikace

Přejděte na IP adresu fondu agentských uzlů Swarm a otestujte aplikaci Azure Vote.

![Obrázek přechodu na aplikaci Azure Vote](media/container-service-docker-swarm-mode-walkthrough/azure-vote.png)

## <a name="delete-cluster"></a>Odstranění clusteru
Pokud už cluster nepotřebujete, můžete k odebrání skupiny prostředků, služby kontejneru a všech souvisejících prostředků použít příkaz [az group delete](/cli/azure/group#az_group_delete).

```azurecli-interactive
az group delete --name myResourceGroup --yes --no-wait
```

## <a name="get-the-code"></a>Získání kódu

V tomto rychlém startu se k vytvoření služby Docker použily předem vytvořené image kontejneru. Související kód aplikace, soubor Dockerfile a soubor Compose jsou k dispozici na GitHubu.

[https://github.com/Azure-Samples/azure-voting-app-redis](https://github.com/Azure-Samples/azure-voting-app-redis.git)

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste nasadili cluster Docker Swarm a do něj jste nasadili vícekontejnerovou aplikaci.

Informace o integraci Dockeru Swarm s Visual Studio Team Services najdete v tématu věnovaném průběžné integraci a doručování s využitím Dockeru Swarm a VSTS.

> [!div class="nextstepaction"]
> [CI/CD s Docker Swarm a VSTS](./container-service-docker-swarm-setup-ci-cd.md)
