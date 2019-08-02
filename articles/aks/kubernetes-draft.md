---
title: Vývoj ve službě Azure Kubernetes Service (AKS) s využitím konceptu
description: Použití konceptu s AKS a Azure Container Registry
services: container-service
author: zr-msft
ms.service: container-service
ms.topic: article
ms.date: 06/20/2019
ms.author: zarhoads
ms.openlocfilehash: bd099b9d76e17eda36be1650ef5081e5aaa7e53a
ms.sourcegitcommit: 3877b77e7daae26a5b367a5097b19934eb136350
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/30/2019
ms.locfileid: "67303546"
---
# <a name="quickstart-develop-on-azure-kubernetes-service-aks-with-draft"></a>Rychlý start: Vývoj ve službě Azure Kubernetes Service (AKS) s využitím konceptu

Koncept je open source nástroj, který pomáhá balíčkům a spouštěním kontejnerů aplikací v clusteru Kubernetes. S konceptem můžete rychle znovu nasadit aplikaci na Kubernetes, když dojde ke změnám kódu, aniž byste museli potvrzovat změny ve správě verzí. Další informace o konceptu najdete v [dokumentaci konceptu na GitHubu][draft-documentation].

V tomto článku se dozvíte, jak používat koncept balíčku a spustit aplikaci v AKS.


## <a name="prerequisites"></a>Požadavky

