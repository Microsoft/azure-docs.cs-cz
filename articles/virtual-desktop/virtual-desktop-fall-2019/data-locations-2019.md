---
title: Umístění dat pro virtuální plochu Windows (Classic) – Azure
description: Stručný přehled, ve kterém jsou uložená data a metadata virtuálních ploch (Classic) Windows.
author: Heidilohr
ms.topic: conceptual
ms.date: 03/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 869defde657c9cb4c8bea6bbacebb9458e5a2b96
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "88008810"
---
# <a name="data-locations-for-windows-virtual-desktop-classic"></a>Umístění dat pro virtuální plochu Windows (Classic)

>[!IMPORTANT]
>Tento obsah se vztahuje na virtuální plochu Windows (Classic), která nepodporuje Azure Resource Manager objektů virtuálních klientů Windows. Pokud se snažíte spravovat Azure Resource Manager objektů virtuálních klientů Windows, přečtěte si [Tento článek](../data-locations.md).

Virtuální plocha Windows je aktuálně dostupná pro všechna geografická umístění. Na začátku se metadata služby dají ukládat jenom v geograficky USA (US). Správci mohou zvolit umístění pro ukládání uživatelských dat při vytváření virtuálních počítačů fondu hostitelů a přidružených služeb, jako jsou například souborové servery. Přečtěte si další informace o geografických grafech Azure na [mapě datacenter Azure](https://azuredatacentermap.azurewebsites.net/).

>[!NOTE]
>Společnost Microsoft nekontroluje ani neomezuje oblasti, ve kterých vy nebo vaši uživatelé máte přístup k datům specifických pro uživatele a aplikace.

>[!IMPORTANT]
>Virtuální desktop Windows ukládá globální informace o metadatech, jako jsou názvy tenantů, názvy fondů hostitelů, názvy skupin aplikací a hlavní názvy uživatelů v datacentru, které se nachází ve USA. Uložená metadata jsou šifrovaná v klidovém stavu a geograficky redundantní zrcátka se udržují v rámci USA. Všechna zákaznická data, jako jsou nastavení aplikace a uživatelská data, se nacházejí v umístění, které zákazník zvolí a není spravován službou.

Metadata služby se replikují v USA pro účely zotavení po havárii.