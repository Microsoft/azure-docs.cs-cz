---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 02/10/2020
ms.author: dacoulte
ms.openlocfilehash: 49ba27ef559748a8805160558d48b7b7c2cbe80f
ms.sourcegitcommit: bdf31d87bddd04382effbc36e0c465235d7a2947
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/12/2020
ms.locfileid: "77172790"
---
|Název |Popis |Vliv (s) |Verze |
|---|---|---|---|
|[\[Preview\]: \[modul AKS\] nepovoluje privilegované kontejnery v clusteru Kubernetes.](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Kubernetes/ContainerNoPrivilege_EnforceOPAConstraint.json) |Tato zásada nepovoluje vytvoření privilegovaných kontejnerů v clusteru Kubernetes. Pokyny k používání této zásady najdete na https://aka.ms/kubepolicydoc. |enforceOPAConstraint, zakázáno |1.0.0 – Preview |
|[\[Preview\]: \[modul AKS\] vynutil příchozí přenosy HTTPS v clusteru Kubernetes](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Kubernetes/IngressHttpsOnly_EnforceOPAConstraint.json) |Tato zásada vynutila příchozí přenosy HTTPS v clusteru Kubernetes. Pokyny k používání této zásady najdete na https://aka.ms/kubepolicydoc. |enforceOPAConstraint, zakázáno |1.0.0 – Preview |
|[\[Preview\]: \[modul AKS\] vynutil interní nástroje pro vyrovnávání zatížení v clusteru Kubernetes.](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Kubernetes/LoadbalancerNoPublicIPs_EnforceOPAConstraint.json) |Tato zásada vynutila, že nástroje pro vyrovnávání zatížení nemají v clusteru Kubernetes veřejné IP adresy. Pokyny k používání této zásady najdete na https://aka.ms/kubepolicydoc. |enforceOPAConstraint, zakázáno |1.0.0 – Preview |
|[\[Preview\]: \[modul AKS\] vynutilí popisky v luskech v clusteru Kubernetes.](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Kubernetes/PodEnforceLabels_EnforceOPAConstraint.json) |Tato zásada vynutila, že zadané popisky jsou k dispozici pro lusky v clusteru Kubernetes. Pokyny k používání této zásady najdete na https://aka.ms/kubepolicydoc. |enforceOPAConstraint, zakázáno |1.0.0 – Preview |
|[\[Preview\]: \[modul AKS\] vymáhat jedinečné názvy příchozích dat v rámci oborů názvů v clusteru Kubernetes.](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Kubernetes/IngressHostnamesConflict_EnforceOPAConstraint.json) |Tato zásada vynutila jedinečné názvy hostitelů v rámci oborů názvů v clusteru Kubernetes. Pokyny k používání této zásady najdete na https://aka.ms/kubepolicydoc. |enforceOPAConstraint, zakázáno |1.0.0 – Preview |
|[\[Preview\]: \[modul AKS\] zajistěte, aby limity prostředků procesoru a paměti nepřesáhly zadané limity v clusteru Kubernetes.](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Kubernetes/ContainerResourceLimits_EnforceOPAConstraint.json) |Tato zásada zajišťuje, že jsou definovány limity procesoru a paměti kontejneru a nepřekročí zadané limity v clusteru Kubernetes. Pokyny k používání této zásady najdete na https://aka.ms/kubepolicydoc. |enforceOPAConstraint, zakázáno |1.0.0 – Preview |
|[\[Preview\]: \[modul AKS\] zajistěte, aby se kontejnery naslouchaly jenom na povolených portech v clusteru Kubernetes.](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Kubernetes/ContainerAllowedPorts_EnforceOPAConstraint.json) |Tato zásada vynutila, aby se kontejnery naslouchaly jenom na povolených portech v clusteru Kubernetes. Pokyny k používání této zásady najdete na https://aka.ms/kubepolicydoc. |enforceOPAConstraint, zakázáno |1.0.0 – Preview |
|[\[Preview\]: \[modul AKS\] zajistěte, aby se v clusteru Kubernetes jenom povolené image kontejneru.](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Kubernetes/ContainerAllowedImages_EnforceOPAConstraint.json) |Tato zásada zajišťuje, aby se v clusteru Kubernetes běžely jenom povolené image kontejneru. Pokyny k používání této zásady najdete na https://aka.ms/kubepolicydoc. |enforceOPAConstraint, zakázáno |1.0.0 – Preview |
|[\[Preview\]: \[modul AKS\] zajistěte, aby se služby naslouchaly jenom na povolených portech v clusteru Kubernetes.](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Kubernetes/ServiceAllowedPorts_EnforceOPAConstraint.json) |Tato zásada vynutila, aby se služby naslouchaly jenom na povolených portech v clusteru Kubernetes. Pokyny k používání této zásady najdete na https://aka.ms/kubepolicydoc. |enforceOPAConstraint, zakázáno |1.0.0 – Preview |
