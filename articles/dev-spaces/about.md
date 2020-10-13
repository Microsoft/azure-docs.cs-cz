---
title: Co je služba Azure Dev Spaces?
services: azure-dev-spaces
ms.date: 05/07/2019
ms.topic: overview
description: Přečtěte si, jak Azure Dev Spaces poskytuje rychlé a iterativní vývojové prostředí Kubernetes pro týmy ve clusterech služby Azure Kubernetes.
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, Containers, kubectl, k8s
manager: gwallace
ms.openlocfilehash: 9bdd6f48a8f48266b8a9aad003fe6ea43715036c
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/13/2020
ms.locfileid: "91978437"
---
# <a name="what-is-azure-dev-spaces"></a>Co je služba Azure Dev Spaces?

[!INCLUDE [Azure Dev Spaces deprecation](../../includes/dev-spaces-deprecation.md)]

Azure Dev Spaces poskytuje rychlé a iterativní vývojové prostředí Kubernetes pro týmy v clusterech Azure Kubernetes Service (AKS). Azure Dev Spaces také umožňuje ladit a testovat všechny součásti aplikace v AKS s minimálním nastavením vývojového počítače, aniž by se musely replikovat nebo napodobovat závislosti.

![Diagram zobrazuje dvě verze aplikace vyvinuté nezávisle. Pak jsou sloučeny do jednoho z Azure Dev Spacesho vývojového prostředí.](media/azure-dev-spaces/collaborate-graphic.gif)

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

Azure Dev Spaces podporuje jenom clustery AKS v [některých oblastech][supported-regions]. Azure Dev Spaces podporuje použití [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest) nebo [Visual Studio Code](https://code.visualstudio.com/download) s [rozšířením Azure dev Spaces](https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds) nainstalovaným v systémech Linux, MacOS nebo Windows 8 nebo novějším pro sestavování a spouštění aplikací na AKS. Podporuje také použití sady [Visual Studio 2019](https://aka.ms/vsdownload?utm_source=mscom&utm_campaign=msdocs) nainstalované v systému Windows s úlohou vývoj pro Azure.

## <a name="next-steps"></a>Další kroky

Přečtěte si další informace o tom, jak Azure Dev Spaces funguje.

> [!div class="nextstepaction"]
> [Jak Azure Dev Spaces funguje](how-dev-spaces-works.md)

[how-dev-spaces-works]: how-dev-spaces-works.md
[supported-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service
