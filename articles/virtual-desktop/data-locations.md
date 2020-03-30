---
title: Umístění dat pro Virtuální plochu Windows – Azure
description: Stručný přehled umístění, ve kterých umístěních jsou uložena data a metadata virtuální plochy systému Windows.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 09/27/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: 5f48586f38c310c77c61f470d3e2fbc819c33f71
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79128056"
---
# <a name="data-locations-for-windows-virtual-desktop"></a>Umístění dat pro virtuální plochu windows

Windows Virtual Desktop je v současné době k dispozici pro všechny geografické umístění. Zpočátku mohou být metadata služby uložena pouze v zeměpisné oblasti USA. Správci mohou zvolit umístění pro ukládání uživatelských dat při vytváření virtuálních počítačů fondu hostitelů a přidružených služeb, jako jsou například souborové servery. Další informace o geografických oblastech Azure najdete na [mapě datového centra Azure](https://azuredatacentermap.azurewebsites.net/).

>[!NOTE]
>Společnost Microsoft neřídí ani neomezuje oblasti, ve kterých máte vy nebo vaši uživatelé přístup k vašim datům specifickým pro uživatele a aplikaci.

>[!IMPORTANT]
>Windows Virtual Desktop ukládá globální informace o metadatech, jako jsou názvy klientů, názvy hostitelských fondu, názvy skupin aplikací a hlavní názvy uživatelů v datovém centru umístěném ve Spojených státech. Uložená metadata jsou šifrována v klidovém stavu a geograficky redundantní zrcadla jsou udržována ve Spojených státech. Všechna zákaznická data, například nastavení aplikací a uživatelská data, se nacházejí v místě, které si zákazník vybere, a služba je nespravuje.

Metadata služby jsou replikována ve Spojených státech pro účely zotavení po havárii.