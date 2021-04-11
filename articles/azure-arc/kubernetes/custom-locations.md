---
title: Vlastní umístění s povoleným Kubernetesem Azure ARC
services: azure-arc
ms.service: azure-arc
ms.date: 04/05/2021
ms.topic: article
author: shashankbarsin
ms.author: shasb
ms.custom: references_regions
description: Použití vlastních umístění k nasazení služeb Azure PaaS v clusterech Kubernetes s povoleným ARC Azure
ms.openlocfilehash: ddda6420acd7126cb46b043f5c1bce67758342bc
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2021
ms.locfileid: "106450982"
---
# <a name="custom-locations-on-azure-arc-enabled-kubernetes"></a>Vlastní umístění s povoleným Kubernetesem Azure ARC

V rámci rozšíření umístění Azure poskytuje *vlastní umístění* způsob, jak můžou správci klientů používat clustery Kubernetes s povoleným rozšířením Azure jako cílová umístění pro nasazení instancí služeb Azure. Mezi příklady prostředků Azure patří spravovaná instance SQL ARC a s povoleným PostgreSQLm rozšířením Azure ARC.

Podobně jako u umístění Azure můžou koncoví uživatelé v rámci tenanta s přístupem k vlastním umístěním nasazovat prostředky, které používají soukromý výpočetní výkon své společnosti.

Koncepční přehled této funkce je k dispozici ve [vlastních umístěních – článek s povoleným Kubernetesem Azure ARC](conceptual-custom-locations.md) .

[!INCLUDE [preview features note](./includes/preview/preview-callout.md)]

## <a name="prerequisites"></a>Požadavky

- [Nainstalujte nebo upgradujte Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) na verzi >= 2.16.0.

- `connectedk8s` (verze >= 1.1.0), `k8s-extension` (verze >= 0.2.0) a `customlocation` (verze >= 0.1.0) rozšíření rozhraní příkazového řádku Azure CLI. Nainstalujte tato rozšíření Azure CLI spuštěním následujících příkazů:
  
    ```azurecli
    az extension add --name connectedk8s
    az extension add --name k8s-extension
    az extension add --name customlocation
    ```
    
    Pokud `connectedk8s` `k8s-extension` `customlocation` jsou rozšíření a již nainstalována, můžete je aktualizovat na nejnovější verzi pomocí následujícího příkazu:

    ```azurecli
    az extension update --name connectedk8s
    az extension update --name k8s-extension
    az extension update --name customlocation
    ```

- Registrace zprostředkovatele je dokončena pro `Microsoft.ExtendedLocation` .
    1. Zadejte následující příkazy:
    
    ```azurecli
    az provider register --namespace Microsoft.ExtendedLocation
    ```

    2. Monitorujte proces registrace. Registrace může trvat až 10 minut.
    
    ```azurecli
    az provider show -n Microsoft.ExtendedLocation -o table
    ```

>[!NOTE]
>**Podporované oblasti pro vlastní umístění:**
>* East US
>* West Europe

## <a name="enable-custom-locations-on-cluster"></a>Povolit vlastní umístění v clusteru

Pokud chcete tuto funkci povolit v clusteru, spusťte následující příkaz:

```console
az connectedk8s enable-features -n <clusterName> -g <resourceGroupName> --features cluster-connect custom-locations
```

> [!NOTE]
> 1. Funkce vlastních umístění závisí na funkci připojení clusteru. Proto musí být obě funkce povolené pro práci s vlastními umístěními.
> 2. `az connectedk8s enable-features` je nutné spustit v počítači, kde soubor odkazuje `kubeconfig` na cluster, ve kterém mají být funkce povoleny.

## <a name="create-custom-location"></a>Vytvořit vlastní umístění

1. Vytvořte cluster Kubernetes s povoleným ARC Azure.
    - Pokud jste ještě nepřipojili cluster, Využijte náš [rychlý Start](quickstart-connect-cluster.md).
    - [Upgradujte agenty](agent-upgrade.md#manually-upgrade-agents) na verzi >= 1.1.0.

1. Nasaďte rozšíření clusteru služby Azure, jehož instanci byste nakonec chtěli mít na svém vlastním umístění:

    ```azurecli
    az k8s-extension create --name <extensionInstanceName> --extension-type microsoft.arcdataservices --cluster-type connectedClusters -c <clusterName> -g <resourceGroupName> --scope cluster --release-namespace arc --config Microsoft.CustomLocation.ServiceAccount=sa-bootstrapper
    ```

    > [!NOTE]
    > Odchozí proxy server bez ověřování a odchozí proxy server s základním ověřováním podporuje rozšíření clusteru s povolenou Data Services. Odchozí proxy server, který očekává důvěryhodné certifikáty, se v tuto chvíli nepodporuje.

1. Získejte identifikátor Azure Resource Manager clusteru Kubernetes s povoleným obloukem Azure, na který odkazuje v pozdějších krocích `connectedClusterId` :

    ```azurecli
    az connectedk8s show -n <clusterName> -g <resourceGroupName>  --query id -o tsv
    ```

1. Získejte Azure Resource Manager identifikátor rozšíření clusteru nasazeného nad clusterem Kubernetes s povoleným ARC Azure, na který odkazuje v pozdějších krocích `extensionId` :

    ```azurecli
    az k8s-extension show --name <extensionInstanceName> --cluster-type connectedClusters -c <clusterName> -g <resourceGroupName>  --query id -o tsv
    ```

1. Vytvořte vlastní umístění odkazem na cluster Kubernetes s povoleným obloukem Azure a rozšířením:

    ```azurecli
    az customlocation create -n <customLocationName> -g <resourceGroupName> --namespace arc --host-resource-id <connectedClusterId> --cluster-extension-ids <extensionId>
    ```

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> Zabezpečené připojení ke clusteru pomocí [připojení clusteru](cluster-connect.md)