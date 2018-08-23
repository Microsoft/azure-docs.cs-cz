---
title: Použití konceptu s AKS a Azure Container Registry
description: Použití konceptu s AKS a Azure Container Registry
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: article
ms.date: 08/15/2018
ms.author: iainfou
ms.openlocfilehash: a64ada61b2edd0a5c5d2314125b7e2a23444a398
ms.sourcegitcommit: 744747d828e1ab937b0d6df358127fcf6965f8c8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/16/2018
ms.locfileid: "42055440"
---
# <a name="use-draft-with-azure-kubernetes-service-aks"></a>Použití konceptu s Azure Kubernetes Service (AKS)

Koncept je open source nástroj, který pomáhá balíčků a nasazení kontejnerů aplikací v clusteru Kubernetes, poskytne vám můžete soustředit na vývoj cyklus – "vnitřní smyčka" vměstnány vývoje. Draft funguje jako kód je vyvíjena, ale před potvrzením do správy verzí. Pomocí nástroje Draft můžete rychle znovu nasadit aplikaci do Kubernetes jako změnách kódu. Další informace o návrhu, najdete v článku [koncept dokumentaci na Githubu][draft-documentation].

Tento článek ukazuje, jak použití konceptu s clusterem Kubernetes v AKS.

## <a name="prerequisites"></a>Požadavky

Kroky popsané v tomto článku se předpokládá, že jste vytvořili AKS cluster a navázali `kubectl` připojení ke clusteru. Pokud potřebujete tyto položky, přečtěte si [AKS quickstart][aks-quickstart].

Je třeba privátního registru Dockeru v Azure Container Registry (ACR). Pokyny o tom, jak vytvořit instanci služby ACR, najdete v článku [rychlý start Azure Container Registry][acr-quickstart].

Helm musí také nainstalovaná ve vašem clusteru AKS. Další informace o tom, jak nainstalovat a nakonfigurovat Helm, naleznete v tématu [helmu pomocí Azure Kubernetes Service (AKS)][aks-helm].

