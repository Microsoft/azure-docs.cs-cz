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
description: Rychlý vývoj na platformě Kubernetes s využitím kontejnerů a mikroslužeb v Azure
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, kontejnery
manager: douge
ms.openlocfilehash: 3dc0dd4b571f716bcabb67c4cbef1ea6d762eb94
ms.sourcegitcommit: 50f82f7682447245bebb229494591eb822a62038
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/08/2018
ms.locfileid: "35248654"
---
# <a name="use-kubectl-with-an-azure-dev-space"></a>Použití kubectl s prostorem Dev Azure

Má přístup ke clusteru Kubernetes v rámci Dev místa Azure a pomocí stávající Kubernetes nástrojů, jako `kubectl`.

Spuštění `az aks use-dev-spaces` příkaz automaticky přidá `kubectl` kontextu konfigurace pro vás, tak kubectl by měly být již připojené ke clusteru Azure Dev prostory Kubernetes. Příklady:
- Potvrďte aktuální kontext: `kubectl config current-context`
- Zobrazí seznam všech dostupných kontextů: `kubectl config get-contexts`. 
- Změna kontextu: `kubectl config use-context <context-name>`
- Zobrazení řídicího panelu Kubernetes: Spusťte `kubectl proxy`, otevřete prohlížeč na adresu, kterou tento příkaz vysílá (připojit `/ui` na adresu URL, přejděte na řídicí panel Kubernetes).
- Seznam spuštění služeb ve výchozí místo prostory Dev Azure s názvem *výchozí*: `kubectl get services --namespace=default`

