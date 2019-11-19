---
title: Nasazení aplikace do Azure pomocí stanoviště
description: Postup, jak konzistentně nasadit vaši aplikaci do virtuálních počítačů a kontejnerů Azure
keywords: Azure, DevOps, virtuální počítače, přehled, automatizace, stanoviště
ms.date: 05/15/2018
ms.topic: article
ms.openlocfilehash: de444836c04d1d37a19a006ce4eafdcae867ca5a
ms.sourcegitcommit: 28688c6ec606ddb7ae97f4d0ac0ec8e0cd622889
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/18/2019
ms.locfileid: "74158054"
---
# <a name="use-habitat-to-deploy-your-application-to-azure"></a>Nasazení aplikace do Azure pomocí stanoviště
[Stanoviště](https://www.habitat.sh/) je balíčky aplikací a běhový systém, který tvoří aplikaci a její automatizaci společně jako jednotku nasazení. Tím se vytvoří maximální přenositelnost aplikace, která umožňuje její nasazení do kontejnerů, virtuálních počítačů, holého nebo PaaS bez nutnosti přepisování nebo opětovného balení.

Tento článek popisuje hlavní výhody používání stanovišť.

## <a name="modernize-and-move-legacy-applications"></a>Modernizovat a přesunutí starších verzí aplikací
Bezplatné starší verze aplikací ze starších operačních systémů a middlewaru jejich přebalením pomocí stanovišť. Výsledný artefakt je přenosný a snadno se replatformuje do novější infrastruktury, jako jsou virtuální počítače nebo kontejnery běžící v cloudu.

## <a name="accelerate-container-adoption"></a>Zrychlení přijetí kontejneru
Stanoviště řeší průběžné nasazování komplexních aplikací orientovaných na mikroslužby, které přesně představují závislosti modulu runtime. Přechází mezi jednoduchým modrým/zeleným nasazením jednotlivých komponent a návrhem sofistikovaného chování při nasazování bez generování složitých toků orchestrace.

## <a name="run-any-application-anywhere"></a>Spuštění libovolné aplikace kdekoli
S použitím stanovišť můžou být aplikace v jakémkoli běhovém prostředí spuštěné beze změny. To zahrnuje vše z holého počítače a virtuálních počítačů do kontejnerů (jako jsou Docker), systémy pro správu clusteru (například Mesosphere nebo Kubernetes) a systémy PaaS (například Pivoting Cloud Foundry).

## <a name="integrate-into-the-chef-devops-workflow"></a>Integrace do pracovního postupu DevOps
Projekt stanovišť je jedním z open source projektu od softwaru pro začínajícího systému, který má silnou komunitu podpory. Stanoviště využívá důkladné prostředí pro používání a automatizaci infrastruktury a přináší tak aplikacím nestejné možnosti automatizace. Jan nabízí komerční podporu pro stanoviště a sestavuje bezproblémovou integraci mezi lokalitami a aplikacemi pro úplné automatizaci cyklu vydávání aplikací od vývoje po nasazení.

## <a name="next-steps"></a>Další kroky

* [Vyzkoušejte si stanoviště](https://www.habitat.sh/learn/)
