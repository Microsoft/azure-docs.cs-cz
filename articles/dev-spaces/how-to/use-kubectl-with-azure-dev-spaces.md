---
title: Jak používat kubectl s Azure Dev Spaces
services: azure-dev-spaces
ms.date: 05/11/2018
ms.topic: conceptual
description: Zjistěte, jak používat příkazy kubectl v dev prostoru v clusteru služby Azure Kubernetes s povoleným Azure Dev Spaces
keywords: 'Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, kontejnery, Helm, síť služeb, směrování sítě služeb, kubectl, k8s '
ms.openlocfilehash: 7530cde68b2ce8d06fb7b16a9a147f0f0bc8645c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75438368"
---
# <a name="use-kubectl-with-an-azure-dev-space"></a>Použití kubectlu s Azure Dev Space

Ke clusteru Kubernetes můžete přistupovat v rámci Azure Dev Space `kubectl`a používat stávající nástroje Kubernetes, jako je .

Spuštění `az aks use-dev-spaces` příkazu `kubectl` automaticky přidá kontext konfigurace pro vás, takže kubectl by již měl být připojen k clusteru Azure Dev Spaces Kubernetes. Příklady:
- Potvrďte aktuální kontext:`kubectl config current-context`
- Seznam všech dostupných `kubectl config get-contexts`kontextů: . 
- Změnit kontext:`kubectl config use-context <context-name>`
- Prohlédněte si řídicí panel Kubernetes: spusťte `kubectl proxy`a otevřete prohlížeč `/ui` na adresu, kterou tento příkaz vyzařuje (přidejte k adrese URL pro přechod na řídicí panel Kubernetes).
- Seznam spuštěných služeb ve výchozím prostoru Azure Dev Spaces s *názvem default*:`kubectl get services --namespace=default`

