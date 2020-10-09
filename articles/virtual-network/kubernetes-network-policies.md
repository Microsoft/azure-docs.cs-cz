---
title: Zásady sítě Azure Kubernetes | Microsoft Docs
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
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "73159696"
---
# <a name="azure-kubernetes-network-policies-overview"></a>Přehled zásad sítě Azure Kubernetes

Zásady sítě poskytují mikrosegmentaci pro lusky stejně jako skupiny zabezpečení sítě (skupin zabezpečení sítě) poskytují mikrosegmentaci pro virtuální počítače. Implementace zásad sítě Azure podporuje standardní specifikace zásad sítě Kubernetes. Pomocí popisků můžete vybrat skupinu lusků a definovat seznam pravidel příchozího a odchozího přenosu, který určuje druh provozu, který je povolený a z těchto lusků. Další informace o zásadách sítě Kubernetes najdete v [dokumentaci k Kubernetes](https://kubernetes.io/docs/concepts/services-networking/network-policies/).

![Přehled zásad sítě Kubernetes](./media/kubernetes-network-policies/kubernetes-network-policies-overview.png)

Zásady sítě Azure fungují ve spojení se službou Azure CNI, která poskytuje integraci virtuální sítě pro kontejnery. V současné době se podporuje jenom na uzlech se systémem Linux. Implementace konfigurují pravidla tabulky IP pro Linux na základě definovaných zásad pro vymáhání filtrování provozu.

## <a name="planning-security-for-your-kubernetes-cluster"></a>Plánování zabezpečení pro cluster Kubernetes
Při implementaci zabezpečení pro váš cluster použijte skupiny zabezpečení sítě (skupin zabezpečení sítě) k filtrování North-South provozu, to znamená provoz, který vstupuje do provozu a opustí podsíť clusteru, a použijte zásady sítě Kubernetes pro provoz East-West, to znamená provoz mezi lusky v clusteru.

## <a name="using-azure-kubernetes-network-policies"></a>Použití zásad sítě Azure Kubernetes
Zásady sítě Azure je možné použít v následujících způsobech, jak poskytnout mikrosegmentaci pro lusky.

### <a name="acs-engine"></a>ACS – modul
ACS-Engine je nástroj, který generuje šablonu Azure Resource Manager pro nasazení clusteru Kubernetes v Azure. Konfigurace clusteru se zadává v souboru JSON, který se předá nástroji při generování šablony. Podrobnosti k úplnému výčtu podporovaných nastavení clusteru včetně jejich popisu najdete v tématu Modul služby Microsoft Azure Container Service – definice clusteru.

Pokud chcete povolit zásady pro clustery nasazené pomocí služby ACS-Engine, zadejte hodnotu nastavení networkPolicy v definičním souboru clusteru tak, aby byla "Azure".

#### <a name="example-configuration"></a>Příklad konfigurace

Následující příklad konfigurace JSON vytvoří novou virtuální síť a podsíť a nasadí do ní cluster Kubernetes s využitím Azure CNI. Pro úpravu souboru JSON doporučujeme použít Poznámkový blok. 
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
Implementace se dá použít k poskytování zásad sítě pro lusky v clusterech Kubernetes, které nasadíte sami, aniž byste se museli spoléhat na nástroje, jako je služba ACS-Engine. V takovém případě nejdřív nainstalujete modul plug-in CNI a povolíte ho na každém virtuálním počítači v clusteru. Podrobné pokyny najdete v tématu o [nasazení modulu plug-in v clusteru Kubernetes, který nasazujete sami](deploy-container-networking.md#deploy-plug-in-for-a-kubernetes-cluster).

Po nasazení clusteru spusťte následující `kubectl` příkaz ke stažení a použití zásad sítě Azure *daemonset* do clusteru.

  ```
  kubectl apply -f https://raw.githubusercontent.com/Azure/acs-engine/master/parts/k8s/addons/kubernetesmasteraddons-azure-npm-daemonset.yaml

  ```
Řešení je také open source a kód je k dispozici v [úložišti Azure Container Networking](https://github.com/Azure/azure-container-networking/tree/master/npm).



## <a name="next-steps"></a>Další kroky
- Přečtěte si o [službě Azure Kubernetes](../aks/intro-kubernetes.md).
-  Přečtěte si o [sítích kontejnerů](container-networking-overview.md).
- [Nasaďte modul plug-in](deploy-container-networking.md) pro clustery Kubernetes nebo kontejnery Docker.
