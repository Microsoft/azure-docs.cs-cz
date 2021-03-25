---
title: Řešení běžných problémů Kubernetes s podporou ARC Azure
services: azure-arc
ms.service: azure-arc
ms.date: 03/02/2020
ms.topic: article
author: mlearned
ms.author: mlearned
description: Řešení běžných problémů s Kubernetes clustery s podporou ARC.
keywords: Kubernetes, oblouk, Azure, kontejnery
ms.openlocfilehash: 992ea75c48b2630032e1314610986fbc610eec7b
ms.sourcegitcommit: a8ff4f9f69332eef9c75093fd56a9aae2fe65122
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2021
ms.locfileid: "105025777"
---
# <a name="azure-arc-enabled-kubernetes-troubleshooting"></a>Kubernetes řešení potíží s podporou ARC Azure

Tento dokument popisuje příručky pro odstraňování problémů s připojením, oprávněními a agenty.

## <a name="general-troubleshooting"></a>Obecné řešení potíží

### <a name="azure-cli"></a>Azure CLI

Než začnete používat `az connectedk8s` příkazy nebo příkazy rozhraní příkazového `az k8s-configuration` řádku, ověřte, že je v Azure CLI nastavené fungování na správné předplatné Azure.

```azurecli
az account set --subscription 'subscriptionId'
az account show
```

### <a name="azure-arc-agents"></a>Agenti ARC Azure

Všichni agenti pro Kubernetes s povoleným ARC Azure se v oboru názvů nasazují jako lusky `azure-arc` . Všechny lusky by měly běžet a předávat kontroly stavu.

Nejdřív ověřte verzi Helm ARC Azure:

```console
$ helm --namespace default status azure-arc
NAME: azure-arc
LAST DEPLOYED: Fri Apr  3 11:13:10 2020
NAMESPACE: default
STATUS: deployed
REVISION: 5
TEST SUITE: None
```

Pokud se verze Helm nenajde nebo chybí, zkuste znovu [připojit cluster ke službě Azure ARC](./quickstart-connect-cluster.md) .

Pokud se verze Helm nachází v systému `STATUS: deployed` , ověřte stav agentů pomocí `kubectl` :

```console
$ kubectl -n azure-arc get deployments,pods
NAME                                       READY  UP-TO-DATE  AVAILABLE  AGE
deployment.apps/clusteridentityoperator     1/1       1          1       16h
deployment.apps/config-agent                1/1       1          1       16h
deployment.apps/cluster-metadata-operator   1/1       1          1       16h
deployment.apps/controller-manager          1/1       1          1       16h
deployment.apps/flux-logs-agent             1/1       1          1       16h
deployment.apps/metrics-agent               1/1       1          1       16h
deployment.apps/resource-sync-agent         1/1       1          1       16h

NAME                                            READY   STATUS  RESTART  AGE
pod/cluster-metadata-operator-7fb54d9986-g785b  2/2     Running  0       16h
pod/clusteridentityoperator-6d6678ffd4-tx8hr    3/3     Running  0       16h
pod/config-agent-544c4669f9-4th92               3/3     Running  0       16h
pod/controller-manager-fddf5c766-ftd96          3/3     Running  0       16h
pod/flux-logs-agent-7c489f57f4-mwqqv            2/2     Running  0       16h
pod/metrics-agent-58b765c8db-n5l7k              2/2     Running  0       16h
pod/resource-sync-agent-5cf85976c7-522p5        3/3     Running  0       16h
```

