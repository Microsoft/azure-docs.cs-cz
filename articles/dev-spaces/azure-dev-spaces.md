---
title: Azure Dev Spaces | Microsoft Docs
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.component: azds-kubernetes
author: ghogen
ms.author: ghogen
ms.date: 05/11/2018
ms.topic: tutorial
description: Rychlý vývoj na platformě Kubernetes s využitím kontejnerů a mikroslužeb v Azure
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, kontejnery
manager: douge
ms.openlocfilehash: 344947b7906d15e819e372e0affe4af3c34ba69b
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/16/2018
ms.locfileid: "34198754"
---
# <a name="azure-dev-spaces"></a>Azure Dev Spaces
Azure Dev Spaces vám usnadňuje rychlý vývoj na platformě Kubernetes. Ve službě Azure Dev Spaces přidáte také všechny možnosti vývoje, jako je ladění do kontejnerů Azure Kubernetes, a můžete iterativně vyvíjet kontejnery v cloudu pomocí známých nástrojů, jako je VS Code, Visual Studio nebo příkazový řádek. Služba Azure Dev Spaces je obzvláště důležitá při týmovém vývoji, kdy izolace jednotlivých větví kódu do samostatných prostor tvoří nezbytnou součást životního cyklu vývoje.

## <a name="how-azure-dev-spaces-simplifies-kubernetes-development"></a>Jak Azure Dev Spaces zjednodušuje vývoj na platformě Kubernetes 

Tento přístup má několik výhod:

* Získáte vývojové prostředí bez infrastruktury, které je typické pro produkci, s úplným přístupem ke cloudovým prostředkům.
* Kontejnery Node.js a .NET Core můžete ladit přímo na platformě Kubernetes pomocí nástroje VS Code nebo sady Visual Studio. V rozhraní příkazového řádku můžete provádět vývoj ve všech ostatních jazycích.
* Instanci Kubernetes můžete sdílet s celým vývojovým týmem a ušetřit tak náklady a minimalizovat instalace místních počítačů pro nové členy týmu.
* Kód můžete vyvíjet izolovaně a pak ho otestovat spolu s ostatními komponentami, aniž byste museli replikovat nebo simulovat závislosti.

[!INCLUDE[](includes/get-started.md)]

![](media/azure-dev-spaces/vscode-overview.png)