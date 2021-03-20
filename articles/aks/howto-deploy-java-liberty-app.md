---
title: Nasazení aplikace v jazyce Java s otevřeným nástrojem svobody nebo WebSphere Svoboda v clusteru Azure Kubernetes Service (AKS)
description: Nasaďte aplikaci Java s otevřeným nebo WebSphere Svobodam v clusteru Azure Kubernetes Service (AKS).
author: jiangma
ms.author: jiangma
ms.service: container-service
ms.topic: conceptual
ms.date: 02/01/2021
keywords: Java, jakartaee, JavaEE, mikroprofile, Open-svoboda, WebSphere-svoboda, AKS, Kubernetes
ms.openlocfilehash: d0e6f2fea6894378da736ba83a90ee28402ec7f9
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "100007124"
---
# <a name="deploy-a-java-application-with-open-liberty-or-websphere-liberty-on-an-azure-kubernetes-service-aks-cluster"></a>Nasazení aplikace v jazyce Java s otevřeným nástrojem svobody nebo WebSphere Svoboda v clusteru Azure Kubernetes Service (AKS)

Tento článek ukazuje, jak:  
* Spusťte aplikaci v jazyce Java, Java EE, Jakarta EE nebo mikroprofile na otevřeném prostředí pro práci s WebSphere nebo svobody.
* Sestavte image Docker aplikace pomocí otevřených imagí kontejnerů.
* Nasaďte kontejnerové aplikace do clusteru AKS pomocí otevřeného operátoru svoboda.   

Otevřený operátor svoboda zjednodušuje nasazení a správu aplikací spuštěných v clusterech Kubernetes. V případě otevřeného operátoru svoboda můžete také provádět pokročilejší operace, například shromažďování trasování a výpisů paměti. 

