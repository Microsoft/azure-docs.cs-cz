---
title: Nasazení aplikace do Azure pomocí Habitat
description: Zjistěte, jak konzistentně nasadit vaše aplikace na Azure virtual machines a kontejnerů
keywords: Azure, chef, devops, virtuální počítače, přehled, automatizovat, habitat
ms.service: virtual-machines-linux
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.date: 05/15/2018
ms.topic: article
ms.openlocfilehash: 2bdcd4c504822a2e60156b0ac565465e0cf23a85
ms.sourcegitcommit: a408b0e5551893e485fa78cd7aa91956197b5018
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/17/2019
ms.locfileid: "54358520"
---
# <a name="use-habitat-to-deploy-your-application-to-azure"></a>Nasazení aplikace do Azure pomocí Habitat
[Habitat](https://www.habitat.sh/) je systém aplikace balení a modulu runtime, který obsahuje ureitou aplikace a její automatizace společně jako jednotky nasazení. Tím se vytvoří ultimate přenositelnost pro aplikace, což umožňuje nasadit kontejnery, virtuálních počítačích, holých počítačů nebo PaaS, bez přepisování nebo opakovanému balení.

Tento článek popisuje hlavní výhody použití Habitat.

## <a name="modernize-and-move-legacy-applications"></a>Modernizace a přesuňte starší verze aplikace
Bezplatné starší aplikace ze starší operační systémy a middleware, ve kterém pomocí Habitatu. Výsledný artefakt je přenosný a snadno replatforms na novější infrastrukturu, jako jsou virtuální počítače a kontejnery v cloudu.

## <a name="accelerate-container-adoption"></a>Rychlejší přijetí kontejneru
Průběžné nasazování složitých aplikací zaměřených na mikroslužby habitat řeší tím, přesně závislosti modulu runtime. Přesunout nad rámec jednoduché modrozelené nasazení jednotlivých součástí a navrhovat chování sofistikované nasazení bez generování událostí komplexní Orchestrace toky.

## <a name="run-any-application-anywhere"></a>Spuštění libovolné aplikace kdekoli
Pomocí Habitatu aplikace se můžou spouštět v jakémkoli prostředí modulu runtime bez úprav. To zahrnuje všechno od holých počítačů a virtuálních počítačů na kontejnery (jako je Docker), systémů pro správu clusteru (třeba Mesosphere nebo Kubernetes) a PaaS systémy (například Pivotal Cloud Foundry).

## <a name="integrate-into-the-chef-devops-workflow"></a>Integrace do pracovních postupů Chef DevOps
Projekt Habitat je jeden projekt open source softwaru, Chef se soustředí silná komunita podpory. Habitat využívá Chefu zkušeností s automatizace infrastruktury pro zajištění možnosti bezprecedentní automatizace pro aplikace. Chef nabízí komerční podporu pro Habitat a bezproblémovou integraci Habitat a Chef Automate plně automatizovat cyklu vydávání verzí aplikace, od vývoje k nasazení sestavení.

## <a name="next-steps"></a>Další postup

* [Zkuste Habitat](https://www.habitat.sh/learn/)
