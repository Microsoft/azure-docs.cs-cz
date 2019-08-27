---
title: Extrakce klíčových frází Kubernetes konfigurace a postup nasazení
titleSuffix: Azure Cognitive Services
description: Extrakce klíčových frází Kubernetes konfigurace a postup nasazení
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 08/21/2019
ms.author: dapine
ms.openlocfilehash: e29aec66d9986b509a5133f5ebe8a99a00f7b9cf
ms.sourcegitcommit: bba811bd615077dc0610c7435e4513b184fbed19
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/27/2019
ms.locfileid: "70051862"
---
## <a name="deploy-the-key-phrase-extraction-container-to-an-aks-cluster"></a>Nasazení kontejneru Extrakce klíčových frází do clusteru AKS

1. Otevřete rozhraní příkazového řádku Azure a přihlaste se k Azure.

    ```azurecli
    az login
    ```

1. Přihlaste se ke clusteru AKS. `your-cluster-name` Nahraďte `your-resource-group` a odpovídajícími hodnotami.

    ```azurecli
    az aks get-credentials -n your-cluster-name -g -your-resource-group
    ```

    Po spuštění tohoto příkazu se nahlásí zpráva podobná následující:

    ```console
    Merged "your-cluster-name" as current context in /home/username/.kube/config
    ```

    > [!WARNING]
    > Pokud máte na svém účtu Azure k dispozici více předplatných a `az aks get-credentials` příkaz se vrátí k chybě, běžný problém je, že používáte nesprávné předplatné. Nastavte kontext relace Azure CLI tak, aby používal stejné předplatné, se kterým jste prostředky vytvořili, a zkuste to znovu.
    > ```azurecli
    >  az account set -s subscription-id
    > ```

1. Otevřete textový editor, který vyberete. Tento příklad používá Visual Studio Code.

    ```azurecli
    code .
    ```

1. V textovém editoru vytvořte nový soubor s názvem *keyphrase. yaml*a vložte do něj následující YAML. Nezapomeňte nahradit `billing/value` a `apikey/value` vlastními informacemi.

    ```yaml
    apiVersion: apps/v1beta1
    kind: Deployment
    metadata:
      name: keyphrase
    spec:
      template:
        metadata:
          labels:
            app: keyphrase-app
        spec:
          containers:
          - name: keyphrase
            image: mcr.microsoft.com/azure-cognitive-services/keyphrase
            ports:
            - containerPort: 5000
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
      name: keyphrase
    spec:
      type: LoadBalancer
      ports:
      - port: 5000
      selector:
        app: keyphrase-app
    ```

1. Uložte soubor a zavřete textový editor.
1. Spusťte příkaz Kubernetes `apply` se souborem *keyphrase. yaml* jako jeho cíl:

    ```console
    kuberctl apply -f keyphrase.yaml
    ```

    Poté, co příkaz úspěšně použije konfiguraci nasazení, se zobrazí zpráva podobná následujícímu výstupu:

    ```console
    deployment.apps "keyphrase" created
    service "keyphrase" created
    ```
1. Ověřte, že byla nasazena pod:

    ```console
    kubectl get pods
    ```

    Výstup pro stav spuštění pod:

    ```console
    NAME                         READY     STATUS    RESTARTS   AGE
    keyphrase-5c9ccdf575-mf6k5   1/1       Running   0          1m
    ```

1. Ověřte, zda je služba k dispozici a získejte IP adresu.

    ```console
    kubectl get services
    ```

    Výstup stavu spuštění služby *mínění* v pod:

    ```console
    NAME         TYPE           CLUSTER-IP    EXTERNAL-IP      PORT(S)          AGE
    kubernetes   ClusterIP      10.0.0.1      <none>           443/TCP          2m
    keyphrase    LoadBalancer   10.0.100.64   168.61.156.180   5000:31234/TCP   2m
    ```
