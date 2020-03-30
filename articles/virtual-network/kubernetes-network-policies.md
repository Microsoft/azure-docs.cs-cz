---
title: Zásady sítě Azure Kubernetes | Dokumenty společnosti Microsoft
description: Přečtěte si o zásadách sítě Kubernetes pro zabezpečení clusteru Kubernetes.
services: virtual-network
documentationcenter: na
author: aanandr
manager: NarayanAnnamalai
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 9/25/2018
ms.author: aanandr
ms.custom: ''
ms.openlocfilehash: 5a6da7e65a9a3e962a2df37b062792fbb990d04d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "73159696"
---
# <a name="azure-kubernetes-network-policies-overview"></a>Přehled zásad sítě Azure Kubernetes

Zásady sítě poskytují mikrosegmentaci pro pody, stejně jako skupiny zabezpečení sítě (NSG) poskytují mikrosegmentace pro virtuální počítač. Implementace zásad sítě Azure podporuje standardní specifikaci zásad sítě Kubernetes. Popisky můžete použít k výběru skupiny podů a definovat seznam příchozího a odchozího pravidla, která určují druh provozu, který je povolen a z těchto podů. Další informace o zásadách sítě Kubernetes naleznete v [dokumentaci kubernetes](https://kubernetes.io/docs/concepts/services-networking/network-policies/).

![Kubernetes síťové zásady přehled](./media/kubernetes-network-policies/kubernetes-network-policies-overview.png)

Zásady sítě Azure fungují ve spojení s Rozhraním Azure CNI, které poskytuje integraci virtuální sítě pro kontejnery. To je podporováno pouze na linuxových uzlech dnes. Implementace konfigurují pravidla linuxové tabulky IP na základě definovaných zásad pro vynucení filtrování provozu.

## <a name="planning-security-for-your-kubernetes-cluster"></a>Plánování zabezpečení clusteru Kubernetes
Při implementaci zabezpečení clusteru pomocí skupin zabezpečení sítě (NSG) můžete filtrovat provoz mezi severem a jihem, to znamená, že provoz y vstupující do podsítě clusteru a jejich opuštění a použití zásad sítě Kubernetes pro provoz na východ-západ, to znamená provoz mezi pody v clusteru.

## <a name="using-azure-kubernetes-network-policies"></a>Použití zásad sítě Azure Kubernetes
Zásady sítě Azure lze použít v následujících způsobech, jak poskytnout mikro segmentace pro pody.

### <a name="acs-engine"></a>ACS-motor
ACS-Engine je nástroj, který generuje šablonu Azure Resource Manager pro nasazení clusteru Kubernetes v Azure. Konfigurace clusteru se zadává v souboru JSON, který se předá nástroji při generování šablony. Podrobnosti k úplnému výčtu podporovaných nastavení clusteru včetně jejich popisu najdete v tématu Modul služby Microsoft Azure Container Service – definice clusteru.

Chcete-li povolit zásady v clusterech nasazených pomocí modulu acs, zadejte hodnotu nastavení networkPolicy v souboru definice clusteru jako "azure".

#### <a name="example-configuration"></a>Příklad konfigurace

Níže uvedená konfigurace příkladu JSON vytvoří novou virtuální síť a podsíť a nasadí v něm cluster Kubernetes s Azure CNI. Doporučujeme upravit soubor JSON pomocí poznámkového bloku. 
```json
{
  "apiVersion": "vlabs",
  "properties": {
    "orchestratorProfile": {
      "orchestratorType": "Kubernetes",
      "kubernetesConfig": {
         "networkPolicy": "azure"
       }
    },
    "masterProfile": {
      "count": 1,
      "dnsPrefix": "<specify a cluster name>",
      "vmSize": "Standard_D2s_v3"
    },
    "agentPoolProfiles": [
      {
        "name": "agentpool",
        "count": 2,
        "vmSize": "Standard_D2s_v3",
        "availabilityProfile": "AvailabilitySet"
      }
    ],
   "linuxProfile": {
      "adminUsername": "<specify admin username>",
      "ssh": {
        "publicKeys": [
          {
            "keyData": "<cut and paste your ssh key here>"
          }
        ]
      }
    },
    "servicePrincipalProfile": {
      "clientId": "<enter the client ID of your service principal here >",
      "secret": "<enter the password of your service principal here>"
    }
  }
}

```
### <a name="creating-your-own-kubernetes-cluster-in-azure"></a>Vytvoření vlastního clusteru Kubernetes v Azure
Implementaci lze použít k poskytování zásad sítě pro pody v clusterech Kubernetes, které sami nasadíte, aniž byste se spoléhali na nástroje, jako je modul ACS-Engine. V takovém případě nejprve nainstalujete modul plug-in CNI a povolíte jej na každém virtuálním počítači v clusteru. Podrobné pokyny najdete v tématu o [nasazení modulu plug-in v clusteru Kubernetes, který nasazujete sami](deploy-container-networking.md#deploy-plug-in-for-a-kubernetes-cluster).

Po nasazení clusteru spusťte následující `kubectl` příkaz ke stažení a použití *daemonsady* zásad sítě Azure v clusteru.

  ```
  kubectl apply -f https://raw.githubusercontent.com/Azure/acs-engine/master/parts/k8s/addons/kubernetesmasteraddons-azure-npm-daemonset.yaml

  ```
Řešení je také open source a kód je k dispozici v [úložišti Azure Container Networking](https://github.com/Azure/azure-container-networking/tree/master/npm).



## <a name="next-steps"></a>Další kroky
- Další informace o [službě Azure Kubernetes Service](../aks/intro-kubernetes.md).
-  Informace o [vytváření kontejnerů](container-networking-overview.md).
- [Nasaďte modul plug-in](deploy-container-networking.md) pro clustery Kubernetes nebo kontejnery Dockeru.
