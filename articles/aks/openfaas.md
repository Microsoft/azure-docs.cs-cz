---
title: Použití OpenFaaS se službou Azure Kubernetes Service (AKS)
description: Nasazení a používání OpenFaaS se službou Azure Kubernetes Service (AKS)
author: justindavies
ms.service: container-service
ms.topic: conceptual
ms.date: 03/05/2018
ms.author: juda
ms.custom: mvc
ms.openlocfilehash: 7949735eff4478d2d04700e1c6df69d28fe25979
ms.sourcegitcommit: 5397b08426da7f05d8aa2e5f465b71b97a75550b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/19/2020
ms.locfileid: "76278484"
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

OpenFaaS udržuje své vlastní Helm grafy, aby se zajistila aktuálnost všech nejnovějších změn.

```azurecli-interactive
helm repo add openfaas https://openfaas.github.io/faas-netes/
helm repo update
```

## <a name="deploy-openfaas"></a>Nasazení OpenFaaS

Osvědčeným postupem je, že funkce OpenFaaS a OpenFaaS by měly být uloženy ve vlastním oboru názvů Kubernetes.

Vytvoření oboru názvů pro OpenFaaS systém a funkce:

```azurecli-interactive
kubectl apply -f https://raw.githubusercontent.com/openfaas/faas-netes/master/namespaces.yml
```

Vygenerujte heslo pro portál uživatelského rozhraní OpenFaaS a REST API:

```azurecli-interactive
# generate a random password
PASSWORD=$(head -c 12 /dev/urandom | shasum| cut -d' ' -f1)

kubectl -n openfaas create secret generic basic-auth \
--from-literal=basic-auth-user=admin \
--from-literal=basic-auth-password="$PASSWORD"
```

Hodnotu tajného kódu můžete získat pomocí `echo $PASSWORD`.

Heslo, které tady vytvoříte, bude používat graf Helm k povolení základního ověřování pro bránu OpenFaaS, která je vystavená Internetu prostřednictvím služby Vyrovnávání zatížení cloudu.

Helm graf pro OpenFaaS je součástí klonovaného úložiště. Pomocí tohoto grafu nasaďte OpenFaaS do svého clusteru AKS.

```azurecli-interactive
helm upgrade openfaas --install openfaas/openfaas \
    --namespace openfaas  \
    --set basic_auth=true \
    --set functionNamespace=openfaas-fn \
    --set serviceType=LoadBalancer
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

Pro přístup k bráně OpenFaaS se vytvoří veřejná IP adresa. Pokud chcete načíst tuto IP adresu, použijte příkaz [kubectl Get Service][kubectl-get] . Přiřazení IP adresy službě může trvat několik minut.

```console
kubectl get service -l component=gateway --namespace openfaas
```

Výkonem.

```console
NAME               TYPE           CLUSTER-IP     EXTERNAL-IP    PORT(S)          AGE
gateway            ClusterIP      10.0.156.194   <none>         8080/TCP         7m
gateway-external   LoadBalancer   10.0.28.18     52.186.64.52   8080:30800/TCP   7m
```

Chcete-li otestovat systém OpenFaaS, přejděte na externí IP adresu na portu 8080, v tomto příkladu `http://52.186.64.52:8080`. Zobrazí se výzva, abyste se přihlásili. Pokud chcete načíst heslo, zadejte `echo $PASSWORD`.

![Uživatelské rozhraní OpenFaaS](media/container-service-serverless/openfaas.png)

Nakonec nainstalujte rozhraní příkazového řádku OpenFaaS. V tomto příkladu se používá Brew. Další možnosti najdete v dokumentaci k rozhraní příkazového [řádku OpenFaaS][open-faas-cli] .

```console
brew install faas-cli
```

Nastavte `$OPENFAAS_URL` na veřejnou IP adresu, kterou jsme našli výše.

Přihlaste se pomocí Azure CLI:

```azurecli-interactive
export OPENFAAS_URL=http://52.186.64.52:8080
echo -n $PASSWORD | ./faas-cli login -g $OPENFAAS_URL -u admin --password-stdin
```

## <a name="create-first-function"></a>Vytvořit první funkci

Teď, když je OpenFaaS funkční, vytvořte funkci pomocí portálu OpenFaas.

Klikněte na **nasadit novou funkci** a vyhledejte **FIGlet**. Vyberte funkci FIGlet a klikněte na **nasadit**.

![Figlet](media/container-service-serverless/figlet.png)

K vyvolání funkce použijte oblé. V následujícím příkladu nahraďte IP adresu bránou OpenFaas.

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

## <a name="create-second-function"></a>Vytvořit druhou funkci

Nyní vytvořte druhou funkci. Tento příklad bude nasazen pomocí rozhraní příkazového řádku OpenFaaS a zahrnuje vlastní image kontejneru a načítání dat z Cosmos DB. Před vytvořením funkce je třeba nakonfigurovat několik položek.

Nejprve vytvořte novou skupinu prostředků pro Cosmos DB.

```azurecli-interactive
az group create --name serverless-backing --location eastus
```

Nasaďte CosmosDB instanci typu `MongoDB`. Instance vyžaduje jedinečný název, `openfaas-cosmos` aktualizace na něco jedinečného pro vaše prostředí.

```azurecli-interactive
az cosmosdb create --resource-group serverless-backing --name openfaas-cosmos --kind MongoDB
```

Získejte připojovací řetězec databáze Cosmos a uložte ho do proměnné.

Aktualizujte hodnotu argumentu `--resource-group` na název vaší skupiny prostředků a argument `--name` na název vašeho Cosmos DB.

```azurecli-interactive
COSMOS=$(az cosmosdb list-connection-strings \
  --resource-group serverless-backing \
  --name openfaas-cosmos \
  --query connectionStrings[0].connectionString \
  --output tsv)
```

Nyní naplňte Cosmos DB pomocí testovacích dat. Vytvořte soubor s názvem `plans.json` a zkopírujte následující JSON.

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

Spuštěním následujícího příkazu vytvořte funkci. Aktualizujte hodnotu argumentu `-g` s adresou brány OpenFaaS.

```azurecli-interctive
faas-cli deploy -g http://52.186.64.52:8080 --image=shanepeckham/openfaascosmos --name=cosmos-query --env=NODE_ENV=$COSMOS
```

Po nasazení byste měli vidět nově vytvořený koncový bod OpenFaaS pro danou funkci.

```console
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
