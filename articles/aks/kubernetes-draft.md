---
title: Vývoj pro služby Azure Kubernetes Service (AKS) pomocí nástroje Draft
description: Použití konceptu s AKS a Azure Container Registry
services: container-service
author: zr-msft
ms.service: container-service
ms.topic: article
ms.date: 06/20/2019
ms.author: zarhoads
ms.openlocfilehash: bd099b9d76e17eda36be1650ef5081e5aaa7e53a
ms.sourcegitcommit: 82efacfaffbb051ab6dc73d9fe78c74f96f549c2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/20/2019
ms.locfileid: "67303546"
---
# <a name="quickstart-develop-on-azure-kubernetes-service-aks-with-draft"></a>Rychlý start: Vývoj pro služby Azure Kubernetes Service (AKS) pomocí nástroje Draft

Koncept je open source nástroj, který pomáhá balíčku a spouštění aplikací kontejnerů v clusteru Kubernetes. Pomocí nástroje Draft můžete rychle znovu nasadit aplikaci do Kubernetes jako dojde ke změnám kódu bez nutnosti potvrzovat změny do správy verzí. Další informace o návrhu, najdete v článku [koncept dokumentaci na Githubu][draft-documentation].

Tento článek ukazuje, jak používat balíček nástroje Draft a spuštění aplikace v AKS.


## <a name="prerequisites"></a>Požadavky

