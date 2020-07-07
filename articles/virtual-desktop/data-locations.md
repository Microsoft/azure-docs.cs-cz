---
title: Umístění dat pro virtuální počítače s Windows – Azure
description: Stručný přehled, ve kterém jsou uložená data a metadata virtuálních počítačů s Windows v.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 04/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 853f5766c4fd6344eecfd7be1d7911163133a8a5
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "82611530"
---
# <a name="data-locations-for-windows-virtual-desktop"></a>Umístění dat pro virtuální počítače s Windows

>[!IMPORTANT]
>Tento obsah se vztahuje na jarní 2020 aktualizaci s Azure Resource Manager objekty virtuálních klientů Windows. Pokud používáte virtuální plochu Windows na verzi 2019 bez Azure Resource Manager objektů, přečtěte si [Tento článek](./virtual-desktop-fall-2019/data-locations-2019.md).
>
> V současnosti je ve verzi Public Preview na jaře 2020 aktualizace virtuálních počítačů s Windows. Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučujeme ji používat pro produkční úlohy. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti. 
> Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Virtuální plocha Windows je aktuálně dostupná pro všechna geografická umístění. Správci mohou zvolit umístění pro ukládání uživatelských dat při vytváření virtuálních počítačů fondu hostitelů a přidružených služeb, jako jsou například souborové servery. Přečtěte si další informace o geografických grafech Azure na [mapě datacenter Azure](https://azuredatacentermap.azurewebsites.net/).

>[!NOTE]
>Společnost Microsoft nekontroluje ani neomezuje oblasti, ve kterých vy nebo vaši uživatelé máte přístup k datům specifických pro uživatele a aplikace.

>[!IMPORTANT]
>Virtuální desktop Windows ukládá globální informace o metadatech, jako jsou názvy tenantů, názvy fondů hostitelů, názvy skupin aplikací a hlavní názvy uživatelů v datacentru. Pokaždé, když zákazník vytvoří objekt služby, musí zadat umístění pro objekt služby. Umístění, které zadáte, určuje, kam se budou ukládat metadata pro objekt. Zákazník vybere oblast Azure a metadata budou uložená v související zeměpisné oblasti. Seznam všech oblastí Azure a souvisejících geografických oblastí najdete v části [geografických oblastí Azure](https://azure.microsoft.com/global-infrastructure/geographies/).

V tuto chvíli podporujeme ukládání metadat jenom v geograficky USA (US) Azure. Uložená metadata jsou šifrovaná v klidovém stavu a geograficky redundantní zrcátka se udržují v rámci geografické oblasti. Všechna zákaznická data, jako jsou nastavení aplikace a uživatelská data, se nacházejí v umístění, které zákazník zvolí a není spravován službou. Další geografické oblasti budou k dispozici, jakmile se služba rozrůstá.

Metadata služby se replikují v rámci Azure geografie pro účely zotavení po havárii.