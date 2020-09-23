---
title: Použití kontejneru Počítačové zpracování obrazu s Kubernetes a Helm
titleSuffix: Azure Cognitive Services
description: Naučte se, jak nasadit kontejner Počítačové zpracování obrazu pomocí Kubernetes a Helm.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: 9a8e0dde8b24c39180a584c26af725ab82ea0176
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/22/2020
ms.locfileid: "90907107"
---
# <a name="use-computer-vision-container-with-kubernetes-and-helm"></a>Použití kontejneru Počítačové zpracování obrazu s Kubernetes a Helm

Jednou z možností, jak místně spravovat Počítačové zpracování obrazu kontejnery, je použití Kubernetes a Helm. Když použijete Kubernetes a Helm k definování Počítačové zpracování obrazu Image kontejneru, vytvoříme balíček Kubernetes. Tento balíček se nasadí do místního clusteru Kubernetes. Nakonec se podíváme, jak otestovat nasazené služby. Další informace o spouštění kontejnerů Docker bez orchestrace Kubernetes najdete v tématu [install and run počítačové zpracování obrazu Containers](computer-vision-how-to-install-containers.md).

## <a name="prerequisites"></a>Požadavky

Před použitím Počítačové zpracování obrazu kontejnerů v místním prostředí použijte následující požadavky:

| Vyžadováno | Účel |
|----------|---------|
| Účet Azure | Pokud ještě předplatné Azure nemáte, vytvořte si napřed [bezplatný účet][free-azure-account]. |
| Kubernetes CLI | [KUBERNETES CLI][kubernetes-cli] se vyžaduje pro správu sdílených přihlašovacích údajů z registru kontejneru. Kubernetes je také potřeba před Helm, což je správce balíčků Kubernetes. |
| Helm CLI | Nainstalujte rozhraní příkazového [řádku Helm][helm-install], které se používá k instalaci grafu Helm (definice balíčku kontejneru). |
| Prostředek Počítačové zpracování obrazu |Aby bylo možné kontejner používat, musíte mít:<br><br>Prostředek Azure **počítačové zpracování obrazu** a přidružený klíč rozhraní API identifikátor URI koncového bodu. Obě hodnoty jsou k dispozici na stránkách přehledu a klíčů pro daný prostředek a jsou požadovány ke spuštění kontejneru.<br><br>**{API_KEY}**: jeden ze dvou dostupných klíčů prostředků na stránce **klíče**<br><br>**{ENDPOINT_URI}**: koncový bod uvedený na stránce **Přehled**|

[!INCLUDE [Gathering required parameters](../containers/includes/container-gathering-required-parameters.md)]

### <a name="the-host-computer"></a>Hostitelský počítač

[!INCLUDE [Host Computer requirements](../../../includes/cognitive-services-containers-host-computer.md)]

### <a name="container-requirements-and-recommendations"></a>Požadavky na kontejner a doporučení

[!INCLUDE [Container requirements and recommendations](includes/container-requirements-and-recommendations.md)]

## <a name="connect-to-the-kubernetes-cluster"></a>Připojení ke clusteru Kubernetes

V hostitelském počítači se očekává, že bude dostupný cluster Kubernetes. V tomto kurzu najdete informace o [nasazení clusteru Kubernetes](../../aks/tutorial-kubernetes-deploy-cluster.md) pro koncepční porozumění způsobu nasazení clusteru Kubernetes do hostitelského počítače. Další informace o nasazeních najdete v [dokumentaci k Kubernetes](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/).

### <a name="sharing-docker-credentials-with-the-kubernetes-cluster"></a>Sdílení přihlašovacích údajů Docker s clusterem Kubernetes

Pokud chcete, aby cluster Kubernetes `docker pull` nakonfigurovali bitové kopie z `containerpreview.azurecr.io` registru kontejneru, je nutné přenést přihlašovací údaje Docker do clusteru. Spuštěním [`kubectl create`][kubectl-create] následujícího příkazu vytvořte *tajný klíč registru Docker-Registry* na základě přihlašovacích údajů, které jste zadali v části požadavky na přístup k registru kontejneru.

V rozhraní příkazového řádku, které zvolíte, spusťte následující příkaz. Nezapomeňte nahradit `<username>` , `<password>` a `<email-address>` s přihlašovacími údaji registru kontejneru.

