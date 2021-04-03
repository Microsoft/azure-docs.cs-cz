---
title: Jak používat kubectl s Azure Dev Spaces
services: azure-dev-spaces
ms.date: 05/11/2018
ms.topic: conceptual
description: Naučte se používat kubectl příkazy v rámci vývojového prostoru v clusteru Azure Kubernetes Service s povoleným Azure Dev Spaces.
keywords: 'Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, Containers, Helm, síť pro služby, směrování sítě pro služby, kubectl, k8s '
ms.openlocfilehash: e6f79d98cf209d1bc19753f19c9b17b06017c2b7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "91960152"
---
# <a name="use-kubectl-with-an-azure-dev-space"></a>Použití kubectl s prostorem pro vývoj v Azure

[!INCLUDE [Azure Dev Spaces deprecation](../../../includes/dev-spaces-deprecation.md)]

Ke clusteru Kubernetes můžete přistupovat v rámci vývojového prostoru Azure a používat existující nástroje Kubernetes, jako je `kubectl` .

Spuštění `az aks use-dev-spaces` příkazu automaticky přidá `kubectl` kontext konfigurace, takže kubectl by již měl být připojen k vašemu clusteru Azure dev Spaces Kubernetes. Příklady:
- Potvrďte aktuální kontext: `kubectl config current-context`
- Zobrazit seznam všech dostupných kontextů: `kubectl config get-contexts` . 
- Změnit kontext: `kubectl config use-context <context-name>`
- Zobrazte si řídicí panel Kubernetes: Spusťte `kubectl proxy` a pak otevřete prohlížeč na adrese, kterou tento příkaz vygeneruje (připojíte `/ui` k adrese URL a přejděte na řídicí panel Kubernetes).
- Vypíše běžící služby ve výchozím Azure Dev Spaces prostoru s názvem *výchozí*: `kubectl get services --namespace=default`

