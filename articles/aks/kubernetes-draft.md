---
title: Vývoj ve službě Azure Kubernetes Service (AKS) s konceptem
description: Pomocí konceptu s Registrem kontejnerů AKS a Azure můžete balit a spouštět kontejnery aplikací v clusteru.
services: container-service
author: zr-msft
ms.topic: article
ms.date: 06/20/2019
ms.author: zarhoads
ms.openlocfilehash: 2d69676121fcb26f7d2f796e6de42435447084cd
ms.sourcegitcommit: d6e4eebf663df8adf8efe07deabdc3586616d1e4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/15/2020
ms.locfileid: "81392752"
---
# <a name="quickstart-develop-on-azure-kubernetes-service-aks-with-draft"></a>Úvodní příručka: Vývoj ve službě Azure Kubernetes Service (AKS) s konceptem

Koncept je open source nástroj, který pomáhá balíček a spouštění kontejnerů aplikací v clusteru Kubernetes. S Koncept, můžete rychle znovu nasadit aplikaci Kubernetes jako změny kódu dojít bez nutnosti potvrdit změny správy verzí. Další informace o konceptu najdete v [dokumentaci konceptu na GitHubu][draft-documentation].

Tento článek ukazuje, jak použít koncept k balení a spuštění aplikace na AKS.


## <a name="prerequisites"></a>Požadavky

