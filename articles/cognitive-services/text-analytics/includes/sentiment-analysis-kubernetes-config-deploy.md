---
title: Analýza mínění Kubernetes konfigurace a nasazení kroky
titleSuffix: Azure Cognitive Services
description: Analýza mínění Kubernetes konfigurace a nasazení kroky
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 11/21/2019
ms.author: dapine
ms.openlocfilehash: 2a99f85cf861c0c36ffac136cdf1f792b40719b2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78262105"
---
### <a name="deploy-the-sentiment-analysis-container-to-an-aks-cluster"></a>Nasazení kontejneru analýzy mínění do clusteru AKS

1. Otevřete azure cli a přihlaste se k Azure.

    ```azurecli
    az login
    ```

1. Přihlaste se ke clusteru AKS. Nahraďte `your-cluster-name` a `your-resource-group` zaměřte příslušné hodnoty.

    ```azurecli
    az aks get-credentials -n your-cluster-name -g -your-resource-group
    ```

    Po spuštění tohoto příkazu hlásí zprávu podobnou následující:

    ```console
    Merged "your-cluster-name" as current context in /home/username/.kube/config
    ```

    > [!WARNING]
    > Pokud máte k dispozici více předplatných na `az aks get-credentials` vašem účtu Azure a příkaz se vrátí s chybou, běžný problém je, že používáte nesprávné předplatné. Nastavte kontext relace azure cli použít stejné předplatné, které jste vytvořili prostředky s a zkuste to znovu.
    > ```azurecli
    >  az account set -s subscription-id
    > ```

1. Otevřete textový editor podle výběru. Tento příklad používá visual studio kód.

    ```console
    code .
    ```

1. V textovém editoru vytvořte nový soubor s názvem *sentiment.yaml*a vložte do něj následující YAML. Nezapomeňte nahradit `billing/value` a `apikey/value` s vlastními informacemi.

    ```yaml
    apiVersion: apps/v1beta1
    kind: Deployment
    metadata:
      name: sentiment
    spec:
      template:
        metadata:
          labels:
            app: sentiment-app
        spec:
          containers:
          - name: sentiment
            image: mcr.microsoft.com/azure-cognitive-services/sentiment
            ports:
            - containerPort: 5000
            resources:
              requests:
                memory: 2Gi
                cpu: 1
              limits:
                memory: 4Gi
                cpu: 1
            env:
            - name: EULA
              value: "accept"
            - name: billing
              value: # {ENDPOINT_URI}
            - name: apikey
              value: # {API_KEY}
     
    --- 
    apiVersion: v1
    kind: Service
    metadata:
      name: sentiment
    spec:
      type: LoadBalancer
      ports:
      - port: 5000
      selector:
        app: sentiment-app
    ```

1. Uložte soubor a zavřete textový editor.
1. Spusťte příkaz Kubernetes `apply` s cílem souboru *sentiment.yaml:*

    ```console
    kubectl apply -f sentiment.yaml
    ```

    Po úspěšném použití konfigurace nasazení se zobrazí zpráva podobná následujícímu výstupu:

    ```output
    deployment.apps "sentiment" created
    service "sentiment" created
    ```
1. Ověřte, zda byl pod nasazen:

    ```console
    kubectl get pods
    ```

    Výstup pro provozní stav podu:

    ```output
    NAME                         READY     STATUS    RESTARTS   AGE
    sentiment-5c9ccdf575-mf6k5   1/1       Running   0          1m
    ```

1. Ověřte, zda je služba k dispozici, a získejte adresu IP.

    ```console
    kubectl get services
    ```

    Výstup pro provozní stav služby *mínění* v podu:

    ```output
    NAME         TYPE           CLUSTER-IP    EXTERNAL-IP      PORT(S)          AGE
    kubernetes   ClusterIP      10.0.0.1      <none>           443/TCP          2m
    sentiment    LoadBalancer   10.0.100.64   168.61.156.180   5000:31234/TCP   2m
    ```
