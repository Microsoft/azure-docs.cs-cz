---
title: Povolení sdílených disků pro Azure Managed disks
description: Nakonfigurujte Azure Managed disk se sdílenými disky (Preview), abyste ho mohli sdílet napříč několika virtuálními počítači.
author: roygara
ms.service: virtual-machines-windows
ms.topic: conceptual
ms.date: 02/18/2020
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: e7ada64a50d6ace6ea4d34db87e0501d0311071d
ms.sourcegitcommit: 64def2a06d4004343ec3396e7c600af6af5b12bb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/19/2020
ms.locfileid: "77471675"
---
# <a name="enable-shared-disk"></a>Povolit sdílený disk

Tento článek popisuje, jak povolit funkci sdílené disky (Preview) pro Azure Managed disks. Azure Shared Disks (Preview) je nová funkce pro služby Azure Managed disks, která umožňuje současně připojit spravovaný disk k několika virtuálním počítačům (VM). Připojení spravovaného disku k několika virtuálním počítačům vám umožní nasadit do Azure nové nebo migrovat existující clusterové aplikace. 

Pokud hledáte koncepční informace o spravovaných discích, které mají povolené sdílené disky, přečtěte si téma [sdílené disky Azure](disks-shared.md).
[!INCLUDE [virtual-machines-enable-shared-disk](../../../includes/virtual-machines-enable-shared-disk.md)]