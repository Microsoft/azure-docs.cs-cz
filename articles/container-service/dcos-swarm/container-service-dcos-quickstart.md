---
title: Rychlý start pro Azure Container Service – Nasazení clusteru DC/OS
description: Rychlý start pro Azure Container Service – Nasazení clusteru DC/OS
services: container-service
author: iainfoulds
manager: jeconnoc
ms.service: container-service
ms.topic: quickstart
ms.date: 02/26/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: ccc0836a296bb392e60dd0c973516a7087ff6ea5
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/02/2018
ms.locfileid: "39429322"
---
# <a name="deploy-a-dcos-cluster"></a>Nasazení clusteru DC/OS

DC/OS poskytuje distribuovanou platformu pro spouštění moderních a kontejnerizovaných aplikací. Se službou Azure Container Service je zřízení clusteru DC/OS připraveného pro produkční prostředí snadné a rychlé. Tento rychlý start podrobně popisuje základní kroky nezbytné k nasazení clusteru DC/OS a spuštění základní úlohy.

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

Tento kurz vyžaduje Azure CLI verze 2.0.4 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete upgrade, přečtěte si téma [Instalace Azure CLI 2.0]( /cli/azure/install-azure-cli). 

## <a name="log-in-to-azure"></a>Přihlášení k Azure 

Přihlaste se k předplatnému Azure pomocí příkazu [az login](/cli/azure/reference-index#az-login) a postupujte podle pokynů na obrazovce.

```azurecli
az login
```

## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Vytvořte skupinu prostředků pomocí příkazu [az group create](/cli/azure/group#az-group-create). Skupina prostředků Azure je logický kontejner, ve kterém se nasazují a spravují prostředky Azure. 

Následující příklad vytvoří skupinu prostředků *myResourceGroup* v umístění *eastus*.

```azurecli
az group create --name myResourceGroup --location eastus
```

## <a name="create-dcos-cluster"></a>Vytvoření clusteru DC/OS

Vytvořte cluster DC/OS pomocí příkazu [az acs create](/cli/azure/acs#az-acs-create).

Následující příklad vytvoří cluster DC/OS *myDCOSCluster* a vytvoří klíče SSH, pokud ještě neexistují. Chcete-li použít konkrétní sadu klíčů, použijte možnost `--ssh-key-value`.  

```azurecli
az acs create --orchestrator-type dcos --resource-group myResourceGroup --name myDCOSCluster --generate-ssh-keys
```

V některých případech, například s omezenou zkušební verzí, má předplatné Azure omezený přístup k prostředkům Azure. Pokud se nasazení nezdaří kvůli omezenému počtu dostupných jader, snižte výchozí počet agentů přidáním možnosti `--agent-count 1` do příkazu [az acs create](/cli/azure/acs#az-acs-create). 

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

Tunel SSH můžete otestovat přechodem na adresu `http://localhost`. Pokud jste použili jiný port než 80, upravte umístění odpovídajícím způsobem. 

Pokud se úspěšně vytvořil tunel SSH, vrátí se portál DC/OS.

![Uživatelské rozhraní DC/OS](./media/container-service-dcos-quickstart/dcos-ui.png)

## <a name="install-dcos-cli"></a>Instalace rozhraní příkazového řádku DC/OS

Rozhraní příkazového řádku DC/OS slouží ke správě clusteru DC/OS z příkazového řádku. Nainstalujte rozhraní příkazového řádku DC/OS pomocí příkazu [az acs dcos install-cli](/azure/acs/dcos#install-cli). Pokud používáte Azure Cloud Shell, rozhraní příkazového řádku DC/OS je už nainstalované. 

Pokud používáte Azure CLI v systému macOS nebo Linuxu, možná budete muset příkaz spustit s příkazem sudo.

```azurecli
az acs dcos install-cli
```

Než bude možné používat rozhraní příkazového řádku s clusterem, musí se nakonfigurovat pro použití tunelu SSH. To provedete spuštěním následujícího příkazu, ve kterém podle potřeby upravte port.

```azurecli
dcos config set core.dcos_url http://localhost
```

## <a name="run-an-application"></a>Spuštění aplikace

Výchozím plánovacím mechanismem pro cluster DC/OS je Marathon. Marathon slouží ke spouštění aplikace a správě stavu aplikace v clusteru DC/OS. Pokud chcete naplánovat aplikaci přes Marathon, vytvořte soubor *marathon-app.json* a zkopírujte do něj následující obsah. 

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
dcos marathon app add marathon-app.json
```

Pokud chcete zobrazit stav nasazení aplikace, spusťte následující příkaz.

```azurecli
dcos marathon app list
```

Až se hodnota ve sloupci **WAITING** (ČEKÁNÍ) změní z *True* na *False*, nasazení aplikace je dokončené.

```azurecli
ID     MEM  CPUS  TASKS  HEALTH  DEPLOYMENT  WAITING  CONTAINER  CMD   
/test   32   1     1/1    ---       ---      False      DOCKER   None
```

Získejte veřejnou IP adresu agentů clusteru DC/OS.

```azurecli
az network public-ip list --resource-group myResourceGroup --query "[?contains(name,'dcos-agent')].[ipAddress]" -o tsv
```

Po přechodu na tuto adresu se vrátí výchozí web NGINX.

![NGINX](./media/container-service-dcos-quickstart/nginx.png)

## <a name="delete-dcos-cluster"></a>Odstranění clusteru DC/OS

Pokud už je nepotřebujete, můžete k odebrání skupiny prostředků, clusteru DC/OS a všech souvisejících prostředků použít příkaz [az group delete](/cli/azure/group#az-group-delete).

```azurecli
az group delete --name myResourceGroup --no-wait
```

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste nasadili cluster DC/OS a spustili v něm jednoduchý kontejner Dockeru. Další informace o službě Azure Container Service najdete v kurzech služby ACS.

> [!div class="nextstepaction"]
> [Správa clusteru ACS DC/OS](container-service-dcos-manage-tutorial.md)