---
title: Použití OpenFaaS se službou Azure Kubernetes Service (AKS)
description: Zjistěte, jak nasadit a používat OpenFaaS v clusteru Azure Kubernetes Service (AKS) k vytváření bezserverových funkcí s kontejnery.
author: justindavies
ms.topic: conceptual
ms.date: 03/05/2018
ms.author: juda
ms.custom: mvc
ms.openlocfilehash: 95039573c607f516755f08f1ebad8b968416ec8b
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/03/2020
ms.locfileid: "80631477"
---
# <a name="using-openfaas-on-aks"></a>Použití OpenFaaS na AKS

[OpenFaaS][open-faas] je framework pro vytváření bezserverových funkcí pomocí kontejnerů. Jako open source projekt získal v rámci komunity rozsáhlé přijetí. Tento dokument podrobně popisuje instalaci a používání OpenFaas v clusteru Služby Azure Kubernetes (AKS).

## <a name="prerequisites"></a>Požadavky

Chcete-li provést kroky v tomto článku, budete potřebovat následující.

* Základní znalost Kubernetes.
* Cluster Azure Kubernetes Service (AKS) a přihlašovací údaje AKS nakonfigurované ve vašem vývojovém systému.
* Azure CLI nainstalované ve vašem vývojovém systému.
* Nástroje příkazového řádku Git nainstalované ve vašem systému.

## <a name="add-the-openfaas-helm-chart-repo"></a>Přidání repo grafu kormidelníku OpenFaaS

