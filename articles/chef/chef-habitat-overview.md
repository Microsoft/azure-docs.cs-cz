---
title: Použití prostředí pro nasazení aplikace do Azure
description: Zjistěte, jak konzistentně nasadit aplikaci na virtuálních počítačích Azure a kontejnery
keywords: Azure, chef, devops, virtuální počítače, přehled, automatizaci, prostředí
ms.service: virtual-machines-linux
author: tomarcher
manager: jeconnoc
ms.author: tarcher
ms.date: 05/15/2018
ms.topic: article
ms.openlocfilehash: 73c6834eb53c0496fa673dd25ab90abc18a6a139
ms.sourcegitcommit: 96089449d17548263691d40e4f1e8f9557561197
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/17/2018
ms.locfileid: "34267377"
---
# <a name="use-habitat-to-deploy-your-application-to-azure"></a>Použití prostředí pro nasazení aplikace do Azure
[Prostředí](https://www.habitat.sh/) je první jeho typu open source projektu, který nabízí úplně novou přístup ke správě aplikací. Prostředí aplikace a její automatizace je jednotka nasazení. Pokud aplikace jsou uzavřen do lightweight "prostředí", běhové prostředí toho, jestli je kontejner, holý počítač nebo PaaS, již není aktivní a neuvádělo aplikace. 

Tento článek popisuje výhody použití prostředí.

## <a name="support-for-the-modern-application"></a>Podpora pro moderní aplikace
Balíčky prostředí obsahovat vše, co je potřeba spustit v průběhu životního cyklu aplikace. Prostředí na základní komponenty jsou:
- Balení formát: aplikace v prostředí balíčku jsou atomic, neměnné a kontrolovatelný.
- Supervisor prostředí spouští balíčky aplikace povědomí o daných balíčků sdílené relace, strategie upgradu a zásady zabezpečení. Prostředí nadřízeného konfiguruje a spravuje žádosti o jakémkoli prostředí je k dispozici.
- Ekosystému prostředí také poskytuje službu sestavení, která přebírá plán prostředí sestavení, vytvoří balíček prostředí a publikuje do skladu.

## <a name="run-any-application-anywhere"></a>Spuštění libovolné aplikace kdekoli
Pomocí prostředí můžete spustit aplikace beze změny v jakémkoli prostředí runtime. To zahrnuje vše z holý počítač a virtuální počítače ke kontejnerům (například Docker), Správa clusteru systémy (například Mesosphere nebo Kubernetes) a systémy PaaS (například hrají Foundry cloudu).

## <a name="easily-port-legacy-applications"></a>Snadno port starších verzí aplikací
Pokud jsou starší verze aplikace zabalená v balíčku prostředí, jsou nezávislé na prostředí, pro které byly původně navrženy aplikace. Balíčky se dají rychle přesunout do více moderní prostředí třeba cloud nebo kontejnerů. Navíc vzhledem k tomu, že prostředí balíčky standardní pasivního přístupných rozhraní, starších verzí aplikací stát mnohem snazší spravovat.

## <a name="improve-the-container-experience"></a>Vylepšit kontejneru
Prostředí snižuje složitost správy kontejnerů v produkčním prostředí. Tím, že automatizuje konfigurace aplikace v rámci kontejneru, řeší prostředí tučné vývojáři problémy při přesunu aplikace založené na kontejneru z vývojových prostředí do produkčního prostředí.

## <a name="integrate-into-the-chef-devops-workflow"></a>Integrovat do pracovního postupu, Chef DevOps
Projekt prostředí je podporován Chef. Prostředí využívá Chef společnosti přímý zkušenosti s automatizace infrastruktury k poskytování možnosti nebývalá automatizace na aplikace. Chef bude nabízet komerční podporu pro prostředí a zajistit bezproblémovou integraci mezi prostředí a Chef doručení pro úplnou automatizaci cyklu verze aplikace, z vývojového do nasazení.

## <a name="next-steps"></a>Další postup
* [Vytvoření virtuálního počítače s Windows v Azure pomocí Chef](/azure/virtual-machines/windows/chef-automation)