Všechny lusky by měly být zobrazeny `STATUS` `Running` buď ve sloupci, `3/3` nebo `2/2` `READY` . Načte protokoly a popište lusky vracející `Error` nebo `CrashLoopBackOff` . Pokud se některé lusky zablokují ve `Pending` stavu, můžou být na uzlech clusteru nedostatečné prostředky. [Horizontální navýšení kapacity clusteru](https://kubernetes.io/docs/tasks/administer-cluster/) může získat tyto lusky do `Running` stavu přechodu.

## <a name="connecting-kubernetes-clusters-to-azure-arc"></a>Připojení clusterů Kubernetes ke službě Azure ARC

Připojení clusterů k Azure vyžaduje přístup k předplatnému Azure a `cluster-admin` přístup k cílovému clusteru. Pokud se nemůžete připojit ke clusteru nebo nemáte dostatečná oprávnění, připojení clusteru ke službě Azure Arc se nezdaří.

### <a name="insufficient-cluster-permissions"></a>Nedostatečná oprávnění clusteru

Pokud zadaný soubor kubeconfig nemá dostatečná oprávnění k instalaci agentů Azure ARC, příkaz Azure CLI vrátí chybu.

```azurecli
$ az connectedk8s connect --resource-group AzureArc --name AzureArcCluster
Ensure that you have the latest helm version installed before proceeding to avoid unexpected errors.
This operation might take a while...

Error: list: failed to list: secrets is forbidden: User "myuser" cannot list resource "secrets" in API group "" at the cluster scope
```

Uživatel připojující cluster ke službě Azure ARC by měl mít `cluster-admin` přiřazenou roli v clusteru.

### <a name="installation-timeouts"></a>Vypršení časových limitů instalace

Připojení clusteru Kubernetes k Kubernetes s povoleným ARC Azure vyžaduje instalaci agentů Azure ARC v clusteru. Pokud je cluster spuštěný přes pomalé připojení k Internetu, může stažení Image kontejneru pro agenty trvat déle, než je časový limit Azure CLI.

```azurecli
$ az connectedk8s connect --resource-group AzureArc --name AzureArcCluster
Ensure that you have the latest helm version installed before proceeding to avoid unexpected errors.
This operation might take a while...
```

### <a name="helm-issue"></a>Problém Helm

Helm `v3.3.0-rc.1` verze obsahuje [problém](https://github.com/helm/helm/pull/8527) s tím, že při instalaci nebo upgradu Helm (používaný `connectedk8s` rozšířením CLI) dojde ke spuštění všech háčků, což vede k následující chybě:

```console
$ az connectedk8s connect -n shasbakstest -g shasbakstest
Ensure that you have the latest helm version installed before proceeding.
This operation might take a while...

Please check if the azure-arc namespace was deployed and run 'kubectl get pods -n azure-arc' to check if all the pods are in running state. A possible cause for pods stuck in pending state could be insufficientresources on the kubernetes cluster to onboard to arc.
ValidationError: Unable to install helm release: Error: customresourcedefinitions.apiextensions.k8s.io "connectedclusters.arc.azure.com" not found
```

Pokud chcete tento problém obnovit, postupujte takto:

1. Odstraňte prostředek Kubernetes s povoleným ARC Azure v Azure Portal.
2. Na svém počítači spusťte následující příkazy:
    
    ```console
    kubectl delete ns azure-arc
    kubectl delete clusterrolebinding azure-arc-operator
    kubectl delete secret sh.helm.release.v1.azure-arc.v1
    ```

3. Místo verze Release Candidate nainstalujte na svém počítači [stabilní verzi](https://helm.sh/docs/intro/install/) Helm 3.
4. Spuštěním `az connectedk8s connect` příkazu s příslušnými hodnotami připojte cluster ke službě Azure ARC.

## <a name="configuration-management"></a>Správa konfigurace

### <a name="general"></a>Obecné
Pokud chcete pomoct s řešením potíží s prostředkem konfigurace, spusťte příkaz AZ Commands se `--debug` zadaným parametrem.

```console
az provider show -n Microsoft.KubernetesConfiguration --debug
az k8s-configuration create <parameters> --debug
```

### <a name="create-configurations"></a>Vytvoření konfigurací

Oprávnění k zápisu pro prostředek Kubernetes s povoleným ARC Azure ( `Microsoft.Kubernetes/connectedClusters/Write` ) jsou nezbytná a dostatečná pro vytváření konfigurací v tomto clusteru.

### <a name="configuration-remains-pending"></a>Konfigurace zůstává `Pending`

```console
kubectl -n azure-arc logs -l app.kubernetes.io/component=config-agent -c config-agent
$ k -n pending get gitconfigs.clusterconfig.azure.com  -o yaml
apiVersion: v1
items:
- apiVersion: clusterconfig.azure.com/v1beta1
  kind: GitConfig
  metadata:
    creationTimestamp: "2020-04-13T20:37:25Z"
    generation: 1
    name: pending
    namespace: pending
    resourceVersion: "10088301"
    selfLink: /apis/clusterconfig.azure.com/v1beta1/namespaces/pending/gitconfigs/pending
    uid: d9452407-ff53-4c02-9b5a-51d55e62f704
  spec:
    correlationId: ""
    deleteOperator: false
    enableHelmOperator: false
    giturl: git@github.com:slack/cluster-config.git
    helmOperatorProperties: null
    operatorClientLocation: azurearcfork8s.azurecr.io/arc-preview/fluxctl:0.1.3
    operatorInstanceName: pending
    operatorParams: '"--disable-registry-scanning"'
    operatorScope: cluster
    operatorType: flux
  status:
    configAppliedTime: "2020-04-13T20:38:43.081Z"
    isSyncedWithAzure: true
    lastPolledStatusTime: ""
    message: 'Error: {exit status 1} occurred while doing the operation : {Installing
      the operator} on the config'
    operatorPropertiesHashed: ""
    publicKey: ""
    retryCountPublicKey: 0
    status: Installing the operator
kind: List
metadata:
  resourceVersion: ""
  selfLink: ""
```
## <a name="monitoring"></a>Monitorování

Azure Monitor pro kontejnery vyžaduje spuštění DaemonSet v privilegovaném režimu. Chcete-li úspěšně vytvořit kanonický cluster Charmed Kubernetes pro monitorování, spusťte následující příkaz:

```console
juju config kubernetes-worker allow-privileged=true
```