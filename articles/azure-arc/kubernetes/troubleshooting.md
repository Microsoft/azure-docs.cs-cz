---
title: Řešení běžných problémů Kubernetes s podporou ARC Azure (Preview)
services: azure-arc
ms.service: azure-arc
ms.date: 05/19/2020
ms.topic: article
author: mlearned
ms.author: mlearned
description: Řešení běžných problémů s Kubernetes clustery s podporou ARC.
keywords: Kubernetes, oblouk, Azure, kontejnery
ms.openlocfilehash: 4a8f4c652f1ab73e0b9979f77d7de5014c8d31a8
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91540604"
---
# <a name="azure-arc-enabled-kubernetes-troubleshooting-preview"></a>Řešení potíží s Kubernetesem v Azure ARC (Preview)

Tento dokument popisuje některé běžné scénáře řešení potíží s připojením, oprávněními a agenty.

## <a name="general-troubleshooting"></a>Obecné řešení potíží

### <a name="azure-cli-set-up"></a>Nastavení Azure CLI
Než použijete příkaz AZ connectedk8s nebo AZ k8sconfiguration CLI, zajistěte, aby byl AZ nastavený na práci se správným předplatným Azure.

```console
az account set --subscription 'subscriptionId'
az account show
```

### <a name="azure-arc-agents"></a>agenti Azure – ARC
Všichni agenti pro Kubernetes s povoleným ARC Azure se v oboru názvů nasazují jako lusky `azure-arc` . V rámci normálních operací by měly být spuštěné všechny lusky a musí se předávat kontroly stavu.

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

Pokud se Helm verze nenajde nebo chybí, zkuste cluster znovu připojit.

Pokud je k dispozici verze Helm a `STATUS: deployed` Určete stav agentů pomocí `kubectl` :

```console
$ kubectl -n azure-arc get deployments,pods
NAME                                        READY   UP-TO-DATE AVAILABLE AGE
deployment.apps/cluster-metadata-operator   1/1     1           1        16h
deployment.apps/clusteridentityoperator     1/1     1           1        16h
deployment.apps/config-agent                1/1     1           1        16h
deployment.apps/controller-manager          1/1     1           1        16h
deployment.apps/flux-logs-agent             1/1     1           1        16h
deployment.apps/metrics-agent               1/1     1           1        16h
deployment.apps/resource-sync-agent         1/1     1           1        16h

NAME                                            READY   STATUS   RESTART AGE
pod/cluster-metadata-operator-7fb54d9986-g785b  2/2     Running  0       16h
pod/clusteridentityoperator-6d6678ffd4-tx8hr    3/3     Running  0       16h
pod/config-agent-544c4669f9-4th92               3/3     Running  0       16h
pod/controller-manager-fddf5c766-ftd96          3/3     Running  0       16h
pod/flux-logs-agent-7c489f57f4-mwqqv            2/2     Running  0       16h
pod/metrics-agent-58b765c8db-n5l7k              2/2     Running  0       16h
pod/resource-sync-agent-5cf85976c7-522p5        3/3     Running  0       16h
```

Všechny lusky by `STATUS` se měly zobrazovat jako `Running` a `READY` by měly být buď `3/3` nebo `2/2` . Načte protokoly a popište lusky, které vrací `Error` nebo `CrashLoopBackOff` . Pokud je některá z těchto lusků zablokovaná ve `Pending` stavu, může to být způsobeno nedostatečnými prostředky v uzlech clusteru. Při vertikálním [navýšení kapacity clusteru](https://kubernetes.io/docs/tasks/administer-cluster/cluster-management/#resizing-a-cluster) budou tyto lusky přecházet do `Running` stavu.

## <a name="connecting-kubernetes-clusters-to-azure-arc"></a>Připojení clusterů Kubernetes ke službě Azure ARC

Připojení clusterů k Azure vyžaduje přístup k předplatnému Azure a `cluster-admin` přístup k cílovému clusteru. Pokud cluster není dostupný nebo má nedostatečná oprávnění k registraci, dojde k chybě.

### <a name="insufficient-cluster-permissions"></a>Nedostatečná oprávnění clusteru

Pokud zadaný soubor kubeconfig nemá dostatečná oprávnění k instalaci agentů Azure ARC, příkaz Azure CLI vrátí při pokusu o volání rozhraní API Kubernetes chybu.

```console
$ az connectedk8s connect --resource-group AzureArc --name AzureArcCluster
Command group 'connectedk8s' is in preview. It may be changed/removed in a future release.
Ensure that you have the latest helm version installed before proceeding to avoid unexpected errors.
This operation might take a while...

Error: list: failed to list: secrets is forbidden: User "myuser" cannot list resource "secrets" in API group "" at the cluster scope
```

Vlastník clusteru by měl používat uživatele Kubernetes s oprávněními správce clusteru.

### <a name="installation-timeouts"></a>Vypršení časových limitů instalace

Instalace agenta Azure ARC vyžaduje spuštění sady kontejnerů v cílovém clusteru. Pokud cluster běží přes pomalé připojení k Internetu, může převzetí služeb při selhání image kontejneru trvat déle než vypršení časových limitů Azure CLI.

```console
$ az connectedk8s connect --resource-group AzureArc --name AzureArcCluster
Command group 'connectedk8s' is in preview. It may be changed/removed in a future release.
Ensure that you have the latest helm version installed before proceeding to avoid unexpected errors.
This operation might take a while...
```

### <a name="helm-issue"></a>Problém Helm

Helm verze obsahuje problém s tím, že se při `v3.3.0-rc.1` instalaci nebo upgradu Helm (za použití v rámci rozšíření digestoře CONNECTEDK8S CLI) spustí všechny háky, což vede k následující chybě: [issue](https://github.com/helm/helm/pull/8527)

```console
$ az connectedk8s connect -n shasbakstest -g shasbakstest
Command group 'connectedk8s' is in preview. It may be changed/removed in a future release.
Ensure that you have the latest helm version installed before proceeding.
This operation might take a while...

Please check if the azure-arc namespace was deployed and run 'kubectl get pods -n azure-arc' to check if all the pods are in running state. A possible cause for pods stuck in pending state could be insufficientresources on the kubernetes cluster to onboard to arc.
ValidationError: Unable to install helm release: Error: customresourcedefinitions.apiextensions.k8s.io "connectedclusters.arc.azure.com" not found
```

Pokud chcete tento problém obnovit, postupujte takto:

1. Odstraňte prostředek Kubernetes s povolenou podporou Azure ARC ve Azure Portal.
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
Chcete-li pomoct řešit problémy s konfigurací správy zdrojového kódu, spusťte příkaz AZ Commands with--Debug.

```console
az provider show -n Microsoft.KubernetesConfiguration --debug
az k8sconfiguration create <parameters> --debug
```

### <a name="create-source-control-configuration"></a>Vytvořit konfiguraci správy zdrojového kódu
Role Přispěvatel v prostředku Microsoft. Kubernetes/connectedCluster je nezbytná a dostatečná pro vytvoření prostředku Microsoft. KubernetesConfiguration/sourceControlConfiguration.

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