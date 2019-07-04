---
title: Spuštění služby Azure Kubernetes
titleSuffix: Text Analytics - Azure Cognitive Services
description: Nasazení kontejnerů text analytics Image analýzy mínění, do služby Azure Kubernetes a otestujte ji ve webovém prohlížeči.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 06/21/2019
ms.author: dapine
ms.openlocfilehash: a419ed3b9c0d2c4db9c552642dc5c662786f6730
ms.sourcegitcommit: d3b1f89edceb9bff1870f562bc2c2fd52636fc21
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/04/2019
ms.locfileid: "67561257"
---
# <a name="deploy-a-sentiment-analysis-container-to-azure-kubernetes-services-aks"></a>Nasazení kontejneru analýza mínění do služby Azure Kubernetes (AKS)

Informace o nasazení služeb Cognitive Services [rozhraní Text Analytics](https://docs.microsoft.com/azure/cognitive-services/text-analytics/how-tos/text-analytics-how-to-install-containers) kontejneru analýzu subjektivního hodnocení Image do služby Azure Kubernetes (AKS). Tento postup exemplifies vytvoření prostředku pro analýzu textu, vytváření přidružené image analýzu subjektivního hodnocení a schopnost vykonávat tuto orchestraci dvě z prohlížeče. Pomocí kontejnerů můžete posunout vývojářů pozornost od správy infrastruktury na místo toho soustředit na vývoj aplikací.

## <a name="prerequisites"></a>Požadavky

Tento postup vyžaduje několik nástrojů, které musí být nainstalován a spuštěn místně. Nepoužívejte Azure Cloud shell.

* Použijte předplatné Azure. Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.
* Textový editor, například: [Visual Studio Code](https://code.visualstudio.com/download).
* Nainstalujte [rozhraní příkazového řádku Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).
* Nainstalujte [příkazového řádku Kubernetes](https://kubernetes.io/docs/tasks/tools/install-kubectl/).
* Prostředek Azure s správné cenovou úroveň. Ne všechny cenové úrovně pracovat s tímto kontejnerem:
    * **Rozhraní text Analytics** pouze úrovní prostředků, které F0 nebo standardní ceny.
    * **Služby cognitive Services** cenová úroveň prostředku s S0.

[!INCLUDE [Create a Cognitive Services Text Analytics resource](../includes/create-text-analytics-resource.md)]

[!INCLUDE [Create a Text Analytics Containers on Azure Kubernetes Services (AKS)](../../containers/includes/create-aks-resource.md)]

## <a name="deploy-text-analytics-container-to-an-aks-cluster"></a>Nasazení kontejneru Text Analytics pro AKS Cluster

1. Otevřete rozhraní příkazového řádku Azure a přihlaste se do Azure

    ```azurecli
    az login
    ```

1. Přihlaste se ke clusteru AKS (nahradit `your-cluster-name` a `your-resource-group` příslušnými hodnotami)

    ```azurecli
    az aks get-credentials -n your-cluster-name -g -your-resource-group
    ```

    Po spuštění tohoto příkazu vypíše zpráva podobná následující:

    ```console
    Merged "your-cluster-name" as current context in /home/username/.kube/config
    ```

    > [!WARNING]
    > Pokud máte více předplatných, které jsou k dispozici v účtu Azure a `az aks get-credentials` příkaz vrátí chybu, je běžný problém, že používáte nesprávné předplatné. Stačí nastavit kontext relaci rozhraní příkazového řádku Azure k používání stejného předplatného, který jste vytvořili prostředky s a zkuste to znovu.
    > ```azurecli
    >  az account set -s subscription-id
    > ```

1. Otevřete textovém editoru, (v tomto příkladu __Visual Studio Code__):

    ```azurecli
    code .
    ```

1. V textovém editoru vytvořte nový soubor s názvem _sentiment.yaml_ a vložte do něj následující kód YAML. Nezapomeňte nahradit `billing/value` a `apikey/value` vlastními.

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

1. Soubor uložte a zavřete textového editoru.
1. Spuštění rozhraní Kubernetes `apply` příkazů _sentiment.yaml_ jako svůj cíl:

    ```console
    kuberctl apply -f sentiment.yaml
    ```

    Po příkazu úspěšně použil konfigurace nasazení, zpráva podobná následující výstup:

    ```
    deployment.apps "sentiment" created
    service "sentiment" created
    ```
1. Ověřte, že byla nasazena POD:

    ```console
    kubectl get pods
    ```

    To bude výstupní stav spuštění POD:

    ```
    NAME                         READY     STATUS    RESTARTS   AGE
    sentiment-5c9ccdf575-mf6k5   1/1       Running   0          1m
    ```

1. Ověřte, zda je služba dostupná a získejte IP adresu:

    ```console
    kubectl get services
    ```

    To bude výstupní stav spuštění _mínění_ služby POD:

    ```
    NAME         TYPE           CLUSTER-IP    EXTERNAL-IP      PORT(S)          AGE
    kubernetes   ClusterIP      10.0.0.1      <none>           443/TCP          2m
    sentiment    LoadBalancer   10.0.100.64   168.61.156.180   5000:31234/TCP   2m
    ```

[!INCLUDE [Verify the Sentiment Analysis container instance](../includes/verify-sentiment-analysis-container.md)]

## <a name="next-steps"></a>Další postup

* Použití více [kontejnery Cognitive Services](../../cognitive-services-container-support.md)
* Použití [rozhraní Text Analytics připojená služba](../vs-text-connected-service.md)
