---
title: Použití kontejneru počítačového vidění s Kubernetes a Helmem
titleSuffix: Azure Cognitive Services
description: Nasaďte kontejner Počítačové zpracování obrazu do instance kontejneru Azure a otestujte ho ve webovém prohlížeči.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: 9aac374de5af748eafbe4c22e5fc89f64e483c2a
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/08/2020
ms.locfileid: "80877965"
---
# <a name="use-computer-vision-container-with-kubernetes-and-helm"></a>Použití kontejneru počítačového vidění s Kubernetes a Helmem

Jednou z možností místní správy kontejnerů počítačového vidění je použití Kubernetes a Helm. Pomocí Kubernetes a Helm k definování image kontejneru počítačového vidění vytvoříme balíček Kubernetes. Tento balíček se nasadí do místního clusteru Kubernetes. Nakonec se podíváme, jak otestovat nasazené služby. Další informace o spouštění kontejnerů Dockeru bez orchestrace Kubernetes najdete v tématu [instalace a spuštění kontejnerů počítačového vidění](computer-vision-how-to-install-containers.md).

## <a name="prerequisites"></a>Požadavky

Následující předpoklady před použitím kontejnerů počítačového zpracování v místním prostředí:

| Požaduje se | Účel |
|----------|---------|
| Účet Azure | Pokud nemáte předplatné Azure, vytvořte si [bezplatný účet,][free-azure-account] než začnete. |
| Kubernetes CLI | [Kubernetes CLI][kubernetes-cli] je vyžadovánpro správu sdílených pověření z registru kontejneru. Kubernetes je také potřeba před Helmem, který je správcem balíčků Kubernetes. |
| Helm CLI | Nainstalujte [helmcli][helm-install], který se používá k instalaci kormidelník (definice balíčku kontejneru). |
| Zdroj počítačového vidění |Chcete-li kontejner používat, musíte mít:<br><br>Prostředek Azure **Computer Vision** a přidružený klíč rozhraní API identifikátor URI koncového bodu. Obě hodnoty jsou k dispozici na stránce Přehled a Klíče pro prostředek a jsou nutné ke spuštění kontejneru.<br><br>**{API_KEY}:** Jeden ze dvou dostupných klíčů prostředků na stránce **Klíče**<br><br>**{ENDPOINT_URI}:** Koncový bod uvedený na stránce **Přehled**|

[!INCLUDE [Gathering required parameters](../containers/includes/container-gathering-required-parameters.md)]

### <a name="the-host-computer"></a>Hostitelský počítač

[!INCLUDE [Host Computer requirements](../../../includes/cognitive-services-containers-host-computer.md)]

### <a name="container-requirements-and-recommendations"></a>Požadavky na kontejnery a doporučení

[!INCLUDE [Container requirements and recommendations](includes/container-requirements-and-recommendations.md)]

## <a name="connect-to-the-kubernetes-cluster"></a>Připojení ke clusteru Kubernetes

Očekává se, že hostitelský počítač bude mít k dispozici cluster Kubernetes. V tomto kurzu o [nasazení clusteru Kubernetes](../../aks/tutorial-kubernetes-deploy-cluster.md) pro koncepční pochopení, jak nasadit cluster Kubernetes do hostitelského počítače.

### <a name="sharing-docker-credentials-with-the-kubernetes-cluster"></a>Sdílení přihlašovacích údajů Dockeru pomocí clusteru Kubernetes

Chcete-li povolit cluster Uubernetes na `docker pull` konfigurované bitové kopie z registru `containerpreview.azurecr.io` kontejneru, je třeba přenést pověření dockeru do clusteru. Spusťte níže uvedený [`kubectl create`][kubectl-create] příkaz a vytvořte tajný klíč registru *dockeru* na základě pověření poskytnutých z předpokladu přístupu k registru kontejneru.

Z vybraného rozhraní příkazového řádku spusťte následující příkaz. Nezapomeňte nahradit pověření `<username>` `<password>`registru `<email-address>` kontejneru a pověření kontejneru.