* Předplatné Azure. Pokud nemáte předplatné Azure, můžete si vytvořit [bezplatný účet](https://azure.microsoft.com/free).
* [Nainstalované rozhraní Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest)
* Docker je nainstalovaný a nakonfigurovaný. Docker nabízí balíčky pro konfiguraci Dockeru v systému [Mac][docker-for-mac], [Windows][docker-for-windows] nebo [Linux][docker-for-linux].
* [Helm v2 nainstalován][helm-install].
* [Koncept nainstalován][draft-documentation].

## <a name="create-an-azure-kubernetes-service-cluster"></a>Vytvoření clusteru služby Azure Kubernetes

Vytvořte cluster AKS. Níže uvedené příkazy vytvoří skupinu prostředků nazvanou MyResourceGroup a cluster AKS s názvem MyAKS.

```azurecli
az group create --name MyResourceGroup --location eastus
az aks create -g MyResourceGroup -n MyAKS --location eastus --node-vm-size Standard_DS2_v2 --node-count 1 --generate-ssh-keys
```

## <a name="create-an-azure-container-registry"></a>Vytvoření služby Azure Container Registry
Chcete-li použít koncept ke spuštění aplikace v clusteru AKS, budete potřebovat Azure Container Registry pro ukládání ibi kontejnerů. Níže uvedený příklad používá [az acr create][az-acr-create] k vytvoření acr s názvem *MyDraftACR* ve skupině prostředků *MyResourceGroup* se *základní* skladovou položkou. Měli byste zadat svůj vlastní jedinečný název registru. Název registru musí být jedinečný v rámci Azure a musí obsahovat 5 až 50 alfanumerických znaků. Skladová položka *Basic* představuje vstupní bod optimalizovaný z hlediska nákladů pro účely vývoje a poskytuje vyváženou kombinaci úložiště a propustnosti.

```azurecli
az acr create --resource-group MyResourceGroup --name MyDraftACR --sku Basic
```

Výstup se podobá následujícímu příkladu. Poznamenejte si hodnotu *loginServer* pro acr, protože bude použita v pozdějším kroku. V níže uvedeném příkladu je *mydraftacr.azurecr.io* *loginServer* pro *MyDraftACR*.

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


Aby koncept mohl použít instanci ACR, musíte se nejprve přihlásit. Pro přihlášení použijte příkaz [az acr login.][az-acr-login] Níže uvedený příklad se přihlásí k acr s názvem *MyDraftACR*.

```azurecli
az acr login --name MyDraftACR
```

Příkaz vrátí *zprávu Úspěšné přihlášení* po dokončení.

## <a name="create-trust-between-aks-cluster-and-acr"></a>Vytvoření vztahu důvěryhodnosti mezi clusterem AKS a acr

Cluster AKS také potřebuje přístup k acr k vytahování inicií kontejneru a jejich spuštění. Vytvořením vztahu důvěryhodnosti povolíte přístup k ACR z AKS. Chcete-li vytvořit vztah důvěryhodnosti mezi clusterem AKS a registrem ACR, udělte oprávnění pro objekt zabezpečení služby Azure Active Directory, který cluster AKS používá pro přístup k registru ACR. Následující příkazy udělují oprávnění instančnímu objektu clusteru *MyAKS* v *myresourcegroup* službě *MyDraftACR* ACR v *myresourcegroup*.

```azurecli
# Get the service principal ID of your AKS cluster
AKS_SP_ID=$(az aks show --resource-group MyResourceGroup --name MyAKS --query "servicePrincipalProfile.clientId" -o tsv)

# Get the resource ID of your ACR instance
ACR_RESOURCE_ID=$(az acr show --resource-group MyResourceGroup --name MyDraftACR --query "id" -o tsv)

# Create a role assignment for your AKS cluster to access the ACR instance
az role assignment create --assignee $AKS_SP_ID --scope $ACR_RESOURCE_ID --role contributor
```

## <a name="connect-to-your-aks-cluster"></a>Připojení ke clusteru AKS

Pokud se chcete připojit ke clusteru Kubernetes z místního počítače, použijte klienta příkazového řádku Kubernetes [kubectl][kubectl].

Pokud používáte Azure Cloud Shell, `kubectl` je už nainstalovaný. Můžete ho také nainstalovat místně, a to pomocí příkazu [az aks install-cli][]:

```azurecli
az aks install-cli
```

Pomocí příkazu [az aks get-credentials][] nakonfigurujte klienta `kubectl` pro připojení k vašemu clusteru Kubernetes. Následující příklad získá pověření pro cluster AKS s názvem *MyAKS* v *MyResourceGroup*:

```azurecli
az aks get-credentials --resource-group MyResourceGroup --name MyAKS
```

## <a name="create-a-service-account-for-helm"></a>Vytvoření účtu služby pro helmu

Před nasazením helmu v clusteru AKS s podporou RBAC potřebujete účet služby a vazbu role pro službu Tiller. Další informace o zabezpečení helmu / kultivátoru v clusteru s podporou RBAC naleznete v [tématech Tiller, Namespaces a RBAC][tiller-rbac]. Pokud váš cluster AKS není povolen rbac, tento krok přeskočte.

Vytvořte soubor `helm-rbac.yaml` s názvem a zkopírujte v následujícím yaml:

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

Vytvořte účet služby a `kubectl apply` vazbu role pomocí příkazu:

```console
kubectl apply -f helm-rbac.yaml
```

## <a name="configure-helm"></a>Konfigurace kormidla
Chcete-li nasadit základní kultivátor do clusteru AKS, použijte příkaz [helm init.][helm-init] Pokud váš cluster není povolen RBAC, odeberte `--service-account` argument a hodnotu.

```console
helm init --service-account tiller --node-selectors "beta.kubernetes.io/os"="linux"
```

## <a name="configure-draft"></a>Konfigurace konceptu

Pokud jste v místním počítači nenakonfigurovali koncept, spusťte `draft init`:

```console
$ draft init
Installing default plugins...
Installation of default plugins complete
Installing default pack repositories...
...
Happy Sailing!
```

Je také nutné nakonfigurovat koncept pro použití *loginServer* vašeho ACR. Následující příkaz `draft config set` slouží `mydraftacr.azurecr.io` k použití jako registr.

```console
draft config set registry mydraftacr.azurecr.io
```

Nakonfigurovali jste koncept tak, aby používal acr, a koncept může do acr vysouvat ibližové obrazy. Při konceptu spustí aplikaci v clusteru AKS, žádná hesla nebo tajné klíče jsou nutné tlačit do nebo vyžádat z registru ACR. Vzhledem k tomu, že byl vytvořen vztah důvěryhodnosti mezi vaším clusterem AKS a acr, ověřování probíhá na úrovni Azure Resource Manager pomocí Služby Azure Active Directory.

## <a name="download-the-sample-application"></a>Stažení ukázkové aplikace

Tento rychlý start používá [ukázkovou aplikaci Java z úložiště Draft GitHub][example-java]. Klonujte aplikaci z GitHubu `draft/examples/example-java/` a přejděte do adresáře.

```console
git clone https://github.com/Azure/draft
cd draft/examples/example-java/
```

## <a name="run-the-sample-application-with-draft"></a>Spuštění ukázkové aplikace s konceptem

K `draft create` přípravě aplikace použijte příkaz.

```console
draft create
```

Tento příkaz vytvoří artefakty, které se používají ke spuštění aplikace v clusteru Kubernetes. Mezi tyto položky patří Dockerfile, graf Helm a soubor *draft.toml,* což je konfigurační soubor Koncept.

```console
$ draft create

--> Draft detected Java (92.205567%)
--> Ready to sail
```

Chcete-li spustit ukázkovou aplikaci v `draft up` clusteru AKS, použijte příkaz.

```console
draft up
```

Tento příkaz vytvoří Dockerfile vytvořit image kontejneru, odešle image do ACR a nainstaluje graf Helm spustit aplikaci v AKS. Při prvním spuštění tohoto příkazu může stisknutí a vytažení bitové kopie kontejneru nějakou dobu trvat. Jakmile jsou základní vrstvy uloženy do mezipaměti, čas nanasazení aplikace se výrazně zkrátí.

```
$ draft up

Draft Up Started: 'example-java': 01CMZAR1F4T1TJZ8SWJQ70HCNH
example-java: Building Docker Image: SUCCESS ⚓  (73.0720s)
example-java: Pushing Docker Image: SUCCESS ⚓  (19.5727s)
example-java: Releasing Application: SUCCESS ⚓  (4.6979s)
Inspect the logs with `draft logs 01CMZAR1F4T1TJZ8SWJQ70HCNH`
```

## <a name="connect-to-the-running-sample-application-from-your-local-machine"></a>Připojení k spuštěné ukázkové aplikaci z místního počítače

Chcete-li aplikaci `draft connect` otestovat, použijte příkaz.

```console
draft connect
```

Tento příkaz proxy zabezpečené připojení k pod Kubernetes. Po dokončení je aplikace přístupná na zadanou adresu URL.

```console
$ draft connect

Connect to java:4567 on localhost:49804
[java]: SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
[java]: SLF4J: Defaulting to no-operation (NOP) logger implementation
[java]: SLF4J: See https://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
[java]: == Spark has ignited ...
[java]: >> Listening on 0.0.0.0:4567
```

Přejděte do aplikace v `localhost` prohlížeči pomocí adresy URL, abyste viděli ukázkovou aplikaci. Ve výše uvedeném příkladu je `http://localhost:49804`adresa URL . Zastavte `Ctrl+c`připojení pomocí aplikace .

## <a name="access-the-application-on-the-internet"></a>Přístup k aplikaci na internetu

Předchozí krok vytvořil proxy připojení k podu aplikace v clusteru AKS. Při vývoji a testování aplikace můžete aplikaci zpřístupnit na internetu. Chcete-li vystavit aplikaci na internetu, můžete vytvořit službu Kubernetes s typem [LoadBalancer][kubernetes-service-loadbalancer].

Aktualizujte `charts/example-java/values.yaml` a vytvořte službu *LoadBalancer.* Změňte hodnotu *service.type* z *ClusterIP* na *LoadBalancer*.

```yaml
...
service:
  name: java
  type: LoadBalancer
  externalPort: 80
  internalPort: 4567
...
```

Uložte změny, zavřete soubor `draft up` a spusťte aplikaci znovu.

```console
draft up
```

Trvá několik minut, než služba vrátí veřejnou IP adresu. Chcete-li sledovat průběh, použijte `kubectl get service` příkaz s parametrem *watch:*

```console
$ kubectl get service --watch

NAME                TYPE          CLUSTER-IP    EXTERNAL-IP   PORT(S)        AGE
example-java-java   LoadBalancer  10.0.141.72   <pending>     80:32150/TCP   2m
...
example-java-java   LoadBalancer   10.0.141.72   52.175.224.118  80:32150/TCP   7m
```

Přejděte do zařízení pro vyrovnávání zatížení aplikace v prohlížeči pomocí *externí IP* zobrazit ukázkové aplikace. Ve výše uvedeném příkladu je IP . `52.175.224.118`

## <a name="iterate-on-the-application"></a>Iterát na aplikaci

Aplikaci můžete itrezovat provedením místních `draft up`změn a opětovným spuštěním aplikace .

Aktualizovat zprávu vrácenou na [řádku 7 src/main/java/helloworld/Hello.java][example-java-hello-l7]

```java
    public static void main(String[] args) {
        get("/", (req, res) -> "Hello World, I'm Java in AKS!");
    }
```

Spusťte příkaz pro `draft up` opětovné nasazení aplikace:

```console
$ draft up

Draft Up Started: 'example-java': 01CMZC9RF0TZT7XPWGFCJE15X4
example-java: Building Docker Image: SUCCESS ⚓  (25.0202s)
example-java: Pushing Docker Image: SUCCESS ⚓  (7.1457s)
example-java: Releasing Application: SUCCESS ⚓  (3.5773s)
Inspect the logs with `draft logs 01CMZC9RF0TZT7XPWGFCJE15X4`
```

Chcete-li zobrazit aktualizovanou aplikaci, přejděte znovu na adresu IP nástrojů pro vyrovnávání zatížení a ověřte, zda se zobrazí změny.

## <a name="delete-the-cluster"></a>Odstranění clusteru

Pokud cluster již není potřeba, použijte příkaz [odstranění skupiny az][az-group-delete] k odebrání skupiny prostředků, clusteru AKS, registru kontejnerů, bitových kopií kontejnerů, které jsou v něm uloženy, a všech souvisejících prostředků.

```azurecli-interactive
az group delete --name MyResourceGroup --yes --no-wait
```

> [!NOTE]
> Při odstranění clusteru se neodebere instanční objekt služby Azure Active Directory používaný clusterem AKS. Postup odebrání instančního objektu najdete v tématu věnovaném [aspektům instančního objektu AKS a jeho odstranění][sp-delete]. Pokud jste použili spravovanou identitu, identita je spravována platformou a nevyžaduje odebrání.

## <a name="next-steps"></a>Další kroky

Další informace o používání konceptu najdete v dokumentaci konceptu na GitHubu.

> [!div class="nextstepaction"]
> [Návrh dokumentace][draft-documentation]


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
[helm-init]: https://v2.helm.sh/docs/helm/#helm-init
[helm-install]: https://v2.helm.sh/docs/using_helm/#installing-helm
[sp-delete]: kubernetes-service-principal.md#additional-considerations
[tiller-rbac]: https://v2.helm.sh/docs/using_helm/#tiller-namespaces-and-rbac
