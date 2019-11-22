---
title: Úvod do Azure Dev Spaces
services: azure-dev-spaces
ms.date: 05/07/2019
ms.topic: overview
description: Úvod do Azure Dev Spaces
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, Containers, kubectl, k8s
manager: gwallace
ms.openlocfilehash: 1065ea65c338aadbff9818ad7f7d26ddbed42602
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/21/2019
ms.locfileid: "74280253"
---
# <a name="azure-dev-spaces"></a>Azure Dev Spaces

Azure Dev Spaces poskytuje rychlé a iterativní vývojové prostředí Kubernetes pro týmy v clusterech Azure Kubernetes Service (AKS). Azure Dev Spaces také umožňuje ladit a testovat všechny součásti aplikace v AKS s minimálním nastavením vývojového počítače, aniž by se musely replikovat nebo napodobovat závislosti.

![](media/azure-dev-spaces/collaborate-graphic.gif)

## <a name="how-azure-dev-spaces-simplifies-kubernetes-development"></a>Jak Azure Dev Spaces zjednodušuje vývoj na platformě Kubernetes

Azure Dev Spaces pomáhá týmům soustředit se na vývoj a rychlou iteraci své aplikace mikroslužeb tím, že týmům umožní pracovat přímo s celou architekturou mikroslužeb nebo aplikací spuštěnou v AKS. Azure Dev Spaces taky poskytuje způsob, jak nezávisle aktualizovat části architektury mikroslužeb v izolaci, aniž by to ovlivnilo zbytek clusteru AKS nebo jiných vývojářů. Azure Dev Spaces slouží pro vývoj a testování ve vývojových a testovacích prostředích nižší úrovně a není určené ke spouštění v produkčních clusterech AKS.

Vzhledem k tomu, že týmy můžou pracovat s celou aplikací a spolupracovat přímo v AKS, Azure Dev Spaces:

* Minimalizuje nastavení místního počítače.
* Snižuje čas nastavení pro nové vývojáře v týmu.
* Zvyšuje rychlost týmu prostřednictvím rychlejší iterace.
* Snižuje počet redundantních prostředí pro vývoj a integraci, protože členové týmu můžou sdílet cluster.
* Odstraní nutnost replikace nebo napodobování závislostí.
* Vylepšuje spolupráci mezi vývojovými týmy a týmy, se kterými pracují, například DevOps týmy.

Azure Dev Spaces poskytuje nástroje pro generování Docker a Kubernetes prostředků pro vaše projekty. Pomocí tohoto nástroje můžete snadno přidávat nové a existující aplikace do vývojového prostoru i dalších clusterů AKS.

Další informace o tom, jak Azure Dev Spaces funguje, najdete v tématu [Jak funguje Azure dev Spaces a která je nakonfigurovaná][how-dev-spaces-works].

## <a name="supported-regions-and-configurations"></a>Podporované oblasti a konfigurace

Azure Dev Spaces podporuje jenom clustery AKS v rámci **východní USA**, **východní USA 2**, **střed USA**, **střed USA – jih**, **západní USA 2**, **Severní Evropa**, **západní Evropa**, **Velká Británie – jih**, **východní Asie**, **jihovýchodní Asie**, Austrálie – **východ**, **Kanada – střed**a **Kanada – východ** . Azure Dev Spaces podporuje použití [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest) nebo editoru [Visual Studio Code](https://code.visualstudio.com/download) s [rozšířením Azure Dev Spaces](https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds) nainstalovaným v systému Linux, MacOS nebo Windows 8 nebo novějším pro sestavování a provoz vašich aplikací v AKS. Podporuje také používání sady [Visual Studio](https://aka.ms/vsdownload?utm_source=mscom&utm_campaign=msdocs) nainstalované ve Windows 8 nebo novějším. V případě sady Visual Studio 2019 budete potřebovat úlohu vývoj pro Azure. V případě sady Visual Studio 2017 budete potřebovat úlohu vývoje webu a [Visual Studio Tools for Kubernetes](https://aka.ms/get-vsk8stools).

## <a name="next-steps"></a>Další kroky

Přečtěte si další informace o rychlém a iterativním vývoji týmů s Azure Dev Spaces s rychlým startem pro vývoj týmu.

> [!div class="nextstepaction"]
> [Rychlý Start pro vývoj týmu](quickstart-team-development.md)


[how-dev-spaces-works]: how-dev-spaces-works.md
[team-development-quickstart]: quickstart-team-development.md
