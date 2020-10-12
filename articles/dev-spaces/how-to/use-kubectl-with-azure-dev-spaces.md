---
title: Jak používat kubectl s Azure Dev Spaces
services: azure-dev-spaces
ms.date: 05/11/2018
ms.topic: conceptual
description: Naučte se používat kubectl příkazy v rámci vývojového prostoru v clusteru Azure Kubernetes Service s povoleným Azure Dev Spaces.
keywords: 'Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, Containers, Helm, síť pro služby, směrování sítě pro služby, kubectl, k8s '
ms.openlocfilehash: 7530cde68b2ce8d06fb7b16a9a147f0f0bc8645c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "75438368"
---
# <a name="use-kubectl-with-an-azure-dev-space"></a>Použití kubectl s prostorem pro vývoj v Azure

Ke clusteru Kubernetes můžete přistupovat v rámci vývojového prostoru Azure a používat existující nástroje Kubernetes, jako je `kubectl` .

Spuštění `az aks use-dev-spaces` příkazu automaticky přidá `kubectl` kontext konfigurace, takže kubectl by již měl být připojen k vašemu clusteru Azure dev Spaces Kubernetes. Příklady:
- Potvrďte aktuální kontext: `kubectl config current-context`
- Zobrazit seznam všech dostupných kontextů: `kubectl config get-contexts` . 
- Změnit kontext: `kubectl config use-context <context-name>`
- Zobrazte si řídicí panel Kubernetes: Spusťte `kubectl proxy` a pak otevřete prohlížeč na adrese, kterou tento příkaz vygeneruje (připojíte `/ui` k adrese URL a přejděte na řídicí panel Kubernetes).
- Vypíše běžící služby ve výchozím Azure Dev Spaces prostoru s názvem *výchozí*: `kubectl get services --namespace=default`

