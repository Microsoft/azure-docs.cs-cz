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
ms.openlocfilehash: 4847d9ce551c9acf1e4fb6325c770187b2cfd89f
ms.sourcegitcommit: d61faf71620a6a55dda014a665155f2a5dcd3fa2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/04/2019
ms.locfileid: "54052287"
---
# <a name="use-habitat-to-deploy-your-application-to-azure"></a>Nasazení aplikace do Azure pomocí Habitat
[Habitat](https://www.habitat.sh/) je první z jeho typu open source projekt, který nabízí zcela nový přístup ke správě aplikací. Habitat je aplikace a její automatizace jednotku nasazení. Když aplikace jsou zabalené ve zjednodušené "habitat", běhové prostředí, jestli je kontejner, holých počítačů nebo PaaS, už není aktivní a neomezuje aplikace. 

Tento článek popisuje výhody použití Habitat.

## <a name="support-for-the-modern-application"></a>Podpora pro moderní aplikace
Mohou balíčky habitat zahrnovat všechny položky, které aplikace potřebuje ke spuštění v průběhu životního cyklu. Habitat na základní komponenty jsou:
- Balení formátu – aplikace v balíčku Habitat jsou atomické, neměnné a auditovatelných.
- Habitat faktorem je frekvence spouštění balíčků aplikací povědomí o balíčky partnerského uzlu relace, upgrade strategie a zásady zabezpečení. Dohledové Habitat konfiguruje a spravuje aplikace pro jakékoli prostředí je k dispozici.
- Ekosystém Habitat také poskytuje služby build service, která přebírá plánu sestavení Habitat, vytvoří balíček Habitat a publikuje ji skladu.

## <a name="run-any-application-anywhere"></a>Spuštění libovolné aplikace kdekoli
Pomocí Habitatu aplikace se můžou spouštět v jakémkoli prostředí modulu runtime bez úprav. Aplikace může být cokoli z holých počítačů a virtuálních počítačů na kontejnery (jako je Docker), systémů pro správu clusteru (třeba Mesosphere nebo Kubernetes) a PaaS systémy (například Pivotal Cloud Foundry).

## <a name="easily-port-legacy-applications"></a>Snadno port starší verze aplikací
Pokud starší aplikace, které jsou zabaleny v balíčku Habitat, aplikace jsou nezávislé na prostředí, pro které byly původně vytvořeny. Balíčky je možné rychle přesunout na Modernější prostředí cloud nebo kontejnery. Navíc vzhledem k tomu, že mohou balíčky Habitat standardní pasivního protilehlé rozhraní, starší verze aplikací budou podstatně usnadňují správu.

## <a name="improve-the-container-experience"></a>Vylepšit kontejneru
Habitat snižuje složitost správy kontejnerů v produkčním prostředí. Díky automatizaci konfigurace aplikace v rámci kontejneru, řeší Habitat tváře vývojáři výzvy při přesouvání aplikací založených na kontejnerech z vývojová prostředí do produkčního prostředí.

## <a name="integrate-into-the-chef-devops-workflow"></a>Integrace do pracovních postupů Chef DevOps
Habitat projektu, se kterou vyvíjí společnost Chef. Habitat využívá Chefu zkušeností s automatizace infrastruktury pro zajištění možnosti bezprecedentní automatizace pro aplikace. Chef budou nabízet komerční podpora Habitat a zajistit bezproblémovou integraci Habitat a doručování Chef k automatizaci aplikace cyklu vydávání verzí, od vývoje k nasazení.

## <a name="next-steps"></a>Další postup
* [Vytvoření virtuálního počítače s Windows v Azure pomocí Chefu](/azure/virtual-machines/windows/chef-automation)