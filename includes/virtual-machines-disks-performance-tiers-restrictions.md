---
title: zahrnout soubor
description: zahrnout soubor
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 02/18/2021
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 7151c110fd50f7485aa0b130832aace4f3143ad9
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/03/2021
ms.locfileid: "101749934"
---
- Tato funkce se v současné době podporuje jenom pro Premium SSD.
- Než budete moct změnit vrstvu disku, musíte virtuální počítač buď zrušit, nebo ho odpojit od spuštěného virtuálního počítače.
- Úrovně výkonu P60, P70 a P80 se můžou používat jenom na discích, které jsou větší než 4 096 GiB.
- Úroveň výkonu disku se dá downgradovat jenom jednou za 12 hodin.

## <a name="change-performance-tier-without-downtime-preview"></a>Změna úrovně výkonu bez výpadků (Preview)

Normálně byste museli zrušit přidělení virtuálního počítače nebo odpojit disk, aby se změnila úroveň výkonu. Ale pokud tuto funkci verze Preview povolíte, nemusíte zrušit přidělení virtuálního počítače nebo odpojit disk, aby se změnila vrstva. Verzi Preview si můžete zaregistrovat [zde](https://aka.ms/liveperftiersignup).

Verze Preview má následující omezení:
- K dispozici pouze v oblasti EastUS2EUAP.
- Aktuálně není k dispozici pro sdílené disky
- `2020-12-01`Aby bylo možné měnit úrovně výkonu bez výpadků, je nutné použít šablony Azure Resource Manager s rozhraním API.