```console
kubectl create secret docker-registry containerpreview \
    --docker-server=containerpreview.azurecr.io \
    --docker-username=<username> \
    --docker-password=<password> \
    --docker-email=<email-address>
```

> [!NOTE]
> Pokud již máte přístup k `containerpreview.azurecr.io` registru kontejneru, můžete místo toho vytvořit tajný kód Kubernetes pomocí obecného příznaku. Vezměte v úvahu následující příkaz, který se provede proti vašemu kódu JSON konfigurace Docker.
> ```console
>  kubectl create secret generic containerpreview \
>      --from-file=.dockerconfigjson=~/.docker/config.json \
>      --type=kubernetes.io/dockerconfigjson
> ```

Následující výstup je vytištěn do konzoly, pokud byl tajný kód úspěšně vytvořen.

```console
secret "containerpreview" created
```

Chcete-li ověřit, zda byl tajný klíč vytvořen, spusťte [`kubectl get`][kubectl-get] s `secrets` příznakem.

```console
kubectl get secrets
```

Po spuštění se `kubectl get secrets` zobrazí všechna nakonfigurovaná tajná klíčová.

```console
NAME                  TYPE                                  DATA      AGE
containerpreview      kubernetes.io/dockerconfigjson        1         30s
```

## <a name="configure-helm-chart-values-for-deployment"></a>Konfigurace hodnot grafu Helm pro nasazení

Začněte vytvořením složky s názvem *Read*. Pak vložte následující obsah YAML do nového souboru s názvem `chart.yaml` :

```yaml
apiVersion: v2
name: read
version: 1.0.0
description: A Helm chart to deploy the microsoft/cognitive-services-read to a Kubernetes cluster
dependencies:
- name: rabbitmq
  condition: read.image.args.rabbitmq.enabled
  version: ^6.12.0
  repository: https://kubernetes-charts.storage.googleapis.com/
- name: redis
  condition: read.image.args.redis.enabled
  version: ^6.0.0
  repository: https://kubernetes-charts.storage.googleapis.com/
```

Pokud chcete nakonfigurovat výchozí hodnoty grafu Helm, zkopírujte a vložte následující YAML do souboru s názvem `values.yaml` . Nahraďte `# {ENDPOINT_URI}` `# {API_KEY}` komentáře a vlastními hodnotami. V případě potřeby nakonfigurujte resultExpirationPeriod, Redis a RabbitMQ.

```yaml
# These settings are deployment specific and users can provide customizations

read:
  enabled: true
  image:
    name: cognitive-services-read
    registry:  containerpreview.azurecr.io/
    repository: microsoft/cognitive-services-read
    tag: latest
    pullSecret: containerpreview # Or an existing secret
    args:
      eula: accept
      billing: # {ENDPOINT_URI}
      apikey: # {API_KEY}
      
      # Result expiration period setting. Specify when the system should clean up recognition results.
      # For example, resultExpirationPeriod=1, the system will clear the recognition result 1hr after the process.
      # resultExpirationPeriod=0, the system will clear the recognition result after result retrieval.
      resultExpirationPeriod: 1
      
      # Redis storage, if configured, will be used by read container to store result records.
      # A cache is required if multiple read containers are placed behind load balancer.
      redis:
        enabled: false # {true/false}
        password: password

      # RabbitMQ is used for dispatching tasks. This can be useful when multiple read containers are
      # placed behind load balancer.
      rabbitmq:
        enabled: false # {true/false}
        rabbitmq:
          username: user
          password: password
```

