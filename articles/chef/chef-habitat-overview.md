---
title: Nasazení aplikace do Azure pomocí habitatu
description: Zjistěte, jak konzistentně nasazovat aplikaci do virtuálních počítačů a kontejnerů Azure.
keywords: azure, chef, devops, virtuální stroje, přehled, automatizace,
ms.date: 05/15/2018
ms.topic: article
ms.openlocfilehash: de444836c04d1d37a19a006ce4eafdcae867ca5a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74158054"
---
# <a name="use-habitat-to-deploy-your-application-to-azure"></a>Nasazení aplikace do Azure pomocí habitatu
[Habitat](https://www.habitat.sh/) je aplikační obalový a runtime systém, který spojuje aplikaci a její automatizaci jako jednotku nasazení. To vytváří maximální přenositelnost pro aplikaci, což umožňuje nasazení do kontejnerů, virtuálních počítačů, holého kovu nebo PaaS, bez přepsání nebo přebalení.

Tento článek popisuje hlavní výhody používání habitatu.

## <a name="modernize-and-move-legacy-applications"></a>Modernizace a přesunutí starších aplikací
Zdarma starší aplikace ze starších operačních systémů a middleware jejich přebalení s Habitat. Výsledný artefakt je přenosný a snadno se přepojí na novější infrastrukturu, jako jsou virtuální počítače nebo kontejnery spuštěné v cloudu.

## <a name="accelerate-container-adoption"></a>Urychlit přijetí kontejneru
Habitat řeší průběžné nasazování složitých aplikací orientovaných na mikroslužby přesným reprezentací runtime závislostí. Přejděte od jednoduchého modrozeleného nasazení jednotlivých komponent a sofistikovaného chování nasazení architekta bez generování složitých toků orchestrace.

## <a name="run-any-application-anywhere"></a>Spusťte libovolnou aplikaci kdekoli
S Habitat, aplikace lze spustit beze změny v libovolném prostředí runtime. To zahrnuje vše od holé holé a virtuální počítače na kontejnery (například Docker), cluster-management systémy (například Mesosphere nebo Kubernetes) a PaaS systémy (například Pivotal Cloud Foundry).

## <a name="integrate-into-the-chef-devops-workflow"></a>Integrace do pracovního postupu Chef DevOps
Projekt Habitat je jedním z open source projektu od Chef Software se silnou komunitou podpory. Habitat využívá hluboké zkušenosti šéfkuchaře s automatizací infrastruktury a přináší do aplikací bezprecedentní automatizační schopnosti. Chef nabízí komerční podporu pro Habitat a buduje bezproblémovou integraci mezi Habitat a Chef Automate, aby plně automatizoval cyklus vydání aplikace, od vývoje až po nasazení.

## <a name="next-steps"></a>Další kroky

* [Vyzkoušejte Habitat](https://www.habitat.sh/learn/)