* Předplatné Azure. Pokud nemáte předplatné Azure, můžete si vytvořit [bezplatný účet](https://azure.microsoft.com/free).
* [Nainstalované rozhraní Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest)
* Docker nainstalovaný a nakonfigurovaný. Docker nabízí balíčky pro konfiguraci Dockeru na [Mac][docker-for-mac], [Windows][docker-for-windows], nebo [Linux][dockeru pro linux] systému.
* [Nainstalovat Helm](https://github.com/helm/helm/blob/master/docs/install.md).
* [Draft nainstalovat][draft-documentation].

## <a name="create-an-azure-kubernetes-service-cluster"></a>Vytvoření clusteru Azure Kubernetes Service

Vytvoření clusteru AKS. Následujících příkazů vytvořte skupinu prostředků s názvem MyResourceGroup a názvem MyAKS clusteru AKS.

```azurecli
az group create --name MyResourceGroup --location eastus
az aks create -g MyResourceGroup -n MyAKS --location eastus --node-vm-size Standard_DS2_v2 --node-count 1 --generate-ssh-keys
```

## <a name="create-an-azure-container-registry"></a>Vytvoření služby Azure Container Registry
K použití nástroje Draft ke spuštění aplikace ve vašem clusteru AKS, potřebujete službu Azure Container Registry k uložení imagí kontejnerů. Následujícím příkladu používá [az acr vytvořit][az-acr-create] k vytvoření ACR s názvem *MyDraftACR* v *MyResourceGroup* skupina prostředků se *základní* SKLADOVÁ POLOŽKA. Vlastní název jedinečný registru byste měli poskytnout. Název registru musí být jedinečný v rámci Azure a musí obsahovat 5 až 50 alfanumerických znaků. Skladová položka *Basic* představuje vstupní bod optimalizovaný z hlediska nákladů pro účely vývoje a poskytuje vyváženou kombinaci úložiště a propustnosti.

```azurecli
az acr create --resource-group MyResourceGroup --name MyDraftACR --sku Basic
```

Výstup se podobá následujícímu příkladu. Poznamenejte si, *loginServer* hodnota pro službu ACR vzhledem k tomu, že se použije v pozdější fázi. V následujícím příkladu *mydraftacr.azurecr.io* je *loginServer* pro *MyDraftACR*.

```console
{
  "adminUserEnabled": false,
  "creationDate": "2019-06-11T13:35:17.998425+00:00",
  "id": "/subscriptions/<ID>/resourceGroups/MyResourceGroup/providers/Microsoft.ContainerRegistry/registries/MyDraftACR",
  "location": "eastus",
  "loginServer": "mydraftacr.azurecr.io",
  "name": "MyDraftACR",
  "networkRuleSet": null,
  "provisioningState": "Succeeded",
  "resourceGroup": "MyResourceGroup",
  "sku": {
    "name": "Basic",
    "tier": "Basic"
  },
  "status": null,
  "storageAccount": null,
  "tags": {},
  "type": "Microsoft.ContainerRegistry/registries"
}
```


Pro návrh použít instanci služby ACR musíte nejdřív přihlásit. Použití [az acr login][az-acr-login] příkaz pro přihlášení. Následujícím příkladu se přihlaste do ACR s názvem *MyDraftACR*.

```azurecli
az acr login --name MyDraftACR
```

Příkaz po dokončení vrátí zprávu *Login Succeeded* (Přihlášení proběhlo úspěšně).

## <a name="create-trust-between-aks-cluster-and-acr"></a>Vytvoření vztahu důvěryhodnosti mezi clusterem AKS a ACR

AKS cluster také potřebuje přístup k záznamu ACR načítat Image kontejneru a jejich spuštění. Můžete povolit přístup k ACR z AKS tím, že vztah důvěryhodnosti. K navázání vztahu důvěryhodnosti mezi clusterem AKS a registr ACR, udělení oprávnění objektu služby Azure Active Directory používané clusterem AKS pro přístup k registru ACR. Následující příkazy udělení oprávnění pro instanční objekt *MyAKS* cluster v *MyResourceGroup* k *MyDraftACR* ACR v  *MyResourceGroup*.

```azurecli
# Get the service principal ID of your AKS cluster
AKS_SP_ID=$(az aks show --resource-group MyResourceGroup --name MyAKS --query "servicePrincipalProfile.clientId" -o tsv)

# Get the resource ID of your ACR instance
ACR_RESOURCE_ID=$(az acr show --resource-group MyResourceGroup --name MyDraftACR --query "id" -o tsv)

# Create a role assignment for your AKS cluster to access the ACR instance
az role assignment create --assignee $AKS_SP_ID --scope $ACR_RESOURCE_ID --role contributor
```

## <a name="connect-to-your-aks-cluster"></a>Připojení k vašemu clusteru AKS

Pro připojení ke clusteru Kubernetes z místního počítače, můžete použít [kubectl][kubectl], klienta příkazového řádku Kubernetes.

Pokud používáte Azure Cloud Shell, `kubectl` je už nainstalovaný. Můžete ho také nainstalovat místně, a to pomocí příkazu [az aks install-cli][]:

```azurecli
az aks install-cli
```

Ke konfiguraci `kubectl` pro připojení k vašemu clusteru Kubernetes, použijte [az aks get-credentials][] příkazu. Následující příklad získá přihlašovací údaje pro cluster AKS, s názvem *MyAKS* v *MyResourceGroup*:

```azurecli
az aks get-credentials --resource-group MyResourceGroup --name MyAKS
```

## <a name="create-a-service-account-for-helm"></a>Vytvoření účtu služby pro Helm

Před nasazením Helm v clusteru AKS povolené RBAC, potřebujete účet služby a role vazby pro službu Tiller. Další informace o zabezpečení Helm / Tiller v RBAC povolena clusteru, naleznete v tématu [Tiller, obory názvů a RBAC][tiller-rbac]. Pokud váš cluster AKS není povoleno RBAC, tento krok přeskočte.

Vytvořte soubor s názvem `helm-rbac.yaml` a zkopírujte do následující kód YAML:

```yaml
apiVersion: v1
kind: ServiceAccount
metadata:
  name: tiller
  namespace: kube-system
---
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  name: tiller
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: cluster-admin
subjects:
  - kind: ServiceAccount
    name: tiller
    namespace: kube-system
```

Vytvořit účet služby a role vazba s `kubectl apply` příkaz:

```console
kubectl apply -f helm-rbac.yaml
```

## <a name="configure-helm"></a>Konfigurace Helm
Chcete-li nasadit základní Tiller do clusteru AKS, použijte [příkaz helm init][helm-init] příkazu. Pokud váš cluster není povoleno RBAC, odeberte `--service-account` argumentu a hodnotu.

```console
helm init --service-account tiller --node-selectors "beta.kubernetes.io/os"="linux"
```

## <a name="configure-draft"></a>Konfigurace nástroje Draft

Pokud jste nenakonfigurovali koncept na místním počítači, spusťte `draft init`:

```console
$ draft init
Installing default plugins...
Installation of default plugins complete
Installing default pack repositories...
...
Happy Sailing!
```

Budete také muset nakonfigurujte Draft pro použití *loginServer* z vaší služby ACR. Následující příkaz používá `draft config set` používat `mydraftacr.azurecr.io` jako registr.

```console
draft config set registry mydraftacr.azurecr.io
```

Nakonfigurujete Draft pro použití vaší služby ACR a koncept nasdílet Image kontejneru do vaší služby ACR. Při návrhu spouští vaši aplikaci ve vašem clusteru AKS, žádná hesla nebo tajné kódy jsou potřebné pro vložení do nebo o přijetí změn z registru ACR. Od vytvoření vztahu důvěryhodnosti mezi clusteru AKS a záznamu ACR, ověření se odehrává na úrovni Azure Resource Manageru pomocí služby Azure Active Directory.

## <a name="download-the-sample-application"></a>Stažení ukázkové aplikace

Tento rychlý start využívá [ukázková aplikace java z úložiště GitHub koncept][example-java]. Klonování aplikace z Githubu a přejděte `draft/examples/example-java/` adresáře.

```console
git clone https://github.com/Azure/draft
cd draft/examples/example-java/
```

## <a name="run-the-sample-application-with-draft"></a>Spuštění ukázkové aplikace s využitím Draftu

Použití `draft create` příkazu připravte aplikaci.

```console
draft create
```

Tento příkaz vytvoří artefakty, které se používají ke spuštění aplikace v clusteru Kubernetes. Tyto položky zahrnují souboru Dockerfile a Digram helmu a *draft.toml* soubor, který je konfigurační soubor nástroje Draft.

```console
$ draft create

--> Draft detected Java (92.205567%)
--> Ready to sail
```

Chcete-li spustit ukázkovou aplikaci v clusteru AKS, použijte `draft up` příkazu.

```console
draft up
```

Tento příkaz vytvoří soubor Dockerfile k vytvoření image kontejneru, ji vloží do vaší služby ACR a nainstaluje grafu helmu a spusťte tak aplikaci ve službě AKS. Při prvním spuštění tohoto příkazu, odesílání a stahování image kontejneru může chvíli trvat. Jakmile základní vrstvy jsou uložené v mezipaměti, čas potřebný k nasazení aplikace došlo k podstatnému omezení.

```
$ draft up

Draft Up Started: 'example-java': 01CMZAR1F4T1TJZ8SWJQ70HCNH
example-java: Building Docker Image: SUCCESS ⚓  (73.0720s)
example-java: Pushing Docker Image: SUCCESS ⚓  (19.5727s)
example-java: Releasing Application: SUCCESS ⚓  (4.6979s)
Inspect the logs with `draft logs 01CMZAR1F4T1TJZ8SWJQ70HCNH`
```

## <a name="connect-to-the-running-sample-application-from-your-local-machine"></a>Připojení ke spuštění ukázkové aplikace z místního počítače

Chcete-li aplikaci otestovat, použijte `draft connect` příkazu.

```console
draft connect
```

Tento příkaz proxy zabezpečené připojení k podu Kubernetes. Jakmile budete hotovi, aplikace je přístupný na zadané adrese URL.

```console
$ draft connect

Connect to java:4567 on localhost:49804
[java]: SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
[java]: SLF4J: Defaulting to no-operation (NOP) logger implementation
[java]: SLF4J: See https://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
[java]: == Spark has ignited ...
[java]: >> Listening on 0.0.0.0:4567
```

Přejděte k aplikaci v prohlížeči pomocí `localhost` adresy url ukázkovou aplikaci. V příkladu výše je adresa url `http://localhost:49804`. Zastavit připojení pomocí `Ctrl+c`.

## <a name="access-the-application-on-the-internet"></a>Přístup k aplikaci na Internetu

V předchozím kroku vytvořili připojení proxy k podu aplikace ve vašem clusteru AKS. Jak vyvíjet a testovat aplikace, můžete zpřístupnit aplikace na Internetu. Zveřejnit aplikaci na Internetu, můžete vytvořit služby Kubernetes s typem [nástroj pro vyrovnávání zatížení][kubernetes-service-loadbalancer].

Aktualizace `charts/example-java/values.yaml` k vytvoření *nástroj pro vyrovnávání zatížení* služby. Změňte hodnotu vlastnosti *service.type* z *ClusterIP* k *nástroj pro vyrovnávání zatížení*.

```yaml
...
service:
  name: java
  type: LoadBalancer
  externalPort: 80
  internalPort: 4567
...
```

Uložte provedené změny a zavřete soubor, spustit `draft up` znovu spusťte aplikaci.

```console
draft up
```

Trvá několik minut, než služba vrátí veřejnou IP adresu. Chcete-li sledovat průběh, použijte `kubectl get service` příkazů *watch* parametr:

```console
$ kubectl get service --watch

NAME                TYPE          CLUSTER-IP    EXTERNAL-IP   PORT(S)        AGE
example-java-java   LoadBalancer  10.0.141.72   <pending>     80:32150/TCP   2m
...
example-java-java   LoadBalancer   10.0.141.72   52.175.224.118  80:32150/TCP   7m
```

Přejděte na nástroje pro vyrovnávání zatížení aplikace v prohlížeči pomocí *EXTERNAL-IP* zobrazíte ukázkovou aplikaci. V příkladu výše, kde se IP adresa `52.175.224.118`.

## <a name="iterate-on-the-application"></a>Iterovat aplikace

Můžete iterovat aplikace tím, že změny místně a opětovné spuštění `draft up`.

Aktualizovat zpráva vráceno na [řádek 7 src/main/java/helloworld/Hello.java][example-java-hello-l7]

```java
    public static void main(String[] args) {
        get("/", (req, res) -> "Hello World, I'm Java in AKS!");
    }
```

Spustit `draft up` příkaz znovu nasadit aplikaci:

```console
$ draft up

Draft Up Started: 'example-java': 01CMZC9RF0TZT7XPWGFCJE15X4
example-java: Building Docker Image: SUCCESS ⚓  (25.0202s)
example-java: Pushing Docker Image: SUCCESS ⚓  (7.1457s)
example-java: Releasing Application: SUCCESS ⚓  (3.5773s)
Inspect the logs with `draft logs 01CMZC9RF0TZT7XPWGFCJE15X4`
```

Pokud chcete zobrazit aktualizovanou aplikaci, znovu přejděte na IP adresu vašeho nástroje pro vyrovnávání zatížení a ověřte, že se změny zobrazují.

## <a name="delete-the-cluster"></a>Odstranění clusteru

Pokud už cluster nepotřebujete, použijte [odstranění skupiny az][az-group-delete] příkazu došlo k odebrání skupiny prostředků, clusteru AKS, registr kontejneru, Image kontejneru uložena, a všechny související prostředky.

```azurecli-interactive
az group delete --name MyResourceGroup --yes --no-wait
```

> [!NOTE]
> Při odstranění clusteru se neodebere instanční objekt služby Azure Active Directory používaný clusterem AKS. Pokyny o tom, jak odebrat instanční objekt služby, najdete v článku [hlavní aspekty a odstranění služby AKS][sp-delete].

## <a name="next-steps"></a>Další postup

Další informace o použití nástroje Draft najdete v dokumentaci nástroje Draft na Githubu.

> [!div class="nextstepaction"]
> [Dokumentace k návrhu][draft-documentation]


[az-acr-login]: /cli/azure/acr#az-acr-login
[az-acr-create]: /cli/azure/acr#az-acr-login
[az-group-delete]: /cli/azure/group#az-group-delete
[az aks get-credentials]: /cli/azure/aks#az-aks-get-credentials
[az aks install-cli]: /cli/azure/aks#az-aks-install-cli
[kubernetes-ingress]: ./ingress-basic.md

[docker-for-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-for-mac]: https://docs.docker.com/docker-for-mac/
[docker-for-windows]: https://docs.docker.com/docker-for-windows/
[draft-documentation]: https://github.com/Azure/draft/tree/master/docs
[example-java]: https://github.com/Azure/draft/tree/master/examples/example-java
[example-java-hello-l7]: https://github.com/Azure/draft/blob/master/examples/example-java/src/main/java/helloworld/Hello.java#L7
[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[kubernetes-service-loadbalancer]: https://kubernetes.io/docs/concepts/services-networking/service/#type-loadbalancer
[helm-init]: https://docs.helm.sh/helm/#helm-init
[sp-delete]: kubernetes-service-principal.md#additional-considerations
[tiller-rbac]: https://docs.helm.sh/using_helm/#tiller-namespaces-and-rbac
