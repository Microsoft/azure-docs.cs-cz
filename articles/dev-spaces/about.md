---
title: Úvod do Azure Dev Spaces
services: azure-dev-spaces
ms.date: 05/07/2019
ms.topic: overview
description: Zjistěte, jak Azure Dev Spaces poskytuje rychlé a iterativní prostředí pro vývoj Kubernetes pro týmy v clusterech služby Azure Kubernetes
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, kontejnery, kubectl, k8s
manager: gwallace
ms.openlocfilehash: 586b19070ec36517add21f7aac86ddf15121be2d
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "79240699"
---
# <a name="introduction-to-azure-dev-spaces"></a>Úvod do Azure Dev Spaces

Azure Dev Spaces poskytuje rychlé a iterativní prostředí pro vývoj Kubernetes pro týmy v clusterech služby Azure Kubernetes Service (AKS). Azure Dev Spaces také umožňuje ladit a testovat všechny součásti vaší aplikace v AKS s minimálnínastavení vývojového počítače, bez replikace nebo zesměšňování závislostí.

![](media/azure-dev-spaces/collaborate-graphic.gif)

## <a name="how-azure-dev-spaces-simplifies-kubernetes-development"></a>Jak Azure Dev Spaces zjednodušuje vývoj na platformě Kubernetes

Azure Dev Spaces pomáhá týmům zaměřit se na vývoj a rychlou iteraci jejich aplikace mikroslužeb tím, že umožňuje týmům pracovat přímo s celou architekturou mikroslužeb nebo aplikací spuštěnou v AKS. Azure Dev Spaces také poskytuje způsob, jak nezávisle aktualizovat části architektury mikroslužeb izolovaně bez ovlivnění zbytku clusteru AKS nebo jiných vývojářů. Azure Dev Spaces je pro vývoj a testování v prostředích vývoje a testování nižší úrovně a není určen ke spuštění v produkčních clusterech AKS.

Vzhledem k tomu, že týmy můžou pracovat s celou aplikací a spolupracovat přímo v AKS, Azure Dev Spaces:

* Minimalizuje nastavení místního počítače
* Zkracovat čas nastavení pro nové vývojáře v týmu
* Zvyšuje rychlost týmu díky rychlejší iteraci
* Snižuje počet redundantních vývojových a integračních prostředí, protože členové týmu mohou sdílet cluster
* Odstraňuje potřebu replikovat nebo napodobovat závislosti.
* Zlepšuje spolupráci napříč vývojovými týmy i týmy, se kterými pracují, jako jsou týmy DevOps.

Azure Dev Spaces poskytuje nástroje pro generování prostředků Dockeru a Kubernetes pro vaše projekty. Tento nástroj umožňuje snadno přidat nové i stávající aplikace do prostoru pro vývoj a do jiných clusterů AKS.

Další informace o tom, jak Azure Dev Spaces funguje, najdete v [tématu Jak Azure Dev Spaces funguje a je nakonfigurovaný][how-dev-spaces-works].

## <a name="supported-regions-and-configurations"></a>Podporované oblasti a konfigurace

Azure Dev Spaces je podporována jenom clustery AKS v [některých oblastech][supported-regions]. Azure Dev Spaces podporuje použití [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest) nebo editoru [Visual Studio Code](https://code.visualstudio.com/download) s [rozšířením Azure Dev Spaces](https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds) nainstalovaným v systému Linux, MacOS nebo Windows 8 nebo novějším pro sestavování a provoz vašich aplikací v AKS. Podporuje také použití [Visual Studia](https://aka.ms/vsdownload?utm_source=mscom&utm_campaign=msdocs) nainstalovaného ve Windows 8 nebo vyšším. Pro Visual Studio 2019 budete potřebovat úlohy vývoje Azure. Pro Visual Studio 2017 budete potřebovat úlohy pro vývoj webu a [nástroje sady Visual Studio pro Kubernetes](https://aka.ms/get-vsk8stools).

## <a name="next-steps"></a>Další kroky

Další informace o rychlém a iterativním vývoji pro týmy s Azure Dev Spaces s rychlým startem pro vývoj týmu.

> [!div class="nextstepaction"]
> [Rychlý start pro vývoj týmu](quickstart-team-development.md)


[how-dev-spaces-works]: how-dev-spaces-works.md
[supported-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service
[team-development-quickstart]: quickstart-team-development.md
