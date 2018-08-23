---
title: Použití kubectl s Azure Dev prostorů | Dokumentace Microsoftu
titleSuffix: Azure Dev Spaces
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.component: azds-kubernetes
author: ghogen
ms.author: ghogen
ms.date: 05/11/2018
ms.topic: article
description: Rychlý vývoj na platformě Kubernetes s využitím kontejnerů a mikroslužeb v Azure
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, kontejnery
manager: douge
ms.openlocfilehash: 978274a6059a327c8596df6776eaa47a27ea4a34
ms.sourcegitcommit: 4ea0cea46d8b607acd7d128e1fd4a23454aa43ee
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/15/2018
ms.locfileid: "42059400"
---
# <a name="use-kubectl-with-an-azure-dev-space"></a>Použití kubectl s prostorem vývoj Azure

Má přístup ke clusteru Kubernetes v rámci adresního prostoru vývoje Azure a použití existujících nástrojů pro Kubernetes, jako jsou `kubectl`.

Spuštění `az aks use-dev-spaces` příkaz automaticky přidá `kubectl` kontextu konfigurace pro vás, takže kubectl musí být už připojené k vašemu clusteru Kubernetes v Azure Dev mezery. Příklady:
- Potvrďte aktuální kontext: `kubectl config current-context`
- Seznam všech dostupných kontextech: `kubectl config get-contexts`. 
- Změna kontextu: `kubectl config use-context <context-name>`
- Zobrazit řídicí panel Kubernetes: Spusťte `kubectl proxy`, potom otevřete prohlížeč na adresu, kterou tento příkaz vysílá (připojit `/ui` k adrese URL můžete přejít na řídicí panel Kubernetes).
- Seznam spuštění služby v prostoru výchozí prostory vývoj Azure s názvem *výchozí*: `kubectl get services --namespace=default`

