---
title: Povolení sdílených disků pro Azure Managed disks
description: Nakonfigurujte Azure Managed disk se sdílenými disky (Preview), abyste ho mohli sdílet napříč několika virtuálními počítači.
author: roygara
ms.service: virtual-machines
ms.topic: conceptual
ms.date: 04/24/2020
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: 0dc7cf6e3f72ff9fa7ef5d7cbd85c1fb93f76f9a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "82146768"
---
# <a name="enable-shared-disk"></a>Povolit sdílený disk

Tento článek popisuje, jak povolit funkci sdílené disky (Preview) pro Azure Managed disks. Azure Shared Disks (Preview) je nová funkce pro služby Azure Managed disks, která umožňuje současně připojit spravovaný disk k několika virtuálním počítačům (VM). Připojení spravovaného disku k několika virtuálním počítačům vám umožní nasadit do Azure nové nebo migrovat existující clusterové aplikace. 

Pokud hledáte koncepční informace o spravovaných discích, které mají povolené sdílené disky, přečtěte si téma [sdílené disky Azure](disks-shared.md).
[!INCLUDE [virtual-machines-enable-shared-disk](../../../includes/virtual-machines-enable-shared-disk.md)]