> [!IMPORTANT]
> - Pokud `billing` `apikey` nejsou zadány hodnoty a, vyprší platnost služeb po 15 minutách. Ověřování je také neúspěšné, protože služby nejsou k dispozici.
> 
> - Pokud nasadíte více kontejnerů pro čtení za nástroj pro vyrovnávání zatížení, například v části Docker Compose nebo Kubernetes, musíte mít externí mezipaměť. Vzhledem k tomu, že kontejner zpracování a kontejner požadavků GET nemusí být stejné, externí mezipaměť ukládá výsledky a sdílí je napříč kontejnery. Podrobnosti o nastavení mezipaměti najdete v tématu [konfigurace kontejnerů docker počítačové zpracování obrazu](https://docs.microsoft.com/azure/cognitive-services/computer-vision/computer-vision-resource-container-config).
>

Vytvořte složku *šablon* v adresáři *pro čtení* . Zkopírujte a vložte následující YAML do souboru s názvem `deployment.yaml` . `deployment.yaml`Soubor bude sloužit jako šablona Helm.

> Šablony generují soubory manifestu, což jsou popisy prostředků ve formátu YAML, které Kubernetes může pochopit. [– Průvodce šablonou grafu Helm][chart-template-guide]

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: read
  labels:
    app: read-deployment
spec:
  selector:
    matchLabels:
      app: read-app
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
        args:        
        - ReadEngineConfig:ResultExpirationPeriod={{ .Values.read.image.args.resultExpirationPeriod }}
        {{- if .Values.read.image.args.rabbitmq.enabled }}
        - Queue:RabbitMQ:HostName={{ include "rabbitmq.hostname" . }}
        - Queue:RabbitMQ:Username={{ .Values.read.image.args.rabbitmq.rabbitmq.username }}
        - Queue:RabbitMQ:Password={{ .Values.read.image.args.rabbitmq.rabbitmq.password }}
        {{- end }}      
        {{- if .Values.read.image.args.redis.enabled }}
        - Cache:Redis:Configuration={{ include "redis.connStr" . }}
        {{- end }}
      imagePullSecrets:
      - name: {{.Values.read.image.pullSecret}}      
--- 
apiVersion: v1
kind: Service
metadata:
  name: read-service
spec:
  type: LoadBalancer
  ports:
  - port: 5000
  selector:
    app: read-app
```

Ve stejné složce *šablon* zkopírujte a vložte do nástroje následující pomocné funkce `helpers.tpl` . `helpers.tpl` definuje užitečné funkce, které vám pomůžou generovat šablonu Helm.

```yaml
{{- define "rabbitmq.hostname" -}}
{{- printf "%s-rabbitmq" .Release.Name -}}
{{- end -}}

{{- define "redis.connStr" -}}
{{- $hostMaster := printf "%s-redis-master:6379" .Release.Name }}
{{- $hostSlave := printf "%s-redis-slave:6379" .Release.Name -}}
{{- $passWord := printf "password=%s" .Values.read.image.args.redis.password -}}
{{- $connTail := "ssl=False,abortConnect=False" -}}
{{- printf "%s,%s,%s,%s" $hostMaster $hostSlave $passWord $connTail -}}
{{- end -}}
```
Šablona Určuje službu Vyrovnávání zatížení a nasazení kontejneru nebo obrázku pro čtení.

### <a name="the-kubernetes-package-helm-chart"></a>Balíček Kubernetes (Helm graf)

*Graf Helm* obsahuje konfiguraci, které imagí Docker mají být vyžádané z `containerpreview.azurecr.io` registru kontejneru.

> [Graf Helm][helm-charts] je kolekce souborů, které popisují související sadu prostředků Kubernetes. Jeden graf se dá použít k nasazení jednoduchého, podobného memcached nebo nějakého složitého, jako je úplný zásobník webových aplikací se servery HTTP, databázemi, mezipamětemi a tak dále.

Zadané *grafy Helm* vyžádají image Docker služby počítačové zpracování obrazu a odpovídající službu z `containerpreview.azurecr.io` registru kontejneru.

## <a name="install-the-helm-chart-on-the-kubernetes-cluster"></a>Instalace grafu Helm v clusteru Kubernetes

K instalaci *grafu Helm*je potřeba spustit [`helm install`][helm-install-cmd] příkaz. Zajistěte spuštění instalačního příkazu z adresáře nad `read` složkou.

```console
helm install read ./read
```

Tady je příklad výstupu, který byste mohli očekávat od úspěšného provedení instalace:

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

Dokončení nasazení Kubernetes může trvat několik minut. Chcete-li ověřit, že jsou všechny lusky i služby správně nasazené a dostupné, spusťte následující příkaz:

```console
kubectl get all
```

Měli byste očekávat, že se zobrazí něco podobného následujícímu výstupu:

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

Další podrobnosti o instalaci aplikací pomocí Helm ve službě Azure Kubernetes Service (AKS) [najdete tady][installing-helm-apps-in-aks].

> [!div class="nextstepaction"]
> [Kontejnery služeb Cognitive Services][cog-svcs-containers]

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
