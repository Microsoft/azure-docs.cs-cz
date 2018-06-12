---
title: OpenFaaS pomocí služby Azure Kubernetes (AKS)
description: Nasazení a používání OpenFaaS s Azure Kubernetes služby (AKS)
services: container-service
author: justindavies
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 03/05/2018
ms.author: juda
ms.custom: mvc
ms.openlocfilehash: b5484233c7d3d32e51098baad8c22ec51df8f0d8
ms.sourcegitcommit: 1b8665f1fff36a13af0cbc4c399c16f62e9884f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/11/2018
ms.locfileid: "35260613"
---
# <a name="using-openfaas-on-aks"></a>Pomocí OpenFaaS na AKS

[OpenFaaS] [ open-faas] je architektura pro vytváření bez serveru funkce nad kontejnery. Jako Opensourcový projekt získala rozsáhlé přijetí v rámci komunity. Tento dokument podrobně popisuje instalaci a použití OpenFaas v clusteru Azure Kubernetes služby (AKS).

## <a name="prerequisites"></a>Požadavky

Aby bylo možné provést kroky v tomto článku, budete potřebovat následující.

* Základní znalosti o Kubernetes.
* Cluster služby Azure Kubernetes služby (AKS) a přihlašovací údaje AKS nakonfigurované ve vývojovém systému.
* Azure CLI nainstalován ve vývojovém systému.
* Nástroje příkazového řádku Gitu v systému nainstalována.

## <a name="get-openfaas"></a>Get OpenFaaS

Naklonujte úložiště projektu OpenFaaS na váš vývojový systém.

```azurecli-interactive
git clone https://github.com/openfaas/faas-netes
```

Přejděte do adresáře klonovaný úložiště.

```azurecli-interactive
cd faas-netes
```

## <a name="deploy-openfaas"></a>Nasazení OpenFaaS

Jako osvědčených postupů, OpenFaaS a OpenFaaS funkce by měly být uložené v vlastní Kubernetes oboru názvů.

Vytvořte obor názvů pro systém OpenFaaS.

```azurecli-interactive
kubectl create namespace openfaas
```

Vytvoření druhého oboru názvů pro funkce OpenFaaS.

```azurecli-interactive
kubectl create namespace openfaas-fn
```

Graf Helm pro OpenFaaS je součástí klonovaný úložiště. Tento graf použijte k nasazení OpenFaaS do AKS clusteru.

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

Pro přístup k bráně OpenFaaS se vytvoří veřejnou IP adresu. Chcete-li získat tuto IP adresu, použijte [kubectl získat služby] [ kubectl-get] příkaz. Může trvat několik minut pro IP adresu pro přiřazení ke službě.

```console
kubectl get service -l component=gateway --namespace openfaas
```

Výstup.

```console
NAME               TYPE           CLUSTER-IP     EXTERNAL-IP    PORT(S)          AGE
gateway            ClusterIP      10.0.156.194   <none>         8080/TCP         7m
gateway-external   LoadBalancer   10.0.28.18     52.186.64.52   8080:30800/TCP   7m
```

Chcete-li otestovat OpenFaaS systému, procházejte k externí IP adresu na portu 8080, `http://52.186.64.52:8080` v tomto příkladu.

![OpenFaaS uživatelského rozhraní](media/container-service-serverless/openfaas.png)

Nainstalujte rozhraní příkazového řádku OpenFaaS. Tento příklad používá brew najdete [dokumentaci k rozhraní příkazového řádku OpenFaaS] [ open-faas-cli] další možnosti.

```console
brew install faas-cli
```

## <a name="create-first-function"></a>Vytvoření první funkce

Teď, když je provozní OpenFaaS, vytvořte pomocí portálu OpenFaas funkci.

Klikněte na **nasadit novou funkci** a vyhledejte **Figlet**. Vyberte funkci Figlet a klikněte na tlačítko **nasadit**.

![Figlet](media/container-service-serverless/figlet.png)

Pomocí curl k vyvolání funkce. IP adresa v následujícím příkladu nahraďte který OpenFaas brány.

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

## <a name="create-second-function"></a>Vytvořte druhý – funkce

Teď vytvořte druhý funkce. Tento příklad se nasadí pomocí rozhraní příkazového řádku OpenFaaS a obsahuje bitovou kopii vlastní kontejner a načítání dat z databáze Cosmos. Několik položek je potřeba nakonfigurovat před vytvořením funkce.

Nejprve vytvořte novou skupinu prostředků pro databázi Cosmos.

```azurecli-interactive
az group create --name serverless-backing --location eastus
```

Nasadit CosmosDB instanci typu `MongoDB`. Instance potřebuje jedinečný název, aktualizujte `openfaas-cosmos` něco jedinečné pro vaše prostředí.

```azurecli-interactive
az cosmosdb create --resource-group serverless-backing --name openfaas-cosmos --kind MongoDB
```

Získání Cosmos připojovací řetězec databáze a uloží jej v proměnné.

Aktualizujte hodnotu `--resource-group` argument na název vaší skupiny prostředků a `--name` argument na název vaší databáze Cosmos.

```azurecli-interactive
COSMOS=$(az cosmosdb list-connection-strings \
  --resource-group serverless-backing \
  --name openfaas-cosmos \
  --query connectionStrings[0].connectionString \
  --output tsv)
```

Nyní naplníte databázi Cosmos s testovacích datech. Vytvořte soubor s názvem `plans.json` a zkopírujte následující kód json.

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

Použití *mongoimport* nástroj k načtení instance CosmosDB s daty.

V případě potřeby nainstalujte nástroje pro MongoDB. Následující příklad nainstaluje tyto nástroje pomocí brew najdete [dokumentace k MongoDB] [ install-mongo] pro další možnosti.

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

Spusťte následující příkaz pro vytvoření funkce. Aktualizujte hodnotu `-g` argument s touto adresou OpenFaaS brány.

```azurecli-interctive
faas-cli deploy -g http://52.186.64.52:8080 --image=shanepeckham/openfaascosmos --name=cosmos-query --env=NODE_ENV=$COSMOS
```

Po nasazení, měli byste vidět nově vytvořený koncový bod OpenFaaS pro funkci.

```console
Deployed. 202 Accepted.
URL: http://52.186.64.52:8080/function/cosmos-query
```

Testování funkce pomocí curl. Aktualizujte adresu IP adresu brány OpenFaaS.

```console
curl -s http://52.186.64.52:8080/function/cosmos-query
```

Výstup:

```json
[{"ID":"","Name":"two_person","FriendlyName":"","PortionSize":"","MealsPerWeek":"","Price":72,"Description":"Our basic plan, delivering 3 meals per week, which will feed 1-2 people."}]
```

Můžete také otestovat funkci v rámci rozhraní OpenFaaS.

![alternativní text](media/container-service-serverless/OpenFaaSUI.png)

## <a name="next-steps"></a>Další kroky

Výchozí nasazení OpenFaas musí být uzamčena pro OpenFaaS brány a funkce. [Příspěvek blogu Alex Ellis](https://blog.alexellis.io/lock-down-openfaas/) obsahuje další podrobnosti o možnostech konfigurace zabezpečeného.

<!-- LINKS - external -->
[install-mongo]: https://docs.mongodb.com/manual/installation/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[open-faas]: https://www.openfaas.com/
[open-faas-cli]: https://github.com/openfaas/faas-cli
