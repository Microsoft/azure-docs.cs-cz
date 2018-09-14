---
title: Použití OpenFaaS s Azure Kubernetes Service (AKS)
description: Nasazení a použití OpenFaaS s Azure Kubernetes Service (AKS)
services: container-service
author: justindavies
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 03/05/2018
ms.author: juda
ms.custom: mvc
ms.openlocfilehash: dc0f4bd1e5b07e30f3c89807fbbbc908b3149810
ms.sourcegitcommit: f983187566d165bc8540fdec5650edcc51a6350a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/13/2018
ms.locfileid: "45542527"
---
# <a name="using-openfaas-on-aks"></a>Použití OpenFaaS v AKS

[OpenFaaS] [ open-faas] je architektura určená k vytváření funkcí bez serveru nad kontejnery. Jako opensourcový projekt získalo ve velkém měřítku přijetí v rámci komunity. Tento dokument podrobně popisuje instalaci a použití OpenFaas na cluster Azure Kubernetes Service (AKS).

## <a name="prerequisites"></a>Požadavky

Abyste mohli dokončit kroky v tomto článku, budete potřebovat.

* Základní znalosti o Kubernetes.
* Cluster Azure Kubernetes Service (AKS) a přihlašovací údaje AKS nakonfigurované pro váš vývojový systém.
* Azure CLI nainstalované ve vývojovém systému.
* Nástroje příkazového řádku Git nainstalovaný ve vašem systému.

## <a name="get-openfaas"></a>Get OpenFaaS

Naklonujte úložiště OpenFaaS projektu pro váš vývojový systém.

```azurecli-interactive
git clone https://github.com/openfaas/faas-netes
```

Přejděte do adresáře naklonované úložiště.

```azurecli-interactive
cd faas-netes
```

## <a name="deploy-openfaas"></a>Nasazení OpenFaaS

Dobrým postupem je OpenFaaS a OpenFaaS funkce by měla být uložena ve vlastním oboru názvů Kubernetes.

Vytvoření oboru názvů systému OpenFaaS.

```azurecli-interactive
kubectl create namespace openfaas
```

Vytvoření druhého oboru názvů pro OpenFaaS funkce.

```azurecli-interactive
kubectl create namespace openfaas-fn
```

Grafu Helm pro OpenFaaS je součástí naklonované úložiště. Pomocí tohoto grafu OpenFaaS nasadit do clusteru AKS.

```azurecli-interactive
helm install --namespace openfaas -n openfaas \
  --set functionNamespace=openfaas-fn, \
  --set serviceType=LoadBalancer, \
  --set rbac=false chart/openfaas/
```

Výstup:

```
NAME:   openfaas
LAST DEPLOYED: Wed Feb 28 08:26:11 2018
NAMESPACE: openfaas
STATUS: DEPLOYED

RESOURCES:
==> v1/ConfigMap
NAME                 DATA  AGE
prometheus-config    2     20s
alertmanager-config  1     20s

{snip}

NOTES:
To verify that openfaas has started, run:

  kubectl --namespace=openfaas get deployments -l "release=openfaas, app=openfaas"
```

Pro přístup k bráně OpenFaaS vytvoří veřejnou IP adresu. Pokud chcete načíst tuto IP adresu, použijte [kubectl get service] [ kubectl-get] příkazu. Může trvat minutu pro IP adresu, která přiřazené příslušné službě.

```console
kubectl get service -l component=gateway --namespace openfaas
```

Výstup.

```console
NAME               TYPE           CLUSTER-IP     EXTERNAL-IP    PORT(S)          AGE
gateway            ClusterIP      10.0.156.194   <none>         8080/TCP         7m
gateway-external   LoadBalancer   10.0.28.18     52.186.64.52   8080:30800/TCP   7m
```

Otestujte systém OpenFaaS, přejděte na externí IP adresu na portu 8080, `http://52.186.64.52:8080` v tomto příkladu.

![OpenFaaS uživatelského rozhraní](media/container-service-serverless/openfaas.png)

Nakonec nainstalujte rozhraní příkazového řádku OpenFaaS. Tento příklad používá brew, najdete v článku [dokumentace k rozhraní příkazového řádku OpenFaaS] [ open-faas-cli] další možnosti.

```console
brew install faas-cli
```

## <a name="create-first-function"></a>Vytvoření první funkce

Teď, když OpenFaaS je funkční, vytvořte pomocí portálu OpenFaas funkci.

Klikněte na **nasadit novou funkci** a vyhledejte **Figlet**. Vyberte funkci Figlet a klikněte na tlačítko **nasadit**.

![Figlet](media/container-service-serverless/figlet.png)

