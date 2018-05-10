---
title: Získávání protokolů, kubelet z Azure Kubernetes služby (AKS)
description: Získání protokolů kubelet z uzlů clusteru Azure Kubernetes služby (AKS)
services: container-service
author: neilpeterson
manager: timlt
ms.service: container-service
ms.topic: article
ms.date: 03/08/2018
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: 0467be7e91fdbf4685fc41a375ea86a503e26009
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/07/2018
---
# <a name="get-kubelet-logs-from-azure-kubernetes-service-aks-cluster-nodes"></a>Získání protokolů kubelet z uzlů clusteru Azure Kubernetes služby (AKS)

V některých případech může potřebujete získat kubelet protokoly z uzlu Azure Kubernetes služby (AKS) pro účely odstraňování potíží. Tento dokument podrobně popisuje jednou z možností pro stahování tyto protokoly.

## <a name="create-an-ssh-connection"></a>Vytvoření připojení SSH

Nejprve vytvořte připojení SSH s uzlem, na kterém budete muset kubelet protokoly pro vyžádání obsahu. Tato operace je podrobně popsaná v [SSH do uzlů clusteru Azure Kubernetes služby (AKS)] [ aks-ssh] dokumentu.

## <a name="get-kubelet-logs"></a>Získání protokolů kubelet

Po připojení k uzlu, spusťte následující příkaz, který kubelet protokoly pro vyžádání obsahu.

```azurecli-interactive
docker logs $(docker ps -a | grep "hyperkube kubele" | awk -F ' ' '{print $1}')
```

Ukázkový výstup:

```console
2018-03-05 00:04:00.883195 I | proto: duplicate proto type registered: google.protobuf.Any
2018-03-05 00:04:00.883242 I | proto: duplicate proto type registered: google.protobuf.Duration
2018-03-05 00:04:00.883253 I | proto: duplicate proto type registered: google.protobuf.Timestamp
Flag --non-masquerade-cidr has been deprecated, will be removed in a future version
Flag --non-masquerade-cidr has been deprecated, will be removed in a future version
I0305 00:04:00.978560    8021 feature_gate.go:144] feature gates: map[Accelerators:true]
I0305 00:04:00.978996    8021 azure.go:174] azure: using client_id+client_secret to retrieve access token
I0305 00:04:00.979041    8021 server.go:439] Successfully initialized cloud provider: "azure" from the config file: "/etc/kubernetes/azure.json"
I0305 00:04:00.979058    8021 server.go:740] cloud provider determined current node name to be aks-nodepool1-42032720-0
```

<!-- LINKS - internal -->
[aks-ssh]: aks-ssh.md