```console
kubectl create secret docker-registry containerpreview \
    --docker-server=containerpreview.azurecr.io \
    --docker-username=<username> \
    --docker-password=<password> \
    --docker-email=<email-address>
```

> [!NOTE]
> Pokud již máte přístup `containerpreview.azurecr.io` k registru kontejneru, můžete vytvořit tajný klíč Kubernetes pomocí obecný příznak místo. Zvažte následující příkaz, který se provede proti konfiguraci Dockeru JSON.
> ```console
>  kubectl create secret generic containerpreview \
>      --from-file=.dockerconfigjson=~/.docker/config.json \
>      --type=kubernetes.io/dockerconfigjson
> ```

Následující výstup je vytištěn do konzoly po úspěšném vytvoření tajného klíče.

```console
secret "containerpreview" created
```

Chcete-li ověřit, že tajný [`kubectl get`][kubectl-get] klíč `secrets` byl vytvořen, spusťte s příznakem.

```console
kubectl get secrets
```

Spuštění vytiskne `kubectl get secrets` všechny nakonfigurované tajné klíče.

```console
NAME                  TYPE                                  DATA      AGE
containerpreview      kubernetes.io/dockerconfigjson        1         30s
```

## <a name="configure-helm-chart-values-for-deployment"></a>Konfigurace hodnot grafu helmu pro nasazení

Začněte vytvořením složky s názvem *read*a vložte následující obsah YAML do nového souboru s názvem *Chart.yml*.

```yaml
apiVersion: v1
name: read
version: 1.0.0
description: A Helm chart to deploy the microsoft/cognitive-services-read to a Kubernetes cluster
```

Chcete-li nakonfigurovat výchozí hodnoty grafu Helm, zkopírujte a `values.yaml`vložte následující hodnotu YAML do souboru s názvem . `# {ENDPOINT_URI}` Nahraďte `# {API_KEY}` komentáře a vlastními hodnotami.

```yaml
# These settings are deployment specific and users can provide customizations

read:
  enabled: true
  image:
    name: cognitive-services-read
    registry: containerpreview.azurecr.io/
    repository: microsoft/cognitive-services-read
    tag: latest
    pullSecret: containerpreview # Or an existing secret
    args:
      eula: accept
      billing: # {ENDPOINT_URI}
      apikey: # {API_KEY}
```

> [!IMPORTANT]
> Pokud `billing` hodnoty `apikey` a nejsou k dispozici, služby vyprší po 15 minutách. Stejně tak se ověření nezdaří, protože služby nebudou k dispozici.

Vytvořte složku *šablon* pod adresářem *pro čtení.* Zkopírujte a vložte následující yaml do souboru s názvem `deployment.yaml`. Soubor `deployment.yaml` bude sloužit jako šablona Helm.

> Šablony generují soubory manifestu, což jsou popisy prostředků formátovaných yaml, kterým Kubernetes rozumí. [- Průvodce šablonami grafu helmu][chart-template-guide]

```yaml
apiVersion: apps/v1beta1
kind: Deployment
metadata:
  name: read
spec:
  template:
    metadata:
      labels:
        app: read-app
    spec:
      containers:
      - name: {{.Values.read.image.name}}
        image: {{.Values.read.image.registry}}{{.Values.read.image.repository}}
        ports:
        - containerPort: 5000
        env:
        - name: EULA
          value: {{.Values.read.image.args.eula}}
        - name: billing
          value: {{.Values.read.image.args.billing}}
        - name: apikey
          value: {{.Values.read.image.args.apikey}}
      imagePullSecrets:
      - name: {{.Values.read.image.pullSecret}}

--- 
apiVersion: v1
kind: Service
metadata:
  name: read
spec:
  type: LoadBalancer
  ports:
  - port: 5000
  selector:
    app: read-app
```

Šablona určuje službu vyrovnávání zatížení a nasazení kontejneru/bitové kopie pro čtení.

### <a name="the-kubernetes-package-helm-chart"></a>Balíček Kubernetes (Helmův graf)

*Helm graf* obsahuje konfiguraci, které image dockeru `containerpreview.azurecr.io` pro vyžádat z registru kontejneru.

