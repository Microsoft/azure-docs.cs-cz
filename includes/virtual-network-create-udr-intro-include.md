---
title: zahrnout soubor
description: zahrnout soubor
services: virtual-network
author: genlin
ms.service: virtual-network
ms.topic: include
ms.date: 04/13/2018
ms.author: genli
ms.custom: include file
ms.openlocfilehash: 482241deb1081ac8a5265a076eabbdc3fb6d659e
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/23/2019
ms.locfileid: "66170868"
---
Ačkoli použití systémových tras vašemu nasazení automaticky usnadňuje provoz, v některých případech je vhodné řídit směrování paketů prostřednictvím virtuálního zařízení. Můžete to provést tím, že vytvoříte trasy definované uživatelem, které určí další segment směrování tak, aby pakety směřující do konkrétní podsítě místo toho přicházely do virtuálního zařízení, a virtuálnímu počítači spuštěnému jako virtuální zařízení povolíte předávání IP.

Případy použití virtuálních zařízení patří:

* Provoz z monitorování systému zjišťování neoprávněných vniknutí (ID)
* Řízení provozu pomocí firewallu

Další informace o předávání směrování definovaného uživatelem a IP [trasy definované uživatelem a předávání IP adres](../articles/virtual-network/virtual-networks-udr-overview.md).

