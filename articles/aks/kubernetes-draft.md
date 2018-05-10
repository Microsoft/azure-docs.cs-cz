---
title: Použít koncept s AKS a kontejner Azure registru
description: Použít koncept s AKS a kontejner Azure registru
services: container-service
author: neilpeterson
manager: timlt
ms.service: container-service
ms.topic: article
ms.date: 03/29/2018
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: f474921acbd3a5082afea03dd982bcfa9c52dcda
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/07/2018
---
# <a name="use-draft-with-azure-kubernetes-service-aks"></a>Použít koncept se službou Azure Kubernetes (AKS)

Koncept je otevřený nástroj, který pomáhá obsahovat a nasazení těchto kontejnerů v clusteru s podporou Kubernetes, můžete ponechat volné soustředit se na vývojáře cyklus – "vnitřní smyčka" vměstnány vývoj. Koncept funguje, jako je vyvíjen kód, ale před potvrzením do správy verzí. S koncept můžete rychle znovu nasadit aplikace do Kubernetes jsou prováděny změny kódu. Další informace o návrhu najdete v tématu [koncept dokumentaci na Githubu][draft-documentation].

Tento dokument údaje pomocí clusteru Kubernetes AKS koncept.

## <a name="prerequisites"></a>Požadavky

Podrobně popsané kroky v tomto dokumentu předpokládají, že jste vytvořili cluster AKS a navázali s ním připojení přes kubectl. Pokud budete potřebovat tyto položky, najdete v článku [rychlý start AKS][aks-quickstart].

Budete také potřebovat privátní registru Docker v registru kontejner Azure (ACR). Pokyny pro nasazení ACR instance najdete v tématu [Azure kontejneru registru Quickstart][acr-quickstart].

Helm musí být nainstalována také v AKS clusteru. Další informace o instalaci helm najdete v tématu [Helm použití s Azure Kubernetes služby (AKS)][aks-helm].