> [Helm chart][helm-charts] je kolekce souborů, které popisují související sadu prostředků Kubernetes. Jeden graf může být použit k nasazení něco jednoduchého, jako memcached pod nebo něco složitého, jako je úplný zásobník webových aplikací s HTTP servery, databázemi, mezipaměti a tak dále.

Za *předpokladu, helm grafy* vytáhnout docker image služby počítačového zpracování obrazu a odpovídající služby z registru kontejneru. `containerpreview.azurecr.io`

## <a name="install-the-helm-chart-on-the-kubernetes-cluster"></a>Instalace grafu Helm v clusteru Kubernetes

Chcete-li nainstalovat *kormidelník* [`helm install`][helm-install-cmd] , budeme muset provést příkaz. Ujistěte se, že spustit příkaz `read` install z adresáře nad složkou.

```console
helm install read ./read
```

Zde je příklad výstupu, který můžete očekávat z úspěšného spuštění instalace:

```console
NAME: read
LAST DEPLOYED: Thu Sep 04 13:24:06 2019
NAMESPACE: default
STATUS: DEPLOYED

RESOURCES:
==> v1/Pod(related)
NAME                    READY  STATUS             RESTARTS  AGE
read-57cb76bcf7-45sdh   0/1    ContainerCreating  0         0s

==> v1/Service
NAME     TYPE          CLUSTER-IP    EXTERNAL-IP  PORT(S)         AGE
read     LoadBalancer  10.110.44.86  localhost    5000:31301/TCP  0s

==> v1beta1/Deployment
NAME    READY  UP-TO-DATE  AVAILABLE  AGE
read    0/1    1           0          0s
```

Nasazení Kubernetes může trvat déle než několik minut. Chcete-li ověřit, zda jsou pody i služby správně nasazeny a k dispozici, proveďte následující příkaz:

```console
kubectl get all
```

Měli byste očekávat, že uvidíte něco podobného následujícímu výstupu:

```console
kubectl get all
NAME                        READY   STATUS    RESTARTS   AGE
pod/read-57cb76bcf7-45sdh   1/1     Running   0          17s

NAME                   TYPE           CLUSTER-IP     EXTERNAL-IP   PORT(S)          AGE
service/kubernetes     ClusterIP      10.96.0.1      <none>        443/TCP          45h
service/read           LoadBalancer   10.110.44.86   localhost     5000:31301/TCP   17s

NAME                   READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/read   1/1     1            1           17s

NAME                              DESIRED   CURRENT   READY   AGE
replicaset.apps/read-57cb76bcf7   1         1         1       17s
```
<!--  ## Validate container is running -->

[!INCLUDE [Container's API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

## <a name="next-steps"></a>Další kroky

Další podrobnosti o instalaci aplikací pomocí helmu ve službě Azure Kubernetes Service (AKS) [najdete na tomto webu][installing-helm-apps-in-aks].

> [!div class="nextstepaction"]
> [Kontejnery služeb cognitive services][cog-svcs-containers]

<!-- LINKS - external -->
[free-azure-account]: https://azure.microsoft.com/free
[git-download]: https://git-scm.com/downloads
[azure-cli]: https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest
[docker-engine]: https://www.docker.com/products/docker-engine
[kubernetes-cli]: https://kubernetes.io/docs/tasks/tools/install-kubectl
[helm-install]: https://helm.sh/docs/using_helm/#installing-helm
[helm-install-cmd]: https://helm.sh/docs/intro/using_helm/#helm-install-installing-a-package
[helm-charts]: https://helm.sh/docs/topics/charts/
[kubectl-create]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#create
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[helm-test]: https://helm.sh/docs/helm/#helm-test
[chart-template-guide]: https://helm.sh/docs/chart_template_guide

<!-- LINKS - internal -->
[vision-container-host-computer]: computer-vision-how-to-install-containers.md#the-host-computer
[installing-helm-apps-in-aks]: ../../aks/kubernetes-helm.md
[cog-svcs-containers]: ../cognitive-services-container-support.md