* Předplatné Azure. Pokud nemáte předplatné Azure, můžete si vytvořit [bezplatný účet](https://azure.microsoft.com/free).
* [Nainstalované rozhraní Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest)
* Docker je nainstalovaný a nakonfigurovaný. Docker poskytuje balíčky, které konfigurují Docker v systému [Mac][docker-for-mac], [Windows][docker-for-windows]nebo [Linux][docker-for-linux] .
* [Helm je nainstalovaný](https://github.com/helm/helm/blob/master/docs/install.md).
* Byl [nainstalován koncept][draft-documentation].

## <a name="create-an-azure-kubernetes-service-cluster"></a>Vytvoření clusteru služby Azure Kubernetes

Vytvořte cluster AKS. Níže uvedené příkazy vytvoří skupinu prostředků s názvem MyResourceGroup a cluster AKS s názvem MyAKS.

```azurecli
az group create --name MyResourceGroup --location eastus
az aks create -g MyResourceGroup -n MyAKS --location eastus --node-vm-size Standard_DS2_v2 --node-count 1 --generate-ssh-keys
```

## <a name="create-an-azure-container-registry"></a>Vytvoření služby Azure Container Registry
Pokud chcete použít koncept ke spuštění aplikace v clusteru AKS, budete potřebovat Azure Container Registry k ukládání imagí kontejneru. Následující příklad používá [AZ ACR Create][az-acr-create] k vytvoření ACR s názvem *MyDraftACR* ve skupině prostředků *MyResourceGroup* se *základní* SKU. Měli byste zadat vlastní jedinečný název registru. Název registru musí být jedinečný v rámci Azure a musí obsahovat 5 až 50 alfanumerických znaků. Skladová položka *Basic* představuje vstupní bod optimalizovaný z hlediska nákladů pro účely vývoje a poskytuje vyváženou kombinaci úložiště a propustnosti.

```azurecli
az acr create --resource-group MyResourceGroup --name MyDraftACR --sku Basic
```

Výstup se podobá následujícímu příkladu. Poznamenejte si hodnotu *loginServer* pro svůj ACR, protože se použije v pozdějším kroku. V následujícím příkladu je *Mydraftacr.azurecr.IO* *loginServer* pro *mydraftacr*.

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


Pokud chcete, aby se v konceptu použila instance ACR, musíte se nejdřív přihlásit. Přihlaste se pomocí příkazu [AZ ACR Login][az-acr-login] . Následující příklad se přihlásí k ACR s názvem *MyDraftACR*.

```azurecli
az acr login --name MyDraftACR
```

Příkaz po dokončení vrátí zprávu *Login Succeeded* (Přihlášení proběhlo úspěšně).

## <a name="create-trust-between-aks-cluster-and-acr"></a>Vytvoření vztahu důvěryhodnosti mezi clusterem AKS a ACR

Váš cluster AKS také potřebuje přístup k vašemu ACR, aby mohl načíst image kontejneru a spustit je. Povolíte přístup k ACR z AKS vytvořením vztahu důvěryhodnosti. K navázání vztahu důvěryhodnosti mezi clusterem AKS a registrem ACR udělte oprávnění pro objekt služby Azure Active Directory používaný clusterem AKS pro přístup k registru ACR. Následující příkazy udělují oprávnění k instančnímu objektu clusteru *MyAKS* v *MyResourceGroup* pro *MyDraftACR* ACR v *MyResourceGroup*.

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

Pokud chcete `kubectl` nakonfigurovat připojení ke clusteru Kubernetes, použijte příkaz [AZ AKS Get-Credentials][] . Následující příklad vrátí pověření pro cluster AKS s názvem *MyAKS* v *MyResourceGroup*:

```azurecli
az aks get-credentials --resource-group MyResourceGroup --name MyAKS
```

## <a name="create-a-service-account-for-helm"></a>Vytvoření účtu služby pro Helm

Než budete moct nasadit Helm v clusteru AKS s povoleným RBAC, budete potřebovat účet služby a vazbu role pro službu do služby. Další informace o zabezpečení Helm/v případě, že je v clusteru s povoleným RBAC, najdete v tématech předané [, obory názvů a RBAC][tiller-rbac]. Pokud váš cluster AKS není RBAC povolený, přeskočte tento krok.

Vytvořte soubor s názvem `helm-rbac.yaml` a zkopírujte ho do následujícího YAML:

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

Vytvořte účet služby a vazbu role pomocí `kubectl apply` příkazu:

```console
kubectl apply -f helm-rbac.yaml
```

## <a name="configure-helm"></a>Konfigurace Helm
K nasazení základní pokladny do clusteru AKS použijte příkaz [Helm init][helm-init] . Pokud váš cluster není RBAC povolený, odeberte `--service-account` argument a hodnotu.

```console
helm init --service-account tiller --node-selectors "beta.kubernetes.io/os"="linux"
```

## <a name="configure-draft"></a>Konfigurovat koncept

Pokud jste na místním počítači nenakonfigurovali koncept, spusťte `draft init`příkaz:

```console
$ draft init
Installing default plugins...
Installation of default plugins complete
Installing default pack repositories...
...
Happy Sailing!
```

Je také potřeba nakonfigurovat koncept pro použití *loginServer* vašeho ACR. Následující příkaz používá `draft config set` nástroj `mydraftacr.azurecr.io` jako registr.

```console
draft config set registry mydraftacr.azurecr.io
```

Nakonfigurovali jste koncept pro použití vašeho ACR a koncept může do vašeho ACRu vložit image kontejneru. Když koncept spustí vaši aplikaci v clusteru AKS, nebudou se vyžadovat žádná hesla ani tajné klíče, aby bylo možné zapisovat do registru ACR ani je z něj získat. Vzhledem k tomu, že mezi clusterem AKS a vaším ACRem byl vytvořen vztah důvěryhodnosti, ověřování probíhá na úrovni Azure Resource Manager pomocí Azure Active Directory.

## <a name="download-the-sample-application"></a>Stažení ukázkové aplikace

V tomto rychlém startu [se používá ukázková aplikace Java z konceptu úložiště GitHub][example-java]. Naklonujte aplikaci z GitHubu a přejděte `draft/examples/example-java/` do adresáře.

```console
git clone https://github.com/Azure/draft
cd draft/examples/example-java/
```

## <a name="run-the-sample-application-with-draft"></a>Spustit ukázkovou aplikaci s využitím konceptu

K přípravě aplikace použijte příkaz.`draft create`

```console
draft create
```

Tento příkaz vytvoří artefakty, které se používají ke spuštění aplikace v clusteru Kubernetes. Mezi tyto položky patří souboru Dockerfile, graf Helm a soubor *koncept. toml* , což je koncept konfiguračního souboru.

```console
$ draft create

--> Draft detected Java (92.205567%)
--> Ready to sail
```

Pokud chcete spustit ukázkovou aplikaci v clusteru AKS, použijte `draft up` příkaz.

```console
draft up
```

Tento příkaz sestaví souboru Dockerfile pro vytvoření image kontejneru, nahraje image do vaší ACR a nainstaluje graf Helm pro spuštění aplikace v AKS. Při prvním spuštění tohoto příkazu může trvat nějakou dobu vkládání a navýšení image kontejneru. Po uložení základních vrstev do mezipaměti se výrazně sníží doba potřebná k nasazení aplikace.

```
$ draft up

Draft Up Started: 'example-java': 01CMZAR1F4T1TJZ8SWJQ70HCNH
example-java: Building Docker Image: SUCCESS ⚓  (73.0720s)
example-java: Pushing Docker Image: SUCCESS ⚓  (19.5727s)
example-java: Releasing Application: SUCCESS ⚓  (4.6979s)
Inspect the logs with `draft logs 01CMZAR1F4T1TJZ8SWJQ70HCNH`
```

## <a name="connect-to-the-running-sample-application-from-your-local-machine"></a>Připojte se ke spuštěné ukázkové aplikaci z místního počítače.

K otestování aplikace použijte `draft connect` příkaz.

```console
draft connect
```

Tento příkaz proxy vytvoří zabezpečené připojení k Kubernetes pod. Po dokončení se k aplikaci dá přistup na zadané adrese URL.

```console
$ draft connect

Connect to java:4567 on localhost:49804
[java]: SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
[java]: SLF4J: Defaulting to no-operation (NOP) logger implementation
[java]: SLF4J: See https://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
[java]: == Spark has ignited ...
[java]: >> Listening on 0.0.0.0:4567
```

Přejděte do aplikace v prohlížeči pomocí `localhost` adresy URL, abyste viděli ukázkovou aplikaci. V předchozím příkladu je `http://localhost:49804`adresa URL. Zastavte připojení `Ctrl+c`pomocí.

## <a name="access-the-application-on-the-internet"></a>Přístup k aplikaci na internetu

Předchozí krok vytvořil proxy připojení k aplikaci pod clusterem AKS. Při vývoji a testování aplikace může být vhodné zpřístupnit aplikaci na internetu. K vystavení aplikace na internetu můžete vytvořit službu Kubernetes s typem nástroje pro vyrovnávání [zatížení][kubernetes-service-loadbalancer].

Pomocí `charts/example-java/values.yaml` aktualizace vytvořte službu *Vyrovnávání zatížení* . Změňte hodnotu *Service. Type* z *ClusterIP* na nástroj pro *Vyrovnávání zatížení*.

```yaml
...
service:
  name: java
  type: LoadBalancer
  externalPort: 80
  internalPort: 4567
...
```

Uložte změny, zavřete soubor a spusťte `draft up` aplikaci znovu.

```console
draft up
```

Vrácení veřejné IP adresy službou může trvat několik minut. Chcete-li monitorovat průběh, `kubectl get service` použijte příkaz s parametrem *Watch* :

```console
$ kubectl get service --watch

NAME                TYPE          CLUSTER-IP    EXTERNAL-IP   PORT(S)        AGE
example-java-java   LoadBalancer  10.0.141.72   <pending>     80:32150/TCP   2m
...
example-java-java   LoadBalancer   10.0.141.72   52.175.224.118  80:32150/TCP   7m
```

Přejděte do nástroje pro vyrovnávání zatížení vaší aplikace v prohlížeči pomocí *externí IP adresy* , abyste viděli ukázkovou aplikaci. V předchozím příkladu je `52.175.224.118`IP adresa.

## <a name="iterate-on-the-application"></a>Iterovat na aplikaci

Můžete provést iteraci aplikace provedením změn místně a znovu spustit `draft up`.

Aktualizuje zprávu vrácenou na [řádku 7 src/Main/Java/HelloWorld/Hello. Java][example-java-hello-l7]

```java
    public static void main(String[] args) {
        get("/", (req, res) -> "Hello World, I'm Java in AKS!");
    }
```

`draft up` Spusťte příkaz pro opětovné nasazení aplikace:

```console
$ draft up

Draft Up Started: 'example-java': 01CMZC9RF0TZT7XPWGFCJE15X4
example-java: Building Docker Image: SUCCESS ⚓  (25.0202s)
example-java: Pushing Docker Image: SUCCESS ⚓  (7.1457s)
example-java: Releasing Application: SUCCESS ⚓  (3.5773s)
Inspect the logs with `draft logs 01CMZC9RF0TZT7XPWGFCJE15X4`
```

Pokud chcete zobrazit aktualizovanou aplikaci, přejděte znovu na IP adresu vašeho nástroje pro vyrovnávání zatížení a ověřte, zda se zobrazí vaše změny.

## <a name="delete-the-cluster"></a>Odstranění clusteru

Pokud už cluster nepotřebujete, odeberte pomocí příkazu [AZ Group Delete][az-group-delete] skupinu prostředků, cluster AKS, registr kontejnerů, uložené image kontejnerů a všechny související prostředky.

```azurecli-interactive
az group delete --name MyResourceGroup --yes --no-wait
```

> [!NOTE]
> Při odstranění clusteru se neodebere instanční objekt služby Azure Active Directory používaný clusterem AKS. Postup odebrání instančního objektu najdete v tématu [AKS a informace o instančním objektu a jejich odstranění][sp-delete].

## <a name="next-steps"></a>Další postup

Další informace o používání konceptu najdete v dokumentaci konceptu na GitHubu.

> [!div class="nextstepaction"]
> [Koncept dokumentace][draft-documentation]


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
