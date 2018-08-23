---
title: Azure Dev Spaces | Microsoft Docs
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.component: azds-kubernetes
author: ghogen
ms.author: ghogen
ms.date: 06/01/2018
ms.topic: tutorial
description: Rychlý vývoj na platformě Kubernetes s využitím kontejnerů a mikroslužeb v Azure
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, kontejnery
manager: douge
ms.openlocfilehash: 14b51cc2ad2e8e0f294e5e73e542001e30d21c9d
ms.sourcegitcommit: 4ea0cea46d8b607acd7d128e1fd4a23454aa43ee
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/15/2018
ms.locfileid: "41919900"
---
# <a name="azure-dev-spaces"></a>Azure Dev Spaces
Služba Azure Dev Spaces poskytuje rychlé a iterativní vývojové prostředí Kubernetes pro týmy. S minimálním nastavováním počítačů pro vývoj můžete iterativně spouštět a ladit kontejnery přímo ve službě Azure Kubernetes Service (AKS). Můžete vyvíjet pro Windows, Mac nebo Linux pomocí známých nástrojů, jako jsou Visual Studio a Visual Studio Code, nebo pomocí příkazového řádku.

[!INCLUDE[](includes/dev-spaces-preview.md)]

## <a name="how-azure-dev-spaces-simplifies-kubernetes-development"></a>Jak Azure Dev Spaces zjednodušuje vývoj na platformě Kubernetes 

Azure Dev Spaces pomáhá vývojovým týmům dosáhnout vyšší produktivity na platformě Kubernetes následujícími způsoby:
- Minimalizuje nastavení místních počítačů pro vývoj pro každého člena týmu a umožňuje pracovat přímo ve službě AKS, spravovaném clusteru Kubernetes v Azure.
- Umožňuje rychle iterovat a ladit kód přímo v Kubernetes pomocí sady Visual Studio 2017 nebo editoru Visual Studio Code.
- Umožňuje generovat prostředky konfigurace jako kódu pro Docker a Kubernetes, které pak můžete používat v různých fázích od vývoje až po produkci. 
- Spravovaný cluster Kubernetes můžete sdílet se svým týmem a všichni v něm spolupracovat. Kód můžete vyvíjet izolovaně a pak ho otestovat spolu s ostatními komponentami, aniž byste museli replikovat nebo simulovat závislosti.

[!INCLUDE[](includes/get-started.md)]

![](media/azure-dev-spaces/vscode-overview.png)

## <a name="see-also"></a>Viz také

[Azure Kubernetes Service](/azure/aks)