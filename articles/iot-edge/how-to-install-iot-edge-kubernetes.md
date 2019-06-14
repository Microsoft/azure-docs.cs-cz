---
title: Jak nainstalovat IoT Edge na Kubernetes | Dokumentace Microsoftu
description: Další informace o tom, jak nainstalovat pomocí prostředí místního vývojového clusteru Kubernetes IoT Edge
author: kgremban
manager: philmea
ms.author: veyalla
ms.date: 04/26/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: 66aca7be9a2df93d846d7e78bc64c93279afc2d1
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "65160692"
---
# <a name="how-to-install-iot-edge-on-kubernetes-preview"></a>Postup instalace IoT Edge v Kubernetes (Preview)

IoT Edge můžete integrovat s využitím Kubernetes s využitím jako vrstvy odolné a vysoce dostupné infrastruktuře. Zaregistruje IoT Edge *vlastní definice prostředků* (CRD) na serveru Kubernetes API. Kromě toho poskytuje *operátor* (agenta IoT Edge), která sloučí cloudově spravovaného požadovaného stavu se stavem místního clusteru. 

Životnost modulu se spravuje přes Plánovač Kubernetes, který udržuje modul dostupnosti a zvolí jejich umístění. IoT Edge spravuje hraniční platformou aplikace běžící na horní, nepřetržitě sjednocování požadovaného stavu zadané ve službě IoT Hub se stavem v clusteru edge. Aplikační model edge je stále známý model na základě moduly IoT Edge a trasy. Operátor agenta IoT Edge provádí *automatické* překlad Kubernetes natives vytvoří jako podů, nasazení, služby atd.

Zde je diagram architektury vysoké úrovně:

![kubernetes arch](./media/how-to-install-iot-edge-kubernetes/k8s-arch.png)

Všechny komponenty nasazení edge působí na obor názvů Kubernetes, která je specifická pro zařízení, což umožňuje sdílení stejných prostředků clusteru mezi více hraničními zařízeními a jejich nasazení.

>[!NOTE]
>IoT Edge na Kubernetes je v [ve verzi public preview](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="install-locally-for-a-quick-test-environment"></a>Nainstalujte místně pro rychlé testovací prostředí

### <a name="prerequisites"></a>Požadavky

* Kubernetes 1.10 nebo novější. Pokud nemáte stávající nastavení clusteru, můžete použít [Minikube](https://kubernetes.io/docs/setup/minikube/) pro prostředí místního clusteru. 

* [Příkaz Helm](https://helm.sh/docs/using_helm/#quickstart-guide), Správce balíčků Kubernetes.

* [kubectl](https://kubernetes.io/docs/tasks/tools/install-kubectl/) pro zobrazení a interakci s clusterem.

### <a name="setup-steps"></a>Postup instalace

1. Spustit **Minikube**

    ``` shell
    minikube start
    ```

1. Inicializovat **Helm** součást serveru (*tiller*) ve vašem clusteru

    ``` shell
    helm init
    ```

1. Přidání úložiště IoT Edge a helm instalace aktualizace

    ``` shell
    helm repo add edgek8s https://edgek8s.blob.core.windows.net/helm/
    helm repo update
    ```

1. [Vytvoření služby IoT Hub](../iot-hub/iot-hub-create-through-portal.md), [zaregistrovat zařízení IoT Edge](how-to-register-device-portal.md)a poznamenejte si jeho připojovací řetězec.

1. Instalace agenta IoT Edge a iotedged do vašeho clusteru

    ```shell
    helm install \
    --name k8s-edge1 \
    --set "deviceConnectionString=replace-with-device-connection-string" \
    edgek8s/edge-kubernetes
    ```
1. V prohlížeči otevřete řídicí panel Kubernetes

    ```shell
    minikube dashboard
    ```

    V části obory názvů clusteru se zobrazí jeden pro zařízení IoT Edge, které jsou podle úmluvy *msiot -\<iothub-name >-\<edgedevice-name >* . IoT Edge agenta a iotedged podů měli pracovat v tomto oboru názvů.

1. Přidat modul simulované teplotní snímač pomocí postupu v [nasazení modulu](quickstart-linux.md#deploy-a-module) části tohoto rychlého startu. Správa modul IoT Edge se provádí na portálu služby IoT Hub, stejně jako ostatní zařízení IoT Edge. Místní změny konfigurace modulu prostřednictvím nástrojů pro Kubernetes se nedoporučuje, protože se může přepsat.

1. Během několika sekund, aktualizace **Podů** stránce pod oborem názvů hraniční zařízení na řídicím panelu se zobrazí seznam Centrum IoT Edge a podů simulovaných senzorů jako běží prostřednictvím centra IoT Edge pod ingestuje data do služby IoT Hub.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud chcete odebrat všechny prostředky vytvořené v rámci nasazení edge, použijte následující příkaz s názvem použili v kroku 5 v předchozí části.

``` shell
helm delete --purge k8s-edge1
```

## <a name="next-steps"></a>Další postup

### <a name="deploy-as-a-highly-available-edge-gateway"></a>Nasadit jako brána s vysokou dostupností 

Hraniční zařízení v clusteru Kubernetes můžete použít jako brány IoT pro příjem dat zařízení. Můžete ji nakonfigurovat, pokud chcete být odolní vůči selhání uzlu tím zajišťuje vysokou dostupnost pro nasazení edge. Najdete v tomto [podrobný návod](https://github.com/Azure-Samples/iotedge-gateway-on-kubernetes) pro použití v tomto scénáři IoT Edge.