---
title: Nasazení Azure IoT Edgech úloh (Preview)
services: azure-arc
ms.service: azure-arc
ms.date: 05/19/2020
ms.topic: article
author: mlearned
ms.author: mlearned
description: Nasazení Azure IoT Edgech úloh
keywords: Kubernetes, oblouk, Azure, K8s, Containers
ms.openlocfilehash: bfaa43a03ddd98616b22fc3fc7b4dccb4c38f44c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "86103972"
---
# <a name="deploy-azure-iot-edge-workloads-preview"></a>Nasazení Azure IoT Edgech úloh (Preview)

## <a name="overview"></a>Přehled

Azure ARC a Azure IoT Edge společně doplňují možnosti. Azure ARC poskytuje mechanizmy pro operátory clusterů ke konfiguraci základních komponent clusteru a také k použití a prosazování zásad clusteru. A IoT Edge umožňuje operátorům aplikace vzdáleně nasazovat a spravovat zatížení ve velkém měřítku s praktickým ingestování v cloudu a obousměrnými komunikačními primitivy. Následující diagram znázorňuje toto:

![Konfigurace pro IoT ARC](./media/edge-arc.png)

## <a name="pre-requisites"></a>Požadavky

* [Zaregistrujte IoT Edge zařízení](../../iot-edge/quickstart-linux.md#register-an-iot-edge-device) a [Nasaďte modul simulovaného senzoru teploty](../../iot-edge/quickstart-linux.md#deploy-a-module). Nezapomeňte si poznamenat připojovací řetězec zařízení.

* Použijte [podporu IoT Edge pro Kubernetes](https://aka.ms/edgek8sdoc) k jejímu nasazení prostřednictvím operátoru toku služby Azure ARC.

* Stáhněte si soubor [**Values. yaml**](https://github.com/Azure/iotedge/blob/master/kubernetes/charts/edge-kubernetes/values.yaml) pro IoT Edge graf Helm a nahraďte zástupný text **deviceConnectionString** na konci souboru, který jste si poznamenali v kroku 1. V případě potřeby můžete nastavit jiné podporované možnosti instalace grafu. Vytvořte obor názvů pro úlohu IoT Edge a vytvořte v něm tajný klíč:

    ```
    $ kubectl create ns iotedge

    $ kubectl create secret generic dcs --from-file=fully-qualified-path-to-values.yaml --namespace iotedge
    ```

    Tuto možnost můžete také nastavit vzdáleně pomocí [konfiguračního příkladu clusteru](./use-gitops-connected-cluster.md).

## <a name="connect-a-cluster"></a>Připojení clusteru

K `az` `connectedk8s` připojení clusteru Kubernetes ke službě Azure ARC použijte rozšíření CLI:

  ```
  az connectedk8s connect --name AzureArcIotEdge --resource-group AzureArcTest
  ```

## <a name="create-a-configuration-for-iot-edge"></a>Vytvořit konfiguraci pro IoT Edge

Příklad úložiště: https://github.com/veyalla/edgearc

Toto úložiště ukazuje na graf IoT Edge Helm a odkazuje na tajný kód vytvořený v části požadavky.

1. Pomocí `az` rozšíření CLI `k8sconfiguration` vytvořte konfiguraci pro propojení připojeného clusteru s úložištěm git:

    ```
    az k8sconfiguration create --name iotedge --cluster-name AzureArcIotEdge --resource-group AzureArcTest --operator-instance-name iotedge --operator-namespace azure-arc-iot-edge --enable-helm-operator --helm-operator-chart-version 0.6.0 --helm-operator-chart-values "--set helm.versions=v3" --repository-url "git://github.com/veyalla/edgearc.git" --cluster-scoped
    ```

    Za minutu nebo dva by se měly zobrazit moduly IoT Edge úloh nasazené do `iotedge` oboru názvů v clusteru. Můžete zobrazit protokoly `SimulatedTemperatureSensor` pod v tomto oboru názvů a zobrazit tak vygenerované ukázkové hodnoty. Zprávy doručené do služby IoT Hub můžete také sledovat pomocí [rozšíření Azure IoT Hub Toolkit pro Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit).

## <a name="cleanup"></a>Vyčištění

Konfiguraci můžete odebrat pomocí:

```
az k8sconfiguration delete -g AzureArcTest --cluster-name AzureArcIotEdge --name iotedge
```

## <a name="next-steps"></a>Další kroky

[Řízení konfigurace clusteru pomocí Azure Policy](./use-azure-policy.md)
