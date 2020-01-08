---
title: Jak používat kubectl s Azure Dev Spaces
services: azure-dev-spaces
ms.date: 05/11/2018
ms.topic: conceptual
description: Naučte se používat kubectl příkazy v rámci vývojového prostoru v clusteru Azure Kubernetes Service s povoleným Azure Dev Spaces.
keywords: 'Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, Containers, Helm, síť pro služby, směrování sítě pro služby, kubectl, k8s '
ms.openlocfilehash: 7530cde68b2ce8d06fb7b16a9a147f0f0bc8645c
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/25/2019
ms.locfileid: "75438368"
---
# <a name="use-kubectl-with-an-azure-dev-space"></a>Použití kubectl s prostorem pro vývoj v Azure

Ke clusteru Kubernetes můžete přistupovat v rámci vývojového prostoru Azure a používat existující nástroje Kubernetes, jako je `kubectl`.

Spuštění příkazu `az aks use-dev-spaces` automaticky přidá kontext konfigurace `kubectl`, takže kubectl by již měl být připojen k vašemu clusteru Azure Dev Spaces Kubernetes. Příklady:
- Potvrďte aktuální kontext: `kubectl config current-context`
- Vypíše všechny dostupné kontexty: `kubectl config get-contexts`. 
- Změnit kontext: `kubectl config use-context <context-name>`
- Zobrazte si řídicí panel Kubernetes: Spusťte `kubectl proxy`a pak otevřete prohlížeč na adrese, kterou tento příkaz vygeneruje (připojovat `/ui` k adrese URL pro přechod na řídicí panel Kubernetes).
- Vypíše běžící služby ve výchozím Azure Dev Spaces prostoru s názvem *Default*: `kubectl get services --namespace=default`

