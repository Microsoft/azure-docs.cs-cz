---
title: Spuštění služby Azure Kubernetes – Analýza textu
titleSuffix: Azure Cognitive Services
description: Nasaďte kontejnery Analýza textu s imagí analýzy mínění do služby Azure Kubernetes a otestujte je ve webovém prohlížeči.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 06/21/2019
ms.author: dapine
ms.openlocfilehash: 44ee5fab5b4e8900b823453e5674fc9bdb5fe9ac
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/26/2019
ms.locfileid: "68552282"
---
# <a name="deploy-a-sentiment-analysis-container-to-azure-kubernetes-service"></a>Nasazení kontejneru analýzy mínění do služby Azure Kubernetes

Naučte se, jak nasadit kontejner Azure Cognitive Services [Analýza textu](https://docs.microsoft.com/azure/cognitive-services/text-analytics/how-tos/text-analytics-how-to-install-containers) s imagí analýzy mínění do služby Azure Kubernetes Service (AKS). Tento postup ukazuje, jak vytvořit prostředek Analýza textu, jak vytvořit přidružený obrázek analýzy mínění a jak tuto orchestraci těchto dvou způsobů vykonat z prohlížeče. Pomocí kontejnerů můžete posouvat pozornost od správy infrastruktury a místo toho se zaměřit na vývoj aplikací.

## <a name="prerequisites"></a>Požadavky

Tento postup vyžaduje několik nástrojů, které je třeba nainstalovat a spustit místně. Nepoužívejte Azure Cloud Shell. Potřebujete následující:

* Předplatné Azure. Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.
* Textový editor, například [Visual Studio Code](https://code.visualstudio.com/download).
* Rozhraní příkazového [řádku Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) je nainstalované.
* Rozhraní [CLI Kubernetes](https://kubernetes.io/docs/tasks/tools/install-kubectl/) je nainstalované.
* Prostředek Azure se správnou cenovou úrovní. Ne všechny cenové úrovně v tomto kontejneru fungují:
    * **Azure analýza textu** prostředky s cenovou úrovní F0 nebo Standard.
    * Prostředek **Azure Cognitive Services** s cenovou úrovní S0

[!INCLUDE [Create a Cognitive Services Text Analytics resource](../includes/create-text-analytics-resource.md)]

[!INCLUDE [Create a Text Analytics container on Azure Kubernetes Service (AKS)](../../containers/includes/create-aks-resource.md)]

## <a name="deploy-a-text-analytics-container-to-an-aks-cluster"></a>Nasazení kontejneru Analýza textu do clusteru AKS

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

1. V textovém editoru vytvořte nový soubor s názvem _mínění. yaml_a vložte do něj následující YAML. Nezapomeňte nahradit `billing/value` a `apikey/value` vlastními informacemi.

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
            env:
            - name: EULA
              value: "accept"
            - name: billing
              value: # < Your endpoint >
            - name: apikey
              value: # < Your API Key >
     
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
1. Spusťte příkaz Kubernetes `apply` s _mínění. yaml_ jako jeho cíl:

    ```console
    kuberctl apply -f sentiment.yaml
    ```

    Poté, co příkaz úspěšně použije konfiguraci nasazení, se zobrazí zpráva podobná následujícímu výstupu:

    ```
    deployment.apps "sentiment" created
    service "sentiment" created
    ```
1. Ověřte, že byla nasazena pod:

    ```console
    kubectl get pods
    ```

    Výstup pro stav spuštění pod:

    ```
    NAME                         READY     STATUS    RESTARTS   AGE
    sentiment-5c9ccdf575-mf6k5   1/1       Running   0          1m
    ```

1. Ověřte, zda je služba k dispozici a získejte IP adresu.

    ```console
    kubectl get services
    ```

    Výstup stavu spuštění služby _mínění_ v pod:

    ```
    NAME         TYPE           CLUSTER-IP    EXTERNAL-IP      PORT(S)          AGE
    kubernetes   ClusterIP      10.0.0.1      <none>           443/TCP          2m
    sentiment    LoadBalancer   10.0.100.64   168.61.156.180   5000:31234/TCP   2m
    ```

[!INCLUDE [Verify the sentiment analysis container instance](../includes/verify-sentiment-analysis-container.md)]

## <a name="next-steps"></a>Další postup

* Použít více [Cognitive Servicesch kontejnerů](../../cognitive-services-container-support.md)
* Použití [připojené služby analýza textu](../vs-text-connected-service.md)
