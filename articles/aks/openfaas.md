---
title: Použití OpenFaaS se službou Azure Kubernetes Service (AKS)
description: Naučte se, jak nasadit a používat OpenFaaS v clusteru Azure Kubernetes Service (AKS) k sestavování funkcí bez serveru pomocí kontejnerů.
author: justindavies
ms.topic: conceptual
ms.date: 03/05/2018
ms.author: juda
ms.custom: mvc
ms.openlocfilehash: 98b1842f81703041f419850be17c0c05a24b7c6b
ms.sourcegitcommit: bf1340bb706cf31bb002128e272b8322f37d53dd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/03/2020
ms.locfileid: "89440894"
---
# <a name="using-openfaas-on-aks"></a>Používání OpenFaaS v AKS

[OpenFaaS][open-faas] je architektura pro sestavování funkcí bez serveru prostřednictvím použití kontejnerů. Jako otevřený zdrojový projekt získal v rámci komunity rozsáhlé přijetí. Tento dokument popisuje, jak nainstalovat a používat OpenFaas v clusteru Azure Kubernetes Service (AKS).

## <a name="prerequisites"></a>Požadavky

K provedení kroků v tomto článku budete potřebovat následující.

* Základní porozumění Kubernetes.
* Cluster Azure Kubernetes Service (AKS) a přihlašovací údaje AKS nakonfigurované ve vývojovém systému.
* Rozhraní příkazového řádku Azure je nainstalované ve vývojovém systému.
* Nástroje příkazového řádku Git nainstalované ve vašem systému.

## <a name="add-the-openfaas-helm-chart-repo"></a>Přidání úložiště grafu OpenFaaS Helm

