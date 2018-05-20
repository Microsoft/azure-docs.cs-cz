---
title: Jak používat kubectl prostory Dev Azure | Microsoft Docs
titleSuffix: Azure Dev Spaces
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.component: azds-kubernetes
author: ghogen
ms.author: ghogen
ms.date: 05/11/2018
ms.topic: article
description: Rychlý vývoj Kubernetes s kontejnery a mikroslužeb v Azure
keywords: Docker, Kubernetes, Azure, AKS, Kubernetes služby Azure, kontejnery
manager: douge
ms.openlocfilehash: 38a433a14ab977fb56a8331a057d27241f1d9783
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/16/2018
---
# <a name="use-kubectl-with-an-azure-dev-space"></a>Použití kubectl s prostorem Dev Azure

Má přístup ke clusteru Kubernetes v rámci Dev místa Azure a pomocí stávající Kubernetes nástrojů, jako `kubectl`.

Spuštění `azds resource create` nebo `azds resource select` automaticky přidá `kubectl` kontextu konfigurace pro vás, tak kubectl by měly být již připojené ke clusteru Azure Dev prostory Kubernetes. Příklady:
- Potvrďte aktuální kontext: `kubectl config current-context`
- Zobrazí seznam všech dostupných kontextů: `kubectl config get-contexts`. 
- Změna kontextu: `kubectl config use-context <context-name>`
- Zobrazení řídicího panelu Kubernetes: Spusťte `kubectl proxy`, otevřete prohlížeč na adresu, kterou tento příkaz vysílá (připojit `/ui` na adresu URL, přejděte na řídicí panel Kubernetes).
- Seznam spuštění služeb ve výchozí místo prostory Dev Azure s názvem *výchozí*: `kubectl get services --namespace=default`

