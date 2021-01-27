---
title: Identifikujte nepřipojené disky Azure – Azure Portal
description: Jak najít nepřipojené disky spravované v Azure a nespravované (VHD/Page BLOB) pomocí Azure Portal.
author: roygara
ms.service: virtual-machines
ms.topic: how-to
ms.date: 01/26/2021
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: 4d31ce4b6086c44de913afd1083bae25fa3d44cd
ms.sourcegitcommit: 100390fefd8f1c48173c51b71650c8ca1b26f711
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/27/2021
ms.locfileid: "98898151"
---
# <a name="find-and-delete-unattached-azure-managed-and-unmanaged-disks---azure-portal"></a>Vyhledání a odstranění nepřipojených spravovaných a nespravovaných disků Azure – Azure Portal

Při odstranění virtuálního počítače v Azure se ve výchozím nastavení neodstraní všechny disky připojené k virtuálnímu počítači. To pomáhá zabránit ztrátě dat kvůli neúmyslnému odstranění virtuálních počítačů. Po odstranění virtuálního počítače budete platit za nepřipojené disky. V tomto článku se dozvíte, jak najít a odstranit všechny nepřipojené disky pomocí Azure Portal a omezit zbytečné náklady. Odstranění jsou trvalá, po odstranění disku nebudete moct data obnovit.

## <a name="managed-disks-find-and-delete-unattached-disks"></a>Spravované disky: najít a odstranit nepřipojené disky

Pokud máte nepřipojené spravované disky a už je nepotřebujete, následující postup vysvětluje, jak je najít z Azure Portal:

1. Přihlaste se na [Azure Portal](https://portal.azure.com/).
1. Vyhledejte a vyberte **disky**.

    V okně **disky** se zobrazí seznam všech disků. Každý disk, který má **-** ve sloupci **vlastník** "", je nepřipojený disk.

    [![Snímek obrazovky okna spravované disky, pokud je disk ve sloupci vlastník, jedná se o nepřipojený disk.](media/disks-find-unattached-portal/managed-disk-unattached-owner.png)](media/disks-find-unattached-portal/managed-disk-owner-unattached.png#lightbox)

1. Vyberte nepřipojený disk, který chcete odstranit, otevře se okno disku.
1. V okně disku můžete potvrdit, že stav disku není připojen a pak vyberte **Odstranit**.

    :::image type="content" source="media/disks-find-unattached-portal/delete-managed-disk-unattached.png" alt-text="Snímek obrazovky okna jednotlivých spravovaných disků Toto okno se zobrazí jako nepřipojené ve stavu disku, pokud je Nepřipojeno. Tento disk můžete odstranit, pokud už nepotřebujete zachovat jeho data.":::

## <a name="unmanaged-disks-find-and-delete-unattached-disks"></a>Nespravované disky: najít a odstranit nepřipojené disky

Nespravované disky jsou soubory VHD, které se ukládají jako [objekty blob stránky](/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs#about-page-blobs) v [účtech úložiště Azure](../storage/common/storage-account-overview.md).

Pokud máte nespravované disky, které nejsou připojené k virtuálnímu počítači, už je nepotřebujete, a chcete je odstranit, následující postup vysvětluje, jak to udělat z Azure Portal:

1. Přihlaste se na [Azure Portal](https://portal.azure.com/).
1. Vyhledejte a vyberte **disky (klasické)**.

    Zobrazí se seznam všech nespravovaných disků. Libovolný disk, který má **-** ve sloupci **připojeno k** , je nepřipojený disk.

    :::image type="content" source="media/disks-find-unattached-portal/unmanaged-disk-unattached-attached-to.png" alt-text="Snímek obrazovky okna nespravované disky Disky v tomto okně, které jsou ve sloupci připojeno ke, nejsou připojené.":::

1. Vyberte nepřipojený disk, který chcete odstranit, a zobrazí se okno disku.

1. V okně disku můžete potvrdit, že je nepřipojený, protože je pořád **k** disjednotce připojené **-** .

    :::image type="content" source="media/disks-find-unattached-portal/unmanaged-disk-unattached-select-blade.png" alt-text="Snímek obrazovky okna jednotlivého nespravovaného disku Bude mít hodnotu připojenou k hodnotě, pokud není připojená. Pokud již nepotřebujete data těchto disků, můžete je odstranit.":::

1. Vyberte **Odstranit**.

    :::image type="content" source="media/disks-find-unattached-portal/delete-unmanaged-disk-unattached.png" alt-text="Snímek obrazovky samostatného nespravovaného disku, zvýrazňování odstranit":::

## <a name="next-steps"></a>Další kroky

Pokud chcete automatizovaný způsob hledání a odstraňování nepřipojených účtů úložiště, přečtěte si článek o rozhraních [CLI](linux/find-unattached-disks.md) nebo [PowerShellu](windows/find-unattached-disks.md) .

Další informace najdete v tématu [odstranění účtu úložiště](../storage/common/storage-account-create.md#delete-a-storage-account) a [Identifikace osamocených disků pomocí prostředí PowerShell](/archive/blogs/ukplatforms/azure-cost-optimisation-series-identify-orphaned-disks-using-powershell) .
