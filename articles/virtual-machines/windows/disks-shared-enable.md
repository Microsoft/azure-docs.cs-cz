---
title: Povolení sdílených disků pro Azure Managed disks
description: Nakonfigurujte Azure Managed disk se sdílenými disky (Preview), abyste ho mohli sdílet napříč několika virtuálními počítači.
author: roygara
ms.service: virtual-machines
ms.topic: how-to
ms.date: 06/03/2020
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: 3b949905f311b1a8878aa691e32abc3f568e1e6b
ms.sourcegitcommit: 5a8c8ac84c36859611158892422fc66395f808dc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/10/2020
ms.locfileid: "84659293"
---
# <a name="enable-shared-disk"></a>Povolení sdíleného disku

Tento článek popisuje, jak povolit funkci sdílené disky (Preview) pro Azure Managed disks. Azure Shared Disks (Preview) je nová funkce pro služby Azure Managed disks, která umožňuje současně připojit spravovaný disk k několika virtuálním počítačům (VM). Připojení spravovaného disku k několika virtuálním počítačům vám umožní nasadit do Azure nové nebo migrovat existující clusterové aplikace. 

Pokud hledáte koncepční informace o spravovaných discích, které mají povolené sdílené disky, přečtěte si téma [sdílené disky Azure](disks-shared.md).
[!INCLUDE [virtual-machines-enable-shared-disk](../../../includes/virtual-machines-enable-shared-disk.md)]