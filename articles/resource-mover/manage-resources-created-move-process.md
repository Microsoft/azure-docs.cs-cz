---
title: Správa prostředků vytvořených během procesu přesunu virtuálního počítače v nástroji Azure Resource stěhovací
description: Naučte se spravovat prostředky, které se vytvoří během procesu přesunu virtuálního počítače v nástroji Azure Resource stěhovací.
manager: evansma
author: rayne-wiselman
ms.service: resource-move
ms.topic: how-to
ms.date: 09/10/2020
ms.author: raynew
ms.openlocfilehash: 7ad0e73a90e733af0dd752100ebc71908f68181a
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/14/2021
ms.locfileid: "100388477"
---
# <a name="manage-resources-created-for-the-vm-move"></a>Správa prostředků vytvořených pro přesun virtuálního počítače

Tento článek popisuje, jak spravovat prostředky, které jsou vytvořené explicitně nástrojem [Azure Resource stěhovací](overview.md) , aby se usnadnil proces přesunu virtuálního počítače. 

Po přesunu virtuálních počítačů mezi oblastmi je k dispozici řada prostředků vytvořených nástrojem Resource Centre, které by se měly vyčistit ručně.

## <a name="delete-resources-created-for-vm-move"></a>Odstranit prostředky vytvořené pro přesun virtuálního počítače

Ručně odstraňte kolekci přesunů a Site Recovery prostředky vytvořené pro přesunutí virtuálního počítače.

1. Zkontrolujte prostředky ve skupině prostředků ```ResourceMoverRG-<sourceregion>-<target-region>``` .
2. Ověřte, že virtuální počítač a všechny ostatní zdrojové prostředky v kolekci Move jsou přesunuté nebo odstraněné. Tím se zajistí, že se nepoužívají žádné nedokončené prostředky.
2. Odstraňte tyto prostředky.

    - Název kolekce přesunutí je ```movecollection-<sourceregion>-<target-region>-<metadata-region>``` .
    - Název účtu úložiště mezipaměti je ```resmovecache<guid>```
    - Název trezoru je ```ResourceMove-<sourceregion>-<target-region>-GUID``` .

## <a name="next-steps"></a>Další kroky

Zkuste [virtuální počítač přesunout](tutorial-move-region-virtual-machines.md) do jiné oblasti s nástrojem Resource stěhovací.
