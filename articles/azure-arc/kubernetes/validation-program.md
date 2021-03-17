---
title: Program ověřování Kubernetes s povoleným ARC Azure
services: azure-arc
ms.service: azure-arc
ms.date: 03/03/2021
ms.topic: article
author: shashankbarsin
ms.author: shasb
description: Popisuje program ověření ARC pro Kubernetes distribuce.
keywords: Kubernetes, oblouk, Azure, K8s, ověřování
ms.openlocfilehash: 819df906add6275997e01fab310fe8dd57a87b51
ms.sourcegitcommit: dac05f662ac353c1c7c5294399fca2a99b4f89c8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/04/2021
ms.locfileid: "102121367"
---
# <a name="azure-arc-validation-program"></a>Ověřovací program ARC Azure

Kubernetes s podporou Azure ARC funguje se všemi certifikovanými clustery Kubernetes CNCF (Cloud Native Computing Foundation). Tým Azure ARC také pracoval s klíčovými obory Kubernetes, kteří nabízejí poskytovatele pro ověření Kubernetes s povoleným rozšířením Azure ARC pomocí jejich Kubernetes distribucí. Budoucí hlavní a dílčí verze Kubernetes distribucí vydaných těmito poskytovateli budou ověřeny pro kompatibilitu s Kubernetesem povoleným pomocí ARC Azure.

## <a name="validated-distributions"></a>Ověřené distribuce

Následující Kubernetes distribuce a poskytovatelé infrastruktury úspěšně prošly testy shody pro Kubernetes s podporou ARC Azure:

| Distribuce a poskytovatel infrastruktury | Verze |
| ---------------------------------------- | ------- |
| Zprostředkovatel rozhraní API pro clustery v Azure            | Verze vydaných verzí: [0.4.12](https://github.com/kubernetes-sigs/cluster-api-provider-azure/releases/tag/v0.4.12); Verze Kubernetes: [1.18.2](https://github.com/kubernetes/kubernetes/releases/tag/v1.18.2) |
| AKS na Azure Stack HCI                   | Verze Release: [aktualizace z prosince 2020](https://github.com/Azure/aks-hci/releases/tag/AKS-HCI-2012); Verze Kubernetes: [1.18.8](https://github.com/kubernetes/kubernetes/releases/tag/v1.18.8) |

Následující poskytovatelé a jejich odpovídající distribuce Kubernetes úspěšně prošly testy shody pro Kubernetes s podporou ARC Azure:

| Název zprostředkovatele | Název distribuce | Verze |
| ------------ | ----------------- | ------- |
| RedHat       | [OpenShift Container Platform](https://www.openshift.com/products/container-platform) | [4,5](https://docs.openshift.com/container-platform/4.5/release_notes/ocp-4-5-release-notes.html), [4,6](https://docs.openshift.com/container-platform/4.6/release_notes/ocp-4-6-release-notes.html), [4,7](https://docs.openshift.com/container-platform/4.7/release_notes/ocp-4-7-release-notes.html) |
| VMware       | [Tanzu Kubernetes Grid](https://tanzu.vmware.com/kubernetes-grid) | Verze Kubernetes: v 1.17.5 |
| Canonical    | [Charmed Kubernetes](https://ubuntu.com/kubernetes) | [1,19](https://ubuntu.com/kubernetes/docs/1.19/components) |
| SUSE Rancher      | [Rancher Kubernetes Engine](https://rancher.com/products/rke/) | Verze rozhraní příkazového řádku RKE: [v 1.2.4](https://github.com/rancher/rke/releases/tag/v1.2.4); Verze Kubernetes: [1.19.6](https://github.com/kubernetes/kubernetes/releases/tag/v1.19.6)), [1.18.14](https://github.com/kubernetes/kubernetes/releases/tag/v1.18.14)), [1.17.16](https://github.com/kubernetes/kubernetes/releases/tag/v1.17.16))  |
| Nutanix      | [Karbon](https://www.nutanix.com/products/karbon)    | Verze 2.2.1 |

Tým ARC Azure taky spouštěl testy shody a Kubernetes scénáře s povoleným ARC Azure na následujících poskytovatelích veřejného cloudu:

| Název poskytovatele veřejného cloudu | Název distribuce | Verze |
| -------------------------- | ----------------- | ------- |
| Amazon Web Services        | Služba elastických Kubernetes (EKS) | v 1.18.9  |
| Google Cloud Platform      | Google Kubernetes Engine (GKE) | v 1.17.15 |

## <a name="scenarios-validated"></a>Schválené scénáře

Testy shody se spouštějí jako součást ověřování Kubernetes s povoleným doplněním Azure ARC v následujících scénářích:

1. Připojení clusterů Kubernetes ke službě Azure ARC: 
    * Nasaďte graf Helm agenta Kubernetes s povoleným ARC v clusteru.
    * V clusteru nastavte certifikát MSI (Managed System identity).
    * Agenti odesílají metadata clusteru do Azure.

2. Konfigurace: 
    * Vytvořte konfiguraci nad prostředkem Kubernetes s povoleným ARC Azure.
    * Na clusteru je nasazený [tok](https://docs.fluxcd.io/), který je potřeba pro nastavení pracovního postupu GitOps.
    * Tok získává manifesty a Helm grafy z ukázkového úložiště Git a nasadí je do clusteru.

## <a name="next-steps"></a>Další kroky

Naučte se připojit cluster ke službě Azure ARC.
> [!div class="nextstepaction"]
> [Připojit cluster ke službě Azure ARC](./quickstart-connect-cluster.md)
