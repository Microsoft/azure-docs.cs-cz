---
title: Úvod do Azure Dev mezery
titleSuffix: Azure Dev Spaces
author: zr-msft
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.author: zarhoads
ms.date: 05/07/2019
ms.topic: overview
description: Úvod do Azure Dev mezery
keywords: Docker, Kubernetes, Azure, AKS, službě Azure Kubernetes, kontejnery, kubectl, k8s
manager: gwallace
ms.openlocfilehash: 33ac5a7aa6d823105b87325ba52aa77cd9b9b3a3
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/09/2019
ms.locfileid: "67706306"
---
# <a name="azure-dev-spaces"></a>Azure Dev Spaces

Azure Dev prostory je rychlý a iterativní Kubernetes vývojové prostředí pro týmy pracující v clusterech Azure Kubernetes Service (AKS). Můžete spolupracovat se svým týmem v sdíleného clusteru AKS. Azure Dev prostory také umožňuje testovat všechny součásti vaší aplikace ve službě AKS bez replikace nebo napodobování nahoru závislosti. Můžete využít iterativní spustit a ladit kontejnery přímo ve službě AKS pomocí nastavení minimální vývojového počítače.

![](media/azure-dev-spaces/collaborate-graphic.gif)


## <a name="how-azure-dev-spaces-simplifies-kubernetes-development"></a>Jak Azure Dev Spaces zjednodušuje vývoj na platformě Kubernetes

Azure Dev prostorech týmům soustředit se víc o rozvoji a rychlé iterace jejich aplikace mikroslužeb tím, že týmy pracovat přímo s jejich architektuře mikroslužeb celý nebo aplikace spuštěná ve službě AKS. Azure Dev prostory také poskytuje způsob, jak nezávisle aktualizovat část vaší architektury mikroslužeb v izolaci bez vlivu na zbytek jinými vývojáři nebo clusteru AKS. Azure Dev prostory je pro vývoj a testování v nižší úrovně vývojové a testovací prostředí a není určena pro spuštění v produkčních clusterech AKS.

Protože týmy můžete pracovat v celé aplikaci a spolupracovat přímo ve službě AKS, Azure Dev mezery:

* Minimalizuje nastavení místního počítače
* Snižuje čas nastavení pro nové vývojáře v týmu
* Zvyšuje rychlost týmu prostřednictvím rychlejší iterace
* Snižuje počet redundantní prostředí pro vývoj a integraci, protože členové týmu můžou sdílet clusteru
* Eliminuje nutnost provádět replikaci nebo navrhnete závislosti
* Zlepší spolupráci napříč vývojářské týmy a týmy, s nimiž pracují, jako je například týmy DevOps

Azure Dev prostory poskytují nástroje pro generování Docker a Kubernetes prostředky pro vaše projekty. Tyto nástroje vám umožňuje snadno přidat nových nebo stávajících aplikací místo vývoj a další clustery AKS.

Další informace o tom, jak funguje Azure Dev mezery, naleznete v tématu [jak prostory vývoj Azure funguje a je nakonfigurován][how-dev-spaces-works].

## <a name="supported-regions-and-configurations"></a>Podporované oblasti a konfigurace

Službu Azure Dev Spaces podporují jenom clustery AKS v oblastech **USA – východ**,**USA – východ 2**, **USA – střed**, **Západní USA 2**, **Severní Evropa**, **Západní Evropa**, **Velká Británie – jih**, **Jihovýchodní Asie**, **Austrálie – východ**, **Kanada – střed** a **Kanada – východ**. Azure Dev Spaces podporuje použití [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest) nebo editoru [Visual Studio Code](https://code.visualstudio.com/download) s [rozšířením Azure Dev Spaces](https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds) nainstalovaným v systému Linux, MacOS nebo Windows 8 nebo novějším pro sestavování a provoz vašich aplikací v AKS. Podporuje také pomocí [sady Visual Studio](https://aka.ms/vsdownload?utm_source=mscom&utm_campaign=msdocs) nainstalované v systému Windows 8 nebo vyšší. Pro Visual Studio 2019 budete potřebovat funkcí vývoj pro Azure. Pro Visual Studio 2017, budete potřebovat úlohy Web Development a [Visual Studio Tools pro systém Kubernetes](https://aka.ms/get-vsk8stools).

## <a name="next-steps"></a>Další postup

Další informace o rychlý a iterativní vývoj pro týmy pracující s Azure Dev prostory pomocí rychlého startu týmový vývoj.

> [!div class="nextstepaction"]
> [Rychlý start týmový vývoj](quickstart-team-development.md)


[how-dev-spaces-works]: how-dev-spaces-works.md
[team-development-quickstart]: quickstart-team-development.md
