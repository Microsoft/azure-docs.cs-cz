---
title: Rozpoznávání jazyka Kubernetes konfigurace a postup nasazení
titleSuffix: Azure Cognitive Services
description: Rozpoznávání jazyka Kubernetes konfigurace a postup nasazení
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: 1edca9cf8449ff386d0a9920e7d80d69692536fd
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/25/2020
ms.locfileid: "96017839"
---
### <a name="deploy-the-language-detection-container-to-an-aks-cluster"></a>Nasazení kontejneru Rozpoznávání jazyka do clusteru AKS

1. Otevřete rozhraní příkazového řádku Azure a přihlaste se k Azure.

    ```azurecli
    az login
    ```

1. Přihlaste se ke clusteru AKS. Nahraďte `your-cluster-name` a `your-resource-group` odpovídajícími hodnotami.

    ```azurecli
    az aks get-credentials -n your-cluster-name -g -your-resource-group
    ```

    Po spuštění tohoto příkazu se nahlásí zpráva podobná následující:

    ```output
    Merged "your-cluster-name" as current context in /home/username/.kube/config
    ```

    > [!WARNING]
    > Pokud máte na svém účtu Azure k dispozici více předplatných a `az aks get-credentials` příkaz se vrátí k chybě, běžný problém je, že používáte nesprávné předplatné. Nastavte kontext relace Azure CLI tak, aby používal stejné předplatné, se kterým jste prostředky vytvořili, a zkuste to znovu.
    > ```azurecli
    >  az account set -s subscription-id
    > ```

1. Otevřete textový editor, který vyberete. Tento příklad používá Visual Studio Code.

    ```console
    code .
    ```

1. V textovém editoru vytvořte nový soubor s názvem *Language. yaml* a vložte do něj následující YAML. Nezapomeňte nahradit `billing/value` a `apikey/value` vlastními informacemi.

    ```yaml
    apiVersion: apps/v1beta1
    kind: Deployment
    metadata:
      name: language
    spec:
      template:
        metadata:
          labels:
            app: language-app
        spec:
          containers:
          - name: language
            image: mcr.microsoft.com/azure-cognitive-services/language
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
      name: language
    spec:
      type: LoadBalancer
      ports:
      - port: 5000
      selector:
        app: language-app
    ```

1. Uložte soubor a zavřete textový editor.
1. Spusťte příkaz Kubernetes `apply` se souborem *Language. yaml* jako jeho cíl:

    ```console
    kubectl apply -f language.yaml
    ```

    Poté, co příkaz úspěšně použije konfiguraci nasazení, se zobrazí zpráva podobná následujícímu výstupu:

    ```output
    deployment.apps "language" created
    service "language" created
    ```
1. Ověřte, že byla nasazena pod:

    ```console
    kubectl get pods
    ```

    Výstup pro stav spuštění pod:

    ```output
    NAME                         READY     STATUS    RESTARTS   AGE
    language-5c9ccdf575-mf6k5   1/1       Running   0          1m
    ```

1. Ověřte, zda je služba k dispozici a získejte IP adresu.

    ```console
    kubectl get services
    ```

    Výstup stavu spuštění *jazykové* služby v části pod:

    ```output
    NAME         TYPE           CLUSTER-IP    EXTERNAL-IP      PORT(S)          AGE
    kubernetes   ClusterIP      10.0.0.1      <none>           443/TCP          2m
    language     LoadBalancer   10.0.100.64   168.61.156.180   5000:31234/TCP   2m
    ```