Přejděte [https://shell.azure.com](https://shell.azure.com) na otevření Azure Cloud Shell ve vašem prohlížeči.

OpenFaaS udržuje své vlastní kormidelní grafy, aby udržel krok se všemi nejnovějšími změnami.

```console
helm repo add openfaas https://openfaas.github.io/faas-netes/
helm repo update
```

## <a name="deploy-openfaas"></a>Nasazení OpenFaaS

Jako dobrý postup, OpenFaaS a OpenFaaS funkce by měly být uloženy ve vlastním oboru názvů Kubernetes.

Vytvořte obor názvů pro systém OpenFaaS a funkce:

```console
kubectl apply -f https://raw.githubusercontent.com/openfaas/faas-netes/master/namespaces.yml
```

Vygenerujte heslo pro portál UI OpenFaaS a ROZHRANÍ REST API:

```console
# generate a random password
PASSWORD=$(head -c 12 /dev/urandom | shasum| cut -d' ' -f1)

kubectl -n openfaas create secret generic basic-auth \
--from-literal=basic-auth-user=admin \
--from-literal=basic-auth-password="$PASSWORD"
```

Můžete získat hodnotu tajemství `echo $PASSWORD`s .

Heslo, které zde vytvoříme, bude použito kormidelním grafem k povolení základního ověřování na bráně OpenFaaS, která je vystavena internetu prostřednictvím cloudového LoadBalancer.

Graf Helm pro OpenFaaS je součástí klonovaného úložiště. Tento graf slouží k nasazení OpenFaaS do clusteru AKS.

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

  kubectl --namespace=openfaas get deployments -l "release=openfaas, app=openfaas"
```

Pro přístup k bráně OpenFaaS je vytvořena veřejná IP adresa. Chcete-li načíst tuto ADRESU IP, použijte příkaz [kubectl get service.][kubectl-get] Může trvat minutu, než bude ip adresa přiřazena ke službě.

```console
kubectl get service -l component=gateway --namespace openfaas
```

Výstup.

```output
NAME               TYPE           CLUSTER-IP     EXTERNAL-IP    PORT(S)          AGE
gateway            ClusterIP      10.0.156.194   <none>         8080/TCP         7m
gateway-external   LoadBalancer   10.0.28.18     52.186.64.52   8080:30800/TCP   7m
```

Chcete-li otestovat systém OpenFaaS, vyhledejte `http://52.186.64.52:8080` v tomto příkladu externí IP adresu na portu 8080. Budete vyzváni k přihlášení. Chcete-li načíst `echo $PASSWORD`heslo, zadejte .

![OpenFaaS UI](media/container-service-serverless/openfaas.png)

Nakonec nainstalujte Cli OpenFaaS. Tento příklad používá vařit, viz [OpenFaaS CLI dokumentace][open-faas-cli] pro další možnosti.

```console
brew install faas-cli
```

Nastavte `$OPENFAAS_URL` na veřejnou IP adresu nalezenou výše.

Přihlaste se pomocí azure cli:

```console
export OPENFAAS_URL=http://52.186.64.52:8080
echo -n $PASSWORD | ./faas-cli login -g $OPENFAAS_URL -u admin --password-stdin
```

## <a name="create-first-function"></a>Vytvořit první funkci

Teď, když je OpenFaaS funkční, vytvořte funkci pomocí portálu OpenFaas.

Klikněte na **Nasadit novou funkci** a vyhledejte **Figlet**. Vyberte funkci Figlet a klepněte na tlačítko **Nasadit**.

![Nafiglet](media/container-service-serverless/figlet.png)

Pomocí curl vyvolat funkci. Nahraďte IP adresu v následujícím příkladu adresou vaší brány OpenFaas.

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

Nyní vytvořte druhou funkci. Tento příklad bude nasazen pomocí OpenFaaS CLI a zahrnuje vlastní image kontejneru a načítání dat z Cosmos DB. Před vytvořením funkce je třeba nakonfigurovat několik položek.

Nejprve vytvořte novou skupinu prostředků pro Cosmos DB.

```azurecli-interactive
az group create --name serverless-backing --location eastus
```

Nasazení instance CosmosDB `MongoDB`druhu . Instance potřebuje jedinečný název, `openfaas-cosmos` aktualizovat na něco jedinečného pro vaše prostředí.

```azurecli-interactive
az cosmosdb create --resource-group serverless-backing --name openfaas-cosmos --kind MongoDB
```

Získejte připojovací řetězec databáze Cosmos a uložte jej do proměnné.

Aktualizujte hodnotu `--resource-group` argumentu na název skupiny `--name` prostředků a argument na název cosmos db.

```azurecli-interactive
COSMOS=$(az cosmosdb list-connection-strings \
  --resource-group serverless-backing \
  --name openfaas-cosmos \
  --query connectionStrings[0].connectionString \
  --output tsv)
```

Nyní naplňte Cosmos DB testovacími daty. Vytvořte soubor `plans.json` s názvem a zkopírujte v následujícím json.

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

Pomocí nástroje *mongoimport* načtěte instanci CosmosDB s daty.

V případě potřeby nainstalujte nástroje MongoDB. Následující příklad nainstaluje tyto nástroje pomocí vařit, naleznete v [dokumentaci MongoDB][install-mongo] pro další možnosti.

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

Chcete-li vytvořit funkci, spusťte následující příkaz. Aktualizujte hodnotu `-g` argumentu adresou brány OpenFaaS.

```console
faas-cli deploy -g http://52.186.64.52:8080 --image=shanepeckham/openfaascosmos --name=cosmos-query --env=NODE_ENV=$COSMOS
```

Po nasazení byste měli vidět nově vytvořený koncový bod OpenFaaS pro funkci.

```output
Deployed. 202 Accepted.
URL: http://52.186.64.52:8080/function/cosmos-query
```

Otestujte funkci pomocí zvlnění. Aktualizujte IP adresu adresou brány OpenFaaS.

```console
curl -s http://52.186.64.52:8080/function/cosmos-query
```

Výstup:

```json
[{"ID":"","Name":"two_person","FriendlyName":"","PortionSize":"","MealsPerWeek":"","Price":72,"Description":"Our basic plan, delivering 3 meals per week, which will feed 1-2 people."}]
```

Funkci můžete také otestovat v rámci ustálené ho suopenské strany OpenFaaS.

![alternativní text](media/container-service-serverless/OpenFaaSUI.png)

## <a name="next-steps"></a>Další kroky

Můžete pokračovat v učení s OpenFaaS workshop prostřednictvím sady praktických laboratoří, které pokrývají témata, jako je například jak vytvořit vlastní GitHub bot, náročné tajné kódy, zobrazení metriky a automatické škálování.

<!-- LINKS - external -->
[install-mongo]: https://docs.mongodb.com/manual/installation/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[open-faas]: https://www.openfaas.com/
[open-faas-cli]: https://github.com/openfaas/faas-cli
[openfaas-workshop]: https://github.com/openfaas/workshop
