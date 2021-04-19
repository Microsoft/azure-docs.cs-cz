---
title: Požadavky | Režim přímého připojení
description: Požadavky na nasazení řadiče dat v režimu přímého připojení.
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
ms.date: 03/31/2021
ms.topic: overview
ms.openlocfilehash: 940c08dc56e8b0f2217e556da1af31b44ceeda0a
ms.sourcegitcommit: b0557848d0ad9b74bf293217862525d08fe0fc1d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/07/2021
ms.locfileid: "107716308"
---
# <a name="deploy-data-controller---direct-connect-mode-prerequisites"></a>Nasadit řadič dat – režim přímého připojení (požadované součásti)

Tento článek popisuje, jak připravit nasazení řadiče dat pro datové služby s podporou ARC Azure v režimu přímého připojení.

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

V souhrnu vysoké úrovně jsou součástí tyto požadavky:

1. Instalace nástrojů
1. Přidání rozšíření
1. Vytvoření instančního objektu a konfigurace rolí pro metriky
1. Připojení clusteru Kubernetes k Azure pomocí Kubernetes s povoleným ARC Azure

Po dokončení těchto požadavků můžete [nasadit řadič dat ARC Azure | Režim přímého připojení](deploy-data-controller-direct-mode.md).

Zbývající části tohoto článku identifikují požadavky.

## <a name="install-tools"></a>Instalace nástrojů

- Helm verze 3.3 + ([install](https://helm.sh/docs/intro/install/))
- Azure CLI ([instalovat](/sql/azdata/install/deploy-install-azdata))

## <a name="add-extensions-for-azure-cli"></a>Přidat rozšíření pro Azure CLI

Kromě toho jsou potřeba také následující AZ Extensions:
- Rozšíření Azure CLI `k8s-extension` (0.2.0)
- Azure CLI `customlocation` (0.1.0)

Ukázka `az` a její rozšíření CLI by byly:

```console
$ az version
{
  "azure-cli": "2.19.1",
  "azure-cli-core": "2.19.1",
  "azure-cli-telemetry": "1.0.6",
  "extensions": {
    "connectedk8s": "1.1.0",
    "customlocation": "0.1.0",
    "k8s-configuration": "1.0.0",
    "k8s-extension": "0.2.0"
  }
}
```

## <a name="create-service-principal-and-configure-roles-for-metrics"></a>Vytvoření instančního objektu a konfigurace rolí pro metriky

Postupujte podle kroků popsaných v článku [odeslání metrik](upload-metrics-and-logs-to-azure-monitor.md) a vytvořte instanční objekt a udělte rolím, jak je popsáno v článku. 

Pokud [nasadíte řadič dat ARC Azure](deploy-data-controller-direct-mode.md), budou se vyžadovat informace o ID SPN, TenantID a tajném klíči klienta. 

## <a name="connect-kubernetes-cluster-to-azure-using-azure-arc-enabled-kubernetes"></a>Připojení clusteru Kubernetes k Azure pomocí Kubernetes s povoleným ARC Azure

Pokud chcete dokončit tuto úlohu, postupujte podle kroků v části [připojení existujícího clusteru Kubernetes ke službě Azure ARC](../kubernetes/quickstart-connect-cluster.md).

## <a name="next-steps"></a>Další kroky

Po dokončení těchto požadavků můžete [nasadit řadič dat ARC Azure | Režim přímého připojení](deploy-data-controller-direct-mode.md).
