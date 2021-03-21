---
title: Nasazení Azure IoT Edgech úloh
services: azure-arc
ms.service: azure-arc
ms.date: 03/03/2021
ms.topic: article
author: mlearned
ms.author: mlearned
description: Nasazení Azure IoT Edgech úloh
keywords: Kubernetes, oblouk, Azure, K8s, Containers
ms.openlocfilehash: e77446170e5a6adac995394d66640fd183f453b8
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "102121724"
---
# <a name="deploy-azure-iot-edge-workloads"></a>Nasazení Azure IoT Edgech úloh

## <a name="overview"></a>Přehled

Azure ARC a Azure IoT Edge snadno doplňují možnosti ostatních. 

Azure ARC poskytuje mechanizmy pro obsluhy clusterů ke konfiguraci základních komponent clusteru a použití a prosazování zásad clusteru. 

Azure IoT Edge umožňuje operátorům aplikace vzdáleně nasazovat a spravovat zatížení ve velkém měřítku s praktickým ingestování v cloudu a obousměrnými komunikačními primitivy. 

Následující diagram znázorňuje vztah Azure ARC a Azure IoT Edge:

![Konfigurace pro IoT ARC](./media/edge-arc.png)

## <a name="pre-requisites"></a>Požadavky

* [Zaregistrujte IoT Edge zařízení](../../iot-edge/quickstart-linux.md#register-an-iot-edge-device) a [Nasaďte modul simulovaného senzoru teploty](../../iot-edge/quickstart-linux.md#deploy-a-module). Poznamenejte si připojovací řetězec zařízení pro *hodnoty. yaml* uvedené níže.

* Použijte [podporu IoT Edge pro Kubernetes](https://aka.ms/edgek8sdoc) k jejímu nasazení prostřednictvím operátoru toku služby Azure ARC.

* Stáhněte si soubor [*Values. yaml*](https://github.com/Azure/iotedge/blob/preview/iiot/kubernetes/charts/edge-kubernetes/values.yaml) pro IoT Edge graf Helm a nahraďte `deviceConnectionString` zástupný text na konci souboru připojovacím řetězcem, který jste si poznamenali dříve. Podle potřeby nastavte všechny podporované možnosti instalace grafu. Vytvořte obor názvů pro úlohu IoT Edge a vygenerujte v něm tajný klíč:

  ```
  $ kubectl create ns iotedge

  $ kubectl create secret generic dcs --from-file=fully-qualified-path-to-values.yaml --namespace iotedge
  ```

  Můžete ji také nastavit vzdáleně pomocí [příkladu konfigurace clusteru](./tutorial-use-gitops-connected-cluster.md).

## <a name="connect-a-cluster"></a>Připojení clusteru

`az` `connectedk8s` Připojení clusteru Kubernetes ke službě Azure ARC pomocí rozšíření Azure CLI:

  ```
  az connectedk8s connect --name AzureArcIotEdge --resource-group AzureArcTest
  ```

## <a name="create-a-configuration-for-iot-edge"></a>Vytvořit konfiguraci pro IoT Edge

[Ukázkové úložiště Git](https://github.com/veyalla/edgearc) odkazuje na graf IoT Edge Helm a odkazuje na tajný kód vytvořený v části požadavky.

Pomocí `az` rozšíření Azure CLI `k8s-configuration` vytvořte konfiguraci, která propojuje připojený cluster s úložištěm git:

  ```
  az k8s-configuration create --name iotedge --cluster-name AzureArcIotEdge --resource-group AzureArcTest --operator-instance-name iotedge --operator-namespace azure-arc-iot-edge --enable-helm-operator --helm-operator-chart-version 0.6.0 --helm-operator-chart-values "--set helm.versions=v3" --repository-url "git://github.com/veyalla/edgearc.git" --cluster-scoped
  ```

Během několika minut byste měli vidět moduly IoT Edge úloh nasazené do `iotedge` oboru názvů vašeho clusteru. 

Podívejte se na `SimulatedTemperatureSensor` protokoly pod v tomto oboru názvů, abyste viděli vygenerované ukázkové hodnoty. Zprávy doručené do služby IoT Hub můžete také sledovat pomocí [rozšíření Azure IoT Hub Toolkit pro Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit).

## <a name="cleanup"></a>Vyčištění

Odeberte konfiguraci pomocí:

```
az k8s-configuration delete -g AzureArcTest --cluster-name AzureArcIotEdge --name iotedge
```

## <a name="next-steps"></a>Další kroky

Naučte se, jak [pomocí Azure Policy řídit konfiguraci clusteru](./use-azure-policy.md).
