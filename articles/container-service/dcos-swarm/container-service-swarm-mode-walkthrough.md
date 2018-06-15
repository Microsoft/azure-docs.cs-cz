---
title: Rychlý start – Cluster Azure Docker CE pro Linux
description: Rychle se naučíte, jak pomocí Azure CLI vytvořit cluster Docker CE pro kontejnery Linuxu ve službě Azure Container Service.
services: container-service
author: neilpeterson
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 02/26/2018
ms.author: nepeters
ms.custom: ''
ms.openlocfilehash: cd52982fc650d5fd3b4edd8513946a721306a451
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2018
ms.locfileid: "32164087"
---
# <a name="deploy-docker-ce-cluster"></a>Nasazení clusteru Docker CE

V tomto rychlém startu se nasadí cluster Docker CE pomocí Azure CLI. Následně se na tomto clusteru nasadí a spustí vícekontejnerová aplikace skládající se z webu front-end a instance Redis. Po dokončení bude aplikace přístupná přes internet.

Docker CE ve službě Azure Container Service je ve verzi Preview a **neměl by se používat pro produkční úlohy**.

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

Pokud se rozhodnete nainstalovat a používat rozhraní příkazového řádku místně, musíte mít rozhraní příkazového řádku Azure ve verzi 2.0.4 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI 2.0]( /cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Vytvořte skupinu prostředků pomocí příkazu [az group create](/cli/azure/group#az_group_create). Skupina prostředků Azure je logická skupina, ve které se nasazují a spravují prostředky Azure.

Následující příklad vytvoří skupinu prostředků *myResourceGroup* v umístění *ukwest*.

```azurecli-interactive
az group create --name myResourceGroup --location ukwest
```

Výstup:

```json
{
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup",
  "location": "ukwest",
  "managedBy": null,
  "name": "myResourceGroup",
  "properties": {
    "provisioningState": "Succeeded"
  },
  "tags": null
}
```

## <a name="create-docker-swarm-cluster"></a>Vytvoření clusteru Docker Swarm

Vytvořte cluster Docker CE ve službě Azure Container Service pomocí příkazu [az acs create](/cli/azure/acs#az_acs_create). 

Následující příklad vytvoří cluster *mySwarmCluster* s jedním hlavním linuxovým uzlem a třemi agentskými linuxovými uzly.

```azurecli-interactive
az acs create --name mySwarmCluster --orchestrator-type dockerce --resource-group myResourceGroup --generate-ssh-keys
```

V některých případech, například s omezenou zkušební verzí, má předplatné Azure omezený přístup k prostředkům Azure. Pokud se nasazení nezdaří kvůli omezenému počtu dostupných jader, snižte výchozí počet agentů přidáním možnosti `--agent-count 1` do příkazu [az acs create](/cli/azure/acs#az_acs_create). 

Po několika minutách se příkaz dokončí a vrátí informace o clusteru ve formátu JSON.

## <a name="connect-to-the-cluster"></a>Připojení ke clusteru

V průběhu tohoto rychlého startu budete potřebovat plně kvalifikovaný název domény hlavního uzlu Dockeru Swarm i fondu agentských uzlů Dockeru. Spusťte následující příkaz, který vrátí plně kvalifikované názvy domén hlavního uzlu i agentských uzlů.


```bash
az acs list --resource-group myResourceGroup --query '[*].{Master:masterProfile.fqdn,Agent:agentPoolProfiles[0].fqdn}' -o table
```

Výstup:

```bash
Master                                                               Agent
-------------------------------------------------------------------  --------------------------------------------------------------------
myswarmcluster-myresourcegroup-d5b9d4mgmt.ukwest.cloudapp.azure.com  myswarmcluster-myresourcegroup-d5b9d4agent.ukwest.cloudapp.azure.com
```

Vytvořte tunel SSH k hlavnímu uzlu Swarm. Nahraďte `MasterFQDN` adresou plně kvalifikovaného názvu domény hlavního uzlu Swarm.

```bash
ssh -p 2200 -fNL localhost:2374:/var/run/docker.sock azureuser@MasterFQDN
```

Nastavte proměnnou prostředí `DOCKER_HOST`. To vám umožní spouštět příkazy Dockeru pro Docker Swarm, aniž byste museli zadávat název hostitele.

```bash
export DOCKER_HOST=localhost:2374
```

Nyní jste připraveni spustit služby Dockeru v Dockeru Swarm.


## <a name="run-the-application"></a>Spuštění aplikace

Vytvořte soubor `azure-vote.yaml` a zkopírujte do něj následující obsah.


```yaml
version: '3'
services:
  azure-vote-back:
    image: redis
    ports:
        - "6379:6379"

  azure-vote-front:
    image: microsoft/azure-vote-front:v1
    environment:
      REDIS: azure-vote-back
    ports:
        - "80:80"
```

Spuštěním příkazu [docker stack deploy](https://docs.docker.com/engine/reference/commandline/stack_deploy/) vytvořte službu Azure Vote.

```bash
docker stack deploy azure-vote --compose-file azure-vote.yaml
```

Výstup:

```bash
Creating network azure-vote_default
Creating service azure-vote_azure-vote-back
Creating service azure-vote_azure-vote-front
```

Použijte příkaz [docker stack ps](https://docs.docker.com/engine/reference/commandline/stack_ps/), který vrátí stav nasazení aplikace.

```bash
docker stack ps azure-vote
```

Aplikace bude připravena, jakmile bude mít `CURRENT STATE` každé služby hodnotu `Running`.

```bash
ID                  NAME                            IMAGE                                 NODE                               DESIRED STATE       CURRENT STATE                ERROR               PORTS
tnklkv3ogu3i        azure-vote_azure-vote-front.1   microsoft/azure-vote-front:v1   swarmm-agentpool0-66066781000004   Running             Running 5 seconds ago                            
lg99i4hy68r9        azure-vote_azure-vote-back.1    redis:latest                          swarmm-agentpool0-66066781000002   Running             Running about a minute ago
```

## <a name="test-the-application"></a>Testování aplikace

Přejděte na plně kvalifikovaný název domény fondu agentských uzlů Swarm a otestujte aplikaci Azure Vote.

![Obrázek přechodu na aplikaci Azure Vote](media/container-service-docker-swarm-mode-walkthrough/azure-vote.png)

## <a name="delete-cluster"></a>Odstranění clusteru
Pokud už cluster nepotřebujete, můžete k odebrání skupiny prostředků, služby kontejneru a všech souvisejících prostředků použít příkaz [az group delete](/cli/azure/group#az_group_delete).

```azurecli-interactive
az group delete --name myResourceGroup --yes --no-wait
```

## <a name="get-the-code"></a>Získání kódu

V tomto rychlém startu se k vytvoření služby Docker použily předem vytvořené image kontejneru. Související kód aplikace, soubor Dockerfile a soubor Compose jsou k dispozici na GitHubu.

[https://github.com/Azure-Samples/azure-voting-app-redis](https://github.com/Azure-Samples/azure-voting-app-redis.git)

## <a name="next-steps"></a>Další postup

V tomto rychlém startu jste nasadili cluster Docker Swarm a do něj jste nasadili vícekontejnerovou aplikaci.

Informace o integraci Dockeru Swarm s Visual Studio Team Services najdete v tématu věnovaném průběžné integraci a doručování s využitím Dockeru Swarm a VSTS.

> [!div class="nextstepaction"]
> [CI/CD s Docker Swarm a VSTS](./container-service-docker-swarm-setup-ci-cd.md)