A konečně, je nutné nainstalovat [Docker](https://www.docker.com).

## <a name="install-draft"></a>Instalace nástroje Draft

Rozhraní příkazového řádku nástroje Draft je klient, který běží ve vývojovém systému a umožňuje nasazení kódu do clusteru Kubernetes. Chcete-li nainstalovat rozhraní příkazového řádku nástroje Draft na počítači Mac, použijte `brew`. Další možnosti instalace, najdete v článku [koncept Instalační příručka][draft-documentation].

> [!NOTE]
> Pokud jste nainstalovali koncept starší než verze 0,12, nejprve odstranit koncept z clusteru pomocí `helm delete --purge draft` a pak odeberte konfiguraci místní spuštěním `rm -rf ~/.draft`. Pokud jste v systému MacOS spusťte `brew upgrade draft`.

```console
brew tap azure/draft
brew install draft
```

Teď inicializovat konceptu s `draft init` příkaz:

```console
draft init
```

## <a name="configure-draft"></a>Konfigurace nástroje Draft

Draft vytvoří místně Image kontejneru a pak buď nasadil v místním registru (například s Minikube), nebo využívá registru imagí, které zadáte. Tento článek používá Azure Container Registry (ACR), takže je potřeba vytvořit vztah důvěryhodnosti mezi clusteru AKS a registrem ACR, nakonfigurujte návrhu nasdílejte Image kontejneru do služby ACR.

### <a name="create-trust-between-aks-cluster-and-acr"></a>Vytvoření vztahu důvěryhodnosti mezi clusterem AKS a ACR

K navázání vztahu důvěryhodnosti mezi clusterem AKS a registr ACR, udělení oprávnění objektu služby Azure Active Directory používané clusterem AKS pro přístup k registru ACR. V následujících příkazech, zadejte vlastní `<resourceGroupName>`, nahraďte `<aksName>` s názvem clusteru AKS, a nahraďte `<acrName>` s názvem vašeho registru ACR:

```azurecli
# Get the service principal ID of your AKS cluster
AKS_SP_ID=$(az aks show --resource-group <resourceGroupName> --name <aksName> --query "servicePrincipalProfile.clientId" -o tsv)

# Get the resource ID of your ACR instance
ACR_RESOURCE_ID=$(az acr show --resource-group <resourceGroupName> --name <acrName> --query "id" -o tsv)

# Create a role assignment for your AKS cluster to access the ACR instance
az role assignment create --assignee $AKS_SP_ID --scope $ACR_RESOURCE_ID --role contributor
```

Další informace o tomto postupu pro přístup k ACR najdete v tématu [ověřování pomocí ACR](../container-registry/container-registry-auth-aks.md).

### <a name="configure-draft-to-push-to-and-deploy-from-acr"></a>Nakonfigurujte Draft pro vložení do a ze služby ACR nasazení

Teď, když existuje vztah důvěryhodnosti mezi AKS a ACR, povolte používání služby ACR v clusteru AKS.

1. Nastavit konfiguraci nástroje Draft *registru* hodnotu. V následujících příkazech nahraďte `<acrName>` s názvem vašeho registru ACR:

    ```console
    draft config set registry <acrName>.azurecr.io
    ```

1. Přihlaste se k registru služby ACR s [az acr login][az-acr-login]:

    ```azurecli
    az acr login --name <acrName>
    ```

Jak mezi AKS a ACR byl vytvořen vztah důvěryhodnosti, žádná hesla nebo tajné kódy jsou potřebné pro vložení do nebo o přijetí změn z registru ACR. Ověřování se odehrává na úrovni Azure Resource Manageru pomocí služby Azure Active Directory.

## <a name="run-an-application"></a>Spuštění aplikace

Pokud chcete zobrazit návrh v akci, můžeme nasadit ukázkovou aplikaci z [úložišti Draft][draft-repo]. Nejprve naklonujte úložiště:

```console
git clone https://github.com/Azure/draft
```

Přejděte do adresáře, příklady Java:

```console
cd draft/examples/example-java/
```

Použití `draft create` příkaz ke spuštění procesu. Tento příkaz vytvoří artefakty, které se používají ke spuštění aplikace v clusteru Kubernetes. Tyto položky zahrnují souboru Dockerfile a Digram helmu a *draft.toml* soubor, který je konfigurační soubor nástroje Draft.

```
$ draft create

--> Draft detected Java (92.205567%)
--> Ready to sail
```

Chcete-li spustit ukázkovou aplikaci v clusteru AKS, použijte `draft up` příkazu. Tento příkaz vytvoří soubor Dockerfile k vytvoření image kontejneru, ji vloží do služby ACR a nakonec nainstaluje grafu helmu a spusťte tak aplikaci ve službě AKS.

Při prvním spuštění tohoto příkazu, odesílání a stahování image kontejneru může chvíli trvat. Jakmile základní vrstvy jsou uložené v mezipaměti, čas potřebný k nasazení aplikace došlo k podstatnému omezení.

```
$ draft up

Draft Up Started: 'example-java': 01CMZAR1F4T1TJZ8SWJQ70HCNH
example-java: Building Docker Image: SUCCESS ⚓  (73.0720s)
example-java: Pushing Docker Image: SUCCESS ⚓  (19.5727s)
example-java: Releasing Application: SUCCESS ⚓  (4.6979s)
Inspect the logs with `draft logs 01CMZAR1F4T1TJZ8SWJQ70HCNH`
```

Pokud narazíte na problémy s nahráním image Dockeru, ujistěte se, že jste úspěšně přihlásili do registru ACR s [az acr login][az-acr-login], poté `draft up` příkaz znovu.

## <a name="test-the-application-locally"></a>Test aplikace v místním prostředí

Chcete-li aplikaci otestovat, použijte `draft connect` příkazu. Tento příkaz proxy zabezpečené připojení k podu Kubernetes. Jakmile budete hotovi, aplikace je přístupný na zadané adrese URL.

> [!NOTE]
> Může trvat několik minut, než se image kontejneru ke stažení a spuštění aplikace. Pokud se zobrazí chybová zpráva při přístupu k aplikaci, pokus o připojení.

```
$ draft connect

Connect to java:4567 on localhost:49804
[java]: SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
[java]: SLF4J: Defaulting to no-operation (NOP) logger implementation
[java]: SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
[java]: == Spark has ignited ...
[java]: >> Listening on 0.0.0.0:4567
```

Pro přístup k aplikaci, otevřete webový prohlížeč na adresu a port zadaný v `draft connect` výstupu, jako například *http://localhost:49804*. 

![Ukázková aplikace Java spuštěná s využitím Draftu](media/kubernetes-draft/sample-app.png)

Použití `Control+C` zastavit připojení proxy server.

> [!NOTE]
> Můžete také použít `draft up --auto-connect` příkazu k sestavení a nasazení vaší aplikace a okamžitě připojit k první spuštěný kontejner.

## <a name="access-the-application-on-the-internet"></a>Přístup k aplikaci na Internetu

V předchozím kroku vytvořili připojení proxy k podu aplikace ve vašem clusteru AKS. Jak vyvíjet a testovat aplikace, můžete zpřístupnit aplikace na Internetu. Zveřejnit aplikaci na Internetu, můžete vytvořit službu Kubernetes s typem [nástroj pro vyrovnávání zatížení][kubernetes-service-loadbalancer], nebo vytvořte [kontroler příchozího přenosu dat] [ kubernetes-ingress]. Vytvoříme *nástroj pro vyrovnávání zatížení* služby.

Nejprve aktualizovat *values.yaml* koncept pack určit, že služba s typem *nástroj pro vyrovnávání zatížení* by měly být vytvořeny:

```console
vi charts/java/values.yaml
```

Vyhledejte *service.type* vlastnost a aktualizujte hodnotu z *ClusterIP* k *nástroj pro vyrovnávání zatížení*, jak je znázorněno v následujícímu zhuštěnému příkladu:

```yaml
[...]
service:
  name: java
  type: LoadBalancer
  externalPort: 80
  internalPort: 4567
[...]
```

Uložte a zavřete soubor a potom použít `draft up` znovu spusťte aplikaci:

```console
draft up
```

Trvá několik minut, než služba vrátí veřejnou IP adresu. Chcete-li sledovat průběh, použijte `kubectl get service` příkazů *watch* parametr:

```console
kubectl get service --watch
```

Na začátku *EXTERNAL-IP* pro služby se zobrazí jako *čekající*:

```
NAME                TYPE          CLUSTER-IP    EXTERNAL-IP   PORT(S)        AGE
example-java-java   LoadBalancer  10.0.141.72   <pending>     80:32150/TCP   2m
```

Jakmile se adresa EXTERNAL-IP změní ze *čekající* na IP adresu, použijte `Control+C` Zastavit `kubectl` sledujte proces:

```
NAME                TYPE           CLUSTER-IP    EXTERNAL-IP     PORT(S)        AGE
example-java-java   LoadBalancer   10.0.141.72   52.175.224.118  80:32150/TCP   7m
```

Pokud chcete zobrazit aplikaci, přejděte na externí IP adresu vašeho nástroje pro vyrovnávání zatížení s `curl`:

```
$ curl 52.175.224.118

Hello World, I'm Java
```

## <a name="iterate-on-the-application"></a>Iterovat aplikace

Teď, když koncept je nakonfigurovaná a je aplikace spuštěna v Kubernetes, jsou nastavené pro iteraci kódu. Pokaždé, když chcete testovat aktualizovaný kód, spusťte `draft up` příkaz k aktualizaci spuštěné aplikaci.

V tomto příkladu aktualizujte ukázkovou aplikaci Java ke změně zobrazení textu. Otevřít *Hello.java* souboru:

```console
vi src/main/java/helloworld/Hello.java
```

Aktualizace textu výstup zobrazíte *Hello World, jsem v jazyce Java ve službě AKS!*:

```java
package helloworld;

import static spark.Spark.*;

public class Hello {
    public static void main(String[] args) {
        get("/", (req, res) -> "Hello World, I'm Java in AKS!");
    }
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

Pokud chcete zobrazit aktualizovanou aplikaci, znovu curl IP adresu vašeho nástroje pro vyrovnávání zatížení:

```
$ curl 52.175.224.118

Hello World, I'm Java in AKS!
```

## <a name="next-steps"></a>Další postup

Další informace o použití nástroje Draft najdete v dokumentaci nástroje Draft na Githubu.

> [!div class="nextstepaction"]
> [Dokumentace k návrhu][draft-documentation]

<!-- LINKS - external -->
[draft-documentation]: https://github.com/Azure/draft/tree/master/docs
[kubernetes-service-loadbalancer]: https://kubernetes.io/docs/concepts/services-networking/service/#type-loadbalancer
[draft-repo]: https://github.com/Azure/draft

<!-- LINKS - internal -->
[acr-quickstart]: ../container-registry/container-registry-get-started-azure-cli.md
[aks-helm]: ./kubernetes-helm.md
[kubernetes-ingress]: ./ingress.md
[aks-quickstart]: ./kubernetes-walkthrough.md
[az-acr-login]: /cli/azure/acr#az-acr-login
