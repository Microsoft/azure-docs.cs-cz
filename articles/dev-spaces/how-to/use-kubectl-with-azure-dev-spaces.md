---
title: Použití kubectl s Azure Dev prostorů | Dokumentace Microsoftu
titleSuffix: Azure Dev Spaces
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.subservice: azds-kubernetes
author: zr-msft
ms.author: zarhoads
ms.date: 05/11/2018
ms.topic: article
description: Rychlý vývoj na platformě Kubernetes s využitím kontejnerů a mikroslužeb v Azure
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, kontejnery
ms.openlocfilehash: b0ceccc4f8b16c21e8a66a5f1b8cf0e7b6f47a4f
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/31/2019
ms.locfileid: "55469307"
---
# <a name="use-kubectl-with-an-azure-dev-space"></a>Použití kubectl s prostorem vývoj Azure

Má přístup ke clusteru Kubernetes v rámci adresního prostoru vývoje Azure a použití existujících nástrojů pro Kubernetes, jako jsou `kubectl`.

Spuštění `az aks use-dev-spaces` příkaz automaticky přidá `kubectl` kontextu konfigurace pro vás, takže kubectl musí být už připojené k vašemu clusteru Kubernetes v Azure Dev mezery. Příklady:
- Potvrďte aktuální kontext: `kubectl config current-context`
- Seznam všech dostupných kontextech: `kubectl config get-contexts`. 
- Změna kontextu: `kubectl config use-context <context-name>`
- Zobrazit řídicí panel Kubernetes: Spusťte `kubectl proxy`, potom otevřete prohlížeč na adresu, kterou tento příkaz vysílá (připojit `/ui` k adrese URL můžete přejít na řídicí panel Kubernetes).
- Seznam spuštění služby v prostoru výchozí prostory vývoj Azure s názvem *výchozí*: `kubectl get services --namespace=default`

