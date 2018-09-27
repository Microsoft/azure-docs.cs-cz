---
title: Zásady sítě, které Azure Kubernetes | Dokumentace Microsoftu
description: Přečtěte si o informace o Kubernetes zásad sítě k zabezpečení vašeho clusteru Kubernetes.
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
ms.openlocfilehash: b4f8577724781e5df10846a5fc4e30c8320403f2
ms.sourcegitcommit: ad08b2db50d63c8f550575d2e7bb9a0852efb12f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/26/2018
ms.locfileid: "47219766"
---
# <a name="azure-kubernetes-network-policies-overview"></a>Přehled zásad sítě služby Azure Kubernetes

Zásady sítě zadejte mikrosegmentaci podů podobně jako na skupiny zabezpečení sítě (Nsg) poskytuje mikrosegmentaci u virtuálních počítačů. Implementace zásad sítě Azure podporuje specifikaci standardní zásady sítě Kubernetes. Popisky můžete vybrat skupinu podů a definovat seznam příchozí a odchozí pravidla určující druh provoz, který je povolen do a z tyto pody. Další informace o Kubernetes zásady sítě v [dokumentaci ke Kubernetes](https://kubernetes.io/docs/concepts/services-networking/network-policies/).

![Přehled zásad sítě Kubernetes](./media/kubernetes-network-policies/kubernetes-network-policies-overview.png)

Zásady sítě Azure fungují ve spojení s Azure CNI poskytující integrace virtuální sítě pro kontejnery. Je podporován pouze na uzly s Linuxem ještě dnes. Implementace konfigurace pravidel tabulky IP Linuxu založené na definovaných zásad pro vynucování filtrování provozu.

## <a name="planning-security-for-your-kubernetes-cluster"></a>Plánování zabezpečení pro Kubernetes cluster
Při implementaci zabezpečení pro váš cluster, použijte skupiny zabezpečení sítě (Nsg) pro filtrování sever-jih provozu, to znamená provoz vstupující do a opuštění vaší podsítě clusteru a zásady sítě Kubernetes pro provoz typu East-West, který je. přenosy dat mezi pody ve vašem clusteru.

## <a name="using-azure-kubernetes-network-policies"></a>Pomocí zásady sítě Kubernetes v Azure
Azure zásady sítě je možné stanovit mikrosegmentaci podů následujícími způsoby.

### <a name="acs-engine"></a>Modul ACS
ACS-Engine je nástroj, který generuje šablonu Azure Resource Manageru pro nasazení clusteru Kubernetes v Azure. Konfigurace clusteru je zadáno v souboru JSON, který je předán nástroj při generování šablony. Další informace o úplný seznam nastavení podporovaná clusteru a jejich popis najdete v tématu modul Microsoft Azure Container Service – definice clusteru.

Pro povolení zásad v clusterech nasazených pomocí služby acs-engine, zadejte hodnotu networkPolicy nastavení v souboru definice clusteru bude "azure".

#### <a name="example-configuration"></a>Příklad konfigurace

Níže JSON Ukázková konfigurace vytvoří novou virtuální síť a podsíť a nasadí cluster Kubernetes v něm s Azure CNI. Doporučujeme používat "Poznámkový blok" a upravte soubor JSON. 
```json
{
  "apiVersion": "vlabs",
  "properties": {
    "orchestratorProfile": {
      "orchestratorType": "Kubernetes",
      “kubernetesConfig”: {
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
Implementace je možné poskytnout zásady sítě pro Podů v clusteru Kubernetes nasadit sami, bez nutnosti spoléhat se na nástroje, jako jsou služby ACS-Engine. V takovém případě můžete nejdřív nainstalovat modul plug-in CNI a jeho povolení pro každý virtuální počítač v clusteru. Podrobné pokyny najdete v tématu [nasadit modul plug-in pro cluster Kubernetes, který nasadíte sami](deploy-container-networking.md#deploy-plug-in-for-a-kubernetes-cluster).

Jakmile se cluster nasazuje, spusťte následující příkaz `kubectl` příkaz ke stažení a použití zásady sítě Azure *daemonset* do clusteru.

  ```
  kubectl apply -f https://raw.githubusercontent.com/Azure/acs-engine/master/parts/k8s/addons/kubernetesmasteraddons-azure-npm-daemonset.yaml

  ```
Toto řešení je také open source a kód je k dispozici na [úložiště Azure kontejner sítě](https://github.com/Azure/azure-container-networking/tree/master/npm).



## <a name="next-steps"></a>Další postup
- Další informace o [Azure Kubernetes Service](../aks/intro-kubernetes.md).
-  Další informace o [sítě kontejnerů](container-networking-overview.md).
- [Nasazení modulu plug-in](deploy-container-networking.md) pro clustery Kubernetes nebo kontejnery Dockeru.