---
title: Umístění dat pro virtuální počítače s Windows – Azure
description: Stručný přehled, ve kterém jsou uložená data a metadata virtuálních počítačů s Windows v.
author: Heidilohr
ms.topic: conceptual
ms.date: 04/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: e626b7e729e394b1012848904f5ce12279c3ef24
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "88010068"
---
# <a name="data-and-metadata-locations-for-windows-virtual-desktop"></a>Data a umístění metadat pro virtuální počítače s Windows

>[!IMPORTANT]
>Tento obsah se vztahuje na virtuální plochu Windows s Azure Resource Manager objekty virtuálních klientů Windows. Pokud používáte virtuální plochu Windows (Classic) bez Azure Resource Manager objektů, přečtěte si [Tento článek](./virtual-desktop-fall-2019/data-locations-2019.md).

Virtuální plocha Windows je aktuálně dostupná pro všechna geografická umístění. Správci mohou zvolit umístění pro ukládání uživatelských dat při vytváření virtuálních počítačů fondu hostitelů a přidružených služeb, jako jsou například souborové servery. Přečtěte si další informace o geografických grafech Azure na [mapě datacenter Azure](https://azuredatacentermap.azurewebsites.net/).

>[!NOTE]
>Společnost Microsoft nekontroluje ani neomezuje oblasti, ve kterých vy nebo vaši uživatelé máte přístup k datům specifických pro uživatele a aplikace.

>[!IMPORTANT]
>Virtuální desktop Windows ukládá globální informace o metadatech, jako jsou názvy tenantů, názvy fondů hostitelů, názvy skupin aplikací a hlavní názvy uživatelů v datacentru. Pokaždé, když zákazník vytvoří objekt služby, musí zadat umístění pro objekt služby. Umístění, které zadáte, určuje, kam se budou ukládat metadata pro objekt. Zákazník vybere oblast Azure a metadata budou uložená v související zeměpisné oblasti. Seznam všech oblastí Azure a souvisejících geografických oblastí najdete v části [geografických oblastí Azure](https://azure.microsoft.com/global-infrastructure/geographies/).

V tuto chvíli podporujeme ukládání metadat jenom v geograficky USA (US) Azure. Uložená metadata jsou šifrovaná v klidovém stavu a geograficky redundantní zrcátka se udržují v rámci geografické oblasti. Všechna zákaznická data, jako jsou nastavení aplikace a uživatelská data, se nacházejí v umístění, které zákazník zvolí a není spravován službou. Další geografické oblasti budou k dispozici, jakmile se služba rozrůstá.

Metadata služby se replikují v rámci Azure geografie pro účely zotavení po havárii.