Používáme nástroj curl k vyvolání funkce. Nahraďte IP adresu v následujícím příkladu, který OpenFaas brány.

```azurecli-interactive
curl -X POST http://52.186.64.52:8080/function/figlet -d "Hello Azure"
```

Výstup:

```console
 _   _      _ _            _
| | | | ___| | | ___      / \    _____   _ _ __ ___
| |_| |/ _ \ | |/ _ \    / _ \  |_  / | | | '__/ _ \
|  _  |  __/ | | (_) |  / ___ \  / /| |_| | | |  __/
|_| |_|\___|_|_|\___/  /_/   \_\/___|\__,_|_|  \___|

```

## <a name="create-second-function"></a>Vytvoření druhého – funkce

Teď vytvořte druhý funkci. V tomto příkladu bude možné nasadit s použitím rozhraní příkazového řádku OpenFaaS a zahrnuje vlastní image kontejneru a načítání dat z Cosmos DB. Několik položek musí být nakonfigurovaná před vytvořením funkce.

Nejprve vytvořte novou skupinu prostředků pro Cosmos DB.

```azurecli-interactive
az group create --name serverless-backing --location eastus
```

Nasaďte instanci služby cosmos DB druhu `MongoDB`. Instance vyžaduje jedinečný název, aktualizujte `openfaas-cosmos` na něco pro vaše prostředí jedinečná.

```azurecli-interactive
az cosmosdb create --resource-group serverless-backing --name openfaas-cosmos --kind MongoDB
```

Získání připojovacího řetězce databáze Cosmos a uložte ji do proměnné.

Aktualizujte hodnotu `--resource-group` argument na název vaší skupiny prostředků a `--name` argument na název služby Cosmos DB.

```azurecli-interactive
COSMOS=$(az cosmosdb list-connection-strings \
  --resource-group serverless-backing \
  --name openfaas-cosmos \
  --query connectionStrings[0].connectionString \
  --output tsv)
```

Nyní naplňte Cosmos DB s využitím testovací data. Vytvořte soubor s názvem `plans.json` a zkopírujte následující JSON.

```json
{
    "name" : "two_person",
    "friendlyName" : "Two Person Plan",
    "portionSize" : "1-2 Person",
    "mealsPerWeek" : "3 Unique meals per week",
    "price" : 72,
    "description" : "Our basic plan, delivering 3 meals per week, which will feed 1-2 people.",
    "__v" : 0
}
```

Použití *věnované* nástroj vyžadovalo načtení instance služby cosmos DB s daty.

V případě potřeby nainstalujte nástroje pro MongoDB. Následující příklad nainstaluje tyto nástroje používat brew, najdete v článku [dokumentace pro MongoDB] [ install-mongo] další možnosti.

```azurecli-interactive
brew install mongodb
```

Načtěte data do databáze.

```azurecli-interactive
mongoimport --uri=$COSMOS -c plans < plans.json
```

Výstup:

```console
2018-02-19T14:42:14.313+0000    connected to: localhost
2018-02-19T14:42:14.918+0000    imported 1 document
```

Spuštěním následujícího příkazu vytvořte novou funkci. Aktualizujte hodnotu `-g` argument adresou brány OpenFaaS.

```azurecli-interctive
faas-cli deploy -g http://52.186.64.52:8080 --image=shanepeckham/openfaascosmos --name=cosmos-query --env=NODE_ENV=$COSMOS
```

Po nasazení, měli byste vidět váš nově vytvořený OpenFaaS koncový bod pro funkci.

```console
Deployed. 202 Accepted.
URL: http://52.186.64.52:8080/function/cosmos-query
```

Otestujte funkci pomocí curl. Aktualizujte adresu IP adresu brány OpenFaaS.

```console
curl -s http://52.186.64.52:8080/function/cosmos-query
```

Výstup:

```json
[{"ID":"","Name":"two_person","FriendlyName":"","PortionSize":"","MealsPerWeek":"","Price":72,"Description":"Our basic plan, delivering 3 meals per week, which will feed 1-2 people."}]
```

Můžete také otestovat funkci v rámci OpenFaaS uživatelského rozhraní.

![alternativní text](media/container-service-serverless/OpenFaaSUI.png)

## <a name="next-steps"></a>Další kroky

Výchozím nasazení produktu OpenFaas musí být uzamčen pro bránu OpenFaaS i funkce. [Alex Ellis Blogový příspěvek](https://blog.alexellis.io/lock-down-openfaas/) obsahuje další podrobnosti o možnostech konfigurace zabezpečeného.

<!-- LINKS - external -->
[install-mongo]: https://docs.mongodb.com/manual/installation/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[open-faas]: https://www.openfaas.com/
[open-faas-cli]: https://github.com/openfaas/faas-cli
