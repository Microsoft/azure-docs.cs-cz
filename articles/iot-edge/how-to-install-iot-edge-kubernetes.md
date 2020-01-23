---
title: Jak nainstalovat IoT Edge v Kubernetes | Microsoft Docs
description: Naučte se, jak nainstalovat IoT Edge v Kubernetes pomocí prostředí místního vývojového clusteru.
author: kgremban
manager: philmea
ms.author: veyalla
ms.date: 04/26/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: d11d23cf7d96482028a9d3738196fc5a787fec91
ms.sourcegitcommit: 38b11501526a7997cfe1c7980d57e772b1f3169b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/22/2020
ms.locfileid: "76510205"
---
# <a name="how-to-install-iot-edge-on-kubernetes-preview"></a>Postup instalace IoT Edge v Kubernetes (Preview)

IoT Edge můžete integrovat s Kubernetes a používat ji jako odolnou, vysoce dostupnou infrastrukturu infrastruktury. Registruje IoT Edge *vlastní definice prostředků* (CRD) pomocí serveru Kubernetes API. Navíc poskytuje *operátor* (IoT Edge Agent), který slučuje požadovaný stav cloudového řízení s místním stavem clusteru.

Doba života modulu je spravovaná plánovačem Kubernetes, který udržuje dostupnost modulu a volí jejich umístění. IoT Edge spravuje platformu hraniční aplikace spuštěnou nahoře a nepřetržitě slučuje požadovaný stav zadaný v IoT Hub se stavem na hraničním clusteru. Model hraniční aplikace je stále známý model založený na IoT Edgech modulech a trasách. Operátor agenta IoT Edge provádí *Automatické* překlady konstrukcí Kubernetes Native, jako jsou lusky, nasazení, služby atd.

Tady je diagram architektury vysoké úrovně:

![Kubernetes arch](./media/how-to-install-iot-edge-kubernetes/k8s-arch.png)

Každá součást nasazení Edge je vymezená na obor názvů Kubernetes specifický pro zařízení, takže je možné sdílet stejné prostředky clusteru mezi více hraničními zařízeními a jejich nasazeními.

>[!NOTE]
>IoT Edge v Kubernetes je ve [verzi Public Preview](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="install-locally-for-a-quick-test-environment"></a>Místní instalace pro rychlé testovací prostředí

### <a name="prerequisites"></a>Požadavky

* Kubernetes 1,10 nebo novější. Pokud nemáte existující instalaci clusteru, můžete použít [Minikube](https://kubernetes.io/docs/setup/minikube/) pro místní Clusterové prostředí.

* [Helm](https://helm.sh/docs/using_helm/#quickstart-guide), správce balíčků Kubernetes.

* [kubectl](https://kubernetes.io/docs/tasks/tools/install-kubectl/) pro zobrazení a interakci s clusterem.

### <a name="setup-steps"></a>Postup nastavení

1. Spustit **Minikube**

    ``` shell
    minikube start
    ```

1. Inicializace součásti serveru **Helm** (*pokladny*) v clusteru

    ``` shell
    helm init
    ```

1. Přidání úložiště IoT Edge a aktualizace instalace Helm

    ``` shell
    helm repo add edgek8s https://edgek8s.blob.core.windows.net/helm/
    helm repo update
    ```

1. [Vytvořte IoT Hub](../iot-hub/iot-hub-create-through-portal.md), [zaregistrujte IoT Edge zařízení](how-to-register-device.md)a poznamenejte si jeho připojovací řetězec.

1. Instalace iotedged a agenta IoT Edge do clusteru

    ```shell
    helm install \
    --name k8s-edge1 \
    --set "deviceConnectionString=replace-with-device-connection-string" \
    edgek8s/edge-kubernetes
    ```

1. Otevření řídicího panelu Kubernetes v prohlížeči

    ```shell
    minikube dashboard
    ```

    V části obory názvů clusteru se zobrazí jedno pro IoT Edge zařízení podle konvence *msiot-\<iothub-name >-\<edgedevice-name >* . Agent IoT Edge iotedged a lusky by měly být v tomto oboru názvů v provozu.

1. Přidejte modul simulovaného senzoru teploty pomocí postupu v části [nasazení modulu](quickstart-linux.md#deploy-a-module) v rychlém startu. Správa modulů IoT Edge se provádí z portálu IoT Hub stejným způsobem jako jiné IoT Edge zařízení. Provádění místních změn v konfiguraci modulů prostřednictvím nástrojů Kubernetes se nedoporučuje, protože by se mohlo přepsat.

1. Během několika sekund se při aktualizaci stránky **lusky** pod oborem názvů hraničního zařízení na řídicím panelu zobrazí seznam IoT Edge hub a simulované IoT Hub IoT Edge snímače.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud chcete odebrat všechny prostředky vytvořené nasazením Edge, použijte následující příkaz s názvem použitým v kroku 5 předchozí části.

``` shell
helm delete --purge k8s-edge1
```

## <a name="next-steps"></a>Další kroky

### <a name="deploy-as-a-highly-available-edge-gateway"></a>Nasazení jako hraniční brány s vysokou dostupností

Hraniční zařízení v clusteru Kubernetes se dá použít jako brána IoT pro podřízená zařízení. Dá se nakonfigurovat tak, aby byl odolný vůči selhání uzlu a poskytoval tak vysokou dostupnost nasazení Edge. V tomto scénáři se dozvíte v tomto [podrobném návodu](https://github.com/Azure-Samples/iotedge-gateway-on-kubernetes) k použití IoT Edge.
