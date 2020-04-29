---
title: Umístění dat pro virtuální počítače s Windows – Azure
description: Stručný přehled, ve kterém jsou uložená data a metadata virtuálních počítačů s Windows v.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 09/27/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: 5f48586f38c310c77c61f470d3e2fbc819c33f71
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "79128056"
---
# <a name="data-locations-for-windows-virtual-desktop"></a>Umístění dat pro virtuální počítače s Windows

Virtuální plocha Windows je aktuálně dostupná pro všechna geografická umístění. Na začátku se metadata služby dají ukládat jenom v geograficky USA (US). Správci mohou zvolit umístění pro ukládání uživatelských dat při vytváření virtuálních počítačů fondu hostitelů a přidružených služeb, jako jsou například souborové servery. Přečtěte si další informace o geografických grafech Azure na [mapě datacenter Azure](https://azuredatacentermap.azurewebsites.net/).

>[!NOTE]
>Společnost Microsoft nekontroluje ani neomezuje oblasti, ve kterých vy nebo vaši uživatelé máte přístup k datům specifických pro uživatele a aplikace.

>[!IMPORTANT]
>Virtuální desktop Windows ukládá globální informace o metadatech, jako jsou názvy tenantů, názvy fondů hostitelů, názvy skupin aplikací a hlavní názvy uživatelů v datacentru, které se nachází ve USA. Uložená metadata jsou šifrovaná v klidovém stavu a geograficky redundantní zrcátka se udržují v rámci USA. Všechna zákaznická data, jako jsou nastavení aplikace a uživatelská data, se nacházejí v umístění, které zákazník zvolí a není spravován službou.

Metadata služby se replikují v USA pro účely zotavení po havárii.