[https://shell.azure.com](https://shell.azure.com)V prohlížeči otevřete Azure Cloud Shell.

OpenFaaS udržuje své vlastní Helm grafy, aby se zajistila aktuálnost všech nejnovějších změn.

```console
helm repo add openfaas https://openfaas.github.io/faas-netes/
helm repo update
```

## <a name="deploy-openfaas"></a>Nasazení OpenFaaS

Osvědčeným postupem je, že funkce OpenFaaS a OpenFaaS by měly být uloženy ve vlastním oboru názvů Kubernetes.

Vytvoření oboru názvů pro OpenFaaS systém a funkce:

```console
kubectl apply -f https://raw.githubusercontent.com/openfaas/faas-netes/master/namespaces.yml
```

Vygenerujte heslo pro portál uživatelského rozhraní OpenFaaS a REST API:

```console
# generate a random password
PASSWORD=$(head -c 12 /dev/urandom | shasum| cut -d' ' -f1)

kubectl -n openfaas create secret generic basic-auth \
--from-literal=basic-auth-user=admin \
--from-literal=basic-auth-password="$PASSWORD"
```

Hodnotu tajného kódu můžete získat pomocí `echo $PASSWORD` .

Heslo, které tady vytvoříte, bude používat graf Helm k povolení základního ověřování pro bránu OpenFaaS, která je vystavená Internetu prostřednictvím služby Vyrovnávání zatížení cloudu.

Helm graf pro OpenFaaS je součástí klonovaného úložiště. Pomocí tohoto grafu nasaďte OpenFaaS do svého clusteru AKS.

```console
helm upgrade openfaas --install openfaas/openfaas \
    --namespace openfaas  \
    --set basic_auth=true \
    --set functionNamespace=openfaas-fn \
    --set serviceType=LoadBalancer
```

Výstup:

```output
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

```console
kubectl --namespace=openfaas get deployments -l "release=openfaas, app=openfaas"
```

Pro přístup k bráně OpenFaaS se vytvoří veřejná IP adresa. Pokud chcete načíst tuto IP adresu, použijte příkaz [kubectl Get Service][kubectl-get] . Přiřazení IP adresy službě může trvat několik minut.

```console
kubectl get service -l component=gateway --namespace openfaas
```

Výkonem.

```output
NAME               TYPE           CLUSTER-IP     EXTERNAL-IP    PORT(S)          AGE
gateway            ClusterIP      10.0.156.194   <none>         8080/TCP         7m
gateway-external   LoadBalancer   10.0.28.18     52.186.64.52   8080:30800/TCP   7m
```

Chcete-li otestovat systém OpenFaaS, přejděte v tomto příkladu na externí IP adresu na portu 8080 `http://52.186.64.52:8080` . Zobrazí se výzva, abyste se přihlásili. Pokud chcete načíst heslo, zadejte `echo $PASSWORD` .

![Uživatelské rozhraní OpenFaaS](media/container-service-serverless/openfaas.png)

Nakonec nainstalujte rozhraní příkazového řádku OpenFaaS. V tomto příkladu se používá Brew. Další možnosti najdete v dokumentaci k rozhraní příkazového [řádku OpenFaaS][open-faas-cli] .

```console
brew install faas-cli
```

Nastavte `$OPENFAAS_URL` na veřejnou IP adresu, která se našla výše.

Přihlaste se pomocí Azure CLI:

```console
export OPENFAAS_URL=http://52.186.64.52:8080
echo -n $PASSWORD | ./faas-cli login -g $OPENFAAS_URL -u admin --password-stdin
```

## <a name="create-first-function"></a>Vytvořit první funkci

Teď, když je OpenFaaS funkční, vytvořte funkci pomocí portálu OpenFaas.

Klikněte na **nasadit novou funkci** a vyhledejte **FIGlet**. Vyberte funkci FIGlet a klikněte na **nasadit**.

![Figlet](media/container-service-serverless/figlet.png)

K vyvolání funkce použijte oblé. V následujícím příkladu nahraďte IP adresu bránou OpenFaas.

```console
curl -X POST http://52.186.64.52:8080/function/figlet -d "Hello Azure"
```

Výstup:

```output
 _   _      _ _            _
| | | | ___| | | ___      / \    _____   _ _ __ ___
| |_| |/ _ \ | |/ _ \    / _ \  |_  / | | | '__/ _ \
|  _  |  __/ | | (_) |  / ___ \  / /| |_| | | |  __/
|_| |_|\___|_|_|\___/  /_/   \_\/___|\__,_|_|  \___|

```

## <a name="create-second-function"></a>Vytvořit druhou funkci

Nyní vytvořte druhou funkci. Tento příklad bude nasazen pomocí rozhraní příkazového řádku OpenFaaS a zahrnuje vlastní image kontejneru a načítání dat z Cosmos DB. Před vytvořením funkce je třeba nakonfigurovat několik položek.

Nejprve vytvořte novou skupinu prostředků pro Cosmos DB.

```azurecli-interactive
az group create --name serverless-backing --location eastus
```

Nasaďte instanci CosmosDB typu `MongoDB` . Instance potřebuje jedinečný název, ale aktualizuje `openfaas-cosmos` se na něco jedinečného pro vaše prostředí.

```azurecli-interactive
az cosmosdb create --resource-group serverless-backing --name openfaas-cosmos --kind MongoDB
```

Získejte připojovací řetězec databáze Cosmos a uložte ho do proměnné.

Aktualizujte hodnotu `--resource-group` argumentu na název vaší skupiny prostředků a `--name` argument na název vašeho Cosmos DB.

```azurecli-interactive
COSMOS=$(az cosmosdb list-connection-strings \
  --resource-group serverless-backing \
  --name openfaas-cosmos \
  --query connectionStrings[0].connectionString \
  --output tsv)
```

Nyní naplňte Cosmos DB pomocí testovacích dat. Vytvořte soubor s názvem `plans.json` a zkopírujte ho do následujícího formátu JSON.

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

K načtení instance CosmosDB s daty použijte nástroj *mongoimport* .

V případě potřeby nainstalujte nástroje MongoDB. Následující příklad nainstaluje tyto nástroje pomocí Brew. Další možnosti najdete v [dokumentaci k MongoDB][install-mongo] .

```console
brew install mongodb
```

Načtěte data do databáze.

```console
mongoimport --uri=$COSMOS -c plans < plans.json
```

Výstup:

```output
2018-02-19T14:42:14.313+0000    connected to: localhost
2018-02-19T14:42:14.918+0000    imported 1 document
```

Spuštěním následujícího příkazu vytvořte funkci. Aktualizujte hodnotu `-g` argumentu s adresou brány OpenFaaS.

```console
faas-cli deploy -g http://52.186.64.52:8080 --image=shanepeckham/openfaascosmos --name=cosmos-query --env=NODE_ENV=$COSMOS
```

Po nasazení byste měli vidět nově vytvořený koncový bod OpenFaaS pro danou funkci.

```output
Deployed. 202 Accepted.
URL: http://52.186.64.52:8080/function/cosmos-query
```

Otestujte funkci pomocí funkce kudrlinkou. Aktualizujte IP adresu pomocí adresy brány OpenFaaS.

```console
curl -s http://52.186.64.52:8080/function/cosmos-query
```

Výstup:

```json
[{"ID":"","Name":"two_person","FriendlyName":"","PortionSize":"","MealsPerWeek":"","Price":72,"Description":"Our basic plan, delivering 3 meals per week, which will feed 1-2 people."}]
```

Funkci můžete také otestovat v uživatelském rozhraní OpenFaaS.

![alternativní text](media/container-service-serverless/OpenFaaSUI.png)

## <a name="next-steps"></a>Další kroky

Pomocí sady praktických cvičení můžete pokračovat v seznámení se službou OpenFaaS Workshop, která zahrnuje témata, jako je například vytvoření vlastního robota GitHubu, používání tajných kódů, zobrazení metrik a automatické škálování.

<!-- LINKS - external -->
[install-mongo]: https://docs.mongodb.com/manual/installation/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[open-faas]: https://www.openfaas.com/
[open-faas-cli]: https://github.com/openfaas/faas-cli
[openfaas-workshop]: https://github.com/openfaas/workshop