Další informace o otevřeném počítači můžete zobrazit [na stránce otevřít a projekt](https://openliberty.io/). Další podrobnosti o IBM WebSphere svobody najdete [na stránce produktu WebSphere svobody](https://www.ibm.com/cloud/websphere-liberty).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

* Tento článek vyžaduje nejnovější verzi rozhraní příkazového řádku Azure CLI. Pokud používáte Azure Cloud Shell, nejnovější verze je už nainstalovaná.
* Pokud se v této příručce spouští příkazy místně (místo Azure Cloud Shell):
  * Připravte místní počítač s nainstalovaným operačním systémem, který má systém UNIX (například Ubuntu, macOS, podsystém Windows pro Linux).
  * Nainstalujte implementaci Java SE systémem (například [AdoptOpenJDK OpenJDK 8 LTS/OpenJ9](https://adoptopenjdk.net/?variant=openjdk8&jvmVariant=openj9)).
  * Nainstalujte [Maven](https://maven.apache.org/download.cgi) 3.5.0 nebo vyšší.
  * Nainstalujte [Docker](https://docs.docker.com/get-docker/) pro váš operační systém.

## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Skupina prostředků Azure je logická skupina, ve které se nasazují a spravují prostředky Azure.  

Vytvořte skupinu prostředků s názvem *Java-svoboda-Project* pomocí příkazu [AZ Group Create](/cli/azure/group#az_group_create) v umístění *eastus* . Tato skupina prostředků se použije později pro vytvoření instance Azure Container Registry (ACR) a clusteru AKS. 

```azurecli-interactive
RESOURCE_GROUP_NAME=java-liberty-project
az group create --name $RESOURCE_GROUP_NAME --location eastus
```

## <a name="create-an-acr-instance"></a>Vytvoření instance ACR

Instanci ACR vytvoříte pomocí příkazu [AZ ACR Create](/cli/azure/acr#az_acr_create) . Následující příklad vytvoří instanci ACR s názvem *youruniqueacrname*. Ujistěte se, že *youruniqueacrname* je v rámci Azure jedinečný.

```azurecli-interactive
REGISTRY_NAME=youruniqueacrname
az acr create --resource-group $RESOURCE_GROUP_NAME --name $REGISTRY_NAME --sku Basic --admin-enabled
```

Po krátké době by se měl zobrazit výstup JSON obsahující:

```output
  "provisioningState": "Succeeded",
  "publicNetworkAccess": "Enabled",
  "resourceGroup": "java-liberty-project",
```

### <a name="connect-to-the-acr-instance"></a>Připojení k instanci ACR

Před nahráním image do této instance se budete muset přihlásit k instanci ACR. Spusťte následující příkazy a ověřte připojení:

```azurecli-interactive
LOGIN_SERVER=$(az acr show -n $REGISTRY_NAME --query 'loginServer' -o tsv)
USER_NAME=$(az acr credential show -n $REGISTRY_NAME --query 'username' -o tsv)
PASSWORD=$(az acr credential show -n $REGISTRY_NAME --query 'passwords[0].value' -o tsv)

docker login $LOGIN_SERVER -u $USER_NAME -p $PASSWORD
```

Pokud jste se `Login Succeeded` úspěšně přihlásili k instanci ACR, měli byste vidět na konci příkazu OUTPUT.

## <a name="create-an-aks-cluster"></a>Vytvoření clusteru AKS

Pomocí příkazu [az aks create](/cli/azure/aks#az_aks_create) vytvořte cluster AKS. Následující příklad vytvoří cluster *myAKSCluster* s jedním uzlem. Dokončení této akce bude trvat několik minut.

```azurecli-interactive
CLUSTER_NAME=myAKSCluster
az aks create --resource-group $RESOURCE_GROUP_NAME --name $CLUSTER_NAME --node-count 1 --generate-ssh-keys --enable-managed-identity
```

Po několika minutách se příkaz dokončí a vrátí informace o clusteru ve formátu JSON, včetně následujících:

```output
  "nodeResourceGroup": "MC_java-liberty-project_myAKSCluster_eastus",
  "privateFqdn": null,
  "provisioningState": "Succeeded",
  "resourceGroup": "java-liberty-project",
```

### <a name="connect-to-the-aks-cluster"></a>Připojení ke clusteru AKS

Ke správě clusteru Kubernetes použijete klienta příkazového řádku Kubernetes [kubectl](https://kubernetes.io/docs/reference/kubectl/overview/). Pokud používáte Azure Cloud Shell, `kubectl` je již nainstalováno. Pokud chcete nainstalovat `kubectl` místně, použijte příkaz [AZ AKS Install-CLI](/cli/azure/aks#az_aks_install_cli) :

```azurecli-interactive
az aks install-cli
```

Pomocí příkazu [az aks get-credentials](/cli/azure/aks#az_aks_get_credentials) nakonfigurujte klienta `kubectl` pro připojení k vašemu clusteru Kubernetes. Tento příkaz stáhne pověření a nakonfiguruje rozhraní příkazového řádku Kubernetes pro jejich použití.

```azurecli-interactive
az aks get-credentials --resource-group $RESOURCE_GROUP_NAME --name $CLUSTER_NAME --overwrite-existing
```

> [!NOTE]
> Výše uvedený příkaz používá výchozí umístění [konfiguračního souboru Kubernetes](https://kubernetes.io/docs/concepts/configuration/organize-cluster-access-kubeconfig/), který je `~/.kube/config` . Můžete zadat jiné umístění konfiguračního souboru Kubernetes pomocí *--File*.

Pokud chcete ověřit připojení ke clusteru, použijte příkaz [kubectl get]( https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get), který vrátí seznam uzlů clusteru.

```azurecli-interactive
kubectl get nodes
```

Následující příklad výstupu ukazuje jeden uzel vytvořený v předchozích krocích. Ujistěte se, že stav uzlu je *připravený*:

```output
NAME                                STATUS   ROLES   AGE     VERSION
aks-nodepool1-xxxxxxxx-yyyyyyyyyy   Ready    agent   76s     v1.18.10
```

## <a name="install-open-liberty-operator"></a>Nainstalovat otevřený operátor svoboda

Po vytvoření a připojení ke clusteru nainstalujte pomocí následujících příkazů [otevřený operátor Open](https://github.com/OpenLiberty/open-liberty-operator/tree/master/deploy/releases/0.7.0) .

```azurecli-interactive
OPERATOR_NAMESPACE=default
WATCH_NAMESPACE='""'

# Install Custom Resource Definitions (CRDs) for OpenLibertyApplication
kubectl apply -f https://raw.githubusercontent.com/OpenLiberty/open-liberty-operator/master/deploy/releases/0.7.0/openliberty-app-crd.yaml

# Install cluster-level role-based access
curl -L https://raw.githubusercontent.com/OpenLiberty/open-liberty-operator/master/deploy/releases/0.7.0/openliberty-app-cluster-rbac.yaml \
      | sed -e "s/OPEN_LIBERTY_OPERATOR_NAMESPACE/${OPERATOR_NAMESPACE}/" \
      | kubectl apply -f -

# Install the operator
curl -L https://raw.githubusercontent.com/OpenLiberty/open-liberty-operator/master/deploy/releases/0.7.0/openliberty-app-operator.yaml \
      | sed -e "s/OPEN_LIBERTY_WATCH_NAMESPACE/${WATCH_NAMESPACE}/" \
      | kubectl apply -n ${OPERATOR_NAMESPACE} -f -
```

## <a name="build-application-image"></a>Sestavit obrázek aplikace

Pokud chcete nasadit a spustit vaši aplikaci svobody v clusteru AKS, kontejnerizace aplikaci jako image Docker pomocí [otevřených imagí kontejnerů](https://github.com/OpenLiberty/ci.docker) nebo WebSphere. [Image kontejnerů](https://github.com/WASdev/ci.docker).

1. Naklonujte vzorový kód pro tento průvodce. Ukázka je na [GitHubu](https://github.com/Azure-Samples/open-liberty-on-aks).
1. Změňte adresář na `javaee-app-simple-cluster` svůj místní klon.
1. Spusťte příkaz `mvn clean package` pro zabalení aplikace.
1. Spusťte `mvn liberty:dev` pro otestování aplikace. V případě úspěchu by se měla zobrazit `The defaultServer server is ready to run a smarter planet.` ve výstupu příkazu. Použijte `CTRL-C` k zastavení aplikace.
1. Spusťte jeden z následujících příkazů a sestavte image aplikace a nahrajte ji do instance ACR.
   * Sestavte se s otevřenými základními obrázky s odlehčeným použitím Open Source Java™ Runtime:

     ```azurecli-interactive
     # Build and tag application image. This will cause the ACR instance to pull the necessary Open Liberty base images.
     az acr build -t javaee-cafe-simple:1.0.0 -r $REGISTRY_NAME .
     ```

   * Sestavte se základní imagí WebSphere svoboda, pokud chcete použít komerční verzi programu Open.

     ```azurecli-interactive
     # Build and tag application image. This will cause the ACR instance to pull the necessary WebSphere Liberty base images.
     az acr build -t javaee-cafe-simple:1.0.0 -r $REGISTRY_NAME --file=Dockerfile-wlp .
     ```

## <a name="deploy-application-on-the-aks-cluster"></a>Nasazení aplikace v clusteru AKS

Pomocí následujících kroků Nasaďte aplikaci svobody v clusteru AKS.

1. Vytvořte tajný klíč pro vyžádání, aby byl cluster AKS ověřený pro vyžádání image z instance ACR.

   ```azurecli-interactive
   kubectl create secret docker-registry acr-secret \
      --docker-server=${LOGIN_SERVER} \
      --docker-username=${USER_NAME} \
      --docker-password=${PASSWORD}
   ```

1. Ověřte, že aktuální pracovní adresář má `javaee-app-simple-cluster` svůj místní klon.
1. Spusťte následující příkazy, abyste nasadili vaši aplikaci svobody se 3 replikami do clusteru AKS. Výstup příkazu je také zobrazen jako vložený.

   ```azurecli-interactive
   # Create OpenLibertyApplication "javaee-app-simple-cluster"
   cat openlibertyapplication.yaml | sed -e "s/\${Container_Registry_URL}/${LOGIN_SERVER}/g" | sed -e "s/\${REPLICAS}/3/g" | kubectl apply -f -

   openlibertyapplication.openliberty.io/javaee-app-simple-cluster created

   # Check if OpenLibertyApplication instance is created
   kubectl get openlibertyapplication javaee-app-simple-cluster

   NAME                        IMAGE                                                   EXPOSED   RECONCILED   AGE
   javaee-app-simple-cluster   youruniqueacrname.azurecr.io/javaee-cafe-simple:1.0.0             True         59s

   # Check if deployment created by Operator is ready
   kubectl get deployment javaee-app-simple-cluster --watch

   NAME                        READY   UP-TO-DATE   AVAILABLE   AGE
   javaee-app-simple-cluster   0/3     3            0           20s
   ```

1. Počkejte, dokud se nezobrazí `3/3` pod `READY` sloupcem a `3` pod ním, a `AVAILABLE` použijte `CTRL-C` k zastavení `kubectl` procesu sledování.

### <a name="test-the-application"></a>Testování aplikace

Když je aplikace spuštěná, služba Vyrovnávání zatížení Kubernetes zpřístupňuje front-end aplikace na internetu. Dokončení tohoto procesu může nějakou dobu trvat.

Pomocí příkazu [kubectl get service](https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get) s argumentem `--watch` můžete sledovat průběh.

```azurecli-interactive
kubectl get service javaee-app-simple-cluster --watch

NAME                        TYPE           CLUSTER-IP     EXTERNAL-IP     PORT(S)          AGE
javaee-app-simple-cluster   LoadBalancer   10.0.251.169   52.152.189.57   80:31732/TCP     68s
```

Jakmile se adresa *External-IP* změní ze *stavu čeká* na skutečnou veřejnou IP adresu, použijte `CTRL-C` k zastavení `kubectl` procesu sledování.

Otevřete webový prohlížeč na externí IP adresu vaší služby ( `52.152.189.57` pro výše uvedený příklad) a zobrazte tak domovskou stránku aplikace. V levém horním rohu stránky by se měl zobrazit název pod názvem repliky vaší aplikace. Počkejte několik minut a aktualizujte stránku, aby se zobrazil jiný název pod, který je zobrazený kvůli vyrovnávání zatížení, které poskytuje cluster AKS.

:::image type="content" source="./media/howto-deploy-java-liberty-app/deploy-succeeded.png" alt-text="Aplikace Java svobody byla úspěšně nasazena v AKS":::

>[!NOTE]
> - V současné době aplikace nepoužívá protokol HTTPS. Doporučuje se povolit protokol [TLS s vlastními certifikáty](ingress-own-tls.md).

## <a name="clean-up-the-resources"></a>Vyčištění prostředků

Abyste se vyhnuli poplatkům za Azure, měli byste vyčistit nepotřebné prostředky.  Pokud už cluster nepotřebujete, odeberte skupinu prostředků, službu kontejneru, registr kontejnerů a všechny související prostředky pomocí příkazu [AZ Group Delete](/cli/azure/group#az_group_delete) .

```azurecli-interactive
az group delete --name $RESOURCE_GROUP_NAME --yes --no-wait
```

## <a name="next-steps"></a>Další kroky

Další informace můžete získat z odkazů používaných v tomto průvodci:

* [Azure Kubernetes Service](https://azure.microsoft.com/free/services/kubernetes-service/)
* [Otevřená svoboda](https://openliberty.io/)
* [Otevřít operátor svoboda](https://github.com/OpenLiberty/open-liberty-operator)
* [Otevřít nastavení serveru svobody](https://openliberty.io/docs/ref/config/)
* [Modul plug-in svobody Maven](https://github.com/OpenLiberty/ci.maven#liberty-maven-plugin)
* [Otevřít soubory z libovolného kontejneru](https://github.com/OpenLiberty/ci.docker)
* [Image kontejnerů WebSphere svobody](https://github.com/WASdev/ci.docker)
