---
title: Použití kubectl s Azure Dev mezery
titleSuffix: Azure Dev Spaces
services: azure-dev-spaces
ms.service: azure-dev-spaces
author: zr-msft
ms.author: zarhoads
ms.date: 05/11/2018
ms.topic: conceptual
description: Rychlý vývoj na platformě Kubernetes s využitím kontejnerů a mikroslužeb v Azure
keywords: 'Docker, Kubernetes, Azure, AKS, službě Azure Kubernetes, kontejnery, Helm, služby sítě, směrování sítě služby, kubectl, k8s '
ms.openlocfilehash: 0dfe88966deeb4dcf0196aa1f1584a06794b36a7
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "60686320"
---
# <a name="use-kubectl-with-an-azure-dev-space"></a>Použití kubectl s prostorem vývoj Azure

Má přístup ke clusteru Kubernetes v rámci adresního prostoru vývoje Azure a použití existujících nástrojů pro Kubernetes, jako jsou `kubectl`.

Spuštění `az aks use-dev-spaces` příkaz automaticky přidá `kubectl` kontextu konfigurace pro vás, takže kubectl musí být už připojené k vašemu clusteru Kubernetes v Azure Dev mezery. Příklady:
- Potvrďte aktuální kontext: `kubectl config current-context`
- Seznam všech dostupných kontextech: `kubectl config get-contexts`. 
- Změna kontextu: `kubectl config use-context <context-name>`
- Zobrazit řídicí panel Kubernetes: Spusťte `kubectl proxy`, potom otevřete prohlížeč na adresu, kterou tento příkaz vysílá (připojit `/ui` k adrese URL můžete přejít na řídicí panel Kubernetes).
- Seznam spuštění služby v prostoru výchozí prostory vývoj Azure s názvem *výchozí*: `kubectl get services --namespace=default`

