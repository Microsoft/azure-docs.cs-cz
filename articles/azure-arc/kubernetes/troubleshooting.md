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
ms.openlocfilehash: e0583fdeaa0819ec961e87ae89ee3aa7592d1f79
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/20/2020
ms.locfileid: "83680702"
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
$ kubectl -n azure-arc get deploy,pods
NAME                                 READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/config-agent         1/1     1            1           53s
deployment.apps/connect-agent        1/1     1            1           53s
deployment.apps/controller-manager   1/1     1            1           53s
deployment.apps/metrics-agent        1/1     1            1           53s

NAME                                      READY   STATUS    RESTARTS   AGE
pod/config-agent-74cf758b5f-cxnhs         2/2     Running   0          53s
pod/connect-agent-bc6b9ff5d-dzkvf         2/2     Running   0          53s
pod/controller-manager-7cf95d5d77-wv5cw   2/2     Running   0          53s
pod/metrics-agent-c77c9dfc7-45n5r         1/1     Running   0          53s
```

Všechny lusky by `STATUS` se měly zobrazovat jako `Running` a `READY` by měly být buď `2/2` nebo `1/1` . Načte protokoly a popište lusky, které vrací `Error` nebo `CrashLoopBackOff` .

## <a name="unable-to-connect-my-kubernetes-cluster-to-azure"></a>Nepovedlo se připojit svůj cluster Kubernetes k Azure.

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

There was a problem with connect-agent deployment. Please run 'kubectl -n azure-arc logs -l app.kubernetes.io/component=connect-agent -c connect-agent' to debug the error.
```

### <a name="incorrect-or-expired-onboarding-credentials"></a>Nesprávná nebo vypršela pověření k registraci

```console
$ kubectl -n azure-arc get deploy,pod
NAME                                 READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/config-agent         1/1     1            1           8m11s
deployment.apps/connect-agent        0/1     1            0           8m11s
deployment.apps/controller-manager   1/1     1            1           8m11s
deployment.apps/metrics-agent        1/1     1            1           8m11s

NAME                                      READY   STATUS             RESTARTS   AGE
pod/config-agent-74cf758b5f-d7qz9         2/2     Running            0          8m11s
pod/connect-agent-bc6b9ff5d-sd9fb         1/2     CrashLoopBackOff   6          8m11s
pod/controller-manager-7cf95d5d77-qlsvs   2/2     Running            0          8m11s
pod/metrics-agent-c77c9dfc7-lp2rf         1/1     Running            1          8m11s
```

Connect Agent protokoluje všechny chyby komunikující s Azure a místním serverem Kubernetes API jako protokoly Standard pod. Načtěte protokoly pomocí nástroje `kubectl` pro ladění.

```console
$ kubectl -n azure-arc logs -l app.kubernetes.io/component=connect-agent -c connect-agent
2020/04/07 20:52:50 Environment validation :success
2020/04/07 20:52:50 Kubernetes API server access validation :success
2020/04/07 20:52:51 Azure Subscription access token :error :http request failed. Authentication Token URL:https://login.microsoftonline.com/72f988bf-86f1-41af-91ab-2d7cd011db47/oauth2/token Authentication Token Body:grant_type=client_credentials&client_id=82195c37-7497-458c-b643-f4a3d0a64190&client_secret=9814c84e-59d7-49fc-bef6-17b717d2f5a8&resource=https%3A%2F%2Fmanagement.azure.com%2F ErrorInfo: Response:{"error":"invalid_client","error_description":"AADSTS7000215: Invalid client secret is provided.\r\nTrace ID: b179b7db-c957-4917-a1b6-66fab2042a00\r\nCorrelation ID: 4cfc9c81-660f-4a1a-ba0b-87db205c5461\r\nTimestamp: 2020-04-07 20:52:51Z","error_codes":[7000215],"timestamp":"2020-04-07 20:52:51Z","trace_id":"b179b7db-c957-4917-a1b6-66fab2042a00","correlation_id":"4cfc9c81-660f-4a1a-ba0b-87db205c5461","error_uri":"https://login.microsoftonline.com/error?code=7000215"} HTTPReturnCode:401
```

Chcete-li opravit neplatné přihlašovací údaje klienta, ověřte správnost client_id a tajného klíče:

```console
$ kubectl -n azure-arc get cm/azure-clusterconfig -o yaml
  AZURE_CLIENT_ID: 82195c37-7497-458c-b643-f4a3d0a64190
  AZURE_RESOURCE_GROUP: AzureArc
  AZURE_RESOURCE_NAME: AzureArcCluster
```

### <a name="expired-credentials"></a>Vypršení platnosti přihlašovacích údajů

Vypršení platnosti přihlašovacích údajů instančního objektu způsobí, že agent Connect zaznamená chybu `AADSTS7000222: The provided client secret keys are expired` .

```console
$ kubectl -n azure-arc logs -l app.kubernetes.io/component=connect-agent -c connect-agent
2020/04/13 19:49:19 Environment validation :success
2020/04/13 19:49:19 Kubernetes API server access validation :success
2020/04/13 19:49:19 Azure Subscription access token :error :http request failed. Authentication Token URL:https://login.microsoftonline.com/72f988bf-86f1-41af-91ab-2d7cd011db47/oauth2/token Authentication Token Body:grant_type=client_credentials&client_id=82195c37-7497-458c-b643-f4a3d0a64190&client_secret=9814c84e-59d7-49fc-bef6-17b717d2f5a8&resource=https%3A%2F%2Fmanagement.azure.com%2F ErrorInfo: Response:{"error":"invalid_client","error_description":"AADSTS7000222: The provided client secret keys are expired.\r\nTrace ID: 69ade0e5-f089-4a9d-b55d-9089e07f6300\r\nCorrelation ID: 10057011-6143-4e87-ad4a-c8256cf0e353\r\nTimestamp: 2020-04-13 19:49:19Z","error_codes":[7000222],"timestamp":"2020-04-13 19:49:19Z","trace_id":"69ade0e5-f089-4a9d-b55d-9089e07f6300","correlation_id":"10057011-6143-4e87-ad4a-c8256cf0e353"} HTTPReturnCode:401
```

Přihlašovací údaje vypršení platnosti je možné resetovat pomocí `az ad sp credential reset` .

## <a name="configuration-management"></a>Správa konfigurace

### <a name="general"></a>Obecné
Chcete-li pomoct řešit problémy s konfigurací správy zdrojového kódu, spusťte příkaz AZ Commands with--Debug.

```console
az provider show -n Microsoft.KubernetesConfiguration --debug
az k8sconfiguration create <parameters> --debug
```

### <a name="create-source-control-configuration"></a>Vytvořit konfiguraci správy zdrojového kódu
Role Přispěvatel v prostředku Microsoft. Kubernetes/connectedCluster je nezbytná a dostatečná pro vytvoření prostředku Microsoft. KubernetesConfiguration/sourceControlConfiguration.

### <a name="configuration-remains-pending"></a>Konfigurace zůstává`Pending`

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