Nakonec je nutné nainstalovat [Docker](https://www.docker.com).

## <a name="install-draft"></a>Nainstalujte konceptu

Rozhraní příkazového řádku koncept je klient, který běží ve vývojovém systému a umožňuje že vám quicky nasazení kódu do clusteru s podporou Kubernetes.

> [!NOTE]
> Pokud jste nainstalovali koncept starší než verze 0.12, z clusteru pomocí nejprve odstraňte koncept `helm delete --purge draft` a pak odeberte konfiguraci místní spuštěním `rm -rf ~/.draft`. Pokud jste v systému MacOS, spusťte `brew upgrade draft`.

Instalace rozhraní příkazového řádku koncept na použití Mac `brew`. Další informace o instalaci naleznete v části, [koncept nainstalovat Průvodce][install-draft].

```console
brew tap azure/draft
brew install draft
```

Nyní inicializovat koncept s `draft init` příkaz.

```console
draft init
```

## <a name="configure-draft"></a>Konfigurace konceptu

Koncept sestavení Image kontejneru místně a pak je buď nasadí v místním registru (v případě Minikube), nebo je nutné zadat registru bitové kopie na použití. Tento příklad používá Azure Container registru (ACR), proto je nutné vytvořit vztah důvěryhodnosti mezi AKS clusteru a ACR registru a nakonfigurovat koncept k ACR kontejneru.

### <a name="create-trust-between-aks-cluster-and-acr"></a>Vytvořit vztah důvěryhodnosti mezi AKS clusteru a ACR

K vybudování důvěry mezi clusteru služby AKS a registru ACR, upravte Azure Active Directory Service Principal použít s AKS přidáním role Přispěvatel na ni s oborem ACR úložiště. Uděláte to tak, spusťte následující příkazy, nahraďte _&lt;aks-rg-name&gt;_ a _&lt;název clusteru aks&gt;_ s skupinu prostředků a název vaší AKS clusteru, a _&lt;acr. rg osoby&gt;_ a _&lt;název acr úložišti&gt;_ s název skupiny a úložiště prostředků ACR úložiště, pro který chcete vytvořit vztah důvěryhodnosti.

```console
export AKS_SP_ID=$(az aks show -g <aks-rg-name> -n <aks-cluster-name> --query "servicePrincipalProfile.clientId" -o tsv)
export ACR_RESOURCE_ID=$(az acr show -g <acr-rg-name> -n <acr-repo-name> --query "id" -o tsv)
az role assignment create --assignee $AKS_SP_ID --scope $ACR_RESOURCE_ID --role contributor
```

(Tyto kroky a další mechanismy ověřování pro přístup k ACR jsou [ověřování s ACR](../container-registry/container-registry-auth-aks.md).)

### <a name="configure-draft-to-push-to-and-deploy-from-acr"></a>Konfigurace koncept nabízená a nasazení z ACR

Teď, když existuje vztah důvěryhodnosti mezi AKS a ACR, následující kroky povolí použití ACR z AKS clusteru.
1. Nastavte konfiguraci koncept `registry` hodnotu spuštěním `draft config set registry <registry name>.azurecr.io`, kde _&lt;název registru&lt;_ je název vaší ACR registru.
2. Přihlaste se k registru ACR spuštěním `az acr login -n <registry name>`.

Protože jste nyní přihlášeni místně na ACR a vytvořit důvěryhodný vztah s AKS a ACR, je potřeba nabízená nebo načítat z ACR do AKS bez hesla nebo tajných klíčů. Ověřování se odehrává na úrovni správce prostředků Azure, pomocí služby Azure Active Directory.

## <a name="run-an-application"></a>Spuštění aplikace

Koncept úložiště zahrnuje několik ukázkové aplikace, které lze použít pro ukázkové konceptu. Vytvoření klonovaného kopie úložišti.

```console
git clone https://github.com/Azure/draft
```

Přejděte do adresáře příklady Java.

```console
cd draft/examples/example-java/
```

Použití `draft create` příkaz ke spuštění procesu. Tento příkaz vytvoří artefakty, které se používají ke spuštění aplikace v clusteru s podporou Kubernetes. Soubor Docker graf Helm, zahrnout tyto položky a `draft.toml` souboru, který je v souboru konfigurace konceptu.

```console
draft create
```

Výstup:

```console
--> Draft detected the primary language as Java with 92.205567% certainty.
--> Ready to sail
```

Ke spuštění aplikace v clusteru s podporou Kubernetes, použijte `draft up` příkaz. Tento příkaz vytvoří soubor Docker vytvoření bitové kopie kontejneru, nabízených oznámení bitovou kopii k ACR a nakonec nainstaluje Helm grafu a spusťte aplikaci v AKS.

Při prvním spuštění, když zavedete a stahování bitovou kopii kontejner může trvat delší dobu; Jakmile základní vrstvy, které jsou uložené v mezipaměti, je výrazně snížit dobu trvání.

```console
draft up
```

Výstup:

```console
Draft Up Started: 'example-java'
example-java: Building Docker Image: SUCCESS ⚓  (1.0003s)
example-java: Pushing Docker Image: SUCCESS ⚓  (3.0007s)
example-java: Releasing Application: SUCCESS ⚓  (0.9322s)
example-java: Build ID: 01C9NPDYQQH2CZENDMZW7ESJAM
Inspect the logs with `draft logs 01C9NPDYQQH2CZENDMZW7ESJAM`
```

## <a name="test-the-application"></a>Testování aplikace

K otestování aplikace, použijte `draft connect` příkaz. Tento příkaz proxy připojení k pod Kubernetes povolení zabezpečené místní připojení. Po dokončení aplikace jsou přístupné na zadané adrese URL.

V některých případech může trvat několik minut pro bitovou kopii kontejner ke stažení a spuštění aplikace. Pokud obdržíte chybu při přístupu k aplikaci, pokus o připojení.

```console
draft connect
```

Výstup:

```console
Connecting to your app...SUCCESS...Connect to your app on localhost:46143
Starting log streaming...
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
== Spark has ignited ...
>> Listening on 0.0.0.0:4567
```

Teď aplikaci otestovat procházením http://localhost:46143 (pro předchozí příklad; portů může být jiný). Po dokončení testování aplikace pomocí `Control+C` zastavit připojení k proxy serveru.

> [!NOTE]
> Můžete také `draft up --auto-connect` příkaz k vytvoření a nasazení aplikace a okamžitě připojit k první spuštěné kontejneru aby iterace cyklus i rychlejší.

## <a name="expose-application"></a>Zveřejnit aplikaci

Při testování aplikace v Kubernetes, můžete chtít zpřístupnění aplikace v síti internet. To lze provést pomocí Kubernetes služby s typem [nástroj pro vyrovnávání zatížení] [ kubernetes-service-loadbalancer] nebo [příjem příchozích dat řadič][kubernetes-ingress]. Tento dokument údaje pomocí Kubernetes služby.


Nejdřív na konceptu pack musí aktualizovat určíte, že služby s typem `LoadBalancer` by měl být vytvořen. Uděláte to tak, aktualizujte typ služby v `values.yaml` souboru.

```console
vi charts/java/values.yaml
```

Vyhledejte `service.type` vlastnost a aktualizujte hodnotu z `ClusterIP` k `LoadBalancer`.

```yaml
replicaCount: 2
image:
  repository: openjdk
  tag: 8-jdk-alpine
  pullPolicy: IfNotPresent
service:
  name: java
  type: LoadBalancer
  externalPort: 80
  internalPort: 4567
resources:
  limits:
    cpu: 100m
    memory: 128Mi
  requests:
    cpu: 100m
    memory: 128Mi
  ```

Spustit `draft up` a znovu spusťte aplikaci.

```console
draft up
```

Může trvat několik minut, než službu, kterou chcete vrátit na veřejnou IP adresu. Ke sledování průběhu použijte `kubectl get service` s sledovat.

```console
kubectl get service -w
```

Standardně *externí IP* pro služby se zobrazí jako `pending`.

```
example-java-java   10.0.141.72   <pending>     80:32150/TCP   14m
```

Jakmile se stav adresy EXTERNAL-IP změní ze stavu `pending` na `IP address`, pomocí klávesové zkratky `Control+C` zastavte sledovací proces kubectl.

```
example-java-java   10.0.141.72   52.175.224.118   80:32150/TCP   17m
```

Pokud se chcete na aplikaci podívat, přejděte na externí IP adresu.

```console
curl 52.175.224.118
```

Výstup:

```
Hello World, I'm Java
```

## <a name="iterate-on-the-application"></a>Iterace v aplikaci

Teď, když byl nakonfigurován koncept a aplikace běží v Kubernetes, se nastavují pro iterace kódu. Pokaždé, když chcete otestovat aktualizované kódu, spusťte `draft up` příkaz k aktualizaci běžící aplikaci.

V tomto příkladu aktualizujte aplikaci Java hello world.

```console
vi src/main/java/helloworld/Hello.java
```

Aktualizace textu Hello World.

```java
package helloworld;

import static spark.Spark.*;

public class Hello {
    public static void main(String[] args) {
        get("/", (req, res) -> "Hello World, I'm Java in AKS!");
    }
}
```

Spustit `draft up --auto-connect` příkaz znovu nasadit aplikaci jen co nejrychleji pod je připraven k reagovat.

```console
draft up --auto-connect
```

Výstup

```
Draft Up Started: 'example-java'
example-java: Building Docker Image: SUCCESS ⚓  (1.0003s)
example-java: Pushing Docker Image: SUCCESS ⚓  (4.0010s)
example-java: Releasing Application: SUCCESS ⚓  (1.1336s)
example-java: Build ID: 01C9NPMJP6YM985GHKDR2J64KC
Inspect the logs with `draft logs 01C9NPMJP6YM985GHKDR2J64KC`
Connect to java:4567 on localhost:39249
Your connection is still active.
Connect to java:4567 on localhost:39249
[java]: SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
[java]: SLF4J: Defaulting to no-operation (NOP) logger implementation
[java]: SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
[java]: == Spark has ignited ...
[java]: >> Listening on 0.0.0.0:4567

```

Nakonec zobrazte aplikaci zobrazíte aktualizace.

```console
curl 52.175.224.118
```

Výstup:

```
Hello World, I'm Java in AKS!
```

## <a name="next-steps"></a>Další postup

Další informace o používání koncept naleznete v dokumentaci k koncept na Githubu.

> [!div class="nextstepaction"]
> [Koncept dokumentace][draft-documentation]

<!-- LINKS - external -->
[draft-documentation]: https://github.com/Azure/draft/tree/master/docs
[install-draft]: https://github.com/Azure/draft/blob/master/docs/install.md
[kubernetes-ingress]: ./ingress.md
[kubernetes-service-loadbalancer]: https://kubernetes.io/docs/concepts/services-networking/service/#type-loadbalancer

<!-- LINKS - internal -->
[acr-quickstart]: ../container-registry/container-registry-get-started-azure-cli.md
[aks-helm]: ./kubernetes-helm.md
[aks-quickstart]: ./kubernetes-walkthrough.md
