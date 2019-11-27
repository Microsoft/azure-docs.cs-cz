---
title: Analýza mínění Kubernetes konfigurace a postup nasazení
titleSuffix: Azure Cognitive Services
description: Analýza mínění Kubernetes konfigurace a postup nasazení
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 11/21/2019
ms.author: dapine
ms.openlocfilehash: bd93773e4d3c5e06bca752612dac6c563a2f5da1
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/22/2019
ms.locfileid: "74383415"
---
### <a name="deploy-the-sentiment-analysis-container-to-an-aks-cluster"></a>Nasazení kontejneru Analýza mínění do clusteru AKS

1. Otevřete rozhraní příkazového řádku Azure a přihlaste se k Azure.

    ```azurecli
    az login
    ```

1. Přihlaste se ke clusteru AKS. Nahraďte `your-cluster-name` a `your-resource-group` příslušnými hodnotami.

    ```azurecli
    az aks get-credentials -n your-cluster-name -g -your-resource-group
    ```

    Po spuštění tohoto příkazu se nahlásí zpráva podobná následující:

    ```console
    Merged "your-cluster-name" as current context in /home/username/.kube/config
    ```

    > [!WARNING]
    > Pokud máte k dispozici více předplatných na vašem účtu Azure a příkaz `az aks get-credentials` se vrátí k chybě, běžný problém je, že používáte nesprávné předplatné. Nastavte kontext relace Azure CLI tak, aby používal stejné předplatné, se kterým jste prostředky vytvořili, a zkuste to znovu.
    > ```azurecli
    >  az account set -s subscription-id
    > ```

1. Otevřete textový editor, který vyberete. Tento příklad používá Visual Studio Code.

    ```azurecli
    code .
    ```

1. V textovém editoru vytvořte nový soubor s názvem *mínění. yaml*a vložte do něj následující YAML. Nezapomeňte nahradit `billing/value` a `apikey/value` vlastními informacemi.

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
1. Jako cíl spusťte příkaz Kubernetes `apply` se souborem *mínění. yaml* :

    ```console
    kubectl apply -f sentiment.yaml
    ```

    Poté, co příkaz úspěšně použije konfiguraci nasazení, se zobrazí zpráva podobná následujícímu výstupu:

    ```console
    deployment.apps "sentiment" created
    service "sentiment" created
    ```
1. Ověřte, že byla nasazena pod:

    ```console
    kubectl get pods
    ```

    Výstup pro stav spuštění pod:

    ```console
    NAME                         READY     STATUS    RESTARTS   AGE
    sentiment-5c9ccdf575-mf6k5   1/1       Running   0          1m
    ```

1. Ověřte, zda je služba k dispozici a získejte IP adresu.

    ```console
    kubectl get services
    ```

    Výstup stavu spuštění služby *mínění* v pod:

    ```console
    NAME         TYPE           CLUSTER-IP    EXTERNAL-IP      PORT(S)          AGE
    kubernetes   ClusterIP      10.0.0.1      <none>           443/TCP          2m
    sentiment    LoadBalancer   10.0.100.64   168.61.156.180   5000:31234/TCP   2m
    ```
