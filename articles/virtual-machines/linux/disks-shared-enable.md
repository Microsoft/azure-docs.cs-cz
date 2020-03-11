---
title: Povolení sdílených disků pro Azure Managed disks
description: Nakonfigurujte Azure Managed disk se sdílenými disky (Preview), abyste ho mohli sdílet napříč několika virtuálními počítači.
author: roygara
ms.service: virtual-machines
ms.topic: conceptual
ms.date: 02/18/2020
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: 0e925abf151abc1a229b57bb035775430fa5332f
ms.sourcegitcommit: 5f39f60c4ae33b20156529a765b8f8c04f181143
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/10/2020
ms.locfileid: "78970366"
---
# <a name="enable-shared-disk"></a>Povolit sdílený disk

Tento článek popisuje, jak povolit funkci sdílené disky (Preview) pro Azure Managed disks. Azure Shared Disks (Preview) je nová funkce pro služby Azure Managed disks, která umožňuje současně připojit spravovaný disk k několika virtuálním počítačům (VM). Připojení spravovaného disku k několika virtuálním počítačům vám umožní nasadit do Azure nové nebo migrovat existující clusterové aplikace. 

Pokud hledáte koncepční informace o spravovaných discích, které mají povolené sdílené disky, přečtěte si téma [sdílené disky Azure](disks-shared.md).
[!INCLUDE [virtual-machines-enable-shared-disk](../../../includes/virtual-machines-enable-shared-disk.md)]